# REACT ERROR BOUNDARIES MASTERCLASS 🚨

Bhai, standard JavaScript programming me jab koi unexpected error ya exception occur hoti hai, tab hum code flow ko crash hone se bachane ke liye standard `try...catch` blocks ka use karte hain. Lekin React ke functional declarative rendering flow me, jab koi component render lifecycle ke dauran crash ho jata hai, tab pure page ko blank state or white-screen-of-death se bachane ke liye React ne ek specialized, declarative paradigm introduce kiya—**Error Boundaries**.

Error Boundaries aisi class components hoti hain jo apne pure nested subtrees me hone wale rendering crashes ko intercept kar leti hain, unhe safely isolate (quarantine) karti hain, aur user ko pure page crash ke bajaye ek localized, user-friendly **Fallback UI** dikhati hain.

---

# THE COMPARISON MATRICES 📊

Sabse pehle, code architecture design karte waqt correct design patterns select karne ke liye in comparative tables ko acche se dhyan me bitha lo:

### Table 1: Error Boundary vs try...catch
| Feature | `try...catch` Block | Error Boundary Component |
| :--- | :--- | :--- |
| **Paradigm** | **Imperative error handling**: Explicit code statement executes step-by-step. | **Declarative recovery boundary**: Wraps components at layout level using JSX. |
| **Target Zone** | Asynchronous operations, click event handlers, and data-fetching promises. | React rendering phases, component lifecycle updates, and reconciler commits. |
| **Functional Hooks** | Wrap hook calls manually inside try-catch block allows hooks rules to break. | Wraps components containing hook executions without disrupting hook stack cycles. |
| **Subtree Scope** | Limited strictly to the immediate code block inside the block curly braces. | Traverses deeply down through all nested child subtrees of the wrapped components. |

### Table 2: Error Boundary vs Error Handling in Event Handlers
| Metric | Event Handlers Error Control | Error Boundary Exception Capture |
| :--- | :--- | :--- |
| **Execution Trigger** | Triggers on user actions (e.g., clicks, inputs, keypresses). | Triggers during component construction, rendering pass, and commit phases. |
| **Is Blank Screen Avoided?** | Yes, but standard state changes must be run manually to render fallback messages. | **Automatically handled**: Catches render-phase crashes and renders custom UI. |
| **Does React unmount the Tree?** | No, because events are external to the render engine's cycle. | Yes, React unmounts the buggy child subtree if not caught by a boundary. |

### Table 3: Error Boundary vs react-error-boundary
| Feature | Custom Class-Based Error Boundary | `react-error-boundary` Package |
| :--- | :--- | :--- |
| **Type** | Manual JavaScript Class extending `React.Component`. | Pre-packaged dynamic component suite from npm library. |
| **Functional Component Support** | **Requires a Class** to implement lifecycle hook algorithms. | Fully compatible with functional React via standard wrappers and props. |
| **Reset/Recovery** | Needs manual implementation of state reset functions inside classes. | Built-in `resetErrorBoundary` callback and `onReset` reset triggers. |

---

# SECTION 1: THE BEGINNER'S PLAYGROUND (5 EXAMPLES)

Chalo bhai, sabse pehle 5 basic class-based aur basic functional error tracking setups se shuru karte hain jisse concepts, error states management, aur basic fallbacks integrate karna seekhenge.

---

### Beginner Example 1: Standard Class-Based Error Boundary

#### File Name: `SimpleErrorBoundary.js`
```javascript
import React, { Component } from 'react'; // Importing React class elements

// 1. Defining a standard Class-based Error Boundary extending React.Component
class SimpleErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false }; // 2. Initial state configuration with hasError tracker
  }

  // 3. Static lifecycle method invoked automatically on rendering crash
  static getDerivedStateFromError(error) {
    console.log("⚡ [getDerivedStateFromError] Triggered. Catching error:", error.message);
    return { hasError: true }; // Synchronously sets the state, triggering fallback render
  }

  // 4. componentDidCatch lifecycle method to handle error side-effects
  componentDidCatch(error, errorInfo) {
    console.log("📋 [componentDidCatch] Logging diagnostic telemetry:", errorInfo.componentStack); //
  }

  render() {
    if (this.state.stateError || this.state.hasError) {
      // 5. Renders a standard default fallback UI if error is active
      return (
        <div style={{ padding: '20px', border: '2px solid red', background: '#ffebee', color: 'red' }}>
          <h4>Something went wrong!</h4>
          <p>Please check your system terminals or try reloading the page.</p>
        </div>
      );
    }

    // 6. Renders children cleanly when no error exists
    return this.props.children; //
  }
}

export default SimpleErrorBoundary;
```

#### Child Component File Name: `WobblyWidget.js`
```javascript
import React, { useState } from 'react'; //

export default function WobblyWidget() {
  const [crashApp, setCrashApp] = useState(false);

  if (crashApp) {
    // Throws a normal rendering error on demand
    throw new Error("CRITICAL_WIDGET_FAIL: Subsystem crashed!"); //
  }

  return (
    <div style={{ padding: '16px', border: '1px dashed orange', margin: '10px 0' }}>
      <h5>Operational Subsystem Controller</h5>
      <button onClick={() => setCrashApp(true)} style={{ background: 'orange', padding: '6px' }}>
        Force Crash Subsystem 💥
      </button>
    </div>
  );
}
```

#### Parent Component File Name: `AppConsole.js`
```javascript
import React from 'react';
import SimpleErrorBoundary from './SimpleErrorBoundary';
import WobblyWidget from './WobblyWidget';

export default function AppConsole() {
  return (
    <div style={{ padding: '24px', fontFamily: 'sans-serif' }}>
      <h3>Enterprise Mission Desk</h3>
      {/* Wrapping the fragile widget inside our Error Boundary */}
      <SimpleErrorBoundary>
        <WobblyWidget />
      </SimpleErrorBoundary>
    </div>
  );
}
```

##### Line-by-Line Explanation
1. `class SimpleErrorBoundary extends Component`: Component classes inheritance implement kiya jisse standard React lifecycles access ho sakein.
2. `this.state = { hasError: false }`: Local state initial value map kiya error flags check karne ke liye.
3. `static getDerivedStateFromError(error)`: Jab child component crash hoga, yeh method child exception trace ko catch karke safely next state update schedule karega.
4. `componentDidCatch(error, errorInfo)`: Telemetries, debugging logs, aur error stacks systems details write karne ke liye use hota hai.
5. `return this.props.children`: Error component mounted status normal hone par standard wrapped children components ko render karata hai.

##### Browser Output
* Pehle screen par header aur ek simple widget with action button display hoga. Button click karte hi console crash check alerts generate karega. Normal page layout white blank page hone ke bajaye instantly custom styled red error message box me revert ho jayega.

