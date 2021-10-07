---
title: Setting up your own test automation environment
slug: Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment
tags:
  - Article
  - Automation
  - Beginner
  - Browser
  - CodingScripting
  - Learn
  - Testing
  - Tools
  - cross browser
  - selenium
---
<div><br>
{{LearnSidebar}}</div>

<div>{{PreviousMenu("Learn/Tools_and_testing/Cross_browser_testing/Automated_testing", "Learn/Tools_and_testing/Cross_browser_testing")}}</div>

<p>In this article, we will teach you how to install your own automation environment and run your own tests using Selenium/WebDriver and a testing library such as selenium-webdriver for Node. We will also look at how to integrate your local testing environment with commercial tools like the ones discussed in the previous article.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Familiarity with the core <a href="/en-US/docs/Learn/HTML">HTML</a>, <a href="/en-US/docs/Learn/CSS">CSS</a>, and <a href="/en-US/docs/Learn/JavaScript">JavaScript</a> languages; an idea of the high-level <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Introduction">principles of cross browser testing</a>, and <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Automated_testing">automated testing</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To show how to set up a Selenium testing environment locally and run tests with it, and how to integrate it with tools like LambdaTest, Sauce Labs, and BrowserStack.</td>
  </tr>
 </tbody>
</table>

<h2 id="Selenium">Selenium</h2>

<p><a href="https://www.seleniumhq.org/">Selenium</a> is the most popular browser automation tool. There are other ways, but the best way to use Selenium is via WebDriver, a powerful API that builds on top of Selenium and makes calls to a browser to automate it, carrying out actions such as "open this web page", "move over this element on the page", "click this link", "see whether the link opens this URL", etc. This is ideal for running automated tests.</p>

<p>How you install and use WebDriver depends on what programming environment you want to use to write and run your tests. Most popular environments have available a package or framework that will install WebDriver and the bindings required to communicate with WebDriver using this language, for example, Java, C#, Ruby, Python, JavaScript (Node), etc. See <a href="https://www.seleniumhq.org/docs/03_webdriver.jsp#setting-up-a-selenium-webdriver-project">Setting Up a Selenium-WebDriver Project</a> for more details of Selenium setups for different languages.</p>

<p>Different browsers require different drivers to allow WebDriver to communicate with and control them. See <a href="https://www.selenium.dev/downloads/">Platforms Supported by Selenium</a> for more information on where to get browser drivers from, etc.</p>

<p>We will cover writing and running Selenium tests using Node.js, as it is quick and easy to get started, and a more familiar environment for front end devs.</p>

<div class="note">
<p><strong>Note:</strong> If you want to find out how to use WebDriver with other server-side environments, also check out <a href="https://www.selenium.dev/downloads/">Platforms Supported by Selenium</a> for some useful links.</p>
</div>

<h3 id="Setting_up_Selenium_in_Node">Setting up Selenium in Node</h3>

<ol>
 <li>To start with, set up a new npm project, as discussed in <a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Automated_testing#setting_up_node_and_npm">Setting up Node and npm</a> in the last chapter. Call it something different, like <code>selenium-test</code>.</li>
 <li>Next, we need to install a framework to allow us to work with Selenium from inside Node. We are going to choose selenium's official <a href="https://www.npmjs.com/package/selenium-webdriver">selenium-webdriver</a>, as the documentation seems fairly up-to-date and it is well-maintained. If you want different options, <a href="https://webdriver.io/">webdriver.io</a> and <a href="https://nightwatchjs.org/">nightwatch.js</a> are also good choices. To install selenium-webdriver, run the following command, making sure you are inside your project folder:
  <pre class="brush: bash">npm install selenium-webdriver</pre>
 </li>
</ol>

<div class="note">
<p><strong>Note:</strong> It is still a good idea to follow these steps even if you previously installed selenium-webdriver and downloaded the browser drivers. You should make sure that everything is up-to-date.</p>
</div>

<p>Next, you need to download the relevant drivers to allow WebDriver to control the browsers you want to test. You can find details of where to get them from on the <a href="https://www.npmjs.com/package/selenium-webdriver">selenium-webdriver</a> page (see the table in the first section.) Obviously, some of the browsers are OS-specific, but we're going to stick with Firefox and Chrome, as they are available across all the main OSes.</p>

<ol>
 <li>Download the latest <a href="https://github.com/mozilla/geckodriver/releases/">GeckoDriver</a> (for Firefox) and <a href="https://chromedriver.storage.googleapis.com/index.html">ChromeDriver</a> drivers.</li>
 <li>Unpack them into somewhere fairly easy to navigate to, like the root of your home user directory.</li>
 <li>Add the <code>chromedriver</code> and <code>geckodriver</code> driver's location to your system <code>PATH</code> variable. This should be an absolute path from the root of your hard disk, to the directory containing the drivers. For example, if we were using a macOS machine, our user name was bob, and we put our drivers in the root of our home folder, the path would be <code>/Users/bob</code>.</li>
</ol>

<div class="note">
<p><strong>Note:</strong> Just to reiterate, the path you add to <code>PATH</code> needs to be the path to the directory containing the drivers, not the paths to the drivers themselves! This is a common mistake.</p>
</div>

<p>To set your <code>PATH</code> variable on a macOS system and on most Linux systems:</p>

