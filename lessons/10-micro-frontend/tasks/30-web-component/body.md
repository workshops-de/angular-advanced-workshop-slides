Your solution works as long as host and remote run the **same Angular version**, because both share one Angular instance. Wrap the rating with **Angular Elements** and it becomes a plain Web Component. The remote brings its own Angular, and the host only needs to know a tag name.

---

- **Create the Web Component** In `apps/rating/src/app/rating/rating-element.ts`:
  - Create an application with `createApplication(){:ts}` from `@angular/platform-browser`.
  - Turn `Rating{:ts}` into a custom element with `createCustomElement(){:ts}` from `@angular/elements` and register it as `rating-stars-element` with `customElements.define(){:ts}`.
  - Do not register the tag twice. The module may be evaluated more than once.

---

- **Expose the element** In `apps/rating/federation.config.mjs` expose `./RatingElement` instead of `./Rating`. The host now needs nothing else from the remote.

---

- **Stop sharing Angular** Add `@angular/*` and `rxjs` to the `skip{:ts}` list of the **remote**. Skipped packages are not shared, the remote bundles its own copy. Restart the remote and look at its `remoteEntry.json`: which packages are still shared?

---

- **Use the tag in the host** Adjust `RemoteRating{:ts}`:
  - Load `./RatingElement`. You do not need the result, loading the module registers the tag.
  - Render `<rating-stars-element>{:html}` as soon as the resource has a value and bind it with `[value]{:html}` and `(valueChange){:html}`. Angular does not know the tag, so allow it with `CUSTOM_ELEMENTS_SCHEMA{:ts}`.
  - The `value` model of the component emits a `CustomEvent{:ts}`. The new value is in `detail{:ts}`.
  - Remove the `ViewContainerRef{:ts}` code, you do not need it anymore. Keep the hint for an unreachable remote.

---

- **Test and check** Update the spec of `RemoteRating{:ts}`: the tag is rendered when the module loads, the value is taken over from a `valueChange` event, and the hint is shown when loading fails.

Start remote and host again. Open the network tab on a book detail page and compare: which Angular chunks does the remote load, and which does the host load? Why does the host not care about the Angular version of the remote anymore?
