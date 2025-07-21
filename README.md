## We can categorize states into (Local / Global) States
### Local State
- A state need only for one component or a few more components 
- Used on a component or it's childes (By passing via props)
### Global State
- State that many components may need it
- Shared all over the application (accessible by each component ) ,Use it by "context Api , Redux"
## when and where to store data using States ?
| **Use Case**                                     | **Use Local State (`useState`, `useReducer`)** | **Use Global State (Redux, Zustand, Jotai, etc.)** | **Use Context API**           | **Don't Use State (derived/computed)** |
| ------------------------------------------------ | ---------------------------------------------- | -------------------------------------------------- | ----------------------------- | -------------------------------------- |
| **Form input values (within one component)**     | ✅ Yes                                          | ❌ No                                               | ❌ Overkill                    | ❌ Not needed                           |
| **UI toggles (modals, dropdowns)**               | ✅ Yes                                          | ❌ No                                               | ⚠️ Maybe for global modals    | ❌ Not needed                           |
| **Authentication status (logged in/out)**        | ❌ No                                           | ✅ Yes                                              | ✅ If very simple              | ❌ Not derived                          |
| **Theme (dark/light mode)**                      | ❌ No                                           | ✅ Yes                                              | ✅ Good use case               | ❌ Not derived                          |
| **Cart/wishlist in e-commerce**                  | ❌ No                                           | ✅ Yes                                              | ⚠️ Not ideal for context only | ❌ Not derived                          |
| **Current user profile (fetched from API)**      | ⚠️ Sometimes                                   | ✅ Yes                                              | ✅ For small apps              | ❌ Not derived                          |
| **Page-specific data (filters, tabs)**           | ✅ Yes                                          | ❌ No                                               | ❌ Not needed                  | ❌ Not derived                          |
| **Sharing data between many distant components** | ❌ No                                           | ✅ Yes                                              | ⚠️ Possible but not scalable  | ❌ Not derived                          |
| **Frequently updated data (e.g. input typing)**  | ✅ Yes                                          | ❌ No                                               | ❌ Not performant              | ❌ Not derived                          |
| **Data from server (fetched with React Query)**  | ❌ No (not needed)                              | ❌ No (React Query handles it)                      | ❌ No                          | ✅ Yes (use React Query)                |
| **Derived/calculated values**                    | ✅ Yes (use `useMemo`)                          | ❌ No                                               | ❌ No                          | ✅ Yes (computed, not stored)           |

-------------------------------------------------------------------------------

# React Behind the Scenes

## 📊 Component vs Element vs Instance

| Term         | What It Is                                     | Code Example                         | When It Exists                     |
|--------------|------------------------------------------------|--------------------------------------|------------------------------------|
| **Component**| A **function** or **class** that defines UI    | `function Button() { return <div /> }` | At **declaration time** (in code) |
| **Element**  | A **plain JS object** describing UI            | `const el = <Button />`              | At **render time**, before DOM     |
| **Instance** | The result of mounting a component (with state, lifecycle, etc.) | `React mounts <Button /> → creates instance` | At **runtime**, when React renders |
### 📌 Explanations:

- **Component** = Blueprint (like a class/function definition)
    
- **Element** = What you return from JSX — a lightweight object (`{ type, props }`)
    
- **Instance** = What React creates internally to track that component’s state, lifecycle, etc.
### 📦 Example Walkthrough:

```
function Welcome(props) {   return <h1>Hello, {props.name}</h1>; }  
const element = <Welcome name="Mahmoud" />; // <-- This is a React Element  ReactDOM.render(element, document.getElementById('root'));`

```
- `Welcome` is the **component**
    
- `<Welcome name="Mahmoud" />` is the **element**
    
- When React renders it, it creates an **instance** of `Welcome
#  React Rendering and Reconciler (Deep Dive)

## 🔄 What Triggers a Render in React?

- Component is **mounted** for the first time.
- **State** is updated via `setState` / `useState`.
- **Props** change.
- **Context** value changes.
- `forceUpdate()` is called (rare).

---

## ⚙️ React Rendering Process

### 1. **Reconciliation Phase (Render Phase)**
- Re-run the component to create a **new virtual DOM** (React Element Tree).
- Compare it to the previous virtual DOM.
- React calculates the **minimal difference**.
- Pure phase: No side effects.