<ol>
 <li>If you’re not already using the <code>bash</code> shell (for example, on macOS systems, the default is the <code>zsh</code> shell, not <code>bash</code>), switch to the <code>bash</code> shell:
  <pre class="brush: bash">exec bash</pre>
 </li>
 <li>Open your <code>.bash_profile</code> (or <code>.bashrc</code>) file (if you can't see hidden files, you'll need to display them, see <a href="https://ianlunn.co.uk/articles/quickly-showhide-hidden-files-mac-os-x-mavericks/">Show/Hide hidden files in Mac OS X</a> or <a href="https://askubuntu.com/questions/470837/how-to-show-hidden-folders-in-ubuntu-14-04">Show hidden folders in Ubuntu</a>).</li>
 <li>Paste the following into the bottom of your file (updating the path as it actually is on your machine):
  <pre class="brush: bash">#Add WebDriver browser drivers to PATH

export PATH=$PATH:/Users/bob</pre>
 </li>
 <li>Save and close this file, then restart your Terminal/command prompt to reapply your Bash configuration.</li>
 <li>Check that your new paths are in the <code>PATH</code> variable by entering the following into your terminal:
  <pre class="brush: bash">echo $PATH</pre>
 </li>
 <li>You should see it printed out in the terminal.</li>
</ol>

<p>To set your <code>PATH</code> variable on Windows, follow the instructions at <a href="http://windowsitpro.com/systems-management/how-can-i-add-new-folder-my-system-path">How can I add a new folder to my system path?</a></p>

<p>OK, let's try a quick test to make sure everything is working.</p>

<ol>
 <li>Create a new file inside your project directory called <code>google_test.js</code>:</li>
 <li>Give it the following contents, then save it:
  <pre class="brush: js">const webdriver = require('selenium-webdriver'),
    By = webdriver.By,
    until = webdriver.until;

const driver = new webdriver.Builder()
    .forBrowser('firefox')
    .build();

driver.get('http://www.google.com');

driver.findElement(By.name('q')).sendKeys('webdriver');

driver.sleep(1000).then(function() {
  driver.findElement(By.name('q')).sendKeys(webdriver.Key.TAB);
});

driver.findElement(By.name('btnK')).click();

driver.sleep(2000).then(function() {
  driver.getTitle().then(function(title) {
    if(title === 'webdriver - Google Search') {
      console.log('Test passed');
    } else {
      console.log('Test failed');
    }
    driver.quit();
  });
});

</pre>
 </li>
 <li>In terminal, make sure you are inside your project folder, then enter the following command:
  <pre class="brush: bash">node google_test</pre>
 </li>
</ol>

<p>You should see an instance of Firefox automatically open up! Google should automatically be loaded in a tab, "webdriver" should be entered in the search box, and the search button will be clicked. WebDriver will then wait for 2 seconds; the document title is then accessed, and if it is "webdriver - Google Search", we will return a message to claim the test is passed. WebDriver will then close down the Firefox instance and stop.</p>

<h2 id="Testing_in_multiple_browsers_at_once">Testing in multiple browsers at once</h2>

<p>There is also nothing to stop you running the test on multiple browsers simultaneously. Let's try this!</p>

<ol>
 <li>Create another new file inside your project directory called <code>google_test_multiple.js</code>. You can feel free to change the references to some of the other browsers we added, remove them, etc., depending on what browsers you have available to test on your operating system. You'll need to make sure you have the right browser drivers set up on your system. In terms of what string to use inside the <code>.forBrowser()</code> method for other browsers,  see the <a href="https://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/index_exports_Browser.html">Browser enum</a> reference page.</li>
 <li>Give it the following contents, then save it:
  <pre class="brush: js">const webdriver = require('selenium-webdriver'),
    By = webdriver.By,
    until = webdriver.until;

let driver_fx = new webdriver.Builder()
    .forBrowser('firefox')
    .build();

let driver_chr = new webdriver.Builder()
    .forBrowser('chrome')
    .build();

searchTest(driver_fx);
searchTest(driver_chr);

function searchTest(driver) {
  driver.get('http://www.google.com');
  driver.findElement(By.name('q')).sendKeys('webdriver');

  driver.sleep(1000).then(function() {
    driver.findElement(By.name('q')).sendKeys(webdriver.Key.TAB);
  });

  driver.findElement(By.name('btnK')).click();

  driver.sleep(2000).then(function() {
    driver.getTitle().then(function(title) {
      if(title === 'webdriver - Google Search') {
        console.log('Test passed');
      } else {
        console.log('Test failed');
      }
      driver.quit();
    });
  });

}</pre>
 </li>
 <li>In terminal, make sure you are inside your project folder, then enter the following command:
  <pre class="brush: bash">node google_test_multiple</pre>
 </li>
 <li>If you are using a Mac and do decide to test Safari, you might get an error message along the lines of "Could not create a session: You must enable the 'Allow Remote Automation' option in Safari's Develop menu to control Safari via WebDriver." If you get this, follow the given instruction and try again.</li>
</ol>

<p>So here we've done the test as before, except that this time we've wrapped it inside a function, <code>searchTest()</code>. We've created new browser instances for multiple browsers, then passed each one to the function so the test is performed on all three browsers!</p>

<p>Fun huh? Let's move on, look at the basics of WebDriver syntax, in a bit more detail.</p>

<h2 id="WebDriver_syntax_crash_course">WebDriver syntax crash course</h2>

