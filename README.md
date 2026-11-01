# pika-vast-build

This public repository dispatches the image workflow in private
`0xAndoroid/pika-config` and waits for its result. It does not check out private
source, build an image, download private logs, or transfer source/image artifacts.
The public run exposes only the private run link and terminal status.

Pass the deployed daemon's Pika commit explicitly:

```sh
gh workflow run build.yml -R 0xAndoroid/pika-vast-build -f ref=<pika-commit>
```

`EXECUTOR_REVISION` in `build.yml` fixes the reviewed private workflow commit.
Before dispatch, its `pika-vast-executor-<full-commit>` release must be immutable
and its tag must resolve to that exact commit. A missing or mutable release
fails; the helper never substitutes `main`. The private workflow uses that same
commit for its image recipe and startup fixture. Updating the pin requires
another source review.

The current pin is an interim candidate. Final canonical native names and the
matching immutable executor release must be settled before either source change
merges or any image is published. A changed config commit, including a squash
merge, requires the pin to match the final reviewed executor commit.

The private workflow retrieves source, builds and tests, then publishes on
separate runners. It keeps private transfers in private Actions artifacts and
publishes the existing `ghcr.io/0xandoroid/pika-vast:latest` and short-Pika-commit
tags only after the actual-image fixture succeeds. The selected Pika source
`ref` retains its existing meaning.

The existing helper secret `PIKA_REPO_TOKEN` stays here. It needs Contents read
and Actions write/read on `0xAndoroid/pika-config` for the pinned release metadata,
dispatch and status requests. Pull requests run only the metadata-read check;
that does not prove dispatch or registry-write permission. No secret is copied
into the private workflow or passed as a dispatch input.

The private executor needs its own prepared source-read credential and write
access to the image package. Publication uses its publish job's `GITHUB_TOKEN`,
not a persistent publication token. The package must grant Actions Write access
to `0xAndoroid/pika-config`; that grant has not been verified. The private source
documents the source credential, package grant, immutable executor release and
native-image acceptance prerequisites. This change satisfies none of them.
Build dispatch and image publication require their normal authorization.
