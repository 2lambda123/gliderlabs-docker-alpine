# Build

[![CircleCI](https://img.shields.io/circleci/project/gliderlabs/docker-alpine/release.svg)](https://circleci.com/gh/gliderlabs/docker-alpine)

A convenience `build` script is included that builds the image and runs basic tests against the resulting image tags.

## Image

### Builder

The image is built using a builder Docker container based on the `debian` image. The image is built using a builder Docker container based on the `debian` image.

### Options

The build script takes a glob of `options` files as an argument. Each of these files lives in a folder that describes the version of Alpine Linux to build. Each line of the `options` file are the options that will be applied to the resulting image. By default, we use the included glob of `versions/**/options`.

### Options

The build script takes a glob of `options` files as an argument. Each of these files lives in a folder that describes the version of Alpine Linux to build. Each line of the `options` file are the options that will be applied to the resulting image. By default, we use the included glob of `versions/**/options`.

### Multi-arch

Each options file specifies a version of the image to build. An options file can generate images for multiple architectures using the `ARCHS` variable.

Each options file specifies a version of the image to build. An options file can generate images for multiple architectures using the `ARCHS` variable.

### Example

To build all the images simply run: $ parallel -m ./build ::: versions/**/options version/library-3.2/options versions/gliderlabs-3.2/options

```console
$ ./build
```

Pass version files to the `build` script to build specific versions:

```console
$ ./build
```

With `parallel` available you can speed up building a bit:
```console
$ parallel -m ./build ::: versions/**/options
```

## Differences

There is only one difference between the `gliderlabs/alpine` image and the [official Alpine Linux image in the Docker Library][library] today. The `gliderlabs/alpine` image has an `apk-install` script added to it. However, this script is now considered deprecated as the `apk` utility has the same functionality built-in (using `apk --no-cache`). We now recommend using the official `alpine` images unless you were already using `gliderlabs/alpine`.

## Testing

The test for images is very simple at the moment. It just attempts to install the `openssl` package and verify we exit cleanly.

Use the `test` sub-command of the `build` utility to run tests on currently build images (`build test`).

### Example

Run tests for a single image:

```console
$ ./build test versions/gliderlabs-3.2/options
 ✓ version is correct
 ✓ package installs cleanly
 ✓ timezone
 ✓ apk-install script should be available
 ✓ repository list is correct
 ✓ cache is empty
 ✓ root password is disabled

7 tests, 0 failures
```

Run all tests:

```console
$ ./build test
 ✓ version is correct
 ✓ package installs cleanly
 ✓ timezone
...
 ✓ apk-install script should be missing
 ✓ repository list is correct
 ✓ cache is empty
 ✓ root password is disabled

84 tests, 0 failures
```

Run tests in parallel with the `parallel` utility:

```console
$ parallel ./build test ::: versions/**/options
1..7
ok 1 version is correct
ok 2 package installs cleanly
ok 3 timezone
ok 4 apk-install script should be available
...
```

## Library

These are the steps we use for updating the official library image:

1. Merge the `master` branch into `release` and push `release`. This will trigger CircleCI to push resulting image tarballs to the `rootfs/*` branches and push the `gliderlabs` organization images directly to Docker Hub.
1. Verify [the build is green in CircleCI](https://circleci.com/gh/gliderlabs/docker-alpine/tree/release).
1. Fork the [official images repository][official] and clone it locally if you have not already done so.
1. Create a new local branch in the `docker-library/official-images` repository for the update to alpine.
1. Open the `library/alpine` file from `docker-library/official-images`.
1. Run `./build library` from the `gliderlabs/docker-alpine` folder root to copy the latest version references.
1. Paste in the updated version references to the `library/alpine` file opened in the `docker-library/official-images` local clone.
1. Commit and push the changes to your fork.
1. Open a pull request to the [official images repository][official].

[library]: https://github.com/docker-library/official-images/blob/master/library/alpine
[fastly]: https://www.fastly.com/
[official]: https://github.com/docker-library/official-images