<p>Let's have a look at a few key features of the webdriver syntax. For more complete details, you should consult the <a href="https://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/">selenium-webdriver JavaScript API reference</a> for a detailed reference, and the Selenium main documentation's <a href="https://www.seleniumhq.org/docs/03_webdriver.jsp">Selenium WebDriver</a> and <a href="https://www.seleniumhq.org/docs/04_webdriver_advanced.jsp">WebDriver: Advanced Usage</a> pages, which contain multiple examples to learn from written in different languages.</p>

<h3 id="Starting_a_new_test">Starting a new test</h3>

<p>To start up a new test, you need to include the <code>selenium-webdriver</code> module like this:</p>

<pre class="brush: js">const webdriver = require('selenium-webdriver'),
    By = webdriver.By,
    until = webdriver.until;</pre>

<p>Next, you need to create a new instance of a driver, using the <code>new webdriver.Builder()</code> constructor. This needs to have the <code>forBrowser()</code> method chained onto it to specify what browser you want to test with this builder, and the <code>build()</code> method to actually build it (see the <a href="https://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/index_exports_Builder.html">Builder class reference</a> for detailed information on these features).</p>

<pre class="brush: js">let driver = new webdriver.Builder()
    .forBrowser('firefox')
    .build();</pre>

<p>Note that it is possible to set specific configuration options for browsers to be tested, for example you can set a specific version and OS to test in the <code>forBrowser()</code> method:</p>

<pre class="brush: js">let driver = new webdriver.Builder()
    .forBrowser('firefox', '46', 'MAC')
    .build();</pre>

<p>You could also set these options using an environment variable, for example:</p>

<pre class="brush: bash">SELENIUM_BROWSER=firefox:46:MAC</pre>

<p>Let's create a new test to allow us to explore this code as we talk about it. Inside your selenium test project directory, create a new file called <code>quick_test.js</code>, and add the following code to it:</p>

<pre class="brush: js">var webdriver = require('selenium-webdriver'),
    By = webdriver.By,
    until = webdriver.until;

var driver = new webdriver.Builder()
    .forBrowser('firefox')
    .build();</pre>

<h3 id="Getting_the_document_you_want_to_test">Getting the document you want to test</h3>

<p>To load the page you actually want to test, you use the <code>get()</code> method of the driver instance you created earlier, for example:</p>

<pre class="brush: js">driver.get('http://www.google.com');</pre>

<div class="note">
<p><strong>Note:</strong> See the <a href="https://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/lib/webdriver_exports_WebDriver.html">WebDriver class reference</a> for details of the features in this section and the ones below it.</p>
</div>

<p>You can use any URL to point to your resource, including a <code>file://</code> URL to test a local document:</p>

<pre class="brush: js">driver.get('file:///Users/chrismills/git/learning-area/tools-testing/cross-browser-testing/accessibility/fake-div-buttons.html');</pre>

<p>or</p>

<pre class="brush: js">driver.get('http://localhost:8888/fake-div-buttons.html');</pre>

<p>But it is better to use a remote server location so the code is more flexible — when you start using a remote server to run your tests (see later on), your code will break if you try to use local paths. </p>

<p>Add this line to the bottom of <code>quick_test.js</code> now:</p>

<pre class="brush: js">driver.get('https://mdn.github.io/learning-area/tools-testing/cross-browser-testing/accessibility/native-keyboard-accessibility.html');</pre>

<h3 id="Interacting_with_the_document">Interacting with the document</h3>

<p>Now we've got a document to test, we need to interact with it in some way, which usually involves first selecting a specific element to test something about. You can <a href="https://www.seleniumhq.org/docs/03_webdriver.jsp#locating-ui-elements-webelements">select UI elements in many ways</a> in WebDriver, including by ID, class, element name, etc. The actual selection is done by the <code>findElement()</code> method, which accepts as a parameter a selection method. For example, to select an element by ID:</p>

<pre class="brush: js">const element = driver.findElement(By.id('myElementId'));</pre>

<p>One of the most useful ways to find an element by CSS — the  By.css method allows you to select an element using a CSS selector</p>

<p>Enter the following at the bottom of your <code>quick_test.js</code> code now:</p>

<pre class="brush: js">const button = driver.findElement(By.css('button:nth-of-type(1)'));</pre>

<h3 id="Testing_your_element">Testing your element</h3>

<p>There are many ways to interact with your web documents and elements on them. You can see useful common examples starting at <a href="https://www.seleniumhq.org/docs/03_webdriver.jsp#getting-text-values">Getting text values</a> on the WebDriver docs.</p>

<p>If we wanted to get the text inside our button, we could do this:</p>

<pre class="brush: js">button.getText().then(function(text) {
  console.log('Button text is \'' + text + '\'');
});</pre>

<p>Add this to <code>quick_test.js</code> now.</p>

<p>Making sure you are inside your project directory, try running the test:</p>

<pre class="brush: bash">node quick_test.js</pre>

<p>You should see the button's text label reported inside the console.</p>

<p>let's do something a bit more useful. delete the previous code entry, then add this line at the bottom instead:</p>

<pre class="brush: js">button.click();</pre>

<p>Try running your test again; the button will be clicked, and the <code>alert()</code> popup should appear. At least we know the button is working!</p>

<p>You can interact with the popup too. Add the following to the bottom of the code, and try testing it again:</p>

<pre class="brush: js">let alert = driver.switchTo().alert();

alert.getText().then(function(text) {
  console.log('Alert text is \'' + text + '\'');
});

alert.accept();</pre>

<p>Next, let's try entering some text into one of the form elements. Add the following code and try running your test again:</p>

