# Fix for Strapi v5
Copy of repository https://github.com/marlokessler/strapi-plugin-image-optimizer to fix the errors for strapi v5

---

<img src="assets/logo.png" alt="image optimizer logo" width="75"/>

# Strapi plugin image optimizer

![Version](https://img.shields.io/npm/v/strapi-plugin-image-optimizer?label=strapi-plugin-image-optimizer)
[![License](https://img.shields.io/github/license/marlokessler/strapi-plugin-image-optimizer)](https://github.com/marlokessler/strapi-plugin-image-optimizer/blob/main/LICENSE)
![Dependencies](https://img.shields.io/librariesio/github/marlokessler/strapi-plugin-image-optimizer)
[![Deploy](https://github.com/marlokessler/strapi-plugin-image-optimizer/actions/workflows/deploy.yml/badge.svg)](https://github.com/marlokessler/strapi-plugin-image-optimizer/actions/workflows/deploy.yml)
[![All Contributors](https://img.shields.io/github/all-contributors/marlokessler/strapi-plugin-image-optimizer)](#contributors-)

## Table of contents

- [Requirements](#requirements)
- [Installation](#installation)
  - [1. Install package](#1-install-package)
  - [2. Extend Strapi's upload plugin](#2-extend-strapis-upload-plugin)
  - [3. Add config options](#3-add-config-options)
- [Config options](#config-options)
  - [Object `Config`](#object-config)
  - [Object `ImageSize`](#object-imagesize)
  - [Type `SourceFormat`](#type-sourceformat)
  - [Type `OutputFormat`](#type-outputformat)
  - [Type `ImageFit`](#type-imagefit)
  - [Type `ImagePosition`](#type-imageposition)
  - [Example config](#example-config)
- [Usage](#usage)
- [Found a bug?](#found-a-bug)
- [Contributors](#contributors-)

## Requirements

Strapi version >= v4.11.7

## Note

This plugin uses [sharp](https://sharp.pixelplumbing.com/) provided via [strapi core](https://github.com/strapi/strapi/blob/3bfe1c913cf037c85a167f83a4bda0d848c9ba50/packages/core/upload/package.json#L52). All settings and options are documented in more detail in the [sharp API documentation](https://sharp.pixelplumbing.com/api-resize).

## Installation

### 1. Install package

Install the package via `npm install strapi-plugin-image-optimizer` or `yarn add strapi-plugin-image-optimizer`.

### 2. Extend Strapi's upload plugin

To make this plugin work, you need to enter the following code to `./src/extensions/upload/strapi-server.ts`. If file and folders do not exist, you need to create them. This code overrides the default image manipulation service of Strapi's `upload` plugin.

```typescript
// ./src/extensions/upload/strapi-server.ts

import imageOptimizerService from "strapi-plugin-image-optimizer/dist/server/services/image-optimizer-service";

module.exports = (plugin) => {
  plugin.services["image-manipulation"] = imageOptimizerService;
  return plugin;
};
```

### 3. Add config options

Configure the plugin in the `config/plugins.(js/ts)` file of your Strapi project.

## Config options

### Object `Config`

| Option                  | Type                                     | Description                                                                                                                                                                                                                                             |
| ----------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `additionalResolutions` | `number[]` <br/> Min: 0                  | Create additional resolutions for high res displays (e.g. Apples Retina Display which has a resolution of 2x). Default is `[]`.                                                                                                                         |
| `exclude`               | [`SourceFormat`](#type-sourceformat)`[]` | Exclude image formats from being optimized. Default is `[]`.                                                                                                                                                                                            |
| `formats`               | [`OutputFormat`](#type-outputformat)`[]` | Specifiy the formats images should be transformed to. Specifiying `original` means that the original format is kept. Default is `["original", "webp", "avif"]`. Only `jpeg`, `jpg`, `png`/`webp`, `avif`, `heif`, `tiff` and `tif` will adjust quality. |
| `include`               | [`SourceFormat`](#type-sourceformat)`[]` | Include image formats that should be optimized. Default is `["jpeg", "jpg", "png"]`.                                                                                                                                                                    |
| `sizes`<sup>\*</sup>    | [`ImageSize`](#object-imagesize)`[]`     | (required) - Specify the sizes to which the uploaded image should be transformed.                                                                                                                                                                       |
| `quality`               | `number` <br/> Min: 0 <br/> Max: 100     | Specific the image quality the output should be rendered in. Default is `80`.                                                                                                                                                                           |

### Object `ImageSize`

| Option               | Type                                   | Description                                                                                                                                                                                                               |
| -------------------- | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fit`                | [`ImageFit`](#type-imagefit)           | The image fit mode if both width and height are specified. Default is `cover`.                                                                                                                                            |
| `height`             | `number` <br/> Min: 0                  | The height of the output image in pixels. If only height is specified then the width is calculated with the original aspect ratio. If neither width nor height are set, the output will be the same size as the original. |
| `name`<sup>\*</sup>  | `string` <br/> Min: 0                  | (required) - The name of the size. This will be used as part of generated image's name and url.                                                                                                                           |
| `position`           | [`ImagePosition`](#type-imageposition) | The position of the image within the output image. This option is only used when fit is cover or contain. Default is `center`.                                                                                            |
| `width`              | `number` <br/> Min: 0                  | The width of the output image in pixels. If only width is specified then the height is calculated with the original aspect ratio. If neither width nor height are set, the output will be the same size as the original.  |
| `withoutEnlargement` | `boolean`                              | When true, the image will not be enlarged if the input image is already smaller than the required dimensions. Default is `false`.                                                                                         |

### Type `SourceFormat`

```typescript
type SourceFormat =
  | "avif"
  | "dz"
  | "fits"
  | "gif"
  | "heif"
  | "input"
  | "jpeg"
  | "jpg"
  | "jp2"
  | "jxl"
  | "magick"
  | "openslide"
  | "pdf"
  | "png"
  | "ppm"
  | "raw"
  | "svg"
  | "tiff"
  | "tif"
  | "v"
  | "webp";
```

### Type `OutputFormat`

```typescript
type OutputFormat = "original" | SourceFormat;
```

### Type `ImageFit`

```typescript
type ImageFit = "contain" | "cover" | "fill" | "inside" | "outside";
```

### Type `ImagePosition`

```typescript
type ImageFit =
  | "top"
  | "right top"
  | "right"
  | "right bottom"
  | "bottom"
  | "left bottom"
  | "left"
  | "left top"
  | "center"
  | "entropy" // only in combination with ImageFit cover
  | "attention"; // only in combination with ImageFit cover;
```

### Example config

The following config would be a good starting point for your project.

```typescript
// ./config/plugins.ts

export default ({ env }) => ({
  // ...
  "image-optimizer": {
    enabled: true,
    config: {
      include: ["jpeg", "jpg", "png"],
      exclude: ["gif"],
      formats: ["original", "webp", "avif"],
      sizes: [
        {
          name: "xs",
          width: 300,
        },
        {
          name: "sm",
          width: 768,
        },
        {
          name: "md",
          width: 1280,
        },
        {
          name: "lg",
          width: 1920,
        },
        {
          name: "xl",
          width: 2840,
          // Won't create an image larger than the original size
          withoutEnlargement: true,
        },
        {
          // Uses original size but still transforms for formats
          name: "original",
        },
      ],
      additionalResolutions: [1.5, 3],
      quality: 70,
    },
  },
  // ...
});
```

If you want type safety, you can extend the configuration with our config typing.

With that approach, you will get the possibility for property IntelliSense and static string type values.

```typescript
import { Config as ImageOptimizerConfig } from "strapi-plugin-image-optimizer/dist/server/models/config";

// ...
export default ({ env }) => ({
  // ...
  "image-optimizer": {
    // ...
    config: {
      // ...
    } satisfies ImageOptimizerConfig,
  },
});
```

## Usage

When uploading an image in the media library, Image Optimizer resizes and converts the uploaded images as specified in the config.

## Found a bug?

If you found a bug or have any questions please [submit an issue](https://github.com/marlokessler/strapi-plugin-image-optimizer/issues). If you think you found a way how to fix it, please feel free to create a pull request!

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/marlokessler"><img src="https://avatars.githubusercontent.com/u/48910761?v=4?s=100" width="100px;" alt="Marlo Kesser"/><br /><sub><b>Marlo Kesser</b></sub></a><br /><a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=marlokessler" title="Code">💻</a> <a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=marlokessler" title="Documentation">📖</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://www.linkedin.com/in/yaroslav-zakhidnyi/"><img src="https://avatars.githubusercontent.com/u/32482428?v=4?s=100" width="100px;" alt="Yaroslav Zakhidnyi"/><br /><sub><b>Yaroslav Zakhidnyi</b></sub></a><br /><a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/issues?q=author%3Ayarikwest" title="Bug reports">🐛</a> <a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=yarikwest" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/JosefBredereck"><img src="https://avatars.githubusercontent.com/u/13408112?v=4?s=100" width="100px;" alt="Josef Bredreck"/><br /><sub><b>Josef Bredreck</b></sub></a><br /><a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=JosefBredereck" title="Code">💻</a> <a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=JosefBredereck" title="Documentation">📖</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://cretezy.com"><img src="https://avatars.githubusercontent.com/u/2672503?v=4?s=100" width="100px;" alt="Cretezy"/><br /><sub><b>Cretezy</b></sub></a><br /><a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=Cretezy" title="Code">💻</a> <a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=Cretezy" title="Documentation">📖</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/Lucurious"><img src="https://avatars.githubusercontent.com/u/6559860?v=4?s=100" width="100px;" alt="Lucurious"/><br /><sub><b>Lucurious</b></sub></a><br /><a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/issues?q=author%3ALucurious" title="Bug reports">🐛</a> <a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=Lucurious" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://tyrola.dev"><img src="https://avatars.githubusercontent.com/u/779751?v=4?s=100" width="100px;" alt="Alexander Birkner"/><br /><sub><b>Alexander Birkner</b></sub></a><br /><a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/issues?q=author%3ABirknerAlex" title="Bug reports">🐛</a> <a href="https://github.com/marlokessler/strapi-plugin-image-optimizer/commits?author=BirknerAlex" title="Code">💻</a></td>
    </tr>
  </tbody>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!

A special thanks to [@nicolashmln](https://github.com/nicolashmln), whose package [strapi-plugin-responsive-image](https://github.com/nicolashmln/strapi-plugin-responsive-image) served as inspiration for this one.
