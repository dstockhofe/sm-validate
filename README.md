# sm-validate
Validate is an extensible client side validation library for form
inputs. It provides a set of basic validators and the possibility
to write your own.

## Table of Contents

* [Installation](#installation)
  * [UMD](#umd)
  * [AMD](#amd)
  * [CommonJS](#commonjs)
  * [ESM](#esm)
* [Usage](#usage)
  * [Initialization](#initialization)
  * [Validating forms](#validating-forms)
  * [Configuration](#configuration)
  * [Attributes](#attributes)
  * [CSS classes](#css-classes)
* [Custom validators](#custom-validators)
* [Todo](#todo)
* [Contributing](#contributing)
* [License](#license)

## Installation
Run `npm install -S @shopmacher/validate` to install the package from npm.
Alternatively, you can download the latest release from this repository.

To include the library, refer to the module definition you are using.

### UMD
Include the `validate.js` and `validate.css` from the `lib` directory
in your project. This makes `Validate` available in the global scope.

### AMD
Adjust your `require.config.js` to include the following code:
```javascript
packages: [{
    name: '@shopmacher/validate',
    location: 'node_modules/@shopmacher/validate/lib',
    main: 'validate'
}]
```

Now you can use the slider in your project like this:
```javascript
define('myModule', ['@shopmacher/validate'], function(Validate) {
    // Access Validate object here
});
```

### CommonJS
Require the slider via `const Validate = require('@shopmacher/validate');` and use
the `Validate` variable to access its methods.

### ESM
Import the slider via `import Validate from '@shopmacher/validate';` and access it
via the `Validate` variable.

## Usage
This section describes how to initialise and configure the validator.

### Initialization
The library can either intialized via the class constructor for a single
input, or via a static method for all inputs that posses the attribute
`data-validate="true"`.

**Class constructor**
```javascript
const $input = document.querySelector('.my-input');
const validator = new Validator($input);
```

**Static initialization**
```javascript
const validators = Validator.init();
```

In case of static initialization, the method returns an array of validators.
This can later be used to validate forms on submit.

### Validating forms
To validate inputs on form submit, you can attach the validators from the
init method or the constructor to a form.

```javascript
const $form = document.querySelector('#my-form');
const validators = Validator.init();
Validator.attachToForm($form, validators);
```

This will check all validators, when the form is submitted and prevent the
submit, if one of the validators fails.

### Configuration
To validate an input, the library provides a set of basic validators that
are controlled via data-attributes. The following example would validate
the input for a minimum length of 5 characters and output the result into
an HTML element.

```html
<input
    data-validate="true"
    data-validate-minlength="5"
    data-validate-minlength-message="Please enter at least 5 chars"
    data-validate-error-element="#validate-message" />

<div id="validate-message"></div>
```

The validation triggers when the user leaves the input field or does not
type for 300 ms (default).

For a list of validations and configurations, please refer to the table below.

### Attributes
Those are the data-attributes you can use to configure the validators.

All attributes are prefixed by `data-validate-`. The messages that will
be emitted when the validator fails uses the `-message`-suffix on the
attribute (as seen in the example above).

| Type           | Attribute       | Description                                                                                                                                                               | Values                      | Required |
|----------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|----------|
| Initialization | validate        | If this attribute is `true`, the input is eligible for being validated.                                                                                                   | boolean                     | yes      |
| Validation     | minlength       | Minimum amount of characters required in the input                                                                                                                        | number                      | no       |
| Validation     | maxlength       | Maximum amount of characters allowed in the input                                                                                                                         | number                      | no       |
| Validation     | empty-message   | Message that is emitted, when the input is empty. This does not need the empty attribute.                                                                                 | any                         | no       |
| Validation     | type            | Type of the inputs content.                                                                                                                                               | "text" | "number" | "email" | no       |
| Validation     | regex           | Content of input is validated via the provided regular expression. Example: `^\d+$`                                                                                       | string / RegExp             | no       |
| Configuration  | invalid-message | Provides a generic message for failing validators. This can be used, if you do not want to provide separate messages for every validator.                                 | any                         | no       |
| Configuration  | error-element   | Provides an element selector to specify the element that the validation messages are rendered to. Example: `#my-output-dev`. Internally, this is passed to querySelector. | string                      | no       |
| Configuration  | debounce        | Time in ms that the validate event is debounced when the user types in an input field. Defaults to 300 ms.                                                                | number                      | no       |

### CSS classes
Whenever a validation failes, the CSS class `sm-validate-error` is added
to the validated element. This allows you to take control over different styling
of invalid inputs.

## Custom validators
In addition to using the predefined validators, you can provide your own.

### Defining a validator
A validator is a JavaScript function that follows a predetermined
scheme. A validator to check, if the inputs value is the string `Hello`
would be implemented like this:

```javascript
// ECMAScript 2015
const validator = {
    attr: 'hello',
    create: ($input, { value, message }) => ({
        isValid: () => $input.value === 'Hellow',
        message
    })
};
```

The property `attr` defines, through which data-attribite the validator
will be accessible.
In case of the example above, the data-attribute `data-validate-hello`
can be attached to the input to use the validator.
The validation message that is emitted if the validator fails can be
defined through the use of the attribute `data-validate-hello-message`.

The `create` method returns the instance of the validator that the
library is going to use.

It receives the following parameters:
* `$input` - the input field the validator is called upon
* An object consisting of:
  * `value` - the value of the input
  * `message` - the validation message (this is mainly used to pass it through)

The `create` method ifself returns an object consisting of:
* `isValid` - a function that returns true, if the input is valid
* `message` - the validation message
* `triggerOnChange` - boolean (default: true) that determines, if the validator should validate on change of the input
* `triggerOnSubmit` - boolean (default: true) that determines, if the validator should validate on form submit

### Using a custom validator
Once defined, the custom validator(s) can be passed to either the static
`init` function or the class constructor.

```javascript
const validator = { /* your validator */ };

Validator.init([validator]);

// or

new Validator($element, [validator]);
```

Note that the validators need to be passed as an array, which means
that you can pass as many as you want.

## Todo
- [x] Implements basic validators
- [x] Provide methods to plug-in custom validators
- [x] Extend documentation to cover custom validators
- [ ] Write unit tests

## Contributing
To contribute to this project, fork the repository and create
your feature/hotfix branch with whatever you want to add.

Install the project dependencies using `npm i` and start the
development server via `npm start`. A webpack-dev-server will now
listen on port 8080.

When you are finished developing, make sure to add a documented pull
request.

**Please note:** Pull requests for new features that are not typed via
flowtype as well as not following the general code style used in this
project will be rejected.

## License
MIT