<pre class="brush: js">const input = driver.findElement(By.id('name'));
input.sendKeys('Filling in my form');</pre>

<p>You can submit key presses that can't be represented by normal characters using properties of the <code>webdriver.Key</code> object. For example, above we used this construct to tab out of the form input before submitting it:</p>

<pre class="brush: js">driver.sleep(1000).then(function() {
  driver.findElement(By.name('q')).sendKeys(webdriver.Key.TAB);
});
</pre>

<h3 id="Waiting_for_something_to_complete">Waiting for something to complete</h3>

<p>There are times where you'll want to make WebDriver wait for something to complete before carrying on. For example if you load a new page, you'll want to wait for the page's DOM to finish loading before you try to interact with any of its elements, otherwise the test will likely fail.</p>

<p>In our <code>google_test.js</code> test for example, we included this block:</p>

<pre class="brush: js">driver.sleep(2000).then(function() {
  driver.getTitle().then(function(title) {
    if(title === 'webdriver - Google Search') {
      console.log('Test passed');
    } else {
      console.log('Test failed');
    }
  });
});</pre>

<p>The <code>sleep()</code> method accepts a value that specifies the time to wait in milliseconds — the method returns a promise that resolves at the end of that time, at which point the code inside the <code>then()</code> executes. In this case we get the title of the current page with the <code>getTitle()</code> method, then return a pass or fail message depending on what its value is.</p>

<p>We could add a <code>sleep()</code> method to our <code>quick_test.js</code> test too — try wrapping your last line of code in a block like this:</p>

<pre class="brush: js">driver.sleep(2000).then(function() {
  input.sendKeys('Filling in my form');
  input.getAttribute("value").then(function(value) {
    if(value !== '') {
      console.log('Form input editable');
    }
  });
});</pre>

<p>WebDriver will now wait for 2 seconds before filling in the form field. We then test whether its value got filled in (i.e. is not empty) by using <code>getAttribute()</code> to retrieve it's <code>value</code> attribute value, and print a message to the console if it is not empty.</p>

<div class="note">
<p><strong>Note:</strong> There is also a method called <code><a href="https://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/lib/webdriver_exports_WebDriver.html#wait">wait()</a></code>, which repeatedly tests a condition for a certain length of time, and then carries on executing the code. This also makes use of the <a href="https://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/lib/until.html">util library</a>, which defines common conditions to use along with <code>wait()</code>.</p>
</div>

<h3 id="Shutting_down_drivers_after_use">Shutting down drivers after use</h3>

<p>After you've finished running a test, you should shut down any driver instances you've opened, to make sure that you don't end up with loads of rogue browser instances open on your machine! This is done using the <code>quit()</code> method. Call this on your driver instance when you are finished with it. Add this line to the bottom of your <code>quick_test.js</code> test now:</p>

<pre class="brush: js">driver.quit();</pre>

<p>When you run it, you should now see the test execute and the browser instance shut down again after the test is complete. This is useful for not cluttering up your computer with loads of browser instances, especially if you have so many that it is causing the computer to slow down.</p>

<h2 id="Test_best_practices">Test best practices</h2>

<p>There has been a lot written about best practices for writing tests. You can find some good background information at <a href="https://www.seleniumhq.org/docs/06_test_design_considerations.jsp">Test Design Considerations</a>. In general, you should make sure that your tests are:</p>

<ol>
 <li>Using good locator strategies: When you are {{anch("Interacting with the document")}}, make sure that you use locators and page objects that are unlikely to change — if you have a testable element that you want to perform a test on, make sure that it has a stable ID, or position on the page that can be selected using a CSS selector, which isn't going to just change with the next site iteration. You want to make your tests as non-brittle as possible, i.e. they won't just break when something changes.</li>
 <li>Write atomic tests: Each test should test one thing only, making it easy to keep track of what test file is testing which criterion. As an example, the <code>google_test.js</code> test we looked at above is pretty good, as it just tests a single thing — whether the title of a search results page is set correctly. We could work on giving it a better name so it is easier to work out what it does if we add more google tests. Perhaps <code>results_page_title_set_correctly.js</code> would be slightly better?</li>
 <li>Write autonomous tests: Each test should work on it's own, and not depend on other tests to work.</li>
</ol>

<p>In addition, we should mention test results/reporting — we've been reporting results in our above examples using simple <code>console.log()</code> statements, but this is all done in JavaScript, so you can use whatever test running and reporting system you want, be it <a href="https://mochajs.org/">Mocha</a>, <a href="http://chaijs.com/">Chai</a>, or some other tool.</p>

<ol>
 <li>For example, try making a local copy of our <code><a href="https://github.com/mdn/learning-area/blob/master/tools-testing/cross-browser-testing/selenium/mocha_test.js">mocha_test.js</a></code> example inside your project directory. Put it inside a subfolder called <code>test</code>. This example uses a long chain of promises to run all the steps required in our test — the promise-based methods WebDriver uses need to resolve for it to work properly.</li>
 <li>Install the mocha test harness by running the following command inside your project directory:
  <pre class="brush: bash">npm install --save-dev mocha</pre>
 </li>
 <li>you can now run the test (and any others you put inside your <code>test</code> directory) using the following command:
  <pre class="brush: bash">npx mocha --no-timeouts</pre>
 </li>
 <li>You should include the <code>--no-timeouts</code> flag to make sure your tests don't end up failing because of Mocha's arbitrary timeout (which is 3 seconds).</li>
