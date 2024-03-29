# How to Connect to an REST-API with JavaScript

A big part of working with JavaScript is knowing how to connect to APIs. 
As a fledgling developer, you may have been told at some point to just “play around with some APIs!” 
to learn what they are and how to work with them. If you’ve ever taken a look at the documentation 
for an API and had no idea where to start or what to do and gotten frustrated, this is the article for you.

We’re going to make a very simple web app with plain JavaScript that will retrieve information from an API and display it on the page. There will be no server, dependencies, build tools, or anything else to 
further muddy the waters on an already difficult and confusing topic for beginners.

## Prerequisites
- Basic knowledge of [HTML and CSS](https://internetingishard.com/html-and-css/).
- Basic knowledge of [JavaScript syntax and datatypes](https://www.sitepoint.com/beginners-guide-javascript-variables-and-datatypes/).
- Basic knowledge of working with [JSON and JavaScript objects](https://www.taniarascia.com/how-to-use-json-data-with-php-or-javascript/). This brief article will clear it up completely.
- Node.js and JSON Server installed on your computer
- Download the latest version of [Node.js](https://nodejs.org) and install it on your computer.
- Follow the instructions in the getting [started section](https://github.com/typicode/json-server#getting-started) to install the JSON Server.
  
Everything else we’ll cover along the way.

## Goals

We are going to write from scratch a web app that connects to an API (in our case an fake json server API), retrieves the data with JavaScript, and displays it on the front end of a website. This is not meant to be an extensive resource on APIs or REST – just the simplest possible example to get up and running that you can build from in the future. We’ll learn:

- What a Web API is.
- Learn how to use the HTTP request `GET` with JavaScript
- How create and display HTML elements with JavaScript.

It will look like this:

![screenshot](https://raw.githubusercontent.com/DavesProgrammingSamples/HowToConnectToAnApi/master/screenshot_webpage.PNG "screenshot")

Let's get started.

## Quick Overview

**API** stands for Application Program Interface, which can be defined as a set of methods of communication between various software components. In other words, an API allows software to communicate with another software.

We’ll be focusing specifically on Web APIs, which allow a web server to interact with third-party software. In this case, the web server is using **HTTP requests** to communicate to a publicly available URL **endpoint** containing JSON data. If this is confusing now, it will make sense by the end of the article.

You may be familiar with the concept of a **CRUD** app, which stands for Create, Read, Update, Delete. Any programming language can be used to make a CRUD app with various methods. A web API uses HTTP requests that correspond to the CRUD verbs.


| Action  | HTTP       |  Method Definition             |  
|---------|------------|--------------------------------|
| Create  | POST       |  Creates a new resource        |  
| Read    | GET        |  Retrieves a resource          |  
| Update  | PUT/PATCH  |  Updates an existing resource  |  
| Delete  | DELETE     |  Deletes a resource            |  

> If you’ve heard REST and RESTful APIs, that is simply referring to a set of standards that conform to a specific architectural style. Most web apps do, or aim to conform to REST standards. Overall, there are a lot of terms, acronyms and concepts to understand – HTTP, API, REST – so it’s normal to feel confused and frustrated, especially when API documentation assumes you already know what to do.

## Setting Up

What is our objective? We want to get the data for the top 10 IMDB movies and display the titles and descriptions in a grid. For some background knowledge, IMDb (Internet Movie Database) is an online database of information related to films, television programs, home videos and video games, and internet streams, including cast, production crew and personnel biographies, plot summaries, trivia, and fan reviews and ratings.

We’re going to start by creating an **index.html** file in a new directory. The project will only consist of **index.html, style.css**, and **script.js** at the end. This HTML skeleton just links to a CSS and JavaScript file, loads in a font, and contains a div with a `root` id. This file is complete and will not change. We’ll be using JavaScript to add everything from here out.

### index.html
```xml
<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>WebApp</title>
  
  <link href="https://fonts.googleapis.com/css?family=Dosis:400,700" rel="stylesheet">
  <link href="style.css" rel="stylesheet">

</head>

<body>

  <div id="root"></div>

  <script src="script.js"></script>
</body>

</html>
```

Since this article is focused on the concepts of APIs and JavaScript, I will not be explaining how the CSS works. We will create a *style.css* that will be used to create a grid. For brevity’s sake, I only included the most pertinent *structural* CSS below, but you can copy the [full CSS code here](https://raw.githubusercontent.com/taniarascia/sandbox/master/ghibli/style.css).

### style.css
```css

#root {
  max-width: 1200px;
  margin: 0 auto;
}

.container {
  display: flex;
  flex-wrap: wrap;
}

.card {
  margin: 1rem;
  border: 1px solid gray;
}

@media screen and (min-width: 600px) {
  .card {
    flex: 1 1 calc(50% - 2rem);
  }
}

@media screen and (min-width: 900px) {
  .card {
    flex: 1 1 calc(33% - 2rem);
  }
}

```

Now we have HTML and CSS set up, so you can make **script.js** and we’ll continue from there.

# Connecting to the API

**Make sure that you have completed the previously mentioned points**
- Node.js and JSON Server installed on your computer (Node.js is already installed on school computers) 
- Download the latest version of [Node.js](https://nodejs.org) and install it on your computer.
- Follow the instructions in the getting [started section](https://github.com/typicode/json-server#getting-started) to install the JSON Server.

Let’s take a look at the JSON Server. The JSON Server gives you the abilitiy to get a Fake REST API real quickly. We will create our own endpoint. In our example we will demonstrate how to show the Data from out API on a webpage. It is perfect to show junior developers how to interact with resources using HTTP requests, which is perfect for us here. Since an API can be accessed by many different methods – JavaScript, PHP, Ruby, Python and so on – the documentation for most APIs doesn’t tend to give specific instructions for how to connect.

For our example I created a json File containing the Top 10 Movies of The Internet Movie Database and saved the content to a json file (imdbfilms.json). 
The file contains the datastructure and the content similiar to a database.
So create an file named imdbfilms.json and save it.
```json
{
  "films": [
    {
      "id": "abcd1",
	  "rank": "1",
      "title": "The Shawshank Redemption",
	  "rating": "9,3",
      "description": "Two imprisoned men bond over a number of years, finding solace and eventual redemption through acts of common decency.",
      "director": "Frank Darabont",
      "stars": ["Tim Robbins", "Morgan Freeman", "Bob Gunton"]
    },
	{
      "id": "qwer1",
	  "rank": "2",
      "title": "The Godfather",
	  "rating": "9,2",
      "description": "The aging patriarch of an organized crime dynasty transfers control of his clandestine empire to his reluctant son.",
      "director": "Francis Ford Coppola",
      "stars": ["Marlon Brando", "Al Pacino", "James Caan"]
    },
	{
      "id": "cvht3",
	  "rank": "3",
      "title": "The Dark Knight",
	  "rating": "9,0",
      "description": "When the menace known as the Joker emerges from his mysterious past, he wreaks havoc and chaos on the people of Gotham. The Dark Knight must accept one of the greatest psychological and physical tests of his ability to fight injustice.",
      "director": "Christopher Nolan",
      "stars": ["Christian Bale", "Heath Ledger", "Aaron Eckhart"]
    },
	{
      "id": "ertb8",
	  "rank": "4",
      "title": "The Godfather: Part II",
	  "rating": "9,0",
      "description": "The early life and career of Vito Corleone in 1920s New York City is portrayed, while his son, Michael, expands and tightens his grip on the family crime syndicate.",
      "director": "Francis Ford Coppola",
      "stars": ["Al Pacino", "Robert De Niro", "Robert Duvall"]
    },
	{
      "id": "werg2",
	  "rank": "5",
      "title": "The Lord of the Rings: The Return of the King",
	  "rating": "8,9",
      "description": "Gandalf and Aragorn lead the World of Men against Sauron's army to draw his gaze from Frodo and Sam as they approach Mount Doom with the One Ring.",
      "director": "Peter Jackson",
      "stars": ["Elijah Wood", "Viggo Mortensen", "Ian McKellen"]
    },
	{
      "id": "msge9",
	  "rank": "6",
      "title": "Pulp Fiction",
	  "rating": "8,9",
      "description": "The lives of two mob hitmen, a boxer, a gangster's wife, and a pair of diner bandits intertwine in four tales of violence and redemption.",
      "director": "Quentin Tarantino",
      "stars": ["John Travolta", "Uma Thurman", "Samuel L. Jackson"]
    },
	{
      "id": "mfgr4",
	  "rank": "7",
      "title": "Schindler's List",
	  "rating": "8,9",
      "description": "In German-occupied Poland during World War II, industrialist Oskar Schindler gradually becomes concerned for his Jewish workforce after witnessing their persecution by the Nazis.",
      "director": "Steven Spielberg",
      "stars": ["Liam Neeson", "Ralph Fiennes", "Ben Kingsley"]
    },
	{
      "id": "mfgr4",
	  "rank": "8",
      "title": "12 Angry Men",
	  "rating": "8,9",
      "description": "A jury holdout attempts to prevent a miscarriage of justice by forcing his colleagues to reconsider the evidence.",
      "director": "Sidney Lumet",
      "stars": ["Henry Fonda", "Lee J. Cobb", "Martin Balsam"]
    },
	{
      "id": "qerg5",
	  "rank": "9",
      "title": "Fight Club",
	  "rating": "8,8",
      "description": "An insomniac office worker and a devil-may-care soapmaker form an underground fight club that evolves into something much, much more.",
      "director": "David Fincher",
      "stars": ["Brad Pitt", "Edward Norton", "Meat Loaf"]
    },
	{
      "id": "adcd3",
	  "rank": "10",
      "title": "The Lord of the Rings: The Fellowship of the Ring",
	  "rating": "8,8",
      "description": "A meek Hobbit from the Shire and eight companions set out on a journey to destroy the powerful One Ring and save Middle-earth from the Dark Lord Sauron.",
      "director": "Peter Jackson",
      "stars": ["Elijah Wood", "Ian McKellen", "Orlando Bloom"]
    }
  ]
}
```

So start a command line window and switch to the folder where you saved the imdbfilms.json file. Start the JSON Server with the following command: json-server --watch imdbfilms.json

Now the API is ready to be used. You can access the endpoint via http://localhost:3000/films 


## Retrieving the data with a HTTP request

Before we try to put anything on the front end of the website, let’s open a connection the API. We’ll do so using ```XMLHttpRequest``` objects, which is a way to open files and make an HTTP request.

We’ll create a ```request variable``` and assign a new ```XMLHttpRequest``` object to it. Then we’ll open a new connection with the ```open()``` method – in the arguments we’ll specify the type of request as ```GET``` as well as the URL of the API endpoint. The request completes and we can access the data inside the ```onload``` function. When we’re done, we’ll send the request.
So create a file called script.js and save it. 

### script.js

```javascript
// Create a request variable and assign a new XMLHttpRequest object to it.
var request = new XMLHttpRequest();

// Open a new connection, using the GET request on the URL endpoint
request.open('GET', 'http://localhost:3000/films', true);

request.onload = function () {
  // Begin accessing JSON data here
  
}

// Send request
request.send();
```


Now open the index.html in the Chrome Browser, open the Developer Tool (Strg + Shift + I) and switch to the Network tab. Reload the page.
<p>You can see that the request has been made with a status response 200. 200 is the standard response for successful HTTP requests.
If you are interested in further HTTP status visit: [List of HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
</p>

![screenshot](https://raw.githubusercontent.com/DavesProgrammingSamples/HowToConnectToAnApi/master/devtool1.JPG "screenshot")

<p>
If you click on films and on Response you can see that the data became loaded from the server.
</p>

![screenshot](https://raw.githubusercontent.com/DavesProgrammingSamples/HowToConnectToAnApi/master/devtool2.JPG "screenshot")




## Working with the JSON response
Now we’ve received a response from our HTTP request, and we can work with it. However, the response is in JSON, and we need to convert that JSON into JavaScript objects in order to work with it.

We’re going to use `JSON.parse()` to parse the response, and create a `data` variable that contains all the JSON as an array of JavaScript objects. Using `forEach()`, we’ll console log out the title of each film to ensure it’s working properly.


### script.js

```javascript
// Begin accessing JSON data here
var data = JSON.parse(this.response);

data.forEach(movie => {
  // Log each movie's title
  console.log(movie.title);
});

```

Using Inspect (Strg + Shift + I for Chrome Browsers) on index.html and viewing the console, you should see the titles of 20 Ghibli films. Success!

The only thing we’re missing here is some way to deal with errors. What if the wrong URL is used, or the URL broke and nothing was being displayed? When an HTTP request is made, the response returns with [HTTP status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status). `404` is the most well-known response, meaning Not Found, and `200` OK is a successful request.

## Dealing with Errors

Let’s just wrap our code in an `if` statement, succeeding on any response in the 200-300 range, and log out an error if the request fails. You can mess up the URL to test the error.

### script.js
```javascript
// Begin accessing JSON data here
var data = JSON.parse(this.response);

if (request.status >= 200 && request.status < 400) {
  data.forEach(movie => {
    console.log(movie.title);
  });
} else {
  console.log('error');
}
```

Here is the entire code so far.

### script.js
```javascript
var request = new XMLHttpRequest();

request.open('GET', 'http://localhost:3000/films', true);
request.onload = function () {

  // Begin accessing JSON data here
  var data = JSON.parse(this.response);

  if (request.status >= 200 && request.status < 400) {
    data.forEach(movie => {
      console.log(movie.title);
    });
  } else {
    console.log('error');
  }
}

request.send();
```

We’ve successfully used a `GET` HTTP request to retrieve (or consume) the API endpoint, which consisted of data in JSON format. However, we’re still stuck in the console – we want to display this data on the front end of the website, which we’ll do by modifying the DOM.

`XMLHttpRequest` is a widely supported built-in method of making requests. There is also a newer [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) which can be used for the same purpose, which is simpler but has less browser support.

## Displaying the Data
In order to display information on the front end of a site, we’ll be working with the DOM, which is actually an API itself that allows JavaScript to communicate with HTML. If you have no experience at all with the DOM, here is a [link](https://www.digitalocean.com/community/tutorials/introduction-to-the-dom) to a DigitalOcean Site that will clarify what the DOM is and how the DOM differs from HTML source code.

By the end, our page will consist of a logo image followed by a container with multiple card elements – one for each film. Each card will have a heading and a paragraph, that contains the title and description of each film. Here’s what that looks like, with only essential CSS loaded in:

![screenshot](https://raw.githubusercontent.com/DavesProgrammingSamples/HowToConnectToAnApi/master/screenshot_webpage_simplestyle.PNG "screenshot")

If you remember, our *index.html* just has a root div – `<div id="root">` right now. We’ll access it with `getElementById()`. *We can briefly remove all the previous code in the script.js file we’ve written for now, which we’ll add back in just a moment*.

### script.js
```
const app = document.getElementById('root');
```

If you’re not 100% positive what `getElementById()` does, take the above code and `console.log(app)`. That should help clarify what is actually happening there.

The first thing in our website is the logo, which is an `img` element. We’ll create the image element with `createElement()`.

```
const logo = document.createElement('img');
```

An empty `img` is no good, so we’ll set the `src` attribute to `logo.png`. The image can be found here: ([logo.png](https://github.com/DavesProgramingSamples/HowToConnectToAnApi/blob/master/logo.png)). So download it i and save it to the same folder as the other files.
```
logo.src = 'logo.png';
```

We’ll create another element, a `div` this time, and set the `class` attribute to `container`.

```
const container = document.createElement('div');
container.setAttribute('class', 'container');
```

Now we have a logo and a container, and we just need to place them in the website. We’ll use the `appendChild()` method to append the logo image and container div to the app root.

```
app.appendChild(logo);
app.appendChild(container);
```

Here is the full code for that. Put that code in front of the already written code.

### script.js
```
const app = document.getElementById('root');

const logo = document.createElement('img');
logo.src = 'logo.png';

const container = document.createElement('div');
container.setAttribute('class', 'container');

app.appendChild(logo);
app.appendChild(container);
```

After saving, on the front end of the website, you’ll see the following.

### Elements
```xml
<div id="root">
  <img src="logo.png">
  <div class="container">
  </div>
</div>
```

This will only be visible in the _Inspect_ Elements tab, not in the HTML source code, as explained in the DOM article I linked.

Now we’re going to paste all our code from earlier back in. The last step will be to take what we consoled out previously and make them into card elements.

Paste everything back in, but we’ll just be looking at what’s inside the `forEach()`.

```javascript
data.forEach(movie => {
  console.log(movie.title);
  console.log(movie.description);
});
```

Now we will change the javaScript Code to the following. Instead of `console.log`, we’ll use `textContent` to set the text of an HTML element to the data from the API. I’m using `substring()` on the `p` element to limit the description and keep each card equal length.

### script.js
```javascript
data.forEach(movie => {
  // Create a div with a card class
  const card = document.createElement('div');
  card.setAttribute('class', 'card');

  // Create an h1 and set the text content to the film's title
  const h1 = document.createElement('h1');
  h1.textContent = movie.title;

  // Create a p and set the text content to the film's description
  const p = document.createElement('p');
  movie.description = movie.description.substring(0, 300); // Limit to 300 chars
  p.textContent = `${movie.description}...`; // End with an ellipses

  // Append the cards to the container element
  container.appendChild(card);

  // Each card will contain an h1 and a p
  card.appendChild(h1);
  card.appendChild(p);
});
```

I’ll also replace the console’d error with an error on the front end, using the best HTML element, ```[marquee](https://developer.mozilla.org/de/docs/Web/HTML/Element/marquee)```! (I’m only doing this as a joke for fun and demonstrative purposes, do not actually use ```marquee``` in any sort of real application, or take me seriously here.)

```
const errorMessage = document.createElement('marquee');
errorMessage.textContent = `Gah, it's not working!`;
app.appendChild(errorMessage);
```

And we’re done! Here is the final script.js code.

### script.js
```javascript
const app = document.getElementById('root');

const logo = document.createElement('img');
logo.src = 'logo.png';

const container = document.createElement('div');
container.setAttribute('class', 'container');

app.appendChild(logo);
app.appendChild(container);

var request = new XMLHttpRequest();
request.open('GET', 'http://localhost:3000/films', true);
request.onload = function () {

  // Begin accessing JSON data here
  var data = JSON.parse(this.response);
  if (request.status >= 200 && request.status < 400) {
    data.forEach(movie => {
      const card = document.createElement('div');
      card.setAttribute('class', 'card');

      const h1 = document.createElement('h1');
      h1.textContent = movie.title;

      const p = document.createElement('p');
      movie.description = movie.description.substring(0, 300);
      p.textContent = `${movie.description}...`;

      container.appendChild(card);
      card.appendChild(h1);
      card.appendChild(p);
    });
  } else {
    const errorMessage = document.createElement('marquee');
    errorMessage.textContent = `Gah, it's not working!`;
    app.appendChild(errorMessage);
  }
}

request.send();
```

To make the page look a bit nicer replace the existing css code with the following:
```css
* {
  box-sizing: border-box
}

html {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  font-family: 'Dosis', sans-serif;
  line-height: 1.6;
  color: #666;
  background: #222222;
}

#root {
  max-width: 1200px;
  margin: 0 auto;
}

h1 {
  text-align: center;
  padding: 1.5rem 2.5rem;
  background-image: linear-gradient(120deg, #EBC12D 0%, #222222 100%);
  margin: 0 0 2rem 0;
  font-size: 1.5rem;
  color: white;
}

img {
  margin: 1rem auto;
  margin-top: 1.5rem;
  margin-left: 1rem	;
  max-width: 100%;
}

p {
  padding: 0 2.5rem 2.5rem;
  margin: 0;
}

.container {
  display: flex;
  flex-wrap: wrap;
}

.card {
  margin: 1rem;
  background: white;
  box-shadow: 2px 4px 25px rgba(0, 0, 0, .1);
  border-radius: 12px;
  overflow: hidden;
  transition: all .2s linear;
}

.card:hover {
  box-shadow: 2px 8px 45px rgba(0, 0, 0, .15);
  transform: translate3D(0, -2px, 0);
}

@media screen and (min-width: 600px) {
  .card {
    flex: 1 1 calc(50% - 2rem);
  }
}

@media screen and (min-width: 900px) {
  .card {
    flex: 1 1 calc(33% - 2rem);
  }
}

.card:nth-child(2n) h1 {
  background-image: linear-gradient(120deg, #EBC12D 0%, #222222 100%);
}


.card:nth-child(4n) h1 {
  background-image: linear-gradient(120deg, #EBC12D 0%, #222222 100%);
}

.card:nth-child(5n) h1 {
  background-image: linear-gradient(120deg, #EBC12D 0%, #222222 100%);
}


```


