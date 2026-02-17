# OSINT Email Harvester - Setup Guide

## Quick Setup (5 minutes)

### Step 1: Import Workflow

1. Download: [email-harvester-workflow.json](../workflows/email-harvester-workflow.json)
2. n8n → Import from File

---

### Step 2: Configure Target URLs

1. Open "Load Target URLs" node
2. Edit URL list:
```javascript
   const targetUrls = [
     'https://yourcompany.com/contact',
     'https://yourcompany.com/team',
     // Add more...
   ];
```
3. Ensure the Stealth Controller is active with a Batch Size of 1 and a 2s delay to bypass WAF detection

---

### Step 3: Execute

1. Click "Execute Workflow"
2. Wait for completion
3. Download CSV: `file.csv`

---

## Ethical Usage

**CRITICAL:** Only use on:
- Your own websites
- Sites you have permission to test
- Public information for legitimate OSINT

**NEVER use for:**
- Unauthorized data collection
- Spam campaigns
- Harassment

---

## Customization

### Add Custom Email Patterns

Edit "Extract Email Addresses" node:
```javascript
// Example: Adding square bracket de-obfuscation
const patterns = [
  { name: 'Brackets', regex: /\s*\[at\]\s*/g, replace: '@' },
  { name: 'SpacedDot', regex: /\s+dot\s+/g, replace: '.' }
];
```

### Filter by Domain

Add after "Validate Email Format":
```javascript
return items.filter(item => {
  const domain = item.json.domain;
  return domain.includes('targetcompany.com');
});
```

### Export to Multiple Formats

Add "Convert to CVS" node:
- JSON: `JSON.stringify(items, null, 2)`
- TXT: One email per line

---

## Troubleshooting

**No emails found:**
- Verify pages contain emails
- Check regex pattern
- Inspect HTML source manually

**Too many false positives:**
- Add to exclude patterns
- Tighten validation rules

**HTTP errors:**
- Add User-Agent header
- Implement rate limiting
- Check robots.txt
