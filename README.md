# Post-build Clean Buildpack

A simple buildpack to run after all other buildpacks have completed,
which removes a set of files defined in `.slugremove`, so that they
are not included in the finished slug.

## Rationale

While this may seem to duplicate functionality provided by Heroku's
`.slugignore`, there is a key difference: `.slugignore`'d files are
removed after the repo is cloned, but before any buildpack is run. They
can therefore not be involved in the build process itself.

However, it is not uncommon for there to exist files in the repo that
are necessary for the build, but are not required at runtime. There may
also be installable build dependencies that are not runtime
dependencies.

In our case, a complex front-end build involves significant CSS, JS and
image assets, along with a large installation of node modules, all of
which are used only for building the production assets, but then remain
part of the slug.

## Usage

You can use [Heroku's multi-buildpack support](https://devcenter.heroku.com/articles/using-multiple-buildpacks-for-an-app)
to add this buildpack to your existing buildpack(s). The post-build-clean buildpack **must** be last in the
buildpack order, which the following command will ensure:

```sh-session
heroku buildpacks:add https://github.com/pilotcreative/heroku-buildpack-slugremove.git
```

The `.slugremove` file supports single-file and single-directory
declarations only, e.g.:

```
some_huge_file.psd
some/nested/directory
why_does_this_app_even_contain_a.tiff

```

**Remember to add a trailing line in the end of the file**
