# UI Design

This documentation explains what the VISKAR project uses to design user interfaces.
Check [Reusable style components](ui-design.md#reusable-style-components) before creating any new styles.

## Technologies

### Tailwind CSS

*"Rapidly build modern websites without ever leaving your HTML."*

https://tailwindcss.com/

In January 2023, Tailwind CSS was chosen as the library to use for any new implementations.
If interrested in understanding the reasonning behind this decision, check out the document [The Future of styling in VisualKarsys](The%20Future%20of%20styling%20in%20VisualKarsys.pdf).

If you never used Tailwind CSS before, take the time to look at a tutorial. It's very easy to use, but has a very different approach to the old-school "writing CSS" approach.

We are stuck to using Tailwind version `=3.0.8` until we upgrade Angular/our build tools. The reason why is explained in details [on Trello](https://trello.com/c/H7BrdQWA/1866-add-tailwind-css).
Therefore, if the documentation mentions a feature that doesn't work for you, check the [CHANGELOG](https://github.com/tailwindlabs/tailwindcss/blob/master/CHANGELOG.md) to confirm if the feature was added before or after `3.0.8`

The Tailwind configuration can be found [here](../../src/client/tailwind.config.js)

Custom styles written with the tailwind utilities are all [here](../../src/client/src/styles.scss).

### Material Icons

We use material icons as a font. The font was last downloaded on 2023.02.07 and stored [here](../../src/client/src/assets/fonts/MaterialIcons-Regular-2023-02-07.woff2).
Material icons are rarely updated. They were downloaded to prevent continuous requests to Google servers when loading our app.

Use the following site to check which icons exist: https://fonts.google.com/icons?selected=Material+Icons

Then, create any tag you want (if you don't need one in particular, prefer "i" tags for "icons"), and give it the `.material-icons` class. Use Tailwind font size utilities to size the icon. Example:

```html
<i class="material-icons text-base">arrow_drop_down</i>
```

### Deprecated

The use of every other styling library in VISKAR is **deprecated**. The goal is to replace everything with Tailwind styles and material icons, or custom icons, and when needed, implement our own reusable components in SCSS by combining Tailwind styles.

When more complexe components are requiered, a reusable Angular component, styled with Tailwind, should be created.

## Reusable style components

If you are in need of a mildly-to-highly complexe design element, please check the list below to see if it already exist.

If nothing fits your need:

- if your need is too peculiar and unlikely to be reused anywhere, simply design it inline
- if your need is likely to be reused in a near future somewhere else in the app, consider creating a well-thought-of reusable component, and adding it to the list below

### Style components

All found in [styles.scss](../../src/client/src/styles.scss)

**.vk-radio / .vk-checkbox**

Used to create simple material design radio and checkbox inputs.

- to size this component, apply a font size utility on the `.vk-radio/checkbox` element
- to color this component, apply a text color utility on the `.vk-radio/checkbox` element

**.vk-plus-checkbox**

An alternate checkbox more suited to "adding elements to a selection list". A rounded yellow square with a big "plus" icon. Once selected, becomes gray and has a "checked" icon to indicate it is already selected. On hover, a "minus" icon is shown to indicate it can be removed.

- to size this component, apply a width/height utility on the `.vk-plus-checkbox` element

**.vk-button**

Basic not-too-opinionated button with disabled/hover/active styles for the entire app. Can be used on any elements (a, button, div, whatever). The element can contain anything, such as an icon and text

- combine this component with a width/grow/padding-x and a background color

**.vk-clickable**

Custom CSS to quickly make an element appear clickable. Changes the cursor, and makes sure it becomes unselectable.

**Forms**

A few classes where made to quickly style form elements. This includes:

- **.vk-form-label**
- **.vk-form-text-input**
- **.vk-form-number-input**
- **.vk-form-select-input**
- **.vk-form-textarea-input**
- **.vk-required**

### Style + Typescript components

These are style components that requiered a tad bit of scripting to work. They are also located in [styles.scss](../../src/client/src/styles.scss), but have additional scripts needed to make them work

**.vk-dropdown**

Basic not-too-opinionated dropdown. Has one button to open it, and a list of buttons for the choices.
Sets `data-open="open"` on the main button to know if it's currently open.

- combine this component with a width/grow

Example usage. The HTML structure needs to be respected:

```html
<div class="vk-dropdown w-some-width">
  <button class="truncate" (click)="dropdownToggle($event)" (blur)="dropdownOnBlur($event)">{{ selectedElement }}</button>
  <ul>
    <li *ngFor="let element of selectableElements">
      <button class="truncate" (click)="myHandler($event, element)">
        {{ element }}
      </button>
    </li>
  </ul>
</div>
```

```typescript
import { dropdownOnBlur, dropdownOnClick, dropdownToggle } from 'src/app/utility/dropdown';

export class MyComponent {
  // This component has VK Dropdowns, put the functions into scope to use them in the template
  dropdownToggle = dropdownToggle;
  dropdownOnBlur = dropdownOnBlur;

  selectableElements: string[]
  selectedElement: string

  // [...]

  myHandler(event: any, element: string) {
    // call this with the event to properly close the dropdown
    dropdownOnClick(event);
    // put any logic when a dropdown element is chosen here
    this.selectedElement = element;
  }
```


### Angular components

**Base Modal**

See detailed [documentation in the component](../../src/client/src/app/utility/base-modal/base-modal.component.ts) to learn how to use or make your own modal.

Usage examples:

- the confirm modal (see below) is the perfect example, since it implements a reusable custom modal based on this one
- the [Import Geodata Poi Updater](../../src/client/src/app/vk/project/wizard/project-setup/meta-project-wizard/import-geodata-poi/import-geodata-poi-updater/import-geodata-poi-updater.component.html) is a more complexe example of a custom one-off modal component


**Confirm Modal**

See detailed [documentation in the component](../../src/client/src/app/utility/confirm-modal/confirm-modal.component.ts) to learn how to use.

- this modal can be used instead of the native `confirm` browser function. It also has an alternative "open/confirmed event" mode.

Usage examples:

- the MetaProject Combiner ([ts](../../src/client/src/app/vk/project/wizard/project-setup/meta-project-wizard/components/meta-project-combiner.component.ts#L321)/[html](../../src/client/src/app/vk/project/wizard/project-setup/meta-project-wizard/components/meta-project-combiner.component.html#L164)) component, for a "confirm" usage
- the Combine DEMs Maps([ts](../../src/client/src/app/vk/project/wizard/project-setup/meta-project-wizard/combine-dems-maps/combine-dems-maps.component.ts#133)/[html](../../src/client/src/app/vk/project/wizard/project-setup/meta-project-wizard/combine-dems-maps/combine-dems-maps.component.html#L79)) component, for the alternative "open/confirmed event" usage
