# Flatfile Angular Component - @flatfile/angular

[![npm](https://img.shields.io/npm/v/@flatfile/angular.svg?label=npm%20version&color=2EBF6A&style=for-the-badge)](https://www.npmjs.com/@flatfile/angular)
[![Minzipped Size](https://img.shields.io/bundlephobia/minzip/@flatfile/angular?color=794cff&style=for-the-badge)](https://bundlephobia.com/result?p=@flatfile/angular)
[![NPM Downloads](https://img.shields.io/npm/dw/@flatfile/angular.svg?color=8c66ff&style=for-the-badge)](https://www.npmjs.com/@flatfile/angular)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=for-the-badge&color=794cff)](/LICENSE)

---

We've made it really simple for you to get started with Flatfile with our new Flatfile Component. Here's what you'll need to know to get started.

> Note: This package is ideally suited for Angular version 8+ (or higher)

First, install the dependency via npm:

`npm install @flatfile/angular`

This will give you access to the `<flatfile-button />` component as well as the same basic functionality as our Adapter.

---

#### Changelog

To view information about the latest releases and any minor/major changes, check out the [changelog here](./CHANGELOG.md).

---

## Codesandbox Demo

Try it for yourself in the [CodesandBox here](https://codesandbox.io/s/flatfile-angular-demo-w10yx?file=/src/app/app.component.ts).

---

## Getting Started with <flatfile-button>

```ts
import { FlatfileAdapterModule } from '@flatfile/angular';

// Add to your Modules imports: []
imports: [
  FlatfileAdapterModule
]
```

Within a Components template use the flatfile-button:

```ts
import {
  // This interface is optional, but helpful to show you all the available required & optional inputs/outputs available to you
  FlatfileMethods
} from "@flatfile/angular";

@Component({
  template: `
    <flatfile-button
      [settings]="settings"
      [customer]="customer"
      [licenseKey]="licenseKey">
      Text you want to show for the button
    </flatfile-button>
  `
}) export class MyDemoComponent implements FlatfileMethods {
  
  customer = { userId: '12345' };
  licenseKey = 'YOUR_LICENSE_KEY_HERE'; // <-- Don't forget your license key!
  settings = {
    type: 'test import',
    fields: [
      { label: 'Name', key: 'name' },
      { label: 'Email', key: 'email' },
    ],
  };
}
```

---

### More advanced use-case example

> Tip: Make sure to use `.bind(this)` when passing down things like onData/onRecordInit/onRecordChange so that if you're using any props/methods within your Component, you won't have any errors.

Within a Components template use the flatfile-button

```ts
import {
  FlatfileMethods,
  FieldHookCallback,
  FlatfileResults,
  IDataHookResponse,
  ScalarDictionaryWithCustom,
} from "@flatfile/angular";

@Component({
  template: `
    <flatfile-button
      [settings]="settings"
      [customer]="customer"
      [licenseKey]="licenseKey"
      [fieldHooks]="fieldHooks"
      [onData]="onData.bind(this)"
      [onRecordInit]="onRecordInit.bind(this)"
      [onRecordChange]="onRecordChange.bind(this)"
      (cancel)="onCancel()"
      class="flatfile-button">
      Text you want to show for the button
    </flatfile-button>
  `,
  /**
   * @note Important if you want to style the child component
   * from this "parent" component
   */
  encapsulation: ViewEncapsulation.None, 
  styles: [`
    .flatfile-button button {
        border: 0;
        border-radius: 3px;
        padding: 1rem;
        background: #794cff;
        color: #fff;
      }
  `],
}) export class MyDemoComponent implements FlatfileMethods {
  
  customer = { userId: '12345' };
  licenseKey = 'YOUR_LICENSE_KEY_HERE'; // <-- Don't forget your license key!
  settings = {
    type: 'test import',
    fields: [
      { label: 'Name', key: 'name' },
      { label: 'Email', key: 'email' },
    ],
  };

  /*
   * @Input()'s
   */
  fieldHooks: Record<string, FieldHookCallback> = {
    email: (values) => {
      return values.map(([item, index]) => [
        { value: item + '@', info: [{message: 'added @ after the email', level: 'warning'}] },
        index
      ]);
    }
  };

  onData(results: FlatfileResults): Promise<string> {
    let errorState = false;

    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (errorState) {
            reject('rejected - this text is controlled by the end-user');
            errorState = false;
          } else {
            resolve('Flatfile upload successful - this text is controlled by the end-user');
          }
      }, 3000);
    });
  }

  onRecordInit(record: ScalarDictionaryWithCustom, index: number): IDataHookResponse | Promise<IDataHookResponse> {
    return {
      email: {
        value: record.email + '@',
        info: [{ message: 'added @ on init', level: 'info' }]
      }
    };
  }

  onRecordChange(record: ScalarDictionaryWithCustom, index: number): IDataHookResponse | Promise<IDataHookResponse> {
    return {
      email: {
        value: record.email + '#',
        info: [{ message: 'added # on change', level: 'warning' }]
      }
    };
  }

  /*
   * @Output() handlers
   */
  onCancel(): void {
    console.log('canceled!');
  }
}
```

#### Styling the component

Note that in order to style this child-component (from the parent), simply supply a `class=""` to the `<flatfile-button class="some_class_name">`, and ensure that your parent component has `encapsulation` set to `ViewEncapsulation.None` (showcased above in this advanced demo).

---

| **<u>flatfile-button options</u>**                                                                                         | **<u>Info</u>**               | <u>**Example**</u>                     |
| ----------------------------------------------------------------------------------------------------------------------- | ----------------------------- | -------------------------------------- |
| `settings` - This is where you will pass your [Flatfile settings/options](https://developers.flatfile.io/docs/options). | **Required. ** <br />_object_ | `settings={{ `<br /> `type: "Customers", fields: [` <br /> `{key: "name", label: "Name"}, {key: "email", label: "Email"}`<br />`]}}` |
|`customer` - Refers to the _[setCustomer function](https://developers.flatfile.io/docs/sdk/classes/flatfileimporter#setcustomer)_. | **Required**. <br /> _object_ - [_CustomerObject_](https://developers.flatfile.io/docs/sdk/interfaces/customerobject) | `customer={{` <br />`usedId: "1234",`<br />`companyId: "12",`<br />` companyName: "ABC",`<br />`email: "john@doe.com",`<br />`name: "John Doe"`<br />`}}`|
|`licenseKey` - Your Flatfile license key can be found in your dashboard when you [login here](https://app.flatfile.io/login).|**Required**. <br /> _string_ | `licenseKey= 'ah12-alksjs2738-shdkaj123'` |
|`onCancel` - An optional callback for handling a user cancelling.|Optional. <br /> _function - callback_| `onCancel () { // do something }`|
|`onData`- An optional way to use [FlatfileResults](https://developers.flatfile.io/docs/sdk/classes/flatfileresults) to return a new Promise.|Optional. <br />_function_| `onData(results: FlatfileResults): Promise<string> { }`|
|`onRecordChange`- An optional way to use [registerRecordHook](https://developers.flatfile.io/docs/datahooks#record-hooks-row-hooks) when a record changes.| Optional. <br /> _function_ |`onRecordChange(record: ScalarDictionaryWithCustom, index: number): IDataHookResponse | Promise<IDataHookResponse> { }`|_`onRecordInit={(data, index) => `[`IDataHookResponse`](https://developers.flatfile.io/docs/sdk/interfaces/idatahookresponse) &#124; `Promise<`[`IDataHookResponse`](https://developers.flatfile.io/docs/sdk/interfaces/idatahookresponse)`>}`_|
|`fieldHooks`- An optional way to pass in one or more callback functions to use with [registerFieldHook](https://developers.flatfile.io/docs/datahooks#field-hooks-column-hooks).|Optional. <br />object function(s) - callback(s)|`fieldHooks: Record<string, FieldHookCallback> = { /* object */ }`|

---

### Running the sample application

```bash
npm i && npm start
```

The same application will be fired up at `http://localhost:4200`.

> Ensure that you've entered in a valid licenseKey in the `AppComponent` file (`projects/sample/app.component.ts`).

### Publishing

Ensure that the correct semantic version has been updated in the `projects/angular-adapter/package.json` version.

Make sure that if you're updating the (base package) `@fulcrumpro/adapter` version, to update it within the dependencies array of the `package.json` within the projects/angular-adapter/ library as well.

Then run:

```bash
npm run build:prod
npm run publish:pkg
```