##### Why Error Boundary is used here
* Render-phase runtime exceptions standard component bodies ko completely crash and clear unmount kar deti hain. Is absolute failure block ko localize recovery me map karne ke liye Error Boundary integration crucial hai.

##### Better Version (Vite optimized React 19 pattern integration)
* Modern React 19 compilation models optimized client diagnostics standard triggers `onCaughtError` provide karte hain jo dev logs ko minimize rakhte hain.

```javascript
// ✅ Modern React 19 Style: Explicitly pass customizable boundary props
export default class SimpleErrorBoundary extends React.Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <h2>Localized error handled safely.</h2>; //
    }
    return this.props.children; //
  }
}
```

##### Dry Run
1. **Initial Mount**: Parent mount hone par `hasError` initially false registers contain karta hai.
2. **Action Click**: Click event `setCrashApp(true)` trigger schedules re-renders.
3. **Execution Exception**: Rendering loop `WobblyWidget` executes, and catches `throw new Error()` statement.
4. **Reconciliation Intercept**: Reconciler engine throws exception up to nearest boundary Fiber component `SimpleErrorBoundary`.
5. **Layout Recovery**: `getDerivedStateFromError` captures the exception trace and returns state update `{ hasError: true }`. Fallen component unmounts and fallback layout paints safely.

---

### Beginner Example 2: Simple Customizable Prop-Fallback Boundary

#### File Name: `CustomPropBoundary.js`
```javascript
import React, { Component } from 'react'; //

class CustomPropBoundary extends Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  render() {
    if (this.state.hasError) {
      // 1. Allowing parents to specify custom fallback components dynamically
      return this.props.fallback || <div>⚠️ Operational Failure. Please contact support.</div>; //
    }
    return this.props.children; //
  }
}

export default CustomPropBoundary;
```

#### Parent Component File Name: `ParentCustomConsole.js`
```javascript
import React from 'react';
import CustomPropBoundary from './CustomPropBoundary';
import WobblyWidget from './WobblyWidget';

export default function ParentCustomConsole() {
  return (
    <div style={{ padding: '20px' }}>
      {/* 2. Overriding default error layout dynamically from Parent prop declarations */}
      <CustomPropBoundary fallback={<div style={{ color: 'blue' }}>🛸 Locally quarantine widget error handler.</div>}>
        <WobblyWidget />
      </CustomPropBoundary>
    </div>
  );
}
```

##### Why Error Boundary is used here
* Single generic layout messages ke bajaye, isolated widgets ko specialized localized layouts templates provide karne ke liye design kiya gaya hai.

---

### Beginner Example 3: Class-Based Logging Boundary

#### File Name: `LoggingBoundary.js`
```javascript
import React, { Component } from 'react'; //

class LoggingBoundary extends Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  // 1. Accessing browser stack trace telemetries synchronously
  componentDidCatch(error, errorInfo) {
    console.warn("📋 [Telemetry Transmitter] Logging exception details...");
    // Simulating endpoint payloads post operations
    const logsPayload = {
      message: error.toString(),
      componentTraceStack: errorInfo.componentStack
    };
    console.log("Transmitting payload to analytics nodes:", logsPayload); //
  }

  render() {
    if (this.state.hasError) {
      return <h3>Logged system failure securely.</h3>; //
    }
    return this.props.children; //
  }
}

export default LoggingBoundary;
```

---

### Beginner Example 4: Localized Nested Forms Boundary (React Native / Web templates)

#### File Name: `LocalFormBoundary.js`
```javascript
import React, { Component } from 'react'; //

class LocalFormBoundary extends Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  render() {
    if (this.state.hasError) {
      return (
        <div style={{ padding: '12px', background: '#ffe0b2', border: '1px solid orange' }}>
          🔒 Local Form Submission crashed. Please re-input credentials.
        </div>
      );
    }
    return this.props.children; //
  }
}

export default LocalFormBoundary;
```

#### Parent Component File Name: `EnterpriseFormApp.js`
```javascript
import React from 'react';
import LocalFormBoundary from './LocalFormBoundary';
import WobblyWidget from './WobblyWidget';

export default function EnterpriseFormApp() {
  return (
    <div style={{ padding: '20px' }}>
      <h4>Form Section A (Secure)</h4>
      <p>Secure input boundaries.</p>

      {/* Wrapping wobbly sections safely */}
      <LocalFormBoundary>
        <WobblyWidget />
      </LocalFormBoundary>

      <h4>Form Section B (Stable)</h4>
      <button onClick={() => alert("SYS_CLEAR")}>Clear Main Buffers</button>
    </div>
  );
}
```

---

### Beginner Example 5: Non-React Legacy HTML fallback Boundary

#### File Name: `LegacyHtmlBoundary.js`
```javascript
import React, { Component } from 'react'; //

class LegacyHtmlBoundary extends Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  render() {
    if (this.state.hasError) {
      // 1. Safe static legacy markup rendering
      return (
        <div className="legacy-fallback-block" style={{ padding: '24px', background: 'gray', color: '#fff' }}>
          <h4>Subsystem Engine offline.</h4>
        </div>
      );
    }
    return this.props.children; //
  }
}

export default LegacyHtmlBoundary;
```

---

# SECTION 2: THE INTERMEDIATE CHANNELS (5 EXAMPLES)

Chalo bhai, ab calculations complexity ko badhate hue error reset patterns, event handlers integrations, and dynamic state recovery setups establish karte hain.

---

### Intermediate Example 6: Error Recovery with "Reset State" triggers

#### File Name: `RecoverableBoundary.js`
```javascript
import React, { Component } from 'react'; //

class RecoverableBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false }; //
    this.triggerManualRecovery = this.triggerManualRecovery.bind(this); //
  }

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  // 1. Method resets the state, enabling React to try re-rendering child components
  triggerManualRecovery() {
    console.log("🔄 Resetting Error Boundary state...");
    this.setState({ hasError: false }); // Resets hasError back to false
    if (this.props.onResetAction) {
      this.props.onResetAction(); // 2. Triggers parent callback to reset external state
    }
  }

  render() {
    if (this.state.hasError) {
      return (
        <div style={{ padding: '20px', border: '1px solid orange', background: '#fff3e0' }}>
          <h4>Wobbly widget crashed!</h4>
          <p>Please click below to retry the transaction loop:</p>
          <button onClick={this.triggerManualRecovery} style={{ padding: '8px', cursor: 'pointer' }}>
            Try Again 🔄
          </button>
        </div>
      );
    }
    return this.props.children; //
  }
}

export default RecoverableBoundary;
```

