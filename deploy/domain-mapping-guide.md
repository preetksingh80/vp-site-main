# Azure Static Web Apps Domain Mapping Guide

## Scope
This guide covers mapping vishvaas.co.uk to Azure Static Web Apps while preserving existing DNS records (for Google Workspace mail and other services).

## 1. Pre-Checks
- Confirm Azure Static Web App is deployed and reachable on default domain:
  - https://<app-name>.<region>.azurestaticapps.net
- Confirm domain registrar and DNS host:
  - likely Squarespace DNS (as noted in project spec)
- Export current DNS zone records before any changes.
- Identify existing records that must stay untouched:
  - MX records (mail)
  - SPF/TXT
  - DKIM CNAME/TXT
  - DMARC TXT
  - Google verification TXT

## 2. Recommended Domain Strategy
- Apex domain: vishvaas.co.uk
- Subdomain: www.vishvaas.co.uk
- Preferred behavior:
  - Option A: apex is primary, www redirects to apex
  - Option B: www is primary, apex redirects to www

Use one canonical host only for SEO consistency.

## 3. Add Custom Domains in Azure
In Azure Portal:
1. Open Static Web App.
2. Go to Custom domains.
3. Add www.vishvaas.co.uk first.
4. Add vishvaas.co.uk second.
5. Azure shows required DNS validation records for each hostname.

## 4. DNS Changes in Squarespace

### 4.1 For www.vishvaas.co.uk
- Add CNAME:
  - Host: www
  - Value: <azure provided target>

### 4.2 For vishvaas.co.uk (apex)
Azure may ask for one of these patterns:
- ALIAS/ANAME/CNAME flattening to Azure host (if supported), or
- A record(s) + TXT validation record.

Apply exactly what Azure Custom domains panel shows at the time of setup.

### 4.3 Validation TXT Record
- Add TXT record with Azure-specified host/value.
- Keep this record until Azure domain status is fully validated and stable.

## 5. Preserve Existing Services
Do not delete or overwrite these record types unless intentionally migrating mail:
- MX
- SPF TXT
- DKIM
- DMARC
- existing Google verification TXT

If there is an existing A/CNAME for root or www used by an old website, replace only those web-routing records.

## 6. Verify Cutover
- Check DNS propagation using at least two public resolvers.
- Verify:
  - https://vishvaas.co.uk
  - https://www.vishvaas.co.uk
- Confirm SSL certificate issuance is complete in Azure.
- Confirm HTTP to HTTPS redirect behavior.
- Validate canonical redirect behavior (www -> apex or apex -> www).

## 7. Post-Cutover Checklist
- Test page load from mobile and desktop.
- Validate all navigation links and forms.
- Check Google Workspace email still sends/receives.
- Re-run DNS check after 24 hours for consistency.

## 8. Rollback Plan
If issues occur:
1. Revert web A/CNAME records to previous values.
2. Keep MX/TXT mail records unchanged.
3. Wait for TTL expiration.
4. Re-test previous site and email.

## 9. Suggested TTL Values
- During migration: TTL 300 seconds where possible.
- After stable cutover: increase TTL (for example 3600).

## 10. Command Snippets (Optional Local Validation)
Use these from terminal for visibility:

```powershell
nslookup vishvaas.co.uk
nslookup www.vishvaas.co.uk
nslookup -type=txt vishvaas.co.uk
```

## 11. Decision Log Template
Record final production decisions:
- Canonical host: <apex or www>
- DNS provider: <provider>
- Cutover date/time:
- Azure app name:
- SSL status verified by:
- Rollback owner:
