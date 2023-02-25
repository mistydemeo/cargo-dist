<div class="oranda-hide">

# `cargo-dist`

</div>

[![crates.io](https://img.shields.io/crates/v/cargo-dist.svg)](https://crates.io/crates/cargo-dist)
[![docs](https://docs.rs/cargo-dist/badge.svg)](https://docs.rs/cargo-dist)
[![Rust CI](https://github.com/axodotdev/cargo-dist/workflows/Rust%20CI/badge.svg?branch=main)](https://github.com/axodotdev/cargo-dist/actions/workflows/ci.yml)

`cargo build` but For Building Final Distributable Artifacts and uploading them to an archive.

The Big Idea of cargo-dist is that we want to streamline all the steps of providing prebuilt binaries
for a rust project. This includes:

1. Generating your "Cut A Release" Github CI for you
2. Picking good build flags for a "production" release
3. Making zips and installers for the resulting binaries
4. Generating a machine-readable manifest so other tools can understand the results
5. Uploading all the resulting artifacts to a Github Release™️

Even though cargo-dist is primarily a tool for building and packaging applications (steps 2-4), we put a fair amount of effort into Generating Your CI Scripts For You because we want to be able to run things locally and know what the CI *should* do without actually running it. It also helps avoid needless vendor lock-in -- in an ideal world, migrating from Github to Gitlab or your own personal infra would be just one invocation of cargo-dist away!

That said, the current version is Experimental and the extra conveniences currently only work with Github CI and Github Releases™️!

# Read The Book!

We've got all the docs you need over at the [cargo-dist book](https://axodotdev.github.io/cargo-dist/book/)!

* [Introduction](https://axodotdev.github.io/cargo-dist/book/introduction.html)
* [Install](https://axodotdev.github.io/cargo-dist/book/install.html)
* [Way-Too-Quickstart](https://axodotdev.github.io/cargo-dist/book/way-too-quickstart.html)
* [Guide](https://axodotdev.github.io/cargo-dist/book/guide.html)
* [Reference](https://axodotdev.github.io/cargo-dist/book/reference.html)

# Contributing

## Updating Snapshots

cargo-dist's tests rely on [cargo-insta](https://crates.io/crates/cargo-insta) for snapshot testing various
outputs. This allows us to both catch regressions and also more easily review UI/output changes. If a snapshot
test fails, you will need to use the `cargo insta` CLI tool to update them:

```sh
cargo install cargo-insta
```

One installed, you can review and accept the changes with:

```sh
cargo insta review
```

If you know you like the changes, just use `cargo insta accept` to auto-apply all changes.

(If you introduced brand-new snapshot tests you will also have to `git add` them!)

> NOTE: when it succeeds, cargo-dist-schema's `emit` test will actually commit the results back to disk to `cargo-dist-schema/cargo-dist-schema.json` as a side-effect. This is a janky hack to make sure we have that stored and up to date at all times (the test also uses an insta snapshot but insta snapshots include an extra gunk header so it's not something we'd want to link end users). The file isn't even used for anything yet, I just want it to Exist because it seems useful and important. In the future we might properly host it and have our outputs link it via a `$schema` field.

## Cutting Releases

cargo-dist is self-hosting, so just follow the usual [usage instructions](#usage-ci) and publish with `cargo release 1.0.0`! (Or whatever cooler version you prefer.)

If that releases succeeds, we recommend updating the boostrap version of cargo-dist as a follow up:

* install the version of cargo-dist you just released by downloading the binary
* edit cargo-dist-version in the root Cargo.toml to that version
* run `cargo dist generate-ci` to regenerate the CI
* commit, push to main

Note that as a consequence of the way we self-host, cargo-dist's published artifacts will always be built/generated by a previous version of itself. This can be problematic if you make breaking changes to cargo-dist-schema's format... so don't! Many things in the schema are intentionally optional to enable forward and backward compatibility, so this should hopefully work well!

(Future work: mark `cargo release` do more magic like cutting RELEASES.md and whatnot?)