#### Parent Component File Name: `RecoveryConsoleApp.js`
```javascript
import React, { useState } from 'react';
import RecoverableBoundary from './RecoverableBoundary';

function BuggyCounter() {
  const [counter, setCounter] = useState(0);

  if (counter >= 3) {
    throw new Error("TELEMETRY_INDEX_OVERFLOW: Counter exceeded maximum value.");
  }

  return (
    <div style={{ padding: '16px', border: '1px solid black' }}>
      <h5>Secure Counter Module</h5>
      <p>Current telemetry index: {counter}</p>
      <button onClick={() => setCounter(prev => prev + 1)}>Increment Metric (+1)</button>
    </div>
  );
}

export default function RecoveryConsoleApp() {
  const [resetKey, setResetKey] = useState(0);

  // 3. Increment key to force remount of BuggyCounter on reset
  const handleSystemReboot = () => {
    console.log("System reboot initiated...");
    setResetKey(prev => prev + 1); 
  };

  return (
    <div style={{ padding: '20px' }}>
      <h3>Central Recovery Terminal</h3>
      <RecoverableBoundary key={resetKey} onResetAction={handleSystemReboot}> {/* */}
        <BuggyCounter />
      </RecoverableBoundary>
    </div>
  );
}
```

##### Why Error Boundary is used here
* Reset trigger loops enable system self-healing. Single components crashes page layouts locks ko bypass hoke safely reboot systems map dynamic triggers handle kar sakte hain.

---

### Intermediate Example 7: react-error-boundary package wrapper integration

#### File Name: `DynamicReactErrorBoundary.js`
```javascript
import React, { useState } from 'react';
import { ErrorBoundary } from 'react-error-boundary'; // 1. Importing the standard npm package

// 2. Define custom Error Fallback UI with reset triggers
function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert" style={{ padding: '20px', border: '2px dashed red', background: '#ffebee' }}>
      <h3>🚨 Subsystem crash resolved safely.</h3>
      <pre style={{ color: 'red' }}>Error: {error.message}</pre>
      <button onClick={resetErrorBoundary} style={{ padding: '8px 12px', cursor: 'pointer' }}>
        Try Again 🔄
      </button>
    </div>
  );
}

export default function DynamicReactErrorBoundary() {
  const [crashed, setCrashed] = useState(false);

  const resetAllConfigs = () => {
    console.log("🔄 Internal configuration reboot completed.");
    setCrashed(false); // Clear buggy state
  };

  return (
    <div style={{ padding: '24px' }}>
      {/* 3. Wrap target components in ErrorBoundary wrapper cleanly */}
      <ErrorBoundary 
        FallbackComponent={ErrorFallback} // Specify fallback layout
        onReset={resetAllConfigs}          // Hook up reset callback
      >
        {crashed ? (
          (() => { throw new Error("GATEWAY_PAYMENT_FAILED: Handshake timeout."); })()
        ) : (
          <div>
            <h5>Main Payment Hub Terminal</h5>
            <button onClick={() => setCrashed(true)}>Process Secure Ingestion 💳</button>
          </div>
        )}
      </ErrorBoundary>
    </div>
  );
}
```

---

### Intermediate Example 8: Handling forms submission actions crashes in React 19

#### File Name: `FormActionBoundary.js`
```javascript
import React from 'react';
import { ErrorBoundary } from 'react-error-boundary'; //

export default function FormActionBoundary() {
  // Action method that executes on form submission
  async function searchAction(formData) {
    const query = formData.get("query");
    if (query === "crash") {
      throw new Error("DATABASE_CONNECTION_REFUSED: Form trace exception."); //
    }
    alert(`Success: Search query logged for "${query}"`);
  }

  return (
    <div style={{ padding: '20px', border: '1px solid gray' }}>
      {/* React 19 wraps dynamic forms actions inside ErrorBoundary directly to catch action errors! */}
      <ErrorBoundary fallback={<p style={{ color: 'red' }}>There was an error while submitting the form 🚨</p>}> {/* */}
        <form action={searchAction} style={{ display: 'flex', gap: '8px' }}> {/* */}
          <input name="query" placeholder="Type 'crash' to trigger boundary..." style={{ padding: '6px' }} />
          <button type="submit">Search</button>
        </form>
      </ErrorBoundary>
    </div>
  );
}
```

---

### Intermediate Example 9: Catching lazy loaded routes failures

#### File Name: `RouteBoundary.js`
```javascript
import React, { lazy, Suspense } from 'react'; //
import { ErrorBoundary } from 'react-error-boundary'; //

// Lazy load a wobbly component that might fail to fetch due to network issues
const LazyFragileComponent = lazy(() => {
  return new Promise((resolve, reject) => {
    // Simulate network bundle fetch failure
    setTimeout(() => reject(new Error("FAILED_TO_FETCH_CHUNK_SCRIPTS")), 1000);
  });
});

export default function RouteBoundary() {
  return (
    <div style={{ padding: '20px', border: '1px solid teal' }}>
      {/* 1. Catch dynamic bundle fetch failures using custom boundaries */}
      <ErrorBoundary fallback={<div>⚠️ Failed to load router chunk file. Please reload page.</div>}>
        <Suspense fallback={<div>⏳ Unpacking lazy components...</div>}>
          <LazyFragileComponent />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

---

### Intermediate Example 10: try-catch with Local State as Fallback Alternative

#### File Name: `LocalEffectBoundary.js`
```javascript
import React, { useState, useEffect } from 'react'; //

export default function LocalEffectBoundary() {
  const [data, setData] = useState(null);
  const [errorState, setErrorState] = useState(null); //

  useEffect(() => {
    const executeFetch = async () => {
      try {
        // Simulating data-fetching failure inside useEffect
        throw new Error("MOCK_REST_API_401: Unauthorized access.");
      } catch (err) {
        console.error("Caught error inside useEffect:", err.message);
        setErrorState(err.message); // Register error to local state
      }
    };
    executeFetch();
  }, []);

  if (errorState) {
    // 1. Manually return fallback UI based on local state
    return (
      <div style={{ padding: '15px', color: 'red', border: '1px solid red' }}>
        🚨 Local Fetch failed: {errorState}
      </div>
    );
  }

  return <div>⏳ Processing server telemetry...</div>;
}
```

---

# SECTION 3: ADVANCED INTEGRATIONS (3 EXAMPLES)

Chalo bhai, ab standard use transitions, promise dynamic status tracking wrappers, aur React 19's native `use()` hook triggers ko advanced coordinates par map karte hain.

---

### Advanced Example 11: useTransition asynchronous actions crashes

#### File Name: `TransitionBoundaryApp.js`
```javascript
import React, { useState, useTransition } from 'react'; //
import { ErrorBoundary } from 'react-error-boundary'; //

function TransitionControlNode() {
  const [isPending, startTransition] = useTransition(); //
  const [status, setStatus] = useState("stable");

  const triggerAsyncCrash = () => {
    // 1. Wrap state changes and async logic inside startTransition
    startTransition(async () => {
      console.log("⚡ Starting transition sequence...");
      // Simulate remote async network call that crashes
      await new Promise(resolve => setTimeout(resolve, 1000));
      
      // Async action throws error
      throw new Error("TRANSACTION_ABORTED: Secure database handshake rejected.");
    });
  };

  return (
    <div style={{ padding: '16px', background: '#eceff1', border: '1px solid #455a64' }}>
      <h5>Asynchronous Transaction Desk</h5>
      <p>Transition Status: <strong>{status}</strong></p>
      
      <button onClick={triggerAsyncCrash} disabled={isPending} style={{ cursor: 'pointer' }}>
        {isPending ? "Connecting safely..." : "Simulate Async Database Call 🚀"}
      </button>
    </div>
  );
}

