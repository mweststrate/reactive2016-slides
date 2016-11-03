## The Quest For Immer Mutable State Management

Michel Weststrate - @mweststrate

MobX - Mendix

<img src="img/mobx2.png" width="150px" />

---

Developers are too smart

.appear[(and too expensive)]

.appear[to have them do stupid adminstrative tasks]

.appear[(that can be done way better by computers anyway)]

---

.appear[Manual releases &rarr; Continuous Deployment]

.appear[Manipulating the DOM &rarr; Components + VDOM]

.appear[Managing data flow &rarr; Transparent Reactive Programming]

---

# Contents

1. MobX basics & philosophy
2. Observable data > Immutable data

---

# MobX

Transparent Reactive Programming Library

Makes state management simple & scalable

---

.appear[
```javascript
const person = {
    name: "michel",
    age: 31
}
```
].appear[
```javascript
const App = ({ person }) => <h1>{ person.name }</h1>
```
].appear[
```javascript
ReactDOM.render(<App person={person} />, document.body)
```
].appear[
```javascript
person.name = "@mweststrate"
```
]

.layer1[
.appear[
```javascript
const person = observable({
    name: "michel",
    age: 31
})
```
].appear[
```javascript
const App = observer(({ person }) => <h1>{ person.name }</h1>)
```

```javascript
ReactDOM.render(<App person={person} />, document.body)
```

```javascript
person.name = "@mweststrate"
```

]
]
---

The view is a function of the state

```
    view = f(state)
```

---

The view is a transformation of the state

```
    view = f(state)
```

---

The view is a *live* transformation of the state

```
mobx.autorun(() => {
    view = f(state)
})
```

---

Consistency is guaranteed

.appear[Regardless *how* or *when* state is changed]

---

<img src="img/flow_notext.png" width="1200" style="margin-left: -2em">

---

# MobX Core

1. .appear[`observable`<br/> mark data as trackable]
2. .appear[`autorun` / `observer`<br/> automatically run function if relevant state changed]
3. .appear[`computed`<br/> derive value, if needed]
4. .appear[`action`<br/> indicates that state will be changed]
---

# Mendix / Live Demo

---

State is the only truth

.appear[Actions, derivations, reactions are decoupled]

---

class: fullscreen

<img src="img/ReactNext.png" width="1200" />

---

Separate teams can individually work on separate features

.appear[No need to coordinate actions]

.appear[No need to coordinate data usage]

.appear[State shape is our only contract]

---

# Example: validation rules

```javascript
function carInvariant(car, rules) {
    when(
        () => car.speed > rules.speedLimit,
        () => car.sendFine()
    )
}
```

---
class: fullscreen

<img src="img/ReactNext2.png" width="1200" />

---

# MobX so far..

.appear[
Free egghead.io course:<br/>
http://egghead.io/courses/mobx-fundamentals<br/><br/>
]

.appear[
5000+ stars, top 1% on NPM<br/><br/>
]

.appear[
Second most popular state management library<br/>
http://stateofjs.com/2016/statemanagement
]

---

# The Reactions

It's so simple and fast :)

<img src="img/diff.png" width="800px" />

---

# The Reactions

It's magic :(

---

# The Reactions

It's unopinionated :)

---

# The Reactions

It's unopinionated :(

---

# The Reactions

It uses mutable data :)

---

# The Reactions

