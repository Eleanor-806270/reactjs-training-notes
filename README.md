# Reactjs Training Notes

# JS ES6

## Variable Hoisting

- JS: 建立期 (creation phase) => 執行期 (execution phase)
- TDZ
- Function declaration vs Function expression
- ES6 const, let 是為了解決 var 的 scope 問題

## Execution Context

## Scope Chain

## prototype & \_\_proto\_\_

## this

- 從被呼叫的方式來決定 this: js => Window, node => Global
- arrow function 沒有 this
- apply, call, bind 會改變 this 的值
- operator: new (自動 return)
- "use strict"

## OOP in JS

## JS Event

- event bubbling
  - mouse event 才有冒泡
- event delegation
- event.currentTarget is this, event.target is the element that triggers the event

### Resources

1. [JS interview questions](https://github.com/lydiahallie/javascript-questions)
2. [JS: the weird parts](https://github.com/jasonbaciulis/javascript-understanding-the-weird-parts)

# ReactJS

## What CRA does under the hood?

- npm & package.json
- webpack
- babel

## React

- data binding
- virtual dom 節省效能
- functional component is a js function, which react will take over and execute the function
- useState
  - setter function is <b>asynchronous</b> (react optimize the execution timing for you)
- useEffct
  - how does react detect changes in dependency array? => `Object.is` =>
    [View React source code](https://github.com/facebook/react/blob/c2034716a5bff586ab68c41a14139a535cbd788e/packages/react-reconciler/src/ReactFiberHooks.js#L314-L361)
- useRef: store variables (that don't affect component to render) & control DOM element, use the special attribute `ref` in JSX,
  - use case: focus the input box while validating form input
  - 課堂練習: Automatically focus the input box when it shows (ueRefExample3)
- useState vs useRef: the former will cause component to rerender, ref return an object, while useState return an array
- JS DOC to declare type (Example12-2)

```
  /**
   * @type {React.MutableRefObject<HTMLInputElement>}
   */
```

- communication between components: redux, a global state management (TBD)
  - best practice: 通常就傳一層，不要超過 2 等親，不然會很難維護
  - best practice: 傳入的 props，如果是 function，可以加上 prefix "on" eg onCallParentComponent
- everything that renders in HTML is <b>string</b>
- Form validation: With a controlled component, the input's value is always driven by the React state
- How React decided when to update components? [React core concept: shallowEqual](https://github.com/facebook/react/blob/main/packages/shared/shallowEqual.js)
- useMemo, useCallback 優化用
- useCallback: to avoid a new function being created in the memory
  - call by value and call by reference
  - best practice/tip: 只要此 function 有當作 props 傳入到其他 component, 一律用 useCallback 包起來
- useCallback vs useMemo: one Returns a memoized callback, while useMemo returns a memoized value
- 為什麼不能用迴圈的 index 當作 element 的 key 值? 因為在 remove element 時會由 key 值來 determine which element should be removed, react diff 結果就是由 key 來判斷
  ![img](./img/forLoopKey.png)

- React 資料夾結構，利用 module.scss，會為 classname 加上一段 hash 值，以避免 global css
  - webpack.config: keyword:
  ```
      {
      loader: 'css-loader',
      options: {
        sourceMap: true,
        ...moduleOptions,
      },
    },
  ```
  - CRA 專案內建 SCSS module

### 前端知識 & best practice & tips

- style: JSX 中盡量減少邏輯的判斷，可以把邏輯拉出去。讓 react return 後做 view 的處理就好

- 在 google dev tool console `$0` 就可以取得當前滑鼠游標指的 DOM element

- `<label><input/></label>` 的做法增加點擊範圍 (for improving UX)

```
<label>
    <input
        name="gender"
        checked={gender === '0'}
        type="radio"
        value="0"
        onChange={atRadioChange}
    />
    女
</label>
```

- 非 html 原生地 attribute 加上 prefix `data-` eg. `data-index`，can get the value from `dataset`
- DOM element 的變動很吃效能 (and that's why react saves it from using virtual dom)
- 兩個斜線方便 comment and uncomment a block of code

```
 //* // TODO
    e.preventDefault();
    if (input.trim() === '') {
      return;
    }
    setInput('');
    onAddItem(input);
    // */
```

- all the npm packages use kebab case

<hr/>

### Resources

1. [老師的 doc](https://docs.google.com/document/d/1lv2h1NluwII_dJ-VpAkJEj8Br-8YP5ZUTVeOrd-nLaE/edit)
1. [react-use](https://github.com/streamich/react-use)

##### 20220418 todo:

1. [homework](https://codesandbox.io/s/01-conditional-render-question-r51szo?file=/src/App.js)
2. study [flowjs](https://flow.org/), and best practice to write js doc
3. study object.is

##### 20220421 todo:

1. [homework](https://codesandbox.io/s/02-countdown-question-x83v9?file=/src/App.js:633-639): countdown: 實作 custom hook: useCountdown (hint: % get remainder)
2. study some parts of [React source code](https://docs.google.com/document/d/1lv2h1NluwII_dJ-VpAkJEj8Br-8YP5ZUTVeOrd-nLaE/edit)
