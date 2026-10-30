# pika-vast-build

This public repository contains only the image build workflow. It compiles the
selected private `0xAndoroid/pika` revision and reads `docker/vast-image` from
private `0xAndoroid/pika-config` main. No private image assets are stored here.

The existing `PIKA_REPO_TOKEN` Actions secret must read both private repositories
and write the existing `ghcr.io/0xandoroid/pika-vast` package. Its name, registry
login and `latest`/short-Pika-commit tags are unchanged.

Pass the deployed daemon's Pika commit explicitly:

```sh
gh workflow run build.yml -R 0xAndoroid/pika-vast-build -f ref=<pika-commit>
```

Pull requests that change the workflow run only a private-repository read check,
using that same secret. They do not build or publish an image. Image publishing
runs only on manual dispatch. The private image recipe must be merged into
`pika-config` main before using the updated builder.
