---
title: Set up an apex domain in Azure Static Web Apps
description: Configure the root domain in Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 02/11/2022
ms.author: cshoe
---

# Set up an apex domain in Azure Static Web Apps

Domain names without a subdomain are known as apex or root domains. For example, the domain `www.example.com` is the `www` subdomain joined with the `example.com` apex domain.

Some domain registrars (like Google and GoDaddy) don't allow you to point the apex domain to an existing URL. If your registrar doesn't support `ALIAS` or `ANAME` records, or doesn't allow `CNAME` flattening, then you can't point your apex domain to the generated URL for your static web app.

If your registrar doesn't allow you to redirect the apex domain, consider the following options:

* Configure your domain with [Azure DNS](custom-domain.md)
* Forward the apex domain to the `www` subdomain

This guide demonstrates two options for configuring an apex domain.

* Use the steps to [set up with an ALIAS record](#set-up-with-an-alias-record) if your domain registrar supports the `ALIAS` DNS record.

    If your registrar doesn't support `ALIAS` records, but does support `ANAME` records or `CNAME` flattening, see their documentation for configuration settings.

* Use the steps in [forward to www subdomain](#forward-to-www-subdomain) if your domain registrar doesn't support the `ALIAS` DNS record.

## Set up with an ALIAS record

Before you create the `ALIAS` record, you first need to validate that you own the domain.

### Validate ownership

1. Open the [Azure portal](https://portal.azure.com).

1. Navigate to your static web app.

1. From the *Overview* window, copy the generated **URL** of your site and set it aside in a text editor for future use.

1. Under *Settings*, select **Custom domains**.

1. Select the **+ Add** button.

1. In the *Enter domain* tab, enter your apex domain name.

    For instance, if your domain name is `example.com`, enter `example.com` into this box (without any subdomains).

1. Select the **Next** button.

1. In the *Validate + Configure* tab, enter the following values.

    | Setting | Value |
    |---|---|
    | Domain name | This value should match the domain name you entered in the previous step. |
    | Hostname record type | Select **TXT**. |

1. Select the **Generate code** button.

    Wait as the code is generated. It make take a minute or so to complete.

1. Once the `TXT` record value is generated, select the **copy button** (next to the generated value) to copy the code to the clipboard.

1. Select the **Close** button.

1. Open a new browser tab and sign in to your domain registrar account.

1. Navigate to your domain name's DNS configuration settings.

1. Add a new `TXT` record with the following values.

    | Setting | Value |
    |--|--|
    | Type | `TXT` |
    | Host | Enter **@** |
    | Value | Paste the generated code value you copied from the Azure portal. |
    | TTL (if applicable) | Leave as default value. |

1. Save changes to your DNS record.

### Set up an ALIAS record

1. Return to your domain name's DNS configuration settings.

1. Add a new `ALIAS` record with the following values.

    | Setting | Value |
    |--|--|
    | Type | `ALIAS` |
    | Host | Enter **@** |
    | Value | Paste the generated URL you copied from the Azure portal. Make sure to remove the `https://` prefix from your URL. |
    | TTL (if applicable) | Leave as default value. |

1. Save changes to your DNS record.

    Since DNS settings need to propagate, this process can take some time to complete.

1. Open a new browser tab and navigate to your apex domain.

    After the DNS records are updated, you should see your static web app in the browser. Also, inspect the location to verify that your site is served securely using `https`.

## Forward to www subdomain

Each domain registrar has a different process for managing domain names. Once you sign in to your account with your registrar, look for domain forwarding options. Some registrars have this functionality listed under *DNS options*, while others have them associated with *Website options*.

Make sure as you set up forwarding that you only configure the apex domain to forward to the `www` subdomain.

See your registrar's documentation for details.

## Next steps

> [!div class="nextstepaction"]
> [Manage the default domain](custom-domain-default.md)
