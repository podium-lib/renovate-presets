# Renovate bot presets

This repo contain a set of [Renovate Bot](https://www.whitesourcesoftware.com/free-developer-tools/renovate/) presets that modules in this organization can depend on.

The purpose of these presets is to be able to optimize the process of keeping dependencies up to date accross all modules in this organization while minimizing the amount of releases of each module in this organization caused by automatically bumping dependencies.

As an example lets say we have a set of modules using Renovate Bot and Semantic Release structured like so:

```sh
|--/@podium/module-a
   |--/3rd-party-dependency
   |--/@podium/module-b
      |--/3rd-party-dependency
```

`@podium/module-a` and `@podium/module-b` depend on the same `3rd-party-dependency`.

`@podium/module-a` is a public facing module that other modules can use as a dependency. `@podium/module-b` is __not__ a public facing module. Rather, it's an internal module which contains code that is only of interest to other modules within our organisation and it should never be used as a dependency by external modules.

If we were to use the default configuration from Renovate Bot the following would happen when we have Semantic Release in our setup and there is a new version of the `3rd-party-dependency`: `@podium/module-a` would get a PR with an update of the `3rd-party-dependency` and when merged a new release of `@podium/module-a` would be cut. Meanwhile `@podium/module-b` would also get a PR with an update of the `3rd-party-dependency` and when merged a new release of `@podium/module-b` would also be cut. 

This release of `@podium/module-b` would again create a new PR in `@podium/module-a` and cause a new release of `@podium/module-a` when merged. 

In other words; Due to both `@podium/module-a` and `@podium/module-b` depending on `3rd-party-dependency` an update of `3rd-party-dependency` will cause two releases of `@podium/module-a` which is not optimal.

## Module categorisation

To deal with problems such as that described in the introduction above we separate modules into two categories, top level modules and sub modules, where each category has slightly different groupings and dependency strategies.

In short; sub modules receive and merge dependencies as they arrive. Top level modules receive and merge dependencies on a less frequent schedule. This more or less removes the problem outlined above.

### Top level modules

Top level modules are publicly facing modules. These are modules that other external modules and projects will use as a dependency. 

Top level modules have their dependencies updated once a week.

To use the preset for top level modules within another module, add the following config to `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>podium-lib/renovate-presets:top-level-module"
  ]
}
```

### Sub modules

Sub modules are __not__ publicly facing. These are internal modules which contain code only of interest to top level modules and should never be used by external projects and modules as dependencies.

Sub modules have their dependencies updated as they are published.

To use the preset for sub modules within another module, add the following config to `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>podium-lib/renovate-presets:sub-level-module"
  ]
}
```

## Dev dependencies

Dev dependencies are updated once a month. Both top level and sub modules have the same dev dependency preset.

## License

See license file.
