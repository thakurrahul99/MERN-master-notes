# REACT React.memo MASTERCLASS 🚀

React functional applications ko high-performance, robust, aur production-ready banane ke liye standard optimization rules ke according hume ek powerful optimization mechanism diya gaya hai—**`React.memo`**. 

Jab hamare pass complex component hierarchies hote hain jahan parent component ke state change hone se poora child tree baar-baar bina kisi badlav ke re-render hota hai, tab `React.memo` hume props ki checking karke unnecessary re-renders ko pure control ke sath skip karne ki capability deta hai.

Chalo bhai, pure **"Examples First"** approach ke sath custom optimizations aur real production applications ke zariye is pure optimization engine ko master karte hain!

---

## THE COMPARISON MATRICES 📊

Aage badhne se pehle in chaar essential comparison matrices ko dhyan se samajh lo taaki tumhare rendering optimization decisions perfect rahein:

### Table 1: React.memo vs Normal Component
| Feature | Normal Component | React.memo Wrapped Component |
| :--- | :--- | :--- |
| **Default Rendering Behavior** | Parent component ke re-render hone par, child component bhi unconditionally re-render hota hai, chahe props badlein ya na badlein. | Parent ke re-render hone par tabhi re-render hota hai jab component ke incoming **props change** hote hain. |
| **Internal Checking** | Koi checking run nahi hoti. Virtual DOM updates directly schedule kiye jate hain. | React automatic props ka **Shallow Comparison** (`Object.is`) chalata hai render skip karne se pehle. |
| **Performance Overhead** | Initial mount ya simple components me minimal checks overhead hota hai. | Props compare karne ka ek chota sa checking cost (overhead) lagta hai, isliye sirf medium/heavy components par hi use karte hain. |
| **Class Component Equivalent** | Standard `React.Component` class. | `React.PureComponent` jo automatic props compare karta hai. |

### Table 2: React.memo vs useMemo
| Feature | `React.memo` (API) | `useMemo` (Hook) |
| :--- | :--- | :--- |
| **Type** | Yeh ek **Higher-Order Component (HOC)** hai jo complete functional component ko wrap karta hai. | Yeh ek **React Hook** hai jo component ke andar direct variables ya computational results ko hold karta hai. |
| **Optimization Target** | Pure component ki unnecessary re-rendering ko skip karna. | Kisi expensive calculation/algorithm ka evaluation result cache karna. |
| **Execution Phase** | Render execution phase se pehle, prop values evaluate karte waqt check hota hai. | Component execution ke dauran, active rendering call stack ke andar execute hota hai. |

### Table 3: React.memo vs useCallback
| Feature | `React.memo` (HOC) | `useCallback` (Hook) |
| :--- | :--- | :--- |
| **Purpose** | UI components ke execution block ko save karta hai. | Instantiated function definitions ke memory reference (identity) ko preserve karta hai. |
| **Dependency Mechanism** | Props values check karke dynamically comparison rules handle karta hai. | Dependency array `[deps]` badalne par hi function recreate karta hai. |
| **Synergy (Combo)** | Jab child component `React.memo` se wrapped ho, tabhi callback functions pass karne par output deliver karta hai. | Children me function properties leak hone par unka reference stable rakhne me help karta hai. |

### Table 4: Memoized vs Non-Memoized Component
| Feature | Non-Memoized Component | Memoized Component (`React.memo`) |
| :--- | :--- | :--- |
| **Rendering Frequency** | Extreme. Parent ki har state tick par component function run hota hai. | Controlled. Sirf strict prop changes par hi trigger hota hai. |
| **Wasteful Renders (Wasted Time)** | Performance profiler me high time waste display hota hai. | Zero wasted renders, performance profiles hamesha clean aur optimal rehte hain. |

---

# SECTION 1: THE BEGINNER'S PLAYGROUND (5 EXAMPLES)

Chalo bhai, sabse pehle 5 basic aur easy-to-understand code examples se shuru karte hain jahan hum static aur standard prop values ko optimized format me render karna sikhenge.

---

### Beginner Example 1: Static Profile Card Renderer

#### File Name: `ProfileCardSimple.js`
```javascript
import React from 'react'; //

// 1. Defining a raw presentational functional component
function ProfileCard({ username, role }) {
  console.log(`🎨 [Render] ProfileCard for ${username} is evaluating...`); // Tracking execution
  return (
    <div style={{ padding: '16px', border: '1px solid #ddd', borderRadius: '8px', margin: '10px 0' }}>
      <h4>User Identity Card</h4>
      <p>Alias Name: <strong>{username}</strong></p>
      <p>Security Class: <strong>{role}</strong></p>
    </div>
  );
}

// 2. Exporting with React.memo wrapping to perform shallow comparison
export default React.memo(ProfileCard); //
```

#### Parent Component File Name: `DashboardApp.js`
```javascript
import React, { useState } from 'react'; //
import ProfileCardSimple from './ProfileCardSimple';

export default function DashboardApp() {
  const [clickCount, setClickCount] = useState(0); // Parent state update simulator

  return (
    <div style={{ padding: '24px', border: '2px solid #ccc' }}>
      <h3>Enterprise Dashboard</h3>
      <p>Parent Interactions Count: <strong>{clickCount}</strong></p>
      <button onClick={() => setClickCount(prev => prev + 1)}>Increment Interactions</button>

      {/* 
        React.memo ensures that ProfileCardSimple doesn't re-render 
        when DashboardApp's clickCount changes, because its props remain identical!
      */}
      <ProfileCardSimple username="ChaiLover" role="L3_Operator" />
    </div>
  );
}
```

##### Line-by-Line Explanation
1. `import React from 'react'`: Components run karne ke liye standard React module import kiya.
2. `function ProfileCard({ username, role })`: Presentational UI logic design kiya jo user props show karta hai.
3. `React.memo(ProfileCard)`: React.memo wrapper hook inject kiya, jo component ko cache memory me register karta hai aur reference comparisons actively monitors karta hai.
4. `const [clickCount, setClickCount] = useState(0)`: Parent state trigger banaya jo component updates trigger karata hai.

##### Browser Output
* Screen par ek counter variable layout, ek action button aur ek card structure show hota hai. Parent button par click karne se parent ka interaction counter to badhta hai, par console logs me `"ProfileCard for ChaiLover... evaluating"` dobara fire nahi hota, kyunki re-render blocks skip ho jate hain!

##### Why React.memo is used here
* Jab parent state update hoti hai tab child component ko referential unchanged props ke sath useless renders se protect karne ke liye ise apply kiya hai.

##### Better Version
* Static structures are completely safe, references comparisons directly inline handles perform karte hain.