</ol>

<div class="note">
<p><strong>Note:</strong> <a href="https://github.com/saucelabs-sample-test-frameworks">saucelabs-sample-test-frameworks</a> contains several useful examples showing how to set up different combinations of test/assertion tools.</p>
</div>

<h2 id="Running_remote_tests">Running remote tests</h2>

<p>It turns out that running tests on remote servers isn't that much more difficult than running them locally. You just need to create your driver instance, but with a few more features specified, including the capabilities of the browser you want to test on, the address of the server, and the user credentials you need (if any) to access it.</p>

<h3 id="LambdaTest">LambdaTest</h3>

<p>Getting Selenium tests to run remotely on LambdaTest is very simple. The code you need should follow the pattern seen below.</p>

<p>Let's write an example:</p>

<ol>
 <li>Inside your project directory, create a new file called <code>lambdatest_google_test.js</code></li>
 <li>Give it the following contents:
  <pre class="brush: js">const webdriver = require('selenium-webdriver');
    By = webdriver.By,
    until = webdriver.until;

// username: Username can be found at automation dashboard
const USERNAME = '{username}';

// AccessKey:  AccessKey can be generated from automation dashboard or profile section
const KEY = '{accessKey}';

// gridUrl: gridUrl can be found at automation dashboard
const GRID_HOST = 'hub.lambdatest.com/wd/hub';

function searchTextOnGoogle() {
// Setup Input capabilities
    const capabilities = {
       platform: 'windows 10',
       browserName: 'chrome',
       version: '67.0',
       resolution: '1280x800',
       network: true,
       visual: true,
       console: true,
       video: true,
       name: 'Test 1', // name of the test
       build: 'NodeJS build' // name of the build
     };

// URL: https://{username}:{accessToken}@hub.lambdatest.com/wd/hub

const gridUrl = 'https://' + USERNAME + ':' + KEY + '@' + GRID_HOST;

// setup and build selenium driver object
     const driver = new webdriver.Builder()
     .usingServer(gridUrl)
     .withCapabilities(capabilities)
     .build();

// navigate to a url, search for a text and get title of page
driver.get('https://www.google.com/ncr').then(function() {
    driver.findElement(webdriver.By.name('q')).sendKeys('LambdaTest\n').then(function() {
         driver.getTitle().then(function(title) {
               setTimeout(function() {
               console.log(title);
               driver.quit();
               }, 5000);
            });
         });
      });
   }

searchTextOnGoogle();
</pre>
 </li>
 <li>Visit your <a href="https://www.lambdatest.com/selenium-automation">LambdaTest automation dashboard</a>, to fetch your LambdaTest's username and access key by clicking on the <strong>key </strong>icon on the top-right(see <em>Username and Access Keys</em>). Replace the <code>{username}</code> and <code>{accessKey}</code> placeholders in the code with your actual user name and access key values (and make sure you keep them secure).</li>
 <li>Run the below command in your terminal to execute your test:
  <pre class="brush: bash">node lambdatest_google_test</pre>
  The test will be sent to LambdaTest, and the output of your test will be reflected on your LambdaTest console.<br>
  If you wish to extract these results for reporting purpose from LambdaTest platform then you can do so by using <a href="https://www.lambdatest.com/blog/lambdatest-launches-api-for-selenium-automation/">LambdaTest restful API</a>.</li>
 <li>Now if you go to your <a href="https://www.lambdatest.com/selenium-automation">LambdaTest Automation dashboard</a>, you'll see your test listed; from here you'll be able to see videos, screenshots, and other such data.<br>
  <a href="https://www.lambdatest.com/blog/wp-content/uploads/2019/02/Automation-logs-1.jpg"><img alt="LambdaTest Automation Dashboard" src="automation-logs-1.jpg"></a>You can retrieve network, command, exception, and Selenium logs for every test within your test build. You will also find a video recording of your Selenium script execution.</li>
</ol>

<div class="note">
<p><strong>Note:</strong> The <em>HELP</em>  button on LambdaTest Automation Dashboard will provide you with an ample amount of information to help you get started with LambdaTest automation. You can also follow our documentation about <a href="https://www.lambdatest.com/support/docs/quick-guide-to-run-node-js-tests-on-lambdatest-selenium-grid/">running first Selenium script in Node JS</a>.</p>
</div>

<div class="note">
<p><strong>Note:</strong> If you don't want to write out the capabilities objects for your tests by hand, you can generate them using the <a href="https://www.lambdatest.com/capabilities-generator/">Selenium Desired Capabilities Generator</a>. </p>
</div>

<h4 id="Filling_in_test_details_on_LambdaTest_programmatically">Filling in test details on LambdaTest programmatically</h4>

<p>When executing numerous automation tests, marking their status as passed or failed makes the task a lot easier.</p>

<ol>
 <li>Use the below command for marking a status as <strong>passed</strong> on LambdaTest.

  <pre class="brush: bash">driver.executeScript("lambda-status=passed");</pre>
 </li>
 <li>
  <p>Use the below command for marking a status as <strong>failed</strong> on LambdaTest.</p>

  <pre class="brush: bash">driver.executeScript("lambda-status=failed");</pre>
 </li>
</ol>

<h3 id="BrowserStack">BrowserStack</h3>

<p>Getting Selenium tests to run remotely on BrowserStack is easy. The code you need should follow the pattern seen below.</p>

<p>Let's write an example:</p>

