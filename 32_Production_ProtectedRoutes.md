# PHASE 5: PRODUCTION GRADE MERN — CHAPTER 3: PROTECTED ROUTES 🛡️ MERN MASTERCLASS

Oi mere bhai! Kaise ho? Jaldi se ek aur garam cup chai bana lo ☕ [cite: 245], kyunki aaj hum front-end security ka sabse zordaar aur absolute production-essential concept seekhne ja rahe hain—**Protected Routes** (Route Guards) [cite: 246, 365, 417]!

Ek baat achhe se dhyan me rakhna: **"Backend par API ko secure karna jitna zaroori hai, frontend par user ko un-authorized pages se dur rakhna aur user roles ke hisab se dashboard dikhana utna hi critical hai."** [cite: 97, 417, 506] Agar tumne ek badhiya admin panel banaya par user browser ke URL bar me direct `/admin` likh kar enter maare aur page bina login ke khul jaye, to samajh lo tumhari security "fail" ho gayi [cite: 97, 166].

Is chapter me hum React Router, Context API, aur token checks ko aapas me ek dam tight link karenge taaki tumhari client-side routing bullet-proof ho jaye [cite: 168, 205, 417]! Let's crack this in full MERN style!

---

## SECTION 1: TOPIC-BY-TOPIC COGNITIVE ANALYSIS 🧠

Bhai, ek MERN architect ki tarah har ek concept ko deep me samajhte hain taaki tum production me kabhi na faso!

---

### 1. Protected Route (Private Route)

#### Best Definition (Easy Hinglish)
Protected Route ek aisa route wrapper ya guard hota hai jo sirf authenticated users ko hi specific page access karne deta hai. Agar user logged-in nahi hai, to ye use automatic `/login` page par dhakel (redirect) deta hai [cite: 365, 417].

#### Ye kya hai?
Ye ek Higher-Order Component (HOC) ya wrapper component hota hai jo children routes ke render hone se pehle check karta hai ki user ke paas login session active hai ya nahi [cite: 365, 416, 417].

#### Kyu use karte hain?
Users ke personal dashboards, setting panels, billing history, aur dynamic profiles ko raw anonymous web traffic se secure rakhne ke liye [cite: 365, 383, 417].

#### Ye kya problem solve karta hai?
Agar user logged out hai aur wo directly URL bar me `/dashboard` type karta hai, to bina protected route ke React use blank ya half-rendered page dikha deta. Protected Route use block karke directly login page par redirect kar deta hai [cite: 365, 417].

#### Kaise kaam karta hai? (Internal Working)
React Router tree parse karte waqt, Protected Route first step me Auth Context se token/state verify karta hai [cite: 205, 365, 417]. Agar state true hai, to `<Outlet />` ya `{children}` render hota hai [cite: 200, 365, 417]. Agar false hai, to `<Navigate to="/login" replace />` action execute ho jata hai [cite: 356, 365, 417].

#### Real-life Example
**Metro Station Automatic Entry Gates** 🎫 [cite: 396]
Metro platform (`Dashboard/Protected Route`) par jane ke liye aapko pehle automatic entry gate par ticket scan karna hoga. Agar card me balance (`Token`) hai, to door khulega (`Render Children`), nahi to gate closed rahega aur aapko ticket counter (`Login Page`) par bhej diya jayega.

#### Kab use karte hain?
User dashboard, checkout pages, account settings, aur private booking systems design karte waqt [cite: 325, 365].

#### Kab use nahi karte?
Static Landing Pages, Public FAQs, Contact Us, ya Pricing charts display karte waqt (wahan ye over-kill hai) [cite: 347, 420].

#### Common Mistakes
Check-state ko sirf local Component memory variable me rakhna aur initial load par localStorage checks miss kar dena, jisse page refresh par login state ud jati hai [cite: 261, 394].

#### Best Practices
Hamesha transition load timers manage karein (loading spinner jab tak initial token verify check chal raha ho) taaki false redirect na ho [cite: 281, 282].

---

### 2. Public Route

#### Best Definition (Easy Hinglish)
Public Route wo routes hote hain jo poori dunia ke liye khule (accessible) hote hain. Kisi ko bhi in pages ko dekhne ke liye login karne ki koi zaroorat nahi hoti [cite: 347, 420].

#### Ye kya hai?
Ye normal, un-guarded standard routes hote hain jo publicly accessible paths (jaise `/`, `/about`, `/contact`) se mapped hote hain [cite: 165].

#### Kyu use karte hain?
Website ki main branding, marketing content, aur search engine optimization (SEO) pages ko bina kisi barrier ke display karne ke liye [cite: 275, 347].

#### Ye kya problem solve karta hai?
Ye un-necessary friction dur karta hai. User ko basic information read karne ke liye forced signup/login nahi karna padta.

#### Kaise kaam karta hai?
React Router directly route component ko load kar deta hai bina kisi token ya Context parameter checks ko intercept kiye [cite: 168, 246].

#### Real-life Example
**Mall Ka Entrance & Common Area** 🏢
Mall ke bahar khadi security aapko mall ke andr jane se nahi rokti. Mall ka common lobby area sab ke liye open hai—chahe aapke paas paise hon ya nahi.

#### Kab use karte hain?
`/about`, `/contact-us`, `/pricing`, `/terms-of-service` jaise general informational components me [cite: 165, 420].

#### Kab use nahi karte?
Payment confirmation, dynamic profiles editing modules ya database control routes me [cite: 97].

#### Common Mistakes
Public routes me unintentionally user context dynamic values direct use karna bina optional chaining `?.` ke, jisse guest user aane par app crash ho jati hai [cite: 24, 228].

#### Best Practices
Public page headers me hamesha dynamic conditional checks lagayein (jaise user logged-in hai to login button hide karke Profile button dikhayein) [cite: 230].

---

### 3. Route Guard (Router Guards)

#### Best Definition (Easy Hinglish)
Route Guard ek functional security layer hai jo decide karta hai ki browser URL change hone par target component load hona chahiye ya bypass hokar redirect hona chahiye [cite: 365, 417].

#### Ye kya hai?
Ye software architecture ka ek design pattern hai jo routing pipelines me ek controller check interceptor ki tarah functional role play karta hai [cite: 365, 416].

