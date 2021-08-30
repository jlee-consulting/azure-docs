---
title: How to sign guest configuration packages
description: You can optionally sign guest configuration content packages and force the agent to only allow signed content
ms.date: 07/12/2021
ms.topic: how-to
---

# How to sign guest configuration packages

Guest configuration custom policies use SHA256 hash to validate the policy
package hasn't changed. Optionally, customers may also use a certificate to sign
packages and force the guest configuration extension to only allow signed
content.

To enable this scenario, there are two steps you need to complete. Run the
cmdlet to sign the content package, and append a tag to the machines that should
require code to be signed.

## Signature validation using a code signing certificate

To use the Signature Validation feature, run the
`Protect-GuestConfigurationPackage` cmdlet to sign the package before it's
published. This cmdlet requires a 'Code Signing' certificate.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parameters of the `Protect-GuestConfigurationPackage` cmdlet:

- **Path**: Full path of the guest configuration package.
- **Certificate**: Code signing certificate to sign the package. This parameter is only supported
  when signing content for Windows.

## Certificate requirements

GuestConfiguration agent expects the certificate public key to be present in
"Trusted Root Certificate Authorities" on Windows machines and in the path
`/usr/local/share/ca-certificates/extra` on Linux machines. For the node to
verify signed content, install the certificate public key on the machine before
applying the custom policy. This process can be done using any technique inside
the VM or by using Azure Policy. An example template is available
[to deploy a machine with a certificate](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows).
The Key Vault access policy must allow the Compute resource provider to access
certificates during deployments. For detailed steps, see
[Set up Key Vault for virtual machines in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Following is an example to export the public key from a signing certificate, to
import to the machine.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

## Tag requirements

After your content is published, append a tag with name
`GuestConfigPolicyCertificateValidation` and value `enabled` to all virtual
machines where code signing should be required. See the
[Tag samples](../samples/built-in-policies.md#tags) for how tags can be
delivered at scale using Azure Policy. Once this tag is in place, the policy
definition generated using the `New-GuestConfigurationPolicy` cmdlet enables the
requirement through the guest configuration extension.

## Next steps

- [Test the package artifact](./guest-configuration-create-test.md)
  from your development environment.
- [Publish the package artifact](./guest-configuration-create-publish.md)
  so it is accessible to your machines.
- Use the `GuestConfiguration` module to
  [create an Azure Policy definition](./guest-configuration-create-definition.md)
  for at-scale management of your environment.
- [Assign your custom policy definition](../assign-policy-portal.md) using
  Azure portal.
- Learn how to view
  [compliance details for guest configuration](./determine-non-compliance.md#compliance-details-for-guest-configuration) policy assignments.