##### Dry Run
1. **Initial Mount**: Parent rendering trigger karta hai. `ProfileCardSimple` pehli baar render hota hai aur screen display layout complete paint hota hai. Console me `"ProfileCard for ChaiLover..."` log print hota hai.
2. **User Clicks Increment**: `clickCount` update hota hai, Parent component (DashboardApp) ka evaluation execute hota hai.
3. **Props Comparison**: React dekhta hai ki `ProfileCardSimple` ke pass pass kiye gaye attributes (`username="ChaiLover"`, `role="L3_Operator"`) exact same hain (Strict Equality checks run hote hain).
4. **Skipping Pass**: Rendering bypass ho jati hai. Child card evaluation run skip ho jata hai.

---

### Beginner Example 2: Static Page Header

#### File Name: `StaticHeader.js`
```javascript
import React from 'react';

function StaticHeader({ appTitle }) {
  console.log("🎨 [StaticHeader] evaluated."); // Check if execution happens
  return (
    <header style={{ background: '#f5f5f5', padding: '10px 20px', borderBottom: '2px solid #ccc' }}>
      <h2>System Terminal: {appTitle}</h2>
    </header>
  );
}

// Ensure the header doesn't recalculate on every menu toggle or side panels modifications
export default React.memo(StaticHeader); 
```

##### Why React.memo is used here
* Static layouts aur branding components ko dynamic parent updates (jaise form inputs ya clocks ticks) ke renders impacts se dur rakhne ke liye wrap kiya hai.

---

### Beginner Example 3: Color Circle Indicator

#### File Name: `ColorCircle.js`
```javascript
import React from 'react';

function ColorCircle({ activeColor }) {
  console.log(`🎨 [ColorCircle] evaluated for color: ${activeColor}`);
  return (
    <div style={{ display: 'flex', alignItems: 'center', margin: '10px 0' }}>
      <span style={{ 
        display: 'inline-block', 
        width: '24px', 
        height: '24px', 
        borderRadius: '50%', 
        backgroundColor: activeColor,
        marginRight: '10px'
      }} />
      <span>Selected Node: {activeColor}</span>
    </div>
  );
}

// Skips evaluation unless activeColor props shifts
export default React.memo(ColorCircle); 
```

---

### Beginner Example 4: Text Label Viewer

#### File Name: `TextLabelViewer.js`
```javascript
import React from 'react';

function TextLabelViewer({ captionText }) {
  console.log(`🎨 [TextLabelViewer] rendered: "${captionText}"`);
  return (
    <div style={{ padding: '8px', background: '#e0f7fa', color: '#006064', margin: '10px 0' }}>
      <strong>Caption snapshot:</strong> {captionText}
    </div>
  );
}

export default React.memo(TextLabelViewer);
```

---

### Beginner Example 5: Basic Numeric Value Badge

#### File Name: `ValueBadge.js`
```javascript
import React from 'react';

function ValueBadge({ totalLogs }) {
  console.log(`🎨 [ValueBadge] evaluated with logs count: ${totalLogs}`);
  return (
    <div style={{ display: 'inline-block', padding: '6px 12px', background: 'navy', color: '#fff', borderRadius: '15px' }}>
      Telemetry Buffer: {totalLogs} files
    </div>
  );
}

export default React.memo(ValueBadge);
```

---

# SECTION 2: INTERMEDIATE CHANNELS (5 EXAMPLES)

Chalo bhai, ab calculations complexity ko badhate hue custom equality comparators, stable callback properties with `useCallback`, aur standard arrays rendering pipelines ko dynamic examples ke sath explore karte hain.

---

### Intermediate Example 6: Custom Comparison Function (Avoiding Shallow reference issues)

#### File Name: `TelemetryGridItem.js`
```javascript
import React from 'react'; //

function TelemetryGridItem({ logData }) {
  console.log(`🎨 [Render] TelemetryGridItem updated: ${logData.nodeId}`);
  return (
    <div style={{ padding: '12px', border: '1px solid green', background: '#e8f5e9', margin: '8px 0' }}>
      <h5>Module ID: #{logData.nodeId}</h5>
      <p>Operational Status: <strong style={{ color: logData.isActive ? 'green' : 'red' }}>
        {logData.isActive ? "ACTIVE 🟢" : "OFFLINE 🔴"}
      </strong></p>
    </div>
  );
}

// 1. Defining a custom comparison function
function areTelemetryPropsEqual(prevProps, nextProps) {
  // Returns true if nextProps render same output as prevProps (skips rendering)
  return (
    prevProps.logData.nodeId === nextProps.logData.nodeId &&
    prevProps.logData.isActive === nextProps.logData.isActive
  );
}

// 2. Passing custom comparison function as second argument to React.memo
export default React.memo(TelemetryGridItem, areTelemetryPropsEqual); //
```

#### Parent Component File Name: `TelemetrySystem.js`
```javascript
import React, { useState } from 'react'; //
import TelemetryGridItem from './TelemetryGridItem';

export default function TelemetrySystem() {
  const [ticks, setTicks] = useState(0);
  const [log, setLog] = useState({ nodeId: 404, isActive: true }); // Object State

  return (
    <div style={{ padding: '24px', border: '2px dashed green' }}>
      <h4>Telemetry Controller Room</h4>
      <p>Continuous Tick Cycle: {ticks}</p>
      <button onClick={() => setTicks(t => t + 1)}>Execute System Tick</button>
      <button onClick={() => setLog({ nodeId: 404, isActive: false })} style={{ marginLeft: '10px' }}>
        In-activate Telemetry
      </button>

      {/* 
        Even though the parent creates a brand new object reference for `logData` 
        on ticks updates, areTelemetryPropsEqual checks properties and prevents re-renders!
      */}
      <TelemetryGridItem logData={log} />
    </div>
  );
}
```

##### Line-by-Line Explanation
1. `function areTelemetryPropsEqual(prevProps, nextProps)`: Custom validation rules design kiye jo previous aur next props objects ki targeted values compare karte hain.
2. `React.memo(TelemetryGridItem, areTelemetryPropsEqual)`: React engine ko custom function register karne ke liye direct reference pass kiya.
3. `prevProps.logData.nodeId === nextProps.logData.nodeId`: Properties compare kiye, references reference address bypass karke value evaluations check kiya.

##### Why React.memo is used here
* Javascript me objects and arrays references comparison par checks break ho jate hain. Custom memo comparison functions dynamic values ko directly nested level par evaluate karke referential leaks block karte hain.

---

### Intermediate Example 7: Callback stability with `useCallback`

#### File Name: `OptimizedActionTrigger.js`
```javascript
import React from 'react';

function OptimizedActionTrigger({ triggerClickCallback, buttonLabel }) {
  console.log(`🎨 [OptimizedActionTrigger] evaluated for label: ${buttonLabel}`);
  return (
    <button onClick={triggerClickCallback} style={{ padding: '10px 16px', margin: '5px' }}>
      Action: {buttonLabel}
    </button>
  );
}

// React.memo relies on triggerClickCallback reference equality to skip rendering
export default React.memo(OptimizedActionTrigger); 
```