#### Kyu use karte hain?
Centralized routing verification logic establish karne ke liye, taaki har ek individual file me baar-baar login checks na likhne padein [cite: 365, 417].

#### Ye kya problem solve karta hai?
Bina central Route Guard ke, aapko har page ke component ke andr `useEffect` me localStorage token read karke manually redirect code likhna padta [cite: 188, 261, 365]. Isse duplicate aur messy code base ban jata [cite: 211, 424].

#### Kaise kaam karta hai?
Route wrapper React core render lifecycle ko intercept karta hai. Condition check pass hone par execution continuous rehti hai, else route dynamic navigation force-redirect triggers fire karta hai [cite: 356, 365, 417].

#### Real-life Example
**Border Security Checkpoint** 🛂
Aap ek shehar se dusre shehar ja rahe hain. Beech highway par toll plaza ya security checkpoint check karta hai ki aapke paas travel permit hai ya nahi.

#### Kab use karte hain?
Bade scale ke enterprise SPAs me routing layers secure karne ke liye [cite: 247, 365].

#### Kab use nahi karte?
Chote single page static components me.

---

### 4. Authentication Check vs Authorization Check

| Parameter | Authentication Check 🔑 | Authorization Check 🛡️ |
| :--- | :--- | :--- |
| **Sawal (Goal)** | "Kya aap logged-in user ho?" [cite: 362, 455] | "Kya aapke paas is specific page ki permission hai?" [cite: 549, 551] |
| **Responsibility** | Token ya dynamic login state ki validity check karta hai [cite: 365, 416, 417]. | Logged-in user ka `role` (Admin, Member, Guest) check karta hai [cite: 383, 549, 551]. |
| **Loopholes Prevented** | Session hijacking, guest users invading private routes [cite: 365, 383, 417]. | Privilege escalation (User trying to access admin configurations) [cite: 383, 553]. |
| **Typical Redirect** | Redirects to `/login` [cite: 365, 417]. | Redirects to `/unauthorized` or `/403` [cite: 383, 551]. |

---

### 5. Role-Based Access Control (RBAC)

#### Best Definition (Easy Hinglish)
Role-Based Access Control (RBAC) ka matlab hai authenticated users ko unke roles (jaise Admin, User, Moderator) ke adhaar par components aur routing access provide karna [cite: 383, 549, 551].

#### Ye kya hai?
Ye authorization check ka core production standard design implementation hai jahan single application dashboard multiple permissions profiles safely resolve karta hai [cite: 383, 549].

#### Kyu use karte hain?
Taaki normal users, business controllers aur admin developers ke operations area completely isolated aur separate rahein [cite: 383, 549, 551].

#### Ye kya problem solve karta hai?
Normal customer agar hack try karke `/admin/delete-database` API trigger karna chahe, to RBAC use block kar deta hai, protecting critical resources [cite: 97, 383].

#### Kaise kaam karta hai?
`ProtectedRoute` component me optional `allowedRoles` props array pass kiya jata hai [cite: 399]. Wrapper check karta hai: `allowedRoles.includes(user.role)` [cite: 383, 549, 551]. True hone par child element render hota hai, warna access restrict ho jata hai [cite: 417, 551].

#### Real-life Example
**Office Access Keycards** 💳
Office building me sub-employees enter kar sakte hain, par Server Room me sirf authorized System Admins ka card scan chalega. Normal staff room keycard wahan error lights flash karega [cite: 342, 549].

#### Kab use karte hain?
B2B systems, complex CRM dashboards aur e-commerce systems ke orders panels me [cite: 247, 549].

#### Kab use nahi karte?
Simple single-role client applications me.

#### Common Mistakes
Frontend par routes role check blocks block kar dena par backend Express endpoints par role check validation rules drop na karna (bypassing via external tools like Postman/cURL) [cite: 383, 671].

#### Best Practices
Frontend security UX transition bachaati hai; real safety backend validations level par ensure karein. JWT token payload me role save karke use sign karein [cite: 383, 520, 549].

---

### 6. Redirect After Login (Target State Redirection)

#### Best Definition (Easy Hinglish)
Redirect After Login ka matlab hai ki jab user kisi protected URL ko direct hit kare aur system use login page par bhej de, to login safal (success) hone ke baad use direct usi targeted URL par wapas bhej dena, na ki generic home page par [cite: 351, 371].

#### Ye kya problem solve karta hai?
Ye major user friction ko solve karta hai. Agar user ne kisi specific booking panel page `/bookings/edit/99` par direct click kiya ho, to login ke baad use dubara dhoondhna nahi padta ki wo pichle kis screen par tha [cite: 346, 351, 352].

#### Kaise kaam karta hai? (Internal Working)
Protected Route block triggers hotey hi standard `useLocation()` hook se current path state variables capture kiye jate hain [cite: 4, 320]. Location state ko as metadata state property navigation redirection me pass kar diya jata hai: `<Navigate to="/login" state={{ from: location }} replace />` [cite: 4, 320, 356]. Login success hone par state check karke `navigate(location.state?.from || "/dashboard")` call trigger ho jata hai [cite: 320, 356].

#### Real-life Example
**Airport Lounge Redirect** ✈️
Lounge entries block criteria checks. Security details check clear hone ke baad aap seedhe usi targeted flight waiting area lounge gate par laut jate hain jahan aap shuruat me khade the.

---

### 7. Unauthorized Access (401 vs 403 Errors)

#### 401 Unauthorized (Unauthenticated) [cite: 229, 472]
*   **Best Definition (Easy Hinglish):** Server kehta hai—**"Tum kaun ho, main tumhein nahi jaanta. Pehle apni identity prove karo (Login karo)!"** [cite: 362, 455]
*   **Kaise handle karein:** Frontend par user token delete karke use `/login` screen par redirect karein [cite: 261, 365, 456].

#### 403 Forbidden [cite: 229]
*   **Best Definition (Easy Hinglish):** Server kehta hai—**"Main jaanta hoon tum kaun ho, tumhara login sahi hai. Lekin tumhare paas is folder ko dekhne ki aukaat/permission nahi hai!"** [cite: 383, 549]
*   **Kaise handle karein:** User ko `/unauthorized` or `/403-access-blocked` warning view render karwayein [cite: 383, 551].

---

### 8. Remember Last Route

