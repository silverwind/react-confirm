# react-confirm
react-confirm is a lightweight library that simplifies the implementation of confirmation dialogs in React applications by offering a Promise-based API that works seamlessly with async/await syntax, similar to `window.confirm`.

Another key feature of react-confirm is that it doesn't provide a specific view or component for the confirmation dialog, which allows you to easily customize the appearance of the dialog to match your application's design. 

In the [example](https://github.com/haradakunihiko/react-confirm/tree/master/example), [react-bootstrap](https://react-bootstrap-v3.netlify.app/components/modal/) and [material-ui](http://www.material-ui.com/#/components/dialog) are used with.

[![npm version](https://badge.fury.io/js/react-confirm.svg)](https://badge.fury.io/js/react-confirm)

## Motivation
React is a powerful library that allows for reactive rendering based on component state. However, managing temporary states like confirmation dialogs can quickly become complex. The question is: is it worth implementing these states within your app? The answer is not always a clear yes.

## What you can do
react-confirm library offers several benefits:

- You can open a dialog component by calling a function without appending it into your React tree. The function returns a promise, allowing you to handle confirmation results with callbacks.
- You can pass arguments to the function and use them inside the dialog component.
- You can retrieve values from the component in the promise.
- The library provides flexibility in designing the dialog. There is no limitation in the type of components you can use, whether it be input forms or multiple buttons. You can even check out the demo site to see examples of how to customize the dialog.

## Demo
https://codesandbox.io/s/react-confirm-with-react-bootstrap-kjju1

## Versions

- React 18+ users should use `react-confirm` version 0.2.x or 0.3.x
- React <=17 users should stick to `react-confirm` version 0.1.x

## Usage
1. create your dialog component.
2. apply `confirmable` to your component (Optional. See `confirmable` implementation).
3. create function with `createConfirmation` by passing your confirmable component.
4. call it!

### create confirmable component

```js
import React from 'react';
import PropTypes from 'prop-types';
import { confirmable } from 'react-confirm';
import Dialog from 'any-dialog-library'; // your choice.

const YourDialog = ({show, proceed, confirmation, options}) => (
  <Dialog onHide={() => proceed(false)} show={show}>
    {confirmation}
    <button onClick={() => proceed(false)}>CANCEL</button>
    <button onClick={() => proceed(true)}>OK</button>
  </Dialog>
)

YourDialog.propTypes = {
  show: PropTypes.bool,            // from confirmable. indicates if the dialog is shown or not.
  proceed: PropTypes.func,         // from confirmable. call to close the dialog with promise resolved.
  confirmation: PropTypes.string,  // arguments of your confirm function
  options: PropTypes.object        // arguments of your confirm function
}

// confirmable HOC pass props `show`, `dismiss`, `cancel` and `proceed` to your component.
export default confirmable(YourDialog);

// or, use `confirmable` as decorator
@confirmable
class YourDialog extends React.Component {
}


```

### create confirm function
```js
import { createConfirmation } from 'react-confirm';
import YourDialog from './YourDialog';

// create confirm function
export const confirm = createConfirmation(YourDialog);

// This is optional. But wrapping function makes it easy to use.
export function confirmWrapper(confirmation, options = {}) {
  return confirm({ confirmation, options });
}

```

### use it!
Now, you can show dialog just like window.confirm with async-await. The most common example is onclick handler for submit buttons.
 
```js
import { confirmWrapper, confirm } from './confirm'

const handleOnClick = async () => {
  if (await confirm({
    confirmation: 'Are you sure?'
  })) {
    console.log('yes');
  } else {
    console.log('no');
  }
}

const handleOnClick2 = async () => {
  if (await confirmWrapper('Are your sure?')) {
    console.log('yes');
  } else {
    console.log('no');
  }
}

```

You can check more complex example in [codesandbox](https://codesandbox.io/s/react-confirm-with-react-bootstrap-kjju1)

## Using with Context
By default, this library renders the confirmation dialog without appending the component to your app's React component tree. While this can be useful, it may cause issues if you need to consume context in your component. To overcome this problem, you can use the `MountPoint` component to include your confirmation dialog within your app's tree, enabling it to access context and other data from the app.

Create your own `createConfirmation` using `createConfirmationCreater` and `createReactTreeMounter`.

```js
import { createConfirmationCreater, createReactTreeMounter, createMountPoint } from 'react-confirm';

const mounter = createReactTreeMounter(); 

export const createConfirmation = createConfirmationCreater(mounter);
export const MountPoint = createMountPoint(mounter);
```

Put `MountPoint` into your React tree.
```js
const YourRootComponent = () => {
  return (
    <YourContext.Provider>
      <MountPoint />
      <Toolbar />
    </YourContext.Provider>
  )
}
```

use your `createConfirmation` as usual.
```js
export const confirm = createConfirmation(YourDialog);
```

To render the confirmation dialog within the React component tree but in a different part of the DOM, you can pass a DOM element to the `createReactTreeMounter` function. This will use the `createPortal` method to render the confirmation dialog in the specified DOM element while keeping it within the React component tree.

```js
const mounter = createReactTreeMounter(document.body); 
```

## typescript

Experimentally added full typescript declaration files at `typescript` branch.

see [typescript example](https://github.com/haradakunihiko/react-confirm/tree/typescript/example/ts-react-bootstrap).

and try `npm install git@github.com:haradakunihiko/react-confirm.git#typescript` to use in your project.
