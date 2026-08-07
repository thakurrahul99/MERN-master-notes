# REACT useReducer MASTERCLASS 🚀

React functional components mein state management ko scalable, predictable, aur cleanly organize karne ke liye standard hooks ke rules ke according hume ek robust state state-transition engine diya jata hai—**`useReducer`**. 

Jab hamare pass complex state structures hote hain jahan multiple sub-values ek dusre par depend karti hain, ya agla state pichle state ke mathematical values par directly calculated hota hai, tab `useReducer` hume pure functional updates coordinate karne mein madad karta hai.

Chalo, pure **"Examples First"** approach ke sath pehle iske 16 dynamic code examples ko live dekhte hain aur iske architecture ko master karte hain!

---

## THE COMPARISON MATRICES 📊

Aage badhne se pehle in chaar essential comparison matrices ko dhyan se samajh lo taaki tumhare architectural decisions perfect rahein:

### Table 1: useReducer vs useState
| Feature | `useState` Hook | `useReducer` Hook |
| :--- | :--- | :--- |
| **State Complexity** | Simple state properties (jaise toggle flags, independent inputs, simple strings) ke liye perfect hai. | Complex nested objects, arrays, ya interdependent state fields ke liye suitable hai. |
| **State Update Logic** | Component ke event handlers ke andar inline ya isolated setters ke zariye scattered rehta hai. | Ek centralized **Reducer Function** ke andar saare updates collocated hote hain. |
| **Data Flow** | Child components ko state change callbacks manually pass karne padte hain. | Global static **`dispatch`** method ko niche deep components me transfer kiya jata hai. |

### Table 2: Reducer vs Normal Function
| Feature | Normal Helper Function | Reducer Function |
| :--- | :--- | :--- |
| **Purity** | Side-effects run kar sakti hai, window/global properties mutate kar sakti hai. | **Pure Function** hona mandatory hai. Same state/action inputs par humesha same output return karti hai. |
| **Immutability** | Arguments ya nested array coordinates ko direct modify/mutate kar sakti hai. | State arguments ko strictly **Read-Only** treat karke, spread operator se naya clone generate karti hai. |
| **Signature** | Koi fixed parameters requirements nahi hote. | Strictly expects exact arguments: `(state, action) => newState`. |

### Table 3: Dispatch vs setState
| Feature | `setState` (useState updater) | `dispatch` Method (useReducer return) |
| :--- | :--- | :--- |
| **Trigger Mechanism** | Direct new state value pass karke trigger hota hai. | Ek programmatic action object trigger (dispatch) karke process hota hai. |
| **Referential Stability** | Identity stable rehti hai, par complex updates cascading callback leaks cause kar sakti hain. | React iske pointer reference ko permanently stable aur unchanged rakhne ki guarantee deta hai. |

### Table 3.4: useReducer + useContext vs Props
| Feature | Prop Drilling (Manual Pipeline) | `useReducer` + `useContext` Integration |
| :--- | :--- | :--- |
| **Boilerplate** | Har intermediate component level par callbacks pipeline define karne padte hain. | Direct nested provider subscription ke thrown action dispatch targets resolve ho jate hain. |
| **Downstream Efficiency** | Parent render state change hone par saare intermediate components recalculate ho jate hain. | `dispatch` handle reference stable hone ki wajah se children rendering completely optimisable ho jati hai. |

---

# SECTION 1: THE BEGINNER'S PLAYGROUND (5 EXAMPLES)

Chalo bhai, sabse pehle 5 basic aur easy-to-understand code examples se shuru karte hain jahan hum state-transitions ke basic actions ko handle karna sikhenge.

---

### Beginner Example 1: Standard Numeric Counter (Increment/Decrement)

#### File Name: `CounterSimple.js`
```javascript
import React, { useReducer } from 'react'; //

// 1. Initial State Object defined outside component to preserve memory
const initialState = { count: 0 }; //

// 2. Pure Reducer function mapping actions to new state immutably
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 }; //
    case 'decrement':
      return { count: state.count - 1 }; //
    default:
      // Reducer standard fallback rule
      throw new Error("Invalid action dispatched!"); 
  }
}

export default function CounterSimple() {
  // 3. Calling useReducer hook to get current state and dispatch method
  const [state, dispatch] = useReducer(reducer, initialState); //

  return (
    <div style={{ padding: '20px', border: '1px solid #ccc', borderRadius: '8px' }}>
      <h3>Standard useReducer Counter 🔢</h3>
      <p>Count value: <strong>{state.count}</strong></p>
      
      {/* 4. Dispatching actions in response to user clicks */}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button> {/* */}
      <button onClick={() => dispatch({ type: 'decrement' })} style={{ marginLeft: '10px' }}>-</button> {/* */}
    </div>
  );
}
```

#### Line-by-Line Explanation
1. `import React, { useReducer } from 'react'`: React package se `useReducer` performance hook ko import kiya.
2. `const initialState = { count: 0 }`: State object blueprint coordinate initialize kiya, jisse count start value memory slot allocate ho sake.
3. `function reducer(state, action)`: Ek pure transformation function construct kiya jo dynamic transitions ko mathematical evaluation coordinate maps par check karega.
4. `switch (action.type)`: Dispatched criteria check karke associated cases target check pipeline trigger karata hai.
5. `const [state, dispatch] = useReducer(reducer, initialState)`: Array destructuring use karke current state snapshot aur static dispatch pointer resolve kiya.

#### Browser Output
* Screen par ek header, count state output (`Count value: 0`), aur do interactive click buttons (`+` aur `-`) render honge.

#### Why useReducer is used here
* `useState` ke normal multi-line handlers ko remove karke pure state transformations ko component UI logic se completely separate aur clean karne ke liye ise use kiya hai.

#### Better Version
* Custom action payloads configuration variables directly pass trigger setups manage karte hain.

#### Dry Run
1. **Mounting Phase**: React `useReducer(reducer, initialState)` parameter run karta hai. State is initialized as `{ count: 0 }`.
2. **User Clicks '+'**: Button trigger dispatch dispatch `action = { type: 'increment' }` register karta hai.
3. **Reducer Triggered**: React reducer function execute karta hai: `reducer({ count: 0 }, { type: 'increment' })`. switch statement `increment` matched block execute karke naya object `{ count: 1 }` return karta hai.
4. **Rendering Update**: Component recalculate hota hai aur target label value screen par updating speed stable pass karata hai.

---

### Beginner Example 2: Add/Subtract value with dynamic Payloads

#### File Name: `CounterPayload.js`
```javascript
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'add':
      // Incrementing state count dynamically by action payload
      return { count: state.count + action.payload }; 
    case 'subtract':
      // Decrementing state count dynamically by action payload
      return { count: state.count - action.payload };
    default:
      return state; // Standard fallback return
  }
}

export default function CounterPayload() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div style={{ padding: '20px', border: '1px solid #ddd', marginTop: '10px' }}>
      <h3>Dynamic Payload Multiplier ⚙️</h3>
      <p>Tracker: <strong>{state.count}</strong></p>
      
      {/* Passing action object containing both type and payload parameter */}
      <button onClick={() => dispatch({ type: 'add', payload: 5 })}>Add 5</button> {/* */}
      <button onClick={() => dispatch({ type: 'subtract', payload: 3 })} style={{ marginLeft: '10px' }}>Subtract 3</button> {/* */}
    </div>
  );
}
```

