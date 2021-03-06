<p align="center">
  <img src="https://github.com/terkelg/prompts/raw/master/prompts.png" alt="Prompts" width="500" height="120" />
</p>
 
<h1 align="center">❯ Prompts</h1>

<p align="center">
  <a href="https://npmjs.org/package/prompts">
    <img src="https://img.shields.io/npm/v/prompts.svg" alt="version" />
  </a>
  <a href="https://travis-ci.org/terkelg/prompts">
    <img src="https://img.shields.io/travis/terkelg/prompts.svg" alt="travis" />
  </a>
  <a href="https://npmjs.org/package/prompts">
    <img src="https://img.shields.io/npm/dm/prompts.svg" alt="downloads" />
  </a>
</p>

<p align="center">
  <b>Lightweight, beautiful and user-friendly interactive prompts</b></br>
  <sub>>_ Easy to use CLI prompts to enquire users for information▌<sub> 
</p>

<br />

* **Simple**: prompts has [no big dependencies](http://npm.anvaka.com/#/view/2d/prompts) nor is it broken into a [dozen](http://npm.anvaka.com/#/view/2d/inquirer) tiny modules that only work well together.
* **User friendly**: prompt uses layout and colors to create beautiful cli interfaces.
* **Promised**: uses promises and `async`/`await`. No callback hell.
* **Flexible**: all prompts are independent and can be used on their own.
* **Testable**: provides a way to submit answers programmatically.
* **Unified**: consistent experience across all prompts. 


![split](https://github.com/terkelg/prompts/raw/master/media/split.png)


## ❯ Install

```
$ npm install --save prompts
```

> This package uses async/await and requires Node.js 7.6

![split](https://github.com/terkelg/prompts/raw/master/media/split.png)

## ❯ Usage

<img src="https://github.com/terkelg/prompts/raw/master/media/number.gif" alt="example prompt" width="499" height="103" />

```js
const prompts = require('prompts');

const response = await prompts({
    type: 'number',
    name: 'value',
    message: 'How old are you?'
});

console.log(response); // => { value: 23 }
```

> Examples are meant to be illustrative. `await` calls need to be run within an async function. See [`example.js`](https://github.com/terkelg/prompts/blob/master/example.js).


![split](https://github.com/terkelg/prompts/raw/master/media/split.png)


## ❯ Examples

### Single Prompt

Prompt with a single prompt object. Returns object with the response.

```js
const prompts = require('prompts');

let response = await prompts({
    type: 'text',
    name: 'meaning',
    message: 'What is the meaning of life?'
});

console.log(response.meaning);
```

### Prompt Chain

Prompt with a list of prompt objects. Returns object with response.
Make sure to give each prompt a unique `name` property to prevent overwriting values.

```js
const prompts = require('prompts');

let questions = [
    {
        type: 'text',
        name: 'username',
        message: 'What is your GitHub username?'
    },
    {
        type: 'number',
        name: 'age',
        message: 'How old are you?'
    },
    {
        type: 'text',
        name: 'about',
        message: 'Tell something about yourself',
        initial: 'Why should I?'
    }
];

let response = await prompts(questions);

// => response => { username, age, about }
```

### Dynamic Prompts

Prompt properties can be functions too.
Prompt Objects with `type` set to `falsy` values are skipped.

```js
const prompts = require('prompts');

let questions = [
    {
        type: 'text',
        name: 'dish',
        message: 'Do you like pizza?'
    },
    {
        type: prev => prev == 'pizza' ? 'text' : null,
        name: 'topping',
        message: 'Name a topping'
    }
];

let response = await prompts(questions);
```


![split](https://github.com/terkelg/prompts/raw/master/media/split.png)


## ❯ API

### prompts(prompts, options)

Type: `Function`<br>
Returns: `Object`

Prompter function which takes your [prompt objects](#-prompt-objects) and returns an object with responses.


#### prompts

Type: `Array|Object`<br>

Array of [prompt objects](#-prompt-objects).
 These are the questions the user will be prompted. You can see the list of supported [prompt types here](#-types).

Prompts can be submitted (<kbd>return</kbd>, <kbd>enter</kbd>) or canceled (<kbd>esc</kbd>, <kbd>abort</kbd>, <kbd>ctrl</kbd>+<kbd>c</kbd>, <kbd>ctrl</kbd>+<kbd>d</kbd>). No property is being defined on the returned response object when a prompt is canceled.

#### options.onSubmit

Type: `Function`<br>
Default: `() => {}`

Callback that's invoked after each prompt submission.
Its signature is `(prompt, response)` where `prompt` is the current prompt object.

Return `true` to quit the prompt chain and return all collected responses so far, otherwise continue to iterate prompt objects.

**Example:**
```js
let questions = [{ ... }];
let onSubmit = (prompt, response) => console.log(`Thanks I got ${response} from ${prompt.name}`);
let response = await prompts(questions, { onSubmit });
```

#### options.onCancel

Type: `Function`<br>
Default: `() => {}`

Callback that's invoked when the user cancels/exits the prompt.
Its signature is `(prompt)` where `prompt` is the current prompt object.

Return `true` to quit the prompt loop and return all collected responses so far, otherwise continue to iterate prompt objects.

**Example:**
```js
let questions = [{ ... }];
let onCancel = prompt => {
  console.log('Lets stop prompting');
  return true;
}
let response = await prompts(questions, { onCancel });
```


### inject(values)

Type: `Function`<br>

Programmatically inject responses. This enables you to prepare the responses ahead of time.
If any injected values are found the prompt is immediately resolved with the injected value.
This feature is intended for testing only.

#### values

Type: `Object`

Object with key/values to inject. Resolved values are deleted from the internel inject object.

**Example:**
```js
const prompts = require('prompts');

prompts.inject({ q1: 'a1', q2: 'q2' });
let response = await prompts({
  type: 'text',
  name: 'q1',
  message: 'Question 1'
});

// => { q1: 'a1' }

```

> When `q1` resolves it's wiped. `q2` doesn't resolve and is left untouched.


![split](https://github.com/terkelg/prompts/raw/master/media/split.png)


## ❯ Prompt Objects

Prompts Objects are JavaScript objects that define the "questions" and the [type of prompt](#-types).
Almost all prompt objects have the following properties:

```js
{
  type: String || Function,
  name: String || Function,
  message: String || Function,
  initial: String || Function || Async Function
  format: Function,
  onState: Function
}
```

Each property be of type `function` and will be invoked right before prompting the user.

The function signature is `(prev, values, prompt)`, where `prev` is the value from the previous prompt, 
`values` is the response object with all values collected so far and `prompt` is the previous prompt object.

**Function example:**
```js
{
    type: prev => prev > 3 ? 'confirm' : null,
    name: 'confirm',
    message: (prev, values) => `Please confirm that you eat ${values.dish} times ${prev} a day?`
}
```

The above prompt will be skipped if the value of the previous prompt is less than 3.

### type

Type: `String|Function`

Defines the type of prompt to display. See the list of [prompt types](#-types) for valid values.

If `type` is a falsy value the prompter will skip that question.
```js
{
  type: null,
  name: 'forgetme',
  message: `I'll never be shown anyway`,
}
```

### name

Type: `String|Function`

The response will be saved under this key/property in the returned response object.
In case you have multiple prompts with the same name only the latest response will be stored.

> Make sure to give prompts unique names if you don't want to overwrite previous values.

### message

Type: `String|Function`

The message to be displayed to the user.

### initial

Type: `String|Function`

Optional default prompt value. Async functions are suported too.

### format

Type: `Function`

Receive the user input and return the formatted value to be used inside the program.
The value returned will be added to the response object.

The function signature is `(val, values)`, where `val` is the value from the current prompt and
`values` is the current response object in case you need to format based on previous responses.

**Example:**
```js
{
    type: 'number',
    name: 'price',
    message: 'Enter price',
    format: val => Intl.NumberFormat(undefined, { style: 'currency', currency: 'USD' }).format(val);
}
```

### onState

Type: `Function`

Callback for when the state of the current prompt changes.
The function signature is `(state)` where `state` is an object with a snapshot of the current state.
The state object have two properties `value` and `aborted`. E.g `{ value: 'This is ', aborted: false }`


![split](https://github.com/terkelg/prompts/raw/master/media/split.png)


## ❯ Types

### text(message, [initial], [style])
> Text prompt for free text input.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/text.gif" alt="text prompt" width="499" height="103" />

```js
{
  type: 'text',
  name: 'value',
  message: `What's your twitter handle?`,
  style: 'default',
  initial: ''
}
```

#### Options
| Param | Type | Default | Description |
| --- | --- | --- | --- |
| message | <code>string</code> |  | Prompt message to display |
| initial | <code>string</code> | <code>''</code> | Default string value |
| style | <code>string</code> | <code>'default'</code> | Render style (`default`, `password`, `invisible`) |
| format | <code>function</code> |  | Receive user input. The returned value will be added to the response object |
| onState | <code>function</code> |  | On state change callback |


### password(message, [initial])
> Password prompt with masked input.

This prompt is a similar to a prompt of type `'text'` with `style` set to `'password'`.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/password.gif" alt="password prompt" width="499" height="103" />

```js
{
  type: 'password',
  name: 'value',
  message: 'Tell me a secret',
  initial '',
}
```

#### Options
| Param | Type | Description |
| --- | --- | --- |
| message | <code>string</code> | Prompt message to display |
| initial | <code>string</code> | Default string value |
| format | <code>function</code> | Receive user input. The returned value will be added to the response object |
| onState | <code>function</code> | On state change callback | 


### invisible(message, [initial])
> Prompts user for invisible text input.

This prompt is working like `sudo` where the input is invisible.
This prompt is a similar to a prompt of type `'text'` with style set to `'invisible'`.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/invisible.gif" alt="invisible prompt" width="499" height="103" />

```js
{
  type: 'invisible',
  name: 'value',
  message: 'Enter password',
  initial: ''
}
```

#### Options
| Param | Type | Description |
| --- | --- | --- |
| message | <code>string</code> | Prompt message to display |
| initial | <code>string</code> | Default string value |
| format | <code>function</code> | Receive user input. The returned value will be added to the response object |
| onState | <code>function</code> | On state change callback |


### number(message, initial, [max], [min], [style])
> Prompts user for number input. 

You can type in numbers and use <kbd>up</kbd>/<kbd>down</kbd> to increase/decrease the value. Only numbers are allowed as input.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/number.gif" alt="number prompt" width="499" height="103" />

```js
{
  type: 'number'
  name: 'value',
  message: 'How old are you?',
  initial: 0,
  style: 'default',
  min: 2,
  max: 10
}
```

#### Options
| Param | Type | Default | Description |
| --- | --- | --- | --- |
| message | <code>string</code> |  | Prompt message to display |
| initial | <code>number</code> | `null` | Default number value |
| format | <code>function</code> |  | Receive user input. The returned value will be added to the response object |
| max | <code>number</code> | `Infinity` | Max value |
| min | <code>number</code> | `-infinity` | Min value |
| style | <code>string</code> | <code>'default'</code> | Render style (`default`, `password`, `invisible`) |
| onState | <code>function</code> |  | On state change callback | 

### confirm(message, [initial])
> Classic yes/no prompt.

Hit <kbd>y</kbd> or <kbd>n</kbd> to confirm/reject.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/confirm.gif" alt="confirm prompt" width="499" height="103" />

```js
{
  type: 'confirm'
  name: 'value',
  message: 'Can you confirm?',
  initial: true
}
```


#### Options
| Param | Type | Default | Description |
| --- | --- | --- | --- |
| message | <code>string</code> |  | Prompt message to display |
| initial | <code>boolean</code> | <code>false</code> | Default value |
| format | <code>function</code> |  | Receive user input. The returned value will be added to the response object |
| onState | <code>function</code> |  | On state change callback | 

### list(message, [initial])
> List prompt that return an array.

Similar to the `text` prompt, but the output is an `Array` containing the
string separated by `separator`.

```js
{
  type: 'list'
  name: 'value',
  message: 'Enter keywords',
  initial: '',
  separator: ','
}
```

<img src="https://github.com/terkelg/prompts/raw/master/media/list.gif" alt="list prompt" width="499" height="103" />


| Param | Type | Default | Description |
| --- | --- | --- | --- |
| message | <code>string</code> |  | Prompt message to display |
| initial | <code>boolean</code> | <code>false</code> | Default value |
| format | <code>function</code> |  | Receive user input. The returned value will be added to the response object |
| seperator | <code>string</code> | <code>','</code> | String seperator. Will trim all white-spaces from start and end of string |
| onState | <code>function</code> |  | On state change callback |


### toggle(message, [initial], [active], [inactive])
> Interactive toggle/switch prompt.

Use tab or <kbd>arrow keys</kbd>/<kbd>tab</kbd>/<kbd>space</kbd> to switch between options.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/toggle.gif" alt="toggle prompt" width="499" height="103" />

```js
{
  type: 'toggle',
  name: 'value',
  message: 'Can you confirm?',
  initial: true,
  active: 'yes',
  inactive: 'no'
}
```

#### Options
| Param | Type | Default | Description |
| --- | --- | --- | --- |
| message | <code>string</code> |  | Prompt message to display |
| initial | <code>boolean</code> | <code>false</code> | Default value |
| format | <code>function</code> |  | Receive user input. The returned value will be added to the response object |
| active | <code>string</code> | <code>'on'</code> | Text for `active` state |
| inactive | <code>string</code> | <code>'off'</code> | Text for `inactive` state |
| onState | <code>function</code> |  | On state change callback |

### select(message, choices, [initial])
> Interactive select prompt.

Use <kbd>up</kbd>/<kbd>down</kbd> to navigate. Use <kbd>tab</kbd> to cycle the list.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/select.gif" alt="select prompt" width="499" height="130" />

```js
{
    type: 'select',
    name: 'value',
    message: 'Pick a color',
    choices: [
        { title: 'Red', value: '#ff0000' },
        { title: 'Green', value: '#00ff00' },
        { title: 'Blue', value: '#0000ff' }
    ],
    initial: 1
}
```

#### Options
| Param | Type | Description |
| --- | --- | --- |
| message | <code>string</code> | Prompt message to display |
| initial | <code>number</code> | Index of default value |
| format | <code>function</code> | Receive user input. The returned value will be added to the response object |
| choices | <code>Array</code> | Array of choices objects `[{ title, value }, ...]` |
| onState | <code>function</code> | On state change callback |


### multiselect(message, choices, [initial], [max], [hint])
> Interactive multi-select prompt.

Use <kbd>space</kbd> to toggle select/unselect and <kbd>up</kbd>/<kbd>down</kbd> to navigate. Use <kbd>tab</kbd> to cycle the list. You can also use <kbd>right</kbd> to select and <kbd>left</kbd> to deselect.
By default this prompt returns an `array` containing the **values** of the selected items - not their display title.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/multiselect.gif" alt="multiselect prompt" width="499" height="130" />

```js
{
    type: 'multiselect',
    name: 'value',
    message: 'Pick colors',
    choices: [
        { title: 'Red', value: '#ff0000' },
        { title: 'Green', value: '#00ff00' },
        { title: 'Blue', value: '#0000ff', selected: true }
    ],
    initial: 1,
    max: 2,
    hint: '- Space to select. Return to submit'
}
```

#### Options
| Param | Type | Description |
| --- | --- | --- |
| message | <code>string</code> | Prompt message to display |
| format | <code>function</code> | Receive user input. The returned value will be added to the response object |
| choices | <code>Array</code> | Array of choices objects `[{ title, value, [selected] }, ...]` |
| max | <code>number</code> | Max select |
| hint | <code>string</code> | Hint to display user |
| onState | <code>function</code> | On state change callback | 

This is one of the few prompts that don't take a initial value.
If you want to predefine selected values, give the choice object an `selected` property of `true`.


### autocomplete(message, choices, [initial], [suggest], [limit], [style])
> Interactive auto complete prompt. 

The prompt will list options based on user input. Type to filter the list.
Use <kbd>up</kbd>/<kbd>down</kbd> to navigate. Use <kbd>tab</kbd> to cycle the result. Hit <kbd>enter</kbd> to select the highlighted item below the prompt. 

The default suggests function is sorting based on the `title` property of the choices.
You can overwrite how choices are being filtered by passing your own suggest function.

#### Example
<img src="https://github.com/terkelg/prompts/raw/master/media/autocomplete.gif" alt="auto complete prompt" width="499" height="163" />

```js
{
    type: 'autocomplete',
    name: 'value',
    message: 'Pick your favorite actor',
    choices: [
        { title: 'Cage' },
        { title: 'Clooney', value: 'silver-fox' },
        { title: 'Gyllenhaal' },
        { title: 'Gibson' },
        { title: 'Grant' },
    ]
}
```

#### Options
| Param | Type | Default | Description |
| --- | --- | --- | --- |
| message | <code>string</code> |  | Prompt message to display |
| format | <code>function</code> |  | Receive user input. The returned value will be added to the response object |
| choices | <code>Array</code> |  | Array of auto-complete choices objects `[{ title, value }, ...]` |
| suggest | <code>function</code> | By `title` string | Filter function. Defaults to stort by `title` property. `suggest` should always return a promise |
| limit | <code>number</code> | <code>10</code> | Max number of results to show |
| style | <code>string</code> | `'default'` | Render style (`default`, `password`, `invisible`) |
| onState | <code>function</code> |  | On state change callback |

Example on what a `suggest` function might look like:
```js
const suggestByTitle = (input, choices) =>
  Promise.resolve(choices.filter(i => i.title.slice(0, input.length) === input))
```


![split](https://github.com/terkelg/prompts/raw/master/media/split.png)


## ❯ Credit
Many of the prompts are based on the work of [derhuerst](https://github.com/derhuerst).


## ❯ License

MIT © [Terkel Gjervig](https://terkel.com)
