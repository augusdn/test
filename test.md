---
title: 'Frontend Workshop - Movie Database'
disqus: hackmd
---

Frontend Workshop - Movie Database
===
This CSESoc frontend workshop aims to introduce you to React and Material UI in order to get you started on creating beautiful interfaces for your own projects. In this demo we will be creating the frontend for a movie database.


## Table of Contents

[TOC]

## Set up

You will need:
* A code editor (e.g. VS code, atom, etc.) + terminal
* Have node and npm installed
    * To check if you have Node.js installed, open a terminal and run: ```node -v```
    * To check if you have npm installed, run ```npm -v```
    * If you don't see a version number, visit https://www.npmjs.com/get-npm and click "Download Node.js and npm".
    

We need to get our movie info from the database. You have a few options depending on whether you attended and completed the backend workshop.

### Option 1: use your completed backend workshop code (recommended)
Use your existing api from the backend workshop. 
:::warning
Add the following to your index.js for this workshop:
```
const cors = require('cors');
app.use(cors({origin: true}));
```
Then, re-deploy to firebase.
:::

### Option 2: clone the backend workshop code (recommended)
If you didn't attend the backend workshop, or your code isn't complete, you can get a copy of the code and deploy your functions. You will need to create your own Firebase project.

Visit https://github.com/hxyalee/CSESOCFirebaseWorkshop and follow the instructions.