#### Parent Component File Name: `ParentTriggerConsole.js`
```javascript
import React, { useState, useCallback } from 'react'; //
import OptimizedActionTrigger from './OptimizedActionTrigger';

export default function ParentTriggerConsole() {
  const [systemLogs, setSystemLogs] = useState([]);
  const [metricState, setMetricState] = useState(10);

  // useCallback caches function definition to maintain stable reference identity
  const dispatchAcknowledgeAction = useCallback(() => {
    console.log("⚡ Telemetry actions acknowledged via stable callback!");
  }, []); // Recreated strictly empty dependency array guarantees stable reference

  return (
    <div style={{ padding: '20px', border: '1px solid navy' }}>
      <h5>Metrics Controller Panel: {metricState}</h5>
      <button onClick={() => setMetricState(m => m + 5)}>Alter Metrics State</button>
      
      {/* ActionTrigger skips re-renders on metrics change because callback reference remains stable */}
      <OptimizedActionTrigger 
        triggerClickCallback={dispatchAcknowledgeAction} 
        buttonLabel="Acknowledge System Logs" 
      />
    </div>
  );
}
```

##### Why React.memo is used here
* Parent function components execute hone par inline declared functions ke address pointers badal jate hain. Un functions ko stable rakhne ke liye `useCallback` aur skip render flows ke liye `React.memo` ka combo coordinate kiya hai.

---

### Intermediate Example 8: Rendering elements using parent `useMemo` arrays

#### File Name: `CachedTaskItem.js`
```javascript
import React from 'react';

function CachedTaskItem({ taskTitle, level }) {
  console.log(`🎨 [CachedTaskItem] evaluated for task: ${taskTitle}`);
  return (
    <li style={{ padding: '8px', borderBottom: '1px solid #eee' }}>
      📋 Task: {taskTitle} | Priority: {level}
    </li>
  );
}

export default React.memo(CachedTaskItem);
```

#### Parent Component File Name: `ProjectSprintBoard.js`
```javascript
import React, { useState, useMemo } from 'react'; //
import CachedTaskItem from './CachedTaskItem';

export default function ProjectSprintBoard() {
  const [tickTracker, setTickTracker] = useState(0);
  const [tasks, setTasks] = useState([
    { id: 1, title: "Audit AWS cluster configurations", priority: "High" },
    { id: 2, title: "Clean PostgreSQL indices matrices", priority: "Medium" }
  ]);

  // Caching components elements nodes output with useMemo
  const renderedTaskItems = useMemo(() => {
    return tasks.map(t => (
      <CachedTaskItem key={t.id} taskTitle={t.title} level={t.priority} />
    ));
  }, [tasks]); // Re-computes elements strictly if tasks array gets mutated

  return (
    <div style={{ padding: '20px', border: '1px solid red' }}>
      <h5>Continuous Ticks: {tickTracker}</h5>
      <button onClick={() => setTickTracker(prev => prev + 1)}>Execute Tick</button>
      
      <ul>
        {renderedTaskItems} {/* Returns cached Virtual DOM elements snapshot */}
      </ul>
    </div>
  );
}
```

---

### Intermediate Example 9: List Item Renderer receiving structural props

#### File Name: `TelemetryRow.js`
```javascript
import React from 'react';

function TelemetryRow({ recordId, speed, timestamp }) {
  console.log(`🎨 [TelemetryRow] rendered with record ID: #${recordId}`);
  return (
    <tr style={{ background: '#fafafa' }}>
      <td>#{recordId}</td>
      <td>{speed} m/s</td>
      <td>{timestamp}</td>
    </tr>
  );
}

// Strict equality check prevents rows evaluation loops
export default React.memo(TelemetryRow);
```

---

### Intermediate Example 10: Deferred query suggestions box combined with React.memo

#### File Name: `DeferredSearchBox.js`
```javascript
import React from 'react';

function DeferredSearchBox({ searchWord }) {
  console.log(`🎨 [DeferredSearchBox] evaluating for search word: ${searchWord}`);
  return (
    <div style={{ padding: '12px', border: '1px solid orange', background: '#ffe0b2' }}>
      <h5>Cached query matched values:</h5>
      <p>Results matched for string: <strong>{searchWord || 'Awaiting queries...'}</strong></p>
    </div>
  );
}

// Prevents intermediate renders during urgent typing states updates
export default React.memo(DeferredSearchBox);
```

---

# SECTION 3: ADVANCED CHANNELS (3 EXAMPLES)

Chalo bhai, ab advanced profiling mechanisms, customized recursive structures, aur React 19 Compiler automatic memoizations structures ko investigate karte hain.

---

### Advanced Example 11: Render Performance Watcher Panel

#### File Name: `TelemetrySubtreeWatcher.js`
```javascript
import React, { useRef } from 'react'; //

function TelemetrySubtreeWatcher({ activeRecordsCount, metaData }) {
  const initialEvaluationsCount = useRef(0); //
  initialEvaluationsCount.current += 1; // Tracking how many times this functional block runs

  console.log(`🔬 [Profiler Subtree] Render cycles triggered count: ${initialEvaluationsCount.current}`);

  return (
    <div style={{ padding: '24px', border: '2px solid black', background: '#fafafa', margin: '15px 0' }}>
      <h3>Performance Metrics Tracker Panel</h3>
      <p>Telemetry Total Records: <strong>{activeRecordsCount}</strong></p>
      <p>System Region: <strong>{metaData.regionCode}</strong></p>
      <p style={{ color: 'red' }}>Total Evaluation Passes Run: <strong>{initialEvaluationsCount.current}</strong></p>
    </div>
  );
}

// Advanced Custom comparison protecting against inline anonymous object reference re-creation
const areSubtreePropsEqual = (prevProps, nextProps) => {
  return (
    prevProps.activeRecordsCount === nextProps.activeRecordsCount &&
    prevProps.metaData.regionCode === nextProps.metaData.regionCode
  );
};

export default React.memo(TelemetrySubtreeWatcher, areSubtreePropsEqual); //
```

##### Line-by-Line Explanation
1. `const initialEvaluationsCount = useRef(0)`: Mutable register use kiya render counts trace karne ke liye bina un-needed update loops trigger kiye.
2. `initialEvaluationsCount.current += 1`: Har render pass me render counter values directly modify memory address par coordinate register kiye.
3. `areSubtreePropsEqual`: Deep inline key patterns check logic implementation ki, jisse dynamic configurations evaluate triggers bypass rahen.

##### Why React.memo is used here
* Render patterns profiling logs create karne, unnecessary render bypass validation matrices confirm karne aur analytics tracking loops establish karne ke liye wraps models optimize kiya hai.

---

### Advanced Example 12: Reducer-Style Comparator for Nested Arrays

#### File Name: `NestedMetricsTable.js`
```javascript
import React from 'react'; //

