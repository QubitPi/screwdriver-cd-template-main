# template-main
[![Version][npm-image]][npm-url] ![Downloads][downloads-image] [![Build Status][status-image]][status-url] [![Open Issues][issues-image]][issues-url] ![License][license-image]

> Validates and publishes templates

## Usage

```bash
npm install -g screwdriver-template-main
```

Create a Screwdriver pipeline with your template repo and start the build to validate and publish it.

To update a Screwdriver template, make changes in your SCM repository and rerun the pipeline build.

### Validating a template

Run the `template-validate` script. By default, the path `./sd-template.yaml` will be read. However, a user can specify a custom path using the env variable: `SD_TEMPLATE_PATH`.

Example `screwdriver.yaml`:

```yaml
shared:
    image: node:18
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - install: npm install -g screwdriver-template-main
      - validate: template-validate
    environment:
      SD_TEMPLATE_PATH: ./path/to/template.yaml
```

`template-validate` can print a result as json by passing `--json` option to the command.

```
$ template-validate --json
{"valid":true}
```

### Publishing a template

Run the `template-publish` script. By default, the path `./sd-template.yaml` will be read. However, a user can specify a custom path using the env variable: `SD_TEMPLATE_PATH`.

Example `screwdriver.yaml` with validation and publishing:

```yaml
shared:
    image: node:18
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - install: npm install -g screwdriver-template-main
      - validate: template-validate
  publish:
    requires: main
    steps:
      - install: npm install -g screwdriver-template-main
      - publish: template-publish --tag stable
```

`template-publish` can print a result as json by passing `--json` option to the command. `template-publish` will tag the published version as well. The default tag is `latest` if none is specified.

```
$ template-publish --json
{name:"template/foo",version:"1.2.3",tag:"stable"}
```

### Removing a template

To remove a template, run the `template-remove` script. You'll need to add an argument for the template name. Removing a template will remove _all_ of its versions.

Example `screwdriver.yaml` with validation and publishing, and template removal as a detached job:

```yaml
shared:
    image: node:18
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - install: npm install -g screwdriver-template-main
      - validate: template-validate
  publish:
    requires: main
    steps:
      - install: npm install -g screwdriver-template-main
      - publish: template-publish
  remove_template:
    steps:
      - install: npm install -g screwdriver-template-main
      - remove: template-remove --name templateName
```

`template-remove` can print a result as json by passing `--json` option to the command.

```
$ template-remove --json --name templateName
{"name":"templateName"}
```

### Removing a template version

To remove a specific version of a template, run the `template-remove-version` binary. This must be done in the same pipeline that published the template. You'll need to specify the template name and version as arguments.
Removing a template version will remove all the tags associated with it.

Example `screwdriver.yaml` with validation, publishing and tagging, and version removal as a detached job:

```yaml
shared:
  image: node:18
  steps:
    - init: npm install -g screwdriver-template-main
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - validate: template-validate
  publish:
    requires: main
    steps:
      - publish: template-publish
      - tag: template-tag --name templateName --version 1.0.0 --tag latest
  detached_remove_version:
    steps:
      - remove: template-remove-version --name templateName --version 1.0.0
```

`template-remove-tag` can print a result as json by passing `--json` option to the command.

### Tagging a template

Optionally, tag a template using the `template-tag` script. This must be done in the same pipeline that published the template. You'll need to add arguments for the template name and tag. You can optionally specify a version; the version must be an exact version, not just a major or major.minor one. If omitted, the latest version will be tagged.

Example `screwdriver.yaml` with validation and publishing and tagging:

```yaml
shared:
  image: node:18
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - install: npm install -g screwdriver-template-main
      - validate: template-validate
  publish:
    requires: main
    steps:
      - install: npm install -g screwdriver-template-main
      - publish: template-publish
      - tag: template-tag --name templateName --version 1.2.3 --tag stable
```

`template-tag` can print a result as json by passing `--json` option to the command.

