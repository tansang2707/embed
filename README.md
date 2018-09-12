![](https://badgen.net/badge/CodeX%20Editor/v2.0/blue)

# Embed Tool

Provides Block tool to embedding content for the [CodeX Editor](https://ifmo.su/editor).
Tool use CodeX Editor pasted patterns handling and inserts iframe with embedded content.

## Installation

### Install via NPM

Get the package

```shell
npm i --save-dev codex.editor.embed
```

Include module at your application

```javascript
const Embed = require('codex.editor.embed');
```

### Download to your project's source dir

1. Upload folder `dist` from repository
2. Add `dist/bundle.js` file to your page.

### Load from CDN
You can load specific version of package from [jsDelivr CDN](https://www.jsdelivr.com/package/npm/codex.editor.embed).

`https://cdn.jsdelivr.net/npm/codex.editor.embed@2.0.0`

Then require this script on page with CodeX Editor.

```html
<script src="..."></script>
```

## Usage

Add a new Tool to the `tools` property of the CodeX Editor initial config.

```javascript
var editor = CodexEditor({
  ...
  
  tools: {
    ...
    embed: Embed,
  },
  
  ...
});
```

## Available configuration

You can add some configuration to customize Embed Tool.

#### Services

Embed Tool support some services by default (see the full list [here](docs/services.md)). But you can provide your own services using simple configuration.

First of all you should create a Service configuration object. It contains following fields:

| Field      | Type       | Description |
| ---------- | ---------- | ----------- |
| `regex`    | `RegExp`   | Pattern of pasted URLs. You should use regexp groups to extract resource id
| `embedUrl` | `string`   | Url of resource\`s embed page. Use `<%= remote_id %>` to substitute resource identifier
| `html`     | `string`   | HTML code of iframe with embedded content. `embedUrl` will be set as iframe `src`
| `height`   | `number`   | _Optional_. Height of inserted iframe 
| `width`    | `number`   | _Optional_. Width of inserted iframe 
| `id`       | `Function` | _Optional_. If your id is complex you can provide function to make the id from extraced regexp groups

Example:

```javascript
{
  regex: /https?:\/\/codepen.io\/([^\/\?\&]*)\/pen\/([^\/\?\&]*)/,
  embedUrl: 'https://codepen.io/<%= remote_id %>?height=300&theme-id=0&default-tab=css,result&embed-version=2',
  html: "<iframe height='300' scrolling='no' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'></iframe>",
  height: 300,
  width: 600,
  id: (groups) => groups.join('/embed/')
}
```

When you create a Service configuration object, you can provide it with Tool\`s configuration:

```javascript
var editor = CodexEditor({
  ...
  
  tools: {
    ...
    embed: {
      class: Embed,
      services: {
        codepen: {
          regex: /https?:\/\/codepen.io\/([^\/\?\&]*)\/pen\/([^\/\?\&]*)/,
          embedUrl: 'https://codepen.io/<%= remote_id %>?height=300&theme-id=0&default-tab=css,result&embed-version=2',
          html: "<iframe height='300' scrolling='no' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'></iframe>",
          height: 300,
          width: 600,
          id: (groups) => groups.join('/embed/')
        }
      },
    },
  },
  
  ...
});
```

#### Black and white lists

If you want to use just few services or if you want to exclude some of them you can use black and white lists. 
Just provide them with Tool\`s configuration as array of services keys:

```javascript
var editor = CodexEditor({
  ...
  
  tools: {
    ...
    embed: {
      class: Embed,
      whitelist: ['youtube', 'coub', 'twitch', 'vimeo'],
      blacklist: ['coub']
    },
  },
  
  ...
});
```

#### Inline Toolbar
CodeX Editor provides useful inline toolbar. You can allow it\`s usage in the Embed Tool caption by providing `inlineToolbar: true`.

```javascript
var editor = CodexEditor({
  ...
  
  tools: {
    ...
    embed: {
      class: Embed,
      inlineToolbar: true
    },
  },
  
  ...
});
```

## Output data

| Field   | Type     | Description                                   
| ------- | -------- | ----------- 
| key     | `string` | service key                                 
| source  | `string` | source URL
| embed   | `string` | URL for source embed page
| width   | `number` | embedded content width
| height  | `number` | embedded content height
| caption | `string` | content caption


```json
{
  "type" : "embed",
  "data" : {
    "key" : "coub",
    "source" : "https://coub.com/view/1czcdf",
    "embed" : "https://coub.com/embed/1czcdf",
    "width" : 580,
    "height" : 320,
    "caption" : "My Life"
  }
}
```

