Title: React 19 Upgrade Guide – React

Description: The library for web and native user interfaces

Source: https://react.dev/blog/2024/04/25/react-19-upgrade-guide

---

[React](https://react.dev/)
[v19.2](https://react.dev/versions)
[Learn](https://react.dev/learn)
[Reference](https://react.dev/reference/react)
[Community](https://react.dev/community)
[Blog](https://react.dev/blog)
[Blog](https://react.dev/blog)

April 25, 2024 by [Ricky Hanlon](https://twitter.com/rickhanlonii)
The improvements added to React 19 require some breaking changes, but we’ve worked to make the upgrade as smooth as possible, and we don’t expect the changes to impact most apps.

#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#react-18-3React 18.3 has also been published
To help make the upgrade to React 19 easier, we’ve published a react@18.3 release that is identical to 18.2 but adds warnings for deprecated APIs and other changes that are needed for React 19.

```
react@18.3
```

We recommend upgrading to React 18.3 first to help identify any issues before upgrading to React 19.
For a list of changes in 18.3 see the [Release Notes](https://github.com/facebook/react/blob/main/CHANGELOG.md#1830-april-25-2024).
In this post, we will guide you through the steps for upgrading to React 19:
- [Installing](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#installing)
- [Codemods](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#codemods)
- [Breaking changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#breaking-changes)
- [New deprecations](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#new-deprecations)
- [Notable changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#notable-changes)
- [TypeScript changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#typescript-changes)
- [Changelog](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#changelog)
[Installing](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#installing)
[Codemods](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#codemods)
[Breaking changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#breaking-changes)
[New deprecations](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#new-deprecations)
[Notable changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#notable-changes)
[TypeScript changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#typescript-changes)
[Changelog](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#changelog)
If you’d like to help us test React 19, follow the steps in this upgrade guide and [report any issues](https://github.com/facebook/react/issues/new?assignees=&labels=React+19&projects=&template=19.md&title=%5BReact+19%5D) you encounter. For a list of new features added to React 19, see the [React 19 release post](https://react.dev/blog/2024/12/05/react-19).

#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#new-jsx-transform-is-now-requiredNew JSX Transform is now required
We introduced a [new JSX transform](https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html) in 2020 to improve bundle size and use JSX without importing React. In React 19, we’re adding additional improvements like using ref as a prop and JSX speed improvements that require the new transform.
If the new transform is not enabled, you will see this warning:
[https://react.dev/link/new-jsx-transform](https://react.dev/link/new-jsx-transform)
We expect most apps will not be affected since the transform is enabled in most environments already. For manual instructions on how to upgrade, please see the [announcement post](https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html).
To install the latest version of React and React DOM:

```
npm install --save-exact react@^19.0.0 react-dom@^19.0.0
```


```
npm install --save-exact react@^19.0.0 react-dom@^19.0.0
```

Or, if you’re using Yarn:

```
yarn add --exact react@^19.0.0 react-dom@^19.0.0
```


```
yarn add --exact react@^19.0.0 react-dom@^19.0.0
```

If you’re using TypeScript, you also need to update the types.

```
npm install --save-exact @types/react@^19.0.0 @types/react-dom@^19.0.0
```


```
npm install --save-exact @types/react@^19.0.0 @types/react-dom@^19.0.0
```

Or, if you’re using Yarn:

```
yarn add --exact @types/react@^19.0.0 @types/react-dom@^19.0.0
```


```
yarn add --exact @types/react@^19.0.0 @types/react-dom@^19.0.0
```

We’re also including a codemod for the most common replacements. See [TypeScript changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#typescript-changes) below.

To help with the upgrade, we’ve worked with the team at [codemod.com](https://codemod.com) to publish codemods that will automatically update your code to many of the new APIs and patterns in React 19.
All codemods are available in the [react-codemod repo](https://github.com/reactjs/react-codemod) and the Codemod team have joined in helping maintain the codemods. To run these codemods, we recommend using the codemod command instead of the react-codemod because it runs faster, handles more complex code migrations, and provides better support for TypeScript.

```
react-codemod
```


```
codemod
```


```
react-codemod
```

#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#run-all-react-19-codemodsRun all React 19 codemods
Run all codemods listed in this guide with the React 19 codemod recipe:

```
codemod
```


```
npx codemod@latest react/19/migration-recipe
```


```
npx codemod@latest react/19/migration-recipe
```

This will run the following codemods from react-codemod:

```
react-codemod
```

- [replace-reactdom-render](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-reactdom-render)
- [replace-string-ref](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-string-ref)
- [replace-act-import](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-act-import)
- [replace-use-form-state](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-use-form-state)
- [prop-types-typescript](https://github.com/reactjs/react-codemod#react-proptypes-to-prop-types)
[replace-reactdom-render](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-reactdom-render)

```
replace-reactdom-render
```

[replace-string-ref](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-string-ref)

```
replace-string-ref
```

[replace-act-import](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-act-import)

```
replace-act-import
```

[replace-use-form-state](https://github.com/reactjs/react-codemod?tab=readme-ov-file#replace-use-form-state)

```
replace-use-form-state
```

[prop-types-typescript](https://github.com/reactjs/react-codemod#react-proptypes-to-prop-types)

```
prop-types-typescript
```

This does not include the [TypeScript changes](https://react.dev/blog/2024/04/25/react-19-upgrade-guide#typescript-changes). See TypeScript changes below.
Changes that include a codemod include the command below.
For a list of all available codemods, see the [react-codemod repo](https://github.com/reactjs/react-codemod).

```
react-codemod
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#errors-in-render-are-not-re-thrownErrors in render are not re-thrown
In previous versions of React, errors thrown during render were caught and rethrown. In DEV, we would also log to console.error, resulting in duplicate error logs.

```
console.error
```

In React 19, we’ve [improved how errors are handled](https://react.dev/blog/2024/04/25/react-19#error-handling) to reduce duplication by not re-throwing:
- Uncaught Errors: Errors that are not caught by an Error Boundary are reported to window.reportError.
- Caught Errors: Errors that are caught by an Error Boundary are reported to console.error.

```
window.reportError
```


```
console.error
```

This change should not impact most apps, but if your production error reporting relies on errors being re-thrown, you may need to update your error handling. To support this, we’ve added new methods to createRoot and hydrateRoot for custom error handling:

```
createRoot
```


```
hydrateRoot
```


```
const root = createRoot(container, { onUncaughtError: (error, errorInfo) => { // ... log error report }, onCaughtError: (error, errorInfo) => { // ... log error report }});
```


```
const root = createRoot(container, { onUncaughtError: (error, errorInfo) => { // ... log error report }, onCaughtError: (error, errorInfo) => { // ... log error report }});
```

For more info, see the docs for [createRoot](https://react.dev/reference/react-dom/client/createRoot) and [hydrateRoot](https://react.dev/reference/react-dom/client/hydrateRoot).

```
createRoot
```


```
hydrateRoot
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-deprecated-react-apisRemoved deprecated React APIs
#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-proptypes-and-defaultpropsRemoved: propTypes and defaultProps for functions

```
propTypes
```


```
defaultProps
```

PropTypes were deprecated in [April 2017 (v15.5.0)](https://legacy.reactjs.org/blog/2017/04/07/react-v15.5.0.html#new-deprecation-warnings).

```
PropTypes
```

In React 19, we’re removing the propType checks from the React package, and using them will be silently ignored. If you’re using propTypes, we recommend migrating to TypeScript or another type-checking solution.

```
propType
```


```
propTypes
```

We’re also removing defaultProps from function components in place of ES6 default parameters. Class components will continue to support defaultProps since there is no ES6 alternative.

```
defaultProps
```


```
defaultProps
```


```
// Beforeimport PropTypes from 'prop-types';function Heading({text}) { return <h1>{text}</h1>;}Heading.propTypes = { text: PropTypes.string,};Heading.defaultProps = { text: 'Hello, world!',};
```


```
// Beforeimport PropTypes from 'prop-types';function Heading({text}) { return <h1>{text}</h1>;}Heading.propTypes = { text: PropTypes.string,};Heading.defaultProps = { text: 'Hello, world!',};
```


```
// Afterinterface Props { text?: string;}function Heading({text = 'Hello, world!'}: Props) { return <h1>{text}</h1>;}
```


```
// Afterinterface Props { text?: string;}function Heading({text = 'Hello, world!'}: Props) { return <h1>{text}</h1>;}
```

Codemod propTypes to TypeScript with:

```
propTypes
```


```
npx codemod@latest react/prop-types-typescript
```


```
npx codemod@latest react/prop-types-typescript
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-removing-legacy-contextRemoved: Legacy Context using contextTypes and getChildContext

```
contextTypes
```


```
getChildContext
```

Legacy Context was deprecated in [October 2018 (v16.6.0)](https://legacy.reactjs.org/blog/2018/10/23/react-v-16-6.html).
Legacy Context was only available in class components using the APIs contextTypes and getChildContext, and was replaced with contextType due to subtle bugs that were easy to miss. In React 19, we’re removing Legacy Context to make React slightly smaller and faster.

```
contextTypes
```


```
getChildContext
```


```
contextType
```

If you’re still using Legacy Context in class components, you’ll need to migrate to the new contextType API:

```
contextType
```


```
// Beforeimport PropTypes from 'prop-types';class Parent extends React.Component { static childContextTypes = { foo: PropTypes.string.isRequired, }; getChildContext() { return { foo: 'bar' }; } render() { return <Child />; }}class Child extends React.Component { static contextTypes = { foo: PropTypes.string.isRequired, }; render() { return <div>{this.context.foo}</div>; }}
```


```
// Beforeimport PropTypes from 'prop-types';class Parent extends React.Component { static childContextTypes = { foo: PropTypes.string.isRequired, }; getChildContext() { return { foo: 'bar' }; } render() { return <Child />; }}class Child extends React.Component { static contextTypes = { foo: PropTypes.string.isRequired, }; render() { return <div>{this.context.foo}</div>; }}
```


```
// Afterconst FooContext = React.createContext();class Parent extends React.Component { render() { return ( <FooContext value='bar'> <Child /> </FooContext> ); }}class Child extends React.Component { static contextType = FooContext; render() { return <div>{this.context}</div>; }}
```


```
// Afterconst FooContext = React.createContext();class Parent extends React.Component { render() { return ( <FooContext value='bar'> <Child /> </FooContext> ); }}class Child extends React.Component { static contextType = FooContext; render() { return <div>{this.context}</div>; }}
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-string-refsRemoved: string refs
String refs were deprecated in [March, 2018 (v16.3.0)](https://legacy.reactjs.org/blog/2018/03/27/update-on-async-rendering.html).
Class components supported string refs before being replaced by ref callbacks due to [multiple downsides](https://github.com/facebook/react/issues/1373). In React 19, we’re removing string refs to make React simpler and easier to understand.
If you’re still using string refs in class components, you’ll need to migrate to ref callbacks:

```
// Beforeclass MyComponent extends React.Component { componentDidMount() { this.refs.input.focus(); } render() { return <input ref='input' />; }}
```


```
// Beforeclass MyComponent extends React.Component { componentDidMount() { this.refs.input.focus(); } render() { return <input ref='input' />; }}
```


```
// Afterclass MyComponent extends React.Component { componentDidMount() { this.input.focus(); } render() { return <input ref={input => this.input = input} />; }}
```


```
// Afterclass MyComponent extends React.Component { componentDidMount() { this.input.focus(); } render() { return <input ref={input => this.input = input} />; }}
```

### Note
Codemod string refs with ref callbacks:

```
ref
```


```
npx codemod@latest react/19/replace-string-ref
```


```
npx codemod@latest react/19/replace-string-ref
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-module-pattern-factoriesRemoved: Module pattern factories
Module pattern factories were deprecated in [August 2019 (v16.9.0)](https://legacy.reactjs.org/blog/2019/08/08/react-v16.9.0.html#deprecating-module-pattern-factories).
This pattern was rarely used and supporting it causes React to be slightly larger and slower than necessary. In React 19, we’re removing support for module pattern factories, and you’ll need to migrate to regular functions:

```
// Beforefunction FactoryComponent() { return { render() { return <div />; } }}
```


```
// Beforefunction FactoryComponent() { return { render() { return <div />; } }}
```


```
// Afterfunction FactoryComponent() { return <div />;}
```


```
// Afterfunction FactoryComponent() { return <div />;}
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-createfactoryRemoved: React.createFactory

```
React.createFactory
```

createFactory was deprecated in [February 2020 (v16.13.0)](https://legacy.reactjs.org/blog/2020/02/26/react-v16.13.0.html#deprecating-createfactory).

```
createFactory
```

Using createFactory was common before broad support for JSX, but it’s rarely used today and can be replaced with JSX. In React 19, we’re removing createFactory and you’ll need to migrate to JSX:

```
createFactory
```


```
createFactory
```


```
// Beforeimport { createFactory } from 'react';const button = createFactory('button');
```


```
// Beforeimport { createFactory } from 'react';const button = createFactory('button');
```


```
// Afterconst button = <button />;
```


```
// Afterconst button = <button />;
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-react-test-renderer-shallowRemoved: react-test-renderer/shallow

```
react-test-renderer/shallow
```

In React 18, we updated react-test-renderer/shallow to re-export [react-shallow-renderer](https://github.com/enzymejs/react-shallow-renderer). In React 19, we’re removing react-test-render/shallow to prefer installing the package directly:

```
react-test-renderer/shallow
```


```
react-test-render/shallow
```


```
npm install react-shallow-renderer --save-dev
```


```
npm install react-shallow-renderer --save-dev
```


```
- import ShallowRenderer from 'react-test-renderer/shallow';+ import ShallowRenderer from 'react-shallow-renderer';
```


```
- import ShallowRenderer from 'react-test-renderer/shallow';+ import ShallowRenderer from 'react-shallow-renderer';
```

### Note
##### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#please-reconsider-shallow-renderingPlease reconsider shallow rendering
Shallow rendering depends on React internals and can block you from future upgrades. We recommend migrating your tests to [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro/) or [@testing-library/react-native](https://testing-library.com/docs/react-native-testing-library/intro).

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-deprecated-react-dom-apisRemoved deprecated React DOM APIs
#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-react-dom-test-utilsRemoved: react-dom/test-utils

```
react-dom/test-utils
```

We’ve moved act from react-dom/test-utils to the react package:

```
act
```


```
react-dom/test-utils
```


```
react
```


```
ReactDOMTestUtils.act
```


```
React.act
```


```
act
```


```
react
```


```
react-dom/test-utils
```

[https://react.dev/warnings/react-dom-test-utils](https://react.dev/warnings/react-dom-test-utils)
To fix this warning, you can import act from react:

```
act
```


```
react
```


```
- import {act} from 'react-dom/test-utils'+ import {act} from 'react';
```


```
- import {act} from 'react-dom/test-utils'+ import {act} from 'react';
```

All other test-utils functions have been removed. These utilities were uncommon, and made it too easy to depend on low level implementation details of your components and React. In React 19, these functions will error when called and their exports will be removed in a future version.

```
test-utils
```

See the [warning page](https://react.dev/warnings/react-dom-test-utils) for alternatives.

### Note
Codemod ReactDOMTestUtils.act to React.act:

```
ReactDOMTestUtils.act
```


```
React.act
```


```
npx codemod@latest react/19/replace-act-import
```


```
npx codemod@latest react/19/replace-act-import
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-reactdom-renderRemoved: ReactDOM.render

```
ReactDOM.render
```

ReactDOM.render was deprecated in [March 2022 (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). In React 19, we’re removing ReactDOM.render and you’ll need to migrate to using [ReactDOM.createRoot](https://react.dev/reference/react-dom/client/createRoot):

```
ReactDOM.render
```


```
ReactDOM.render
```


```
ReactDOM.createRoot
```


```
// Beforeimport {render} from 'react-dom';render(<App />, document.getElementById('root'));// Afterimport {createRoot} from 'react-dom/client';const root = createRoot(document.getElementById('root'));root.render(<App />);
```


```
// Beforeimport {render} from 'react-dom';render(<App />, document.getElementById('root'));// Afterimport {createRoot} from 'react-dom/client';const root = createRoot(document.getElementById('root'));root.render(<App />);
```

### Note
Codemod ReactDOM.render to ReactDOMClient.createRoot:

```
ReactDOM.render
```


```
ReactDOMClient.createRoot
```


```
npx codemod@latest react/19/replace-reactdom-render
```


```
npx codemod@latest react/19/replace-reactdom-render
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-reactdom-hydrateRemoved: ReactDOM.hydrate

```
ReactDOM.hydrate
```

ReactDOM.hydrate was deprecated in [March 2022 (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). In React 19, we’re removing ReactDOM.hydrate you’ll need to migrate to using [ReactDOM.hydrateRoot](https://react.dev/reference/react-dom/client/hydrateRoot),

```
ReactDOM.hydrate
```


```
ReactDOM.hydrate
```


```
ReactDOM.hydrateRoot
```


```
// Beforeimport {hydrate} from 'react-dom';hydrate(<App />, document.getElementById('root'));// Afterimport {hydrateRoot} from 'react-dom/client';hydrateRoot(document.getElementById('root'), <App />);
```


```
// Beforeimport {hydrate} from 'react-dom';hydrate(<App />, document.getElementById('root'));// Afterimport {hydrateRoot} from 'react-dom/client';hydrateRoot(document.getElementById('root'), <App />);
```

### Note
Codemod ReactDOM.hydrate to ReactDOMClient.hydrateRoot:

```
ReactDOM.hydrate
```


```
ReactDOMClient.hydrateRoot
```


```
npx codemod@latest react/19/replace-reactdom-render
```


```
npx codemod@latest react/19/replace-reactdom-render
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-unmountcomponentatnodeRemoved: unmountComponentAtNode

```
unmountComponentAtNode
```

ReactDOM.unmountComponentAtNode was deprecated in [March 2022 (v18.0.0)](https://react.dev/blog/2022/03/08/react-18-upgrade-guide). In React 19, you’ll need to migrate to using root.unmount().

```
ReactDOM.unmountComponentAtNode
```


```
root.unmount()
```


```
// BeforeunmountComponentAtNode(document.getElementById('root'));// Afterroot.unmount();
```


```
// BeforeunmountComponentAtNode(document.getElementById('root'));// Afterroot.unmount();
```

For more see root.unmount() for [createRoot](https://react.dev/reference/react-dom/client/createRoot#root-unmount) and [hydrateRoot](https://react.dev/reference/react-dom/client/hydrateRoot#root-unmount).

```
root.unmount()
```


```
createRoot
```


```
hydrateRoot
```

Codemod unmountComponentAtNode to root.unmount:

```
unmountComponentAtNode
```


```
root.unmount
```


```
npx codemod@latest react/19/replace-reactdom-render
```


```
npx codemod@latest react/19/replace-reactdom-render
```


#### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-reactdom-finddomnodeRemoved: ReactDOM.findDOMNode

```
ReactDOM.findDOMNode
```

ReactDOM.findDOMNode was [deprecated in October 2018 (v16.6.0)](https://legacy.reactjs.org/blog/2018/10/23/react-v-16-6.html#deprecations-in-strictmode).

```
ReactDOM.findDOMNode
```

We’re removing findDOMNode because it was a legacy escape hatch that was slow to execute, fragile to refactoring, only returned the first child, and broke abstraction levels (see more [here](https://legacy.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)). You can replace ReactDOM.findDOMNode with [DOM refs](https://react.dev/learn/manipulating-the-dom-with-refs):

```
findDOMNode
```


```
ReactDOM.findDOMNode
```


```
// Beforeimport {findDOMNode} from 'react-dom';function AutoselectingInput() { useEffect(() => { const input = findDOMNode(this); input.select() }, []); return <input defaultValue="Hello" />;}
```


```
// Beforeimport {findDOMNode} from 'react-dom';function AutoselectingInput() { useEffect(() => { const input = findDOMNode(this); input.select() }, []); return <input defaultValue="Hello" />;}
```


```
// Afterfunction AutoselectingInput() { const ref = useRef(null); useEffect(() => { ref.current.select(); }, []); return <input ref={ref} defaultValue="Hello" />}
```


```
// Afterfunction AutoselectingInput() { const ref = useRef(null); useEffect(() => { ref.current.select(); }, []); return <input ref={ref} defaultValue="Hello" />}
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#deprecated-element-refDeprecated: element.ref
```
element.ref
```

React 19 supports [ref as a prop](https://react.dev/blog/2024/04/25/react-19#ref-as-a-prop), so we’re deprecating the element.ref in place of element.props.ref.

```
ref
```


```
element.ref
```


```
element.props.ref
```

Accessing element.ref will warn:

```
element.ref
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#deprecated-react-test-rendererDeprecated: react-test-renderer
```
react-test-renderer
```

We are deprecating react-test-renderer because it implements its own renderer environment that doesn’t match the environment users use, promotes testing implementation details, and relies on introspection of React’s internals.

```
react-test-renderer
```

The test renderer was created before there were more viable testing strategies available like [React Testing Library](https://testing-library.com), and we now recommend using a modern testing library instead.
In React 19, react-test-renderer logs a deprecation warning, and has switched to concurrent rendering. We recommend migrating your tests to [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro/) or [@testing-library/react-native](https://testing-library.com/docs/react-native-testing-library/intro) for a modern and well supported testing experience.

```
react-test-renderer
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#strict-mode-improvementsStrictMode changes
React 19 includes several fixes and improvements to Strict Mode.
When double rendering in Strict Mode in development, useMemo and useCallback will reuse the memoized results from the first render during the second render. Components that are already Strict Mode compatible should not notice a difference in behavior.

```
useMemo
```


```
useCallback
```

As with all Strict Mode behaviors, these features are designed to proactively surface bugs in your components during development so you can fix them before they are shipped to production. For example, during development, Strict Mode will double-invoke ref callback functions on initial mount, to simulate what happens when a mounted component is replaced by a Suspense fallback.

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#improvements-to-suspenseImprovements to Suspense
In React 19, when a component suspends, React will immediately commit the fallback of the nearest Suspense boundary without waiting for the entire sibling tree to render. After the fallback commits, React schedules another render for the suspended siblings to “pre-warm” lazy requests in the rest of the tree:
Previously, when a component suspended, the suspended siblings were rendered and then the fallback was committed.
In React 19, when a component suspends, the fallback is committed and then the suspended siblings are rendered.
This change means Suspense fallbacks display faster, while still warming lazy requests in the suspended tree.

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#umd-builds-removedUMD builds removed
UMD was widely used in the past as a convenient way to load React without a build step. Now, there are modern alternatives for loading modules as scripts in HTML documents. Starting with React 19, React will no longer produce UMD builds to reduce the complexity of its testing and release process.
To load React 19 with a script tag, we recommend using an ESM-based CDN such as [esm.sh](https://esm.sh/).

```
<script type="module"> import React from "https://esm.sh/react@19/?dev" import ReactDOMClient from "https://esm.sh/react-dom@19/client?dev" ...</script>
```


```
<script type="module"> import React from "https://esm.sh/react@19/?dev" import ReactDOMClient from "https://esm.sh/react-dom@19/client?dev" ...</script>
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#libraries-depending-on-react-internals-may-block-upgradesLibraries depending on React internals may block upgrades
This release includes changes to React internals that may impact libraries that ignore our pleas to not use internals like SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED. These changes are necessary to land improvements in React 19, and will not break libraries that follow our guidelines.

```
SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED
```

Based on our [Versioning Policy](https://react.dev/community/versioning-policy#what-counts-as-a-breaking-change), these updates are not listed as breaking changes, and we are not including docs for how to upgrade them. The recommendation is to remove any code that depends on internals.
To reflect the impact of using internals, we have renamed the SECRET_INTERNALS suffix to:

```
SECRET_INTERNALS
```

_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE

```
_DO_NOT_USE_OR_WARN_USERS_THEY_CANNOT_UPGRADE
```

In the future we will more aggressively block accessing internals from React to discourage usage and ensure users are not blocked from upgrading.

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#removed-deprecated-typescript-typesRemoved deprecated TypeScript types
We’ve cleaned up the TypeScript types based on the removed APIs in React 19. Some of the removed have types been moved to more relevant packages, and others are no longer needed to describe React’s behavior.

### Note
We’ve published [types-react-codemod](https://github.com/eps1lon/types-react-codemod/) to migrate most type related breaking changes:

```
types-react-codemod
```


```
npx types-react-codemod@latest preset-19 ./path-to-app
```


```
npx types-react-codemod@latest preset-19 ./path-to-app
```

If you have a lot of unsound access to element.props, you can run this additional codemod:

```
element.props
```


```
npx types-react-codemod@latest react-element-default-any-props ./path-to-your-react-ts-files
```


```
npx types-react-codemod@latest react-element-default-any-props ./path-to-your-react-ts-files
```

Check out [types-react-codemod](https://github.com/eps1lon/types-react-codemod/) for a list of supported replacements. If you feel a codemod is missing, it can be tracked in the [list of missing React 19 codemods](https://github.com/eps1lon/types-react-codemod/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc+label%3A%22React+19%22+label%3Aenhancement).

```
types-react-codemod
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#ref-cleanup-requiredref cleanups required
```
ref
```

This change is included in the react-19 codemod preset as [no-implicit-ref-callback-return](https://github.com/eps1lon/types-react-codemod/#no-implicit-ref-callback-return).

```
react-19
```


```
no-implicit-ref-callback-return
```

Due to the introduction of ref cleanup functions, returning anything else from a ref callback will now be rejected by TypeScript. The fix is usually to stop using implicit returns:

```
- <div ref={current => (instance = current)} />+ <div ref={current => {instance = current}} />
```


```
- <div ref={current => (instance = current)} />+ <div ref={current => {instance = current}} />
```

The original code returned the instance of the HTMLDivElement and TypeScript wouldn’t know if this was supposed to be a cleanup function or not.

```
HTMLDivElement
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#useref-requires-argumentuseRef requires an argument
```
useRef
```

This change is included in the react-19 codemod preset as [refobject-defaults](https://github.com/eps1lon/types-react-codemod/#refobject-defaults).

```
react-19
```


```
refobject-defaults
```

A long-time complaint of how TypeScript and React work has been useRef. We’ve changed the types so that useRef now requires an argument. This significantly simplifies its type signature. It’ll now behave more like createContext.

```
useRef
```


```
useRef
```


```
createContext
```


```
// @ts-expect-error: Expected 1 argument but saw noneuseRef();// PassesuseRef(undefined);// @ts-expect-error: Expected 1 argument but saw nonecreateContext();// PassescreateContext(undefined);
```


```
// @ts-expect-error: Expected 1 argument but saw noneuseRef();// PassesuseRef(undefined);// @ts-expect-error: Expected 1 argument but saw nonecreateContext();// PassescreateContext(undefined);
```

This now also means that all refs are mutable. You’ll no longer hit the issue where you can’t mutate a ref because you initialised it with null:

```
null
```


```
const ref = useRef<number>(null);// Cannot assign to 'current' because it is a read-only propertyref.current = 1;
```


```
const ref = useRef<number>(null);// Cannot assign to 'current' because it is a read-only propertyref.current = 1;
```

MutableRef is now deprecated in favor of a single RefObject type which useRef will always return:

```
MutableRef
```


```
RefObject
```


```
useRef
```


```
interface RefObject<T> { current: T}declare function useRef<T>: RefObject<T>
```


```
interface RefObject<T> { current: T}declare function useRef<T>: RefObject<T>
```

useRef still has a convenience overload for useRef<T>(null) that automatically returns RefObject<T | null>. To ease migration due to the required argument for useRef, a convenience overload for useRef(undefined) was added that automatically returns RefObject<T | undefined>.

```
useRef
```


```
useRef<T>(null)
```


```
RefObject<T | null>
```


```
useRef
```


```
useRef(undefined)
```


```
RefObject<T | undefined>
```

Check out [[RFC] Make all refs mutable](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/64772) for prior discussions about this change.

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#changes-to-the-reactelement-typescript-typeChanges to the ReactElement TypeScript type
```
ReactElement
```

This change is included in the [react-element-default-any-props](https://github.com/eps1lon/types-react-codemod#react-element-default-any-props) codemod.

```
react-element-default-any-props
```

The props of React elements now default to unknown instead of any if the element is typed as ReactElement. This does not affect you if you pass a type argument to ReactElement:

```
props
```


```
unknown
```


```
any
```


```
ReactElement
```


```
ReactElement
```


```
type Example2 = ReactElement<{ id: string }>["props"];// ^? { id: string }
```


```
type Example2 = ReactElement<{ id: string }>["props"];// ^? { id: string }
```

But if you relied on the default, you now have to handle unknown:

```
unknown
```


```
type Example = ReactElement["props"];// ^? Before, was 'any', now 'unknown'
```


```
type Example = ReactElement["props"];// ^? Before, was 'any', now 'unknown'
```

You should only need it if you have a lot of legacy code relying on unsound access of element props. Element introspection only exists as an escape hatch, and you should make it explicit that your props access is unsound via an explicit any.

```
any
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#the-jsx-namespace-in-typescriptThe JSX namespace in TypeScript
This change is included in the react-19 codemod preset as [scoped-jsx](https://github.com/eps1lon/types-react-codemod#scoped-jsx)

```
react-19
```


```
scoped-jsx
```

A long-time request is to remove the global JSX namespace from our types in favor of React.JSX. This helps prevent pollution of global types which prevents conflicts between different UI libraries that leverage JSX.

```
JSX
```


```
React.JSX
```

You’ll now need to wrap module augmentation of the JSX namespace in `declare module ”…”:

```
// global.d.ts+ declare module "react" { namespace JSX { interface IntrinsicElements { "my-element": { myElementProps: string; }; } }+ }
```


```
// global.d.ts+ declare module "react" { namespace JSX { interface IntrinsicElements { "my-element": { myElementProps: string; }; } }+ }
```

The exact module specifier depends on the JSX runtime you specified in the compilerOptions of your tsconfig.json:

```
compilerOptions
```


```
tsconfig.json
```

- For "jsx": "react-jsx" it would be react/jsx-runtime.
- For "jsx": "react-jsxdev" it would be react/jsx-dev-runtime.
- For "jsx": "react" and "jsx": "preserve" it would be react.

```
"jsx": "react-jsx"
```


```
react/jsx-runtime
```


```
"jsx": "react-jsxdev"
```


```
react/jsx-dev-runtime
```


```
"jsx": "react"
```


```
"jsx": "preserve"
```


```
react
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#better-usereducer-typingsBetter useReducer typings
```
useReducer
```

useReducer now has improved type inference thanks to [@mfp22](https://github.com/mfp22).

```
useReducer
```

However, this required a breaking change where useReducer doesn’t accept the full reducer type as a type parameter but instead either needs none (and rely on contextual typing) or needs both the state and action type.

```
useReducer
```

The new best practice is not to pass type arguments to useReducer.

```
useReducer
```


```
- useReducer<React.Reducer<State, Action>>(reducer)+ useReducer(reducer)
```


```
- useReducer<React.Reducer<State, Action>>(reducer)+ useReducer(reducer)
```

This may not work in edge cases where you can explicitly type the state and action, by passing in the Action in a tuple:

```
Action
```


```
- useReducer<React.Reducer<State, Action>>(reducer)+ useReducer<State, [Action]>(reducer)
```


```
- useReducer<React.Reducer<State, Action>>(reducer)+ useReducer<State, [Action]>(reducer)
```

If you define the reducer inline, we encourage to annotate the function parameters instead:

```
- useReducer<React.Reducer<State, Action>>((state, action) => state)+ useReducer((state: State, action: Action) => state)
```


```
- useReducer<React.Reducer<State, Action>>((state, action) => state)+ useReducer((state: State, action: Action) => state)
```

This is also what you’d also have to do if you move the reducer outside of the useReducer call:

```
useReducer
```


```
const reducer = (state: State, action: Action) => state;
```


```
const reducer = (state: State, action: Action) => state;
```

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#other-breaking-changesOther breaking changes
- react-dom: Error for javascript URLs in src and href [#26507](https://github.com/facebook/react/pull/26507)
- react-dom: Remove errorInfo.digest from onRecoverableError [#28222](https://github.com/facebook/react/pull/28222)
- react-dom: Remove unstable_flushControlled [#26397](https://github.com/facebook/react/pull/26397)
- react-dom: Remove unstable_createEventHandle [#28271](https://github.com/facebook/react/pull/28271)
- react-dom: Remove unstable_renderSubtreeIntoContainer [#28271](https://github.com/facebook/react/pull/28271)
- react-dom: Remove unstable_runWithPriority [#28271](https://github.com/facebook/react/pull/28271)
- react-is: Remove deprecated methods from react-is [28224](https://github.com/facebook/react/pull/28224)

```
src
```


```
href
```

[#26507](https://github.com/facebook/react/pull/26507)

```
errorInfo.digest
```


```
onRecoverableError
```

[#28222](https://github.com/facebook/react/pull/28222)

```
unstable_flushControlled
```

[#26397](https://github.com/facebook/react/pull/26397)

```
unstable_createEventHandle
```

[#28271](https://github.com/facebook/react/pull/28271)

```
unstable_renderSubtreeIntoContainer
```

[#28271](https://github.com/facebook/react/pull/28271)

```
unstable_runWithPriority
```

[#28271](https://github.com/facebook/react/pull/28271)

```
react-is
```

[28224](https://github.com/facebook/react/pull/28224)

### https://react.dev/blog/2024/04/25/react-19-upgrade-guide#other-notable-changesOther notable changes
- react: Batch sync, default and continuous lanes [#25700](https://github.com/facebook/react/pull/25700)
- react: Don’t prerender siblings of suspended component [#26380](https://github.com/facebook/react/pull/26380)
- react: Detect infinite update loops caused by render phase updates [#26625](https://github.com/facebook/react/pull/26625)
- react-dom: Transitions in popstate are now synchronous [#26025](https://github.com/facebook/react/pull/26025)
- react-dom: Remove layout effect warning during SSR [#26395](https://github.com/facebook/react/pull/26395)
- react-dom: Warn and don’t set empty string for src/href (except anchor tags) [#28124](https://github.com/facebook/react/pull/28124)
[#25700](https://github.com/facebook/react/pull/25700)
[#26380](https://github.com/facebook/react/pull/26380)
[#26625](https://github.com/facebook/react/pull/26625)
[#26025](https://github.com/facebook/react/pull/26025)
[#26395](https://github.com/facebook/react/pull/26395)
[#28124](https://github.com/facebook/react/pull/28124)
For a full list of changes, please see the [Changelog](https://github.com/facebook/react/blob/main/CHANGELOG.md#1900-december-5-2024).
Thanks to [Andrew Clark](https://twitter.com/acdlite), [Eli White](https://twitter.com/Eli_White), [Jack Pope](https://github.com/jackpope), [Jan Kassens](https://github.com/kassens), [Josh Story](https://twitter.com/joshcstory), [Matt Carroll](https://twitter.com/mattcarrollcode), [Noah Lemen](https://twitter.com/noahlemen), [Sophie Alpert](https://twitter.com/sophiebits), and [Sebastian Silbermann](https://twitter.com/sebsilbermann) for reviewing and editing this post.
[PreviousReact 19 RC](https://react.dev/blog/2024/04/25/react-19)
[NextReact Labs: What We've Been Working On – February 2024](https://react.dev/blog/2024/02/15/react-labs-what-we-have-been-working-on-february-2024)
[@sawaratsuki1004](https://twitter.com/sawaratsuki1004)
[Learn React](https://react.dev/learn)
[Quick Start](https://react.dev/learn)
[Installation](https://react.dev/learn/installation)
[Describing the UI](https://react.dev/learn/describing-the-ui)
[Adding Interactivity](https://react.dev/learn/adding-interactivity)
[Managing State](https://react.dev/learn/managing-state)
[Escape Hatches](https://react.dev/learn/escape-hatches)
[API Reference](https://react.dev/reference/react)
[React APIs](https://react.dev/reference/react)
[React DOM APIs](https://react.dev/reference/react-dom)
[Community](https://react.dev/community)
[Code of Conduct](https://github.com/facebook/react/blob/main/CODE_OF_CONDUCT.md)
[Meet the Team](https://react.dev/community/team)
[Docs Contributors](https://react.dev/community/docs-contributors)
[Acknowledgements](https://react.dev/community/acknowledgements)
[Blog](https://react.dev/blog)
[React Native](https://reactnative.dev/)
[Privacy](https://opensource.facebook.com/legal/privacy)
[Terms](https://opensource.fb.com/legal/terms/)

