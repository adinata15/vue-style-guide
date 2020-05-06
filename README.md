# Vue.js code style & standards


## What this guide is and is not

This guide assumes that you have an understanding of Vue.js concepts and building applications in Vue.js. This guide aims to provide a clearly defined set of rules and best practices for writing code in Vue applications.

This guide is _not_ a tutorial on Vue concepts, but functions to provide developers with an intentionally limited set of ways to utilize many Vue concepts to achieve a standard throughout our codebase.

[[toc]]


---
## Component naming standards

#### Components _must_ be multi-word
- All component names must always be multi-word (except for App.vue).

#### Component name property
- All components _must_ have a name property. That name must be the same name as the component file name. 

```js
👎 BAD 😱 
// AppDrawer.vue
export default {
  props: {
    //...
  },
  computed: {
    //...
  }
}
```
```js
👍 GOOD 😊
// AppDrawer.vue
export default {
  name: "AppDrawer",
  props: {
    //...
  },
  computed: {
    //...
  }
}
```

#### Single-instance component names
- Names of components that should only have a single active instance used throughout the application should begin with the `App` prefix. 

```js
👎 BAD 😱 
// Drawer.vue
export default {
  name: "Drawer"
}

// TheDrawer.vue
export default {
  name: "TheDrawer"
}
```
```js
👍 GOOD 😊
// AppDrawer.vue
export default {
  name: "AppDrawer",
}
```

#### Base component names

- Components that are reused throughout the application and have no coupled relationship to a particular component, container or view should be prepended with `Base`. These will generally be functional components with no state of their own and only function as UI components.

- Their names often include the name of an element they wrap (e.g. BaseButton, BaseColumn), unless no element exists for their specific purpose (e.g. BaseIcon). If you build similar components for a more specific context, they will almost always consume these components (e.g. BaseButton may be used in ButtonSubmit).

> 💡 _Some advantages of this convention:_
>- When organized alphabetically in editors, your app’s base components are all listed together, making them easier to identify.
>- Since component names should always be multi-word, this convention prevents you from having to choose an arbitrary prefix for simple component wrappers (e.g. MyButton, VueButton).


#### Tightly coupled component names

- Child components that are tightly coupled with their parent should include the parent component name as a prefix.

- If a component only makes sense in the context of a single parent component, that relationship should be evident in its name.

- This naming convention should continue down the chain of parent/child relationships between components. For example if I have a `AppDrawer` component, which has a component which contains a list of navigation links called `AppDrawerNav`, then its child link component would be named `AppDrawerNavLinks`.


#### Order of words in component names

- Component names should start with the highest-level (often most general) words and end with descriptive modifying words. For example...

```
components/
  app/
    AppDrawer.vue
    AppDrawerFooter.vue
    AppDrawerHeader.vue
    AppDrawerHeaderLogo.vue
    AppDrawerNav.vue
    AppDrawerNavLink.vue
```

>The one exception to this rule is for view components AKA pages. These components should be appended with the word `Page`.

- Do not place the modifier words at the beginning of the component name. Although it may read more like plain english this way, it will cause your components to be unorganized in the code editor, as well as increase the possibility of naming conflicts.

```
👎 BAD 😱 
components/ 
  ClearSearchButton.vue
  LaunchOnStartupCheckbox.vue
  RunSearchButton.vue
  SearchInput.vue
  TermsCheckbox.vue
```
```
👍 GOOD 😊
components/
  SearchButtonClear.vue
  SearchButtonRun.vue
  SearchInputQuery.vue
  SettingsCheckboxTerms.vue
  SettingsCheckboxLaunchOnStartup.vue
```


#### Component name casing

- All naming of and references to components should be PascalCase thoughout the application. Referencing components in the template and JavaScript should be PascalCase. Naming of component files should be PascalCase.

> 💡 _PascalCase has a few advantages over kebab-case:_
>- Editors can autocomplete component names in templates, because PascalCase is also used in JavaScript.
>- `<MyComponent>` is more visually distinct from a single-word HTML element than `<my-component>`, because there are two character differences (the two capitals), rather than just one (a hyphen).
>- If you use any non-Vue custom elements in your templates, such as a web component, PascalCase ensures that your Vue components remain distinctly visible.
- _If_ we ever have the need to use DOM templates, you have to use kebab-casing, but this is unlikely. 


---
## Layout components
<!-- IDEA: What is a layout component, how does it differ from templates, views, containers, etc? -->
#### Shared Layout components

- Favor creating layout components that can wrap components to provide shared components or create a layout of multiple components passed as children.

- These components should either use slots (single file component) or children (JS style components).

- In the example below, the `AppLayout` component contains the header and footer which will render around the page displayed in `<router-view>`.

