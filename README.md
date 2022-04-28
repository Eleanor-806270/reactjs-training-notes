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
- 套件管理工具:
  - npm/yarn: 後者由Facebook開發，起初是為了解決npm過於肥大
  - [package.json](https://docs.npmjs.com/cli/v7/configuring-npm/package-json): records important metadata about a project which is required before publishing to NPM
  - package-lock.json/yarn.lock: 固定套件版本
- 打包工具
  - webpack
  - rollup
  - parcel
- babel: JS compiler, convert ES6 (2015+) => into a backwards compatible version of JS that can be run on older browser.
  - JSX => plain JS
  - polyfills
- react-dom: react的虛擬DOM
- react-script
    ```
    "scripts: {
      "start": "react-scripts start",
      "build": "react-scripts build",
      "test": "react-scripts test",
      "eject": "react-scripts eject"
    },
    ```

create-react-app將上述(及更多)的config處理好 讓使用者不用去煩惱專案設定可以專心coding

<br>

## React fundamentals

- data binding, e.g., controlled form/input, 由react state控制form/input的值
  - 表單驗證: With a controlled component, the input's value is always driven by the React state

- virtual dom 節省效能，在每次render前, react會檢視異動後render畫面([by react的演算法](https://medium.com/coding-hot-pot/react-vitural-dom-134fa79e0d4f))

- functional component is a js function, which react will take over and execute the function

- communication between components: redux, a global state management (TBD)
  - best practice: 通常就傳一層，不要超過 2 等親，不然會很難維護
  - best practice: 傳入的 props，如果是 function，可以加上 prefix "on" eg onCallParentComponent

  <br>

- react.memo: achieve memory boost by memorizing the result 快取 決定是否要render component
  - higher order function
  - only checks for prop change: <b>只檢查第一層(shallowEqual)</b>，可以放自定義的function當作第二個參數

    ```
      // Test for A's keys different from B.
    for (let i = 0; i < keysA.length; i++) {
      const currentKey = keysA[i];
      if (
        !hasOwnProperty.call(objB, currentKey) ||
        !is(objA[currentKey], objB[currentKey])
      ) {
        return false;
      }
    }

    return true;
    ```
    [View React source code](https://github.com/facebook/react/blob/main/packages/shared/shallowEqual.js)

<br>


## React hooks

- useState
  - setter function is <b>asynchronous</b> (react optimize the execution timing for you), 可以用useEffect，然後把state放進dependency array中 [see example](https://dev.to/shareef/react-usestate-hook-is-asynchronous-1hia)



- useEffct
  ```
  // 執行callback when component first render (常用在API CALL)
  useEffect(() => {
    console.log('run once when the component mounted')
  }, [])

  // 執行callback when 'value' change
  useEffect(() => {
    console.log('value changes')
  }, [value])

  // cleanup side effect when component is unmounted
  useEffect(() => {
    let timeout = setTimeout(() => {
      // do something
    }, 1000)

    return () => clearTimeout(timeout)
  }, [value])

  ```
  - how does react detect changes in dependency array? => `Object.is` =>
    ```
    for (let i = 0; i < prevDeps.length && i < nextDeps.length; i++) {
      if (is(nextDeps[i], prevDeps[i])) {
        continue;
      }
      return false;
    }
    return true;
    ```
    [View React source code](https://github.com/facebook/react/blob/c2034716a5bff586ab68c41a14139a535cbd788e/packages/react-reconciler/src/ReactFiberHooks.js#L314-L361)
<br>
<br>
- useRef: store variables (that don't affect component to render) & control DOM element, use the special attribute `ref` in JSX,
  - use case: focus the input box while validating form input
  - 課堂練習: Automatically focus the input box when it shows (ueRefExample3)

    ```
      const UseRefExample2 = () => {
      const [show, setShow] = React.useState(false);
      /**
      * @type {React.MutableRefObject<HTMLInputElement>}
      */
      const refInput = React.useRef();

      const atShowClick = () => {
        setShow(!show);
        console.log(refInput.current); // undefined
      };

      // 用useEffect，把state放進dependency array中
      React.useEffect(() => {
        if (show) {
          // console.log(refInput.current); // correct
          refInput.current.focus();
        }
      }, [show]);

      return (
        <section data-name="UseRefExample2">
          <div>
            <button className="my-btn" onClick={atShowClick}>
              show input
            </button>
          </div>
          {show && <input ref={refInput} />}
        </section>
      );
    };

    ```

- useState vs useRef: the former will cause component to rerender, ref return an object, while useState return an array

- useMemo, useCallback 優化用，to avoid unnecessary re-renders

- useCallback: to avoid a new function being created in the memory
  - call by value and call by reference
  - best practice/tip: 只要此 function 有當作 props 傳入到其他 component, 一律用 useCallback 包起來

- useCallback vs useMemo: one Returns a memoized callback, while useMemo returns a memoized value

    |                   | useMemo      | useCallback  |
    | -------------     |:-------------| -----:|
    | return a memoized |  value       |  callback |
    | purpose           |避免重複execute耗時的運算, e.g., infinite loop|避免在 component 內部宣告的 function，因為隨著每次 render 不斷重新被宣告跟建立 |
    | zebra stripes | are neat      |    $1 |



<br>

### 前端知識 & best practice & tips

<br>

- JS DOC to declare type (Example12-2)

  ```
    /**
    * @type {React.MutableRefObject<HTMLInputElement>}
    */
  ```



- everything that renders in HTML is <b>string</b>

- How React decided when to update components? [React core concept: shallowEqual](https://github.com/facebook/react/blob/main/packages/shared/shallowEqual.js)


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
- CRA 專案內建 SCSS module，scss module的className會自動帶一段hash值以防style相撞

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
3. [來自中國程序猿ㄉ筆記](https://github.com/puxiao/react-hook-tutorial)

##### 20220418 todo:

1. [homework](https://codesandbox.io/s/01-conditional-render-question-r51szo?file=/src/App.js)
2. study [flowjs](https://flow.org/), and best practice to write js doc
3. study object.is

##### 20220421 todo:

1. [homework](https://codesandbox.io/s/02-countdown-question-x83v9?file=/src/App.js:633-639): countdown: 實作 custom hook: useCountdown (hint: % get remainder)
2. study some parts of [React source code](https://docs.google.com/document/d/1lv2h1NluwII_dJ-VpAkJEj8Br-8YP5ZUTVeOrd-nLaE/edit)

======= 20220428 ========
1. emotion css in JS, 可以使用於user defined style 由api回傳css 字串 (但如果style已知，不推薦css in JS: for readibility and maintainance reason
b_03_emotion-css-module: 提供 examples of styled-component, emotion, emtion/react & css module(the most recommended tool)
  ```
  <button
          // THIS IS BAD!!

          css={css`
            color: ${select ? 'white' : 'black'};
            background: ${select ? 'black' : 'white'};
            padding: 10px;
            border-radius: 20px;
          `}
          onClick={() => setSelect(!select)}
        >
          Switch
        </button>
  ```

2. @emtion/react=> cause higher order component, decrease performance 專案龐大後會犧牲效能
3. 團隊可自定義 html attribute來控制style eg, data-active (see example5)
4. react useContext, react built-in api for cross-component communication (performance problem, not recommended in large scale project)