<ol>
 <li>Inside your project directory, create a new file called <code>bstack_google_test.js</code>.</li>
 <li>Give it the following contents:
  <pre class="brush: js">const webdriver = require('selenium-webdriver'),
    By = webdriver.By,
    until = webdriver.until;

// Input capabilities
let capabilities = {
   'browserName' : 'Firefox',
   'browser_version' : '56.0 beta',
   'os' : 'OS X',
   'os_version' : 'Sierra',
   'resolution' : '1280x1024',
   'browserstack.user' : 'YOUR-USER-NAME',
   'browserstack.key' : 'YOUR-ACCESS-KEY',
   'browserstack.debug' : 'true',
   'build' : 'First build'
};

let driver = new webdriver.Builder().
  usingServer('http://hub-cloud.browserstack.com/wd/hub').
  withCapabilities(capabilities).
  build();

driver.get('http://www.google.com');
driver.findElement(By.name('q')).sendKeys('webdriver');

driver.sleep(1000).then(function() {
  driver.findElement(By.name('q')).sendKeys(webdriver.Key.TAB);
});

driver.findElement(By.name('btnK')).click();

driver.sleep(2000).then(function() {
  driver.getTitle().then(function(title) {
    if(title === 'webdriver - Google Search') {
      console.log('Test passed');
    } else {
      console.log('Test failed');
    }
  });
});

driver.quit();</pre>
 </li>
 <li>From your <a href="https://www.browserstack.com/automate">BrowserStack automation dashboard</a>, get your user name and access key (see <em>Username and Access Keys</em>). Replace the <code>YOUR-USER-NAME</code> and <code>YOUR-ACCESS-KEY</code> placeholders in the code with your actual user name and access key values (and make sure you keep them secure).</li>
 <li>Run your test with the following command:
  <pre class="brush: bash">node bstack_google_test</pre>
  The test will be sent to BrowserStack, and the test result will be returned to your console. This shows the importance of including some kind of result reporting mechanism!</li>
 <li>Now if you go back to the <a href="https://www.browserstack.com/automate">BrowserStack automation dashboard</a> page, you'll see your test listed:<br>
  <img alt="" src="bstack_automated_results.png"></li>
</ol>

<p>If you click on the link for your test, you'll get to a new screen where you will be able to see a video recording of the test, and multiple detailed logs of information pertaining to it.</p>

<div class="note">
<p><strong>Note:</strong> The <em>Resources</em> menu option on the Browserstack automation dashboard contains a wealth of useful information on using it to run automated tests. See <a href="https://www.browserstack.com/automate/node">Node JS Documentation for writing automate test scripts in Node JS</a> for the node-specific information. Explore the docs to find out all the useful things BrowserStack can do.</p>
</div>

<div class="note">
<p><strong>Note:</strong> If you don't want to write out the capabilities objects for your tests by hand, you can generate them using the generators embedded in the docs. See <a href="https://www.browserstack.com/automate/node#run-tests-on-mobile">Run tests on mobile browsers</a> and <a href="https://www.browserstack.com/automate/node#setting-os-and-browser">Run tests on desktop browsers</a>.</p>
</div>

<h4 id="Filling_in_BrowserStack_test_details_programmatically">Filling in BrowserStack test details programmatically</h4>

<p>You can use the BrowserStack REST API and some other capabilities to annotate your test with more details, such as whether it passed, why it passed, what project the test is part of, etc. BrowserStack doesn't know these details by default!</p>

<p>Let's update our <code>bstack_google_test.js</code> demo, to show how these features work:</p>

