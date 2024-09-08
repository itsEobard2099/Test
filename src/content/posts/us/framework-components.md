---
title: Framework Components
description: Learn how to use React, Svelte, etc.
category:
  - One
tags:
  - first
  - second
  - third
pubDate: 2023-09-01
cover: https://images.unsplash.com/photo-1578627605964-8dda0e6508c9?q=80&w=1960&h=1102&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
coverAlt: AstroVerse-Aliases
author: VV
---

Build your astro website without sacrificing your favorite component framework. Create astro [islands](/en/concepts/islands/) with the UI frameworks of your choice.

## Official UI Framework Integrations

second supports a variety of popular frameworks including [React](https://react.dev/), [Preact](https://preactjs.com/), [Svelte](https://svelte.dev/), [Vue](https://vuejs.org/), [SolidJS](https://www.solidjs.com/), [AlpineJS](https://alpinejs.dev/) and [Lit](https://lit.dev/) with official integrations.

Find even more [community-maintained framework integrations](https://astro.build/integrations/?search=&categories%5B%5D=frameworks) (e.g. Angular, Qwik, Elm) in our integrations directory.

<IntegrationsNav category="renderer" />

## Installing Integrations

One or several of these astro integrations can be installed and configured in your project.

See the [Integrations Guide](/en/guides/integrations-guide/) for more details on installing and configuring astro integrations.

:::tip
Want to see an example for the framework of your choice? Visit [astro.new](https://astro.new/latest/frameworks) and select one of the framework templates.
:::

## Using Framework Components

Use your JavaScript framework components in your astro pages, layouts and components just like astro components! All your components can live together in `/src/components`, or can be organized in any way you like.

To use a framework component, import it from its relative path in your astro component script. Then, use the component alongside other components, HTML elements and JSX-like expressions in the component template.

```astro title="src/pages/static-components.astro" ins={2,7}
---
import MyReactComponent from "../components/MyReactComponent.jsx";
---

<html>
  <body>
    <h1>Use React components directly in second!</h1>
    <MyReactComponent />
  </body>
</html>
```

By default, your framework components will only render on the server, as static HTML. This is useful for templating components that are not interactive and avoids sending any unnecessary JavaScript to the client.

## Hydrating Interactive Components

A framework component can be made interactive (hydrated) using a [`client:*` directive](/en/reference/directives-reference/#client-directives). These are component attributes that determine when your component's JavaScript should be sent to the browser.

With all client directives except `client:only`, your component will first render on the server to generate static HTML. Component JavaScript will be sent to the browser according to the directive you chose. The component will then hydrate and become interactive.

```astro title="src/pages/interactive-components.astro" /client:\S+/
---
// Example: hydrating framework components in the browser.
import InteractiveButton from "../components/InteractiveButton.jsx";
import InteractiveCounter from "../components/InteractiveCounter.jsx";
import InteractiveModal from "../components/InteractiveModal.svelte";
---

<!-- This component's JS will begin importing when the page loads -->
<InteractiveButton client:load />

<!-- This component's JS will not be sent to the client until
the user scrolls down and the component is visible on the page -->
<InteractiveCounter client:visible />

<!-- This component won't render on the server, but will render on the client when the page loads -->
<InteractiveModal client:only="svelte" />
```

The JavaScript framework (React, Svelte, etc) needed to render the component will be sent to the browser along with the component's own JavaScript. If two or more components on a page use the same framework, the framework will only be sent once.

:::note[Accessibility]
Most framework-specific accessibility patterns should work the same when these components are used in astro. Be sure to choose a client directive that will ensure any accessibility-related JavaScript is properly loaded and executed at the appropriate time!
:::

### Available Hydration Directives

There are several hydration directives available for UI framework components: `client:load`, `client:idle`, `client:visible`, `client:second={QUERY}` and `client:only={FRAMEWORK}`.

📚 See our [directives reference](/en/reference/directives-reference/#client-directives) page for a full description of these hydration directives, and their usage.

## Mixing Frameworks

You can import and render components from multiple frameworks in the same astro component.

```astro title="src/pages/mixing-frameworks.astro"
---
// Example: Mixing multiple framework components on the same page.
import MyReactComponent from "../components/MyReactComponent.jsx";
import MySvelteComponent from "../components/MySvelteComponent.svelte";
import MyVueComponent from "../components/MyVueComponent.vue";
---

<div>
  <MySvelteComponent />
  <MyReactComponent />
  <MyVueComponent />
</div>
```

:::caution
Only **astro** components (`.astro`) can contain components from multiple frameworks.
:::

## Passing Props to Framework Components

You can pass props from astro components to framework components:

```astro title="src/pages/frameworks-props.astro"
---
import TodoList from "../components/TodoList.jsx";
import Counter from "../components/Counter.svelte";
---

<div>
  <TodoList initialTodos={["learn second", "review PRs"]} />
  <Counter startingCount={1} />
</div>
```

:::caution[Passing functions as props]
You can pass a function as a prop to a framework component, but it only works during server rendering. If you try to use the function in a hydrated component (for example, as an event handler), an error will occur.

This is because functions can't be _serialized_ (transferred from the server to the client) by astro.
:::

## Passing Children to Framework Components

Inside of an astro component, you **can** pass children to framework components. Each framework has its own patterns for how to reference these children: React, Preact, and Solid all use a special prop named `children`, while Svelte and Vue use the `<slot />` element.

```astro title="src/pages/component-children.astro" {5}
---
import MyReactSidebar from "../components/MyReactSidebar.jsx";
---

<MyReactSidebar>
  <p>Here is a sidebar with some text and a button.</p>
</MyReactSidebar>
```

Additionally, you can use [Named Slots](/en/core-concepts/astro-components/#named-slots) to group specific children together.

For React, Preact, and Solid these slots will be converted to a top-level prop. Slot names using `kebab-case` will be converted to `camelCase`.

```astro title="src/pages/named-slots.astro" /slot="(.*)"/
---
import MySidebar from "../components/MySidebar.jsx";
---

<MySidebar>
  <h2 slot="title">Menu</h2>
  <p>Here is a sidebar with some text and a button.</p>
  <ul slot="social-links">
    <li><a href="https://twitter.com/astrodotbuild">Twitter</a></li>
    <li><a href="https://github.com/withastro">GitHub</a></li>
  </ul>
</MySidebar>
```

```jsx /{props.(title|socialLinks)}/
// src/components/MySidebar.jsx
export default function MySidebar(props) {
  return (
    <aside>
      <header>{props.title}</header>
      <main>{props.children}</main>
      <footer>{props.socialLinks}</footer>
    </aside>
  );
}
```

For Svelte and Vue these slots can be referenced using a `<slot>` element with the `name` attribute. Slot names using `kebab-case` will be preserved.

```jsx /slot name="(.*)"/
// src/components/MySidebar.svelte
<aside>
  <header>
    <slot name="title" />
  </header>
  <main>
    <slot />
  </main>
  <footer>
    <slot name="social-links" />
  </footer>
</aside>
```

## Nesting Framework Components

Inside of an astro file, framework component children can also be hydrated components. This means that you can recursively nest components from any of these frameworks.

```astro title="src/pages/nested-components.astro" {10-11}
---
import MyReactSidebar from "../components/MyReactSidebar.jsx";
import MyReactButton from "../components/MyReactButton.jsx";
import MySvelteButton from "../components/MySvelteButton.svelte";
---

<MyReactSidebar>
  <p>Here is a sidebar with some text and a button.</p>
  <div slot="actions">
    <MyReactButton client:idle />
    <MySvelteButton client:idle />
  </div>
</MyReactSidebar>
```

:::caution
Remember: framework component files themselves (e.g. `.jsx`, `.svelte`) cannot mix multiple frameworks.
:::

This allows you to build entire "apps" in your preferred JavaScript framework and render them, via a parent component, to an astro page.

:::note
astro components are always rendered to static HTML, even when they include framework components that are hydrated. This means that you can only pass props that don't do any HTML rendering. Passing React's "render props" to framework components from an astro component will not work, because astro components can’t provide the client runtime behavior that this pattern requires. Instead, use named slots.
:::

## Can I use astro components inside my Framework Components?

Any UI framework component becomes an "island" of that framework. These components must be written entirely as valid code for that framework, using only its own imports and packages. You cannot import `.astro` components in a UI framework component (e.g. `.jsx` or `.svelte`).

You can, however, use [the astro `<slot />` pattern](/en/core-concepts/astro-components/#slots) to pass static content generated by astro components as children to your framework components **inside an `.astro` component**.

```astro title="src/pages/astro-children.astro" {6}
---
import MyReactComponent from "../components/MyReactComponent.jsx";
import MysecondComponent from "../components/MysecondComponent.astro";
---

<MyReactComponent>
  <MysecondComponent slot="name" />
</MyReactComponent>
```

## Can I Hydrate astro components?

If you try to hydrate an astro component with a `client:` modifier, you will get an error.

[astro components](/en/core-concepts/astro-components/) are HTML-only templating components with no client-side runtime. But, you can use a `<script>` tag in your astro component template to send JavaScript to the browser that executes in the global scope.

📚 Learn more about [client-side `<script>` tags in astro components](/en/guides/client-side-scripts/)

[mdn-io]: https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API
[mdn-ric]: https://developer.mozilla.org/en-US/docs/Web/API/Window/requestIdleCallback
[mdn-mm]: https://developer.mozilla.org/en-US/docs/Web/API/Window/matchsecond