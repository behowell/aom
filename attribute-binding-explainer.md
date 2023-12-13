
## Appendix B: Declarative syntax for referring OUT of the Shadow DOM

### Motivation

Currently, the ARIAMixin attributes like `ariaLabelledByElements` aren't compatible with [Declarative Shadow DOM](https://developer.chrome.com/docs/css-ui/declarative-shadow-dom). 

This presents the following drawbacks:

* Requires a nontrivial amount of code to hook up custom attributes to an element inside the shadow tree. 
   * This increases the chances for bugs or sub-optimal behavior in developer code, and reduces the likelihood that it will be implemented in the first place.
   * In particular: it is difficult to implement in a way that works with nested shadow DOM. [In short: the component needs some way to know what level of nested shadow DOMs to do the `getElementById` lookup.]
* No support for [server-side rendered](https://web.dev/articles/rendering-on-the-web) (SSR) content. Labels will not be available to screen readers until the JavaScript is loaded and executed to hydrate the elements.
* Requires the component to do work to look up and connect `ariaLabelledByElements`, etc. even if no screen reader, etc. is present. [This is a hypothetical perf concern, and may not materialize in practice.]

### Proposal

_This is the beginnings of proposal, and needs more work. It's based on the ideas in the [ExportID proposal](https://github.com/WICG/aom/blob/gh-pages/exportid-explainer.md#could-the-syntax-for-useids-be-improved)._

This adds the following:
* New syntax used in IDREF attributes, which references an attribute on the host element: `":host[attribute-name]"`
* New attribute on the shadow host `shadowrootboundattributes="... ..."`, to opt-in specific attributes for this behavior.

The following example hooks up custom attributes `labelledby` and `listbox` to attributes on the `<input>` inside the custom component.

```html
<combobox-input labelledby="combo-label" listbox="combo-listbox">
  <template shadowrootmode="open"
            shadowrootboundattributes="labelledby listbox">
    <input role="combobox"
           aria-labelledby=":host[labelledby]"
           aria-controls=":host[listbox]"
           aria-activedescendant=":host[listbox]"
    />
</combobox-input>
```

[... more details in progress ...]