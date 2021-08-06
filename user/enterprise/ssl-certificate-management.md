---
title: Travis CI Enterprise SSL Certificate Management
layout: en_enterprise
---

This page contains information relating to SSL certificates and their use in Travis CI Enterprise (TCIE).

By default, Travis CI Enterprise verifies SSL for all traffic between internal platform components, RabbitMQ, and e.g. Github.com/Github Enterprise. SSL verification helps to ensure that traffic is delivered securely.

> It is **strongly recommended** that you configure your installation with a valid certificate from a trusted authority.

If you have questions about a specific section or have an issue that is not covered, please contact us at [enterprise@travis-ci.com](mailto:enterprise@travis-ci.com) for assistance.

## Using Self-Signed Certificates

We strongly recommend that you use a valid certificate from a trusted certificate authority when configuring your installation. That said, we recognize that there are various scenarios in which using a self-signed certificate might make more sense for your organization.

> In all cases of using self-signed certificates you must [disable SSL certificate validation](#disabling-ssl-verification) in your settings. This can be done after installation is complete.

> After installation you can upload a certificate at any time via the [Admin Dashboard](#adding-or-updating-certificates-after-installation).

#### Using a trial license

If you are using a trial license to evaluate Travis CI Enterprise, it might not make sense to use a trusted certificate. For this scenario we offer the ability to use self-signed certificates that are generated by Travis CI Enterprise itself.

##### Trial in TCIE 3.x 

You may use your admin-console to [upload your self-signed SSL certificate](#adding-or-updating-certificates-in-tcie-3x) and/or you may need to [disable SSL Verification](#disabling-ssl-in-tcie-3x) mechanism. 

##### Trial in TCIE 2.x 

You can select the 'Use Self-Signed Cert' option when prompted to provide a custom SSL certificate during installation.

#### Organizations with an internally managed certificate authority

Some organizations manage their own internal certificate authority. An internal authority may use a root certificate provided by a [trusted root authority](https://en.wikipedia.org/wiki/Certificate_authority).

#### Without a root certificate from a trusted certificate authority

If your internal certificate authority is not using a root certificate provided by a trusted certificate authority, any certificates produced are considered to be 'untrusted' by most tools and browsers, same as a self-signed certificate.

Certificates created by an internal certificate authority can be uploaded during installation in the same way as any other certificate.

> Please be aware that you will also need to follow the [instructions on disabling SSL verification](#disabling-ssl-verification) below in order to use Travis CI Enterprise.

#### With a root certificate from a trusted certificate authority

If your organization's internal certificate authority does use a root certificate purchased from a trusted certificate authority, the above will not apply. You will not need to disable SSL verification and can proceed as if you are using a certificate directly from a trusted certificate authority.

> In some cases it may be necessary to include the intermediate certificate chain in the settings in order for SSL verification to work correctly. The reasons for this can vary depending on your internal setup and your root certificate provider. Please see the section below on [how to set intermediate certificate chains](#using-intermediate-certificate-chains) for instructions.

### Disabling SSL Verification

> SSL verification helps to ensure that all traffic between Travis CI Enterprise components is protected. Disabling SSL verification has serious implications!

#### Disabling SSL Verification in TCIE 3.x

1. Access the admin-console config page under `http://localhost:8800`. If it's not available, try running `kubectl kots admin-console --namespace [namespace]` and access `http://localhost:8800` (admin-console is run on local machine).
2. Browse to 'Config'
3. Find ‘Advanced settings’
4. Disable SSL verification via markign appropriate checkbox and save settings
5. Click 'Save Config' at the bottom of the page.
6. New version will appear in Dashboard. Click 'Deploy'. The changes will be deployed in your Kubernetes cluster.

![Disabling SSL in TCIE 3.x](/images/tcie-3.x-ssl-2.png)


#### Disabling SSL Verification in TCIE 2.x

To disable SSL verification in Travis CI Enterprise 2.x:

1. Log into your dashboard at `https://<your-travis-ci-enterprise-domain>:8800`.
2. Click on 'Settings' in the topmost menu.
3. Click 'Advanced Settings' in the left menu (or scroll down).
4. Check the box for 'Disable SSL cert verification'.
5. Click 'Save' at the bottom of the page.

> Please note that this will require a restart of Travis CI Enterprise in order to take affect and may cause brief downtime.

This will remove SSL verification checks with internal traffic between Travis CI components. This will also cause all repository hooks created on Github to turn off SSL verification for traffic coming to Travis CI.

> Please note that this setting will only apply to hooks created _after_ the setting was enabled.

## Adding or Updating Certificates after Installation

You can add or update certificates via the Admin Dashboard at any time after installation.

### Adding or Updating Certificates in TCIE 3.x

1. Access the admin-console config page under `http://localhost:8800`. If it's not available, try running `kubectl kots admin-console --namespace [namespace]` and access `http://localhost:8800` (admin-console is run on local machine). 
2. Browse to 'Config'
3. Find 'HTTP Setting'
4. Please choose between: 
    1. If you want to use an automatically generated by TCIE 3.x self-signed certificate: select ‘HTTPS enabled’ option. 
    2. If you want to upload your own SSL certificat: select ‘HTTPS enabled’ and ‘Upload SSL keys’ options - two text fields will be shown for uploading TLS certificate and key. Paste appropriate data in.
    3. If you want to use generated [letsencrypt certificates](#using-a-lets-encrypt-ssl-certificate): select ‘Let's Encrypt HTTPS option’
    > Please note that during the very first installation of TCIE 3.x, when DNS records do not point yet to the correct Load Balancer in the Kubernetes cluster, Let's Encrypt certificate cannot be properly generated. Therefore, configure a self-signed certificate first and after the deployment of the Platform and DNS setup are done, re-configure the TCIE Platform to use the Let's Encrypt certificate.
5. Click 'Save Config' at the bottom of the page.
6. New version will appear in Dashboard. Click 'Deploy'. The changes will be deployed in your Kubernetes cluster.
    
![Adding or updating SSL in TCIE 3.x](/images/tcie-3.x-ssl-1.png)

> Please note that selecting 'HTTPS disable' will disable https for whole Platform installation.



### Adding or Updating Certificates in TCIE 2.x

To upload your certificates:

1. Log into your dashboard at `https://<your-travis-ci-enterprise-domain>:8800`.
2. Click the gear icon on the rightmost side of the top menu and select 'Console Settings' from the dropdown.
3. Click 'TLS Key & Cert' in the left menu (or scroll down).
4. Select the appropriate option depending on the location of your certificate files.
5. Enter the paths or select the files to upload.
6. Click 'Save' at the bottom of the page.

> Please note that this will require a restart of Travis CI Enterprise in order to take affect and may cause brief downtime.

> A valid x509 certificate and private key files are required. The certificate and key must be in PEM format. The key must be unencrypted.

## Using Intermediate Certificate Chains

In some cases it may be required to provide the [intermediate certificate chain](https://support.dnsimple.com/articles/what-is-ssl-certificate-chain/) for your desired certificate. Reasons for this can vary depending on your internal setup and your root certificate provider.

If you have uploaded a valid certificate but are still seeing unexpected issues related to SSL verification errors, you can set your intermediate certificate chain in the settings.

### Intermediate Certificate Chains in TCIE 3.x

Not available at the moment. Will be added soon.

### Intermediate Certificate Chains in TCIE 2.x

To add your certificate chain:

1. Log into your dashboard at `https://<your-travis-ci-enterprise-domain>:8800`.
2. Click on 'Settings' in the topmost menu.
3. Click 'Advanced Settings' in the left menu (or scroll down).
4. Copy and paste your certificate chain into the text box labeled 'Custom Certificate Authority (CA) Bundle'.
5. Click 'Save' at the bottom of the page.

> Please note that this will require a restart of Travis CI Enterprise in order to take affect and may cause a brief downtime.

Upon restart you can then verify whether SSL verification is now working. If you are still experiencing trouble even after setting the chain, please contact us at [enterprise@travis-ci.com](mailto:enterprise@travis-ci.com) for assistance.

## Using a Let's Encrypt SSL Certificate

You can use a certificate from [Let's Encrypt](https://letsencrypt.org/) instead of a self-signed certificate or a certificate purchased from a trusted certificate authority. Certificates from Let's Encrypt are free and behave the same as those purchased from a trusted certificate authority.

What you will need:

- An email address (Let's Encrypt will send notifications regarding urgent renewal and security issues).
- A domain name under which your installation is available.

> The following steps require downtime for your Travis CI Enterprise instance. For this reason we recommend that you perform these steps during a maintenance window.

### Let's Encrypt SSL Certificate in TCIE 3.x

Since `certbot` is already installed within TCIE 3.0 images, you don't need to install it seperately.

1. Access the admin-console config page under `http://localhost:8800`. If it's not available, try running `kubectl kots admin-console --namespace [namespace]` and access `http://localhost:8800` (admin-console is run on local machine). 
2. Browse to 'Config'
3. Find 'HTTP Setting'
4. Choose from existing methods select ‘Lets Encrypt HTTPS option’
5. Click 'Save Config' at the bottom of the page.
6. New version will appear in Dashboard. Click 'Deploy'. The changes will be deployed in your Kubernetes cluster.

The Let's Encrypt certificate will be automatically generated and installed. A restart of nginx will occur during the deploy. 

### Installing certbot in TCIE 2.x

We will be using [certbot](https://certbot.eff.org/#ubuntutrusty-other) to obtain an SSL certificate from Let's Encrypt. To install certbot:

1. Open an SSH connection to the platform machine.
2. Add the certbot personal package archive:
  ```bash
  sudo add-apt-repository ppa:certbot/certbot
  ```
3. Update available packages:
  ```bash
  sudo apt-get update
  ```
3. Install required certbot dependency:
  ```bash
  sudo apt-get install software-properties-common
  ```
5. Install the certbot package:
  ```bash
  sudo apt-get install certbot
  ```

### Generating a new Let's Encrypt certificate in TCIE 2.x

The certbot tool offers multiple ways to obtain a certificate. We'll pick the 'temporary webserver' option since it doesn't require any additional setup or configuration. The only prerequisite is that the Travis CI Enterprise container must be stopped so that the certbot webserver can bind properly to port 443.



#### Generating a new Let's Encrypt certificate TCIE 2.x 

> Your Travis CI Enterprise instance will be unavailable during this process.

1. Open an SSH connection to the platform machine.
2. Stop your Travis CI Enterprise:
  ```bash
  replicatedctl app stop
  ```
3. Start the interactive certificate process:
  ```bash
  sudo certbot certonly
  ```
4. Add the prompt select option `1` to `Spin up a temporary webserver`:
  ```bash
  How would you like to authenticate with the ACME CA?
  -------------------------------------------------------------------------------
  1: Spin up a temporary webserver (standalone)
  2: Place files in webroot directory (webroot)
  -------------------------------------------------------------------------------
  Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 1
  ```
5. Fill in your email address:
  ```bash
  Enter email address (used for urgent renewal and security notices) (Enter 'c' to
  cancel): ops@example.com
  ```
6. Accept the Terms of Services:
  ```bash
  -------------------------------------------------------------------------------
  Please read the Terms of Service at
  https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf. You must agree
  in order to register with the ACME server at
  https://acme-v01.api.letsencrypt.org/directory
  -------------------------------------------------------------------------------
  (A)gree/(C)ancel: A
  ```
7. Decide if you'd like to share your email address with the EFF (this decision has no effect on your certificate or your Travis CI Enterprise instance):
  ```bash
  -------------------------------------------------------------------------------
  Would you be willing to share your email address with the Electronic Frontier
  Foundation, a founding partner of the Let's Encrypt project and the non-profit
  organization that develops Certbot? We'd like to send you email about EFF and
  our work to encrypt the web, protect its users and defend digital rights.
  -------------------------------------------------------------------------------
  (Y)es/(N)o: N
  ```
8. Provide your domain name:
  ```bash
  Please enter in your domain name(s) (comma and/or space separated)  (Enter 'c'
  to cancel): <your-travis-ci-enterprise-domain>
  ```
9. Upon successful completion you should see a message similar to the following:
  ```bash
  IMPORTANT NOTES:
     Congratulations! Your certificate and chain have been saved at:
     /etc/letsencrypt/live/<your-travis-ci-enterprise-domain>/fullchain.pem
     Your key file has been saved at:
     /etc/letsencrypt/live/<your-travis-ci-enterprise-domain>/privkey.pem
     Your cert will expire on 2018-02-07. To obtain a new or tweaked
     version of this certificate in the future, simply run certbot
     again. To non-interactively renew *all* of your certificates, run
     "certbot renew"
  ```
10. Restart your Travis CI Enterprise instance:
  ```bash
  replicatedctl app start
  ```

Your certificate is now generated and saved on your Travis CI Enterprise platform machine. However, you must take additional steps to configure your Travis CI Enterprise instance to use the new certificate. Please see the [Using a Let's Encrypt certificate](#using-a-lets-encrypt-certificate) section for instructions.

#### Using a Let's Encrypt certificate in TCIE 2.x 

> Your Travis CI Enterprise instance will be unavailable during this process.

To use your generated certificate in your Travis CI Enterprise 2.x instance:

1. Log into your dashboard at `https://<your-travis-ci-enterprise-domain>:8800`.
2. On the rightmost side of the top menu click the gear icon and select 'Console Settings' from the dropdown.
3. Click 'TLS Key & Cert' in the left menu (or scroll down).
4. Select the 'Server path' option.
5. Enter the paths to your certificate that were provided in the certbot output above. Example:
  ```
  - SSL Private Key Filename: `/etc/letsencrypt/live/<your-travis-ci-enterprise-domain>/privkey.pem`
  - SSL Certificate Filename: `/etc/letsencrypt/live/<your-travis-ci-enterprise-domain>/fullchain.pem`
  ```
6. Click 'Save' at the bottom of the page.
7. Open an SSH connection to the platform machine.
8. Stop your Travis CI Enterprise instance:
  ```bash
  replicatedctl app stop
  ```
9. Restart your Travis CI Enterprise instance:
  ```bash
  replicatedctl app start
  ```

### Renewing your certificate

Let's Encrypt certificates are short-lived and ***expire after 90 days.*** Because of this you will need to renew them on a regular basis. This can be done manually with certbot in TCIE 2.x.

### Renewing your Let's Encrypt certificate for TCIE 3.x

At the moment, Let's Encrypt certificate is automatically refreshed every time the nginx pod is started. So in order to refresh it:

1. Kill the nginx pod
2. Deploy it again (e.g. scale replicas down to 0 and back to target number)

### Renewing your Let's Encrypt  certificate for TCIE 2.x

> Your Travis CI Enterprise instance will be unavailable during this process.

To manually renew your certificate for another 90 days:

1. Open an SSH connection to the platform machine.
2. Stop your Travis CI Enterprise instance:
  ```bash
  replicatedctl app stop
  ```
3. Perform the certificate renewal:
  ```bash
  sudo certbot renew
  ```
4. Restart your Travis CI Enterprise instance:
  ```bash
  replicatedctl app start
  ```

### Automate renewal of your Let's Encrypt SSL Certificate with a cron job in TCIE2.x

You can avoid manual renewals by using a cron job to automate the certbot renewal:

1. Create `/home/ubuntu/renew-certs.sh` containing the following script:

    ```bash
    #!/bin/bash

    set -u

    function usage() {
      echo
      echo "Usage: $(basename $0)"
      echo
      echo "Simple script to renew certs, should be used via a cron. Assumes that certs are already set up."
      echo
      echo "See https://docs.travis-ci.com/user/enterprise/platform-tips/#use-a-lets-encrypt-ssl-certificate for initial setup info."
      echo

      exit 1
    }

    if [[ $# -ne 0 ]]; then
      usage
    fi

    replicatedctl app stop
    sudo certbot renew
    replicatedctl app start
    ```
    {: data-file="/home/ubuntu/renew-certs.sh"}

2. Make the script executable:

    ```sh
    $ chmod +x /home/ubuntu/renew-certs.sh
    ```

3. Create a cronjob by editing `/etc/crontab` and appending the following:

    ```sh
    # Renews certs at 2am on the 1st of February, May, August, and November.
    # Please change the configuration that applies to the certificate you are creating.
    0 2 1 2,5,8,11 * /home/ubuntu/renew-certs.sh
    ```
    {: data-file="/etc/crontab"}

> This process will introduce a small amount of downtime while the certificates are renewed. We recommend that you communicate with your users before each renewal so they are aware that their builds will temporarily be stopped until the certificate is renewed.