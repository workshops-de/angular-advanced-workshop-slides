- **Configure the element from the host** The rating has more inputs than `value`. Let the host set `max` to `10` and switch the rating to `readonly` after the user clicked once. Which of the two is a property, which could be an attribute, and what does that mean for `[max]{:html}` against `max="10"{:html}`?

---

- **Isolate the styles** The stars bring their own CSS. Set `encapsulation: ViewEncapsulation.ShadowDom{:ts}` on the `Rating{:ts}` component and compare how a global `button { color: red; }` of the host affects the stars before and after.

---

- **Think about versions** The remote now ships its own Angular. What would you have to change to build the remote with a different Angular version than the host? What stays a shared contract between both teams (tag name, properties, events), and where would you document it?
