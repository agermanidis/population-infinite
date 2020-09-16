# Identity Simulation on the Web With Puppeteer

![Puppeteer Logo](/images/puppeteer.png)

> [Puppeteer](https://github.com/GoogleChrome/puppeteer) is a [Node.js](https://nodejs.org/en/) library which provides a high-level API to control Chrome or Chromium

## Useful reference links:

* To get all the capabilities of Puppeteer, checkout [the readme,](https://github.com/GoogleChrome/puppeteer/blob/master/README.md), [the API docs](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md) or the nicely formatted [API reference](https://pptr.dev/).
* Check out [the official Puppeteer examples](https://github.com/GoogleChrome/puppeteer/tree/master/examples), or try them by going to [https://try-puppeteer.appspot.com/](https://try-puppeteer.appspot.com/) and clicking the menu under "Run an Example"
* For a good guide to dom selectors, check out the [Mozilla guide to CSS selectors.](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors) These are the same selectors you would use for selecting HTML elements with Puppeteer.
* To better understand async/await syntax: watch the [Coding train video on async/await](https://www.youtube.com/watch?v=XO77Fib9tSI)

## Useful tools:

* To run a puppeteer tester from the browser, check out [the browser based Try Puppeteer tool](https://try-puppeteer.appspot.com/).  This can be useful to get up and running if you don't feel like working with node on your computer, or want to demo something to someone by just providing a link.  
* Use [the Puppeteer Recorder](https://chrome.google.com/webstore/detail/puppeteer-recorder/djeegiggegleadkkbgopoonhjimgehda?hl=en) chrome extension to record actions when browsing and generate a Puppeteer script.

## Useful libraries:

* [Faker.js](https://github.com/marak/Faker.js/) is a tool for generating massive amounts of realistic fake data in Node.js and the browser.
* [Darius Kazemi's Corpora,](https://github.com/dariusk/corpora) described as "a collection of static corpora (plural of "corpus") that are potentially useful in the creation of weird internet stuff." . This can be used as source of words for generating content.

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
import puppeteer from 'puppeteer';

const browser = await puppeteer.launch();

// create a page
const page = await browser.newPage();

// navigate to google.com
await page.goto('https://google.com');
// capture a screenshot
await page.screenshot({path: 'google.png'});

// close the browser
await browser.close();
```

## Running the program 

### top level await

The above program makes use of top-level `await` syntax.  Traditionally with node.js, code that uses
`await` must be wrapped in an `async` function.  However, with Node version `v14.3.0` and above,
you can run it in the 'top level' by adding the flag --harmony-top-level-await` to your node.js calls.

### Run the program:

Now, in ther terminal, run the program with:

```sh
node --harmony-top-level-await program.js
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
// wait for the main search box to appear
await page.waitFor("#main");

// type some text into an element that matches the selector #main
await page.type('#main', 'Some text for main input');

// press `Enter` on the keyboard, triggering the search.
await page.keyboard.press('Enter');
```

In class, let's go over what is happening in these functions, and what [the query selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors) are doing.  Then we'll modify the program `program.js` to search google for some text, and press enter to search.

To test a query selector, open google chrome's developer tools, then a console, then enter:

```javascript
document.querySelector('#selector.goes[here]');
```

## Some Sample Programs

#### Infinitely following and watching youtube recommendations; watching each recommendation for 5 seconds:

```javascript
import puppeteer from 'puppeteer';

const browser = await puppeteer.launch({headless: false});

const page = await browser.newPage();

await page.setViewport({width: 1280, height: 720});

console.log('going to youtube')
await page.goto('https://www.youtube.com');

await page.click('form');

const searchBox = await page.waitForSelector('#search');

await page.type('#search', "I'm so scared :(");

await page.keyboard.press('Enter');

// wait for the link for the first video appear.
await page.waitForSelector('a#video-title');

console.log('found video, opening the first one.')

// click the link for the first video appear.
await page.click('a#video-title');

const watchDuration = 5;

while(true) {
  await page.waitFor('h1 yt-formatted-string');

  const videoTitle = await page.evaluate(function() {
    return document.querySelector('h1 yt-formatted-string').innerText;
  });

  console.log('watching video ' + videoTitle);

  // watch the video for the watchDuration seconds
  await page.waitFor(watchDuration * 1000);

  await page.waitForSelector('#related #items #contents a');

  await page.click('#related #items #contents a');

}
```

#### Opening facebook, manually logging in, and sending a message to the first person on facebook messenger:

```javascript

import puppeteer from 'puppeteer';

const browser = await puppeteer.launch({
  headless: false,
  slowMo: 10
});

const context = await browser.createIncognitoBrowserContext();
const page = await context.newPage();

await page.goto('https://www.facebook.com');

await page.setViewport({
  width: 1280,
  height: 720
});

console.log('open the browser window, and login to facebook:');

// wait for the facebook messenger link to appear.
const messengerLink = await page.waitForSelector('[data-testid=left_nav_item_Messenger]');

// let the page load
await page.waitForTimeout(3000);

// click the messenger link.
await messengerLink.click();

console.log('opened messenger link');

// wait for a chat to be opened.
await page.waitForSelector("[aria-label='New message']");

await page.keyboard.type('Hi!  Hows it going today?', {delay: 10});
await page.keyboard.press('Enter');

// wait 5 seconds before sending another message.
await page.waitForTimeout(5000);

await page.keyboard.type('Aw, ok.  Lets chat another day!', {delay: 20});
await page.keyboard.press('Enter');
```

#### Opening twitter, manually logging in, and posting a couple messages:

```javascript
import puppeteer from 'puppeteer';

const browser = await puppeteer.launch({
  headless: false,
  slowMo: 10
});

const context = await browser.createIncognitoBrowserContext();
const page = await context.newPage();

await page.goto('https://www.twitter.com');

await page.setViewport({
  width: 1280,
  height: 720
});

console.log('open the browser window, and login to twitter:');

// wait for the facebook messenger link to appear.
const messengerContainer = await page.waitForSelector('.DraftEditor-editorContainer');

console.log('found messenger container, clicking.');

await messengerContainer.click();
// const messengerLink = await page.waitForSelector('[data-testid=left_nav_item_Messenger]');

await page.keyboard.type('I love this site? But how do I use it?', {delay: 100});

const tweetButton = await page.waitForSelector('a[data-testid=addButton]');

await tweetButton.click();

// add another tweet dialog comes up.  lets wait a few seconds.
console.log('waiting 3 seconds');
await page.waitForTimeout(3000);

console.log('typing a second tweet');
await page.keyboard.type('Im tired.  lets try again tomorrow :', { delay: 100});

const tweetAgainButton = await page.waitForSelector('div[data-testid=tweetButton]')
await tweetAgainButton.click();
```