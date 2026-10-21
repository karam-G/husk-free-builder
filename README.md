# Husk Free Builder

This repository contains only a build workflow. The Husk source remains in the private `karam-G/Husk-Audit` repository.

## One-time setup

1. Create a fine-grained personal access token at https://github.com/settings/personal-access-tokens/new
   - Resource owner: `karam-G`
   - Repository access: **Only select repositories** → `Husk-Audit`
   - Repository permissions: **Contents: Read-only** (no other permissions)
   - Set an expiration date and generate the token.
2. Open this helper repository's **Settings → Secrets and variables → Actions → New repository secret**.
   - Name: `HUSK_READ_TOKEN`
   - Secret: paste the token you just generated.
3. Add another repository secret named `HUSK_ARTIFACT_PASSWORD`. Use a strong password/passphrase and save it somewhere safe. Do not post it in issues or chat. It encrypts the IPA because this helper repository is public.
4. Open **Actions → Build private Husk IPA → Run workflow**.

The workflow checks out branch `husk-windows-build` from the private source, builds it on a standard macOS runner, encrypts the IPA, and uploads the encrypted output as an artifact. The public helper repository does not contain the source or an unencrypted IPA.

## Download and decrypt on Windows

Download the workflow artifact from the completed run and extract it. If the artifact contains `Husk.ipa.enc`, install OpenSSL on Windows or use a trusted OpenSSL distribution, then run in PowerShell from the extracted folder:

```powershell
$env:HUSK_ARTIFACT_PASSWORD = Read-Host "Enter your saved artifact password"
openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 -pass env:HUSK_ARTIFACT_PASSWORD -in .\Husk.ipa.enc -out .\Husk.ipa
Remove-Item Env:HUSK_ARTIFACT_PASSWORD
```

Keep the password private. Anyone with the password and encrypted artifact can decrypt the IPA. This workflow does not sign the app for App Store distribution; installation on a personal iPhone may still require an appropriate signing/install method.