<ol>
 <li>First, we'll need to import the node request module, so we can use it to send requests to the REST API. Add the following line at the very top of your code:
  <pre class="brush: js">const request = require("request");</pre>
 </li>
 <li>Now we'll update our <code>capabilities</code> object to include a project name — add the following line before the closing curly brace, remembering to add a comma at the end of the previous line (you can vary the build and project names to organize the tests in different windows in the BrowserStack automation dashboard):
  <pre class="brush: js">'project' : 'Google test 2'</pre>
 </li>
 <li>Next we need to access the <code>sessionId</code> of the current session, so we know where to send the request (the ID is included in the request URL, as you'll see later). Include the following lines just below the block that creates the <code>driver</code> object (<code>let driver ...</code>) :
  <pre class="brush: js">let sessionId;

driver.session_.then(function(sessionData) {
    sessionId = sessionData.id_;
});</pre>
 </li>
 <li>Finally, update the <code>driver.sleep(2000)</code> ... block near the bottom of the code to add REST API calls (again, replace the <code>YOUR-USER-NAME</code> and <code>YOUR-ACCESS-KEY</code> placeholders in the code with your actual user name and access key values):
  <pre class="brush: js">driver.sleep(2000).then(function() {
  driver.getTitle().then(function(title) {
    if(title === 'webdriver - Google Search') {
      console.log('Test passed');
      request({uri: "https://YOUR-USER-NAME:YOUR-ACCESS-KEY@www.browserstack.com/automate/sessions/" + sessionId + ".json", method:"PUT", form:{"status":"passed","reason":"Google results showed correct title"}});
    } else {
      console.log('Test failed');
      request({uri: "https://YOUR-USER-NAME:YOUR-ACCESS-KEY@www.browserstack.com/automate/sessions/" + sessionId + ".json", method:"PUT", form:{"status":"failed","reason":"Google results showed wrong title"}});
    }
  });
});</pre>
 </li>
</ol>

<p>These are fairly intuitive — once the test completes, we send an API call to BrowserStack to update the test with a passed or failed status, and a reason for the result.</p>

<p>If you now go back to your <a href="https://www.browserstack.com/automate">BrowserStack automation dashboard</a> page, you should see your test session available, as before, but with the updated data attached to it:</p>

<p><img alt="" src="bstack_custom_results.png"></p>

<h3 id="Sauce_Labs">Sauce Labs</h3>

<p>Getting Selenium tests to run remotely on Sauce Labs is also very simple, and very similar to BrowserStack albeit with a few syntactic differences. The code you need should follow the pattern seen below.</p>

<p>Let's write an example:</p>

<ol>
 <li>Inside your project directory, create a new file called <code>sauce_google_test.js</code>.</li>
 <li>Give it the following contents:
  <pre class="brush: js">const webdriver = require('selenium-webdriver'),
    By = webdriver.By,
    until = webdriver.until,
    username = "YOUR-USER-NAME",
    accessKey = "YOUR-ACCESS-KEY";

let driver = new webdriver.Builder()
    .withCapabilities({
      'browserName': 'chrome',
      'platform': 'Windows XP',
      'version': '43.0',
      'username': username,
      'accessKey': accessKey
    })
    .usingServer("https://" + username + ":" + accessKey +
          "@ondemand.saucelabs.com:443/wd/hub")
    .build();

driver.get('http://www.google.com');

driver.findElement(By.name('q')).sendKeys('webdriver');

driver.sleep(1000).then(function() {
  driver.findElement(By.name('q')).sendKeys(webdriver.Key.TAB);
});

driver.findElement(By.name('btnK')).click();

driver.sleep(2000).then(function() {
  driver.getTitle().then(function(title) {
    if(title === 'webdriver - Google Search') {
      console.log('Test passed');
    } else {
      console.log('Test failed');
    }
  });
});

driver.quit();</pre>
 </li>
 <li>From your <a href="https://saucelabs.com/beta/user-settings">Sauce Labs user settings</a>, get your user name and access key. Replace the <code>YOUR-USER-NAME</code> and <code>YOUR-ACCESS-KEY</code> placeholders in the code with your actual user name and access key values (and make sure you keep them secure).</li>
 <li>Run your test with the following command:
  <pre class="brush: bash">node sauce_google_test</pre>
  The test will be sent to Sauce Labs, and the test result will be returned to your console. This shows the importance of including some kind of result reporting mechanism!</li>
 <li>Now if you go to your <a href="https://saucelabs.com/beta/dashboard/tests">Sauce Labs Automated Test dashboard</a> page, you'll see your test listed; from here you'll be able to see videos, screenshots, and other such data.<br>
  <img alt="" src="sauce_labs_automated_test.png"></li>
</ol>

<div class="note">
<p><strong>Note:</strong> Sauce Labs' <a href="https://wiki.saucelabs.com/display/DOCS/Platform+Configurator/#/">Platform Configurator</a> is a useful tool for generating capability objects to feed to your driver instances, based on what browser/OS you want to test on.</p>
</div>

<div class="note">
<p><strong>Note:</strong> for more useful details on testing with Sauce Labs and Selenium, check out <a href="https://wiki.saucelabs.com/display/DOCS/Getting+Started+with+Selenium+for+Automated+Website+Testing">Getting Started with Selenium for Automated Website Testing</a>, and <a href="https://wiki.saucelabs.com/display/DOCS/Instant+Selenium+Node.js+Tests">Instant Selenium Node.js Tests</a>.</p>
</div>

<h4 id="Filling_in_Sauce_Labs_test_details_programmatically">Filling in Sauce Labs test details programmatically</h4>

<p>You can use the Sauce Labs API to annotate your test with more details, such as whether it passed, the name of the test, etc. Sauce Labs doesn't know these details by default!</p>

<p>To do this, you need to:</p>

<ol>
 <li>Install the Node Sauce Labs wrapper using the following command (if you've not already done it for this project):
  <pre class="brush: bash">npm install saucelabs --save-dev</pre>
 </li>
 <li>Require saucelabs — put this at the top of your <code>sauce_google_test.js</code> file, just below the previous variable declarations:
  <pre class="brush: js">const SauceLabs = require('saucelabs');</pre>
 </li>
 <li>Create a new instance of SauceLabs, by adding the following just below that:
  <pre class="brush: js">let saucelabs = new SauceLabs({
    username : "YOUR-USER-NAME",
    password : "YOUR-ACCESS-KEY"
});</pre>
  Again, replace the <code>YOUR-USER-NAME</code> and <code>YOUR-ACCESS-KEY</code> placeholders in the code with your actual user name and access key values (note that the saucelabs npm package rather confusingly uses <code>password</code>, not <code>accessKey</code>). Since you are using these twice now, you may want to create a couple of helper variables to store them in.</li>
 <li>Below the block where you define the <code>driver</code> variable (just below the <code>build()</code> line), add the following block — this gets the correct driver <code>sessionID</code> that we need to write data to the job (you can see it action in the next code block):
  <pre class="brush: js">driver.getSession().then(function (sessionid){
      driver.sessionID = sessionid.id_;
});</pre>
 </li>
 <li>Finally, replace the <code>driver.sleep(2000)</code> ... block near the bottom of the code with the following:
  <pre class="brush: js">driver.sleep(2000).then(function() {
  driver.getTitle().then(function(title) {
    if(title === 'webdriver - Google Search') {
      console.log('Test passed');
      let testPassed = true;
    } else {
      console.log('Test failed');
      let testPassed = false;
    }

    saucelabs.updateJob(driver.sessionID, {
      name: 'Google search results page title test',
      passed: testPassed
    });
  });
});</pre>
 </li>
</ol>

<p>Here we've set a <code>testPassed</code> variable to <code>true</code> or <code>false</code> depending on whether the test passed or fails, then we've used the <code>saucelabs.updateJob()</code> method to update the details.</p>

<p>If you now go back to your <a href="https://saucelabs.com/beta/dashboard/tests">Sauce Labs Automated Test dashboard</a> page, you should see your new job now has the updated data attached to it:</p>

<p><img alt="" src="sauce_labs_updated_job_info.png"></p>

<h3 id="Your_own_remote_server">Your own remote server</h3>

<p>If you don't want to use a service like Sauce Labs or BrowserStack, you can always set up your own remote testing server. Let's look at how to do this.</p>

<ol>
 <li>The Selenium remote server requires Java to run. Download the latest JDK for your platform from the <a href="https://www.oracle.com/technetwork/java/javase/downloads/index.html">Java SE downloads page</a>. Install it when it is downloaded.</li>
 <li>Next, download the latest <a href="https://selenium-release.storage.googleapis.com/index.html">Selenium standalone server</a> — this acts as a proxy between your script and the browser drivers. Choose the latest stable version number (i.e. not a beta), and from the list choose a file starting with "selenium-server-standalone". When this has downloaded, put it in a sensible place, like in your home directory. If you've not already added the location to your <code>PATH</code>, do so now (see the {{anch("Setting up Selenium in Node")}} section).</li>
 <li>Run the standalone server by entering the following into a terminal on your server computer
  <pre class="brush: bash">java -jar selenium-server-standalone-3.0.0.jar</pre>
  (update the <code>.jar</code> filename) so it matches exactly what file you've got.</li>
 <li>The server will run on <code><a href="http://localhost:4444/wd/hub">http://localhost:4444/wd/hub</a></code> — try going there now to see what you get.</li>
</ol>

<p>Now we've got the server running, let's create a demo test that will run on the remote selenium server.</p>

<ol>
 <li>Create a copy of your <code>google_test.js</code> file, and call it <code>google_test_remote.js</code>; put it in your project directory.</li>
 <li>Update the second code block (which starts with <code>let driver = ...</code>) like so
  <pre class="brush: js">let driver = new webdriver.Builder()
    .forBrowser('firefox')
    .usingServer('http://localhost:4444/wd/hub')
    .build();</pre>
 </li>
 <li>Run your test, and you should see it run as expected; this time however you will be running it on the standalone server:
  <pre class="brush: bash">node google_test_remote.js</pre>
 </li>
</ol>

<p>So this is pretty cool. We have tested this locally, but you could set this up on just about any server along with the relevant browser drivers, and then connect your scripts to it using the URL you choose to expose it at.</p>

<h2 id="Integrating_Selenium_with_CI_tools">Integrating Selenium with CI tools</h2>

<p>As another point, it is also possible to integrate Selenium and related tools like LambdaTest, and Sauce Labs with continuous integration (CI) tools — this is useful, as it means you can run your tests via a CI tool, and only commit new changes to your code repository if the tests pass.</p>

<p>It is out of scope to look at this area in detail in this article, but we'd suggest getting started with Travis CI — this is probably the easiest CI tool to get started with and has good integration with web tools like GitHub and Node.</p>

<p>To get started, see for example:</p>

<ul>
 <li><a href="https://docs.travis-ci.com/user/for-beginners">Travis CI for complete beginners</a></li>
 <li><a href="https://docs.travis-ci.com/user/languages/javascript-with-nodejs/">Building a Node.js project</a> (with Travis)</li>
 <li><a href="https://www.lambdatest.com/support/docs/travis-ci-with-lambdatest/">Using LambdaTest with Travis CI</a></li>
 <li><a href="https://www.lambdatest.com/support/docs/circleci-integration-with-lambdatest/">Using LambdTest with CircleCI</a></li>
 <li><a href="https://www.lambdatest.com/support/docs/jenkins-with-lambdatest/">Using LambdaTest with Jenkins</a></li>
 <li><a href="https://docs.travis-ci.com/user/sauce-connect/">Using Sauce Labs with Travis CI</a></li>
</ul>

<div class="note">
<p><strong>Note:</strong> If you wish to perform continuous testing with <strong>codeless automation</strong> then you can use <a href="https://endtest.io">Endtest</a> or <a href="https://testingbot.com">TestingBot</a>.</p>
</div>

<h2 id="Summary">Summary</h2>

<p>This module should have proven fun, and should have given you enough of an insight into writing and running automated tests for you to get going with writing your own automated tests.</p>

<p>{{PreviousMenu("Learn/Tools_and_testing/Cross_browser_testing/Automated_testing", "Learn/Tools_and_testing/Cross_browser_testing")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Introduction">Introduction to cross browser testing</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Testing_strategies">Strategies for carrying out testing</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/HTML_and_CSS">Handling common HTML and CSS problems</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/JavaScript">Handling common JavaScript problems</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Accessibility">Handling common accessibility problems</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Feature_detection">Implementing feature detection</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Automated_testing">Introduction to automated testing</a></li>
 <li><a href="/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment">Setting up your own test automation environment</a></li>
</ul>