function NestedMetricsTable({ dataNodes }) {
  console.log("🎨 [Render] NestedMetricsTable heavy tables evaluating...");
  return (
    <table style={{ width: '100%', border: '1px solid #333', borderCollapse: 'collapse' }}>
      <thead>
        <tr style={{ background: '#ddd' }}>
          <th>Node Name</th>
          <th>Metrics Index</th>
        </tr>
      </thead>
      <tbody>
        {dataNodes.map(node => (
          <tr key={node.nodeCode}>
            <td>{node.nodeCode}</td>
            <td>{node.readingsMetric}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}

// Deep Comparison utilizing arrays reduce helper computations
function areTablePropsEqual(prevProps, nextProps) {
  if (prevProps.dataNodes.length !== nextProps.dataNodes.length) return false; // Immediate checks

  // Compare every element values to avoid deep object mutations leaks
  return prevProps.dataNodes.every((prevNode, index) => {
    const nextNode = nextProps.dataNodes[index];
    return (
      prevNode.nodeCode === nextNode.nodeCode &&
      prevNode.readingsMetric === nextNode.readingsMetric
    );
  });
}

export default React.memo(NestedMetricsTable, areTablePropsEqual); //
```

---

### Advanced Example 13: Static Compilation Mock with React 19 Compiler

#### File Name: `AutomaticCompilerSimulation.js`
```javascript
import React, { useState } from 'react'; //

// In React 19, the React Compiler automatically memoizes components under the hood
// We don't need manual useMemo, useCallback, or React.memo wrappers!
function AutomaticCompilerSimulation({ consoleTitle }) {
  console.log(`🎨 [React 19 Auto-Memo] evaluated: ${consoleTitle}`);
  return (
    <div style={{ padding: '16px', background: '#eceff1', border: '1px solid #607d8b' }}>
      <h5>React 19 Native Auto-Memoized Engine 🚀</h5>
      <p>Branding Title: {consoleTitle}</p>
    </div>
  );
}

export default AutomaticCompilerSimulation; // Plain functional export!
// The build system (babel-plugin-react-compiler) automatically compiles this into static memo templates!
```

---

# SECTION 4: REAL PRODUCTION SUITES (3 EXAMPLES)

Chalo bhai, ab pure production-level systems (jaise John Larsen's Bookable Spaces system, Accio task list updates, aur heavy metrics arrays loaders) ko code structures ke sath design aur study karte hain.

---

### Production Project 14: John Larsen's Bookings Grid Selection Highlights

#### Folder Structure
```text
bookings-grid-memo/
├── src/
│   ├── components/
│   │   ├── HighlightedBookableCell.js
│   │   └── BookablesListView.js
│   └── App.js
```

#### File Name: `HighlightedBookableCell.js`
```javascript
import React from 'react'; //

// High-performance cell rendering skip triggers
function HighlightedBookableCell({ bookableInfo, isSelectedHighlight, updateSelectionTrigger }) {
  console.log(`🎨 [Render Cell] Bookable #${bookableInfo.id} evaluated. Selected: ${isSelectedHighlight}`);
  return (
    <div 
      onClick={() => updateSelectionTrigger(bookableInfo.id)}
      style={{ 
        padding: '16px', 
        margin: '8px 0', 
        background: isSelectedHighlight ? 'lightblue' : '#fff',
        border: `2px solid ${isSelectedHighlight ? 'blue' : '#ccc'}`,
        cursor: 'pointer',
        borderRadius: '4px'
      }}
    >
      <h5>{bookableInfo.title}</h5>
      <p>Cleared Room Rate: ${bookableInfo.rate}/hour</p>
    </div>
  );
}

// arePropsEqual custom comparisons preventing re-runs on unrelated selections updates
const areCellPropsEqual = (prevProps, nextProps) => {
  return (
    prevProps.isSelectedHighlight === nextProps.isSelectedHighlight &&
    prevProps.bookableInfo.id === nextProps.bookableInfo.id &&
    prevProps.updateSelectionTrigger === nextProps.updateSelectionTrigger // Works when wrapped in useCallback!
  );
};

export default React.memo(HighlightedBookableCell, areCellPropsEqual); //
```

#### File Name: `BookablesListView.js`
```javascript
import React, { useState, useCallback, useMemo } from 'react'; //
import HighlightedBookableCell from './HighlightedBookableCell';

export default function BookablesListView() {
  const [selectedId, setSelectedId] = useState(101);
  const [tickState, setTickState] = useState(0);

  const mockRooms = useMemo(() => [
    { id: 101, title: "Lecture Hall Booking Option", rate: 300 }, //
    { id: 102, title: "Games Room Rental Unit", rate: 150 }, //
    { id: 103, title: "Lounge Area Rental", rate: 80 }
  ], []); // Cached static array

  // Maintaining stable function identity prevents highlight cells from losing memo benefits
  const selectActiveRoom = useCallback((id) => {
    setSelectedId(id);
  }, []); // Stable dispatcher reference

  return (
    <div style={{ padding: '24px', border: '3px solid black', background: '#fff' }}>
      <h3>John Larsen's Bookable Spaces 🗺️</h3>
      <p>Parent clock ticks counter: {tickState}</p>
      <button onClick={() => setTickState(t => t + 1)}>Execute Parent Render</button>

      <div style={{ marginTop: '20px' }}>
        {mockRooms.map(room => (
          <HighlightedBookableCell 
            key={room.id}
            bookableInfo={room}
            isSelectedHighlight={room.id === selectedId}
            updateSelectionTrigger={selectActiveRoom}
          />
        ))}
      </div>
    </div>
  );
}
```

##### Why React.memo is used here
* Grid updates hone par, jis grid elements state cell variables ka visual focus parameters (selection matches) change nahi hua hai, un cells ko rendering evaluation passes run skip karwane ke liye is pattern ko apply kiya hai.

---

### Production Project 15: Accio To-do App Task Rows optimized to avoid list appends re-renders

#### Folder Structure
```text
accio-todo-memo/
├── src/
│   ├── components/
│   │   ├── AccioTaskRow.js
│   │   └── AccioDashboard.js
│   └── App.js
```

#### File Name: `AccioTaskRow.js`
```javascript
import React from 'react';

// AccioTaskRow wrapped in React.memo
function AccioTaskRow({ task, onToggleStatus, onDeleteTask }) {
  console.log(`🎨 [AccioTaskRow] evaluated for Node ID: #${task.id}`);
  return (
    <li className="task" style={{ 
      display: 'flex', 
      justifyContent: 'space-between', 
      padding: '12px', 
      borderBottom: '1px solid #eee',
      alignItems: 'center'
    }}>
      <span style={{ textDecoration: task.isDone ? 'line-through' : 'none' }}>
        {task.title}
      </span>
      <div>
        <button className="edit" onClick={() => onToggleStatus(task.id)}>Toggle</button> {/* */}
        <button className="delete" onClick={() => onDeleteTask(task.id)} style={{ marginLeft: '10px' }}>Delete</button> {/* */}
      </div>
    </li>
  );
}

// Compare props to avoid re-rendering untouched tasks on appends
export default React.memo(AccioTaskRow); 
```

#### File Name: `AccioDashboard.js`
```javascript
import React, { useState, useCallback } from 'react'; //
import AccioTaskRow from './AccioTaskRow';

export default function AccioDashboard() {
  const [tasks, setTasks] = useState([
    { id: 1, title: "Clean transactional audit logs", isDone: false }, //
    { id: 2, title: "Perform security regression checks", isDone: true }
  ]);
  const [inputVal, setInputVal] = useState("");

  const handleAppend = (e) => {
    e.preventDefault();
    if (!inputVal.trim()) return;
    setTasks(prev => [...prev, { id: Date.now(), title: inputVal, isDone: false }]); // Mutate safely
    setInputVal("");
  };

  // stable status updater reference prevents unnecessary re-rendering
  const toggleTaskStatus = useCallback((id) => {
    setTasks(prevTasks => prevTasks.map(t => 
      t.id === id ? { ...t, isDone: !t.isDone } : t
    ));
  }, []); 

  // stable delete handler reference prevents unnecessary re-rendering
  const deleteTask = useCallback((id) => {
    setTasks(prevTasks => prevTasks.filter(t => t.id !== id)); // Remove immutably
  }, []);

  return (
    <div className="add_tasks_section" style={{ padding: '24px', border: '3px solid #111', background: '#fff' }}>
      <h3>AccioJob Optimized Task Board 📋</h3>
      <form onSubmit={handleAppend}>
        <input value={inputVal} onChange={e => setInputVal(e.target.value)} placeholder="Queue task..." />
        <button type="submit" style={{ marginLeft: '10px' }}>Add Task</button>
      </form>

      <ul className="tasks_section" style={{ listStyle: 'none', padding: 0, marginTop: '20px' }}>
        {tasks.map(t => (
          <AccioTaskRow 
            key={t.id}
            task={t}
            onToggleStatus={toggleTaskStatus}
            onDeleteTask={deleteTask}
          />
        ))}
      </ul>
    </div>
  );
}
```

##### Why React.memo is used here
* Ek naya item append karne par array reference update hota hai. Bina `React.memo` ke purane 1000 items ke list records render hone lagte the, jo system ko slow karte the. Is pattern se purane rows rendering completely skipped rehti hai.

---

### Production Project 16: Telemetry Logs Analytics Grid

#### Folder Structure
```text
telemetry-analytics/
├── src/
│   ├── components/
│   │   ├── TelemetryCardItem.js
│   │   └── AnalyticsDashboard.js
│   └── App.js
```

#### File Name: `TelemetryCardItem.js`
```javascript
import React from 'react';

function TelemetryCardItem({ status, codeValue }) {
  console.log(`🎨 [TelemetryCardItem] evaluated with code: #${codeValue}`);
  return (
    <div style={{ padding: '16px', background: '#ffebee', border: '1px solid red', margin: '10px 0' }}>
      <h5>Metrics Node Cluster: #{codeValue}</h5>
      <p>Data Status: <strong>{status}</strong></p>
    </div>
  );
}

export default React.memo(TelemetryCardItem);
```

#### File Name: `AnalyticsDashboard.js`
```javascript
import React, { useState, useMemo } from 'react'; //
import TelemetryCardItem from './TelemetryCardItem';

export default function AnalyticsDashboard() {
  const [metricSum, setMetricSum] = useState(0);
  const [clusterInfo] = useState({ nodeKey: 909, serverStatus: "ONLINE" }); // Immutable state configurations

  return (
    <div style={{ padding: '24px', border: '3px solid red', background: '#fff' }}>
      <h3>Enterprise Analytics Platform 🛰️</h3>
      <p>Computed Metrics Sum: {metricSum}</p>
      <button onClick={() => setMetricSum(s => s + 10)}>Alter Computations</button>

      {/* Grid items skip evaluation during metric changes */}
      <TelemetryCardItem status={clusterInfo.serverStatus} codeValue={clusterInfo.nodeKey} />
    </div>
  );
}
```

---

# SECTION 5: DEEP-DIVE TECHNICAL ANALYSIS 🧠

---

### Definition
**`React.memo`** ek built-in React Higher-Order Component (HOC) optimization API hai jo functional components ke props ka structural checking process automate karke render skip checks manage karta hai.

---

### Easy Hinglish Explanation
Bhai, socho jab parent component ke state change hone par pure component re-renders trigger hote hain, toh hamare children components bina kisi prop badlav ke bhi execute hone lagte hain. Yeh waste process CPU cycles aur memory burn karata hai.

**React.memo** component ke charo taraf ek secure shield (wrapper) bitha deta hai. Parent re-render hone par, yeh automatic purane props aur naye props ka validation check chalata hai. Agar values exact same hain, toh bypass flag use karke component re-render pass skip karata hai!

---

### Internal Working: Virtual DOM & Shallow Comparisons
1. **Compilation Step**: Components runtime coordinates par register kiye jate hain.
2. **Prop Snapshot Cache**: React memory slots coordinates me previous component rendering tree props and output values map caches parameters design karta hai.
3. **Shallow Check Verification**: React engine `Object.is` check algorithms execute karta hai (shallow comparisons: `prevProps === nextProps` references comparison keys evaluation).
4. **Skip Render Scheduling**: Evaluation positive (`true`) matching resolve hone par rendering stack loop schedule run skip karata hai aur existing cached fiber template directly display karta hai.

---

### ASCII Diagram: React.memo Rendering Flow

```text
    Parent Render Event
            │
            ▼
    [ Passes Props to Component ]
            │
            ▼
    ┌───────────────────────────────────┐
    │   React.memo Shallow Comparison   │  ◄── Object.is(prev, next) checks
    └─────────────────┬─────────────────┘
                      │
            ┌─────────┴─────────┐
            ▼                   ▼
    Props Changed? Props UN-changed?
            │                   │
            ▼                   ▼
    [ Trigger Rerender ]   [ Skip Rendering! ]
            │                   │
            ▼                   ▼
    Compute New VDOM       Return Cached VDOM
```

---

### Flow Diagram: Memoization Decision Matrix
```text
[Component wrapped in React.memo is called]
                    │
                    ▼
[Is there custom comparison function?]
        ├── YES ──► Execute customFn(prevProps, nextProps)
        │             ├── Returns true  ──► Skip rendering, return cached output
        │             └── Returns false ──► Re-render component
        │
        └── NO  ──► Run React shallow comparison (Object.is)
                      ├── All props match  ──► Skip rendering
                      └── Prop mismatch    ──► Re-render component
```

---

### When NOT to use React.memo
1. **Simple Presentational Components**: Chote components jahan only single primitive elements display hote hain, wahan comparison checks performance degradation check loops cause kar sakti hain.
2. **Frequently Mutated Data Grid**: Jab properties index hamesha badalte hain (jaise millisecond timers counters grids), wahan `React.memo` automatic skip calculations cost waste karega.

---

### Common Mistakes
1. **Passing Anonymous Callback Functions inside props directly**: Parent scope me direct inline execution `onClick={() => setX()}` pass karne par rendering check bypass mismatch checks run generate karke, optimization features break karta hai.
2. **Mutating State objects inside nested arrays**: Shallow comparison object pointers values verify compare maps, mutations direct state checks bypass skips failures perform karati hain.

---

### Best Practices
1. Hamesha functions properties ko `useCallback` se stable identity configure karke transfer karein.
2. Decouple layout parameters variables check, configurations templates clear mapping levels.

---

# SECTION 6: STRICT TECHNICAL INTERVIEW BOARD (65 QUESTIONS)

Bhai, functional optimization aur advanced React performance benchmarks se related har ek question scenario ko standard specifications limits ke with respect complete clear pipeline se design kiya gaya hai:

---

## 1. Beginner Interview Questions (1-15)

### Q1: What is `React.memo` and why is it used?
*   **Professional English Answer**: `React.memo` is a Higher-Order Component (HOC) provided by React that optimizes functional components by caching their rendered output. It prevents unnecessary re-renders by performing a shallow comparison of the component's incoming props.
*   **Easy Hinglish Explanation**: `React.memo` ek Higher-Order Component wrapper hai jo components ki performance ko optimize karne ke liye unka rendered result cache karleta hai. Agar incoming props nahi badle hain, toh yeh unnecessary re-renders ko bypass kar deta hai.
*   **Follow-up Questions**:
    1. Does React.memo watch state and context changes?
    2. What equivalent exists for class components?

---

### Q2: How does shallow comparison behave in React.memo?
*   **Professional English Answer**: React.memo uses shallow reference comparison (`Object.is`) by default. It compares top-level keys of prevProps and nextProps. If they hold matching primitive values or identical object reference pointers, it skips rendering.

---

### Q3: Does React.memo prevent re-render if the component's internal state updates?
*   **Professional English Answer**: No, `React.memo` only checks for changes in props. If the component's internal state (managed by `useState` or `useReducer`) or a consumed `useContext` changes, it will still re-render.

---

### Q4: What is the default comparison behavior if no second argument is passed to React.memo?
*   **Professional English Answer**: If omitted, React default is shallow reference comparisons of all top-level keys inside the props object.

---

### Q5: Can we use React.memo inside Class Components?
*   **Professional English Answer**: No, React.memo is explicitly designed to optimize functional components. Class components utilize `React.PureComponent` or implement `shouldComponentUpdate` manually.

---

### Q6: Why is calling React.memo on every component considered an anti-pattern?
*   **Professional English Answer**: Because prop comparison has a computational cost. For cheap presentational components, the comparison cost can exceed the execution cost of a simple render pass.

---

### Q7: What parameters does the custom comparison function accept?
*   **Professional English Answer**: It accepts exactly two arguments: the previous props object (`prevProps`) and the next props object (`nextProps`).

---

### Q8: What boolean value must the custom comparison function return to skip render?
*   **Professional English Answer**: It must return `true` to skip rendering (indicating props are equal). Returning `false` triggers a re-render.

---

### Q9: Does wrapping a component in React.memo affect its lifecycle execution during mount?
*   **Professional English Answer**: No, it operates identical to standard functional components on mount, compiling visual templates normally.

---

### Q10: What does the React Compiler in React 19 change about React.memo usage?
*   **Professional English Answer**: The React Compiler automatically inserts compile-time memoization, eliminating the manual boilerplate of wrapping components in `React.memo`.

---

### Q11: Where should React.memo wrap our components?
*   **Professional English Answer**: It wraps the functional component during its declaration or at the export boundary.

---

### Q12: Why are inline objects `style={{ color: 'red' }}` bad for React.memo?
*   **Professional English Answer**: Every render pass creates a new object reference for `style`. Since reference comparisons fail, `React.memo` fails to skip rendering.

---

### Q13: Does React.memo provide a semantic guarantee or performance hint?
*   **Professional English Answer**: It serves as a performance optimization. React can choose to release cached render buffers if system memory runs low.

---

### Q14: How can we trace unnecessary re-renders in DevTools?
*   **Professional English Answer**: By using the React DevTools Profiler and enabling "Highlight updates when components render" options.

---

### Q15: Why are array indexing list keys problematic for shallow comparison optimizations?
*   **Professional English Answer**: If items are reordered, index keys force elements mapping mismatches, breaking `React.memo` optimizations.

---

## 2. Intermediate Interview Questions (16-30)

### Q16: How do `useCallback` and `React.memo` work together to optimize component subtrees?
*   **Professional English Answer**: If a child component is wrapped in `React.memo`, passing an un-memoized parent callback will break memoization because the callback's reference changes on every render. Wrapping the parent callback in `useCallback` ensures referential stability, allowing `React.memo` to skip re-renders.
*   **Easy Hinglish Explanation**: React.memo props checks tabhi skip render triggers block karte hain jab parameters values constant rahen. Parent custom handlers un-memoized callbacks dynamic re-creation references break kar dete hain. Is reference identity changes ko block karne ke liye `useCallback` mandatory hai.
*   **Follow-up Questions**:
    1. How does the dependencies array affect callbacks recreation?
    2. What does missing dependencies trigger inside effect channels?

---

### Q17: What is the difference between `React.memo` and `useMemo`?
*   **Professional English Answer**: `React.memo` is a Higher-Order Component targeting entire components to optimize re-renders, while `useMemo` is a hook targeting specific expensive calculations or variable values inside a component.

---

### Q18: Explain the "shallow comparison mismatch" problem with inline array mapping.
*   **Professional English Answer**: Passing arrays directly in props creates a new array reference on every render, which breaks the default shallow comparison of `React.memo`.

---

### Q19: Why does StrictMode render components twice in development, and does this bypass React.memo?
*   **Professional English Answer**: StrictMode double-renders components to help developers catch unintended side effects. This is a development-only behavior and does not mean `React.memo` is failing in production.

---

### Q20: How do you handle deep object comparisons inside React.memo?
*   **Professional English Answer**: By providing a custom comparison function as the second argument to `React.memo` that explicitly checks the nested properties of the object.

---

### Q21: Can you use React.memo with Higher-Order Components?
*   **Professional English Answer**: Yes, Higher-Order Components can wrap components optimized by `React.memo`, as long as referential prop stability is preserved.

---

### Q22: What is the risk of utilizing Math.random() as key index elements while using React.memo?
*   **Professional English Answer**: Random values regenerate on every render, which destroys structural reference checks and forces constant re-renders.

---

### Q23: Why do we write custom comparators carefully to avoid unexpected state sync bugs?
*   **Professional English Answer**: If the comparator returns `true` incorrectly, the component will skip rendering, leading to outdated or stale UI states.

---

### Q24: What scheduling priority does React assign to component evaluations skipped by memoization?
*   **Professional English Answer**: Skipped components bypass rendering entirely, freeing up the React Scheduler to process higher-priority updates.

---

### Q25: How does the children prop affect React.memo's default comparison?
*   **Professional English Answer**: Since `children` is JSX, it is compiled into a new object reference on every render, which breaks `React.memo` unless children references are memoized.

---

### Q26: Explain the equivalent class lifecycle method for custom props comparison.
*   **Professional English Answer**: The equivalent is `shouldComponentUpdate(nextProps, nextState)` which returns a boolean to determine if a class component should re-render.

---

### Q27: How can useDeferredValue benefit from React.memo?
*   **Professional English Answer**: Wrapping a child component in `React.memo` allows it to skip rendering during urgent updates, deferring until the deferred state stabilizes.

---

### Q28: Does React.memo support profiling metrics tracking out of the box?
*   **Professional English Answer**: Yes, we can analyze the performance benefits of `React.memo` using the React Profiler in Chrome DevTools.

---

### Q29: Can we use React.memo inside Server Components?
*   **Professional English Answer**: No, Server Components run only on the server. Memoization is a client-side optimization pattern for interactive components.

---

### Q30: What is referential integrity in React optimizations?
*   **Professional English Answer**: Referential integrity means objects and functions keep the same reference pointer across render cycles, which is critical for optimization hooks.

---

## 3. Advanced Interview Questions (31-45)

### Q31: How does React's internal Fiber engine track and resolve memoized component structures during the reconciliation process?
*   **Professional English Answer**: During reconciliation, React matches fiber nodes using compilation sequences. For components wrapped in `React.memo`, React checks for props changes. If they are unchanged, React skips the render phase and clones the existing fiber subtree, avoiding unnecessary Virtual DOM diffing.
*   **Easy Hinglish Explanation**: React internal level par fiber nodes trees maintain karta hai. React.memo components updates ke dauran React previous props snapshots aur incoming values ko compare karta hai. Identical values hone par Virtual DOM parsing call structure ko complete skip karke current node ko reuse kar leta hai.
*   **Follow-up Questions**:
    1. How does conditional hook calling break Fiber sequence indices matching?
    2. What does double evaluations inside StrictMode reveal?

---

### Q32: Why does the React Compiler (React 19) reduce the need for manual React.memo?
*   **Professional English Answer**: The React Compiler statically analyzes code during build time and automatically inserts memoization, removing the need for developers to write manual wrappers.

---

### Q33: How does React.memo handle props that are passed via a context provider?
*   **Professional English Answer**: Changes to context values bypass `React.memo` optimizations. Any component consuming context via `useContext` will still re-render when the context value changes.

---

### Q34: What is the performance impact of memoizing components with heavy nested JSX structures?
*   **Professional English Answer**: Memoizing components with complex nested children can be beneficial, but if those children change references constantly, the optimization is bypassed, adding rendering overhead.

---

### Q35: Contrast the overhead of running custom comparison functions versus React's default shallow comparison.
*   **Professional English Answer**: Default shallow comparisons are fast but fail for nested objects. Custom comparison functions can handle nested objects but can degrade performance if they run complex, heavy deep-checks.

---

### Q36: How can we implement deep comparison safely without introducing performance degradation?
*   **Professional English Answer**: By using target comparison strategies on key properties (such as database IDs) rather than checking entire deep object graphs.

---

### Q37: Why does a custom comparison function returning `true` prevent the component from updating on parent renders?
*   **Professional English Answer**: Returning `true` tells React that the props are identical, which tells React to skip the render pass.

---

### Q38: How does React.memo interact with Suspense boundaries during lazy loading?
*   **Professional English Answer**: Lazy-loaded components optimized with `React.memo` will skip evaluation passes while suspended, helping prevent unnecessary render flashes.

---

### Q39: Explain "tearing" in concurrent rendering and how it affects memoized components.
*   **Professional English Answer**: Tearing occurs when components render different values for the same state during concurrent updates. Using `useSyncExternalStore` resolves this issue.

---

### Q40: What happens under the hood when a memoized component is unmounted and then remounted?
*   **Professional English Answer**: The cached output buffer is destroyed. React executes a full render pass on remounting.

---

### Q41: Can you pass a Ref as a prop to a memoized component?
*   **Professional English Answer**: Yes, since Refs have stable identities, passing them as props does not break `React.memo` optimizations.

---

### Q42: How does the compiler recognize that a component is pure?
*   **Professional English Answer**: The compiler analyzes data flows statically to verify that components do not read or mutate global variables.

---

### Q43: What are primitive versus non-primitive props in React optimizations?
*   **Professional English Answer**: Primitives (strings, numbers, booleans) are compared by value. Non-primitives (objects, arrays, functions) are compared by reference, which can break memoization.

---

### Q44: Why does returning `false` from a custom comparator cause a re-render?
*   **Professional English Answer**: Returning `false` tells React that the props are different, triggering a re-render.

---

### Q45: How can garbage collection impact memoized templates cached by React.memo?
*   **Professional English Answer**: React may release cached templates to free up system memory.

---

## 4. Scenario-Based Questions (46-55)

### Q46: Scenario: Clicking "Alter System" updates state in the parent, but the child wrapped in React.memo does not update on screen. What comparator mistake occurred?
*   **Professional English Answer**: The custom comparison function is likely returning `true` unconditionally, causing the component to skip rendering even when props update.
*   **Easy Hinglish Explanation**: custom comparison function nested levels data conditions dynamically detect nahi kar raha hai. Hamesha `true` return karne se child optimization lock ho gayi hai.

---

### Q47: Scenario: A search bar's dropdown list flickers on every keypress. Profiling shows parent filters are running. How does React.memo help?
*   **Professional English Answer**: Wrapping the dropdown in `React.memo` and memoizing filter values using `useMemo` will prevent the dropdown from re-rendering on every keypress.

---

### Q48: Scenario: Child buttons re-render on every parent render despite being wrapped in React.memo. Why are optimizations still failing?
*   **Professional English Answer**: The parent is likely passing inline callbacks or un-memoized functions as props. Wrapping parent callbacks in `useCallback` will solve the issue.

---

### Q49: Scenario: A dynamic table optimized with React.memo re-renders when data columns are reordered. What list rendering rule was violated?
*   **Professional English Answer**: Using array indexes as keys in dynamic lists causes mapping mismatches on reordering. Using unique database IDs as keys resolves this.

---

### Q50: Scenario: App performance degrades after wrapping 100 simple badge elements in React.memo. Why?
*   **Professional English Answer**: Running shallow comparison checks on too many small, cheap components adds computational overhead.

---

### Q51: Scenario: Toggling a sidebar freezes the UI. Profiling points to getAnagrams running on every render. How do you fix this?
*   **Professional English Answer**: Wrap the expensive calculation in `useMemo` so it only runs when its direct dependencies change.

---

### Q52: Scenario: Sibling panels lose synchronization during data fetching. Why did React.memo block updates?
*   **Professional English Answer**: The components are likely updating state asynchronously. State or context updates bypass `React.memo` optimizations.

---

### Q53: Scenario: Forms reset unexpectedly on button clicks. Why?
*   **Professional English Answer**: Button clicks inside forms trigger browser page reloads. Calling `e.preventDefault()` inside click handlers prevents this.

---

### Q54: Scenario: Context updates are not reaching child components wrapped in React.memo. Why?
*   **Professional English Answer**: This is incorrect; context updates bypass `React.memo` optimizations and will always trigger re-renders in consuming child components.

---

### Q55: Scenario: Multiple simultaneous clicks on a submit button trigger duplicate server requests. How do you prevent this?
*   **Professional English Answer**: Disable the submit button during in-flight transactions using a boolean state variable.

---

## 5. Live Coding Questions (56-60)

### Q56: Code a basic functional component optimized with React.memo.
```jsx
import React from 'react';

const Badge = React.memo(({ score }) => {
  return <span>Current Score: {score}</span>;
});

export default Badge;
```

---

### Q57: Code a custom comparison function for nested object props.
```jsx
import React from 'react';

const UserRow = React.memo(({ profile }) => {
  return <p>Active User: {profile.name}</p>;
}, (prev, next) => prev.profile.id === next.profile.id);

export default UserRow;
```

---

### Q58: Code a stable state updates callback inside custom hooks.
```jsx
import { useState, useCallback } from 'react';

export function useToggler() {
  const [value, setValue] = useState(false);
  const toggle = useCallback(() => setValue(v => !value), []);
  return [value, toggle];
}
```

---

### Q59: Code a standard debounce execution safely using setTimeout.
```jsx
import React, { useState, useEffect } from 'react';

export function useDebouncedValue(val, delay) {
  const [debounced, setDebounced] = useState(val);

  useEffect(() => {
    const timer = setTimeout(() => setDebounced(val), delay);
    return () => clearTimeout(timer);
  }, [val, delay]);

  return debounced;
}
```

---

### Q60: Code a React 19 stable useActionState form controller.
```jsx
import React, { useActionState } from 'react';

async function submitAction(state, formData) {
  return { status: "Success", task: formData.get("title") };
}

export default function ActionForm() {
  const [state, dispatch, isPending] = useActionState(submitAction, null);
  return (
    <form action={dispatch}>
      <input name="title" required />
      <button type="submit" disabled={isPending}>Save</button>
    </form>
  );
}
```

---

## 6. Debugging Scenarios (61-65)

### Q61: Debug this code: App crashes with un-caught TypeError during render.
```jsx
// 🔴 Buggy Code
const TargetCard = React.memo(({ info }) => {
  return <h4>Node: {info.title}</h4>;
}, true); // Direct boolean is passed instead of function!
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
const TargetCard = React.memo(({ info }) => {
  return <h4>Node: {info.title}</h4>;
}, (prev, next) => prev.info.title === next.info.title); // Function reference passed
```
*   **Reasoning**: `React.memo` expects a function as the second argument, not a raw boolean value.

---

### Q62: Debug this code: UI does not update on clicking delete task button.
```jsx
// 🔴 Buggy Code
function reducer(state, action) {
  if (action.type === 'delete') {
    state.tasks.splice(action.index, 1); // Direct mutation!
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
      tasks: state.tasks.filter((t, i) => i !== action.index) // Filter returns a new array reference
    };
  }
  return state;
}
```
*   **Reasoning**: Direct mutation does not change the array reference, causing React to skip rendering. Immutably return a new array instead.

---

### Q63: Debug this code: Input focus is lost on every character typed.
```jsx
// 🔴 Buggy Code
export default function ParentApp() {
  const [text, setText] = useState("");
  // Component declared inside parent component render body!
  const NestedInput = () => <input value={text} onChange={e => setText(e.target.value)} />;
  return <NestedInput />;
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
function NestedInput({ text, onUpdate }) {
  return <input value={text} onChange={onUpdate} />;
}
export default function ParentApp() {
  const [text, setText] = useState("");
  return <NestedInput text={text} onUpdate={e => setText(e.target.value)} />;
}
```
*   **Reasoning**: Declaring components inside another component forces React to destroy and recreate the DOM subtree on every render, losing focus.

---

### Q64: Debug this code: Infinite rendering loop inside list search queries.
```jsx
// 🔴 Buggy Code
export default function SearchApp() {
  const [query, setQuery] = useState("");
  const runFilter = () => {
    console.log("Filtering...", query);
  };
  useEffect(() => {
    runFilter();
  }, [runFilter]); // Re-creates helper on every render, looping rendering cycles!
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
export default function SearchApp() {
  const [query, setQuery] = useState("");
  
  const runFilter = useCallback(() => {
    console.log("Filtering...", query);
  }, [query]); // Re-creates safely only when query changes

  useEffect(() => {
    runFilter();
  }, [runFilter]); // Runs safely
}
```
*   **Reasoning**: Defining helper functions inside render bodies recreates references on every pass, causing endless effects loops if listed as dependencies. `useCallback` stabilizes the reference.

---

### Q65: Debug this code: App uses stale closures capturing historical state.
```jsx
// 🔴 Buggy Code
const [count, setCount] = useState(0);
const saveLog = useCallback(() => {
  console.log("Current count is:", count); // Always logs 0 due to stale closure!
}, []); // Empty array captures initial state value only!
```
*   **Correct Code**:
```jsx
// ✅ Correct Code
const [count, setCount] = useState(0);
const saveLog = useCallback(() => {
  console.log("Current count is:", count);
}, [count]); // Correct dependency tracking
```
*   **Reasoning**: An empty dependency array locks the callback's closure to values from the first render. Including the variable in dependencies ensures the closure stays up to date.

---

## 7. Educational Assessment & Study Guides

### Mini Assignment
1. Vite environment setup karke ek to-do app build karein.
2. List row component ko `React.memo` se wrap karein.
3. Parent component ticks count evaluate karke row renders skip trace karein.

---

### Practice Questions
1. `React.memo` Fiber tree reconciliations skip render processing stages summarize karein.
2. React 19 native compiler automatic memoizations mechanisms explain karein.

---

### Multiple Choice Questions (MCQs)

1. **What is the comparison algorithm React.memo uses by default?**
    * (A) Deep comparison
    * (B) Shallow reference comparison
    * (C) Double compiler validation
    * *Correct Answer: (B)*

2. **When should React.memo be avoided?**
    * (A) High-frequency mutating dynamic components
    * (B) Static presentational layouts
    * (C) Components accepting stable callback refs
    * *Correct Answer: (A)*

---

### Revision Notes
* **Referential Stability**: Always wrap parent-provided callback handlers in `useCallback` when using `React.memo`.
* **Reference Isolation**: Avoid inline object configurations passed in props directly.

---

### Cheat Sheet
```jsx
// Simple wrapper
const Component = React.memo(({ title }) => <p>{title}</p>);

// Custom comparisons
const HeavyRow = React.memo(Row, (prev, next) => prev.id === next.id);
```


