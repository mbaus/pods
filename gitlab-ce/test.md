# TLS

## https://docs.gitlab.com/charts/installation/tls.html
helm install gitlab gitlab/gitlab \
  --set certmanager-issuer.email=you@example.com


---

# authentication

## Registry authentication certificates
### Generate a certificate-key pair:
```
mkdir -p certs
openssl req -new -newkey rsa:4096 -subj "/CN=gitlab-issuer" -nodes -x509 -keyout certs/registry-example-com.key -out certs/registry-example-com.crt
```
Create a secret containing these certificates. We will create registry-auth.key and registry-auth.crt keys inside the <name>-registry-secret secret. Replace <name> with the name of the release.

kubectl create secret generic <name>-registry-secret --from-file=registry-auth.key=certs/registry-example-com.key --from-file=registry-auth.crt=certs/registry-example-com.crt

This secret is referenced by the global.registry.certificate.secret setting.

Registry sensitive notification headers
Check documentation regarding configuring Registry notifications for more details.

The secret content should be a list of items, even if it contains a single item. If the content is just a string, the charts WILL NOT convert it to a list as needed.

Consider the example where registry-authorization-header secret with value RandomFooBar is created.

kubectl create secret generic registry-authorization-header --from-literal="value=[RandomFooBar]"

By default, the key used within the secret is “value”. However, users can use a different key, but must ensure that it’s specified as key under the header map item.

SSH Host Keys
Generate the OpenSSH certificate-key pairs:

mkdir -p hostKeys
ssh-keygen -t rsa  -f hostKeys/ssh_host_rsa_key -N ""
ssh-keygen -t dsa  -f hostKeys/ssh_host_dsa_key -N ""
ssh-keygen -t ecdsa  -f hostKeys/ssh_host_ecdsa_key -N ""
ssh-keygen -t ed25519  -f hostKeys/ssh_host_ed25519_key -N ""

Create the secret containing these certificates. Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-shell-host-keys --from-file hostKeys

This secret is referenced by the global.shell.hostKeys.secret setting.

Initial Enterprise license
This method will only add a license at the time of installation. Use the Admin Area in the web user interface to renew or upgrade licenses.
Create a Kubernetes secret for storing the Enterprise license for the GitLab instance. Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-license --from-file=license=/tmp/license.gitlab

Then use --set global.gitlab.license.secret=<name>-gitlab-license to inject the license into your configuration.

You can also use the global.gitlab.license.key option to change the default license key pointing to the license in the license secret.

Initial root password
Create a Kubernetes secret for storing the initial root password. The password should be at least 6 characters long. Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-initial-root-password --from-literal=password=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 32)

Redis password
Generate a random 64 character alpha-numeric password for Redis. Replace <name> with the name of the release.

kubectl create secret generic <name>-redis-secret --from-literal=secret=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64)

If deploying with an already existing Redis cluster, please use the password for accessing the Redis cluster that has been base64 encoded instead of a randomly generated one.

GitLab Shell secret
Generate a random 64 character alpha-numeric secret for GitLab Shell. Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-shell-secret --from-literal=secret=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64)

This secret is referenced by the global.shell.authToken.secret setting.

Gitaly secret
Generate a random 64 character alpha-numeric token for Gitaly. Replace <name> with the name of the release.

kubectl create secret generic <name>-gitaly-secret --from-literal=token=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64)

This secret is referenced by the global.gitaly.authToken.secret setting.

Praefect secret
Generate a random 64 character alpha-numeric token for Praefect. Replace <name> with the name of the release:

kubectl create secret generic <name>-praefect-secret --from-literal=token=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64)

This secret is referenced by the global.praefect.authToken.secret setting.

GitLab Rails secret
Replace <name> with the name of the release.

cat << EOF > secrets.yml
production:
  secret_key_base: $(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 128)
  otp_key_base: $(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 128)
  db_key_base: $(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 128)
  encrypted_settings_key_base: $(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 128)
  openid_connect_signing_key: |
$(openssl genrsa 2048 | awk '{print "    " $0}')
  ci_jwt_signing_key: |
$(openssl genrsa 2048 | awk '{print "    " $0}')
EOF

kubectl create secret generic <name>-rails-secret --from-file=secrets.yml

This secret is referenced by the global.railsSecrets.secret setting.

The encrypted_settings_key_base was added in GitLab 13.7, and will be required for GitLab 14.0.
GitLab Workhorse secret
Generate the workhorse secret. This must have a length of 32 characters and base64-encoded. Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-workhorse-secret --from-literal=shared_secret=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 32 | base64)

This secret is referenced by the global.workhorse.key setting.

GitLab Runner secret
Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-runner-secret --from-literal=runner-registration-token=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64)