export default function TransitionBoundaryApp() {
  return (
    <div style={{ padding: '24px', border: '3px solid black' }}>
      <h3>Central Database Console</h3>
      {/* 2. ErrorBoundary captures errors thrown asynchronously inside useTransition actions! */}
      <ErrorBoundary fallback={<div>🚨 System captured secure database transaction failure.</div>}>
        <TransitionControlNode />
      </ErrorBoundary>
    </div>
  );
}
```

##### Why Error Boundary is used here
* Asynchronous execution flows traditional class boundaries ko skip kar jate hain. `useTransition` actions parameters catch maps loops standard boundary components par securely translate trigger karwate hain.

---

### Advanced Example 12: Catching React 19 use(promise) errors

#### File Name: `PromiseUseBoundary.js`
```javascript
import React, { use, Suspense } from 'react'; //
import { ErrorBoundary } from 'react-error-boundary'; //

// Simulate an asynchronous API promise fetch that fails
const rejectedPromiseInstance = new Promise((_, reject) => {
  setTimeout(() => reject(new Error("MOCK_TELEMETRY_SERVER_DISCONNECTED")), 1500);
});

function PromisesConsumer() {
  // 1. use() unwraps the promise value directly during rendering pass!
  // Caveat: Do not wrap in try-catch block! Let the boundary catch it.
  const data = use(rejectedPromiseInstance); //

  return <h5>Mainframe Payload: {data}</h5>;
}

