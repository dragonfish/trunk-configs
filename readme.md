<!-- trunk-ignore(markdownlint/MD041) -->
<p align="center">
  <a href="https://docs.trunk.io">
    <img height="128" src="https://static.trunk.io/assets/vscode_icon.png" />
  </a>
</p>
<h2 align="center">Trunk Configs</h2>
<h3 align="center">Clean default configs for linters, formatters, and more</h2>
<p align="center">
  <a href="https://marketplace.visualstudio.com/items?itemName=Trunk.io">
    <img src="https://img.shields.io/visual-studio-marketplace/i/Trunk.io?logo=visualstudiocode"/>
  </a>
  <a href="https://slack.trunk.io">
    <img src="https://img.shields.io/badge/slack-slack.trunk.io-blue?logo=slack"/>
  </a>
  <a href="https://docs.trunk.io">
    <img src="https://img.shields.io/badge/docs.trunk.io-7f7fcc?label=docs&logo=readthedocs&labelColor=555555&logoColor=ffffff"/>
  </a>
  <a href="https://api.securityscorecards.dev/projects/github.com/trunk-io/configs">
    <img src="https://api.securityscorecards.dev/projects/github.com/trunk-io/configs/badge"/>
  </a>
</p>

> 🎉 [Trunk][trunk] is a constantly evolving product. We'd appreciate your feedback - stop by the
> [Trunk Community Slack](https://slack.trunk.io/) and let us know what you think. Thanks!

Here are the default **configuration files, linters, and actions** that we turn on in all of our own
repositories during development. Every setting has been carefully scrutinized to optimize our own
workflow. Feel free to import them for your own use, or turn them all on by adding the following to
your `.trunk/trunk.yaml`:

```yaml
plugins:
  sources:
    - id: configs
      uri: https://github.com/trunk-io/configs
      ref: v0.0.1
```

Included in this repository is a set of `exported_configs`. Simply source this repository and you
will automatically have access to all of the config files enumerated in [plugin.yaml](./plugin.yaml)
whenever you run `trunk check`. You can override these configs by adding your own file with the same
name to `.trunk/configs`.

For more information about [plugins](https://github.com/trunk-io/plugins) and Trunk config merging,
check out our [docs](https://docs.trunk.io)!

## Three tips for successful linter configs

### 1. Turn off formatting errors, use autoformatters instead

Nobody wants to hear about every missing space as a separate lint error. Don't run prettier via
eslint. Don't run flake8 formatting errors instead of autoformatting with
[black](https://github.com/psf/black), etc. The same theme rings true with most language linter
setups.

### 2. Put your linter configs in standalone files

Sure, you can embed your eslint settings in `package.json` or your flake8 settings in `setup.cfg`,
but please don't. It's just good separation of concerns. It's easy for other people to find the
config options in standalone files, if you stop using a linter it's easy to delete the config, it's
easy to look up the format of a config or get editor autocompletion (they often have schemas on
schemastore.org), everything is easier.

### 3. Keep your linter configs in one place in .trunk/configs

To override any of the config files exported from this repo, simply include a config file with the
same name in your `.trunk/configs` directory or in the root of your repo. Trunk will take care of
the rest.

## Why you should spend more time on linter configs

An ounce of prevention is worth a pound of cure. Running more well configured linters will reduce
your bugs, increase consistency, reduce security vulnerabilities, and increase your overall speed.
Spend a little time now, reap the rewards forever.

## Notes

### `.editorconfig`

Always have a `.editorconfig` file, not only for users of your repo using editors, but also some
linters use the settings in `.editorconfig`. For example `shfmt` uses it to figure out indentation,
and `prettier` will also respect it as a fallback to its own config (though that is undocumented).

<!-- ### `.eslintrc.yaml`

Open to contributions! It's really tricky to make a general eslint config that works for everyone,
so we don't have one in this collection yet. Eslint's config structure is _the_ singularly most
complicated linter config we've seen, and we've seen a lot. That said, there is one must have:

Use `eslint-config-prettier` to disable any formatting rules eslint turns on:

```bash
npm install --save-dev eslint-config-prettier
```

In your eslint config:

```yaml
extends:
  - eslint:recommended
  ...
  - prettier
```

This allows you to autoformat your code with prettier instead of seeing formatting issues one line
at a time via eslint.

### `.clang-format`

Clang-format can format a bunch of languages, but we prefer `prettier` on languages they both cover.
To lint additional languages, you need a section for that language as is shown in this
`.clang-format`. Note there's a section for proto: Clang-format can autoformat proto files too!
Cool.

This config is yaml, but it's kind of multiple yaml files stacked into the same file. Bad config
design, but a good tool!

### `.flake8`

We turned off all the formatting categories because [black](https://github.com/psf/black) handles
autoformatting. No one should be hearing about formatting issues one space at a time. This is a
theme across many linters.

Black itself has a black-compatible flake8 config
[here](https://github.com/psf/black/blob/main/.flake8), however it keeps flake8 formatting errors
_on_. If you've autoformatted with black, you won't have any flake8 errors with their config, but
really you should be gating your CI on both black and flake8 (with [trunk]), and it's much nicer to
hear that you just need to autoformat your file with black, not hear about every missing space as a
different lint error.

### `.clang-tidy`

Clang-tidy has one of the more annoying configs around. Tidy has ~50 rules which are aliases of
other rules. If you don't disable them, you end up getting duplicate results _and_ tidy takes
longer.

Also, the config is yaml, but the `Checks` key takes a string which is a comma separated list
instead of a yaml list. :( The comment blocks at the top describe what we've turned on/off and why.

### `.markdownlint.yaml`

We turned off all formatting categories which are handled by prettier. If you use [trunk], you'll
just see that your file needs to be autoformatted, not every instance of missing whitespace in your
markdown.

### `.shellcheckrc`

This config turns on as much as possible, but relies on sourcing other scripts always relative to
the current script's directory. If you want to turn off checking related to `source`, you could add:

```text
disable=SC1090
disable=SC1091
```

### `.stylelintrc.yaml`

This config turns on the standard scss config, which is a superset of the standard css config and
compatible whether you use scss or not.

Stylelint does require you to install plugins locally though:

```bash
npm install --save-dev stylelint stylelint-config-standard-scss stylelint-config-prettier
```

This config also uses `stylelint-config-prettier`, which turns off all formatting rules which
prettier takes care of. Since you'll be enabling prettier, you don't need to hear about formatting
issues that prettier will take care of.

Note: In `trunk.yaml` you'll want to enable both `stylelint-fmt` and `stylelint`. The former
automatically fixes what stylelint supports automatic fixes for, and runs via `trunk check` and
`trunk fmt`. The latter is for issues without autofixes.

### `svgo.config.js`

This config is curated to _not break_ things, as opposed to minify maximally. Specifically it will
leave embedded raster images, and both `viewbox` and `width`/`height` attributes in place.

### `.yamllint.yaml`

Yamllint mostly has formatting rules without autofixes. As mentioned above, we recommend using
autoformatters as opposed to linters which give formatting errors one space at a time. This config
enables only select rules, and not any formatting rules which prettier handles automatically. -->

## What's the best way to run linters and formatters?

[Trunk][trunk] 🎉 ([docs](https://docs.trunk.io) •
[vscode extension](https://marketplace.visualstudio.com/items?itemName=trunk.io) •
[github action](https://github.com/trunk-io/trunk-action))

## Contributing

Think there's a better setup for one of these linters? Put up a PR and let's see it! Also happy to
see contributions for linters we haven't covered here in our
[plugins](https://github.com/trunk-io/plugins) repo.

[trunk]: https://trunk.io/
