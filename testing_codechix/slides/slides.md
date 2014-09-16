% title: Testing Beyond jUnit
% subtitle: (so your users don't have to do it for you)
% author: Mindy Preston
% thankyou: Thanks everyone!
% thankyou_details: And especially these people:
% contact: <span>CodeChix Madison</span> <a href="http://www.codechix/">website</a>

---
title: Testing Beyond jUnit
build_lists: false

What we'll talk about

- what I mean by 'testing'
- why test?
- testing methodologies
- what to do with testing data
- your questions!

---
title: You Keep Using That Word

<!-- explain what I mean when I say "testing" -->

- figure out whether your software does what it should
- "what it should" is often the hard part of testing
- focus on automated testing but manual testing has its place

---
<!-- things testing encompasses: happy path (the code does the right thing to well-formed input in the absence of exceptions) -->

<!--TODO image -->
<img src="figures/happy_path_img.png" />

<footer class="source">source of image </footer>

---
<!-- the program handles bad input as specified (hopefully gracefully) -->
<!--TODO image -->
<img src="figures/garbage_in.png" />

<footer class="source">source of image </footer>

---
<!-- the program handles systems failures as gracefully as possible -->
<!-- TODO image -->
<img src="figures/server_on_fire.png" />

<footer class="source">source of image </footer>
---
most people focus on 
<img src="figures/happy_path_img.png" />

but the worst bugs are usually
<img src="figures/garbage_in.png" /> and <img src="figures/server_on_fire.png" />
---
title: why test?

<!-- if you're not doing it, your users (or your QA people, or your support people, or people who build things on top of your product) are doing it for you '-->
if not you, who?

---
title: User-Generated Bug Reports
<!-- users make terrible bug reports. -->

<!--TODO: get a really horrible email screenshot of the "it's broken" sort -->
---
title: Patience is a Finite Resource

<!-- user patience is a finite resource.  if your product is generally reliable you'll start up top, but every user-visible bug you ship moves you closer to the bottom of this list. ' -->

* "surely it's meant to work this way; we must be doing it wrong"
* "I'll just live with it"
* "this thing sucks; let's get/build something else"

---

you can't catch every bug with testing. <!-- ' -->

---

that makes it *even more important* to catch the bugs you can catch.

---
title: How to Test

* unit tests 
	* test-driven development
	* generative tests
* integration tests
* performance tests
* regression tests
* a shoutout to manual testing
* user experience tests

---
title: Unit tests
<!-- generally, take the form of some kind of assertion about your data - usually the return value from a function -->

<!-- it can be hard to see the point of these in systems that are put together like we're taught they should be -- small functions limited in scope doing one job -- because those functions are human-sized and we imagine we know their behavior.  It's true that we know their behavior when we're writing the function, and for a little while after we're done, but as anyone who's had to go back and read their code years later  can tell you, it doesn't stay obvious. -->

<!-- refactoring and legacy code shoutout. -->

<!-- TODO: redo this in javascript -->
<pre class="prettyprint" data-lang="java">
function testMyCoolFunction() {
	assert myCoolFunction(goodData) == expectedValue;
	assert myCoolFunction(badData) == expectedErrorValue;
	breakSomethingImportant();
	assert myCoolFunction(goodData) == expectedException;
}
</pre>

---
title: Unit tests
<!-- in more complicated cases, you have to do a lot of bookkeeping; moreover, statechecking can get nontrivial -->
<!-- you can see this when unit tests are the only testing framework; in a lot of cases, other kinds of testing frameworks are better for nontrivial side-effecting systems -->

<pre class="prettyprint" data-lang="c">
void testMySideEffectingFunction() {
	state = establishInitialState();
	ASSERT_EQUAL(mySideEffectingFunction(state, goodData), expectedValue);
	ASSERT_EQUAL(state, expectedModifiedState);
	destroyState(state);
}
</pre>

---
title: Bad Data 

Happy families are all alike; every unhappy family is unhappy in its own way.

-- Leo Tolstoy, <i>Anna Karenina</i>

<!-- so too with good data and bad data.  There's probably >1 kind of data that your function shouldn't try to operate on. -->

<pre class="prettyprint" data-lang="java">
function testMyCoolFunction() {
	assert myCoolFunction(nullData) == expectedErrorValue;
	assert myCoolFunction(dataMissingAParticularField) == expectedErrorValue;
	assert myCoolFunction(justPlainWeirdData) == expectedErrorValue;
	assert myCoolFunction(maliciouslyIncorrectData) == expectedErrorValue;
	assert myCoolFunction(incompleteData) == expectedErrorValue;
	assert myCoolFunction(dataWithABunchOfZalgoInIt) == expectedErrorValue;
	...
}
</pre>
---
title: Generative Testing
<!-- generally our unit tests take the form of some assertion about returned values from functions -->
<!-- what if we could make assertions about our *code* instead? -->
* our functions have *properties* (e.g., "always returns a JSON object or throws a BadDataException")
* we use *generators* to generate a bunch of random data with which to test them 

---
title: Python example: genty

<pre class="prettyprint" data-lang="python">
# Here's the class under test
class MyClass(object):
    def add_one(self, x):
        return x + 1

# Here's the test code
@genty
class MyClassTests(TestCase):
    @genty_dataset(
        (0, 1),
        (100000, 100001),
    )
    def test_add_one(self, value, expected_result):
        actual_result = MyClass().add_one(value)
        self.assertEqual(expected_result, actual_result)
</pre>

<footer class="source">https://pypi.python.org/pypi/genty/1.1.0</footer>

---
title: Slide with a figure
subtitle: Subtitles are cool too
class: img-top-center

<img height=150 src=figures/200px-6n-graf.svg.png />

- Some point to make about about this figure from wikipedia
- This slide has a class that was defined in theme/css/custom.css

<footer class="source"> Always cite your sources! </footer>

---
title: Segue slide
subtitle: I can haz subtitlz?
class: segue dark nobackground

---
title: Maybe some code?

press 'h' to highlight an important section (that is highlighted
with &lt;b&gt;...&lt;/b&gt; tags)

<pre class="prettyprint" data-lang="javascript">
function isSmall() {
  return window.matchMedia("(min-device-width: ???)").matches;
}

<b>function hasTouch() {
  return Modernizr.touch;
}</b>

function detectFormFactor() {
  var device = DESKTOP;
  if (hasTouch()) {
    device = isSmall() ? PHONE : TABLET;
  }
  return device;
}
</pre>