### 2. **Commit Phase**
- React applies the changes to the **actual DOM**.
- Runs side effects (`useEffect`, `useLayoutEffect`, `refs`, etc).

---

## 🧱 Virtual DOM vs Real DOM

- **Virtual DOM (VDOM)**: JS representation of the UI.
- React **diffs the new VDOM with the old one** and only updates the real DOM where needed.

---

## ⚡ React Optimizations

- **Batching**: Groups multiple updates into one render.
- **Memoization**:
  - `React.memo`
  - `useMemo`, `useCallback`
- **Keys in lists**: Helps with efficient list diffing.
- **Avoid unnecessary renders** using:
  - `shouldComponentUpdate`
  - `React.memo`
  - `PureComponent`

---

## 🔁 Lifecycle Phases (Class vs Hooks)

| Phase    | Class Component             | Functional Component           |
|----------|-----------------------------|--------------------------------|
| Mount    | `componentDidMount`         | `useEffect(() => {}, [])`     |
| Update   | `componentDidUpdate`        | `useEffect(() => {}, [deps])` |
| Unmount  | `componentWillUnmount`      | `useEffect(() => { return () => {} }, [])` |

---

# 🧠 Deep Dive: The React Reconciler

## 🔍 What is the Reconciler?

- The part of React responsible for:
  - Comparing the new virtual DOM with the old one.
  - Determining what changed.
  - Building the list of updates to be committed to the real DOM.

---

## ⚙️ Reconciliation Process (Step-by-Step)

1. A render is triggered (by state/prop/context).
2. React **calls the component** to generate a new **React element tree** (VDOM).
3. React compares the **new tree** to the **previous tree**.
4. It updates the **Fiber tree** — React’s internal data structure.
5. React builds a **list of effects** (DOM updates).
6. Moves to **commit phase** to apply changes.

---

## 🔁 Diffing Algorithm (Heuristics)

- **Same type** → Update props
```jsx
<div>Hi</div> → <div>Hello</div>
```

- **Different type** → Replace node
`<div>Hi</div> → <span>Hello</span>`

- **Use keys in lists**
`[{ id: 1 }, { id: 2 }].map(item => <li key={item.id}>...</li>)`

> Without keys: React re-renders everything.  
> With keys: React can move, add, or delete only what's needed.

---

## 🧵 What is Fiber?

- React’s internal tree data structure.
    
- Each component becomes a **Fiber Node**.
    
- Fiber makes rendering **interruptible and async**.
    
- Enables **concurrent features** (like Suspense).
    

### Each Fiber node contains:

- `type`: component/function/html tag
    
- `key`: used for list diffing
    
- `props`, `state`
    
- `return`, `child`, `sibling`: to form the tree
    

---

## 📌 Real-World Impact

Understanding reconciliation helps with:

- **Performance tuning**
    
- **Choosing keys wisely**
    
- **Preventing unnecessary re-renders**
    
- **Debugging rendering issues**
    

---

## 🧠 Summary Diagram

```
State or Props Change 
        ↓ 
Re-render Component
        ↓ 
Create New VDOM
        ↓
Compare with Old VDOM (Reconciler)
        ↓
Build Effect List (Fiber Tree)
        ↓
Commit DOM Changes + Effects
```

**Summary**

- We have a **trigger** (something that tells React to start rendering — such as a project starting with `npm run dev` or a **state/props/context** change).
  
- Then the **render phase** starts (this is where React calls the component function and builds a new virtual DOM).  
  ➤ ✅ No real DOM changes happen in this phase — **no visual output yet**.

- The **Reconciler (Fiber)** compares the **new virtual DOM** with the previous one to determine what has changed.  
  ➤ ✅ This is called **diffing**.

- React builds a **list of changes** that need to be made (called the **effect list** or "mutation effects").

- Then React enters the **commit phase**, where it:
  - Applies the necessary changes to the **real DOM**.
  - Runs lifecycle effects (e.g. `useEffect`, `useLayoutEffect`, refs).

- Once the **DOM is updated**, the **browser takes over**:  
  - It schedules a **layout + paint + composite** to visually show the changes.  
  ➤ ✅ This is part of the **browser rendering pipeline**, not React itself.