### Option 3: use the web API
If you don't have time to create your own Firebase project, you can fetch data from the url https://asia-east2-csesocworkshop.cloudfunctions.net/api. (More instructions on this later). However, you will not be able to fully complete the last part of the workshop.
<br/>
### Checkpoint 1: Set up complete
**Option 1 & 2:** Navigate to your url + '/getMovies' on the end (e.g. https://asia-east2-movie-workshop-12345.cloudfunctions.net/api/getMovies) in your browser.

**Option 3**: Navigate to https://asia-east2-csesocworkshop.cloudfunctions.net/api/getMovies in your browser.

**Checklist**
- [ ] I can see the list of movies in my brower
<br/>

## Create the React App
We are going to use React, a JavaScript library, to build our interface. The project can be set up with starter code using the  `create-react-app` command.

Inside the folder you want to create your project in, run the following commands:

> `npx create-react-app movie-frontend`
> `cd movie-frontend`

You can call your project anything you like, in this example the project folder is called "movie-frontend".

Start your application:
> `npm start`

Navigate to http://localhost:3000 to view your application. You should see a spinning react logo.

### Checkpoint 2: React app boilerplate can be viewed in browser

- [ ] I have the React starter files in my directory, e.g. index.js, App.js, ...
- [ ] I can see the starter template code in my browser when I go to localhost:3000
<br/>
## Adding an Element
Let's try modifying our code to display something else.

Open App.js and replace the code inside the `<header>` tag with `<h1>Hello<h1>`.

You should now have:

```gherkin=
import React from 'react';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <h1>Hello</h1>
      </header>
    </div>
  );
}

export default App;

```
Once you save your App.js file, the React app in your browser should reflect these changes.

### Styling the page
Okay, let's do something a little more useful now. We're going to turn our `<h1>Hello</h1>` into the heading for our page. Let's replace "Hello" with the name of your site, e.g. `<h1>The Movie List</h1>` 


We also need to change our styling so that it displays at the top of the page. 

Our `<header>` is has className="App-header" which means it's being styled by "App-header". Open up App.css and find .App-header. It shows all the style properties applied to this element on the page, such as the background colour.

We want our `<h1>` to appear at the top of the page, so let's change *justify-content* to `start`.

You should now have:
```gherkin=
.App-header {
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: start;   <----------
  font-size: calc(10px + 2vmin);
  color: white;
}
```
Now our heading appears at the top of the page.

> Optional: try adding or modifying the styles of your page. You could change the background colour, font, or even add a background image. See https://www.w3schools.com/css/default.asp 

### Checkpoint 3: App has a heading
Your page should look something like this.
![](https://i.imgur.com/1Xm7Qqv.png)


- [ ] Starter code has been replaced (you shouldn't see the React logo anymore).
- [ ] Heading is displayed at the top of the page.

## Fetching the Data
The main function of our website is to display the movies in the database. Let's write some code to fetch the movie information (using the backend from the previous workshop) and display it on the page.

### Requesting the list of movies
We're going to write a function to request the list of movies and their data from our firebase functions.

Let's create a new folder called in src called "services" to keep these functions separate from our html code. Inside the new services folder, create a new file called MovieService.js

![](https://i.imgur.com/PPrKmYQ.png)

Inside MovieService.js we're going to create a function called getMovies. Because our function is requesting data from an external API it might take a while to complete. So we need to make our function asynchronous. We also need to `export` the function so we can import and use it in App.js

```gherkin=
export async function getMovies() {

}
```
Inside the function, use the fetch method to retrieve the list of movies. The fetch() method takes one mandatory argument: the path to the resource you want to fetch. It returns a response which contains the status and body, among other properties.

```gherkin=
export async function getMovies() {
    const response = await fetch('https://asia-east2-csesocworkshop.cloudfunctions.net/api/getMovies');
    return await response.json();
}
```
> Change the url to your own Firebase project url. (If you are following the workshop without a Firebase project leave the url as is).

They keyword `await` will make our program wait until we get the response before continuing on. After we get the response we return `response.json()` which parses the body of our response as JSON.

### Render the list of movies
Switch back to the App.js file. We are going to use `react-async` to call our asynchronous function and render it when we have the response.

> In your terminal run the command:`npm install react-async`

Now add `import Async from 'react-async';` to the top of your App.js file with the other imports.

We want to call getMovies() from this file, so add `import { getMovies } from './services/MovieService';`


Now add `<Async>` tags and give the promiseFn our getMovies function.

```gherkin=
<Async promiseFn={getMovies}>
   ...
</Async>
}
```
Inside the `<Async>` tags we will render something different depending on the whether the async function is Loading, Fulfilled, or Rejected.

If fetching the movie list was successful we will render a list of movies.
```gherkin=
<Async promiseFn={getMovies}>
  <Async.Loading>Loading...</Async.Loading>
  <Async.Fulfilled>
    {data => {
      return(
          <ul>
            {data.movies.map((movie, index) => (
              <li key={index}>
                {movie.title}
              </li>
            ))}  
          </ul>
      )
    }}
  </Async.Fulfilled>
  <Async.Rejected>
    Something went wrong.
  </Async.Rejected>
</Async>
```
Note: each element of the array need a unique identifier`key`. This allows React to detect changes.
### Checkpoint 4: the list of movies is displayed
![](https://i.imgur.com/SRMAPOc.png)

- [ ] List of movies is displayed in alphabetical order.
<br/>
## Styling the movie items using Material UI
We now have a list of movies, but dot points is a little boring. Instead we're going to render them like this:
![](https://i.imgur.com/iijibDy.png)

We don't need to write all of the css and components for this from scratch. We can use a UI framework, such as [Material UI](https://material-ui.com/).

> In your terminal run the command `npm install @material-ui/core`.

We can use a Material UI component, e.g. Card https://material-ui.com/components/cards/, find one that suits our needs, then copy and paste the example code. Just remember to import the component at the top of the file, e.g. `import Card from @material-ui/core/Card`;
![](https://i.imgur.com/s9nBWrz.png)

### Movie Card Component
We're going to create a reusable component called MovieCard which displays the information about a single movie nicely.

Underneath our `function App()` we're going to create another functional component: `function MovieCard()`

```gherkin=
function MovieCard() {

}
```
We can call and render this component from within our App component. Replace the `<ul>` tag with `<div>` and `<li>` with our new `<MovieCard />` component.

```gherkin=
    ...
        <Async.Fulfilled>
            {data => {
              return(
                <div>
                  {data.movies.map((movie, index) => (
                    <MovieCard key={index} />
                  ))}  
                </div>
              )
            }}
          </Async.Fulfilled>
    ...
```

Now copy and paste the Material UI code for the Card into the MovieCard functional component. Delete all unnecessary styling and sub-components (e.g. in this example I'm going to delete the description and buttons). 

You can also change the Card height and width to get the dimesions you want. In this example I added some inline styling to make the card 275 wide and the height of the movie image 400.

```gherkin=
function MovieCard() {
  return (
    <Card style={{width: 275, margin: 20}}>
      <CardActionArea>
        <CardMedia style={{height: 400}} image="/static/images/cards/contemplative-reptile.jpg"/>
        <CardContent>
          <Typography  variant="h5" component="h2">
            Lizard
          </Typography>
        </CardContent>
      </CardActionArea>
    </Card>
  );
}
```
Now we want to change the Card info from the lizard demo data to our movie data. The values will be different for each movie, so we need to pass these into the function as props (kind of like parameters in C).

In our App component, we will pass the movie data to the component like so:
```gherkin=
    <MovieCard 
      key={index} 
      title={movie.title}
      yearReleased={movie.yearReleased}
      imageURL={movie.imageURL}
      genres={movie.genres}
    />
```

Now we can access the movie title, year, image, and genres from the MovieCard component through `props`. E.g. `props.title` gets us the movie title.

```gherkin=
function MovieCard(props) {
  return (
    <Card style={{width: 275, margin: 20}}>
      <CardActionArea>
        <CardMedia style={{height: 400}} image={props.imageURL}/>
        <CardContent>
          <Typography  variant="h5" component="h2">
            {props.title} ({props.yearReleased})
          </Typography>
        </CardContent>
      </CardActionArea>
    </Card>
  );
}
```

### Checkpoint 5: movies are displayed in a card
![](https://i.imgur.com/PR8Jb4S.png)

- [ ] I have a new functional component called MovieCard
- [ ] I have styled and customised the Card
- [ ] I am passing the movie properties into the MovieCard from the App component 
- [ ] Lizard demo data has been replaced with the passed in movie props

### Using Flexbox to lay out the cards (optional)
Flexbox is a module of many different properties that define how a series of elements should be positioned. See https://css-tricks.com/snippets/css/a-guide-to-flexbox/ for a helpful guide.

Give the surrounding div a CSS class (in this example our class is called "container"):
```gherkin
    ...
        <div className="container">
          {data.movies.map((movie, index) => (
            <MovieCard 
              key={index} 
              title={movie.title}
              yearReleased={movie.yearReleased}
              imageURL={movie.imageURL}
              genres={movie.genres}
              />
          ))}  
        </div>
    ...
```

Now, in App.css create the "container" class and give it the desired flexbox properties:
* First we need add `display: flex` to define the component as a flex container.
* We want our movies to overflow in to the next row, so set`flex-wrap` to `wrap`.

```gherkin=
.container {
  display: flex;
  flex-wrap: wrap;
}
```
By default, our items appear in a row and our `justify-content` property is `flex start` (left-aligned). If you want to your items to display differently, e.g. as a column or be center justified, you need to change these properties (see https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
![](https://i.imgur.com/RUOx9wW.png)

Now, our movies are displayed like this:
![](https://i.imgur.com/bFDWN1B.png)

### Styling the list of genres
To style the list of genres, we will use the Material UI Chip component https://material-ui.com/components/chips/. Add `import Chip from '@material-ui/core/Chip';` to the list of imports at the top of your App.js file.

Similar to rending the list of movies, we will use `map` on the array of genres. Add your code after the `<Typography>` tags for the movie title.

```gherkin=
      <div>
        {props.genres.map((genre, index) => (
          <Chip key={index} label={genre} style={{margin: 5}}/>
         ))} 
      </div>
```
Your movies should now display the genres like this:
![](https://i.imgur.com/iijibDy.png)


### Checklist 6: Genres Displayed on Card
- [ ] The movie genres specific to each movie are displayed on the card.

## Routes
In the second part of the workshop, we will create a form that users can use to submit new movies. This form will be on a different page to our movie list. Therefore our app needs to be able to display different pages depending on the url path.

Locally, our 'localhost:3000' is our home page. We can view our movie list by visiting this url. But, we want our submission form to be accessible from 'localhost:3000/addMovie'.

To do this, we will use *routes*.
> In your terminal run `npm install react-router-dom` 

Add `import {BrowserRouter as Router, Switch, Route} from 'react-router-dom';` to the list of imports at the top of your App.js file. 

### Creating the routes
To make the App component less cluttered, you might like to extract the movie list code to it's own functional component (in this example it's called 'Home').

```gherkin=
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <Home />
      </header>
    </div>
  );
}

function Home() {
  return (
    <div>
      <h1>The Movie List</h1>
      <Async promiseFn={getMovies}>
        <Async.Loading>Loading...</Async.Loading>
        <Async.Fulfilled>
          {data => {
            return(
              <div className="container">
                {data.movies.map((movie, index) => (
                  <MovieCard 
                    key={index} 
                    title={movie.title}
                    yearReleased={movie.yearReleased}
                    imageURL={movie.imageURL}
                    genres={movie.genres}
                    />
                ))}  
              </div>
            )
          }}
        </Async.Fulfilled>
        <Async.Rejected>
          {error => `Something went wrong: ${error.message}`}
        </Async.Rejected>
      </Async>
    </div>
  );
}
```



Now add `<Router>` and `<Switch>` tags. `<Switch>` will look at the URL and render the first one of its `<Route>` children that matches. Let's add 2 routes:
```gherkin=
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <Router>
            <Switch>
                <Route path="/addMovie">
                    {/* TODO: add movie form component here */}
                </Route>
                <Route path="/">
                    <Home />
                </Route>
            </Switch>
        </Router>
      </header>
    </div>
  );
}
```

### Checkpoint 7: Routes 
- [ ] Navigating to 'localhost:3000/addMovie' takes you to an empty page.
- [ ] Navigating back to 'localhost:3000' displays the movie list.

## Add Movie Form
We're now going to create a component for the Add Movie page. To avoid cluttering our App.js file, we're going to put our the code for our new page in another file and import it into App.js

Create a new file in the same directory as App.js called "AddMovie.js".

Inside AddMovie.js create a new functional component called AddMovie which returns some simple html to begin with:
```gherkin=
import React from 'react';

export function AddMovie() {
    return(
        <h1>Add a Movie</h1>
    );
}
```
If you navigate to http://localhost:3000/addMovie you should see the new changes.

### Create the Form
Forms are used to gather one or more inputs from the user. In this demo we'll gather the **title**, **year released**, **image url**, and **genres** from the user.

First create a Card to contain the form --this will provide a nice blank canvas to work on. Then give it a width (in this example I used inline styling to do this).

> **Note**: you will need to wrap the `<h1>` and `<Card>` in a `<div>` or other container as React only allows 1 element to be returned).

```gherkin=
import React from 'react';
import Card from '@material-ui/core/Card';

export function AddMovie() {
    return(
        <div>
            <h1>Add a Movie</h1>
            <Card style={{width: 800}}>
                {/* Form will go here */}
            </Card>
        </div>
    );
}
```

Add a html `<form>` tag inside the Card to define a form. We're going to use a Material UI TextField to capture the user data. Visit https://material-ui.com/components/text-fields/ and find a styled textfield that you like, copy the code into your form.

Change the id and label to signify that this textfield is for capturing the movie title. The `label` value is what gets displayed on the form.
E.g. `<TextField id="title" label="Title" variant="outlined"/>`

Now do the same for the other inputs:
```gherkin=
...
  <Card style={{width: 800}}>
      <form>
          <TextField id="title" label="Title" variant="outlined" />
          <TextField id="year-released" label="Year Released" variant="outlined" />
          <TextField id="image-url" label="Image URL" variant="outlined" />
          <TextField id="genres" label="Genres" variant="outlined" />
      </form>
  </Card>
 ...
```

You should now see 4 text input fields.

![](https://i.imgur.com/FR3uUVt.png)

### Checkpoint 8: Form has Text Inputs
- [ ] I have a new file called AddMovie.js
- [ ] Card element has a defined width and contains the form.
- [ ] My form has 4 text inputs that can be typed in.

### Adding Buttons to the Form
Pick out a Material UI button you'd like to use from https://material-ui.com/components/buttons/. Add your `<Button>` code twice underneath the textfields: Submit and Cancel. (I also added some inline styling for the margins).
```gherkin=
    <Button variant="contained" color="primary" style={{margin: 10}}>
        Submit
    </Button>
    <Button variant="outlined" style={{margin: 10}}>
        Cancel
    </Button>
```

### Styling the Form (Optional)
We would now like to change the layout of the TextFields so it looks more like a form. 

![](https://i.imgur.com/JQ2SVNE.png)

To do this, we can use our flexbox properties again. Create a `<div>` around the `<TextField` tags and give it the `display: flex` property. You can do this via inline styling or create an AddMovie.css

We want our form elements to wrap around onto the next row, so we'll also add `flex-wrap: wrap`. To not have any gaps on the left and right of our form, we'll use `justify-content: space-between`.

```gherkin=
<div style={{display: 'flex', flexWrap: 'wrap', justifyContent: 'space-between'}}>
    <TextField id="title" label="Title" variant="outlined"/>
    <TextField id="year-released" label="Year Released" variant="outlined" />
    <TextField id="image-url" label="Image URL" variant="outlined" />
    <TextField id="genres" label="Genres" variant="outlined" />
</div>
```

> Visit https://css-tricks.com/snippets/css/a-guide-to-flexbox/ to refresh your knowledge.

Add some styling to the `Textfield` components too:
* Add a margin to each input element
* Add the `fullWidth` property to the image URL and genres textfields so they take up a whole row.
* Add a`flex-basis` property to the title textfield so that it takes up a greater percentage of space than the year released textfield.

```gherkin=
<div style={{display: 'flex', flexWrap: 'wrap', justifyContent: 'space-between'}}>
    <TextField id="title" label="Title" variant="outlined" style={{flexBasis: '70%', margin: 10}}/>
    <TextField id="year-released" label="Year Released" variant="outlined" style={{margin: 10}}/>
    <TextField id="image-url" label="Image URL" variant="outlined" style={{margin: 10}} fullWidth/>  
    <TextField id="genres" label="Genres" variant="outlined" style={{margin: 10}} fullWidth/>
</div>
```

Your form should now look like the above image.
<br/>
## Form Submission
When the user fills out the form and clicks the 'Submit' button. We'd like to perform some action.

* Add `type="submit"` to the Submit Button element.
* Add `onSubmit={handleSubmit}` to the `<form>` element. (`handleSubmit` is the name of the function we are about the write) 

To begin with let's make our handleSubmit function pop up an alert message:
```gherkin=
export function AddMovie() {

    const handleSubmit = (event) => {
        event.preventDefault();
        alert("Form submitted!");
    };

    return(
        <div>
            <h1>Add a Movie</h1>
                    ...
```
### Checkpoint 9: handleSubmit function is called
- [ ] When the 'Submit' button is pressed, an alert message pops up on the screen.
<br/>
### Keeping track of state
In React, form values are kept in the React state and update when there are any changes (e.g. a user enters characters into the textfield). We'll use hooks to do this.

Hooks let you use state and other React features without writing a class. This is an example from the [react website](https://reactjs.org/docs/hooks-overview.html). When the button is pressed the counter increases.

```gherkin=
import React, { useState } from 'react';

function Example() {
  // Declare a new state variable, which we'll call "count".
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

First, import `useState` at the top of your AddMovie.js file. 

Then, inside the AddMovie() component, create a state variable for the movie title:
```gherkin=
export function AddMovie() {

    const [title, setTitle] = useState('');

    const handleSubmit = (event) => {
        event.preventDefault();
        alert("Form submitted!");
    };
```
Now we'll modify the title textfield to update the state when there are changes detected (i.e. `onChange`)
```gherkin=
<TextField id="title" label="Title" onChange={(event) => setTitle(event.target.value)} variant="outlined" style={{flexBasis: '70%', margin: 10}}/>
```
To see our new changes in effect, modify the alert message to print out the title when the form submits. Our state value for the movie title can be referenced inside our function using its name: `title`.
```gherkin=
const handleSubmit = (event) => {
    event.preventDefault();
    alert(`Form submitted with title: ${title}`);  
};
```
Repeat these steps for the other inputs.

### Checkpoint 10: Form values have state
- [ ] I have state variables for each of my form inputs.
- [ ] The title, year, image URL, and genres are displayed in the alert.
<br/>
### Sending the POST request - !!Own Database ONLY
:::danger
**IMPORTANT:** Only do this step if you have your own Firebase project (i.e. Option 1 or 2).
:::

We need to send the new movie data to our backend which will add the movie to the database. Open up the services/MovieService.js file, and create a new function. This time we will give it some input `data` which holds the movie info.

```gherkin=
export async function addMovie(data) {

}
```
Similar to getMovies() we will use fetch. The url should be your database url + '/createNewMovie' on the end. 
E.g. `https://asia-east2-movie-workshop-12345.cloudfunctions.net/api/createNewMovie`

Since we're sending data, we'll pass in the optional init object to specify a few settings:
* `method` will be 'POST' because we're sending data
* the second line just signals that the format is JSON
* in our `body` we'll send the data in the format our backend expects
```gherkin=
export async function addMovie(data) {
    const response = await fetch('https://asia-east2-movie-workshop-12345.cloudfunctions.net/api/createNewMovie', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({
            title: data.title,
            yearReleased: data.yearReleased,
            imageURL: data.imageURL,
            genres: data.genres,
        })
    });
    return await response.json();
}
```
Open the AddMovie.js file back up, and import your addMovie function at the top of the file, e.g. `import { addMovie } from './services/MovieService';`

Modify the `handleSubmit` function. Replace the alert with a call to `addMovie`, passing in the movie info data as an object. Make sure to change your function to `async`.
> **Optional:** add `window.location.href='./'` to your code to take the user back to the movie list page after the form is submitted.

```gherkin=
const handleSubmit = async (event) => {
    event.preventDefault();
    const data = {
        title: title,
        yearReleased: yearReleased,
        imageURL: imageURL,
        genres: genres,
    };
    await addMovie(data);
    window.location.href='./';
};
```
Test out your code by entering a movie into the form and pressing submit.

### Checkpoint 11: New Movie Appears in the Movie List
- [ ] Form submits successfully
- [ ] New movie appears in the movie list at https://localhost:3000
<br/>

### Linking between the Movie List and Add Movie Form (Optional)
Change the 'Cancel' button to act as a link to another page by adding `component={Link}`. Tell it to go to the home page by adding `to='/'`.

```gherkin=
<Button component={Link} to='/' variant="outlined" style={{margin: 10}}>Cancel</Button>
```
In order to use `Link` remember to `import { Link } from 'react-router-dom';`
<br/>
To get to the Add Movie Form from the home page, we'll add a Floating Action Button (FAB). Visit https://material-ui.com/components/floating-action-button/ to find a FAB style that suits your website.

Open up our App.js which contains the code for the home page. At the bottom of our Home() function, after the closing `</Async>` tag, add the FAB code.
```gherkin=
<Fab color="primary" aria-label="add">
    <AddIcon />
</Fab>
```
To position the '+' button at the bottom right corner of the screen (or wherever you want it to appear), add some styling: e.g. `style={{position: 'fixed', right: 0, bottom: 0, margin: 30}}`

Finally, add a `<Link>` tag around the `<Fab>`. Link it to the movie form page by adding `to="/addMovie"`
```gherkin=
<Link to="/addMovie">
    <Fab color="primary" aria-label="add" style={{position: 'fixed', right: 0, bottom: 0, margin: 30}}>
      <AddIcon />
    </Fab>
</Link>
```
Click the FAB and check it takes you to the movie form page.

## Hosting - !!Own Database ONLY
Congratulations you have finished building the movie database app! The only thing left to do is host it using Firebase. Since we already have a Firebase project from the backend workshop, this will be fairly quick and straighforward.

In your react app directory, run the following command to create a production build of your app:
> `npm run build`

You should now see a *build* folder has been added to your directory. Inside *build/static* are your js and css files.

Now run the following to initialise firebase in your app:
> `firebase init`

You should already have firebase tools installed and be logged in. But if the command doesn't work try running `npm install -g firebase-tools` and `firebase login`, before trying again.

For this demo, we'll select the following configurations:
1. At the first prompt, select 'Hosting' using the arrows and space key:
![](https://i.imgur.com/IHKsAY9.png)
2. Select 'User an existing project'
3. Select your firebase project from the list of options, e.g. movie-workshop-12345
4. Type 'build' to use the newly create build folder as the project's public directory.
5. For our app, we'll type 'N' and not configure it as a single-page app.
6. Enter 'N' again to not overwrite the build/index.html file. This file was created when we did the production build so we want to keep it as is.
![](https://i.imgur.com/oGMtkTE.png)

Only thing left to do is deploy:
> `firebase deploy`

Navigate to the Hosting URL the console prints out and view your app (something like **movie-workshop-12345.web.app**). Try testing out your pages to confirm everything is working properly.

Congratulations --happy coding for the [Personal Projects Competition](https://www.facebook.com/events/226604452107246/) :)
<br/>

## Resources
### React
React: https://reactjs.org/
React Routes: https://reactrouter.com/web/guides/quick-start
Hooks: https://reactjs.org/docs/hooks-intro.html
Production Build (npm run build): https://create-react-app.dev/docs/production-build/
Deploying to Firebase Hosting: https://dzone.com/articles/react-apps-firebase

### Material UI
Material UI Library: https://material-ui.com/

### CSS
Flexbox: https://css-tricks.com/snippets/css/a-guide-to-flexbox/

### APIs
REST APIs: https://www.smashingmagazine.com/2018/01/understanding-using-rest-api/
Fetch API: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
:::info
**Find this document incomplete or has an error? Have further questions?** 
Contact: alli.murray@unsw.edu.au (i'll try my best)
:::
