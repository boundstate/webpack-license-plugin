<h1 align="center">
  <br>
  <a href="https://github.com/codepunkt/webpack-license-plugin"><img src="https://raw.githubusercontent.com/codepunkt/webpack-license-plugin/master/docs/logo.png" alt="" width="200"></a>
  <br>
  webpack-license-plugin
  <br>
</h1>

<h4 align="center">Manage third-party license compliance in your webpack build</h4>

<p align="center">
  <a href="https://badge.fury.io/js/webpack-license-plugin">
    <img src="https://img.shields.io/npm/v/webpack-license-plugin.svg?logo=npm&style=popout"/>
  </a>
  <a href="https://github.com/codepunkt/webpack-license-plugin/commits/master">
    <img src="https://img.shields.io/github/actions/workflow/status/codepunkt/webpack-license-plugin/test.yml?branch=main&style=popout&logo=github&logoColor=ddd" alt="Continuous integration status"/>
  </a>
  <a href="https://codecov.io/gh/codepunkt/webpack-license-plugin">
    <img src="https://img.shields.io/codecov/c/github/codepunkt/webpack-license-plugin/master.svg?style=popout&logo=codecov" alt="Code coverage"/>
  </a>
  <a href="https://snyk.io/test/github/codepunkt/webpack-license-plugin">
    <img src="https://img.shields.io/snyk/vulnerabilities/github/codepunkt/webpack-license-plugin.svg?logo=snyk&style=popout" alt="Known vulnerabilities"/>
  </a>
  <a href="https://choosealicense.com/licenses/mit/">
    <img src="https://img.shields.io/npm/l/webpack-license-plugin.svg?style=popout&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZlcnNpb249IjEuMSIgd2lkdGg9IjMyIiBoZWlnaHQ9IjMyIiB2aWV3Qm94PSIzIDMgMjYgMjYiPgogIDxwYXRoIGZpbGw9IiMzZGE2MzkiIGQ9Ik0xNiA1LjU1OWMtNi4xMTggMC0xMS4wNzggNC45Ni0xMS4wNzggMTEuMDc5IDAgNC43NDkgMi45ODkgOC43OTkgNy4xODggMTAuMzc0bDIuNTUzLTYuODA4YTMuODA4IDMuODA4IDAgMSAxIDIuNjc0IDBsMi41NTMgNi44MDhjNC4xOTktMS41NzUgNy4xODgtNS42MjUgNy4xODgtMTAuMzc0IDAtNi4xMTktNC45Ni0xMS4wNzktMTEuMDc5LTExLjA3OXoiLz4KPC9zdmc+Cg==&colorB=lightgray" alt="License: MIT"/>
  </a>
  <a href="https://twitter.com/intent/tweet?text=webpack-license-plugin%20by%20@code_punkt%20extracts%20open%20source%20license%20information%20about%20the%20npm%20packages%20in%20your%20webpack%20output%20and%20helps%20you%20identify%20and%20fix%20problems%20with%20open%20source%20licensing%20https://github.com/codepunkt/webpack-license-plugin%20#webpack%20#oss">
    <img src="https://img.shields.io/badge/tweet-⇢-1da1f3.svg?logo=twitter&style=popout" alt="Contact on Twitter"/>
  </a>
</p>

<p align="center">
  <a href="#key-features">Key Features</a> •
  <a href="#installation">Installation</a> •
  <a href="#how-to-use">How to use</a> •
  <a href="#available-options">Available options</a> •
  <a href="#examples">Examples</a>
</p>

# Key features

This plugin extracts _open source license information_ about all of the npm packages in your webpack output and helps you identify and fix problems with your open source licensing policy.

This plugin has full test coverage and is tested with **webpack 2, 3, 4 and 5**. It will help you:

- Discover every npm package used in your webpack output
- Find out how it is licensed
- Cancel builds that include unacceptable licenses
- Exclude internal packages from being scanned
- Create a customized inventory (usually called _bill of materials_) in `json`, `html`, `csv` or other formats


# Installation

Install `webpack-license-plugin` as a development dependency to your current project

#### npm

```console
$ npm install -D @boundstate/webpack-license-plugin
```

#### Yarn

```console
$ yarn add -D @boundstate/webpack-license-plugin
```

# How to use

Use `webpack-license-plugin` in your webpack configuration by adding it to the `plugins` array.

```js
const LicensePlugin = require('@boundstate/webpack-license-plugin')

module.exports = {
  plugins: [
    // there might be other plugins here
    new LicensePlugin()
  ],
}
```

# Available options

Options are given as an `Object` to the first parameter of the `LicensePlugin` constructor:

```js
new LicensePlugin({ outputFilename: 'thirdPartyNotice.json' })
```

The available options are:

