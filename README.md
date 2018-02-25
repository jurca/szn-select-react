# szn-select-react

[![npm](http://img.shields.io/npm/v/@jurca/szn-select-react.svg)](https://www.npmjs.com/package/@jurca/szn-select-react)
[![License](https://img.shields.io/npm/l/@jurca/szn-select-react.svg)](https://github.com/jurca/szn-select-react/blob/master/LICENSE)

Accessible HTML `<select>` element replacement with customizable UI. This
package provides integration of the
[szn-select](https://github.com/jurca/szn-select) project for React projects.

## Usage

First install the package:

```
npm install --save @jurca/szn-select-react
``` 

The `<szn-select>` (as the `SznSelect` component) element can be then used the
same way as you would use a `<select>` element in your project. The following
example shows various usage options:

```jsx harmony
import React from 'react'
import SznSelect from '@jurca/szn-select-react'

export default props =>
  <form action='/submit-form' method='post'>
    <div className='inline-form'>
      <label htmlFor='mySelect'>
        Choose one
      </label>
      {/* use the <SznSelect> component as if if was an ordinary <select> element */}
      <SznSelect name='singleOption' id='mySelect' onChange={props.onFirstChanged}>
        <option value='1'>first</option>
        <option value='2' selected>second</option>
        <optgroup label='this is a group'>
          <option value='3'>option groups are supported as well</option>
        </optgroup>
      </SznSelect>
    </div>
    <div className='inline-form'>
      <label htmlFor='anotherSelect'>
        Choose any
      </label>
      <SznSelect name='manyOptions' id='anotherSelect' multiple>
        <option value='foo'>foo</option>
        <option value='foo' title='multiple options may have the same value'>foo 2</option>
        <option value='bar' selected>bar</option>
        <option value='baz' disabled>baz</option>
      </SznSelect>
    </div>
  </form>
```

### Fallback CSS for legacy browsers and no-js scenarios

In order to improve the UX in case of extremely-slowly-loaded JS, legacy
browsers or situations where JS is disabled or fails, it is recommended to
include the following CSS file in your page to provide fallback styles for
the native `<select>` element that would be where the `<SznSelect>` component
is used:

```html
<link rel="stylesheet" href="https://unpkg.com/@jurca/szn-select@<VERSION>/szn-select-nojs.css">
```

## Configuring the loader

The component, by default, injects the `szn-select`'s
[loader script](https://www.npmjs.com/package/@jurca/szn-select#usage-on-static-or-server-rendered-websites)
into the `<head>` to load the latest compatible version of szn-select using
the latest compatible loader. This technique is used automatically provide
bugfixes and compatibility updates (compatibility with the assistive
technologies does shift in time, unfortunately).

Another thing the loader does for you is selecting the smallest,
best-performing bundle for the current browser. This means that the modern
browsers (e.g. Chrome) tend to download smaller bundles than the legacy
browsers (e.g. Internet Explorer).

The downside of this is that the browser has to download two more JS files
from the unpkg.com's CDN (by default, configurable) to enable all features of
the `<SznSelect>` component.

This behavior may be, if needed, modified using the loader options passed as
an object to the component in the `loaderOptions` prop:

```jsx harmony
<SznSelect loaderOptions={{ /* loader options go here, see below for details */ }}>
  select's option elements go here
</SznSelect>
```

Note that only the first `<SznSelect>` instance activated by React will
perform the loading, therefore passing different options to multiple
`SznSelect`s on a single page might result in inconsistent or unexpected
results. To avoid this, a common pattern used is using a higher-order
component, as seen below:

```jsx harmony
import React from 'react'
import SznSelect from '@jurca/szn-select-react'

const LOADER_OPTIONS = {
  // loaders options go here, se below fo details
}

export default props =>
  // Make sure you specify the loaderOptions props as last so that loader
  // options that were passed-in accidentally will be overridden by your
  // intended configuration
  <SznSelect {...props} loaderOptions={LOADER_OPTIONS}>
    {props.children}
  </SznSelect>
```

The loader options are:
* `enable` - whether to use a loader at all. This defaults to `true`, setting
  this option to `false` is useful when using a self-hosted deployment (see
  below). Use this option with care as it usually leads to getting no updates
  and bugfixes until you update the dependencies in your project manually.
* `useEmbeddedLoader` - When set to `true`, the component will use a more
  light-weight loader to load the `szn-element`'s bundle. This saves a single
  (tiny) JavaScript file download. This usually leads to the same results as
  using a full-feature loader, however, if there are bugfixes available for
  the loader, you will might not receive them in certain situations. In most
  cases it is OK to set this option to `true`. Defaults to `false`.
* `useAsyncLoading` - when set to `true`, the `async` attribute will be set on
  the `<script>` elements used to load the `szn-select`'s JavaScript logic.
  Since the loader is always triggered **after** the `DOMContentLoaded` event,
  the scripts will be loaded asynchronously anyway regardless of this flag.
  This option is simply exposed from the underlying loader mechanics. Defaults
  to `true`.
* `urls` - an object that provides URL overrides for the loader:
  * `package` - the URL at which the `@jurca/szn-select` package's files are
    available. This can be overridden on per-file basis using the options
    below. Defaults to `https://unpkg.com/@jurca/szn-select@<VERSION>/`, the
    trailing slash is optional.
  * `loader` - the URL override for the full-featured loader. This option has
    no effect when the `useEmbeddedLoader` is set to `true`.
  * `es3` - the URL override for the ES3-compatible implementation of the
    `szn-select` element. This is used on legacy browsers if the
    implementation for the `szn-tethered` helper element and the szn-elements
    runtime is already loaded.
  * `es2016` - the URL override for the ES2016-compatible implementation of
    the `szn-select` element. This is used on modern browsers if the
    implementation for the `szn-tethered` helper element and the szn-elements
    runtime is already loaded.
  * `bundle-elements.es3` - the URL override for the ES3-compatible
    implementation of the `szn-select` and `szn-tethered` elements. This is
    used on legacy browsers if the szn-elements runtime is already loaded.
  * `bundle-elements.es2016` - the URL override for the ES2016-compatible
    implementation of the `szn-select` and `szn-tethered` elements. This is
    used on modern browsers if the szn-elements runtime is already loaded.
  * `bundle-full.es3` - the URL override the ES3-compatible implementation of
    the `szn-select` and `szn-tethered` elements and the szn-elements runtime.
    This is used on legacy browsers if no dependencies are loaded yet.
  * `bundle-full.es2016` - the URL override the ES2016-compatible
    implementation of the `szn-select` and `szn-tethered` elements and the
    szn-elements runtime. This is used on modern browsers if no dependencies
    are loaded yet.
  * `bundle-full.ce` - the URL override the ES2016-compatible implementation
    of the `szn-select` and `szn-tethered` elements and the szn-elements
    runtime. This is used on modern browsers that support the
    [custom elements](https://mdn.io/Window/customElements) API if no
    dependencies are loaded yet.

TODO: sync loading - see self-hosted deployment

## Self-hosted deployment

TODO
