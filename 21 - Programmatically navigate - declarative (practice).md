### (Practice) Programmatically Navigate - Declarative

App.js
```js
/*
  1. You're given three components, App, Results and Form. Inside the 
  App component, creates two Routes. One that will render the Form component
  when the user is at '/' and the other which will render the Results component
  when the user is at '/results.

  2. Refactor the Form component so that when the user submits the form, 
    you redirect them (declaratively using <Navigate />) to the /results page.
*/

import * as React from "react";
import { BrowserRouter as Router } from "react-router-dom";

const submit = () => {
  // fake AF
  return new Promise((res) => {
    setTimeout(() => res(), 500);
  });
};

function Results() {
  return <h1>Mmmm. Thanks for submitting your favorite food.</h1>;
}

function Form() {
  const [name, setName] = React.useState("");
  const [food, setFood] = React.useState("");

  const handleChange = (e) => {
    const { name, value } = e.target;

    name === "name" ? setName(value) : setFood(value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    submit(name, food);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Your name
        <input type="text" value={name} onChange={handleChange} name="name" />
      </label>
      <label>
        Favorite Food
        <input type="text" value={food} onChange={handleChange} name="food" />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default function App() {
  return (
    <Router>
      <div>See the instructions</div>
    </Router>
  );
}
```


index.js
```js
import React from "react";
import ReactDOM from "react-dom/client";
import "./theme.css";
import "./styles.css";
import App from "./App";

const rootElement = document.getElementById("root");
const root = ReactDOM.createRoot(rootElement);

root.render(
  <React.StrictMode>
    <div className="yellow-border" />
    <div className="container">
      <App />
    </div>
  </React.StrictMode>
);
```


styles.css
```css
form {
  width: 300px;
  margin: 50px auto;
}

label {
  color: var(--white);
  font-weight: 900;
  font-size: 30px;
  display: block;
  margin: 20px 0;
}

input {
  width: 100%;
  font-size: 18px;
  padding: 10px;
}

button {
  color: var(--white);
  background: var(--pink);
  text-align: center;
  font-size: 20px;
  font-weight: 900;
  padding: 1.5vw;
  text-transform: uppercase;
  display: table;
  margin: 20px 0;
  transition: all 300ms;
  border: none;
  width: 100%;
}

button:hover {
  transform: rotate(-1deg);
}

h1 {
  font-size: 50px;
  color: var(--white);
  font-weight: 900;
  text-align: center;
}
```