|      Name       |             Description                                                                                                              |
| :------------- | :----------------------------------------------------------------------------------------------------------------------- |
| **`additionalFiles`** | Default: `{}`. Object that defines additional files that should be generated by this plugin based on it's default output (e.g. an html representation of the licenses in addition to the generated json). Keys represent filenames, values are functions that get invoked with the packages array and should return the content written to the additional file. These functions can be async or return a Promise. |
| **`excludedPackageTest`** | A method to exclude packages from the process. It is invoked with `packageName` (string) and `version` (string) of every package and should return true to exclude the package. |
| **`licenseOverrides`** | Default: `{}`. Object with licenses to override. Keys have the format `<name>@<version>`, values are valid [spdx license expressions](https://spdx.org/spdx-specification-21-web-version#h.jxpfx0ykyb60). This can be helpful when license information is inconclusive and has been manually checked. |
| **`outputFilename`** | Default: `oss-licenses.json`. Path to the output file that will be generated. Relative to the bundle output directory. |
| **`replenishDefaultLicenseTexts`** | Default: `false`. When this is enabled, default license texts are taken from spdx.org for packages where no license text was found. |
| **`unacceptableLicenseTest`** | A method to define license identifiers as unacceptable. It is invoked with `licenseIdentifier` (string) for every package and should return true when the license is unacceptable and encountering it should fail the build. |
| **`includePackages`** | Default: `() => []`. A method to define packages that should always be included in the output. It must return an array containing the absolute paths of those packages. This function can be async or return a Promise.

### Example with custom options

This example writes the result to a file named `meta/licenses.json` in the output directory, fails whenever it encounters one of the given licenses and overrides the license of the package `fuse.js@3.2.1`.

```js
const LicensePlugin = require('@boundstate/webpack-license-plugin')

module.exports = {
  // ...
  plugins: [
    new LicensePlugin({
      excludedPackageTest: (packageName, version) => {
        return packageName.startsWith('@internal/')
      },
      licenseOverrides: {
        // has "Apache" in package.json, but Apache-2.0 text in LICENSE file
        'fuse.js@3.2.1': 'Apache-2.0'
      },
      outputFilename: 'meta/licenses.json',
      unacceptableLicenseTest: (licenseIdentifier) => {
        return ['GPL', 'AGPL', 'LGPL', 'NGPL'].includes(licenseIdentifier)
      }
    }),
  ],
}
```

# Default output

The output is a `oss-licenses.json` file in the webpack build output directory. It contains an array of packages that were found to be part of the webpack output and lists several license-related details for every package:

| Name | Description |
| :------------- | :-------------------------------- |
| **`name`**  | package name |
| **`version`**  | package version |
| **`author`** | author listed in `package.json` _(if available)_ |
| **`repository`** | repository url listed in `package.json` _(if available)_ |
| **`source`** | package tarball url on npm registry |
| **`license`** | the license listed in `package.json`. If this is not a valid [spdx license expression](https://spdx.org/spdx-specification-21-web-version#h.jxpfx0ykyb60), this plugin will inform you. You can then inform the package maintainers about this problem and temporarily workaround this issue with the `licenseOverrides` option for the specific combination of package name and version. |
| **`licenseText`** | the license text read from a file matching `/^licen[cs]e/i` in the package's root |

### Example output file
```json
[
  {
    "name": "fbjs",
    "version": "0.8.17",
    "repository": "https://github.com/facebook/fbjs",
    "source": "https://registry.npmjs.org/fbjs/-/fbjs-0.8.17.tgz",
    "license": "MIT",
    "licenseText": "..."
  },
  {
    "name": "object-assign",
    "version": "4.1.1",
    "author": "Sindre Sorhus",
    "repository": "https://github.com/sindresorhus/object-assign",
    "source": "https://registry.npmjs.org/object-assign/-/object-assign-4.1.1.tgz",
    "license": "MIT",
    "licenseText": "..."
  },
  {
    "name": "react-dom",
    "version": "16.4.2",
    "repository": "https://github.com/facebook/react",
    "source": "https://registry.npmjs.org/react-dom/-/react-dom-16.4.2.tgz",
    "license": "MIT",
    "licenseText": "..."
  },
  {
    "name": "react",
    "version": "16.4.2",
    "repository": "https://github.com/facebook/react",
    "source": "https://registry.npmjs.org/react/-/react-16.4.2.tgz",
    "license": "MIT",
    "licenseText": "..."
  }
]
```


# Examples

## _additionalFiles_ examples

When the `additionalFiles` option is set, the output shown above is passed into the transform function as an array for every additional file configured.

This way, the output can be formatted to any format you might need and then be written to one or more additional files.

### Package list as CSV

```js
const LicensePlugin = require('@boundstate/webpack-license-plugin')

const csvTransform = (packages) => {
  const keys = ['name', 'version', 'license']

  return [
    '"sep=,"',
    keys.join(','),
    ...packages.map(pckg => keys.map(key => `="${pckg[key]}"`).join(',')),
  ].join('\n')
}

module.exports = {
  // ...
  plugins: [
    new LicensePlugin({
      additionalFiles: {
        'oss-licenses.csv': csvTransform
      }
    }),
  ],
}
```

### Package list and additional summary

```js
const LicensePlugin = require('@boundstate/webpack-license-plugin')

module.exports = {
  // ...
  plugins: [
    new LicensePlugin({
      additionalFiles: {
        'oss-summary.json': packages => {
          return JSON.stringify(
            packages.reduce(
              (prev, { license }) => ({
                ...prev,
                [license]: prev[license] ? prev[license] + 1 : 1,
              }),
              {}
            ),
            null,
            2
          )
        },
      },
    }),
  ],
}
```