It uses mutable data :(

---

## Immutable or Mutable Data?

---

## Redux or MobX?

---

.background[
    ![frozen](img/frozen/frozen.jpg)
]

---

# Immutable Data

.lighten.background[
    ![frozen](img/frozen/Elsa-Snow-Queen-In-Frozen.jpg)
]

<ol style="clear:none; position: relative; left: 200px; width:400px;">
<li>State snapshots</li>
<li>Replayable actions</li>
<li>State hydration</li>
<li>Traceability</li>
<li>Time travelling</li>
</ol>

---

# Observable, Mutable Data

.lighten.background[
    ![frozen](img/frozen/Frozen-Movie-Anna-HD-Wallpaper1.jpg)
]

<ol style="clear:none; position: relative; left: -140px; width: 800px;">
<li>Excels at complex, coupled domains</li>
<li>And complex, deep calculations</li>
<li>Mimimal boilerplate</li>
<li>Efficient</li>
<li>Unopinionated</li>
<li>Encourages strong typing</li>
</ol>

---

The relevance of each benefit is different in each project.

.appear[
What are the driving principles?
]

---

.appear[
_Redux_<br/>Predictability through transactional state
]

.appear[
<br/>
_MobX_<br/>Simplicity through minimally defined,<br/>automatically derived state
]

---

# The Quest For

A minimally defined, *snapshot-able* *state container* with replayable, KISS *actions* and efficient, *transparent* reactive *derivations*

---

class: fullscreen

![frozen](img/frozen/061913_inspiration-for-frozen-disney-animation-6_0.jpg)

---

Demo

---

.boring[
```
const states = []
```
]
.appear[
```
autorun(() => {
```
]

```
    snapshot = serialize(state)
    states.push(snapshot)
```

.appear[
```
})
```
]

---

A snapshot is a *live* transformation of the state

---

# Problems

1. .appear[No standardized serialization .appear[(&ldquo;serializr&rdquo; package helps)]]
2. .appear[Deep serializing state is expensive]
3. .appear[No structural sharing]

---

# Solutions

1. .appear[Trees are easy to serialize]
3. .appear[A snapshot is a derived value]
2. .appear[Rendering a tree with structural sharing? <br/>Solved problem]

---

## MobX computed values

---
```
class Person {
    firstName = "Michel"
    lastName = "Weststrate"

    get fullName() {
        console.log("calculating!")
        return [this.firstName, this.lastName]
    }
}
```

---

```
person.firstName = "John"

console.log(person.fullName)
// calculating!

console.log(person.fullName)
// calculating!
```

<small>_Pull Based: Recompute every time value is needed_</small>

---

```
class Person {
    @observable firstName = "Michel"
    @observable lastName = "Weststrate"

    @computed get fullName() {
        console.log("calculating!")
        return [this.firstName, this.lastName]
    }
}
```
---
```
person.firstName = "John"
// calculating!

console.log(person.fullName)

console.log(person.fullName)
```

<small>_Push Based: Recompute when a source value changes_</small>

---

# Snapshotting Observable Mutable Data

.boring[
```
class Todo {
    @observable id = 0
    @observable text = ""
    @observable completed = false
```
]

.appear[
```
    @computed get json() {
        return {
            id: this.id,
            text: this.text,
            completed: this.completed
        }
    }
```
]

.boring[
```
}
```
]

---

# Snapshotting Observable Mutable Data

.boring[
```
class TodoStore {
    @observable todos = []
```
]

```
    @computed json() {
        return this.todos.map(
            todo => todo.json
        )
    }
```

.boring[
```
}
```
]

---

class: fullscreen stacked whitebg

.appear[![snapshot](img/snapshot1.png)]
.appear[![snapshot](img/snapshot2.png)]
.appear[![snapshot](img/snapshot3.png)]
.appear[![snapshot](img/snapshot4.png)]
.appear[![snapshot](img/snapshot5.png)]
.appear[![snapshot](img/snapshot6.png)]

---

# mobx-state-tree

_Opinionated, MobX powered state container_

https://github.com/mobxjs/mobx-state-tree

---

# Core Concepts

.appear[state is a tree of models]

.appear[models are mutable, observable, rich]

.appear[snapshot: immutable representation of the state of a model]

---

# Factories

.appear[

```
const myModelFactory = createFactory({
    /* exampleModel */

    // properties
    // computed values
    // actions
})
```
]

.appear[
```
// returns fn:
snapshot => observable({...exampleModel, ...snapshot })
```
]

---

# Factories

.boring[
```
import {createFactory} from "mobx-state-tree"
```
]

```javascript
const Box = createFactory({
    name: "A cool box instance",
    x: 0,
    y: 0,

    get width() {
        return this.name.length * 15;
    }
})
```

.appear[
```
const box1 = Box({ name: "Hello, Reactive2016!" })
```
]
---

# Factories

.boring[
```
import {createFactory, mapOf, arrayOf} from "mobx-state-tree"
```
]
```
const Store = createFactory({
    boxes: mapOf(Box),
    arrows: arrayOf(Arrow),
    selection: ""
})
```

---

.lighten.background[
    <img src="img/frozen/frozen-3-5.jpg" alt="snapshots" style="max-width: 130%;">
]

# Snapshots

Representation of the state of a model<br/> at a particular moment in time

---

# Snapshots

```
    getSnapshot(model): snapshot
```
```
    applySnapshot(model, snapshot)
```
```
    onSnapshot(model, callback)
```
---

# Time Travelling

```
const states = [];
let currentFrame = -1;

onSnapshot(store, snapshot => {
    if (currentFrame === states.length -1) {
        currentFrame++
        states.push(snapshot);
    }
})

function previousState() {
    if (--currentFrame >= 0)
        applySnapshot(store, states[currentFrame])
}
```

---

# Snapshots & Forms

```javascript
const todoEditor({todo}) => (
    <TodoEditForm
        todo={clone(todo)}
        onSubmit={
            (modifiedTodo) => {
                applySnapshot(todo, getSnapshot(modifiedTodo))
            }
        }
    />
)
```
.appear[
```javascript
function clone(model) {
    return getFactory(model)(getSnapshot(model))
}
```
]

---


# Snapshots & Testing

```javascript
const todo = clone(exampleTodo)

todo.markCompleted()

assert.deepEqual(getSnapshot(todo), {
    title: "test", completed: true
})
```

---

# Snapshots & Jest

![jest](img/jest.gif)

```
expect(getSnapshot(todo)).toMatchSnapshot()
```

---

Demo

---

# Snapshots & Syncing

```
onSnapshot(store, (data) => {
    socketSend(data)
})

onSocketMessage((data) => {
    applySnapshot(store, data)
})
```

---

.lighten.background[
    ![frozne](img/frozen/patches.jpg)
]

# Patches

JSON-patch rfc6902

*Changes need to be broadcasted!*

---

# Patches

```
    onPatch(model, calback)
```
```
    applyPatch(model, jsonPatch)
```
---

Demo

---

# Patches & Syncing

```
onPatch(store, (data) => {
    socketSend(data)
})

onSocketMessage((data) => {
    applyPatch(store, data)
})
```
---

# Patches

```javascript
onPatch(store, patch => console.dir(patch))

onPatch(store.box.get("0d42afa6"), patch => console.dir(patch))
```
.appear[
```
store.box.get("0d42afa6").move(5, 0)
```
]
.appear[
```
// output:

{ op: "replace", path: "/boxes/0d42afa6/x", value: 105 }

{ op: "replace", path: "/x", value: 105 }
```
]
---

class: fullscreen stacked whitebg

.appear[![patch](img/patch1.png)]
.appear[![patch](img/patch2.png)]
.appear[![patch](img/patch3.png)]
.appear[![patch](img/patch4.png)]

---

.lighten.background[
    ![frozne](img/frozen/Disney-Frozen-Movie-kristoff-and-sven-wallpaper.jpg)
]

# Actions

---

What if an action description is the effect,

instead of the cause of a function call?

---

# Actions

.boring[
```javascript
const Box = createFactory({
    x: 0,
    y: 0,
```
]

```
    move: action(function(dx, dy) {
        this.x += dx
        this.y += dy
    })
```

.boring[
```
})
```
]
.appear[
```
box1.move(10, 10)
```
]
---

# Actions

```
    onAction(model, callback)
```
```
    applyAction(model, actionCall)
```

---

# Actions & Middleware

```javascript
onAction(store, (action, next) => {
    console.dir(action)
    return next()
})

store.get("ce9131ee").move(23, -8)
```

.appear[
```
// prints:
{
    "name":"move",
    "path":"/boxes/ce9131ee",
    "args":[23,-8]
}
```
]
---

Demo

---

# Actions & Syncing

```
onAction(store, (data, next) => {
    const res = next()
    socketSend(data)
    return res
})

onSocketMessage((data) => {
    applyAction(store, data)
})
```

---
# Actions & Forms

```
function editTodo(todo) {
    const todoCopy = clone(todo)
    const actionLog = []

    onAction(todoCopy, (action, next) => {
        actionLog.push(action)
        return next()
    })

    showEditForm(todoCopy, () => {
        applyActions(todo, actionLog)
    })
}
```

---
# Actions

* Based on MobX actions
* Unlock part of the state tree for editing
* Emit action events, apply middleware
* Straight forward
* Bound
---

.lighten.background[
    <img src="img/frozen/frozen3.png" style="max-width:none"/>
]

# References

---

# References

```javascript
const myFavoriteBox = store.boxes.get("abc123")

store.selection = myFavoriteBox
```

.appear[
```
//  Throws: element is already part of a state tree
```
]

.appear[
```
store.selection = myFavoriteBox.id
```
]

---

# References

.boring[
```javascript
const Store = createFactory({
    boxes: mapOf(Box),
```
]

```
    selectionId: '',

    get selection() {
        return this.selectionId ? this.boxes.get(this.selectionId) : null
    },
    set selection(value) {
        this.selectionId = value ? value.id : null
    }
```
.boring[
```
})
```
]
---

# References

```javascript
const Store = createFactory({
    boxes: mapOf(Box),
    selection: referenceTo("/boxes/id")
})
```

.appear[
```javascript
const myFavoriteBox = store.boxes.get("abc123")

store.selection = myFavoriteBox
```
]

---

# mobx-state-tree

A minimally defined,

*snapshot-able*
.appear[&nbsp;&#8730;]

*state container*
.appear[&nbsp;&#8730;]

with replayable *actions*
.appear[&nbsp;&#8730;]

and efficient, *transparent* reactive *derivations*
.appear[&nbsp;&#8730;]

.appear[_ & ... patches, middleware, references, dependency injection..._]

---

.background[
    ![frozen](img/frozen/image_a492ba07.png)
]

---

Demo

---

redux actions

redux dispatching

redux provider & connect

redux devtools

.appear[

<span style="text-decoration:line-through">redux store</span>

<span style="text-decoration:line-through">redux reducers</span>
]
.appear[

mobx-state-tree factories

mobx-state-tree actions

]
---


.background[
    <img src="img/frozen/789efe3729df42857ca3fb477fad6626.jpg" style="max-width: 110%" />
]

---

.boring[
```
const initialState = {
    todos: [{
        text: 'learn mobx-state-tree',
        completed: false,
        id: 0
    }]
}
```
]

```
const store = TodoStore(initialState)
const reduxStore = asReduxStore(store)

render(
  <Provider store={reduxStore}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

.boring[
```
connectReduxDevtools(store)
```
]

---

```
function asReduxStore(model) {
    return {
        getState : ()       => getSnapshot(model),
        dispatch : action   => {
            applyAction(model, reduxActionToAction(action))
        },
        subscribe: listener => onSnapshot(model, listener),
    }
}

```

---

```
const Todo = createFactory({
    text: 'Use mobx-state-tree',
    completed: false,
    id: 0
})
```

```
const TodoStore = createFactory({
  todos: arrayOf(Todo),

  COMPLETE_TODO: action(function ({id}) {
    const todo = this.findTodoById(id)
    todo.completed = !todo.completed
  }),
```

.boring[
```
  findTodoById: function (id) {
    return this.todos.find(todo => todo.id === id)
  }
})
```
]
---

Demo

---

.appear[Try mobx-state-tree]

.appear[Transactional state is just reactive transformation away]

.appear[
    <img src="img/letitgo.jpg" width="450px" style="margin-right: 1000px"/>
]

.appear[
    <img src="img/letitgo.gif" width="450px" style="position: relative; top: -230px; left: 300px;"/>
]

.appear[
<div style="position: relative; top: -230px">
<p>egghead.io/courses/mobx-fundamentals</p>
<p>@mweststrate</p>
</div>
]

---



---

# What about ELM?

```
import cool from "my-cool-store"

const app = Elm.Main.embed(myHtmlElement);

app.ports.myPort.subscribe(data => {
    applySnapshot(cool.part.of.the.state, data)
})

onSnapshot(cool.part.of.the.state, snapshot => {
    app.ports.myPort.send(snapshot)
})
```
