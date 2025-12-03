# SSL Certificate Files for rentsalestay.com.ng

## Files Included

1. **rentsalestay.csr** - Certificate Signing Request (submit this to your SSL provider)
2. **rentsalestay-private.key** - Private Key (keep this SECRET and secure)

## Certificate Details

- **Domain:** rentsalestay.com.ng
- **Organization:** RentSaleStay
- **Department:** IT Department
- **Location:** Victoria Island, Lagos, Nigeria
- **Key Type:** RSA 2048-bit
- **Generated:** November 18, 2025

## How to Use

### Step 1: Submit CSR to SSL Provider

1. Open the file `rentsalestay.csr`
2. Copy the entire contents (including `-----BEGIN CERTIFICATE REQUEST-----` and `-----END CERTIFICATE REQUEST-----`)
3. Submit this to your SSL certificate provider:
   - Let's Encrypt (free)
   - Cloudflare (free with account)
   - Sectigo
   - GoDaddy
   - DigiCert
   - Any other SSL provider

### Step 2: Secure Your Private Key

1. **NEVER share** `rentsalestay-private.key` with anyone
2. **NEVER upload** it to any website or SSL provider
3. Store it securely - you'll need it when installing the SSL certificate
4. Keep a backup in a secure location

### Step 3: Install Certificate

Once your SSL provider issues your certificate:

1. You'll receive certificate files (usually .crt or .pem files)
2. You'll need both:
   - The issued certificate file(s)
   - Your private key (`rentsalestay-private.key`)
3. Install them on your web server or hosting platform

## Important Security Notes

- The private key file is **NOT** password protected
- Store it in a secure location with restricted access
- Never commit it to version control (git)
- Never share it via email or messaging apps
- Only the CSR should be shared with SSL providers

## Support

If you need to regenerate these files, you can use the OpenSSL command:

```bash
openssl req -new -newkey rsa:2048 -nodes \
  -keyout rentsalestay-private.key \
  -out rentsalestay.csr \
  -subj "/C=NG/ST=Lagos/L=Victoria Island/O=RentSaleStay/OU=IT Department/CN=rentsalestay.com.ng"
```

## Verification

To verify your CSR contains the correct information:

```bash
openssl req -text -noout -verify -in rentsalestay.csr
```