```html
<!-- App.vue -->
<template>
  <div id="app">
    <AppLayout>
      <router-view />
    </AppLayout>
  </div>
</template>

<!-- AppLayout.vue -->
<template>
  <div>
    <AppHeader>{{ title }}</AppHeader>
    <slot></slot>
    <AppFooter />
  </div>
</template>
```

---
## Templates

#### Always use directive shorthands

- Always use directive [shorthands](https://vuejs.org/v2/guide/syntax.html#Shorthands) in templates
  - `:` > `v-bind:`
  - `@` > `v-on:`
  - `#` > `v-slot`

> This keeps the code standardized and less prone to misunderstanding if using shorthand in some components but not in others.

#### No complex expressions in templates
- Any JavaScript expressions should be encapsulated in the methods or computed properties and called in the template. Templates should not contain any complex expressions, only function/method references. 
```html
👎 BAD 😱 
<!-- In template -->
<MyComponent :name="fullName.split(' ').map(word => word[0].toUpperCase() + word.slice(1)).join(' ')" />
```
```html
👍 GOOD 😊
<!-- In template -->
<MyComponent :name="normalizedFullName"/>
<!-- In the script -->
computed: {
  normalizedFullName() {
    return this.fullName.split(' ').map(word =>
      word[0].toUpperCase() + word.slice(1)
    ).join(' ')
  }
}
```
#### Computed style classes should come from the computed property
- Computed styles should be written as computed properties rather than writing the expression in the template.
// TODO: Add examples

#### Self-closing components
- Components with no child/slot content should be self-closing in single-file components. 
```html
👎 BAD 😱 
<!-- Without any slot content -->
<MyComponent></MyComponent>
```
```html
👍 GOOD 😊
<!-- Without any slot content -->
<MyComponent/>
<!-- With slot content -->
<MyComponent>Hello!</MyComponent>
```

>Components that self-close communicate that they not only have no content, but are meant to have no content. It’s the difference between a blank page in a book and one labeled “This page intentionally left blank.” Your code is also cleaner without the unnecessary closing tag.


---
## Props

Prop validations should be as specific as possible. See the Vue documentation for more on this. [Prop Validation documentation.](https://vuejs.org/v2/guide/components-props.html#Prop-Validation)

#### Types

- All props _must_ be typed. A type can be any JavaScript constructor. See [prop type documentation here](https://vuejs.org/v2/guide/components-props.html#Type-Checks). 

```js
props: {
  someProp: String
}
```

#### Required
- Ideally, props should contain the `required` option as well.
```js
props: {
  someProp: {
    type: String,
    required: false
  }
}
```
#### Default reference values (non primitives)
- If a prop has a default value that is not a primitive, write it as an anonymous function that returns the value
```js
props: {
  somePropObj: Object,
  required: true,
  default: () => ({})
}
```
OR
```js
props: {
  somePropArr: Array,
  required: true,
  default: () => []
}
```

>⚠️ Note that props are validated before a component instance is created, so instance properties (e.g. data, computed, etc) will not be available inside default or validator functions.
---
## Computed properties
#### Always return a value
- Computed properties should always `return`.

#### Never take arguments
- Computed properties should never take any arguments.

#### Keep them small, testable, and composable.
Simpler, well-named computed properties are:
- Easier to test
    When each computed property contains only a very simple expression, with very few dependencies, it’s much easier to write tests confirming that it works correctly.
- Easier to read
    Simplifying computed properties forces you to give each value a descriptive name, even if it’s not reused. This makes it much easier for other developers (and future you) to focus in on the code they care about and figure out what’s going on.
- More adaptable to changing requirements
    Any value that can be named might be useful to the view. For example, we might decide to display a message telling the user how much money they saved. We might also decide to calculate sales tax, but perhaps display it separately, rather than as part of the final price.
    Small, focused computed properties make fewer assumptions about how information will be used, so require less refactoring as requirements change.


---
## Non-Reactive State
> If the application has data that will never change, it is pointless to add the overhead to have it reactive. Data that exists in the data property before the component is created will always be reactive. Data in the computed property will be reactive as well. 

#### Initial non-reactive state in the lifecycle hooks
- 

#### Import static data from another file
- 

#### Initialize static data outside of the component instance
- 


---
## Managing State

#### State should live at the highest reasonable parent component
- State should be kept at the highest parent in the component tree that allows it to be shared among any child branches and passed down as props and mutated by listening to emitted events in its children. 
- Generally this should be the view components which will act as the primary "container" components. In some cases the highest parent may be App.vue or in a global state management solution like Vuex or the Apollo Client Cache.

#### Do not use `$root` or event buses
- Vuex or Apollo Client Cache should be preferred for global state management instead of `this.$root` or an event bus.


---
## Parent/Child Communication
Communication between parent and child components should only happen through props (parent to child) and emitting events (child to parent).
<!-- IDEA: This may be a good place for a "good" and "bad" example of each of these sub-points -->

#### Do not use `this.$parent` or event busses
- Although Vue.js supports nested components which have access to their parent context, accessing context outside your vue component goes against the idea of proper component based development. Therefore you should avoid using `this.$parent`.

#### Do not pass functions down as props
- Unlike React, in Vue you should not pass functions as props to change the state of the parent component from a child. 
- Instead the function should live in the parent component, and the child should emit an event to the parent to trigger the function. In the parent, an event listener should listen for an emitted event from the child.  


---
## Styles

#### Prefer `scoped` styles
For applications, styles in a top-level App component and in layout components may be global, but all other components' styles should always be scoped.
Consistent scoping will ensure that your styles only apply to the components they are meant for.

#### Avoid element selectors with `scoped` styles
- Prefer class selectors over element selectors in scoped styles, because large numbers of element selectors are slow.
>To scope styles, Vue adds a unique attribute to component elements, such as data-v-f3f3eg9. Then selectors are modified so that only matching elements with this attribute are selected (e.g. button[data-v-f3f3eg9]).<br/><br/> The problem is that large numbers of element-attribute selectors (e.g. button[data-v-f3f3eg9]) will be considerably slower than class-attribute selectors (e.g. .btn-close[data-v-f3f3eg9]), so class selectors should be preferred whenever possible.
---
## Folder structure
<!-- IDEA: Maybe add some additional context around this section, I may even move this to the top and order this document from most general to most specific -->
#### Source
```
src/
  apollo/
  assets/
  components/
  layout/
  utils/
  views/
  App.vue
  main.js
  router.js
```
#### Assets
```
assets/
  fonts/
  styles/
  images/
```
#### Components
```
components/
  app/
    AppHeader.vue
    AppDrawer.vue
    AppDrawerNavigation.vue
    ...
  base/
    form/
      BaseForm.vue
      BaseFormGroup.vue
      BaseFormInput.vue
      ...
    grid/
      BaseContainer.vue
      BaseColumn.vue
      BaseRow.vue
      ...
    input/
      BaseButton.vue
      ...
  containerOne/
    containerOneArea.vue
    containerOneAreaInput.vue
    ...
  containerTwo/
    containerTwoArea.vue
    containerTwoAreaSearch.vue
    ...
```

#### Each component folder should have an `index.js`
- To speed up component development and reduce the amount import statements in components, a folder containing multiple components should contain an `index.js` which exports all of the components in that folder or its subfolders as named exports.

```js
👎 BAD 😱
// Usage of the above components in a component without an index.js
import MatrixTag from "@/components/matrix/MatrixTag"
import MatrixNode from "@/components/matrix/MatrixNode"
import MatrixElement from "@/components/matrix/MatrixElement";

export default {
  name: "MatrixPage",
  components: {
    MatrixTag,
    MatrixNode,
    MatrixElement
  },
```
```js
👍 GOOD 😊
// Example of index.js from a component folder
import MatrixNode from "./MatrixNode";
import MatrixElement from "./MatrixElement";
import MatrixTag from "./MatrixTag";

export { MatrixElement, MatrixNode, MatrixTag };


// Usage of the above components in a component
import { MatrixTag, MatrixNode, MatrixElement } from "@/components/matrix";

export default {
  name: "MatrixPage",
  components: {
    MatrixTag,
    MatrixNode,
    MatrixElement
  },
```

>⚠️ Note that importing from a folder such as `"@/components/matrix"` will always import the `index.js` if it exists in the folder.  As such, the whole file path is not necessary (e.g. `"@/components/matrix/index"` )

#### Component folders may be broken up into subfolders
- In the case of the base components, since there will be so many, it can be okay to break them up into subfolders. This practice should be avoided in general, but can be helpful in certain situations. 

#### Each component folder or subfolder should have tests and stories folders
- Each folder directly composed of component files should contain a tests folder and a stories folder. Webpack is configured to look in any folder inside `/src` for tests (`.spec.js`) and stories (`.stories`). 

---
## Globally Registered Components

#### Base components should be globally registered
- To reduce the amount of redundant import statements of frequently used components throughout the application, base components should be globally registered. 
- See the Vue documentation for how this can be done using webpack. (https://vuejs.org/v2/guide/components-registration.html#Global-Registration)

#### Non-base components should not be globally registered
- Global registration often isn’t ideal. For example, if you’re using a build system like Webpack, globally registering all components means that even if you stop using a component, it could still be included in your final build. This unnecessarily increases the amount of JavaScript your users have to download.

#### Components from a 3rd party component library
- In the case that a component from a 3rd party component library is needed, you may need to globally register the single component. Do not install the whole library.


<!-- TODO: Add sections for the following...
- Configuring aliases in webpack
- 



-->