#### Best Definition (Easy Hinglish)
User jab application band karta hai ya browser tab close karta hai, to pichli active routing location state path save rakhna, aur dubara tab open karne par automatic usi targeted page par hydrate dynamic dashboard panel display kar dena [cite: 21, 261, 325].

#### Real-life Example
**Book Bookmark Ribbon** 🔖
Kitab band karte waqt ribbon lagana, taaki next time read start karte waqt direct wahi page open ho sake jahan padhna band kiya tha.

#### Best Practices
Hamesha routing changes par current URL path parameters `localStorage.setItem('active_route', location.pathname)` par track karein [cite: 4, 21, 261].

---

## SECTION 2: ADDITIONAL (WEB RESEARCH) - ADVANCED COMPOSITION 🔍

### The React 19 Promise Unwrapping API `use()` inside Guards [cite: 2, 257, 268]
*   Modern React 19 environments me dynamic authentication check ko optimize karne ke liye, `use()` context-based hooks use kiye ja sakte hain, jo routes rendering cycles me directly client context streams evaluate kar sakte hain [cite: 2, 257, 268].
*   Is stateless secure model me, routing engines conditional hooks validations directly execute kar sakte hain, jisse route updates automatic React 19 compile frameworks me safely flow hotey hain [cite: 253, 268].

---

## SECTION 3: PACKAGE MANAGEMENT & DEPENDENCIES 🛠️

Humare system architecture me routing aur guards handle karne ke liye teen major dependencies use hoti hain:
1. **react-router-dom** (Production standard routing framework) [cite: 28, 171]
2. **jwt-decode** (Client-side token parsing helper)

---

### Library: `react-router-dom` [cite: 28, 171]

#### npm install command [cite: 328]
```bash
npm install react-router-dom
```

#### npm uninstall command
```bash
npm uninstall react-router-dom
```

#### Kis folder me command chalani hai? [cite: 34]
Hamesha project ke **Root Directory** me jahan `package.json` file exist karti hai [cite: 34].

#### Install verify kaise kare? [cite: 34]
`package.json` open karein, check `"dependencies"` section, wahan `"react-router-dom": "^x.x.x"` details mapping check kijiye [cite: 34].

#### Common installation issues & solution
- **Peer Dependency Conflicts:** `npm install react-router-dom --legacy-peer-deps` flag use karke installation forced bypass target clear karein.

---

## SECTION 4: PROJECT INTEGRATION ROADMAP 🗺️

MERN layout structures design me files directory alignments kahan and kaise compile kiye jayenge, niche clean production map diya gaya hai [cite: 220, 271, 326]:

```text
secure-mern-project/
├── package.json
└── src/
    ├── main.jsx                  # Bootstrapping React app [cite: 35, 552]
    ├── App.jsx                   # Setting up Router Providers [cite: 33, 177, 221]
    ├── context/
    │   └── AuthContext.jsx        # Managing global auth state registers [cite: 168, 213]
    ├── guards/
    │   └── ProtectedRoute.jsx    # NEW: Secure dynamic Router Guards! [cite: 365, 417]
    └── pages/
        ├── Login.jsx             # Auth credentials inputs form [cite: 362, 451]
        ├── UserDashboard.jsx     # Logged-in standard members page [cite: 325, 404]
        ├── AdminPanel.jsx        # Restricted Administrative system console [cite: 404, 549]
        └── Unauthorized.jsx      # Access denied fallback warning component [cite: 383, 551]
```

### Flow Lifecycle (Login to Guard Redirection) [cite: 365, 417, 455]
```text
  [Enter Restricted Route] ──► [ProtectedRoute Intercepts] ──► [Check context state]
                                                                        │
                                      ┌─────────────────────────────────┘
                                      ▼
                      [Authenticated? Yes / No]
                       ├── Yes ──► Render <Outlet /> (Dashboard) [cite: 200, 365]
                       └── No  ──► Render <Navigate to="/login" state={{ from: currentPath }} /> [cite: 320, 356, 365]
```

---

## SECTION 5: THE PRACTICAL SANDBOX 💻

Bhai, step-by-step verified, solid executable codes implement karte hain! [cite: 395]

---

### Example 1: Beginner level - Basic Protected Route Guard with local state [cite: 1, 168, 356, 365, 417]

Simple local checks parameters gating, redirecting to login page natively [cite: 365, 417].

#### Folder Structure
```text
guarded-beginner-routes/
├── package.json
└── src/
    ├── main.jsx
    └── App.jsx
```

#### Complete Code

##### File Name: `src/App.jsx` [cite: 1, 177, 180, 181, 186, 187, 356, 365, 417]
```javascript
import React, { useState } from 'react'; // [cite: 1]
import { 
  createBrowserRouter, 
  RouterProvider, 
  Navigate, 
  Outlet, 
  Link 
} from 'react-router-dom'; // [cite: 173, 177, 178, 200]

// 1. Core Route Guard Component [cite: 365, 417]
function ProtectedRoute({ isAuthenticated }) {
  if (!isAuthenticated) {
    console.warn("ProtectedRoute Guard Event: User unauthenticated, redirecting...");
    return <Navigate to="/login" replace />; // Redirecting to safety [cite: 356, 365, 417]
  }
  return <Outlet />; // Allowing access to nested components [cite: 200, 365]
}

// 2. Page Components layouts [cite: 325, 404, 451]
function Home() {
  return (
    <div style={{ padding: '20px' }}>
      <h3>Public Home Hub 🏢</h3>
      <Link to="/dashboard" style={{ color: 'lightblue' }}>Access Protected Dashboard 🚀</Link> {/* [cite: 173] */}
    </div>
  );
}

function Login({ onAuthenticate }) {
  return (
    <div style={{ padding: '20px', background: '#1e293b', borderRadius: '8px' }}>
      <h3>🔑 Login Credentials Access Required</h3>
      <button onClick={onAuthenticate} style={{ padding: '10px', background: 'green', color: '#fff', border: 'none', cursor: 'pointer' }}>
        Perform Secure Handshake Login
      </button>
    </div>
  );
}

function Dashboard({ onDeauthenticate }) {
  return (
    <div style={{ padding: '20px', background: '#0f172a', border: '1px solid #10b981', borderRadius: '8px' }}>
      <h3>🟢 Secured Operator Console Panel</h3>
      <p>Status parameters: Operational & Active [cite: 179].</p>
      <button onClick={onDeauthenticate} style={{ padding: '8px', background: 'red', color: '#fff', border: 'none', cursor: 'pointer' }}>
        Log Out Subsystem ⚙️
      </button>
    </div>
  );
}

export default function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(false); // Local auth state [cite: 1]

  const handleLogin = () => setIsAuthenticated(true);
  const handleLogout = () => setIsAuthenticated(false);

  // 3. Creating v6 styled routing layouts configuration [cite: 178, 186]
  const routesEngine = createBrowserRouter([
    {
      path: "/",
      element: (
        <div style={{ padding: '24px', fontFamily: 'monospace', color: '#fff', background: '#0b0f19', minHeight: '100vh' }}>
          <h2>Vite Protected Routes Playground</h2>
          <hr style={{ borderColor: '#333', marginBottom: '20px' }} />
          <Outlet /> {/* Layout Outlet [cite: 200] */}
        </div>
      ),
      children: [
        { path: "", element: <Home /> }, // Public root [cite: 180, 181]
        { path: "login", element: isAuthenticated ? <Navigate to="/dashboard" replace /> : <Login onAuthenticate={handleLogin} /> }, // [cite: 356]
        
        // 4. Wrapping dashboard routes inside Protected Route guard [cite: 180, 365]
        {
          element: <ProtectedRoute isAuthenticated={isAuthenticated} />, // Guard [cite: 365, 417]
          children: [
            { path: "dashboard", element: <Dashboard onDeauthenticate={handleLogout} /> } // Nested protected dashboard [cite: 180]
          ]
        }
      ]
    }
  ]);

  return <RouterProvider router={routesEngine} />; // [cite: 177]
}
```

