# References
*[bittersweetryan/jasmine-jquery](https://github.com/bittersweetryan/jasmine-jquery)    
*[Loading Fixtures - slide show](https://bittersweetryan.github.io/jasmine-presentation/#slide-71)   
*[Jasmine : Fixture could not be loaded](https://stackoverflow.com/questions/35498059/jasmine-fixture-could-not-be-loaded)      



# jasmine-jquery

jasmine-jquery provides two extensions for [Jasmine](http://pivotal.github.com/jasmine/) JavaScript Testing Framework:
  
- a set of custom matchers for jQuery framework
- an API for handling HTML, CSS, and JSON fixtures in your specs

## Installation

Simply download _jasmine-jquery.js_ from [here](https://raw.github.com/velesin/jasmine-jquery/master/lib/jasmine-jquery.js) and include it in your Jasmine's test runner file (or add it to _jasmine.yml_ file if you're using Ruby with [jasmine-gem](http://github.com/pivotal/jasmine-gem)). Remember to include also jQuery library as jasmine-jquery relies on it.

For Ruby on Rails I recommend to comply with the standard RSpec and Jasmine frameworks dir structure and keep your tests in `spec/javascripts/` dir. I put jasmine-jquery (and other libraries like jasmine-ajax) into `spec/javascripts/helpers` dir (so they are automatically loaded) and fixtures into `spec/javascripts/fixtures` dir.

## jQuery matchers

jasmine-jquery provides following custom matchers (in alphabetical order):

- `toBe(jQuerySelector)`
  - e.g. `expect($('<div id="some-id"></div>')).toBe('div')`
  - e.g. `expect($('<div id="some-id"></div>')).toBe('div#some-id')`
- `toBeChecked()`
  - only for tags that have checked attribute
  - e.g. `expect($('<input type="checkbox" checked="checked"/>')).toBeChecked()` 
- `toBeEmpty()`
  - Checks for child DOM elements or text.
- `toBeHidden()`
  
  Elements can be considered hidden for several reasons:
    - They have a CSS `display` value of `none`.
    - They are form elements with `type` equal to `hidden`.
    - Their `width` and `height` are explicitly set to `0`.
    - An ancestor element is hidden, so the element is not shown on the page.
- `toHaveCss(css)`
  - e.g. `expect($('<div style="display: none; margin: 10px;"></div>')).toHaveCss({display: "none", margin: "10px"})`
  - e.g. `expect($('<div style="display: none; margin: 10px;"></div>')).toHaveCss({margin: "10px"})`
- `toBeSelected()`
  - only for tags that have selected attribute
  - e.g. `expect($('<option selected="selected"></option>')).toBeSelected()`
- `toBeVisible()`
  - Elements are considered visible if they consume space in the document. Visible elements have a width or height that is greater than zero.
- `toContain(jQuerySelector)`
  - e.g. `expect($('<div><span class="some-class"></span></div>')).toContain('span.some-class')`
- `toExist()`
- `toHaveAttr(attributeName, attributeValue)`
  - attribute value is optional, if omitted it will check only if attribute exists
- `toHaveProp(propertyName, propertyValue)`
  - property value is optional, if omitted it will check only if property exists
- `toHaveBeenTriggeredOn(selector)`
  - if event has been triggered on `selector` (see "Event Spies", below)
- `toHaveBeenTriggered()`
  - if event has been triggered on `selector` (see "Event Spies", below)
- `toHaveBeenPreventedOn(selector)`
  - if event has been prevented on `selector` (see "Event Spies", below)
- `toHaveBeenPrevented()`
  - if event has been prevented on `selector` (see "Event Spies", below)
- `toHaveClass(className)`
  - e.g. `expect($('<div class="some-class"></div>')).toHaveClass("some-class")`  
- `toHaveData(key, value)`
  - value is optional, if omitted it will check only if an entry for that key exists
- `toHaveHtml(string)`
  - e.g. `expect($('<div><span></span></div>')).toHaveHtml('<span></span>')`
- `toContainHtml(string)`
  - e.g. `expect($('<div><ul></ul><h1>header</h1></div>')).toContainHtml('<ul></ul>')`
- `toHaveId(id)`
  - e.g. `expect($('<div id="some-id"></div>')).toHaveId("some-id")`
- `toHaveText(string)`
  - accepts a String or regular expression
  - e.g. `expect($('<div>some text</div>')).toHaveText('some text')`
- `toHaveValue(value)`
  - only for tags that have value attribute
  - e.g. `expect($('<input type="text" value="some text"/>')).toHaveValue('some text')`
- `toBeDisabled()`
  - e.g. `expect('<input type='submit' disabled='disabled'/>').toBeDisabled()`
- `toBeFocused()`
  - e.g. `expect($('<input type='text' />').focus()).toBeFocused()`
- `toHandle(eventName)`
  - e.g. `expect($form).toHandle("submit")`
- `toHandleWith(eventName, eventHandler)`
  - e.g. `expect($form).toHandleWith("submit", yourSubmitCallback)`
  
The same as with standard Jasmine matchers, all of above custom matchers may be inverted by using `.not` prefix, e.g.:

    expect($('<div>some text</div>')).not.toHaveText(/other/)

## HTML Fixtures

The Fixture module of jasmine-jquery allows you to load HTML content to be used by your tests. The overall workflow is like follows:

In _myfixture.html_ file:

    <div id="my-fixture">some complex content here</div>
    
Inside your test:

    loadFixtures('myfixture.html');
    $('#my-fixture').myTestedPlugin();
    expect($('#my-fixture')).to...;
    
By default, fixtures are loaded from `spec/javascripts/fixtures`. You can configure this path: `jasmine.getFixtures().fixturesPath = 'my/new/path';`.

Your fixture is being loaded into `<div id="jasmine-fixtures"></div>` container that is automatically added to the DOM by the Fixture module (If you _REALLY_ must change id of this container, try: `jasmine.getFixtures().containerId = 'my-new-id';` in your test runner). To make tests fully independent, fixtures container is automatically cleaned-up between tests, so you don't have to worry about left-overs from fixtures loaded in preceeding test. Also, fixtures are internally cached by the Fixture module, so you can load the same fixture file in several tests without penalty to your test suite's speed.

To invoke fixture related methods, obtain Fixtures singleton through a factory and invoke a method on it:

    jasmine.getFixtures().load(...);
    
There are also global short cut functions available for the most used methods, so the above example can be rewritten to just:

    loadFixtures(...);
    
Several methods for loading fixtures are provided:

- `load(fixtureUrl[, fixtureUrl, ...])`
  - Loads fixture(s) from one or more files and automatically appends them to the DOM (to the fixtures container).
- `appendLoad(fixtureUrl[, fixtureUrl, ...])`
  - Same as load, but adds the fixtures to the pre-existing fixture container.
- `read(fixtureUrl[, fixtureUrl, ...])`
  - Loads fixture(s) from one or more files but instead of appending them to the DOM returns them as a string (useful if you want to process fixture's content directly in your test).
- `set(html)`
  - Doesn't load fixture from file, but instead gets it directly as a parameter (html parameter may be a string or a jQuery element, so both `set('<div></div>')` and `set($('<div/>'))` will work). Automatically appends fixture to the DOM (to the fixtures container). It is useful if your fixture is too simple to keep it in an external file or is constructed procedurally, but you still want Fixture module to automatically handle DOM insertion and clean-up between tests for you.
- `appendSet(html)`
  - Same as set, but adds the fixtures to the pre-existing fixture container.
- `preload(fixtureUrl[, fixtureUrl, ...])`
  - Pre-loads fixture(s) from one or more files and stores them into cache, without returning them or appending them to the DOM. All subsequent calls to `load` or `read` methods will then get fixtures content from cache, without making any AJAX calls (unless cache is manually purged by using `clearCache` method). Pre-loading all fixtures before a test suite is run may be useful when working with libraries like jasmine-ajax that block or otherwise modify the inner workings of JS or jQuery AJAX calls.
  
All of above methods have matching global short cuts:

- `loadFixtures(fixtureUrl[, fixtureUrl, ...])`
- `appendLoadFixtures(fixtureUrl[, fixtureUrl, ...])`
- `readFixtures(fixtureUrl[, fixtureUrl, ...])`
- `setFixtures(html)`
- `appendSetFixtures(html)`

Also, a helper method for creating HTML elements for your tests is provided:

- `sandbox([{attributeName: value[, attributeName: value, ...]}])`

It creates an empty DIV element with a default id="sandbox". If a hash of attributes is provided, they will be set for this DIV tag. If a hash of attributes contains id attribute it will override the default value. Custom attributes can also be set. So e.g.:

    sandbox();
    
Will return:

    <div id="sandbox"></div>    
    
And:

    sandbox({
      id: 'my-id',
      class: 'my-class',
      myattr: 'my-attr'
    });
    
Will return:

    <div id="my-id" class="my-class" myattr="my-attr"></div>

Sandbox method is useful if you want to quickly create simple fixtures in your tests without polluting them with HTML strings:

    setFixtures(sandbox({class: 'my-class'}));
    $('#sandbox').myTestedClassRemoverPlugin();
    expect($('#sandbox')).not.toHaveClass('my-class');

This method also has a global short cut available:

- `sandbox([{attributeName: value[, attributeName: value, ...]}])`

Additionally, two clean up methods are provided:

- `clearCache()`
  - purges Fixture module internal cache (you should need it only in very special cases; typically, if you need to use it, it may indicate a smell in your test code)
- `cleanUp()`
  - cleans-up fixtures container (this is done automatically between tests by Fixtures module, so there is no need to ever invoke this manually, unless you're testing a really fancy special case and need to clean-up fixtures in the middle of your test)
  
These two methods do not have global short cut functions.

## Style Fixtures

The StyleFixtures module is pretty much like the Fixtures module, but it allows you to load CSS content on the page while testing. It may be useful if your tests expect that certain css rules are applied to elements that you are testing. The overall workflow is typically the same:

In _mycssfixture.css_ file:

    .elem { position: absolute }
    
Inside your test:

    loadStyleFixtures('mycssfixture.css');
    $('#my-fixture').myTestedPlugin();
    expect($('#my-fixture .elem')).toHaveCss({left: "300px"});

Notice that if you haven't applied the `position: absolute` rule to the `.elem` and try to test its left position in some browsers (e.g. GoogleChrome) you will allways get the value `auto` even if your plugin did everything correct and applied positioning. So that's why you might need to load style fixtures. In Firefox though you will get the correct value even without the `position: absolute`.
        
By default, style fixtures are loaded from `spec/javascripts/fixtures`. You can configure this path: `jasmine.getStyleFixtures().fixturesPath = 'my/new/path';`.

Like in Fixtures module, StyleFixtures are also automatically cleaned-up between tests and are internally cached, so you can load the same fixture file in several tests without penalty to your test suite's speed.

To invoke fixture related methods, obtain StyleFixtures singleton through a factory and invoke a method on it:

    jasmine.getStyleFixtures().load(...);
    
There are also global short cut functions available for the most used methods, so the above example can be rewritten to just:

    loadStyleFixtures(...);
    
Several methods for loading fixtures are provided:

- `load(fixtureUrl[, fixtureUrl, ...])`
  - Loads fixture(s) from one or more files and automatically appends them to the DOM into the HEAD element. This method will remove all existing fixtures loaded previously, if any.
- `appendLoad(fixtureUrl[, fixtureUrl, ...])`
  - Same as load, but it won't remove fixtures you added earlier.
- `set(css)`
  - Doesn't load fixture from file, but instead gets it directly as a parameter (e.g. `set('body {background: red}')`). Automatically appends style to the DOM. It is useful if your css fixture is too simple to keep it in an external file. This method will remove all existing fixtures loaded previously, if any.
- `appendSet(css)`
  - Same as set, but it won't remove fixtures you added earlier.
- `preload(fixtureUrl[, fixtureUrl, ...])`
  - Pre-loads fixture(s) from one or more files and stores them into cache, without returning them or appending them to the DOM. All subsequent calls to `load` methods will then get fixtures content from cache, without making any AJAX calls (unless cache is manually purged by using `clearCache` method).
    
All of above methods have matching global short cuts:

- `loadStyleFixtures(fixtureUrl[, fixtureUrl, ...])`
- `appendLoadStyleFixtures(fixtureUrl[, fixtureUrl, ...])`
- `setStyleFixtures(css)`
- `appendSetStyleFixtures(css)`    

Additionally, two clean up methods are provided:

- `clearCache()`
  - purges StyleFixture module internal cache (you should need it only in very special cases; typically, if you need to use it, it may indicate a smell in your test code)
- `cleanUp()`
  - cleans-up all existing style fixtures (this is done automatically between tests, so there is no need to ever invoke this manually, unless you're testing a really fancy special case and need to clean-up fixtures in the middle of your test)
  
These two methods do not have global short cut functions.

## JSON Fixtures

The JSONFixtures modules allows you to load JSON data from file (instead of putting huge blocks of data in the spec files).  

In _myjsonfixture.json_ file:

    {"property1":"value1", "array1":[1,2,3]}
    
Inside your test:

    var data = getJSONFixture('myjsonfixture.json');
    // or load and get the JSON two-step
    var fixtures = loadJSONFixtures('myjsonfixture.json');
    var data = fixtures['myjsonfixture.json'];
    
    expect(myDataManipulator.processData(test_data)).to...)
    
By default, fixtures are loaded from `spec/javascripts/fixtures/json`. You can configure this path: `jasmine.getJSONFixtures().fixturesPath = 'my/new/path';`.

Your fixture data is loaded into an object stashed by the JSONFixtures structure.  You fetch the data using the filename as the key.  This allows you to load multiple chunks of test data in a spec.  

Because a deep copy of Javascript objects can be a little tricky, this module will refetch data each time you call `load`.  If you modify the data within a spec, you must call `load` or `loadJSONFixtures` again to repopulate the data.

To invoke fixture related methods, obtain Fixtures singleton through a factory and invoke a method on it:

    jasmine.getJSONFixtures().load(...);
    
There are also global short cut functions available for the most used methods, so the above example can be rewritten to just:

    loadJSONFixtures(...);
    
Several methods for loading fixtures are provided:

- `load(fixtureUrl[, fixtureUrl, ...])`
  - Loads fixture(s) from one or more files and automatically adds them to the fixture list.  This method returns the entire set of fixtures keyed by their filename.
  
All of above methods have matching global short cuts:

- `loadJSONFixtures(fixtureUrl[, fixtureUrl, ...])`

- `getJSONFixture(fixtureUrl)`
  - After you've loaded fixture files, this global helper will retrieve the fixture data given the fixtureUrl


## Event Spies

Spying on jQuery events can be done with `spyOnEvent` and
`expect(eventName).toHaveBeenTriggeredOn(selector)` or
`expect(spyEvent).toHaveBeenTriggered()` . First, spy on the event:

    var spyEvent = spyOnEvent('#some_element', 'click');
    $('#some_element').click();
    expect('click').toHaveBeenTriggeredOn('#some_element');
    expect(spyEvent).toHaveBeenTriggered();

You can reset spy events

    var spyEvent = spyOnEvent('#some_element', 'click');
    $('#some_element').click();
    expect('click').toHaveBeenTriggeredOn('#some_element');
    expect(spyEvent).toHaveBeenTriggered();
    // reset spy events
    spyEvent.reset();
    expect('click').not.toHaveBeenTriggeredOn('#some_element');
    expect(spyEvent).not.toHaveBeenTriggered();

You can similarly check if triggered event was prevented:

    var spyEvent = spyOnEvent('#some_element', 'click');
    $('#some_element').click(function(event){event.preventDefault();});
    $('#some_element').click();
    expect('click').toHaveBeenPreventedOn('#some_element');
    expect(spyEvent).toHaveBeenPrevented();

Much thanks to Luiz Fernando Ribeiro for his
[article on Jasmine event spies](http://luizfar.wordpress.com/2011/01/10/testing-events-on-jquery-objects-with-jasmine/).

## Dependencies

jasmine-jquery was tested with Jasmine 1.2 and jQuery 1.8 on IE, FF, Chrome, and Safari. There is a high chance it will work with older versions and other browsers as well, but I don't typically run test suite against them when adding new features.

## Cross domain policy problems under Chrome

Newer versions of Chrome don't allow file:// URIs read other file:// URIs. In effect, jasmine-jquery cannot properly load fixtures under some versions of Chrome. An override for this is to run Chrome with a switch `--allow-file-access-from-files`. The full discussion on this topic can be found in [this GitHub ticket](https://github.com/velesin/jasmine-jquery/issues/4).

Under Windows 7, you have to launch `C:\Users\[UserName]\AppData\Local\Google\Chrome[ SxS]\Application\chrome.exe --allow-file-access-from-files`

## Mocking with jasmine-ajax

[jasmine-ajax](https://github.com/pivotal/jasmine-ajax) library doesn't let user to manually start / stop XMLHttpRequest mocking, but instead it overrides XMLHttpRequest automatically when loaded. This breaks jasmine-jquery fixtures as fixture loading mechanism uses jQuery.ajax, that stops to function the very moment jasmine-ajax is loaded. A workaround for this may be to invoke jasmine-jquery `preloadFixtures` function (specifying all required fixtures) before jasmine-ajax is loaded. This way subsequent calls to `loadFixtures` or `readFixtures` methods will get fixtures content from cache, without need to use jQuery.ajax and thus will work correctly even after jasmine-ajax is loaded.

## Testing with Javascript Test Driver

When using [jstd](http://code.google.com/p/js-test-driver/) and the jasmine adapter you will need to include jasmine-jquery.js after your jasmine-jstd-adapter files, otherwise jasmine-jquery matchers will not be available when tests are executed. Check out [this issue](https://github.com/velesin/jasmine-jquery/issues/95#issuecomment-9293180) for a thorough configuration example too.

## Contributing

jasmine-jquery is maintained by [Travis Jeffery](http://github.com/travisjeffery).

### Setup

- Fork the project and clone the repository.

    [Here's how.](http://help.github.com/fork-a-repo/)
- Create a dedicated branch.

    `git checkout -b your_feature`

### Writing the Code

- Get the code right.
- Include tests that fail without your code, and pass with it.
- Update the (surrounding) documentation, examples elsewhere, and the guides: whatever is affected by your contribution.
- Follow the conventions in the source you see used already, basically [npm coding style](http://npmjs.org/doc/coding-style.html)

If you can, have another developer sanity check your change.

### Committing

- Commit your change with a message on what you changed in the commit.

    `git commit -am "A commit message on what you changed"`
- Please squash your commits when appropriate. This simplifies future cherry picks, and also keeps the git log clean.

    [Here's how.](http://gitready.com/advanced/2009/02/10/squashing-commits-with-rebase.html)
- Push to your remote

    `git push origin your_feature`

### Issue a Pull Request

- Navigate to the jasmine-jquery GitHub page, press "Pull Request".
- Write your branch name in the branch field, press "Update Commit Range".
- Fill in some details about your potential patch including a meaningful
  title. When finished, press "Send pull request".

(These guidelines are heavily inspired and similar to [Rails's](http://edgeguides.rubyonrails.org/contributing_to_ruby_on_rails.html#contributing-to-the-rails-code)).
