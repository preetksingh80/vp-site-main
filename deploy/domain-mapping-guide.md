# Azure Static Web Apps + Domain Mapping Guide

## Scope
This guide covers deployment and custom-domain mapping for vishvaas.co.uk to Azure Static Web Apps while preserving Google Workspace mail.

## Current Project State (Verified)
- Azure subscription: Microsoft Partner Network
- Resource group: rg-vp-site-uksouth
- Static Web App name: vishvaas-site-main
- Static Web App region: West Europe
- Default hostname: polite-coast-043367903.7.azurestaticapps.net
- Site deployment: completed via GitHub Actions workflow
- Workflow file: .github/workflows/azure-static-web-apps.yml

## Current DNS Baseline (Live Lookup)
The live DNS lookup currently shows:
- Nameservers are NS1-hosted (dns1-4.p09.nsone.net)
- Apex (vishvaas.co.uk) has no active A/CNAME/MX/TXT answers (SOA-only responses)
- www.vishvaas.co.uk has no A/CNAME/MX/TXT records

This indicates a parked or incomplete DNS zone state. It also means email for contact@vishvaas.co.uk is likely at risk unless Google Workspace records are restored.

## Critical Rule
Only change website-routing records (apex/web). Do not remove Google Workspace records (MX/SPF/DKIM/DMARC) once added.

## DNS Records to Add (Squarespace DNS)

### 1) Website Routing

Recommended canonical pattern:
- Primary host: www.vishvaas.co.uk
- Redirect apex vishvaas.co.uk to www (HTTP 301) if desired

Records:
- CNAME
  - Host: www
  - Value: polite-coast-043367903.7.azurestaticapps.net

For apex vishvaas.co.uk:
- Preferred: ALIAS/ANAME/CNAME flattening to polite-coast-043367903.7.azurestaticapps.net
- If provider does not support flattening: use Azure guidance for apex via A record (less preferred)

### 2) Azure Domain Validation (Apex)
Azure has already generated a TXT validation token for apex:
- validation token: _sc26l5dzb1kc7rgo2vcaed23qvc1bjq

Add TXT record used by Azure validation:
- Host: asuid (or asuid.vishvaas.co.uk if UI requires FQDN)
- Value: _sc26l5dzb1kc7rgo2vcaed23qvc1bjq

Azure command to re-check status:
```powershell
az staticwebapp hostname list -n vishvaas-site-main -g rg-vp-site-uksouth -o table
```

### 3) Google Workspace Mail Protection (Must Exist)
Google Workspace now supports a single MX target (modern setup):
- MX
  - Host: @
  - Priority: 1
  - Value: smtp.google.com

If you are on legacy Google MX and it is already working, you can keep legacy values.

Also ensure these exist:
- SPF TXT (example): v=spf1 include:_spf.google.com ~all
- DKIM record(s) from Google Admin
- DMARC TXT (recommended)

## Azure Custom Domain Binding Order (Low Risk)
1. Keep current website live on old host until validation is ready.
2. Add apex custom domain in Azure using TXT-token method (already initiated).
3. Add TXT validation record in Squarespace (asuid token).
4. Add www CNAME -> polite-coast-043367903.7.azurestaticapps.net.
5. Add apex routing record (ALIAS/ANAME/flattening preferred).
6. In Azure, add/validate www.vishvaas.co.uk custom domain.
7. Confirm both hosts show as Ready in Azure.
8. Verify HTTPS cert is issued for both hosts.

## Squarespace-Specific Notes
- If Squarespace DNS UI blocks apex flattening options, keep www as primary website host and use forwarding for apex.
- If DNS records are conflicting or hidden by presets, remove only old web A/CNAME records, not mail records.
- Because the screenshots indicate problems, assume manual record cleanup is needed before final cutover.

## Verification Checklist
After DNS changes, verify:
- https://polite-coast-043367903.7.azurestaticapps.net (already live)
- https://www.vishvaas.co.uk
- https://vishvaas.co.uk
- SSL status for custom domains in Azure = Ready
- Gmail send/receive for contact@vishvaas.co.uk

Command checks:
```powershell
Resolve-DnsName vishvaas.co.uk -Type A
Resolve-DnsName vishvaas.co.uk -Type TXT
Resolve-DnsName vishvaas.co.uk -Type MX
Resolve-DnsName www.vishvaas.co.uk -Type CNAME
```

## Rollback
If web cutover fails:
1. Revert apex/www web records to previous known-good values.
2. Leave MX/SPF/DKIM/DMARC untouched.
3. Recheck DNS after TTL expiry.

## Suggested TTL
- Migration window: 300 seconds
- Post-stabilization: 3600 seconds
