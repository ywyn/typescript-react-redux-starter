# TypeScript React Redux Starter

这篇快速上手指南会教你如何将 TypeScript 与 React 结合起来使用。 在最后，你将学到：

- 使用 TypeScript 和 React 创建工程
- 使用[TSLint](https://github.com/palantir/tslint)进行代码检查
- 使用[Jest](https://facebook.github.io/jest/) 和 [Enzyme](http://airbnb.io/enzyme/)进行测试
- 使用[Redux](https://github.com/reactjs/react-redux)状态管理

我们会使用 [create-react-app](https://github.com/facebookincubator/create-react-app) 工具快速搭建工程环境.

这里假设你已经在使用[Node.js](https://nodejs.org/) 和 [npm](https://www.npmjs.com/).
并且已经了解了[React 的基础知识](https://facebook.github.io/react/docs/hello-world.html).

## 安装 create-react-app

我们之所以使用 create-react-app 是因为它能够为 React 工程设置一些有效的工具和权威的默认参数。 它仅仅是一个用来搭建 React 工程的命令行工具而已。

```shell
npm install -g create-react-app
```

## 创建新项目

让我们首先创建一个叫做 `my-app`的新项目

```shell
create-react-app my-app --scripts-version=react-scripts-ts
```

[react-scripts-ts](https://www.npmjs.com/package/react-scripts-ts)是一系列适配器，它利用标准的 create-react-app 工程管道并把 TypeScript 混入进来。

此时，您的项目结构应如下所示：

```text
my-app/
├─ .gitignore
├─ images.d.ts
├─ node_modules/
├─ public/
├─ src/
│  └─ ...
├─ package.json
├─ tsconfig.json
├─ tsconfig.prod.json
├─ tsconfig.test.json
└─ tslint.json
```

注意:

- `tsconfig.json` 包含了项目里 TypeScript 特定的选项。
  - 如果要对我们的生产版本或测试版本进行任何调整，我们也有一个`tsconfig.prod.json` 和 一个 `tsconfig.test.json`。
- `tslint.json` 保存了要使用的代码检查器的设置，[TSLint](https://github.com/palantir/tslint)。
- `package.json` 包含了依赖，还有一些命令的快捷方式，如测试命令，预览命令和发布应用的命令。
- `public` 包含了静态资源如 HTML 页面或图片。除了 index.html 文件外，其它的文件都可以删除。
- `src` 包含了 TypeScript 和 CSS 源码。 `index.tsx` 是默认使用的入口文件。
- `images.d.ts` 会告诉 TypeScript 可以对某些类型的图像文件进行 import-ed，create-react-app 支持。

## 修改 TSlint 规则

```diff
 {
   "extends": ["tslint:recommended", "tslint-react", "tslint-config-prettier"],
   "linterOptions": {
     "exclude": [
       "config/**/*.js",
       "node_modules/**/*.ts"
     ]
   },
    "rules": {
      "interface-name": [true, "never-prefix"],
      "ordered-imports": false,
      "object-literal-sort-keys": false
    }
 }
```

[Configuring TSLint](https://palantir.github.io/tslint/usage/configuration/)超出了本入门指南的范围，但是您应该随意尝试一些适合自己的东西。

## 运行项目

通过下面的方式即可轻松地运行这个工程。

```sh
npm run start
```

它会执行`package.json`里面指定的`start`命令，并且会启动一个服务器，当我们保存文件时还会自动刷新页面。 通常这个服务器的地址是`http://localhost:3000`，页面应用会被自动地打开。

它会保持监听以方便我们快速地预览改动。

## 测试项目

测试也仅仅是一行命令的事儿：

```sh
npm run test
```

这个命令会运行 Jest，一个非常好用的测试工具，它会运行所有扩展名是`.test.ts`或`.spec.ts`的文件。 好比是`npm run start`命令，当检测到有改动的时候 Jest 会自动地运行。 如果喜欢的话，你还可以同时运行`npm run start`和`npm run test`，这样你就可以在预览的同时进行测试。

## 生成生产环境的构建版本

在使用 npm run start 运行工程的时候，我们并没有生成一个优化过的版本。 通常我们想给用户一个运行的尽可能快并在体积上尽可能小的代码。 像压缩这样的优化方法可以做到这一点，但是总是要耗费更多的时间。 我们把这样的构建版本称做“生产环境”版本（与开发版本相对）。

要执行生产环境的构建，可以运行如下命令：

```sh
npm run build
```

This will create an optimized JS and CSS build in `./build/static/js` and `./build/static/css` respectively.

这会相应地创建优化过的 JS 和 CSS 文件，`./build/static/js`和`./build/static/css`。
大多数情况下你不需要生成生产环境的构建版本， 但它可以帮助你衡量应用最终版本的体积大小。

## 创建一个组件

下面我们将要创建一个 Hello 组件。 这个组件接收任意一个我们想对之打招呼的名字（我们把它叫做`name`），并且有一个可选数量的感叹号做为结尾（通过`enthusiasmLevel`）。

若我们这样写`<Hello name="Daniel" enthusiasmLevel={3} />`，这个组件大至会渲染成`<div>Hello Daniel!!!</div>`。 如果没指定`enthusiasmLevel`，组件将默认显示一个感叹号。 若`enthusiasmLevel`为`0`或负值将抛出一个错误。

下面来写一下`Hello.tsx`:

```ts
// src/components/Hello.tsx

import * as React from 'react'

export interface Props {
  name: string
  enthusiasmLevel?: number
}

function Hello({ name, enthusiasmLevel = 1 }: Props) {
  if (enthusiasmLevel <= 0) {
    throw new Error('You could be a little more enthusiastic. :D')
  }

  return (
    <div className="hello">
      <div className="greeting">
        Hello {name + getExclamationMarks(enthusiasmLevel)}
      </div>
    </div>
  )
}

export default Hello

// helpers

function getExclamationMarks(numChars: number) {
  return Array(numChars + 1).join('!')
}
```

注意我们定义了一个类型`Props`，它指定了我们组件要用到的属性。 `name`是必需的且为`string`类型，同时`enthusiasmLevel`是可选的且为`number`类型（你可以通过名字后面加`?`为指定可选参数）。
我们创建了一个无状态的函数式组件（`Stateless Functional Components，SFC）Hello`。 具体来讲，`Hello`是一个函数，接收一个`Props`对象并拆解它。 如果`Props`对象里没有设置`enthusiasmLevel`，默认值为`1`。

使用函数是 React 中定义组件的[两种方式](<(https://facebook.github.io/react/docs/components-and-props.html#functional-and-class-components)>).
如果你喜欢的话，也可以通过类的方式定义：

```ts
class Hello extends React.Component<Props, object> {
  render() {
    const { name, enthusiasmLevel = 1 } = this.props

    if (enthusiasmLevel <= 0) {
      throw new Error('You could be a little more enthusiastic. :D')
    }

    return (
      <div className="hello">
        <div className="greeting">
          Hello {name + getExclamationMarks(enthusiasmLevel)}
        </div>
      </div>
    )
  }
}
```

[当我们的组件具有某些状态的时候](https://facebook.github.io/react/docs/state-and-lifecycle.html)，
使用类的方式是很有用处的。 但在这个例子里我们不需要考虑状态 - 事实上，在`React.Component<Props, object>`我们把状态指定为了`object`，因此使用 SFC 更简洁。 当在创建可重用的通用 UI 组件的时候，在表现层使用组件局部状态比较适合。 针对我们应用的生命周期，我们会审视应用是如何通过 Redux 轻松地管理普通状态的。

现在我们已经写好了组件，让我们仔细看看 index.tsx，把`<App />`替换成`<Hello ... />`。

首先我们在文件头部导入它：

```ts
import Hello from './components/Hello'
```

然后修改`render`调用：

```ts
ReactDOM.render(
  <Hello name="TypeScript" enthusiasmLevel={10} />,
  document.getElementById('root') as HTMLElement
)
```

## 类型断言

这里还有一点要指出，就是最后一行`document.getElementById('root') as HTMLElement`。 这个语法叫做类型断言，有时也叫做转换。 当你比类型检查器更清楚一个表达式的类型的时候，你可以通过这种方式通知`TypeScript`。
这里，我们之所以这么做是因为`getElementById`的返回值类型是`HTMLElement | null`。 简单地说，`getElementById`返回`null`是当无法找对对应`id`元素的时候。 我们假设`getElementById`总是成功的，因此我们要使用 as 语法告诉`TypeScript这点`。

`TypeScript`还有一种感叹号（`!`）结尾的语法，它会从前面的表达式里移除`null`和`undefined`。 所以我们也可以写成`document.getElementById('root')!`，但在这里我们想写的更清楚些。

## 状态组件

前面我们提到过，我们的组件不需要状态。如果我们希望能够随着时间的推移基于用户交互来更新组件，该怎么办？在这一点上，状态变得更加重要。

深入了解`React`中有关组件状态的最佳实践超出了本入门指南的范围，但是让我们快速浏览一下组件的有状态版本，`Hello`以了解添加状态的外观。我们将渲染两个`<button>`，以更新`Hello`组件显示的感叹号的数量。

为此，我们要

1. 定义个状态类型（例如 this.state）
1. `this.state`根据构造函数中给出的道具进行初始化。
1. 为我们的按钮（`onIncrement`和`onDecrement`）创建两个事件处理程序。

```ts
// src/components/StatefulHello.tsx

import * as React from 'react'

export interface Props {
  name: string
  enthusiasmLevel?: number
}

interface State {
  currentEnthusiasm: number
}

class Hello extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props)
    this.state = { currentEnthusiasm: props.enthusiasmLevel || 1 }
  }

  onIncrement = () => this.updateEnthusiasm(this.state.currentEnthusiasm + 1)
  onDecrement = () => this.updateEnthusiasm(this.state.currentEnthusiasm - 1)

  render() {
    const { name } = this.props

    if (this.state.currentEnthusiasm <= 0) {
      throw new Error('You could be a little more enthusiastic. :D')
    }

    return (
      <div className="hello">
        <div className="greeting">
          Hello {name + getExclamationMarks(this.state.currentEnthusiasm)}
        </div>
        <button onClick={this.onDecrement}>-</button>
        <button onClick={this.onIncrement}>+</button>
      </div>
    )
  }

  updateEnthusiasm(currentEnthusiasm: number) {
    this.setState({ currentEnthusiasm })
  }
}

export default Hello

function getExclamationMarks(numChars: number) {
  return Array(numChars + 1).join('!')
}
```

在此启动程序中，我们将不再使用此有状态组件。有状态组件非常适合用于创建仅专注于呈现内容的组件（而不是处理核心应用程序状态）。在某些情况下，它可以用于处理整个应用程序的状态，其中一个中央组件可以传递可以`setState`适当调用的功能。但是，对于更大的应用程序，最好使用专用的状态管理器（我们将在下面讨论）。

## 增加样式

通过我们的设置为一个组件添加样式很容易。 若要设置 Hello 组件的样式，我们可以创建这样一个 CSS 文件`src/components/Hello.css`。

```css
.hello {
  text-align: center;
  margin: 20px;
  font-size: 48px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.hello button {
  margin-left: 25px;
  margin-right: 25px;
  font-size: 40px;
  min-width: 50px;
}
```

`create-react-app` 包含的工具（`Webpack`和一些加载器）允许我们导入样式表文件。 当我们构建应用的时候，所有导入的`.css`文件会被拼接成一个输出文件。 因此在`src/components/Hello.tsx`，我们需要添加如下导入语句。

```ts
import './Hello.css'
```

## 使用 Jest 编写测试

如果你没使用过 Jest，你可能先要把它安装为开发依赖项。

```sh
npm install -D jest jest-cli jest-config
```

我们对 Hello 组件有一些假设。 让我们在此重申一下：

> - 当这样写`<Hello name="Daniel" enthusiasmLevel={3} />`时，组件应被渲染成`<div>Hello Daniel!!!</div>`。something like `<div>Hello Daniel!!!</div>`.
> - 若未指定`enthusiasmLevel`，组件应默认显示一个感叹号。
> - 若`enthusiasmLevel`为`0`或负值，它应抛出一个错误。

我们将针对这些需求为组件写一些注释。
但首先，我们要安装 Enzyme。 [Enzyme](https://airbnb.io/enzyme/)是 React 生态系统里一个通用工具，它方便了针对组件的行为编写测试。 默认地，我们的应用包含了一个叫做`jsdom`的库，它允许我们模拟`DOM`以及在非浏览器的环境下测试运行时的行为。 `Enzyme`与此类似，但是是基于`jsdom`的，并且方便我们查询组件。

```sh
npm install -D enzyme @types/enzyme enzyme-adapter-react-16 @types/enzyme-adapter-react-16 react-test-renderer
```

注意我们同时安装了`enzyme`和`@types/enzyme`。 `enzyme`包指的是包含了实际运行的`JavaScript`代码包，而`@types/enzyme`则包含了声明文件（`.d.ts`文件）的包，以便`TypeScript`能够了解该如何使用`Enzyme`。 你可以在[这里](https://www.typescriptlang.org/docs/handbook/declaration-files/consumption.html)了解更多关于`@types`包的信息。

我们还需要安装`enzyme-adapter`和`react-addons-test-utils`。 它们是使用`enzyme`所需要安装的包，前者作为配置适配器是必须的，而后者若采用的`React`版本在`15.5.0`之上则毋需安装。

现在我们已经设置好了`Enzyme`，下面开始编写测试！ 先创建一个文件`src/components/Hello.test.tsx`，与先前的`Hello.tsx`文件放在一起。

```ts
// src/components/Hello.test.tsx

import * as React from 'react'
import * as enzyme from 'enzyme'
import Hello from './Hello'

it('renders the correct text when no enthusiasm level is given', () => {
  const hello = enzyme.shallow(<Hello name="Daniel" />)
  expect(hello.find('.greeting').text()).toEqual('Hello Daniel!')
})

it('renders the correct text with an explicit enthusiasm of 1', () => {
  const hello = enzyme.shallow(<Hello name="Daniel" enthusiasmLevel={1} />)
  expect(hello.find('.greeting').text()).toEqual('Hello Daniel!')
})

it('renders the correct text with an explicit enthusiasm level of 5', () => {
  const hello = enzyme.shallow(<Hello name="Daniel" enthusiasmLevel={5} />)
  expect(hello.find('.greeting').text()).toEqual('Hello Daniel!!!!!')
})

it('throws when the enthusiasm level is 0', () => {
  expect(() => {
    enzyme.shallow(<Hello name="Daniel" enthusiasmLevel={0} />)
  }).toThrow()
})

it('throws when the enthusiasm level is negative', () => {
  expect(() => {
    enzyme.shallow(<Hello name="Daniel" enthusiasmLevel={-1} />)
  }).toThrow()
})
```

这些测试都十分基础，但你可以从中得到启发。

## 添加 state 管理

到此为止，如果你使用 React 的目的是只获取一次数据并显示，那么你已经完成了。 但是如果你想开发一个可以交互的应用，那么你需要添加 state 管理。

## state 管理概述

React 本身就是一个适合于创建可组合型视图的库。 但是，React 并没有任何在应用间同步数据的功能。 就 React 组件而言，数据是通过每个元素上指定的 props 向子元素传递。

因为 React 本身并没有提供内置的 state 管理功能，React 社区选择了 Redux 和 MobX 库。

[Redux](https://redux.js.org/)依靠一个统一且不可变的数据存储来同步数据，并且更新那里的数据时会触发应用的更新渲染。 state 的更新是以一种不可变的方式进行，它会发布一条明确的 action 消息，这个消息必须被 reducer 函数处理。 由于使用了这样明确的方式，很容易弄清楚一个 action 是如何影响程序的 state。

[MobX](https://mobx.js.org/)借助于函数式响应型模式，state 被包装在了可观察对象里，并通过 props 传递。 通过将 state 标记为可观察的，即可在所有观察者之间保持 state 的同步性。 另一个好处是，这个库已经使用 TypeScript 实现了。

这两者各有优缺点。 但 Redux 使用得更广泛，因此在这篇教程里，我们主要看如何使用 Redux； 但是也鼓励大家两者都去了解一下。
后面的小节学习曲线比较陡。 因此强烈建议大家先去熟悉一下[Redux](https://redux.js.org/)。

## 设置 actions

只有当应用里的 state 会改变的时候，我们才需要去添加 Redux。 我们需要一个 action 的来源，它将触发改变。 它可以是一个定时器或者 UI 上的一个按钮。
为此，我们将增加两个按钮来控制`Hello`组件的感叹级别。

## 安装 Redux

安装`redux`和`react-redux`以及它们的类型文件做为依赖。

```sh
npm install -S redux react-redux @types/react-redux
```

这里我们不需要安装`@types/redux`，因为`Redux`已经自带了声明文件（`.d.ts`文件）。

## 定义应用的状态

我们需要定义 Redux 保存的 state 的结构。 创建 `src/types/index.tsx`文件，它保存了类型的定义，我们在整个程序里都可能用到。

```ts
// src/types/index.tsx

export interface StoreState {
  languageName: string
  enthusiasmLevel: number
}
```

这里我们想让`languageName`表示应用使用的编程语言（例如，TypeScript 或者 JavaScript），`enthusiasmLevel`是可变的。 在写我们的第一个容器的时候，就会明白为什么要令 state 与 props 稍有不同。

## 添加 actions

下面我们创建这个应用将要响应的消息类型， `src/constants/index.tsx`.

```ts
// src/constants/index.tsx

export const INCREMENT_ENTHUSIASM = 'INCREMENT_ENTHUSIASM'
export type INCREMENT_ENTHUSIASM = typeof INCREMENT_ENTHUSIASM

export const DECREMENT_ENTHUSIASM = 'DECREMENT_ENTHUSIASM'
export type DECREMENT_ENTHUSIASM = typeof DECREMENT_ENTHUSIASM
```

这里的`const/type`模式允许我们以容易访问和重构的方式使用 TypeScript 的字符串字面量类型。

接下来，我们创建一些 actions 以及创建这些 actions 的函数，`src/actions/index.tsx`。

```ts
import * as constants from '../constants'

export interface IncrementEnthusiasm {
  type: constants.INCREMENT_ENTHUSIASM
}

export interface DecrementEnthusiasm {
  type: constants.DECREMENT_ENTHUSIASM
}

export type EnthusiasmAction = IncrementEnthusiasm | DecrementEnthusiasm

export function incrementEnthusiasm(): IncrementEnthusiasm {
  return {
    type: constants.INCREMENT_ENTHUSIASM
  }
}

export function decrementEnthusiasm(): DecrementEnthusiasm {
  return {
    type: constants.DECREMENT_ENTHUSIASM
  }
}
```

我们创建了两个类型，它们负责增加操作和减少操作的行为。 我们还定义了一个类型（EnthusiasmAction），它描述了哪些 action 是可以增加或减少的。 最后，我们定义了两个函数用来创建实际的 actions。

这里有一些清晰的模版，你可以参考类似 [redux-actions](https://www.npmjs.com/package/redux-actions) 的库。

## 添加 reducer

现在我们可以开始写第一个 reducer 了！ Reducers 是函数，它们负责生成应用 state 的拷贝使之产生变化，但它并没有副作用。 它们是一种[纯函数](https://en.wikipedia.org/wiki/Pure_function)。

我们的 reducer 将放在 src/reducers/index.tsx 文件里。 它的功能是保证增加操作会让感叹级别加 1，减少操作则要将感叹级别减 1，但是这个级别永远不能小于 1。

```ts
// src/reducers/index.tsx

import { EnthusiasmAction } from '../actions'
import { StoreState } from '../types/index'
import { INCREMENT_ENTHUSIASM, DECREMENT_ENTHUSIASM } from '../constants/index'

export function enthusiasm(
  state: StoreState,
  action: EnthusiasmAction
): StoreState {
  switch (action.type) {
    case INCREMENT_ENTHUSIASM:
      return { ...state, enthusiasmLevel: state.enthusiasmLevel + 1 }
    case DECREMENT_ENTHUSIASM:
      return {
        ...state,
        enthusiasmLevel: Math.max(1, state.enthusiasmLevel - 1)
      }
  }
  return state
}
```

注意我们使用了对象展开（...state），当替换 enthusiasmLevel 时，它可以对状态进行浅拷贝。 将 enthusiasmLevel 属性放在末尾是十分关键的，否则它将被旧的状态覆盖。
你可能想要对 reducer 写一些测试。 因为 reducers 是纯函数，它们可以传入任意的数据。 针对每个输入，可以测试 reducers 生成的新的状态。 可以考虑使用 Jest 的[toEqual](https://facebook.github.io/jest/docs/expect.html#toequalvalue)方法。

## 创建容器

在使用 Redux 时，我们常常要创建组件和容器。 组件是数据无关的，且工作在表现层。 容器通常包裹组件及其使用的数据，用以显示和修改状态。 你可以在这里阅读更多关于这个概念的细节： [Dan Abramov 写的表现层的容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0).

现在我们修改`src/components/Hello.tsx`，让它可以修改状态。 我们将添加两个可选的回调属性到`Props`，它们分别是`onIncrement`和`onDecrement`：

```ts
export interface Props {
  name: string
  enthusiasmLevel?: number
  onIncrement?: () => void
  onDecrement?: () => void
}
```

然后将这两个回调绑定到两个新按钮上，将按钮添加到我们的组件里。

```ts
function Hello({ name, enthusiasmLevel = 1, onIncrement, onDecrement }: Props) {
  if (enthusiasmLevel <= 0) {
    throw new Error('You could be a little more enthusiastic. :D')
  }

  return (
    <div className="hello">
      <div className="greeting">
        Hello {name + getExclamationMarks(enthusiasmLevel)}
      </div>
      <div>
        <button onClick={onDecrement}>-</button>
        <button onClick={onIncrement}>+</button>
      </div>
    </div>
  )
}
```

通常情况下，我们应该给`onIncrement`和`onDecrement`写一些测试，它们是在各自的按钮被点击时调用。 试一试以便掌握编写测试的窍门。

现在我们的组件更新好了，可以把它放在一个容器里了。 让我们来创建一个文件`src/containers/Hello.tsx`，在开始的地方使用下列导入语句。

```ts
import Hello from '../components/Hello'
import * as actions from '../actions/'
import { StoreState } from '../types/index'
import { connect } from 'react-redux'
import { Dispatch } from 'redux'
```

两个关键点是初始的`Hello`组件和 react-redux 的 connect 函数。 `connect`可以将我们的`Hello`组件转换成一个容器，通过以下两个函数：

- `mapStateToProps` 将当前 store 里的数据以我们的组件需要的形式传递到组件。
- `mapDispatchToProps` 利用`dispatch`函数，创建回调 props 将 actions 送到 store。

回想一下，我们的应用包含两个属性：`languageName`和`enthusiasmLevel`。 我们的`Hello`组件，希望得到一个`name`和一个`enthusiasmLevel`。 `mapStateToProps`会从`store`得到相应的数据，如果需要的话将针对组件的`props`调整它。 下面让我们继续往下写。

```ts
export function mapStateToProps({ enthusiasmLevel, languageName }: StoreState) {
  return {
    enthusiasmLevel,
    name: languageName
  }
}
```

注意`mapStateToProps`仅创建了`Hello`组件需要的四个属性中的两个。 我们还想要传入`onIncrement`和`onDecrement`回调函数。 `mapDispatchToProps`是一个函数，它需要传入一个调度函数。 这个调度函数可以将`actions`传入`store`来触发更新，因此我们可以创建一对回调函数，它们会在需要的时候调用调度函数。

```ts
export function mapDispatchToProps(
  dispatch: Dispatch<actions.EnthusiasmAction>
) {
  return {
    onIncrement: () => dispatch(actions.incrementEnthusiasm()),
    onDecrement: () => dispatch(actions.decrementEnthusiasm())
  }
}
```

最后，我们可以调用`connect`了。 `connect`首先会接收`mapStateToProps`和`mapDispatchToProps`，然后返回另一个函数，我们用它来包裹我们的组件。 最终的容器是通过下面的代码定义的：

```ts
export default connect(mapStateToProps, mapDispatchToProps)(Hello)
```

现在，我们的文件应该是下面这个样子：

```ts
// src/containers/Hello.tsx

import Hello from '../components/Hello'
import * as actions from '../actions/'
import { StoreState } from '../types/index'
import { connect } from 'react-redux'
import { Dispatch } from 'redux'

export function mapStateToProps({ enthusiasmLevel, languageName }: StoreState) {
  return {
    enthusiasmLevel,
    name: languageName
  }
}

export function mapDispatchToProps(
  dispatch: Dispatch<actions.EnthusiasmAction>
) {
  return {
    onIncrement: () => dispatch(actions.incrementEnthusiasm()),
    onDecrement: () => dispatch(actions.decrementEnthusiasm())
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(Hello)
```

## 创建 store

让我们回到`src/index.tsx`。 要把所有的东西合到一起，我们需要创建一个带初始状态的`store`，并用我们所有的`reducers`来设置它。

```ts
import { createStore } from 'redux'
import { enthusiasm } from './reducers/index'

const store = createStore(enthusiasm, {
  enthusiasmLevel: 1,
  languageName: 'TypeScript'
})
```

`store`可能正如你想的那样，它是我们应用全局状态的核心`store`。

接下来，我们将要用`./src/containers/Hello`来包裹`./src/components/Hello`，然后使用`react-redux`的`Provider`将 props 与容器连通起来。 我们将导入它们：

```ts
import Hello from './containers/Hello'
import { Provider } from 'react-redux'
```

将`store`以`Provider`的属性形式传入：

```ts
ReactDOM.render(
  <Provider store={store}>
    <Hello />
  </Provider>,
  document.getElementById('root') as HTMLElement
)
```

注意，`Hello`不再需要`props`了，因为我们使用了`connect`函数为包裹起来的`Hello`组件的`props`适配了应用的状态。

## 退出

如果你发现 create-react-app 使一些自定义设置变得困难，那么你就可以选择不使用它，使用你需要配置。 比如，你要添加一个 Webpack 插件，你就可以利用 create-react-app 提供的“eject”功能。

运行：

```sh
npm run eject
```

这样就可以了！

你要注意，在运行 eject 前最好保存你的代码。 你不能撤销 eject 命令，因此退出操作是永久性的除非你从一个运行 eject 前的提交来恢复工程。

## 下一步

create-react-app 带有很多很棒的功能。 它们的大多数都在我们工程生成的 README.md 里面有记录，所以可以简单阅读一下。

如果你想学习更多关于 Redux 的知识，你可以前往[官方站点](https://redux.js.org/)查看文档。 同样的，[MobX](https://mobx.js.org/README.html)官方站点。

如果你想要在某个时间点 eject，你需要了解再多关于[Webpack](https://webpack.js.org/)的知识。

有时候你需要路由功能。 已经有一些解决方案了，但是对于 Redux 工程来讲[react-router](https://github.com/ReactTraining/react-router)是最流行的，并经常与[react-router-redux](https://github.com/reactjs/react-router-redux)联合使用。