##### File Name: `src/main.jsx` [cite: 396]
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <App />
);
```

#### Browser Output
* Public route `/` loads without issue. Clicking dashboard link triggers Route Guard [cite: 173, 365].
* Unauthenticated state redirects browser path directly to `/login` [cite: 365, 417].
* Clicking Login button updates state, enabling `/dashboard` view to load [cite: 365].

#### Dry Run
1. User clicks `/dashboard` link [cite: 173].
2. React Router matches path `/dashboard` and reaches protected children routing block [cite: 180, 365].
3. Interceptor runs `<ProtectedRoute isAuthenticated={false} />` [cite: 365].
4. Condition evaluate `false` -> redirects to `/login` with clean replace state [cite: 356, 365].

---

### Example 2: Route Guard with Context API and localStorage check [cite: 1, 168, 188, 205, 261, 365, 417, 455]

Secure credentials management using global Auth Provider setups [cite: 168, 205].

#### Folder Structure
```text
guarded-context-localStorage/
├── package.json
└── src/
    ├── main.jsx
    ├── App.jsx
    └── context/
        └── AuthContext.jsx
```

#### Complete Code

##### File Name: `src/context/AuthContext.jsx` [cite: 1, 168, 188, 205, 261]
```javascript
import React, { createContext, useContext, useState, useEffect } from 'react'; // [cite: 1, 168]

const SecureAuthContext = createContext(null); // [cite: 161, 168]

export function AuthProvider({ children }) { // [cite: 168]
  const [tokenActive, setTokenActive] = useState(false);
  const [loading, setLoading] = useState(true); // Load gate indicators [cite: 281]

  // Checking local storage tokens on system mount [cite: 188, 261]
  useEffect(() => {
    const cachedToken = localStorage.getItem('secure_session_key'); // [cite: 261]
    if (cachedToken) {
      setTokenActive(true); // Restoring user auth state [cite: 456]
    }
    setLoading(false); // Resolve check gate [cite: 281]
  }, []);

  const loginAction = (generatedToken) => {
    localStorage.setItem('secure_session_key', generatedToken); // Save [cite: 261, 455]
    setTokenActive(true);
  };

  const logoutAction = () => {
    localStorage.removeItem('secure_session_key'); // Evict [cite: 261, 456]
    setTokenActive(false);
  };

  return (
    <SecureAuthContext.Provider value={{ tokenActive, loginAction, logoutAction, loading }}>
      {children}
    </SecureAuthContext.Provider>
  );
}

export function useAuthSystem() {
  return useContext(SecureAuthContext); // [cite: 132, 233]
}
```

##### File Name: `src/App.jsx` [cite: 1, 177, 180, 186, 356, 365, 417]
```javascript
import React, { useState } from 'react';
import { createBrowserRouter, RouterProvider, Navigate, Outlet } from 'react-router-dom'; // [cite: 177, 200]
import { AuthProvider, useAuthSystem } from './context/AuthContext'; // [cite: 168]

// Protected Route reading directly from Auth Context [cite: 168, 365, 417]
function ProtectedRoute() {
  const { tokenActive, loading } = useAuthSystem(); // [cite: 233]

  if (loading) return <div style={{ color: 'yellow', padding: '24px' }}>⏳ Authenticating credentials...</div>; // Loader gate [cite: 281]

  if (!tokenActive) {
    return <Navigate to="/login" replace />; // Redirect on unauthenticated [cite: 356, 365, 417]
  }

  return <Outlet />; // Render child dashboard if active [cite: 200, 365]
}

function Login() {
  const { loginAction } = useAuthSystem(); // [cite: 233]
  const [operatorCode, setOperatorCode] = useState(''); // [cite: 1]

  const handleVerificationSubmit = (e) => {
    e.preventDefault();
    if (operatorCode.trim() === '') return;
    
    const signedToken = `LARSEN_JWT_KEY_${Date.now()}`;
    loginAction(signedToken); // Trigger login context [cite: 455]
  };

  return (
    <form onSubmit={handleVerificationSubmit} style={{ padding: '20px', maxWidth: '300px', background: '#111827', borderRadius: '8px' }}>
      <h4>🔑 Access Token Verification Required</h4>
      <input 
        value={operatorCode} 
        onChange={(e) => setOperatorCode(e.target.value)} 
        placeholder="Operator code name..." 
        style={{ width: '90%', padding: '10px', background: '#1f2937', color: '#fff', marginBottom: '15px', border: '1px solid #333' }}
      />
      <button type="submit" style={{ width: '100%', padding: '10px', background: '#0284c7', color: '#fff', border: 'none', cursor: 'pointer' }}>
        Verify Credentials
      </button>
    </form>
  );
}