export default function PromiseUseBoundary() {
  return (
    <div style={{ padding: '24px', border: '2px solid red' }}>
      <h4>React 19 Promise Resolver Engine</h4>
      
      {/* 2. Error Boundary captures the rejected promise thrown by use() */}
      <ErrorBoundary fallback={<div>🚨 ErrorBoundary captured use() promise rejection!</div>}>
        <Suspense fallback={<div>⏳ Retrieving server telemetry buffers (1.5s delay)...</div>}>
          <PromisesConsumer />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

##### Why Error Boundary is used here
* `use()` engine exceptions ko handle karne ke liye try-catch standard blocks are strictly blocked internally. Dynamic rejections ko cleanly coordinate maps par compile aur capture loops target boundaries par redirect maps standard use systems solve karte hain.

---

### Advanced Example 13: John Larsen style Promise Status-Checking Wrapper

#### File Name: `AdvancedStatusBoundary.js`
```javascript
import React, { Component } from 'react'; //

// John Larsen style promise wrapper
function wrapPromise(promise) {
  let status = "pending";
  let result;
  let suspender = promise.then(
    r => {
      status = "success";
      result = r;
    },
    e => {
      status = "error";
      result = e;
    }
  );
  return {
    read() {
      if (status === "pending") {
        throw suspender; // Caught by Suspense
      } else if (status === "error") {
        throw result;    // Caught by ErrorBoundary
      } else if (status === "success") {
        return result;   // Return resolved payload
      }
    }
  };
}

const mockFailingResource = wrapPromise(new Promise((_, reject) => {
  setTimeout(() => reject(new Error("LARSEN_BOOKINGS_SERVER_CRASH")), 1000);
}));

class AdvancedStatusBoundary extends Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  render() {
    if (this.state.hasError) {
      return <div>🚨 Larsen Bookings Interface Crashed. Recovery active.</div>;
    }
    return this.props.children; //
  }
}

export { AdvancedStatusBoundary, mockFailingResource };
```

---

# SECTION 4: REAL PRODUCTION SUITES (3 EXAMPLES)

Chalo actual real-world production level applications architectures check karte hain.

---

### Production Project 14: John Larsen style Spaces Bookings System

#### Folder Structure
```text
bookings-error-security/
├── src/
│   ├── components/
│   │   ├── JohnLarsenErrorBoundary.js
│   │   └── BookableSelectionPanel.js
│   └── App.js
```

#### File Name: `JohnLarsenErrorBoundary.js`
```javascript
import React, { Component } from 'react'; //

// John Larsen style custom telemetry error capturing component class
export default class JohnLarsenErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, caughtErrorMessage: "" }; //
    this.rebootSubsystem = this.rebootSubsystem.bind(this); //
  }

  static getDerivedStateFromError(error) {
    // 1. Capture error details synchronously on state change
    return { hasError: true, caughtErrorMessage: error.message };
  }

  componentDidCatch(error, errorInfo) {
    console.error(" John Larsen diagnostic engine caught crash stack:", errorInfo.componentStack); //
  }

  rebootSubsystem() {
    console.log("🔄 Resetting booking subsystem controllers...");
    this.setState({ hasError: false, caughtErrorMessage: "" }); //
    if (this.props.onSystemReset) {
      this.props.onSystemReset(); // Trigger custom parent actions
    }
  }

  render() {
    if (this.state.hasError) {
      return (
        <div style={{ padding: '24px', background: '#ffebee', border: '3px solid red', borderRadius: '8px' }}>
          <h4>🚨 Space Bookings Hub Offline</h4>
          <p>The space coordinator crashed with error: <strong>{this.state.caughtErrorMessage}</strong></p>
          <button onClick={this.rebootSubsystem} style={{ padding: '8px 16px', background: 'red', color: '#fff', border: 'none', cursor: 'pointer' }}>
            Reboot Coordinator Node 🔄
          </button>
        </div>
      );
    }
    return this.props.children; //
  }
}
```

#### File Name: `BookableSelectionPanel.js`
```javascript
import React, { useState } from 'react';

export default function BookableSelectionPanel({ initialRoomsList }) {
  const [rooms, setRooms] = useState(initialRoomsList);
  const [selectedRoomIdx, setSelectedRoomIdx] = useState(0);

  // Business Validation crash test
  const activeRoom = rooms[selectedRoomIdx];
  if (!activeRoom) {
    // Crash component dynamically if array index goes out of range
    throw new Error("ROOM_SELECTOR_OUT_OF_BOUNDS: Index location contains no registered node.");
  }

  return (
    <div style={{ padding: '16px', border: '1px solid #111', background: '#f5f5f5' }}>
      <h5>John Larsen Bookable Coordinator Desk 🗺️</h5>
      <p>Operational Space Selection: <strong>{activeRoom.title}</strong></p>
      
      <div style={{ display: 'flex', gap: '10px' }}>
        <button onClick={() => setSelectedRoomIdx(0)}>Select Suite A</button>
        {/* Simulate deletion/state mutation that crashes the selector */}
        <button onClick={() => setSelectedRoomIdx(99)} style={{ background: '#d32f2f', color: '#fff' }}>
          Query Non-Existent Area Code (Force Crash) 💥
        </button>
      </div>
    </div>
  );
}
```

##### Why Error Boundary is used here
* Bookable selector panels grids me wrong array indexing calculations or dynamic data changes components render logic loops ko completely break and blank page convert kar sakti hain. Localized isolation provides solid stability.

---

### Production Project 15: Accio To-do App Task Details Drawer

#### Folder Structure
```text
accio-todo-drawer-error/
├── src/
│   ├── components/
│   │   ├── AccioErrorBoundary.js
│   │   └── AccioTodoDesk.js
│   └── App.js
```

#### File Name: `AccioErrorBoundary.js`
```javascript
import React, { Component } from 'react'; //

export default class AccioErrorBoundary extends Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  render() {
    if (this.state.hasError) {
      return (
        <div style={{ padding: '15px', border: '2px solid navy', background: '#eceff1' }}>
          <h4>AccioJob System Quarantine Active</h4>
          <p>A task processing block crashed. The main workspace remains active.</p>
          <button onClick={() => this.setState({ hasError: false })} style={{ cursor: 'pointer' }}>
            Dismiss Fallback
          </button>
        </div>
      );
    }
    return this.props.children; //
  }
}
```

#### File Name: `AccioTodoDesk.js`
```javascript
import React, { useState } from 'react';
import AccioErrorBoundary from './AccioErrorBoundary';

function BuggyRow({ taskText }) {
  if (taskText.includes("crash")) {
    throw new Error("TASK_TEXT_VIOLATION: Input text contains blocked words.");
  }
  return <li>Task Action: {taskText}</li>;
}

export default function AccioTodoDesk() {
  const [tasks, setTasks] = useState(["Verify secure SSL handshakes", "Clear telemetry traces"]);
  const [inputVal, setInputVal] = useState("");

  return (
    <div style={{ padding: '24px', border: '3px solid black', background: '#fff' }}>
      <h2>AccioJob Enterprise Task Desk 📋</h2>
      <input 
        value={inputVal} 
        onChange={(e) => setInputVal(e.target.value)} 
        placeholder="Type blocked word 'crash' to test..." 
        style={{ padding: '6px', width: '250px' }}
      />
      <button onClick={() => { setTasks(p => [...p, inputVal]); setInputVal(""); }}>
        Add Task
      </button>

      <ul style={{ marginTop: '15px' }}>
        {tasks.map((t, idx) => (
          // 1. Quarantining individual todo rows using individual boundaries
          <AccioErrorBoundary key={idx}>
            <BuggyRow taskText={t} />
          </AccioErrorBoundary>
        ))}
      </ul>
    </div>
  );
}
```

---

### Production Project 16: Telemetry Analytics Alert Dashboard

#### Folder Structure
```text
telemetry-analytics-alert/
├── src/
│   ├── components/
│   │   ├── TelemetryBoundary.js
│   │   └── AnalyticsDashboard.js
│   └── App.js
```

#### File Name: `TelemetryBoundary.js`
```javascript
import React, { Component } from 'react'; //

export default class TelemetryBoundary extends Component {
  state = { hasError: false }; //

  static getDerivedStateFromError() {
    return { hasError: true }; //
  }

  render() {
    if (this.state.hasError) {
      return (
        <div style={{ padding: '16px', background: '#ffcdd2', border: '2px solid red' }}>
          🔒 Telemetry module quarantined. Diagnostic engine executing repairs...
        </div>
      );
    }
    return this.props.children; //
  }
}
```

---

# SECTION 5: TOPIC-BY-TOPIC COGNITIVE ANALYSIS 🧠

---

## 1. WHAT ARE ERROR BOUNDARIES?

### What is it?
Error Boundaries React standard applications components lifecycle hooks frameworks me aisi customizable Class-based components hoti hain jo apne nested subtrees me hone wale rendering crashes ko intercept kar leti hain.

### Why was it introduced?
React 16 se pehle, rendering loops me agar koi small JavaScript error occur hota tha, tab React pure page body unmount kar deta tha. User white empty screen dekhkar completely freeze ho jata tha. Is absolute collapse ko stop karne ke liye error capture models introduce kiye gaye.

### What problem does it solve?
Yeh isolated failures (jaise profile component crash) ke hone par pure workspace application layouts ko blank out hone se protect karta hai, safely crash node quarantine coordinates establish karta hai.

### Why should we use it?
Enterprise level mission-critical portals design systems, banking transactional UI flows, aur highly dynamic analytical tracking panels me robust rendering lifecycle ensure karne ke liye.

### When should we use it?
Dynamic layout blocks wrap-ups, lazy-loaded routers components switch blocks, or customizable third-party widget ingestion channels are perfect targets.

### When should we NOT use it?
Standard inline actions click-handling loops, async API promise structures (without standard use wrapper), hooks conditionals checking engines, and try-catch normal business limits checks me.

---

## 2. RECONCILER ENGINE COMMIT PHASE

### How does it work internally? (The Life Cycle Engine)
1. **The Exception Generation**: Rendering calculations running cycle nodes me dynamic TypeError occur hota hai.
2. **Reconciler Traversal**: React Fiber reconciler engine immediately rendering block ko halt karke exception upward bubble maps start kar deta hai parent nodes directions me.
3. **The Static interception**: Nearest structural fiber block par `getDerivedStateFromError` method matching flags verify hoke state updates register karta hai.
4. **The Commit phase dispatch**: State register complete hote hi reconciler commit updates scheduling loop dispatch karke parent component's error state apply karta hai and fallback templates display updates execute ho jati hain screen par.
5. **The Side effects logging**: Reconciler tree stabilization phase finished triggers resolve hote hi asynchronous non-blocking pipelines `componentDidCatch` lifecycle run hoke logs transmitters and callbacks fire up execute karwata hai.

### ASCII Diagram: Error Propagation Lifecycle
```text
  [ Error Occurs in Leaf Node ] ──► (React stops DOM paint)
               │
               ▼
  [ Bubble upward in Fiber Tree ]
               │
               ▼
  [ Capture: getDerivedStateFromError() ] ──► Returns state update {hasError: true}
               │
               ▼
  [ DOM Commit: Fallback UI Paints ]
               │
               ▼
  [ Event Call: componentDidCatch() logs details ]
```

### Flow Diagram: Dynamic Action Error Recovery
```text
[Form Submissions Action throws error]
                 │
                 ▼
[Does Parent ErrorBoundary wrap-up exist?]
       ├── NO  ──► React unmounts standard viewport screen (Blank screen)
       └── YES ──► Intercept Exception & set State flag true
                 │
                 ▼
    [Renders fallback UI with Recovery buttons]
                 │
                 ▼
    [Click Event: resetErrorBoundary() trigger]
                 │
                 ▼
    [Re-evaluates component subtree rendering passes]
```

---

## 3. WHEN NOT TO USE ERROR BOUNDARIES

1. **Standard inline synchronous actions**: Button click events validations aur input values checking steps are not targets.
2. **Asynchronous timeouts exceptions**: JavaScript native `setTimeout` callbacks exceptions are completely invisible to Error Boundaries reconciler runs.

---

# SECTION 6: THE TECHNICAL INTERVIEW BLUEPRINT (65 QUESTIONS)

Bhai, standard examinations aur strict engineering validation cycles ke liye interview prep segment ko exact levels par configure kiya gaya hai:

---

## 1. Beginner Interview Questions (1-15)

### Q1: What is the primary purpose of the `getDerivedStateFromError` lifecycle method?
*   **Professional English Answer**: `getDerivedStateFromError` is a static lifecycle method in class-based Error Boundaries designed to update the component's state synchronously when a rendering error occurs in a descendant component. It returns a state object that triggers a fallback UI render during the commit phase.
*   **Easy Hinglish Explanation**: Jab bhi child component me render phase me crash hoga, `getDerivedStateFromError` static method trigger hota hai, jisse hum instantly `{ hasError: true }` state return karke fallback UI display kar pate hain.
*   **Follow-up Questions**:
    1. Can we call side-effects like API logging inside getDerivedStateFromError?
    2. What does it return?

---

### Q2: What is the role of `componentDidCatch`?
*   **Professional English Answer**: `componentDidCatch` is used to capture exception trace diagnostics, log errors to external services, or execute post-crash side-effects. Unlike `getDerivedStateFromError`, it runs during the commit phase and is suitable for asynchronous tasks.

---

### Q3: Why can't we use functional components natively as Error Boundaries?
*   **Professional English Answer**: Because React's functional hooks do not have equivalent implementations for class-based lifecycle hooks like `getDerivedStateFromError` or `componentDidCatch`.

---

### Q4: List three error categories that Error Boundaries cannot catch.
*   **Professional English Answer**: (1) Errors in event handlers, (2) asynchronous code exceptions (e.g., `setTimeout`), and (3) Server-Side Rendering (SSR) failures.

---

### Q5: What does the React DevTools console show when an error is caught?
*   **Professional English Answer**: In development, it highlights the component stack trace and displays caught error markers without unmounting the parent tree.

---

### Q6: Can a single component act as an Error Boundary for itself?
*   **Professional English Answer**: No, an Error Boundary only catches errors that occur within its descendant components, not within itself.

---

### Q7: What is the return structure of `getDerivedStateFromError`?
*   **Professional English Answer**: It must return an object representing the updated state or `null` if no state update is needed.

---

### Q8: What does the browser console display if an error is uncaught by any boundary?
*   **Professional English Answer**: It throws an "Uncaught Exception" and React will completely unmount the entire page tree.

---

### Q9: What happens if `componentDidCatch` is omitted?
*   **Professional English Answer**: The boundary will still catch errors and render fallback UI normally, but it won't perform logging.

---

### Q10: Why are Error Boundaries compared to try-catch blocks?
*   **Professional English Answer**: They serve a similar purpose but are designed specifically for React's declarative component architecture rather than imperative code blocks.

---

### Q11: What import is required to build a class-based Error Boundary?
*   **Professional English Answer**: Import `React` and `Component` from the core `'react'` library.

---

### Q12: Can we nesting multiple Error Boundaries?
*   **Professional English Answer**: Yes, to localize fallbacks and prevent a crash in one widget from affecting the rest of the application.

---

### Q13: Does an Error Boundary catch compilation errors?
*   **Professional English Answer**: No, it only catches runtime rendering exceptions.

---

### Q14: How does ESLint help with Error Boundaries?
*   **Professional English Answer**: The `eslint-plugin-react-hooks` package checks for proper boundary placements.

---

### Q15: What is the default fallback UI of the standard react-error-boundary?
*   **Professional English Answer**: It does not have one; you must specify either a fallback prop or fallback component.

---

## 2. Intermediate Interview Questions (16-30)

### Q16: Explain how to reset an Error Boundary's state to recover from errors.
*   **Professional English Answer**: To recover from an error, the boundary can provide a callback (like `resetErrorBoundary`) that resets the component state (`hasError: false`), allowing React to attempt re-rendering the children.
*   **Easy Hinglish Explanation**: hasError state ko false par reset karke, aur target parent state variables keys ko mutate karke, hum children ko dynamic re-render patterns provide kar sakte hain.
*   **Follow-up Questions**:
    1. How does react-error-boundary facilitate this?
    2. What is the onReset prop?

---

### Q17: Contrast Error Boundary behavior during Development vs. Production.
*   **Professional English Answer**: In development, React overlays the crash on screen and prints diagnostic logs. In production, it silently catches the error and displays the fallback UI.

---

### Q18: Why is getDerivedStateFromError a static method?
*   **Professional English Answer**: Because it executes during the render phase before any component instance has committed, so it cannot access `this` or instance-specific methods.

---

### Q19: Explain how react-error-boundary handles asynchronous errors under the hood.
*   **Professional English Answer**: It wraps async callbacks in event handlers that throw exceptions during subsequent render ticks, allowing the boundary to catch them.

---

### Q20: How do you handle hydration mismatch errors in React 19?
*   **Professional English Answer**: React 19 provides onCaughtError and onUncaughtError callbacks on createRoot to handle hydration mismatches elegantly.

---

### Q21: What is the risk of calling setState inside componentDidCatch?
*   **Professional English Answer**: It is discouraged because it can trigger infinite rendering loops if the fallback itself throws an error.

---

### Q22: Can a boundary catch errors in a portaled component?
*   **Professional English Answer**: Yes, because events and errors in portals propagate through the React virtual tree logically.

---

### Q23: Why should we use multiple Error Boundaries?
*   **Professional English Answer**: To isolate component failures so that a crash in a non-critical widget doesn't take down the entire page.

---

### Q24: How does getDerivedStateFromError integrate with Suspense?
*   **Professional English Answer**: If a promise is rejected, Suspense delegates the recovery to the nearest Error Boundary.

---

### Q25: Can an Error Boundary catch errors inside custom hooks?
*   **Professional English Answer**: Yes, because custom hooks run within the render lifecycle of the component using them.

---

### Q26: Does useTransition require an Error Boundary?
*   **Professional English Answer**: Yes, wrapping transitions in an Error Boundary ensures asynchronous transition failures are caught.

---

### Q27: How do you test Error Boundaries using Jest or Vitest?
*   **Professional English Answer**: By rendering a component that throws an error, wrapping it in the boundary, and asserting that the fallback UI is displayed.

---

### Q28: Can a boundary catch compile-time syntax errors?
*   **Professional English Answer**: No, syntax errors prevent compilation and must be fixed during build time.

---

### Q29: What is the onReset prop in react-error-boundary used for?
*   **Professional English Answer**: It defines a callback that executes when the boundary's error state is reset, allowing you to synchronize parent state changes.

---

### Q30: How can we implement fallback UI dynamically using props?
*   **Professional English Answer**: By passing a custom component or element to the fallback prop of your Error Boundary.

---

## 3. Advanced Interview Questions (31-45)

### Q31: How does React's reconciler handle throwing promises vs. throwing errors during rendering loops?
*   **Professional English Answer**: When a component throws an unresolved promise, the reconciler halts rendering and looks for the nearest `Suspense` boundary. However, if it throws a standard JavaScript `Error` or a rejected promise, the reconciler bypasses Suspense and propagates the exception to the nearest class-based `Error Boundary`.
*   **Easy Hinglish Explanation**: React Reconciler engine rendering loops me exceptions catch karta hai. Agar component Promise throw karega toh `Suspense` use execute karega, par agar raw error throw hoga toh processing directly `ErrorBoundary` lifecycle methods ki taraf delegate ho jayegi.
*   **Follow-up Questions**:
    1. What happens if a promise rejects?
    2. Can use() hook throw both?

---

### Q32: Explain the internal stack allocation differences during React 19's error consolidation.
*   **Professional English Answer**: React 19 consolidates duplicate logs from different parts of the boundary system into a single clean stack trace to improve debugging clarity.

---

### Q33: How does Concurrent Rendering (React 18+) affect Error Boundary execution?
*   **Professional English Answer**: If a render pass is aborted or updated, the boundary guarantees that state updates only commit once the tree reaches a stable layout state.

---

### Q34: What is the risk of using uncached promises with the `use()` hook?
*   **Professional English Answer**: Uncached promises recreate on every render pass, triggering infinite suspension and rendering loops.

---

### Q35: Contrast error handling in Server Components vs. Client Components.
*   **Professional English Answer**: Server Components handle errors during server rendering, whereas Client Components handle interactive rendering crashes on the client.

---

### Q36: How does StrictMode interact with class-based Error Boundaries?
*   **Professional English Answer**: StrictMode invokes lifecycle methods twice to verify render purity and flush out side-effects.

---

### Q37: Can a portal unmount parent error boundaries during crashes?
*   **Professional English Answer**: No, portals are managed within the logical React tree, so the parent Error Boundary handles any crashes gracefully.

---

### Q38: How do we prevent infinite loops when recovering from API errors?
*   **Professional English Answer**: By implementing strict state updates or resetting keys to force a clean remount of components.

---

### Q39: Can class components use functional hooks internally?
*   **Professional English Answer**: No, hooks are strictly limited to functional components and custom hooks.

---

### Q40: What happens when an Error Boundary itself throws an error?
*   **Professional English Answer**: The exception propagates further up the tree to the next nearest Error Boundary parent.

---

### Q41: How do you build a custom logging service integration with componentDidCatch?
*   **Professional English Answer**: By using `componentDidCatch` to package error stack traces and transmit them asynchronously to logging servers.

---

### Q42: Explain the performance impact of nesting many Error Boundaries.
*   **Professional English Answer**: The overhead is minimal because React optimized fiber structures are highly efficient.

---

### Q43: How do you avoid white screen issues in SSR builds?
*   **Professional English Answer**: Ensure that root components are wrapped in a robust Error Boundary that handles hydration mismatches safely.

---

### Q44: Can you pass functions to the fallback prop of react-error-boundary?
*   **Professional English Answer**: Yes, using the `FallbackComponent` prop allows you to pass a component function directly.

---

### Q45: Why is Error Boundary state update considered a layout side-effect?
*   **Professional English Answer**: Because it intercept failures during rendering passes and must commit fallback UI synchronously before repaint.

---

## 4. Scenario-Based Questions (46-55)

### Q46: Scenario: Clicking "Verify Credentials" crashes, but parent boundary does not render fallback UI. What was violated?
*   **Professional English Answer**: The crash likely occurred inside an event handler, which is outside the render loop and not caught by Error Boundaries.
*   **Easy Hinglish Explanation**: Click handler events rendering stack ke outside run hote hain, isliye standard Error Boundaries unhe catch nahi kar pate. Local try-catch state update execute karna hoga.

---

### Q47: Scenario: Input fields lose focus during rendering loops inside custom forms. Why?
*   **Professional English Answer**: Recreating components or nested states inside the render body forces React to unmount the entire subtree on every pass, losing focus.

---

### Q48: Scenario: Component crashes with "Suspense Exception" error inside try-catch. What is the bug?
*   **Professional English Answer**: The component called the `use` hook inside a try-catch block, which intercepts React's internal promise tracking. Use an Error Boundary instead.

---

### Q49: Scenario: Nested page routes crash the entire page layout. How do you resolve this?
*   **Professional English Answer**: Wrap individual routes in localized Error Boundaries to quarantine failures and keep the main shell active.

---

### Q50: Scenario: Forms inside payment overlays reset unexpectedly on button click. Why?
*   **Professional English Answer**: Button elements inside forms default to `type="submit"`, triggering unexpected submissions and page reloads. Set `type="button"` explicitly.

---

### Q51: Scenario: Infinite loop crashes the browser when loading user data. Why?
*   **Professional English Answer**: An effect is likely updating a state variable that is also listed as a dependency, triggering endless render cycles.

---

### Q52: Scenario: State updates inside transitions fail to reflect. What is the fix?
*   **Professional English Answer**: Ensure that custom dispatch actions are wrapped inside `startTransition` to allow React to track pending states.

---

### Q53: Scenario: Stale state values are captured inside child components. Why?
*   **Professional English Answer**: Stale closures occur when component states mutate directly without using updater functions or proper dependencies.

---

### Q54: Scenario: SSR hydration fails with mismatch logs on render. How do you recover?
*   **Professional English Answer**: Ensure that server-client rendered markups match on load, or defer dynamic client renders inside `useEffect`.

---

### Q55: Scenario: Multiple simultaneous clicks on a submit button trigger duplicate server requests. How do you prevent this?
*   **Professional English Answer**: Disable the submit button during in-flight transitions using the `isPending` state variable.

---

## 5. Live Coding Questions (56-60)

### Q56: Code a basic functional component using standard Class Error Boundary.
```jsx
import React, { Component } from 'react';

export default class BaseBoundary extends Component {
  state = { hasError: false };
  static getDerivedStateFromError() { return { hasError: true }; }
  render() {
    if (this.state.hasError) return <h2>Something went wrong.</h2>;
    return this.props.children;
  }
}
```

---

### Q57: Code a custom error fallback with stopPropagation.
```jsx
import React from 'react';

const BlockedPanel = ({ error, reset }) => {
  return (
    <div onClick={(e) => e.stopPropagation()} style={{ border: '2px solid red' }}>
      <h3>Crash: {error.message}</h3>
      <button onClick={reset}>Reset</button>
    </div>
  );
};
```

---

### Q58: Code a secure form action block.
```jsx
import React from 'react';
import { ErrorBoundary } from 'react-error-boundary';

async function formAction() {
  throw new Error("Action failed");
}

export default function SecureForm() {
  return (
    <ErrorBoundary fallback={<p>Form action error handled.</p>}>
      <form action={formAction}>
        <button type="submit">Submit</button>
      </form>
    </ErrorBoundary>
  );
}
```

---

### Q59: Code a React 19 conditional use(promise) implementation wrapped inside Error Boundary.
```jsx
import React, { use, Suspense } from 'react';
import { ErrorBoundary } from 'react-error-boundary';

function IngestNode({ promise }) {
  const data = use(promise);
  return <p>Resolved: {data}</p>;
}

export default function App({ failedPromise }) {
  return (
    <ErrorBoundary fallback={<p>Rejection caught.</p>}>
      <Suspense fallback={<p>Hydrating...</p>}>
        <IngestNode promise={failedPromise} />
      </Suspense>
    </ErrorBoundary>
  );
}
```

---

### Q60: Code a React 19 useTransition router switch skeleton.
```jsx
import React, { useState, useTransition } from 'react';
import { ErrorBoundary } from 'react-error-boundary';

export default function TransitionApp() {
  const [isPending, startTransition] = useTransition();
  return (
    <ErrorBoundary fallback={<div>Transition crashed.</div>}>
      <button onClick={() => startTransition(() => { throw new Error(); })}>
        {isPending ? "Pending..." : "Trigger transition crash"}
      </button>
    </ErrorBoundary>
  );
}
```

---

## 6. Debugging Scenarios (61-65)

### Q61: Debug this code: App crashes with un-caught TypeError during render.
```jsx
// 🔴 Buggy Code: use() is wrapped inside a try-catch block!
function Albums({ promise }) {
  try {
    const data = use(promise);
  } catch (err) {
    return <p>Error</p>;
  }
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code: Wrap components using use() inside an Error Boundary!
import { ErrorBoundary } from 'react-error-boundary';

function Albums({ promise }) {
  const data = use(promise); //
  return <p>{data}</p>;
}

export default function App({ promise }) {
  return (
    <ErrorBoundary fallback={<p>Caught error safely.</p>}>
      <Albums promise={promise} />
    </ErrorBoundary>
  );
}
```
*   **Reasoning**: `use()` relies on throwing promises and exceptions to communicate with Suspense and Error Boundaries. Wrapping it in try-catch breaks this mechanism and causes crashes.

---

### Q62: Debug this code: UI does not update on clicking delete task button.
```jsx
// 🔴 Buggy Code: Array mutated directly, preserving reference pointer!
const [tasks, setTasks] = useState([]);
const removeTask = (idx) => {
  tasks.splice(idx, 1); // Mutating state array directly!
  setTasks(tasks);
};
```
*   **Correct Code**:
```jsx
// ✅ Correct Code: Return a new array copy immutably
const [tasks, setTasks] = useState([]);
const removeTask = (idx) => {
  setTasks(prev => prev.filter((_, i) => i !== idx)); // Filter returns a new array reference
};
```
*   **Reasoning**: React uses shallow reference comparisons to detect updates. Mutating state directly does not change the reference, so React skips the re-render pass.

---

### Q63: Debug this code: Input focus is lost on every character typed.
```jsx
// 🔴 Buggy Code: Component declared nested inside parent render body!
export default function ParentApp() {
  const [text, setText] = useState("");
  const NestedInput = () => <input value={text} onChange={e => setText(e.target.value)} />;
  return <NestedInput />;
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code: Declare component outside parent body
function NestedInput({ text, onUpdate }) {
  return <input value={text} onChange={onUpdate} />;
}

export default function ParentApp() {
  const [text, setText] = useState("");
  return <NestedInput text={text} onUpdate={e => setText(e.target.value)} />;
}
```
*   **Reasoning**: Nested declarations recreate components on every render, causing React to destroy and remount the DOM subtree and lose focus.

---

### Q64: Debug this code: Infinite rendering loop inside list search queries.
```jsx
// 🔴 Buggy Code: Function reference recreated on every render
export default function SearchApp() {
  const [query, setQuery] = useState("");
  const runFilter = () => {
    console.log("Filtering...", query);
  };
  useEffect(() => {
    runFilter();
  }, [runFilter]); // Re-creates helper on every render, looping rendering cycles!
}
```
*   **Correct Code**:
```jsx
// ✅ Correct Code: use useCallback to maintain stable reference
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
*   **Reasoning**: Recreating helper functions on every render pass triggers infinite effects loops if they are listed as dependencies. `useCallback` stabilizes the reference.

---

### Q65: Debug this code: App uses stale closures capturing historical state.
```jsx
// 🔴 Buggy Code: Missing dependencies array tracks stale values
const [count, setCount] = useState(0);
const saveLog = useCallback(() => {
  console.log("Current count is:", count); // Always logs 0 due to stale closure!
}, []); // Empty array captures initial state value only!
```
*   **Correct Code**:
```jsx
// ✅ Correct Code: Add dependencies to let closure update
const [count, setCount] = useState(0);
const saveLog = useCallback(() => {
  console.log("Current count is:", count);
}, [count]); // Correct dependency tracking
```
*   **Reasoning**: An empty dependency array locks the callback's closure to values from the first render pass. Including variables in the dependency array keeps closures updated.

---

## 7. Educational Assessment & Study Guides

### Mini Assignment
1. Vite environment setup karke ek profile dashboard design karein.
2. Class-based custom Error Boundary with reset capability apply karein.
3. Multiple nested widgets me boundaries wrapping apply karke validation triggers verify karein.

---

### Practice Questions
1. `componentDidCatch` aur `getDerivedStateFromError` ke render flow timing parameters differentiate karein.
2. React 19 consolidated logs aur hydration mismatch error checks write down best practices.

---

### Multiple Choice Questions (MCQs)

1. **Which lifecycle method runs asynchronously during the commit phase?**
    * (A) getDerivedStateFromError
    * (B) componentDidCatch
    * (C) shouldComponentUpdate
    * *Correct Answer: (B)*

2. **Why can't Error Boundaries catch click event handler crashes?**
    * (A) Because click events run synchronously.
    * (B) Because event handlers execute outside the component rendering cycle.
    * (C) Because click events use native DOM.
    * *Correct Answer: (B)*

---

### Revision Notes
* **Local quarantine**: Use nested boundaries to keep unaffected parts of the application working.
* **No render mutations**: Never write state mutations directly in render bodies.

---

### Cheat Sheet
```jsx
// Standard Class Error Boundary
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  static getDerivedStateFromError() { return { hasError: true }; }
  render() {
    if (this.state.hasError) return this.props.fallback;
    return this.props.children;
  }
}
```