#### Why useReducer is used here
* Is example mein dynamically action attributes pass coordinates run karwaye hain, jisse input events dynamic properties payload se directly maps state update coordinate evaluate ho sakein.

---

### Beginner Example 3: Boolean Toggler (Style Theme Swapper)

#### File Name: `ThemeTogglerReducer.js`
```javascript
import React, { useReducer } from 'react';

const initialState = { isDark: false };

function reducer(state, action) {
  switch (action.type) {
    case 'toggle':
      return { isDark: !state.isDark }; // Inverting boolean flag immutably
    default:
      return state;
  }
}

export default function ThemeTogglerReducer() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div style={{ 
      padding: '24px', 
      background: state.isDark ? '#333' : '#fff', 
      color: state.isDark ? '#fff' : '#000',
      border: '1px solid #ddd',
      marginTop: '10px'
    }}>
      <h3>Theme Swapper Workspace 🎨</h3>
      <button onClick={() => dispatch({ type: 'toggle' })}>
        Switch CSS Paint Mode
      </button>
    </div>
  );
}
```

---

### Beginner Example 4: Uncontrolled Input Capture to State

#### File Name: `SimpleFormState.js`
```javascript
import React, { useReducer } from 'react';

const initialState = { textInput: "" };

function reducer(state, action) {
  switch (action.type) {
    case 'update':
      return { textInput: action.payload }; // Mapping typed values
    default:
      return state;
  }
}

export default function SimpleFormState() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div style={{ padding: '20px', border: '1px solid #aaa', marginTop: '10px' }}>
      <h3>State Form Input Tracker ✍️</h3>
      <input 
        type="text" 
        value={state.textInput} 
        onChange={(e) => dispatch({ type: 'update', payload: e.target.value })} //
        placeholder="Type secure code..."
      />
      <p>Captured String: <strong>{state.textInput}</strong></p>
    </div>
  );
}
```

---

### Beginner Example 5: Counter with Initial State Reset

#### File Name: `CounterWithReset.js`
```javascript
import React, { useReducer } from 'react';

const baseCount = 10;
const initialState = { count: baseCount };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'reset':
      return { count: action.payload }; // Resetting count value
    default:
      return state;
  }
}

export default function CounterWithReset() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div style={{ padding: '20px', border: '1px solid #bbb', marginTop: '10px' }}>
      <h3>Reset Trigger Counter ⏱️</h3>
      <p>Tracker Sum: <strong>{state.count}</strong></p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'reset', payload: baseCount })} style={{ marginLeft: '10px' }}>
        Reset
      </button>
    </div>
  );
}
```

---

# SECTION 2: INTERMEDIATE CHANNELS (5 EXAMPLES)

Chalo bhai, ab calculations complexity ko target karte hue lazy initialization, nested list array maps updates, aur API states profiling ko intermediate examples ke sath samajhte hain.

---

### Intermediate Example 6: Lazy Initializer Setup (init function)

#### File Name: `LazyInitCounter.js`
```javascript
import React, { useReducer } from 'react';

// 1. Expensive initialization calculation function defined
function init(initialValue) {
  console.log("⚡ Expensive computation for lazy initial state triggered!");
  return { count: initialValue * 10 }; // Generating initial state from argument
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'reset':
      return init(action.payload); // Reuse lazy function for state reset
    default:
      return state;
  }
}

export default function LazyInitCounter({ baseVal = 5 }) {
  // 2. Passing init function as the third argument to useReducer
  const [state, dispatch] = useReducer(reducer, baseVal, init); //

  return (
    <div style={{ padding: '24px', border: '2px solid purple', marginTop: '10px' }}>
      <h3>Lazy Initialization Controller 🧪</h3>
      <p>Calculated Count: <strong>{state.count}</strong></p>
      
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'reset', payload: baseVal })} style={{ marginLeft: '10px' }}>
        Reset State
      </button>
    </div>
  );
}
```

#### Line-by-Line Explanation
1. `function init(initialValue)`: State initialization function ko design kiya jo pass parameters ko calculate karta hai.
2. `useReducer(reducer, baseVal, init)`: useReducer hook execute hone par dynamic initializer ko call karke initial count ko calculate karta hai.
3. `return init(action.payload)`: Reset action resolve hone par lazy init execution body ko invoke kiya, jisse calculation reuse ho sake.

#### Why useReducer is used here
* Hardcoded configurations ko scale components ke props parameters par dynamically calculate karke compute pipeline ko optimize rakhne ke liye ise use kiya hai.

---

### Intermediate Example 7: BookablesList Navigation & Details

#### File Name: `BookablesListBasic.js`
```javascript
import React, { useReducer } from 'react';

// Mock list structures
const mockRooms = [
  { id: 101, title: "Meeting Room Large", group: "Rooms" }, //
  { id: 102, title: "Games Room Rental Unit", group: "Rooms" }, //
  { id: 103, title: "Breakfast Hall Booking", group: "Rooms" } //
];

const initialState = {
  group: "Rooms", //
  bookableIndex: 0, //
  bookables: mockRooms, //
  hasDetails: true //
};

function reducer(state, action) {
  switch (action.type) {
    case 'SET_INDEX':
      return { ...state, bookableIndex: action.payload }; // Update bookable index
    case 'TOGGLE_HAS_DETAILS':
      return { ...state, hasDetails: !state.hasDetails }; // Toggle details panel
    case 'NEXT_BOOKABLE':
      // Counting total bookables inside group to wrap index safely
      const count = state.bookables.filter(b => b.group === state.group).length; //
      return {
        ...state,
        bookableIndex: (state.bookableIndex + 1) % count //
      };
    default:
      return state;
  }
}

export default function BookablesListBasic() {
  const [state, dispatch] = useReducer(reducer, initialState);

  const activeBookable = state.bookables[state.bookableIndex]; // Derived active selection

  return (
    <div style={{ padding: '20px', border: '1px solid #ccc', marginTop: '10px' }}>
      <h3>Bookable Space Navigator 🧭</h3>
      <p>Active Space: <strong>{activeBookable?.title}</strong></p>

      <button onClick={() => dispatch({ type: 'NEXT_BOOKABLE' })}>
        Next Space ──►
      </button>
      <button onClick={() => dispatch({ type: 'TOGGLE_HAS_DETAILS' })} style={{ marginLeft: '10px' }}>
        Toggle Details Overlay
      </button>

      {state.hasDetails && (
        <div style={{ marginTop: '10px', background: '#f5f5f5', padding: '10px' }}>
          <p>Clearance Zone ID: #{activeBookable?.id}</p>
        </div>
      )}
    </div>
  );
}
```