function Dashboard() {
  const { logoutAction } = useAuthSystem(); // [cite: 233]

  return (
    <div style={{ padding: '20px', background: '#064e3b', border: '1px solid #10b981', borderRadius: '8px' }}>
      <h3>🟢 Dashboard Operational Hub</h3>
      <button onClick={logoutAction} style={{ padding: '8px 16px', background: '#dc2626', color: '#fff', border: 'none', cursor: 'pointer' }}>
        Wipe Session Token 🔑 [cite: 456]
      </button>
    </div>
  );
}

export default function App() {
  const router = createBrowserRouter([
    {
      path: "/",
      element: (
        <AuthProvider> {/* Provider wraps app to contextually deliver state [cite: 168] */}
          <div style={{ padding: '24px', fontFamily: 'monospace', color: '#fff', background: '#0b0f19', minHeight: '100vh' }}>
            <h2>Enterprise context-local auth guard</h2>
            <hr style={{ borderColor: '#333', marginBottom: '20px' }} />
            <Outlet /> {/* [cite: 200] */}
          </div>
        </AuthProvider>
      ),
      children: [
        { path: "login", element: <Login /> },
        {
          element: <ProtectedRoute />, // Secured guard [cite: 365, 417]
          children: [
            { path: "dashboard", element: <Dashboard /> } // [cite: 180]
          ]
        }
      ]
    }
  ]);

  return <RouterProvider router={router} />; // [cite: 177]
}
```

##### File Name: `src/main.jsx` [cite: 396]
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <App />
);
```

---

### Example 3: Intermediate level - Route Guard with Location Memory ("Remember Last Route") [cite: 1, 4, 168, 320, 356, 365, 417, 455]

Advanced path memory check redirection, loading user back into target route [cite: 4, 320, 365].

#### Folder Structure
```text
guarded-intermediate-history/
├── package.json
└── src/
    ├── main.jsx
    ├── App.jsx
    └── context/
        └── AuthContext.jsx
```

#### Complete Code

##### File Name: `src/context/AuthContext.jsx` [cite: 1, 168, 261]
```javascript
import React, { createContext, useContext, useState } from 'react'; // [cite: 1, 168]

const AuthContext = createContext(null); // [cite: 161]

export function AuthProvider({ children }) { // [cite: 168]
  const [isLogged, setIsLogged] = useState(false);

  const login = () => {
    localStorage.setItem('auth_active', 'true'); // [cite: 261]
    setIsLogged(true);
  };

  const logout = () => {
    localStorage.removeItem('auth_active'); // [cite: 261]
    setIsLogged(false);
  };

  return (
    <AuthContext.Provider value={{ isLogged, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext); // [cite: 132, 233]
}
```

##### File Name: `src/App.jsx` [cite: 4, 177, 180, 181, 186, 187, 320, 356, 365, 417]
```javascript
import React from 'react';
import { 
  createBrowserRouter, 
  RouterProvider, 
  Navigate, 
  Outlet, 
  useLocation, 
  useNavigate, 
  Link 
} from 'react-router-dom'; // [cite: 4, 177, 200, 320]
import { AuthProvider, useAuth } from './context/AuthContext'; // [cite: 168]

// 1. Guard Component with Location Memory! [cite: 4, 320, 365]
function GuardWithMemory() {
  const { isLogged } = useAuth(); // [cite: 233]
  const location = useLocation(); // Hook to capture current route path metadata [cite: 4, 320]

  if (!isLogged) {
    // 
    // THE MAGIC: Storing the un-reached path path in state metadata! [cite: 320, 365]
    //
    return <Navigate to="/login" state={{ from: location }} replace />; // [cite: 320, 356, 365]
  }

  return <Outlet />; // [cite: 200, 365]
}

function Welcome() {
  return (
    <div>
      <h4>Public Entrance Lounge 🚪</h4>
      <Link to="/protected-dashboard" style={{ color: 'lightblue' }}>Go direct to Protected Panel [cite: 173]</Link>
    </div>
  );
}

function Login() {
  const { login } = useAuth(); // [cite: 233]
  const navigate = useNavigate(); // [cite: 4, 320, 356]
  const location = useLocation(); // [cite: 4, 320]

  // Retrieve previous target route path from state metadata, default to dashboard [cite: 320, 365]
  const targetRedirectPath = location.state?.from?.pathname || "/protected-dashboard"; // [cite: 320, 365]

  const handleFormLogin = () => {
    login(); // Authenticate user session [cite: 455]
    console.warn(`Success: Redirecting user back to memory route: ${targetRedirectPath}`);
    navigate(targetRedirectPath, { replace: true }); // Dynamic redirection [cite: 320, 356, 365]
  };

  return (
    <div style={{ padding: '20px', background: '#1e293b', borderRadius: '8px' }}>
      <h3>🔒 Login Screen</h3>
      <p style={{ fontSize: '11px', color: '#ef4444' }}>
        Redirection memory registered path: <code>{targetRedirectPath}</code>
      </p>
      <button onClick={handleFormLogin} style={{ padding: '10px 20px', background: '#0284c7', color: '#fff', border: 'none', cursor: 'pointer' }}>
        Authenticate & Resume 🚀
      </button>
    </div>
  );
}

function ProtectedDashboard() {
  const { logout } = useAuth(); // [cite: 233]

  return (
    <div style={{ padding: '20px', background: '#064e3b', border: '1px solid #10b981', borderRadius: '8px' }}>
      <h3>🟢 Confidential Admin Dashboard</h3>
      <p>System operational registers confirmed stable [cite: 179].</p>
      <button onClick={logout} style={{ padding: '8px', background: 'red', color: '#fff', border: 'none', cursor: 'pointer' }}>
        Log Out Session 🔑 [cite: 456]
      </button>
    </div>
  );
}

export default function App() {
  const router = createBrowserRouter([
    {
      path: "/",
      element: (
        <AuthProvider> {/* [cite: 168] */}
          <div style={{ padding: '24px', fontFamily: 'monospace', color: '#fff', background: '#0b0f19', minHeight: '100vh' }}>
            <h2>System Location Redirection memory</h2>
            <hr style={{ borderColor: '#333', marginBottom: '20px' }} />
            <Outlet /> {/* [cite: 200] */}
          </div>
        </AuthProvider>
      ),
      children: [
        { path: "", element: <Welcome /> }, // [cite: 180, 181]
        { path: "login", element: <Login /> }, // [cite: 180, 181]
        {
          element: <GuardWithMemory />, // Secure memory guard [cite: 365, 417]
          children: [
            { path: "protected-dashboard", element: <ProtectedDashboard /> } // Protected child path [cite: 180]
          ]
        }
      ]
    }
  ]);

  return <RouterProvider router={router} />; // [cite: 177]
}
```

