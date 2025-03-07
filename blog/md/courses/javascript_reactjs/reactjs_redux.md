React Router is a standard library for routing in React applications. It enables navigation between views of different components, allows changing the browser URL, and keeps the UI in sync with the URL.

In this tutorial, I'll guide you through setting up and using React Router in your React application.

---

### **1. Install React Router**
First, install the `react-router-dom` package:
```bash
npm install react-router-dom
```

---

### **2. Basic Setup**
Let’s create a simple app with multiple pages using React Router.

#### **Folder Structure**
```
src/
├── components/
│   ├── Home.js
│   ├── About.js
│   └── Contact.js
├── App.js
├── index.js
└── ...
```

---

### **3. Create Components**
Create three components: `Home`, `About`, and `Contact`.

#### **Home.js**
```jsx
import React from 'react';

function Home() {
  return <h1>Home Page</h1>;
}

export default Home;
```

#### **About.js**
```jsx
import React from 'react';

function About() {
  return <h1>About Page</h1>;
}

export default About;
```

#### **Contact.js**
```jsx
import React from 'react';

function Contact() {
  return <h1>Contact Page</h1>;
}

export default Contact;
```

---

### **4. Set Up Routing in `App.js`**
Import and use `BrowserRouter`, `Routes`, and `Route` from `react-router-dom` to define routes.

#### **App.js**
```jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';
import Home from './components/Home';
import About from './components/About';
import Contact from './components/Contact';

function App() {
  return (
    <Router>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
          <li>
            <Link to="/contact">Contact</Link>
          </li>
        </ul>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </Router>
  );
}

export default App;
```

---

### **5. Explanation**
- **`BrowserRouter`**: Wraps your entire application and enables routing.
- **`Routes`**: Defines the container for all routes.
- **`Route`**: Maps a URL path to a component.
- **`Link`**: Used for navigation between routes (similar to `<a>` tags in HTML).

---

### **6. Nested Routes**
You can create nested routes for more complex applications.

#### **Example: Nested Routes**
Update `App.js` to include a nested route:
```jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Link, Outlet } from 'react-router-dom';
import Home from './components/Home';
import About from './components/About';
import Contact from './components/Contact';

function App() {
  return (
    <Router>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
          <li>
            <Link to="/contact">Contact</Link>
          </li>
        </ul>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="/dashboard" element={<Dashboard />}>
          <Route path="profile" element={<Profile />} />
          <Route path="settings" element={<Settings />} />
        </Route>
      </Routes>
    </Router>
  );
}

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <nav>
        <Link to="profile">Profile</Link>
        <Link to="settings">Settings</Link>
      </nav>
      <Outlet />
    </div>
  );
}

function Profile() {
  return <h2>Profile Page</h2>;
}

function Settings() {
  return <h2>Settings Page</h2>;
}

export default App;
```

- **`Outlet`**: Renders the child routes within a parent route.

---

### **7. Dynamic Routes**
You can create dynamic routes using URL parameters.

#### **Example: Dynamic Route**
Update `App.js` to include a dynamic route:
```jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Link, useParams } from 'react-router-dom';

function App() {
  return (
    <Router>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/users/1">User 1</Link>
          </li>
          <li>
            <Link to="/users/2">User 2</Link>
          </li>
        </ul>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/users/:id" element={<User />} />
      </Routes>
    </Router>
  );
}

function Home() {
  return <h1>Home Page</h1>;
}

function User() {
  const { id } = useParams();
  return <h1>User ID: {id}</h1>;
}

export default App;
```

- **`useParams`**: Hook to access URL parameters.

---

### **8. Programmatic Navigation**
You can navigate programmatically using the `useNavigate` hook.

#### **Example: Programmatic Navigation**
Update `App.js` to include a button for navigation:
```jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Link, useNavigate } from 'react-router-dom';

function App() {
  return (
    <Router>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
        </ul>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Router>
  );
}

function Home() {
  const navigate = useNavigate();

  return (
    <div>
      <h1>Home Page</h1>
      <button onClick={() => navigate('/about')}>Go to About</button>
    </div>
  );
}

function About() {
  return <h1>About Page</h1>;
}

export default App;
```

---

### **9. Deploying with React Router**
When deploying your app, ensure your server is configured to handle client-side routing. For example, in a static server, you may need to redirect all requests to `index.html`.

---

### **10. Next Steps**
- Explore **protected routes** for authentication.
- Learn about **lazy loading** for better performance.
- Use **React Router v6** features like `useRoutes` and `useSearchParams`.

Happy routing! 🚀