---

### Intermediate Example 8: Week Picker utilizing custom dates utility

#### File Name: `WeekPickerReducer.js`
```javascript
import React, { useReducer } from 'react';

// Date utility function returning custom week structure
function getWeek(dateInput) {
  const date = new Date(dateInput);
  return {
    currentDate: date,
    start: new Date(date.setDate(date.getDate() - date.getDay())), // Get start of week
    end: new Date(date.setDate(date.getDate() + 6)) // Get end of week
  };
}

function reducer(state, action) {
  switch (action.type) {
    case 'SET_DATE':
      return getWeek(new Date(action.payload)); // Set new week from payload
    default:
      return state;
  }
}

export default function WeekPickerReducer() {
  // Lazily generating initial week using date argument and getWeek helper
  const [week, dispatch] = useReducer(reducer, "2026-08-04", getWeek); //

  return (
    <div style={{ padding: '20px', border: '1px solid orange', marginTop: '10px' }}>
      <h3>Week Calendar Picker 📅</h3>
      <p>Week Start Date: <strong>{week.start.toDateString()}</strong></p>
      <p>Week End Date: <strong>{week.end.toDateString()}</strong></p>

      <button onClick={() => dispatch({ type: 'SET_DATE', payload: "2026-08-11" })}>
        Select Next Week (Aug 11)
      </button>
    </div>
  );
}
```

---

### Intermediate Example 9: API Loader (isLoading, error, and data)

#### File Name: `AsyncLoaderReducer.js`
```javascript
import React, { useReducer } from 'react';

const initialState = {
  isLoading: false, //
  error: null, //
  data: [] //
};

function reducer(state, action) {
  switch (action.type) {
    case 'FETCH_REQUEST':
      return { isLoading: true, error: null, data: [] }; // Clear previous data
    case 'FETCH_SUCCESS':
      return { isLoading: false, error: null, data: action.payload }; // Set loaded data
    case 'FETCH_ERROR':
      return { isLoading: false, error: action.payload, data: [] }; // Set error state
    default:
      return state;
  }
}

export default function AsyncLoaderReducer() {
  const [state, dispatch] = useReducer(reducer, initialState);

  const simulateFetch = () => {
    dispatch({ type: 'FETCH_REQUEST' });
    setTimeout(() => {
      // Simulation success scenario
      dispatch({ type: 'FETCH_SUCCESS', payload: ["Central Security Log", "Node Metrics Console"] });
    }, 1500);
  };

  return (
    <div style={{ padding: '20px', border: '1px solid green', marginTop: '10px' }}>
      <h3>Async API State Tracker 🛰️</h3>
      <button onClick={simulateFetch} disabled={state.isLoading}>
        {state.isLoading ? "Querying server..." : "Query Telemetry"}
      </button>

      {state.isLoading && <p>Loading data snapshots...</p>}
      {state.data.length > 0 && (
        <ul>
          {state.data.map((item, index) => <li key={index}>{item}</li>)}
        </ul>
      )}
    </div>
  );
}
```

---

### Intermediate Example 10: Multi-InputField Registration Form

#### File Name: `MultiInputFieldForm.js`
```javascript
import React, { useReducer } from 'react';

const initialState = {
  username: "",
  email: "",
  clearanceLevel: "Guest"
};

function reducer(state, action) {
  switch (action.type) {
    case 'INPUT_CHANGE':
      return {
        ...state, // Copying existing form state
        [action.field]: action.value // Override dynamic input property
      };
    default:
      return state;
  }
}

export default function MultiInputFieldForm() {
  const [form, dispatch] = useReducer(reducer, initialState);

  const handleTextChange = (e) => {
    dispatch({
      type: 'INPUT_CHANGE',
      field: e.target.name,
      value: e.target.value
    });
  };

  return (
    <div style={{ padding: '20px', border: '1px solid #aaa', marginTop: '10px' }}>
      <h3>Enterprise Registration Portal 🏛️</h3>
      <input 
        name="username" 
        value={form.username} 
        onChange={handleTextChange} 
        placeholder="Enter username..." 
      />
      <input 
        name="email" 
        value={form.email} 
        onChange={handleTextChange} 
        placeholder="Enter email..." 
        style={{ marginLeft: '10px' }}
      />
      <p>Review: {form.username} | {form.email}</p>
    </div>
  );
}
```

---

# SECTION 3: ADVANCED CHANNELS (3 EXAMPLES)

Chalo bhai, ab custom dispatch pass operations, dynamic object matrices, aur pure immutable loops transformations ko study karte hain.

---

### Advanced Example 11: Optimized Dispatch Pipeline

#### File Name: `DeepRerenderOptimizer.js`
```javascript
import React, { useReducer } from 'react'; //

const initialState = { clicks: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { clicks: state.count || state.clicks + 1 };
    default:
      return state;
  }
}

// Optimized static Child Button utilizing React.memo
const ActionButton = React.memo(({ dispatchAction }) => {
  console.log("🎨 ActionButton Child Component evaluated (skipped rerendering on clicks!)");
  return (
    // dispatch identity remains stable, preventing prop change triggers
    <button onClick={() => dispatchAction({ type: 'increment' })}>
      Increment Counter
    </button>
  );
});

export default function DeepRerenderOptimizer() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div style={{ padding: '24px', border: '3px solid black', background: '#fff' }}>
      <h3>Deep Rerender Optimizer 🏎️</h3>
      <p>Tracker Clicks Count: <strong>{state.clicks}</strong></p>
      
      {/* Passing dispatch down safely */}
      <ActionButton dispatchAction={dispatch} />
    </div>
  );
}
```

#### Line-by-Line Explanation
1. `const ActionButton = React.memo(...)`: Pure button UI ko memoize kiya taaki unnecessary rendering passes completely block ho sakein.
2. `dispatchAction({ type: 'increment' })`: React ke secure internal reference mechanism ke throws stable dispatch call dispatch kiya.
3. `dispatch` ko prop filter ke thrown transfer kiya bina use compile hooks list trigger kiye.

#### Why useReducer is used here
* React callback pointers to compare karne par naye function references generate hone se child components ko bypass-optimization trigger errors se safely protect karne ke liye ise use kiya hai.

---

### Advanced Example 12: WeekPicker with dynamic navigation rules