##### File Name: `src/main.jsx` [cite: 396]
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <App />
);
```

#### Dry Run
1. Unauthenticated user goes direct to `/protected-dashboard`.
2. `GuardWithMemory` runs: `isLogged` is false [cite: 365, 417].
3. Storing current path `/protected-dashboard` in `Navigate` `state` metadata [cite: 320, 356, 365].
4. User redirected to `/login` [cite: 365].
5. User clicks login button. Authenticated session clears, reads `/protected-dashboard` from location state, and `navigate()` redirects back to `/protected-dashboard` [cite: 320, 365]!

---

### Example 4: Production level - Role-Based Access Guard inside complete MERN Roster App [cite: 97, 168, 200, 205, 261, 356, 365, 383, 417, 549]

Role restrictions, dynamic Auth context reading, automatic redirections and layout isolations [cite: 365, 383, 549].

#### Folder Structure
```text
guarded-production-mern/
├── package.json
└── src/
    ├── main.jsx                  # Root Bootstrapping [cite: 35, 552]
    ├── App.jsx                   # Setting up Router structures [cite: 43]
    ├── context/
    │   └── AuthContext.jsx        # Token and User roles Context [cite: 168, 213]
    ├── guards/
    │   └── RBACGuard.jsx         # Highly secure Role-Based access guard [cite: 365, 549]
    └── pages/
        ├── Login.jsx             # Secure Authentication Gate [cite: 362, 451]
        ├── MemberDashboard.jsx   # Logged-in verified user dashboard [cite: 404]
        ├── AdminPanel.jsx        # Admin restricted configurations terminal [cite: 404, 549]
        └── Unauthorized.jsx      # Fallback unauthorized alerts panel [cite: 383, 551]
```

#### Complete Code

##### File Name: `src/context/AuthContext.jsx` [cite: 1, 168, 261]
```javascript
import React, { createContext, useContext, useState, useEffect } from 'react'; // [cite: 1, 168]

const AuthContext = createContext(null); // [cite: 161, 168]

