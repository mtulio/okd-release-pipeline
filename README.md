# OKD Release Pipeline

## Prerequisites
1. create or update the following secrets:
* `githubapp-key`
  * contains key `githubapp-private-key`, which is private key of GitHub App (okd-tekton-token) with permissions to create releases in `okd-scos` project
* `okd-okd-robot-pull-secret`, which is a dockerconfig with permissions to push to `quay.io/okd/scos-release` and `quay.io/okd/scos-content`
* `okd-private-key`
  * contains key `private.key`, which is the GPG key used to sign the OKD release
* `prow-okd-secret`
  * contains key `ci-prow-token`, which is a Service Account token for the Prow CI cluster, with permissions to tag release images. This token comes from the secret (sa-image-tagger-token-chsw6) stored in the app.ci cluster on the origin project.
* `release-signing-gpg-key` - most probably not needed - to be confirmed
* `okd-bot-matrix-user`, which contains an access token for Matrix (under key `token`)
* `config-trusted-cabundle` : ca bundle
2. ```yaml
oc apply -k .
```

## Run the pipeline
Launch the pipeline run
Example for stable release: 
```bash
tkn pipeline start okd-release-pipeline --param release-controller="https://origin-release.ci.openshift.org" --param release-stream="4.13.0-0.okd-scos" --param release-imagestream="release-scos" --param content-mirror-pushspec="quay.io/okd/scos-content" --param release-mirror-pushspec="quay.io/okd/scos-release" --param github-org-repo="okd-project/okd-scos" --param is-release-latest=true --param enable-notifications=true --param matrix-room=\!nStsazaBvZCZQHPWTY:fedoraproject.org --param matrix-endpoint=matrix.org --param matrix-secret=okd-bot-matrix-user --workspace name=release-binaries,volumeClaimTemplateFile=templates/claimTemplate.yaml  --pipeline-timeout 4h 

```

Example for 4.next release: 
```bash
tkn pipeline start okd-release-pipeline --param release-controller="https://origin-release.ci.openshift.org" --param release-stream="4.14.0-0.okd-scos" --param release-imagestream="release-scos-next" --param content-mirror-pushspec="quay.io/okd/scos-content" --param release-mirror-pushspec="quay.io/okd/scos-release" --param github-org-repo="okd-project/okd-scos" --param is-release-latest=false --param enable-notifications=true --param matrix-room=\!nStsazaBvZCZQHPWTY:fedoraproject.org --param matrix-endpoint=matrix.org --param matrix-secret=okd-bot-matrix-user --workspace name=release-binaries,volumeClaimTemplateFile=templates/claimTemplate.yaml  --pipeline-timeout 4h 

```

## Annex - running a task individually
```bash

tkn task start create-github-release   --param github-org-repo="okd-project/okd-scos"    --param github-token-secret-key="gh-okd-token"    --param github-token-secret-name="gh-token"    --param gpg-key-id="maintainers@okd.io"    --param gpg-secret-key-name="private.key"    --param gpg-secret-name="okd-private-key"    --param mirrored-release-pullspec="quay.io/okd/scos-release:4.12.0-0.okd-scos-2022-12-02-083740"    --param release-name="4.12.0-0.okd-scos-2022-12-02-083740"    --param os-release='NAME="CentOS Stream CoreOS"
    ID="scos"
    ID_LIKE="rhel fedora"
    VERSION="412.9.202211241749-0"
    VERSION_ID="4.12"
    VARIANT="CoreOS"
    VARIANT_ID=coreos
    PLATFORM_ID="platform:el9"
    PRETTY_NAME="CentOS Stream CoreOS 412.9.202211241749-0"
    ANSI_COLOR="0;31"
    CPE_NAME="cpe:/o:centos:centos:9::coreos"
    HOME_URL="https://centos.org/"
    DOCUMENTATION_URL="https://docs.okd.io/latest/welcome/index.html"
    BUG_REPORT_URL="https://access.redhat.com/labs/rhir/"
    REDHAT_BUGZILLA_PRODUCT="OpenShift Container Platform"
    REDHAT_BUGZILLA_PRODUCT_VERSION="4.12"
    REDHAT_SUPPORT_PRODUCT="OpenShift Container Platform"
    REDHAT_SUPPORT_PRODUCT_VERSION="4.12"
    OPENSHIFT_VERSION="4.12"
    OSTREE_VERSION="412.9.202211241749-0"'
```
