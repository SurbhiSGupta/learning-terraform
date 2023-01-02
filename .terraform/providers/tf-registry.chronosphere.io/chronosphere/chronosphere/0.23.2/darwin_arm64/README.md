# Terraform Provider for Chronosphere

This repo contains a Terraform provider for Chronosphere resources.

See also the [the public README](docs/README.md) for using the provider.

## Using the provider

The `install` Make target will build the provider and copy it into your local Terraform plugins
directory. Once the provider has been installed, run `terraform init` to initialize it, and then
you can use it in a terraform module. See the `examples` folder for examples of how to use the
provider.

To find the current version of the Terraform provider, run `make version`

## Running scenario tests

The provider comes with a suite of integration tests that can run against a real environment.

The test suite _resets environment state_ so you'll need a dedicated RC environment and an internal service account
token:

```
./bin/droidcli mirror create -C master -e rc_rc_TODO --notes 'terraform testing'
./bin/droidcli tenant create-service-account --namespace rc-TODO -C local --name=terraformtest --cluster=rc
```

Prior to running scenario tests, set env vars for the RC environment using [set-chrono-env](https://stackoverflow.com/c/chronosphere/questions/163/164#164):

```
set-chrono-env --tf rc-TODO
```

You may also need to update your Google Cloud credentials:

```
gcloud auth login
```

Finally, run scenario tests:

```
make scenarios

# Or run a single test:
SCENARIO=TestBucket_SingleBucket make scenario
```

## Releasing a new version of the provider

First update docs to ensure they are up-to-date:

- [Changelog](CHANGELOG.md)
- [Docs in the monorepo](https://github.com/chronosphereio/monorepo/compare/master...terraform-unrealesed-changes)
- Examples reference upcoming provider version

Prestep for release:
1. Because we need so checksum and sign the checksum, you'll need to have the signing key
   on you local machine. To do this, run:
   ```sh
   $ eval $(op signin chronosphereinc)
   $ op get document terraform-signer-private.gpg | gpg --import
   ```

NOTE: If using a newer version of the 1password-cli (>= 2.0.0), the steps become:
   ```sh
   $ brew install --cask 1password-cli
   $ brew install gpg
   $ op account add # and follow prompts to add account for chronosphereinc.1password.com
   $ eval $(op signin)
   $ op document get terraform-signer-private.gpg | gpg --import
   ```


Then create the release:

1. On the `master` branch, tag a commit for the new version using `scripts/increment_patch.sh` or `scripts/increment_minor.sh`.
2. Run `make release` to build and publish artifacts.
3. Create a [Github release](https://github.com/chronosphereio/terraform-provider-chronosphere/releases).
4. Announce the release with a changelog in [#release-notes](https://chronosphereio.slack.com/channels/release-changes) channel and copy them into the [eng release notes doc](https://docs.google.com/document/d/1mttderrMxRY1e8-RpgYfkCoXYr389w9Hgntk2k2JLSw/edit).
5. Upgrade the monorepo's provider version to the new version, e.g. https://github.com/chronosphereio/monorepo/pull/20593

### Installing a public artifact

Released provider versions are publicly downloadable

First set the plugin version to download:

```
CHRONOSPHERE_TF_PLUGIN_VERSION=set_me    # Ex: 0.2.0
```

Then run the following to download that version of the provider to a temp directory:

```
cd /tmp
mkdir /tmp/chronosphere-terraform
rm /tmp/chronosphere-terraform/*
cd /tmp/chronosphere-terraform

(
    OS=$(uname -s | tr '[:upper:]' '[:lower:]')
    ARCH=$(uname -m | sed "s/x86_64/amd64/")

    ARTIFACT=terraform-provider-chronosphere_${CHRONOSPHERE_TF_PLUGIN_VERSION}_${OS}_${ARCH}.zip

    wget "https://storage.googleapis.com/chronosphere-terraform/${CHRONOSPHERE_TF_PLUGIN_VERSION}/${ARTIFACT}"

    unzip "${ARTIFACT}"
)
```

Optionally copy the provider to the local Terraform provider directory:

```
TF_PLUGIN_DIR=${TF_PLUGIN_DIR:-~/.terraform.d/plugins}

mkdir -p ${TF_PLUGIN_DIR}/local/chronosphereio/chronosphere

(
    OS=$(uname -s | tr '[:upper:]' '[:lower:]')
    ARCH=$(uname -m | sed "s/x86_64/amd64/")

    # terraform-provider-chronosphere_v${CHRONOSPHERE_TF_PLUGIN_VERSION}
    ARTIFACT=terraform-provider-chronosphere_${CHRONOSPHERE_TF_PLUGIN_VERSION}_${OS}_${ARCH}.zip

    cp terraform-provider-chronosphere_v${CHRONOSPHERE_TF_PLUGIN_VERSION} \
        ${TF_PLUGIN_DIR}/local/chronosphereio/chronosphere/${CHRONOSPHERE_TF_PLUGIN_VERSION}/${OS}_${ARCH}/
)
```

## Known limitations

- Cannot rename a notifier when it is referenced by notification policies.

- Cannot simultaneously delete a notifier while removing references to it from notification policies.
  This is due to a Terraform bug: https://github.com/hashicorp/terraform-provider-google/issues/6376
  Instead, this change must be done in two "terraform apply" steps:
  1. Update notification policy to no longer reference the notifier.
  2. Delete the notifier