#### File Name: `WeekPickerActionState.js`
```javascript
import React, { useReducer } from 'react';

function getWeek(dateInput) {
  const date = new Date(dateInput);
  return {
    currentDate: date,
    start: new Date(date.setDate(date.getDate() - date.getDay())),
    end: new Date(date.setDate(date.getDate() + 6))
  };
}

function reducer(state, action) {
  switch (action.type) {
    case 'PREV_WEEK':
      // Move week backward by 7 days
      const prevDate = new Date(state.currentDate);
      prevDate.setDate(prevDate.getDate() - 7);
      return getWeek(prevDate);
    case 'NEXT_WEEK':
      // Move week forward by 7 days
      const nextDate = new Date(state.currentDate);
      nextDate.setDate(nextDate.getDate() + 7);
      return getWeek(nextDate);
    case 'SET_DATE':
      return getWeek(new Date(action.payload)); //
    default:
      return state;
  }
}

export default function WeekPickerActionState() {
  const [week, dispatch] = useReducer(reducer, "2026-08-04", getWeek); //

  return (
    <div style={{ padding: '24px', border: '3px solid navy', background: '#fafafa' }}>
      <h3>Calender Navigation Matrix 📅</h3>
      <p>Target Date: {week.currentDate.toDateString()}</p>
      <p>Range: <strong>{week.start.toDateString()} ──► {week.end.toDateString()}</strong></p>

      <button onClick={() => dispatch({ type: 'PREV_WEEK' })}>◄── Previous Week</button>
      <button onClick={() => dispatch({ type: 'NEXT_WEEK' })} style={{ marginLeft: '10px' }}>Next Week ──►</button>
    </div>
  );
}
```

---

### Advanced Example 13: Array Nested Objects Immutable Transforms

#### File Name: `ComplexObjectArrayReducer.js`
```javascript
import React, { useReducer } from 'react';

const initialState = {
  tasks: [
    { id: 1, title: "Check cluster logs", isDone: false },
    { id: 2, title: "Clean database registry", isDone: true }
  ]
};

function reducer(state, action) {
  switch (action.type) {
    case 'TOGGLE_TASK_STATUS':
      return {
        ...state,
        // Mapping tasks safely to prevent mutations
        tasks: state.tasks.map(task => 
          task.id === action.id ? { ...task, isDone: !task.isDone } : task
        )
      };
    case 'DELETE_TASK':
      return {
        ...state,
        // Returning new copy array immutably
        tasks: state.tasks.filter(task => task.id !== action.id)
      };
    default:
      return state;
  }
}

export default function ComplexObjectArrayReducer() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div style={{ padding: '24px', border: '3px solid purple', background: '#fff' }}>
      <h3>Immutable Nested Array Transforms 📊</h3>
      <ul>
        {state.tasks.map(t => (
          <li key={t.id} style={{ textDecoration: t.isDone ? 'line-through' : 'none' }}>
            <span>{t.title}</span>
            <button onClick={() => dispatch({ type: 'TOGGLE_TASK_STATUS', id: t.id })} style={{ marginLeft: '10px' }}>
              Toggle
            </button>
            <button onClick={() => dispatch({ type: 'DELETE_TASK', id: t.id })} style={{ marginLeft: '5px' }}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

# SECTION 4: REAL PRODUCTION SUITES (3 EXAMPLES)

Chalo bhai, ab real enterprise pipelines (jaise Accio Task Board, John Larsen's Bookables Grid, aur multi-stage Context Providers) ko pure code structures se explore karte hain.

---

### Production Project 14: Accio Todo list with edit & save capabilities

#### Folder Structure
```text
accio-todo-reducer/
├── src/
│   ├── components/
│   │   └── AccioTaskBoardApp.js
│   └── App.js
```

#### File Name: `AccioTaskBoardApp.js`
```javascript
import React, { useReducer, useState } from 'react'; //

const initialState = {
  tasks: [
    { id: 101, title: "Clean transactional audit logs", isEditing: false }, //
    { id: 102, title: "Review telemetry configurations", isEditing: false }
  ]
};

function reducer(state, action) {
  switch (action.type) {
    case 'ADD_TASK':
      return {
        ...state,
        // Appending task cleanly using spread operator to ensure immutability
        tasks: [...state.tasks, { id: Date.now(), title: action.payload, isEditing: false }]
      };
    case 'TOGGLE_EDIT':
      return {
        ...state,
        tasks: state.tasks.map(task => 
          task.id === action.id ? { ...task, isEditing: !task.isEditing } : task
        )
      };
    case 'SAVE_TASK_EDIT':
      return {
        ...state,
        tasks: state.tasks.map(task => 
          task.id === action.id ? { ...task, title: action.updatedText, isEditing: false } : task
        )
      };
    case 'DELETE_TASK':
      return {
        ...state,
        tasks: state.tasks.filter(task => task.id !== action.id) // Filtering out target item
      };
    default:
      return state;
  }
}