```
$ template-tag --json --name templateName --version 1.2.3 --tag stable
{"name":"templateName","tag":"stable","version":"1.2.3"}
```

### Removing a template tag


To remove a template tag, run the `template-remove-tag` binary. This must be done in the same pipeline that published the template. You'll need to specify the template name and tag as arguments.

Example `screwdriver.yaml` with validation, publishing and tagging, and tag removal as a detached job:

```yaml
shared:
  image: node:18
  steps:
    - init: npm install -g screwdriver-template-main
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - validate: template-validate
  publish:
    requires: main
    steps:
      - publish: template-publish
      - tag: template-tag --name templateName --version 1.0.0 --tag latest
  detached_remove_tag:
    steps:
      - remove: template-remove-tag --name templateName --tag latest
```

`template-remove-tag` can print a result as json by passing `--json` option to the command.

```
$ template-remove-tag --json --name templateName --tag stable
{"name":"templateName","tag":"stable"}
```

### Getting the version from a template tag

To get the version from a template tag, run the `template-get-version-from-tag` binary. This must be done in the same pipeline that published the template. You'll need to add arguments for the template name and tag.

Example `screwdriver.yaml` with validation, publishing and tagging, and getting a version as a detached job:

```yaml
shared:
  image: node:18
  steps:
    - init: npm install -g screwdriver-template-main
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - validate: template-validate
  publish:
    requires: main
    steps:
      - publish: template-publish
      - tag: template-tag --name templateName --version 1.0.0 --tag latest
  detached_get_version_from_tag:
    steps:
      - get_version: template-get-version-from-tag --name templateName --tag latest
```
## Pipeline Templates

### Validating a template

Run the `pipeline-template-validate` script. By default, the path `./sd-template.yaml` will be read. However, a user can specify a custom path using the env variable: `SD_TEMPLATE_PATH`.

Example `screwdriver.yaml`:

```yaml
shared:
  image: node:18
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - install: npm install -g screwdriver-template-main
      - validate: pipeline-template-validate
    environment:
      SD_TEMPLATE_PATH: ./path/to/pipeline-template.yaml
```

`pipeline-template-validate` can print a result as json by passing `--json` option to the command.

```
$ pipeline-template-validate --json
{"valid":true}
```

### Publishing a pipeline template

Run the `pipeline-template-publish` script. By default, the path `./sd-template.yaml` will be read. However, a user can specify a custom path using the env variable: `SD_TEMPLATE_PATH`.

Example `screwdriver.yaml` with validation and publishing:

```yaml
shared:
  image: node:18
jobs:
  main:
    requires: [~pr, ~commit]
    steps:
      - install: npm install -g screwdriver-template-main
      - validate: pipeline-template-validate
  publish:
    requires: main
    steps:
      - install: npm install -g screwdriver-template-main
      - publish: pipeline-template-publish --tag stable
```

`pipeline-template-publish` can print a result as json by passing `--json` option to the command. `pipeline-template-publish` will tag the published version as well. The default tag is `latest` if none is specified.

```
$ pipeline-template-publish --json
{namespace:"template", name:"foo",version:"1.2.3",tag:"stable"}
```

## Testing

```bash
npm test
```

## License

Code licensed under the BSD 3-Clause license. See LICENSE file for terms.

[npm-image]: https://img.shields.io/npm/v/screwdriver-template-main.svg
[npm-url]: https://npmjs.org/package/screwdriver-template-main
[downloads-image]: https://img.shields.io/npm/dt/screwdriver-template-main.svg
[license-image]: https://img.shields.io/npm/l/screwdriver-template-main.svg
[issues-image]: https://img.shields.io/github/issues/screwdriver-cd/template-main.svg
[issues-url]: https://github.com/screwdriver-cd/template-main/issues
[status-image]: https://cd.screwdriver.cd/pipelines/493/badge
[status-url]: https://cd.screwdriver.cd/pipelines/493
