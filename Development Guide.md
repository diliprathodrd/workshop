# Development Guide

---

> **Environment Pre-requisites**

- Node.js [Download](https://nodejs.org/en)
- google chrome browser
- Mongodb cloud account
- vscode editor 
  - vscode extensions
    - Auto Rename Tag
    - Code Spell Checker
    - ES7+ React/Redux/React-Native snippets
    - JavaScript (ES6) code snippets
    - Live Server
    - Markdown Preview Enhanced
    - Postman
    - React snippets

---

> **Local Setup**

- create a folder with name `workout`
- create a folder with name `backend` inside `workout`

---

> **Backend Development**

- lets create `server.js` in `backend` folder (entry file for backend)

- lets create `package.json` file for our project in `backend` folder
- for that open integrated terminal in vscode
- hit below command to create `package.json`
  `npm init -y`
- now lets install express by following command
  `npm install express`
- lets import this package into `server.js` and start our express app

  ```javascript
  const express = require("express");

  // express app
  const app = express();
  ```
- to parse the request data into json lets add below code in `server.js`
  ```javascript
  app.use(express.json());
  ```
- lets create a middleware for our app
  ```javascript
  app.use((req, res, next) => {
    console.log(`Path : ${req.path}, Method : ${req.method}`);
    next();
  });
  ```
- lets add code for request listener
  ```javascript
  // listen for requests
  app.listen(4000, () => {
    console.log("listening on port 4000");
  });
  ```
- lets run the server and check if it is listening the requests
  `node server.js`
- lets install nodemon package globally to check live changes
  `npm install -g nodemon`
- now lets run the server using `nodemon`
  `nodemon server.js`
  
<br>
  
  > **Warning:** If you are facing access issue on powershell terminal, use git bash.

<br>

- lets add script for `nodemon` in our `package.json` file
- add below code in `"scripts"` of `package.json`
  ```javascript
  "dev": "nodemon server.js"
  ```
- now we can directly run command `npm run dev` instead of `nodemon server.js`

- now lets add routes for our requests
  ```javascript
  //routes
  app.get("/", (req, res) => {
    res.json({ mssg: "Welcome to the App" });
  });
  ```
- lets open localhost at port 4000 to see if this is working
  [http://localhost:4000](http://localhost:4000)
- now lets create a secret variable file also known as environment variable file to store sensitive data variables
- create a file with name `.env` in `backend` folder
- add below variable in it
  ```javascript
  PORT = 4000
  ```
- lets install `dotenv` package to use these environment variables across app
  `npm install dotenv`
- lets import `dotenv` package in `server.js` and invoke `config` method
- also update static port number with environment variable

  ```javascript
  require("dotenv").config();

  // listen for requests
  app.listen(process.env.PORT, () => {
    console.log("listening on port:", process.env.PORT);
  });
  ```

- check if it is working as expected by running server `npm run dev`

- lets check our request in `postman`

  - open `postman` extension
  - select `GET` request
  - add address localhost:4000/
  - click on send

- to save api addresses for later use, click on `save > create collection > workouts`

- below are the endpoints or apis we are going to implement in our app

| Method |     Route     |            Working             |
| :----: | :-----------: | :----------------------------: |
|  GET   |   /workouts   | Gets all the workout documents |
|  POST  |   /workouts   | Creates a new workout document |
|  GET   | /workouts/:id | Gets a single workout document |
| DELETE | /workouts/:id |    Deletes a single workout    |
| PATCH  | /workouts/:id |    Updates a single workout    |

- we can create routes for all apis in `server.js` but to keep it clean we will create a new folder in `backend` with name `routes` and a file inside it with name `workouts.js`

- inside this file first lets import `express` and create an instance of `router` by invoking `Router()` function

  ```javascript
  const express = require("express");

  const router = express.Router();
  ```

- at the end after creating all the required routes, we will exports the router

  ```javascript
  module.exports = router;
  ```

- lets import these routes in `server.js`
  ```javascript
  const workoutRoutes = require("./routes/workouts");
  ```
- now we can use these routes on our app
- remove app.get() code unders //routes and add below code
  ```javascript
  app.use("/api/workouts", workoutRouts);
  ```
- what this code do is, it will navigate us to a function in `workouts.js` based on path and method
- lets come to `workouts.js` and create the endpoints for each apis

  ```javascript
  // GET all workouts
  router.get("/", (req, res) => {
    res.json({ mssg: "GET all workouts" });
  });

  // GET a single workout
  router.get("/:id", (req, res) => {
    res.json({ mssg: "GET a single workout" });
  });

  // POST a new workout
  router.post("/", (req, res) => {
    res.json({ mssg: "POST a new workout" });
  });

  // DELETE a workout
  router.delete("/:id", (req, res) => {
    res.json({ mssg: "DELETE a workout" });
  });

  // UPDATE a workout
  router.patch("/", (req, res) => {
    res.json({ mssg: "UPDATE a workout" });
  });
  ```

- now lets try all api endpoints in `postman` if they are working or not
- URLs for each api

  - Get all workouts - localhost:4000/api/workouts/
  - Get single workout - localhost:4000/api/workouts/123
  - Post a new workout - localhost:4000/api/workouts/
  - Delete a workout - localhost:4000/api/workouts/123
  - Update a workout - localhost:4000/api/workouts/123

- save all these urls in our `workout` folder

<br>

- lets do the database setup now
- we need to add `MONGO_URI` variable into into our `.env` file
  (Get `MONGO_URI` from mongodb cloud)
  - Go to mongodb cloud and sign in
  - click on `project > new project`
  - add project name i.e. `workout`
  - click `next > create project`
  - again click on `create`
  - select `M0 FREE` cluster and click on `create`
  - set username and password (copy-paste password in `.env`, we need to use it in url) and click on `create user`
  - scroll down and click on `finish and close > go to overview`
  - click on `connect > drivers`
  - copy the path and paste it in front of `MONGO_URI` and replace `<password>` with your actual password.
  - remove params after `retryWrites=true` from url
  - go to `Network Access` on mongodb cloud and click on `ADD IP ADDRESS > ALLOW ACCESS FROM ANYWHERE > Confirm`

<br>

- now lets install mongoose package
  `npm install mongoose`
- lets import `mongoose` in `server.js`
  ```javascript
  const mongoose = require("mongoose");
  ```
- lets connect to db now and copy-paste listen for requests part into then section after db connection is successful
  ```javascript
  // connect to db
  mongoose
    .connect(process.env.MONGO_URI)
    .then(() => {
      // listen for requests
      app.listen(process.env.PORT, () => {
        console.log("connected to db & listening on port", process.env.PORT);
      });
    })
    .catch((error) => {
      console.log("error in connecting db", error);
    });
  ```
- check if everything is working fine by restarting server

- now lets create models for our projects
- create a folder inside `backend` folder with name `models` and a file inside it with name `workoutModel.js`
- add below code in it to create workout schema

  ```javascript
  const mongoose = require("mongoose");

  const Schema = mongoose.Schema;

  const workoutSchema = new Schema(
    {
      title: {
        type: String,
        required: true,
      },
      reps: {
        type: Number,
        required: true,
      },
      load: {
        type: Number,
        required: true,
      },
    },
    { timestamps: true }
  );

  module.exports = mongoose.model("Workout", workoutSchema);
  ```

- lets import this schema in our `routes > workouts.js`
  ```javascript
  const Workout = require("../models/workoutModel");
  ```
- now update the // POST a new workout section with below code

  ```javascript
  router.post("/", async (req, res) => {
    const { title, load, reps } = req.body;

    try {
      const workout = await Workout.create({ title, load, reps });
      res.status(200).json(workout);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  });
  ```

- now try to hit post api from `postman` to validate the changes (add req body as per requirement)
- also check with missing property in request body if it throws error
- check if this data got created on mongodb cloud by clicking on `Browse Collections`

<br>

- `routes > workouts.js` is request handler and we don't want to mix-up our database logic in request handler, so we will create a controller to write this logic separately and we can call those functions in our request handler
- lets create a folder in `backend` folder with name `controllers` and create a file inside it with name `workoutController.js`
- lets first cut workout model imported in `routes > workout.js` and paste it in `workoutController.js`
- now lets create functions for each request in `workoutController.js` and export it so that we can use those functions in `routes > workout.js`

- add below code to `workoutController.js`

  ```javascript
  const Workout = require("../models/workoutModel");
  const mongoose = require("mongoose");

  // get all workouts
  const getWorkouts = async (req, res) => {
    const workouts = await Workout.find({}).sort({ createdAt: -1 });

    res.status(200).json(workouts);
  };

  // get a single workout
  const getWorkout = async (req, res) => {
    const { id } = req.params;

    if (!mongoose.Types.ObjectId.isValid(id)) {
      return res.status(404).json({ error: "No such workout" });
    }

    const workout = await Workout.findById(id);

    if (!workout) {
      return res.status(404).json({ error: "No such workout" });
    }

    res.status(200).json(workout);
  };

  // create new workout
  const createWorkout = async (req, res) => {
    const { title, load, reps } = req.body;
    // add doc to db
    try {
      const workout = await Workout.create({ title, load, reps });
      res.status(200).json(workout);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  };

  // delete a workout
  const deleteWorkout = async (req, res) => {
    const { id } = req.params;

    if (!mongoose.Types.ObjectId.isValid(id)) {
      return res.status(404).json({ error: "No such workout" });
    }

    const workout = await Workout.findOneAndDelete({ _id: id });

    if (!workout) {
      return res.status(404).json({ error: "No such workout" });
    }

    res.status(200).json(workout);
  };

  // update a workout
  const updateWorkout = async (req, res) => {
    const { id } = req.params;

    if (!mongoose.Types.ObjectId.isValid(id)) {
      return res.status(404).json({ error: "No such workout" });
    }

    const workout = await Workout.findOneAndUpdate(
      { _id: id },
      { ...req.body }
    );

    if (!workout) {
      return res.status(404).json({ error: "No such workout" });
    }

    res.status(200).json(workout);
  };

  module.exports = {
    getWorkouts,
    getWorkout,
    createWorkout,
    deleteWorkout,
    updateWorkout,
  };
  ```

- now import all these functions into `routes > workout.js` and add reference of all respective functions

  ```javascript
  const {
    getWorkouts,
    getWorkout,
    createWorkout,
    deleteWorkout,
    updateWorkout,
  } = require("../controllers/workoutController");

  // Get all workouts
  router.get("/", getWorkouts);

  // Get a single workout
  router.get("/:id", getWorkout);

  // Post a new workout
  router.post("/", createWorkout);

  // Delete a workout
  router.delete("/:id", deleteWorkout);

  // Update a workout
  router.patch("/:id", updateWorkout);
  ```

---

> **Frontend Development**

- lets go to our project folder `workout` using integrated terminal of vscode
- use below command to create frontend project
  `npx create-react-app frontend`
- after that go inside `frontend` folder
  `cd frontend`
- lets clean our project
  - delete `App.css, App.test.js, logo.svg, reportWebVitals.js, setupTests.js` from `src` folder
  - remove import of any of the above file if existing
  - in `App.js` we will keep only one div with className `App` and remove other code
- now lets install react router dom package using below command
  `npm install react-router-dom`
- lets import `BrowserRouter, Routes, Route` from `react-router-dom` into `App.js`
- lets create a home component first
- create a folder with name `pages` and inside it create a file with name `Home.jsx` or `Home.js`
- write below code inside it for now (rafce - shortcut)

  ```javascript
  const Home = () => {
    return (
      <div className="home">
        <h2>Home</h2>
      </div>
    );
  };

  export default Home;
  ```

- lets import this in `App.js` and add route for it

  ```javascript
  import { BrowserRouter, Routes, Route } from "react-router-dom";

  // pages & components
  import Home from "./pages/Home";

  function App() {
    return (
      <div className="App">
        <BrowserRouter>
          <div className="pages">
            <Routes>
              <Route path="/" element={<Home />} />
            </Routes>
          </div>
        </BrowserRouter>
      </div>
    );
  }

  export default App;
  ```

- lets check if this is working by running below command
  `npm start`
- lets create a folder with name `components` into `src` folder and create a file with name `Navbar.js` inside it
- add below code inside it

  ```javascript
  import { Link } from "react-router-dom";

  const Navbar = () => {
    return (
      <header>
        <div className="container">
          <Link to="/">
            <h1>Workout Buddy</h1>
          </Link>
        </div>
      </header>
    );
  };

  export default Navbar;
  ```

- import this component in root component i.e. `App.js` and add component under `BrowserRouter` component

  ```javascript
  import Navbar from "./components/Navbar";

  <BrowserRouter>
    <Navbar />
    <div className="pages">--- ---</div>
  </BrowserRouter>;
  ```

- lets add some basic styling, go to `src > index.css`, remove existing css and add below css code

  ```css
  /* google font */
  @import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;800&family=VT323&display=swap");

  /* layout */
  :root {
    --primary: #1aac83;
    --error: #e7195a;
  }
  body {
    background: #f1f1f1;
    margin: 0;
    font-family: "Poppins";
  }
  header {
    background: #fff;
  }
  header .container {
    max-width: 1400px;
    margin: 0 auto;
    padding: 10px 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  header a {
    color: #333;
    text-decoration: none;
  }
  .pages {
    max-width: 1400px;
    padding: 20px;
    margin: 0 auto;
  }
  ```

- check running the app if everything is working fine

<br>

- now lets fetch the data from database using api call
- add below code in `Home.js` component

  ```javascript
  import { useEffect, useState } from "react";

  const Home = () => {
    const [workouts, setWorkouts] = useState(null);

    useEffect(() => {
      const fetchWorkouts = async () => {
        const response = await fetch("http://localhost:4000/api/workouts");
        const json = await response.json();

        if (response.ok) {
          setWorkouts(json);
        }
      };

      fetchWorkouts();
    }, []);

    return (
      <div className="home">
        <div className="workouts">
          {workouts &&
            workouts.map((workout) => <p key={workout._id}>{workout.title}</p>)}
        </div>
      </div>
    );
  };

  export default Home;
  ```

- to check this lets run application and to make api calls we must start the server
- open new terminal, go to `backend` and run `npm run dev`
- if we try to run the app now, still we will get error `cross origin request error` because our frontend and backend are running on different ports
- to resolve this, we will go to `package.json` in `frontend` and add a `proxy` property in it
  ```javascript
  "proxy": "http://localhost:4000",
  "name": "frontend"
  ---
  ---
  ```
- now we can call our apis without complete url, we can remove `http://localhost:4000` from all api calls
  ```javascript
  const response = await fetch('/api/workouts');
  const json = await response.json();
  ---
  ---
  ```
- lets restart the frontend node server and see if it works
- lets create a new component with name `WorkoutDetails.js` into `components` folder to show workout details got from api
- import this into `Home` component
  ```javascript
  import WorkoutDetails from "../components/WorkoutDetails";
  ```
- replace `p` tag with `WorkoutDetails` component
  ```javascript
  <WorkoutDetails key={workout._id} workout={workout} />
  ```
- lets go to `WorkoutDetails` component and write below code

  ```javascript
  const WorkoutDetails = ({ workout }) => {
    return (
      <div className="workout-details">
        <h4>{workout.title}</h4>
        <p>
          <strong>Load (kg): </strong> {workout.load}
        </p>
        <p>
          <strong>Reps: </strong> {workout.reps}
        </p>
        <p>{workout.createdAt}</p>
      </div>
    );
  };

  export default WorkoutDetails;
  ```

- lets add little more css
  ```css
  /* homepage */
  .home {
    display: grid;
    grid-template-columns: 3fr 1fr;
    gap: 100px;
  }
  .workout-details {
    background: #fff;
    border-radius: 4px;
    margin: 20px auto;
    padding: 20px;
    position: relative;
    box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.05);
  }
  .workout-details h4 {
    margin: 0 0 10px 0;
    font-size: 1.2em;
    color: var(--primary);
  }
  .workout-details p {
    margin: 0;
    font-size: 0.9em;
    color: #555;
  }
  /* for delete button*/
  .workout-details span {
    position: absolute;
    top: 20px;
    right: 20px;
    cursor: pointer;
    background: #f1f1f1;
    padding: 6px;
    border-radius: 50%;
    color: #333;
  }
  ```
- lets go to browser and see the changes

<br>

- now lets create a form to add new workout
- for that lets create a new component inside `component` folder with the name `WorkoutForm.js`
- add below code in it

  ```javascript
  import { useState } from "react";

  const WorkoutForm = () => {
    const [title, setTitle] = useState("");
    const [load, setLoad] = useState("");
    const [reps, setReps] = useState("");
    const [error, setError] = useState(null);

    const handleSubmit = async (e) => {
      e.preventDefault();

      const workout = { title, load, reps };

      const response = await fetch("/api/workouts", {
        method: "POST",
        body: JSON.stringify(workout),
        headers: {
          "Content-Type": "application/json",
        },
      });

      const json = await response.json();

      if (!response.ok) {
        setError(json.error);
      }

      if (response.ok) {
        setTitle("");
        setLoad("");
        setReps("");
        setError(null);
        console.log("new workout added", json);
      }
    };

    return (
      <form className="create" onSubmit={handleSubmit}>
        <h3>Add a New Workout</h3>

        <label>Excersize Title:</label>
        <input
          type="text"
          onChange={(e) => setTitle(e.target.value)}
          value={title}
        />

        <label>Load (in kg):</label>
        <input
          type="number"
          onChange={(e) => setLoad(e.target.value)}
          value={load}
        />

        <label>Reps:</label>
        <input
          type="number"
          onChange={(e) => setReps(e.target.value)}
          value={reps}
        />

        <button>Add Workout</button>
        {error && <div className="error">{error}</div>}
      </form>
    );
  };

  export default WorkoutForm;
  ```

- now import this component into our `Home` component and add it after `workouts` div
  ```javascript
  import WorkoutForm from '../components/WorkoutForm';
  ---
  ---
  <div className="workouts">
      ---
      ---
  </div>
  <WorkoutForm/>
  ```
- lets add some more css

  ```css
  /* new workout form */
  label,
  input {
    display: block;
  }
  input {
    padding: 10px;
    margin-top: 10px;
    margin-bottom: 20px;
    width: 100%;
    border: 1px solid #ddd;
    border-radius: 4px;
    box-sizing: border-box;
  }
  form button {
    background: var(--primary);
    border: 0;
    color: #fff;
    padding: 10px;
    font-family: "Poppins";
    border-radius: 4px;
    cursor: pointer;
  }
  div.error {
    padding: 10px;
    background: #ffefef;
    border: 1px solid var(--error);
    color: var(--error);
    border-radius: 4px;
    margin: 20px 0;
  }
  ```

- lets run the app and try to add new workout, check for validations also

<br>

> ***Context Start***

- there is one problem here that we need to refresh the page every time when we add a new workout, we will fix it using `React Context`

- lets create new folder with name `context` into `src` folder and create a file with name `workoutContext.js` into it
- import `createContext` function from `react` and create a new context with name `WorkoutsContext`
- add below code into it

  ```javascript
  import { createContext, useReducer } from "react";

  export const WorkoutsContext = createContext();

  export const workoutsReducer = (state, action) => {
    switch (action.type) {
      case "SET_WORKOUTS":
        return {
          workouts: action.payload,
        };
      case "CREATE_WORKOUT":
        return {
          workouts: [action.payload, ...state.workouts],
        };
      default:
        return state;
    }
  };

  export const WorkoutsContextProvider = ({ children }) => {
    const [state, dispatch] = useReducer(workoutsReducer, {
      workouts: null,
    });

    return (
      <WorkoutsContext.Provider value={{ ...state, dispatch }}>
        {children}
      </WorkoutsContext.Provider>
    );
  };
  ```

- lets import this in our root component in `index.js` and wrap it around `App` component

  ```javascript
  import { WorkoutsContextProvider } from './context/WorkoutContext';

  ---
  <WorkoutsContextProvider>
      <App/>
  </WorkoutsContextProvider>
  ```

- lets create a new folder in `src` with name `hooks` and create a file inside it with name `useWorkoutsContext.js`
- add below code in it

  ```javascript
  import { WorkoutsContext } from "../context/WorkoutContext";
  import { useContext } from "react";

  export const useWorkoutsContext = () => {
    const context = useContext(WorkoutsContext);

    if (!context) {
      throw Error(
        "useWorkoutsContext must be used inside an WorkoutsContextProvider"
      );
    }

    return context;
  };
  ```

- now lets use this context into our `Home` component
- first we will import `useWorkoutsContext` from hooks
- we no longer need `workouts` state so we will remove that, also we will not use `useState` hook so remove it from import
- remove `setWorkouts` if response is ok

  ```javascript
  import { useEffect } from 'react';
  import { useWorkoutsContext } from '../hooks/useWorkoutsContext';
  
  const Home = () => {
    const {workouts, dispatch} = useWorkoutsContext();
    useEffect(() => {
        ---
        ---
        if(response.ok) {
            dispatch({type: 'SET_WORKOUTS', payload: json});
        }
    }, [dispatch])
  }
  ```

- lets also use this context into our `WorkoutForm` component

  ```javascript
  import { useWorkoutsContext } from '../hooks/useWorkoutsContext';
  const WorkoutForm = () => {
      const { dispatch } = useWorkoutsContext();
      const [title, setTitle] = useState('');
      ---
      ---
      ---
      if(response.ok) {
          ---
          ---
          ---
          console.log('new workout added', json);
          dispatch({type: 'CREATE_WORKOUT', payload: json});
      }
  }
  ```

- try the change on browser by adding new workout


> ***Context End***

<br>

- now lets create a delete button into `WorkoutDetails` components to add delete functionality

  ```javascript
  import { useWorkoutsContext } from '../hooks/useWorkoutsContext';

  const WorkoutDetails = ({ workout }) => {
      const { dispatch } = useWorkoutsContext();

      const handleDelete = async() => {
          const response = await fetch('/api/workouts/' + workout._id, {
              method: 'DELETE'
          });

          const json = await response.json();

          if(response.ok) {
              dispatch({type: 'DELETE_WORKOUT', payload: json});
          }
      }

      return(
          ---
          ---
          <p>{workout.createdAt}</p>
          <span onClick={handleClick}>delete</span>
      )
  }
  ```

- lets add case for `delete workout` in `src > context > WorkoutContext.js`

  ```javascript
  case 'DELETE_WORKOUT':
      return {
          workouts: state.workouts.filter((w) => w._id !== action.payload._id)
      }
  default
  ---
  ---
  ```

- verify changes into browser

- now lets do some custom error handling
- the errors we are getting now are from `mongoose`
- lets go into `backend > controllers > workoutControllers.js`
  ```javascript
  // create new workout
  ---
  ---
  let emptyFields = [];
  if(!title) {
      emptyFields.push('title');
  }
  if(!load) {
      emptyFields.push('load');
  }
  if(!reps) {
      emptyFields.push('reps');
  }
  if(emptyFields.length > 0) {
      return res.status(400).json({ error: 'Please fill in all the fields', emptyFields });
  }
  ```
- now lets come to `frontend > src > components > WorkoutForm.js`
    ``` javascript
    const [emptyFields, setEmptyFields] = useState([]);
    ---
    ---
    if(!response.ok) {
        setEmptyFields(json.emptyFields);
    }

    if(response.ok) {
        ---
        ---
        setEmptyFields([]);
    }
    ---
    ---
    <input
        ---
        value={title}
        className={emptyFields.includes('title') ? 'error' : ''}
    />
    ---
    ---
    <input
        ---
        value={load}
        className={emptyFields.includes('load') ? 'error' : ''}
    />
    ---
    ---
    <input
        ---
        value={reps}
        className={emptyFields.includes('reps') ? 'error' : ''}
    />
    ```
- lets add css for this error class into `index.css`
    ``` css
    input.error {
        border: 1px solid var(--error);
    }
    ```

- verify changes on browser

<br>

- lets add some finishing touch to our app
- add below link into `head` tag of `public > index.html`
    ``` html
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
    ```
- now come to our `WorkoutDetails` component and add below className for delete `span` tag
    ``` html
    <span className="material-symbols-outlined" onClick={handleDelete}>delete</span>
    ```
- now lets format our dates, for that we will use `date-fns` package
- install `date-fns` package into `frontend` folder using below command
    `npm install date-fns`

- import `formatDistanceToNow` function from this package into `WorkoutDetails` component
    ``` javascript
    // date fns
    import formatDistanceToNow from 'date-fns/formatDistanceToNow';
    ---
    ---
    ---
    <p>{formatDistanceToNow(new Date(workout.createAt), { addSuffix: true })}</p>
    ```

- verify changes on browser
> **The End**

> **Exercise** :- **Add Update Feature**