export function AuthProvider({ children }) { // [cite: 168]
  const [user, setUser] = useState(null); // { username, role, token } [cite: 1, 255]
  const [initChecking, setInitChecking] = useState(true);

  useEffect(() => {
    // 1. Auto login check on mount [cite: 188, 261]
    const activeToken = localStorage.getItem('access_token'); // [cite: 261]
    const savedRole = localStorage.getItem('user_role'); // [cite: 261]
    const savedName = localStorage.getItem('user_name'); // [cite: 261]

    if (activeToken && savedRole) {
      // Re-hydrate session state natively [cite: 456]
      setUser({
        username: savedName,
        role: savedRole,
        token: activeToken
      });
    }
    setInitChecking(false);
  }, []);

  const loginSystem = (token, role, username) => {
    localStorage.setItem('access_token', token); // [cite: 261]
    localStorage.setItem('user_role', role); // [cite: 261]
    localStorage.setItem('user_name', username); // [cite: 261]
    setUser({ username, role, token }); // Populate Context [cite: 455]
  };

  const logoutSystem = () => {
    localStorage.removeItem('access_token'); // [cite: 261]
    localStorage.removeItem('user_role'); // [cite: 261]
    localStorage.removeItem('user_name'); // [cite: 261]
    setUser(null); // Evict global Context [cite: 456]
  };

  return (
    <AuthContext.Provider value={{ user, loginSystem, logoutSystem, initChecking }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useMernAuth() {
  return useContext(AuthContext); // [cite: 132, 233]
}
```

##### File Name: `src/guards/RBACGuard.jsx` [cite: 4, 200, 320, 356, 365, 383, 417, 549]
```javascript
import React from 'react';
import { Navigate, Outlet, useLocation } from 'react-router-dom'; // [cite: 4, 200, 320]
import { useMernAuth } from '../context/AuthContext'; // [cite: 168]

// Highly configurable role restriction guard! [cite: 365, 383, 549]
export default function RBACGuard({ allowedRoles }) { // [cite: 399]
  const { user, initChecking } = useMernAuth(); // [cite: 233]
  const location = useLocation(); // [cite: 4, 320]

  if (initChecking) {
    return <div style={{ color: 'yellow', padding: '24px' }}>⏳ Authenticating registers...</div>;
  }

  // 1. Auth check: If not logged-in, redirect to login page! [cite: 365, 417]
  if (!user) {
    return <Navigate to="/login" state={{ from: location }} replace />; // [cite: 320, 356, 365]
  }

  // 2. Role check: If role not matched, redirect to unauthorized warning! [cite: 383, 549, 551]
  if (allowedRoles && !allowedRoles.includes(user.role)) { // [cite: 383, 549, 551]
    console.warn(`RBAC Unauthorized: User has role [${user.role}], needs [${allowedRoles}]`);
    return <Navigate to="/unauthorized" replace />; // [cite: 356, 383, 551]
  }

  return <Outlet />; // If all checks pass, render protected child pages [cite: 200, 365]
}
```

##### File Name: `src/pages/Login.jsx` [cite: 1, 4, 320, 356, 362, 451, 455]
```javascript
import React, { useState } from 'react'; // [cite: 1]
import { useNavigate, useLocation } from 'react-router-dom'; // [cite: 4, 320]
import { useMernAuth } from '../context/AuthContext'; // [cite: 168]

export default function Login() {
  const { loginSystem } = useMernAuth(); // [cite: 233]
  const navigate = useNavigate(); // [cite: 4, 320, 356]
  const location = useLocation(); // [cite: 4, 320]
  
  const [operatorId, setOperatorId] = useState(''); // [cite: 1]
  const [operatorRole, setOperatorRole] = useState('user'); // Default: user [cite: 1]

  const fromRedirectPath = location.state?.from?.pathname || "/dashboard"; // [cite: 320, 365]

  const handleLoginSubmit = (e) => {
    e.preventDefault();
    if (operatorId.trim() === '') return;

    // Simulate token response from backend Express API [cite: 451, 455]
    const signedJWT = "MOCK_JWT_PAYLOAD_HEADER.MOCK_JWT_PAYLOAD_DATA.MOCK_JWT_SIGNATURE";
    loginSystem(signedJWT, operatorRole, operatorId); // Update Context [cite: 168, 455]
    
    navigate(fromRedirectPath, { replace: true }); // Redirect to memory target path [cite: 320, 356, 365]
  };

  return (
    <form onSubmit={handleLoginSubmit} style={{ maxWidth: '400px', background: '#111827', padding: '24px', borderRadius: '12px', border: '1px solid #1f2937' }}>
      <h3>🔑 Security Ingestion Portal</h3>
      
      <div style={{ marginBottom: '15px' }}>
        <label style={{ display: 'block', fontSize: '11px', color: '#9ca3af', marginBottom: '5px' }}>Operator ID Code:</label>
        <input 
          value={operatorId} 
          onChange={(e) => setOperatorId(e.target.value)} 
          placeholder="Operator Name..." 
          style={{ width: '92%', padding: '10px', background: '#1f2937', color: '#fff', border: '1px solid #333', borderRadius: '6px' }}
        />
      </div>

      <div style={{ marginBottom: '20px' }}>
        <label style={{ display: 'block', fontSize: '11px', color: '#9ca3af', marginBottom: '5px' }}>Access Role Level:</label>
        <select 
          value={operatorRole} 
          onChange={(e) => setOperatorRole(e.target.value)} 
          style={{ width: '100%', padding: '10px', background: '#1f2937', color: '#fff', border: '1px solid #333', borderRadius: '6px' }}
        >
          <option value="user">Standard User (Member)</option>
          <option value="admin">System Administrator (Super-User)</option>
        </select>
      </div>

      <button type="submit" style={{ width: '100%', padding: '12px', background: '#0284c7', color: '#fff', fontWeight: 'bold', border: 'none', borderRadius: '6px', cursor: 'pointer' }}>
        Perform Safe Login
      </button>
    </form>
  );
}
```

##### File Name: `src/pages/MemberDashboard.jsx` [cite: 173, 404, 456]
```javascript
import React from 'react';
import { Link } from 'react-router-dom'; // [cite: 173]
import { useMernAuth } from '../context/AuthContext'; // [cite: 168]

export default function MemberDashboard() {
  const { user, logoutSystem } = useMernAuth(); // [cite: 233]

  return (
    <div style={{ padding: '24px', background: '#1e293b', borderRadius: '8px' }}>
      <h3>👤 Member Terminal Operational Workspace</h3>
      <p>Operator: <strong>{user?.username}</strong> [cite: 197]. Verified Authorization Level: <code>{user?.role}</code></p>
      
      <div style={{ display: 'flex', gap: '15px', marginTop: '20px' }}>
        <Link to="/admin" style={{ color: 'lightblue', textDecoration: 'underline' }}>Attempt Administrative Portal Access ⚙️ [cite: 173]</Link>
        <button onClick={logoutSystem} style={{ padding: '8px 16px', background: '#dc2626', color: '#fff', border: 'none', borderRadius: '4px', cursor: 'pointer' }}>
          Terminate Session [cite: 456]
        </button>
      </div>
    </div>
  );
}
```

##### File Name: `src/pages/AdminPanel.jsx` [cite: 173, 404, 456, 549]
```javascript
import React from 'react';
import { Link } from 'react-router-dom'; // [cite: 173]
import { useMernAuth } from '../context/AuthContext'; // [cite: 168]

export default function AdminPanel() {
  const { user, logoutSystem } = useMernAuth(); // [cite: 233]

  return (
    <div style={{ padding: '24px', background: '#450a0a', border: '2px solid red', borderRadius: '8px' }}>
      <h3>⚙️ Restricted System Administrative Console Panel</h3>
      <p>Access Cleared: <strong>{user?.username}</strong> [cite: 197]. System variables status: Highly Stable [cite: 179].</p>
      <div style={{ background: '#000', padding: '15px', borderRadius: '6px', fontFamily: 'monospace' }}>
        <p style={{ color: 'red', margin: 0 }}>🚨 [Mainframe Audit Logs] Server operations: Fully unlocked.</p>
      </div>
      <div style={{ display: 'flex', gap: '15px', marginTop: '20px' }}>
        <Link to="/dashboard" style={{ color: 'lightblue', textDecoration: 'underline' }}>Return to Normal User Area [cite: 173]</Link>
        <button onClick={logoutSystem} style={{ padding: '8px 16px', background: '#dc2626', color: '#fff', border: 'none', borderRadius: '4px', cursor: 'pointer' }}>
          Revoke Session Clearance [cite: 456]
        </button>
      </div>
    </div>
  );
}
```

##### File Name: `src/pages/Unauthorized.jsx` [cite: 173, 383, 551]
```javascript
import React from 'react';
import { Link } from 'react-router-dom'; // [cite: 173]

export default function Unauthorized() {
  return (
    <div style={{ padding: '24px', background: '#1e1b4b', border: '2px dashed #4338ca', borderRadius: '8px', color: '#e0e7ff' }}>
      <h3>🛑 ACCESS BLOCKED: Status 403 Forbidden [cite: 229]</h3>
      <p>Error reason: Your current operator keys do not match Admin specifications [cite: 383, 549, 551].</p>
      <Link to="/dashboard" style={{ color: '#818cf8', textDecoration: 'underline' }}>Return to Safe Workspace Area [cite: 173]</Link>
    </div>
  );
}
```

##### File Name: `src/App.jsx` [cite: 177, 180, 181, 186, 356, 365, 383, 549]
```javascript
import React from 'react';
import { createBrowserRouter, RouterProvider, Navigate, Outlet } from 'react-router-dom'; // [cite: 177, 200]
import { AuthProvider, useMernAuth } from './context/AuthContext'; // [cite: 168]
import RBACGuard from './guards/RBACGuard'; // Guard [cite: 365, 383, 549]
import Login from './pages/Login';
import MemberDashboard from './pages/MemberDashboard';
import AdminPanel from './pages/AdminPanel';
import Unauthorized from './pages/Unauthorized';

function AppLayout() {
  return (
    <div style={{ padding: '24px', fontFamily: 'monospace', color: '#fff', background: '#0b0f19', minHeight: '100vh' }}>
      <h2>Larsen Enterprise Booking Dashboard</h2>
      <hr style={{ borderColor: '#333', marginBottom: '25px' }} />
      <Outlet /> {/* [cite: 200] */}
    </div>
  );
}

export default function App() {
  const routes = createBrowserRouter([
    {
      path: "/",
      element: (
        <AuthProvider> {/* Global auth store initialized [cite: 168] */}
          <AppLayout />
        </AuthProvider>
      ),
      children: [
        { path: "", element: <Navigate to="/dashboard" replace /> }, // [cite: 356]
        { path: "login", element: <Login /> }, // [cite: 180, 181]
        { path: "unauthorized", element: <Unauthorized /> }, // [cite: 180, 181]

        // 1. Secured Route Guard Block for ALL logged-in operators [cite: 180, 365, 383, 549]
        {
          element: <RBACGuard allowedRoles={["user", "admin"]} />, // Allows members and admins [cite: 383, 549, 551]
          children: [
            { path: "dashboard", element: <MemberDashboard /> } // Protected Dashboard [cite: 180]
          ]
        },

        // 2. Restricted Route Guard Block ONLY for System Administrators! [cite: 180, 365, 383, 549]
        {
          element: <RBACGuard allowedRoles={["admin"]} />, // Only admins allowed [cite: 383, 549, 551]
          children: [
            { path: "admin", element: <AdminPanel /> } // Admin panel [cite: 180]
          ]
        }
      ]
    }
  ]);

  return <RouterProvider router={routes} />; // [cite: 177]
}
```

##### File Name: `src/main.jsx` [cite: 396]
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <App />
);
```

---

## SECTION 6: THE COMMAND CONSOLE 📟

Bhai, is entire architecture setup ko configure karne me use hone wali terminal commands niche di gayi hain: [cite: 211]

| Command | Ye kya karti hai? | Kab use karte hain? |
| :--- | :--- | :--- |
| `npm install react-router-dom` | Node packages directory registry se stable React Router packages fetch karke configurations me install karti hai [cite: 43]. | Project setup shuru karte hi client-side routing configure karne ke liye use karte hain [cite: 171]. |
| `npm run dev` [cite: 411] | Vite local compiler development servers run karti hai (e.g. localhost:5173). | UI component views aur redirects test karne ke liye compile check run karti hai [cite: 411, 412]. |

---

## SECTION 7: CORE ASSESSMENT INTERVIEW SUITE 📝

---

#### Q1: What is the benefit of the Outlet component in React Router v6? How does it simplify Protected Route wrapper designs? [cite: 200, 365]
*   **Professional English Answer:** In React Router v6, the `<Outlet />` component serves as a dynamic placeholder that renders the child route elements matched by the parent route [cite: 200, 365]. In Protected Route designs, this enables declarative and centralized layouts [cite: 365]. Instead of wrapping every individual page component manually, you wrap a parent route element with a single `<ProtectedRoute />` guard [cite: 180, 365]. If the authentication check evaluates to true, the guard simply renders the `<Outlet />` [cite: 365]. This allows the child routes to be mounted contextually without manual component drilling [cite: 200, 365].
*   **Easy Hinglish Explanation:** `<Outlet />` ek placeholder block ki tarah kaam karta hai jo parent route ke andar match hone wale saare nested child routes ko screen par automatic render kar deta hai [cite: 200, 365]. Isse route guarding bohot clean ho jati hai [cite: 365]. Har ek file me login checks lagane ke badle, hum ek parent route me `<ProtectedRoute />` element lagate hain [cite: 180, 365]. Agar login token verified hai, to Outlet child components ko render kar deta hai, jisse code repetition 100% bach jata hai [cite: 200, 365].

---

#### Q2: What is the practical difference between a 401 and a 403 status code in a MERN application? How do you orchestrate their handling on the frontend? [cite: 97, 229, 383, 455]
*   **Professional English Answer:** A `401 Unauthorized` status indicates that the request lacks valid authentication credentials (the user identity is unverified or the JWT token has expired) [cite: 416, 455, 472]. The client must intercept this, clear local token storage, and redirect the user to the login route [cite: 261, 365, 456]. On the other hand, a `403 Forbidden` status indicates that the user's identity is verified, but their authorization tier (role level) lacks permission to access the requested resource [cite: 383, 549, 551]. The frontend handles this by preventing the redirect to login, and instead displaying a custom "Access Blocked / Unauthorized" landing screen to preserve user context [cite: 383, 551].
*   **Easy Hinglish Explanation:** `401 Unauthorized` ka matlab hai ki server user ko pehchanta hi nahi hai (jaise expired token) [cite: 416, 455, 472]. Jab 401 error mile, to frontend ko active tokens delete karke user ko seedhe `/login` page par redirect kar dena chahiye [cite: 261, 365, 456]. Lekin `403 Forbidden` ka matlab hai ki user login to hai par use us data ki permission nahi hai (jaise customer page se admin database access karne ki koshish karna) [cite: 383, 549]. Is case me user ko login page par nahi bheja jata, balki ek `/unauthorized` warning message screen par dikhaya jata hai [cite: 383, 551].

---

## SECTION 8: THE CHAPTER 3 SUMMARY SHIELDS 🏁

---

### Cheat Sheet
```javascript
// Declarative v6 Role Protection Guard blueprint
import { Navigate, Outlet } from 'react-router-dom'; // [cite: 200, 356]

export const Guard = ({ userRole, allowed }) => {
  if (!userRole) return <Navigate to="/login" replace />; // [cite: 356, 365]
  return allowed.includes(userRole) ? <Outlet /> : <Navigate to="/403" replace />; // [cite: 200, 356, 383]
};
```

---

### Mini Assignment
1. Custom Context Provider set karke automatic token verify checks on mount logic configure karo [cite: 168, 188].
2. React Router routes ko wrapper Guard component (`GuardedConsolePanel`) ke andr wrap kijiye [cite: 314, 365].
3. Invalid tokens hits checks me automatic logout redirect actions triggers kijiye [cite: 365, 456].

---

### Practice Task
Ek full Role-Based Access control matrix design kijiye jo dynamic route paths toggles aur un-authorized access block mechanisms cleanly perform kare [cite: 365, 383, 549].

