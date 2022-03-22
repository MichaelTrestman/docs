---
title: cockroach cert
summary: A secure CockroachDB cluster uses TLS for encrypted inter-node and client-node communication.
toc: true
key: create-security-certificates.html
filter_category: security_cert
filter_html: Use cockroach cert
filter_sort: 1
docs_area: reference.cli
---

The CockroachDB CLI's `cockroach cert` command allows you to create transport layer security (TLS) certificates. The CLI offers the following functionality:
- Generate a root certificate authority (CA) certificate for your cluster, and use it to sign public certificates.
- Generate key pair for use by a CockroachDB node.
- Generate a key pair for use by a TLS-authenticated CockroachDB client.


{{site.data.alerts.callout_info}}
`cockroach cert` can only be used to sign key pairs with a certificate authority (CA) private key that is **present on the local file-system** where the command is being run. This limitation may be incompatible
{{site.data.alerts.end}}






!!! {
 cockroach-cert.html should only provide informationa about the command.
 Declarative specifications about tls should go to [Reference / Security / Transport Layer Security](security-refence/transport-layer-security.html)
How to make certs stuff should go to:
-[Manage / Security / TLS Keys and Certificates / Managing TLS Credentials with the CockroachDB CLI]()
-[Manage / Security / TLS Certificates / Managing TLS Credentials with OpenSSL](../create-security-certificates-openssl.html)

With a distinct (less funneled but easy to search) discoverability for:
-[Manage / Security / TLS Certificates / Managing TLS Credentials Using a Custom Certificate Authority (CA)](create-security-certificates-custom-ca.html)

also remove this one from the filter tabs thing.

}!!!

## Subcommands

### `create-ca`

Create a certificate authorit a self-signed certificate authority (CA) key pair (private key and public certificate), which you'll use to create and authenticate certificates for your entire cluster.

{% include_cached copy-clipboard.html %}
~~~ shell
cockroach cert create-ca \
 --certs-dir=[path-to-certs-directory] \
 --ca-key=[path-to-ca-key]
~~~

### `create-node`

Create a certificate and key for a specific node in the cluster. You specify all addresses at which the node can be reached and pass appropriate flags.

Create a node certificate and key:

{% include_cached copy-clipboard.html %}
~~~ shell
cockroach cert create-node \
  [node-hostname] \
  [node-other-hostname] \
  [node-yet-another-hostname] \
  [hostname-in-wildcard-notation] \
  --certs-dir=[path-to-certs-directory] \
  --ca-key=[path-to-ca-key]
~~~

### `create-client`

Create a certificate and key for a [specific user](create-user.html) accessing the cluster from a client. You specify the username of the user who will use the certificate and pass appropriate flags.

Create a client certificate and key:

{% include_cached copy-clipboard.html %}
~~~ shell
cockroach cert create-client \
 [username] \
 --certs-dir=[path-to-certs-directory] \
 --ca-key=[path-to-ca-key]
~~~


### `list`

List certificates and keys found in the certificate directory.

~~~ shell
cockroach cert list \
 --certs-dir=[path-to-certs-directory]
~~~

### View help

For the `cert` command overall:

{% include_cached copy-clipboard.html %}
~~~ shell
cockroach cert --help
~~~

For a subcommand:

{% include_cached copy-clipboard.html %}
~~~ shell
cockroach cert <subcommand> --help
~~~


## Flags

The `cert` command and subcommands support the following [general-use](#general) and [logging](#logging) flags.

### General

Flag | Description
-----|-----------
`--certs-dir` | The path to the [certificate directory](#certificate-directory) containing all certificates and keys needed by `cockroach` commands.<br><br>This flag is used by all subcommands.<br><br>**Default:** `${HOME}/.cockroach-certs/`
`--ca-key` | The path to the private key protecting the CA certificate. <br><br>This flag is required for all `create-*` subcommands. When used with `create-ca` in particular, it defines where to create the CA key; the specified directory must exist.<br><br>**Env Variable:** `COCKROACH_CA_KEY`
`--allow-ca-key-reuse` | When running the `create-ca` subcommand, pass this flag to re-use an existing CA key identified by `--ca-key`. Otherwise, a new CA key will be generated.<br><br>This flag is used only by the `create-ca` subcommand. It helps avoid accidentally re-using an existing CA key.
`--overwrite` | When running `create-*` subcommands, pass this flag to allow existing files in the certificate directory (`--certs-dir`) to be overwritten.<br><br>This flag helps avoid accidentally overwriting sensitive certificates and keys.
`--lifetime` | The lifetime of the certificate, in hours, minutes, and seconds. <br><br>Certificates are valid from the time they are created through the duration specified in `--lifetime`.<br><br>**Default:** `87840h0m0s` (10 years)
`--key-size` | The size of the CA, node, or client key, in bits.<br><br>**Default:** `2048`
<a name="flag-pkcs8"></a> `--also-generate-pkcs8-key` | Also create a key in [PKCS#8 format](https://tools.ietf.org/html/rfc5208), which is the standard key encoding format used by Java.  For example usage, see [Build a Java App with CockroachDB](build-a-java-app-with-cockroachdb.html).

### Logging

[The `--log` and `--log-config-file` flags can be used to configure logging behavior for all CockroachDB CLI commands](../configure-logs.html), including `cockroach cert`.
{% include {{ page.version.version }}/misc/logging-defaults.md %}
