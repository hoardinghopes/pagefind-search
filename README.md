# pagefind-search

This cloned version simply adds `/*webpackIgnore: true*/` to prevent the build breaking.

Web Component to easily add the [Pagefind search UI](https://pagefind.app/) to your web site. This unlocks more control of asset loading via [`<is-land>`](https://www.11ty.dev/docs/plugins/partial-hydration/).

## Usage

```html
<script type="module" src="pagefind-search.js"></script>

<!-- No fallback content (works, but not ideal) -->
<pagefind-search></pagefind-search>

<!-- Better: fallback to DuckDuckGo site search (use any search engine here) -->
<pagefind-search>
	<form action="https://duckduckgo.com/" method="get" style="min-height: 3.2em;"><!-- min-height to reduce CLS -->
		<label>
			Search for:
			<input type="search" name="q" autocomplete="off" autofocus>
		</label>
		<!-- Put your searchable domain here -->
		<input type="hidden" name="sites" value="www.zachleat.com">
		<button type="submit">Search</button>
	</form>
</pagefind-search>
```

### Extend Pagefind Options

Any attribute that starts with an underscore will be passed to the Pagefind constructor as an option. Use underscore case for option names (as HTML attributes are case insensitive). This is only compatible with boolean/number/string options (but read on for a solution for the others below). Full [options list on the Pagefind documentation](https://pagefind.app/docs/ui/).

* `_page_size` for `pageSize`
* `_show_sub_results` for `showSubResults`
* `_show_images` for `showImages`
* `_excerpt_length` for `excerptLength`
* (not attribute-friendly, read below) `processTerm`
* (not attribute-friendly, read below) `processResult`
* `_show_empty_filters` for `showEmptyFilters`
* `_reset_styles` for `resetStyles`
* `_bundle_path` for `bundlePath`
* `_debounce_timeout_ms` for `debounceTimeoutMs`
* (not attribute-friendly, read below) `translations`

```html
<pagefind-search _show_images="false">
	<!-- Don’t forget your fallback content! -->
</pagefind-search>
```

#### Advanced: Full JavaScript access

Use the `manual` attribute to manually initialize your Pagefind UI with any custom options (including functions, objects, etc).

```html
<pagefind-search manual id="my-search">
	<!-- Don’t forget your fallback content! -->
</pagefind-search>

<script type="module">
let el = document.querySelector("#my-search");
await el.pagefind({
	showImages: false,
	debounceTimeoutMs: 100,
	processTerm: function (term) {
		return term.replace(/aa/g, 'ā');
	}
});

// Use `el.pagefindUI` to access the PagefindUI instance
</script>
```

### Loading via Islands

Use [`<is-land>` to enable more control over the component’s downstream asset loading](https://www.11ty.dev/docs/plugins/partial-hydration/).

```html
<script type="module" src="pagefind-search.js"></script>

<!-- Use any of is-land’s on: conditions -->
<is-land on:idle on:visible on:media="(min-width: 40em)" on:save-data="false">
	<pagefind-search>
		<!-- Don’t forget your fallback content! -->
	</pagefind-search>
</is-land>
```

If search is a secondary feature on a page, you _could_ lazy load the component definition too, though that would chain two separate JavaScript file loads together which is probably not what you want:

```html
<!-- Use any of is-land’s on: conditions -->
<is-land on:idle on:visible on:media="(min-width: 40em)" on:save-data="false">
	<pagefind-search>
		<!-- Don’t forget your fallback content! -->
	</pagefind-search>
	<template data-island>
		<script type="module" src="pagefind-search.js"></script>
	</template>
</is-land>
```

#### Eager CSS Loading

You can optionally load the Pagefind CSS yourself! This is useful if Search is a primary use case for the page and loads the 3 kB (compressed) Stylesheet up front but will still allow lazy loading the 20 kB (compressed) JavaScript file via `<is-land>`.

```html
<link rel="stylesheet" href="/pagefind/pagefind-ui.css">
<script type="module" src="pagefind-search.js"></script>
```

### Full Attribute List

* `pagefind-autofocus` to focus on the form element after pagefind has initialized.
* `manual` for manual initialization.
