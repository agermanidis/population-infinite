# Identity Simulation on the Web With Puppeteer

![Puppeteer Logo](/images/puppeteer.png)

> [Puppeteer](https://github.com/GoogleChrome/puppeteer) is a [Node.js](https://nodejs.org/en/) library which provides a high-level API to control Chrome or Chromium

## Prerequisites

* [Download and install Node.js](https://nodejs.org/en/)
* Install a code editor, [Visual Studio Code](https://code.visualstudio.com/) is **strongly** recommended; it can be downloaded [here.](https://code.visualstudio.com/)


## What is node.js?

Puppeteer runs in Node.js, which is an environment to run **javascript outside** of the **browser.**

To try it out, create a file called `test_program.js` with the following contents:

```javascript
for(let i = 0; i < 5; i++) {
  console.log(i);
}
```

In the terminal on Mac, or Powershell on Windows, tun the program with:

```sh
node test_program.js
```

## Brief introduction to the terminal

[The terminal](https://macpaw.com/how-to/use-terminal-on-mac) is what is used to run node.js programs, and correspondingly node.js.  The easiest way to open it, on Mac, is to press spotlight search on the top right, and type "Terminal".  On Windows, open [Powershell.](https://www.isunshare.com/windows-10/5-ways-to-open-windows-powershell-in-windows-10.html)

Some basic terminal commands useful for this class:

```sh
# print what the current directory is
pwd

# make a directory with directory name 'directory_name'
mkdir directory_name

# list the contents of the current directory
ls
```

## To get started for this class

Run the following commands in your terminal:

```sh
# print what the current directory is
pwd

# make a directory with directory name 'puppeteer_demos'
mkdir puppeteer_demos

# go intot he puppeteer_demos directory
cd puppeteer_demos

# install the puppeteer node.js library
npm i puppeteer
```

**Now open the directory puppeteer_demos you created above in Visual Studio Code**

## Our first Puppeteer Program

Lets create our first program in Puppeteer.  In a file named `program.js`  add the following code:

```javascript
const puppeteer = require('puppeteer');

async function program() {
  // launch puppeteer
  const browser = await puppeteer.launch();

  // create a page
  const page = await browser.newPage();

  // navigate to google.com
  await page.goto('https://google.com');
  // capture a screenshot
  await page.screenshot({path: 'google.png'});

  // close the browser
  await browser.close();
}

program();
```

Now, in ther terminal, run the program with:

```sh
node program.js
```

You should have a screenshot of google's homepage in the same folder this program was running.

We'll go over in class what is going on in this program, starting with `require` to import node modules, then a brief overview of `async`/`await`, then the basic `puppeteer` commands.

## Puppeteer Commands

The best way to get familiar with puppeteer is to open its [API reference.](https://pptr.dev)

To launch a Puppeteer Browser:

```javascript
const browser = await puppeteer.launch();
```

Puppeteer runs headless by default, but can be configured to run full (non-headless) Chrome or Chromium.  To run with Chromium:

```javascript
const browser = await puppeteer.launch({headless: false});
```

To create a page, call `browser.newPage()`:

```javascript
const browser = await puppeteer.launch({headless: false});

const page = await browser.newPage();
```

Most actions will be performed using the `page` that's created above. For a full list of actions available to the page, refer to the [Page API documentation.](https://pptr.dev/#?product=Puppeteer&version=v1.19.0&show=api-class-page)

To navigate, call `page.goto(url)`:

```javascript
const browser = await puppeteer.launch({headless: false});
const page = await browser.newPage();

await page.goto('https://google.com');
```

Some basic actions that can be done with a page:

```javascript
// type some text into an element that matches the selector #main
await page.type('#main', 'Some text for main input');

// click an element that matches the selector .active
await page.click('.active');

// wait for an element to appear that matches the selector  'input[type="text"]'
await page.waitFor('input[type="text"]');

// type a key on the keybaord
await page.keyboard.press("Enter");
```

In class, let's go over what is happening in these functions, and what [the query selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors) are doing.  Then we'll modify the program `program.js` to search google for some text, and press enter to search.

To test a query selector, open google chrome's developer tools, then a console, then enter:

```javascript
document.querySelector('#selector.goes[here]');
```

## Some Sample Programs

#### Infinitely following and watching youtube recommendations; watching each recommendation for 5 seconds:

```javascript
const puppeteer = require('puppeteer');

async function program() {
  const browser = await puppeteer.launch({headless: false, slowMo: 10});

  const page = await browser.newPage();

  await page.setViewport({width: 1280, height: 720});

  console.log('going to youtube')
  await page.goto('https://www.youtube.com');

  await page.type('#search', "I'm so scared :(");

  await page.keyboard.press('Enter');

  // wait for the link for the first video appear.
  await page.waitFor('a#video-title');
  // click the link for the first video appear.
  await page.click('a#video-title');

  const watchDuration = 5;

  while(true) {
    const videoTitle = await page.evaluate(function() {
      return document.querySelector('h1 yt-formatted-string').innerText;
    });

    console.log('watching video ' + videoTitle);

    // watch the video for the watchDuration seconds
    await page.waitFor(watchDuration * 1000);

    await page.waitFor('#related #items #contents a');

    await page.click('#related #items #contents a');

  }
}

program();
```

#### Logging into facebook and posting about wanting a cat.  The type of cat is randomly selected:

```javascript
const puppeteer = require('puppeteer');

function randomElementFromArray(array) {
  const randomIndex = Math.floor(Math.random() * array.length);

  return array[randomIndex];
}

async function loginToFacebook(page, email, password) {
  await page.goto('https://www.facebook.com');

  // enter email address and password
  await page.type('input[type="email"]', email);
  await page.type('input[type="password"]', password);

  await page.click('input[type="submit"]');

  // give page time to load
  await page.waitFor(5000);

  // click on page to get rid of message
  await page.mouse.click(1000, 1000);
}

async function program() {
  const browser = await puppeteer.launch({
    headless: false,
    slowMo: 10
  });

  const context = await browser.createIncognitoBrowserContext();
  const page = await context.newPage();

  await page.setViewport({
    width: 1280,
    height: 720
  });

  const email = "PUT_A_FACEBOOK_EMAIL_HERE";
  const password = "PUT_THE_FACEBOOK_PASSWORD_HERE";

  await loginToFacebook(page, email, password);

  const postBoxSelector = 'textarea';
  await page.waitFor(postBoxSelector);

  // open the post box
  console.log('clicking to open post box');
  await page.click(postBoxSelector);

  // select a random cat to want
  const cats = ["Kurilian Bobtail", "LaPerm", "Lykoi", "Maine Coon", "Manx", "Mekong Bobtail", "Minskin", "Munchkin"]
  const randomCat = randomElementFromArray(cats)

  console.log('posting about a cat ' + randomCat);
  await page.keyboard.type("I want a " + randomCat + ' cat.');;

  console.log('clicking to post');
  // click the post button
  await page.waitFor('[data-testid="react-composer-post-button"]');
  await page.click('[data-testid="react-composer-post-button"]');
}

program();
```

#### Logging onto Facebook and sending a message to the first person on facebook messenger:

```javascript

const puppeteer = require('puppeteer');

function randomElementFromArray(array) {
  const randomIndex = Math.floor(Math.random() * array.length);

  return array[randomIndex];
}

async function loginToFacebook(page, email, password) {
  await page.goto('https://www.facebook.com');

  // enter email address and password
  await page.type('input[type="email"]', email);
  await page.type('input[type="password"]', password);

  await page.click('input[type="submit"]');

  // give page time to load
  await page.waitFor(5000);

  // click on page to get rid of message
  await page.mouse.click(1000, 1000);
}

async function program() {
  const browser = await puppeteer.launch({
    headless: false,
    slowMo: 10
  });

  const context = await browser.createIncognitoBrowserContext();
  const page = await context.newPage();

  await page.setViewport({
    width: 1280,
    height: 720
  });

  const email = "PUT_A_FACEBOOK_EMAIL_HERE";
  const password = "PUT_THE_FACEBOOK_PASSWORD_HERE";

  await loginToFacebook(page, email, password);

  console.log('opening messenger');
  await page.click('a[data-testid="left_nav_item_Messenger"]');

  // wait for 2 seconds
  await page.waitFor(2000);

  console.log('typing a message');
  await page.keyboard.type('Hey!  so nice to see you on here :)');

  console.log('pressing enter');
  await page.keyboard.press('Enter');
}

program();
```