GitLab KAS secret
GitLab Rails will always requires that a secret for KAS is present, even if one deploys this chart without installing the KAS sub-chart. Still, one can create this secret manually by following the below procedure or leave it to the chart to auto-generate the secret.

Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-kas-secret --from-literal=kas_shared_secret=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 32 | base64)

MinIO secret
Generate a set of random 20 & 64 character alpha-numeric keys for MinIO. Replace <name> with the name of the release.

kubectl create secret generic <name>-minio-secret --from-literal=accesskey=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 20) --from-literal=secretkey=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64)

This secret is referenced by the global.minio.credentials.secret setting.

PostgreSQL password
Generate a random 64 character alpha-numeric password. Replace <name> with the name of the release.

kubectl create secret generic <name>-postgresql-password \
    --from-literal=postgresql-password=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64) \
    --from-literal=postgresql-postgres-password=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64)

Grafana password
If configuring Grafana integration, generate a random 64 character alpha-numeric password.

generate_secret_if_needed "gitlab-grafana-initial-password" --from-literal=password=$(gen_random 'a-zA-Z0-9' 64)

GitLab Pages secret
Generate the GitLab Pages secret. This must have a length of 32 characters and base64-encoded. Replace <name> with the name of the release.

kubectl create secret generic <name>-gitlab-pages-secret --from-literal=shared_secret=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 32 | base64)

This secret is referenced by the global.pages.apiSecret.secret setting.

Registry HTTP secret
Generate a random 64 character alpha-numeric key shared by all registry pods. Replace <name> with the name of the release.

kubectl create secret generic <name>-registry-httpsecret --from-literal=secret=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64 | base64)

Praefect DB password
Generate a random 64 character alpha-numeric password. Replace <name> with the name of the release:

kubectl create secret generic <name>-praefect-dbsecret \
    --from-literal=secret=$(head -c 512 /dev/urandom | LC_CTYPE=C tr -cd 'a-zA-Z0-9' | head -c 64) \

This secret is referenced by the global.praefect.dbSecret setting.

External services
Some charts have further secrets to enable functionality that can not be automatically generated.

OmniAuth
In order to enable the use of OmniAuth Providers with the deployed GitLab, please follow the instructions in the Globals chart

LDAP Password
If you need password authentication to connect with your LDAP server, you must store the password in a Kubernetes secret.

kubectl create secret generic ldap-main-password --from-literal=password=yourpasswordhere

Then use --set global.appConfig.ldap.servers.main.password.secret=ldap-main-password to inject the password into your configuration.

Use the Secret name, not the actual password when configuring the Helm property.
SMTP password
If you are using an SMTP server that requires authentication, store the password in a Kubernetes secret.

kubectl create secret generic smtp-password --from-literal=password=yourpasswordhere

Then use --set global.smtp.password.secret=smtp-password in your Helm command.

Use the Secret name, not the actual password when configuring the Helm property.
IMAP password for incoming emails
To let GitLab have access to incoming emails store the password of the IMAP account in a Kubernetes secret.

kubectl create secret generic incoming-email-password --from-literal=password=yourpasswordhere

Then use --set global.appConfig.incomingEmail.password.secret=incoming-email-password in your Helm command along with other required settings as specified in the docs.

Use the Secret name, not the actual password when configuring the Helm property.
IMAP password for service desk emails
To let GitLab have access to service_desk emails store the password of the IMAP account in a Kubernetes secret.

kubectl create secret generic service-desk-email-password --from-literal=password=yourpasswordhere

Then use --set global.appConfig.serviceDeskEmail.password.secret=service-desk-email-password in your Helm command along with other required settings as specified in the docs.

Use the Secret name, not the actual password when configuring the Helm property.
S/MIME Certificate
Outgoing email messages can be digitally signed using the S/MIME standard. The S/MIME certificate needs to be stored in a Kubernetes secret as a TLS type secret.

kubectl create secret tls smime-certificate --key=file.key --cert file.crt

If there is an existing secret as a opaque type, then the global.email.smime.keyName and global.email.smime.certName values will need to be adjusted for the specific secret.

S/MIME settings can be set through the values.yaml file or on the command line. Use --set global.email.smime.enabled=true to enable S/MIME and --set global.email.smime.secretName=smime-certificate to specify the secret that contains the S/MIME certificate.

Smartcard Authentication
Smartcard authentication uses a custom Certificate Authority (CA) to sign client certificates. The certificate of this custom CA needs to be injected to the Webservice pod for it to verify whether a client certificate is valid or not. This is provided as a k8s secret.

kubectl create secret generic <secret name> --from-file=ca.crt=<path to CA certificate>

The key name inside the secret where the certificate is stored MUST BE ca.crt.

Next steps