export default function AccioTaskBoardApp() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const [newTitle, setNewTitle] = useState("");

  const handleAppend = (e) => {
    e.preventDefault();
    if (!newTitle.trim()) return;
    dispatch({ type: 'ADD_TASK', payload: newTitle });
    setNewTitle("");
  };

  return (
    <div style={{ padding: '24px', border: '3px solid #111', background: '#fff', margin: '20px' }}>
      <h3>AccioJob Optimized Todo list 📋</h3>
      
      {/* 1. Add task input section */}
      <form onSubmit={handleAppend} className="add_tasks_section">
        <input 
          value={newTitle} 
          onChange={(e) => setNewTitle(e.target.value)} 
          placeholder="Queue enterprise task..."
          required 
        />
        <button type="submit" style={{ marginLeft: '10px' }}>Queue Task</button>
      </form>

      {/* 2. Tasks display section */}
      <ul className="tasks_section" style={{ listStyle: 'none', padding: 0, marginTop: '20px' }}>
        {state.tasks.map(item => (
          <li key={item.id} className="task" style={{ padding: '10px 0', borderBottom: '1px solid #ddd' }}>
            {item.isEditing ? (
              <div style={{ display: 'inline-block' }}>
                <input 
                  type="text" 
                  defaultValue={item.title} 
                  id={`input-${item.id}`} // Unique input id mapping
                />
                <button 
                  className="save" //
                  onClick={() => {
                    const text = document.getElementById(`input-${item.id}`).value;
                    dispatch({ type: 'SAVE_TASK_EDIT', id: item.id, updatedText: text });
                  }}
                  style={{ marginLeft: '10px' }}
                >
                  save
                </button>
              </div>
            ) : (
              <div>
                <span>{item.title}</span>
                <button 
                  className="edit" //
                  onClick={() => dispatch({ type: 'TOGGLE_EDIT', id: item.id })}
                  style={{ marginLeft: '15px' }}
                >
                  edit
                </button>
                <button 
                  className="delete" //
                  onClick={() => dispatch({ type: 'DELETE_TASK', id: item.id })}
                  style={{ marginLeft: '5px' }}
                >
                  delete
                </button>
              </div>
            )}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

##### Line-by-Line Explanation
* `tasks: [...state.tasks, { ... }]`: Spread operator use karke tasks array mein naya element append kiya, isse state reference break nahi hota.
* `className="edit" / className="delete"`: Accio specifications classes use karke task items ko update kiya.
* `dispatch({ type: 'SAVE_TASK_EDIT' })`: Inline actions trigger input value, updates save hone par items isEditing is reset to false.

---

### Production Project 15: John Larsen's Bookings Grid with Details Pane

#### Folder Structure
```text
bookings-grid-reducer/
├── src/
│   ├── components/
│   │   └── EnterpriseBookingsGrid.js
│   └── App.js
```

#### File Name: `EnterpriseBookingsGrid.js`
```javascript
import React, { useReducer } from 'react'; //

const mockSpaces = [
  { id: 201, title: "Lecture Hall Booking Option", rate: 300, group: "Rooms" }, //
  { id: 202, title: "Games Room Rental Unit", rate: 150, group: "Rooms" } //
];

const initialState = {
  activeGroup: "Rooms", //
  spaceIndex: 0, //
  spaces: mockSpaces, //
  showInvoice: false //
};

function reducer(state, action) {
  switch (action.type) {
    case 'SELECT_SPACE':
      return { ...state, spaceIndex: action.payload }; //
    case 'TOGGLE_INVOICE':
      return { ...state, showInvoice: !state.showInvoice }; //
    case 'NEXT_SPACE':
      const total = state.spaces.filter(s => s.group === state.activeGroup).length; //
      return {
        ...state,
        spaceIndex: (state.spaceIndex + 1) % total // Modulo index wrap protection
      };
    default:
      return state;
  }
}

export default function EnterpriseBookingsGrid() {
  const [state, dispatch] = useReducer(reducer, initialState);

  const activeSpace = state.spaces[state.spaceIndex]; //

  return (
    <div style={{ padding: '24px', border: '3px solid black', background: '#fff', margin: '20px' }}>
      <h3>John Larsen's Bookings Grid 🗺️</h3>
      
      <div style={{ background: '#f5f5f5', padding: '15px', borderRadius: '4px' }}>
        <h5>Selectable Spaces List:</h5>
        {state.spaces.map((s, idx) => (
          <button 
            key={s.id}
            onClick={() => dispatch({ type: 'SELECT_SPACE', payload: idx })}
            style={{ 
              display: 'block', 
              margin: '5px 0',
              background: idx === state.spaceIndex ? 'lightblue' : '#fff'
            }}
          >
            {s.title}
          </button>
        ))}
      </div>

      <div style={{ marginTop: '15px' }}>
        <button onClick={() => dispatch({ type: 'NEXT_SPACE' })}>Next Space ──►</button>
        <button onClick={() => dispatch({ type: 'TOGGLE_INVOICE' })} style={{ marginLeft: '10px' }}>
          {state.showInvoice ? "Hide Invoice details" : "Show Invoice details"}
        </button>
      </div>

      {state.showInvoice && (
        <div style={{ marginTop: '15px', padding: '10px', background: '#e8f5e9', border: '1px solid green' }}>
          <p>Charge Rate: ${activeSpace?.rate}/hour</p>
        </div>
      )}
    </div>
  );
}
```

---

### Production Project 16: Shared Global State Dispatcher Context

#### Folder Structure
```text
global-dispatch-system/
├── src/
│   ├── context/
│   │   └── GlobalDispatchContext.js
│   └── App.js
```

#### File Name: `GlobalDispatchContext.js`
```javascript
import React, { createContext, useReducer, useContext } from 'react'; //

const SecuritySessionContext = createContext(null);

const initialState = { clearanceZone: "Level_1" };

function reducer(state, action) {
  switch (action.type) {
    case 'ELEVATE_SECURITY':
      return { clearanceZone: "Level_ROOT_Admin" };
    default:
      return state;
  }
}

// Global Provider abstraction wrapping dispatch cleanly
export function SecurityProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <SecuritySessionContext.Provider value={{ state, dispatch }}>
      {children}
    </SecuritySessionContext.Provider>
  );
}

// Custom consumption hook
export function useSecurity() {
  return useContext(SecuritySessionContext); //
}
```

---

# SECTION 5: MASTER CLASS DESIGN PHILOSOPHY

### Definition
**`useReducer`** ek dynamic state management built-in React Hook hai jo standard functional components ke state transitions ko ek **centralized, pure reducer function** ke zariye collocate aur predictable format mein manage karne ka infrastructure design configure karta hai.

---

### Easy Hinglish Explanation
Bhai, socho jab hum functional component ke andar 4 ya 5 variables ko manage karne ke liye multiple `useState` calls likhte hain, toh component ka code bohot messy ho jata hai. Har ek button click ya event par hume 3-4 alag updater functions ko bar-bar call karna padta hai.

**useReducer** is problem ko ek clean, predictable design pattern se solve karta hai.

Isme hum states ko direct mutate karne ke bajaye ek **Action** dispatch karte hain (jaise: *"Bhai, counter ko increment kar do!"*). Component is request ko aage humare **Reducer Function** ko bhejta hai, jo purane state aur dynamic action ko lekar, calculation chala kar ek brand new safe state return kar deta hai!

---

### Internal Working
React functional components ke under-the-hood execution ko phases mein split karta hai:
1. **Initial Mounting Phase**: `useReducer` call runtime parameter check coordinate karta hai. Memory registers mein reducer pointer compile kiya jata hai.
2. **Dispatch Actions Execution**: Component level se dispatch execute karne par, event loop immediately state mutations pipeline trigger karta hai.
3. **Pure Reducer Computation**: React current state snapshot aur current action parameters ko reducer function mein inject karta hai. Reducer pure mathematical clone return karta hai jisse reference mismatch check evaluate trigger parameter pass ho sake.
4. **Virtual DOM Reconciliation**: React computed output value compare karta hai, changes evaluate hone par, only changed elements coordinates ko virtual updates se paint blocks par schedule trigger kara deta hai.

---

### Behind the Scenes: Fiber Engine memory structures
```text
Fiber Node (Component Instance)
       │
       ├─► memoizedState (Index registry)
       │         │
       │         ▼
       │   [ state, dispatch_function_identity ]  ◄── Dispatch remains strictly stable
```

---

### ASCII Diagram: useReducer Data Flow pipeline

```text
       [ Event Triggered in UI ]
                   │
                   ▼
      [ Dispatch action Dispatched ] ──► (type & payload)
                   │
                   ▼
       [ Reducer function runs ] ──► (state, action) => newState
                   │
         ┌─────────┴─────────┐
         ▼                   ▼
   [ Compares State ]   [ Return New State ]
         │                   │
         └──► Identical? ────┼──► YES: Skip Rerender
                             │
                             └──► NO: Trigger Virtual DOM Update
```

---

### Flow Diagram: useReducer Hook Argument Resolution
```text
[useReducer Hook Invoked]
           │
           ▼
[Has Component mounted previously?]
    ├── NO  ──► Is third argument (init function) specified?
    │             ├── YES ──► State = init(initialArg)
    │             └── NO  ──► State = initialArg
    │
    └── YES ──► Skip arguments evaluation entirely, return current state snapshot
```

---

### When NOT to use useReducer
1. **Simple independent state fields**: Simple text values ya true/false togglers ke liye `useState` humesha standard cleaner approach hai.
2. **Global asynchronous actions**: API interactions or asynchronous state transfers belong inside Specialized libraries or query frameworks directly, NOT inside synchronous useReducer blocks.

---

### Common Mistakes
1. **Directly mutating state objects**: `state.count = 5` likhna pure functions ki execution constraints violate karta hai, jisse React rendering update miss kar deta hai.
2. **Missing default return fallback inside Reducer**: Default condition me if state return na kiya jaye, toh undefined state evaluate parameters cause un-caught compiler failures.

---

### Best Practices
1. Maintain highly descriptive case titles (Action types jaise SCREAMING_SNAKE_CASE: `'ELEVATE_SECURITY'`) to eliminate overlapping issues.
2. Decouple action parameters validation logic from UI using helper abstractions.

---

# SECTION 6: STRICT INTERVIEW MODULE (65 QUESTIONS)

Bhai, useReducer aur professional enterprise level optimizations ke har ek interview scenario ko ace karne ke liye questions series ko standard segments mein customize kiya hai:

---

## 1. Beginner Interview Questions (1-15)

### Q1: What is the main purpose of `useReducer` in React?
*   **Professional English Answer**: `useReducer` is a React state management hook designed to organize complex state transitions synchronously by decoupling state updates logic from components UI files and grouping it inside a single reducer function.
*   **Easy Hinglish Explanation**: `useReducer` ka use complex state logic ko organize karne ke liye kiya jata hai. Yeh state changes ke steps ko components se alag karke ek central place par collocate kar deta hai.
*   **Follow-up Questions**:
    1. When is useReducer chosen over useState?
    2. What does array destructuring return when useReducer is called?

---

### Q2: What parameters does useReducer hook accept?
*   **Professional English Answer**: It accepts three parameters: the `reducer` function, the `initialArg` value, and an optional `init` function used for lazy state initialization.

---

### Q3: What is the role of a "dispatch" function?
*   **Professional English Answer**: `dispatch` is a stable identity method returned by useReducer used to dispatch action objects to the reducer, triggering updates evaluations.

---

### Q4: Does useReducer's dispatch trigger re-render if the returned state is identical?
*   **Professional English Answer**: No, React performs Object.is comparisons, skipping the component re-render pass if the returned state is referentially identical.

---

### Q5: Why is action.type usually defined as a string?
*   **Professional English Answer**: Strings are easily serializable, highly descriptive, and make tracking state changes in logging tools very predictable.

---

### Q6: Can we use useReducer without an action object?
*   **Professional English Answer**: Yes, we can pass primitive values to dispatch, but following action object conventions is highly recommended for scalability.

---

### Q7: What value does useReducer return when called?
*   **Professional English Answer**: It returns an array of exactly two elements: the current state value, and the stable dispatch function.

---

### Q8: What does dispatch identity stability mean?
*   **Professional English Answer**: It means React guarantees the reference pointer of the dispatch function remains unchanged across all renders, making it safe to omit from effect dependency arrays.

---

### Q9: What happens if we omit the default case inside a switch-case reducer?
*   **Professional English Answer**: If an unknown action is dispatched, useReducer will return undefined, which can crash component renderings. Always return state as fallback.

---

### Q10: Can we declare custom hooks with useReducer inside?
*   **Professional English Answer**: Yes, custom hooks can leverage useReducer internally to manage encapsulated state flows.

---

### Q11: Where should the Reducer function be declared?
*   **Professional English Answer**: It should be declared outside the component scope or in a separate file to prevent recreation on every single render cycle.

---

### Q12: Is useReducer a replacement for Redux?
*   **Professional English Answer**: No, useReducer manages local component or component-subtree state, whereas Redux provides global state caching, middleware, and DevTools for massive systems.

---

### Q13: Does useReducer support asynchronous side-effects natively inside reducers?
*   **Professional English Answer**: No, reducers must remain strictly synchronous pure functions. Async logic must reside inside event handlers or actions transition pipelines.

---

### Q14: What is the benefit of passing dispatch down instead of callbacks?
*   **Professional English Answer**: Since dispatch has a stable identity, it does not cause child components to re-render, eliminating optimization breaks.

---

### Q15: What is the difference between initialArg and lazy init?
*   **Professional English Answer**: `initialArg` sets the state directly, while lazy init computes the initial state dynamically by passing the argument to an init function on initial mount.

---

## 2. Intermediate Interview Questions (16-30)

### Q16: Why are React Reducer functions strictly required to be "Pure Functions"?
*   **Professional English Answer**: React assumes reducers are pure to perform predictable rendering reconciliations. Impure functions mutating external state variables introduce unpredictable UI behavior and make testing extremely unreliable.
*   **Easy Hinglish Explanation**: Reducer ko pure isliye rakha jata hai taaki dynamic comparison matches safe rahen. Agar reducer direct memory properties badlega (mutate karega), toh component state out-of-sync ho jayegi.
*   **Follow-up Questions**:
    1. How does StrictMode capture un-pure reducers during development?
    2. What are the core benefits of pure functions in state tracking?

---

### Q17: How do you perform state initialization lazily inside useReducer?
*   **Professional English Answer**: By passing an init function as the third argument: `useReducer(reducer, baseVal, init)`. State sets to `init(baseVal)` synchronously.

---

### Q18: What is the role of action.payload in useReducer?
*   **Professional English Answer**: The payload contains additional data (like inputs, integers, selected objects) needed by the reducer to perform state transitions.

---

### Q19: How can we implement state resetting dynamically with useReducer?
*   **Professional English Answer**: By dispatching a reset action type and returning the output of our initializer function inside the target case.

---

### Q20: What is the risk of mutating state directly: `state.value = newValue` inside a reducer?
*   **Professional English Answer**: Direct mutation does not change the object's reference. Since React performs shallow pointer comparisons, it won't detect the change and will fail to trigger a re-render.

---

### Q21: Explain how spread operator `...state` prevents state mutation.
*   **Professional English Answer**: The spread operator shallow-copies all top-level keys into a brand new object, allowing us to safely override targeted fields immutably.

---

### Q22: Why doesn't useReducer support `state = initialState` parameter syntax popularized by Redux?
*   **Professional English Answer**: React's useReducer initial value often depends on dynamic component props, so it is specified during the hook's invocation instead.

---

### Q23: How can we conditionally invoke hooks inside our reducer functions?
*   **Professional English Answer**: You cannot. Calling hooks inside reducers violates the Rules of Hooks and causes immediate runtime errors.

---

### Q24: What scheduling differences exist between useReducer dispatch and useState setter?
*   **Professional English Answer**: Structurally none. Both schedule batch updates asynchronously to optimize rendering passes.

---

### Q25: Can useReducer manage arrays of objects immutably?
*   **Professional English Answer**: Yes, using array mapping and filtering helpers combined with spread syntax to construct a new array reference.

---

### Q26: What does table 3.2 explain about subsequent useReducer arguments parsing?
*   **Professional English Answer**: On subsequent renders, React completely ignores the arguments passed to useReducer, returning only the updated state and the same dispatch function.

---

### Q27: How does write-testing pure reducers differ from testing standard component UI files?
*   **Professional English Answer**: Reducers are simple Javascript functions. Testing them only requires asserting expected outputs for given inputs without mock-rendering any DOM elements.

---

### Q28: Can we declare multiple useReducer hooks inside a single component?
*   **Professional English Answer**: Yes, React distinguishes between them based on their top-level declaration order.

---

### Q29: Why is dispatching actions cleaner than calling individual state updaters?
*   **Professional English Answer**: Instead of writing separate event handlers for different updates, dispatch lets you describe actions with clear intent while delegating the update logic to the reducer.

---

### Q30: What is the recommended way to handle errors for unrecognized action types in reducers?
*   **Professional English Answer**: Throwing a descriptive runtime Error: `throw new Error("Unknown action type")` inside the switch default case.

---

## 3. Advanced Interview Questions (31-45)

### Q31: How does React's internal Fiber engine manage useReducer hooks registers under the hood?
*   **Professional English Answer**: React attaches hook states sequentially inside the active component's Fiber linked list. The useReducer Fiber node stores the current state at index 0 and the dependency mapping at index 1. Dispatch actions are queued, let React compute updates during standard rendering phases synchronously.
*   **Easy Hinglish Explanation**: React internal Fiber linked list me useReducer cells allocate karta hai. useReducer ke memory coordinates me index 0 par resolved state aur index 1 par standard transitions queue maintain hote hain predictability secure rakhne ke liye.
*   **Follow-up Questions**:
    1. How does the engine handle conditional Hook execution blocks?
    2. What index location does useState use in comparison?

---

### Q32: Explain the concept of Reducer Composition and how it helps scale state logic.
*   **Professional English Answer**: Reducer composition splits a large state tree into smaller sub-reducer functions. A top-level reducer receives the global state, delegating updates of specific fields to these modular, focused sub-reducers.

---

### Q33: How does React 19 useActionState differ from useReducer conceptually?
*   **Professional English Answer**: Conceptually similar, but `useActionState` manages the state of Actions and allows async side-effects natively, while `useReducer` manages UI state and must remain strictly synchronous and pure.

---

### Q34: What is the behavior of useReducer when used under Concurrent Mode's time-slicing?
*   **Professional English Answer**: Concurrent rendering can pause and resume rendering phases. useReducer's pure function requirement guarantees that paused or aborted renders won't produce side-effects or state inconsistencies.

---

### Q35: How does useReducer prevent state synchronization issues compared to using multiple useState hooks?
*   **Professional English Answer**: Since useReducer updates are consolidated in a single reducer, you avoid out-of-sync updates when state fields are interdependent.

---

### Q36: Can we call useReducer recursively or nested inside other hooks?
*   **Professional English Answer**: No, useReducer must run unconditionally at the top level of function components.

---

### Q37: How do you handle deep nested object updates inside useReducer immutably?
*   **Professional English Answer**: By using nested spread operators or structural composition helpers, cloning intermediate level objects to prevent reference mutation.

---

### Q38: Why does useReducer optimize performance for deeply nested component subtrees?
*   **Professional English Answer**: Because you can pass the stable `dispatch` function down the tree instead of creating and passing individual callbacks that trigger updates.

---

### Q39: What is the standard behavior when dispatch is called during the rendering phase?
*   **Professional English Answer**: Dispatching actions during render causes React to instantly trigger an early re-render loop, which can throw maximum update depth errors if not guarded.

---

### Q40: How do memory leak profiles get managed when unmounting components containing useReducer?
*   **Professional English Answer**: On unmounting, the Fiber node is deleted, freeing up state references and letting Garbage Collection release memory.

---

### Q41: Explain how `combineReducers` maps sub-reducers automatically in Redux.
*   **Professional English Answer**: It wraps modular reducers into a single combinatorial function, calling each with its respective state slice and returning a unified state object.

---

### Q42: Is there a performance difference between switch-case and if-else branches in reducers?
*   **Professional English Answer**: Practically none, but switch-case scales better and is much more readable for complex sets of actions.

---

### Q43: How can useReducer prevent hydration mismatch bugs during SSR?
*   **Professional English Answer**: By initializing state with pure inputs only (never `Math.random` or date parameters) so client and server initial renders match perfectly.

---

### Q44: What are "Action Creators" and why are they used?
*   **Professional English Answer**: Functions that return action objects. They encapsulate action creation logic and document all possible actions in one place.

---

### Q45: How can a custom Context bridge useReducer state to deep consumers safely?
*   **Professional English Answer**: By placing useReducer's state and dispatch as values in a Context Provider, letting consumers subscribe directly.

---

## 4. Scenario-Based Questions (46-55)

### Q46: Scenario: Your component triggers multiple API updates. Using useState isLoading, error, and data scattered. How do you refactor with useReducer?
*   **Professional English Answer**: Consolidate the state into a single object `{ isLoading: false, error: null, data: [] }`. Define a reducer that updates these properties in response to clear actions like request, success, and failure, ensuring all related fields update together predictability.
*   **Easy Hinglish Explanation**: Multiple state hooks use karne par updates mismatch ho sakte hain. Hum saare status updates ko useReducer ke single object state me groups karte hain, jisse error aur loading state humesha sync rahen.

---

### Q47: Scenario: Clicking "Delete" deletes the task in the console, but the UI doesn't update. What reducer implementation mistake occurred?
*   **Professional English Answer**: The reducer is likely mutating the array directly via `push()` or `splice()`. Since the array reference is identical, React skips rendering. Return a new copied array instead.

---

### Q48: Scenario: Child buttons re-render endlessly on every parent count change, despite being wrapped in React.memo. Why did useReducer optimization fail?
*   **Professional English Answer**: The parent is likely passing inline callbacks or newly created functions as props instead of passing the stable `dispatch` function.

---

### Q49: Scenario: Toggling options inside checkout forms freezes the UI. Profiling shows the bottleneck is getWeek recalculation. How do you resolve this?
*   **Professional English Answer**: Pass `getWeek` as the third argument (lazy initializer) to useReducer instead of calling it directly during render. This ensures it executes once on mount.

---

### Q50: Scenario: Forms reset unexpectedly when validation triggers memoized list runs. Why?
*   **Professional English Answer**: Form submission trigger reloads the page. Calling `e.preventDefault()` inside handler actions preserves memory caches.

---

### Q51: Scenario: Toggling list categories captures old, stale state values. How do you trace it?
*   **Professional English Answer**: Check if the reducer is missing the spread operator, causing it to drop existing state properties on updates.

---

### Q52: Scenario: Wrap-around logic for list indexes wraps at incorrect targets. How do you validate?
*   **Professional English Answer**: The wrap calculation should use the dynamic list count of the current group: `(index + 1) % count`.

---

### Q53: Scenario: Sibling panels lose data synchronization during network transitions. How do you ensure safety?
*   **Professional English Answer**: Lift the state and reducer up to the closest shared parent, passing down state and dispatch as props.

---

### Q54: Scenario: Reducer updates trigger unexpected type warnings in TypeScript. Why?
*   **Professional English Answer**: The returned state type from a case likely mismatches the type of your initial state. Use strict type annotations.

---

### Q55: Scenario: Multiple simultaneous action dispatches drop values. How do you handle transaction safety?
*   **Professional English Answer**: For sequential transactions that can perform side-effects, migrate the updates to React 19's `useActionState`.

---

## 5. Live Coding Questions (56-60)

### Q56: Code a basic Increment Counter using useReducer.
```jsx
import React, { useReducer } from 'react';

const reducer = (state, action) => {
  return action.type === 'inc' ? { val: state.val + 1 } : state;
};

export default function App() {
  const [state, dispatch] = useReducer(reducer, { val: 0 });
  return <button onClick={() => dispatch({ type: 'inc' })}>{state.val}</button>;
}
```

---

### Q57: Code a standard WeekPicker with lazy initialization.
```jsx
import React, { useReducer } from 'react';

const initWeek = (date) => ({ active: new Date(date) });
const reducer = (state, action) => {
  return action.type === 'set' ? initWeek(action.payload) : state;
};

export default function Picker() {
  const [week, dispatch] = useReducer(reducer, "2026-08-04", initWeek);
  return <p>{week.active.toDateString()}</p>;
}
```

---

### Q58: Code an immutable Array task status toggler.
```jsx
import React, { useReducer } from 'react';

const reducer = (state, action) => {
  return action.type === 'toggle' ? state.map(t => t.id === action.id ? { ...t, done: !t.isDone } : t) : state;
};
```

---

### Q59: Code an uncontrolled form input with dynamic useReducer updates.
```jsx
import React, { useReducer, useRef } from 'react';

const reducer = (state, action) => ({ text: action.payload });

export default function Form() {
  const [state, dispatch] = useReducer(reducer, { text: "" });
  const ref = useRef(null);

  return (
    <form onSubmit={(e) => { e.preventDefault(); dispatch({ payload: ref.current.value }); }}>
      <input ref={ref} />
      <p>{state.text}</p>
    </form>
  );
}
```

---

### Q60: Code a global dispatcher sharing state via Context.
```jsx
import React, { createContext, useReducer } from 'react';

const DispatchContext = createContext(null);
const reducer = (state, action) => state;

export function Provider({ children }) {
  const [state, dispatch] = useReducer(reducer, { value: 0 });
  return <DispatchContext.Provider value={{ state, dispatch }}>{children}</DispatchContext.Provider>;
}
```

---

## 6. Debugging Scenarios (61-65)

### Q61: Debug this code: App crashes with un-caught TypeError.
```jsx
// 🔴 Buggy Code
function reducer(state, action) {
  if (action.type === 'inc') {
    return { val: state.val + 1 };
  }
  // Missing fallback condition!
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
function reducer(state, action) {
  switch (action.type) {
    case 'inc':
      return { val: state.val + 1 };
    default:
      return state; // Safe default return
  }
}
```
*   **Reasoning**: If a reducer doesn't return state in its fallback block, it returns `undefined`, which crashes the app on subsequent renders.

---

### Q62: Debug this code: UI does not update on clicking delete task button.
```jsx
// 🔴 Buggy Code
function reducer(state, action) {
  if (action.type === 'delete') {
    // Array mutation directly!
    state.tasks.splice(action.index, 1); 
    return state;
  }
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
function reducer(state, action) {
  if (action.type === 'delete') {
    return {
      ...state,
      // Filter returns a new array reference immutably
      tasks: state.tasks.filter((t, i) => i !== action.index) 
    };
  }
  return state;
}
```
*   **Reasoning**: Direct mutation does not change the array reference, causing React to skip rendering. Immutably return a new array instead.

---

### Q63: Debug this code: Input focus is lost on typing.
```jsx
// 🔴 Buggy Code
export default function App() {
  const [state, dispatch] = useReducer(reducer, { text: "" });
  
  // Component declared inside parent component body!
  const InnerInput = () => <input value={state.text} onChange={(e) => dispatch({ payload: e.target.value })} />;
  
  return <InnerInput />;
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
function InnerInput({ val, onUpdate }) {
  return <input value={val} onChange={onUpdate} />;
}

export default function App() {
  const [state, dispatch] = useReducer(reducer, { text: "" });
  return <InnerInput val={state.text} onUpdate={(e) => dispatch({ payload: e.target.value })} />;
}
```
*   **Reasoning**: Declaring components inside another component forces React to destroy and recreate the DOM subtree on every render, losing focus.

---

### Q64: Debug this code: useReducer arguments throw undefined error on SSG/SSR.
```jsx
// 🔴 Buggy Code
const [week, dispatch] = useReducer(reducer, "2026-08-04", getWeek(new Date())); // Direct call execution!
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
const [week, dispatch] = useReducer(reducer, "2026-08-04", getWeek); // Pass function reference
```
*   **Reasoning**: You must pass the function reference `getWeek` as the initializer, not execute it immediately during render.

---

### Q65: Debug this code: Component freezes with infinite re-rendering loops.
```jsx
// 🔴 Buggy Code
export default function App() {
  const [state, dispatch] = useReducer(reducer, { value: 0 });
  // Calling dispatch directly in render!
  dispatch({ type: 'inc' }); 
  return <p>{state.value}</p>;
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
export default function App() {
  const [state, dispatch] = useReducer(reducer, { value: 0 });
  
  return (
    <div>
      <p>{state.value}</p>
      <button onClick={() => dispatch({ type: 'inc' })}>Increment</button>
    </div>
  );
}
```
*   **Reasoning**: Dispatching actions directly inside the render path triggers synchronous re-renders, causing infinite loop page crashes.

---

## 7. Educational Assessment & Study Guides

### Mini Assignment
1. Vite use karke ek task logging dashboard build karein.
2. useReducer se categories (isLoading, data, error) states manage karein.
3. Actions and dynamic payload triggers log karke dry run parameters verify karein.

---

### Practice Questions
1. `useReducer` Fiber linked list node allocations index maps structures summarize karein.
2. `useActionState` (React 19) aur `useReducer` sync state execution differences ko clear karein.

---

### Multiple Choice Questions (MCQs)

1. **What is the mathematical signature of a Reducer function?**
    * (A) `(action) => state`
    * (B) `(state, action) => newState`
    * (C) `(initArg, init) => void`
    * *Correct Answer: (B)*

2. **How does lazy initialization execute inside useReducer?**
    * (A) Runs on every component render cycle
    * (B) Runs once on initial mount by passing base argument to an init function
    * (C) Runs after paint deferred
    * *Correct Answer: (B)*

---

### Revision Notes
* ** 예측 update**: useReducer manages complex interdependent state parameters cleanly.
* **Immutability rules**: Always clone existing state using spread operator inside cases.

---

### Cheat Sheet
```jsx
// Setup useReducer
const [state, dispatch] = useReducer(reducer, initialState); 

// Lazy initialized setups
const [week, dispatch] = useReducer(reducer, "2026-08-04", getWeek); 
```


