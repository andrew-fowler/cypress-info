---
@title[Cypress.io]

@snap[west headline text-white span-70]
Cypress.io<br>*Overview*
@snapend

---

## What is it?

+++

Cypress is
- a (mostly) open source
- javascript based test framework.
- using a local web based test harness
- based on an injected js proxy, similar to Selenium 1

---

## How does it fit into our testing strategy

+++

Cypress tests should be

- small
- isolated
- component based
- supported by high level, parallelized, CI-based wdio tests for x-browser coverage

---

## Why are we using it?

+++

Large test counts = Long run times <br>
Long run times = Tests aren't ran <br>
Tests aren't ran = Regression escapes

+++

In adopting Cypress we hope to increase the likelihood that tests will be executed and increase the likelihood that regressions are caught, quickly and pre-merge.

---

## Weaknesses

+++

- Doesn't support Firefox / IE / Edge / Safari
  <!--- But we can still use wdio/sauce for x-browser-->

+++

- Doesn't support iframes / tabs / windows
  <!--- Any existing tests needing tabs (e.g. SSO/IDP tests) won't work-->

+++

- Doesn't support xpath
  <!--- 
   So existing xpaths will need to be converted to css selectors or similar, may require markup modification for unlocatable elements.
  -->

+++

- Doesn't support parallelization* 
  <!--- 
   Which means execution costs are linearly proportional to test count, which limits scalability.
  -->

+++

- Doesn't support categorisation-by-config

+++

- Javascript only

+++

- Doesn't support async/await

+++

- Forces mocha usage

+++

- Cypress code doesn't work like you think it does


---

## Strengths

+++

- Tests run on a watch
  <!--- Handy for quick feedback-->

+++

- Comes with a strong test harness app for local execution
  <!--- Handy for debugging-->

+++

- You can access the console from code, and manually (local only, not in CI)
  <!--- Handy for debugging & asserting on console state-->

+++

- It's quicker than standalone & sequential webdriver

---

## Code

+++
@snap[north-east template-note text-gray]
An example of a test
@snapend
```javascript
let indexPage = require('../model/index.page');
let forgottenPasswordPage = require('../model/forgottenPassword.page.js');

describe('User can request a new password', function() {
  it('Given the user loads the landing page', function() {
    cy.visit('/');
  })
 
  it('And goes to the Forgotten Password page', function() {
      indexPage.forgottenPasswordLink().click();
  })

  it('And they specify an email address', function() {
      forgottenPasswordPage.emailInput().type("foo@bar.com");
  })

  it('When they submit the request', function(){
      forgottenPasswordPage.submitButton().click();
  })

  it('Then the success message is displayed', function(){
      forgottenPasswordPage.successMessage().should('be.visible');
  })
})
```
@[4,24](Standard mocha test structure.)
@[5,9, 13, 17, 21, 25](Gherkin style verbose step descriptors)
@[1,2,10,14,18,22](Standard model interaction pattern)
+++
@snap[north-east template-note text-gray]
An example of a model object
@snapend
```javascript
var forgottenPasswordPage = {
    emailInput: function(){return cy.get('#email')},
    submitButton: function(){return cy.get('#form_submit')},
    successMessage: function(){return cy.contains(`Your e-mail's been sent!`)}
}

module.exports = forgottenPasswordPage;
```
+++
@snap[north-east template-note text-gray]
A pretty standard hooks implementation
@snapend
```javascript
before(function() {
// runs once before all tests in the block
})

after(function() {
// runs once after all tests in the block
})

beforeEach(function() {
// runs before each test in the block
})

afterEach(function() {
// runs after each test in the block
})
```
+++?image=template/img/bg/orange.jpg&position=right&size=50% 100%

@snap[west split-screen-heading text-orange span-20]
element.click()
@snapend

@snap[east text-white span-45]
Unlike other automation code, in Cypress, this does _not_ click the element.  
<br>
It records your _intent_ to click the element, which actually happens later.
@snapend

+++
@snap[north-east template-note text-gray]
Which means you can't, for example, do this
@snapend

```javascript
expect(myElement1.text).not.to.eq(myElement2.text)
```

+++
@snap[north-east template-note text-gray]
If you want to branch based on the result of a query, you have to do something like this...
@snapend


```javascript
myElement1.invoke('text').then((text1) => {
  myElement2.invoke('text').should((text2) => {
    expect(text1).not.to.eq(text2)
  })
})
```
+++
@snap[north-east template-note text-gray]
Similarly, instead of...
@snapend
```javascript
expect(myPage.paragraphs().to.have.lengthOf(3));
expect(myPage.paragraphs()[0].text()).to.equal('Some text from first p');
expect(myPage.paragraphs()[1].text()).to.equal('More text from second p');
expect(myPage.paragraphs()[2].text()).to.equal('And even more text from third p');
```
+++
@snap[north-east template-note text-gray]
You have something like...
@snapend
```javascript
cy.get('.-p').find('p')
.should(($p) => {
  let texts = $p.map((i, el) =>
    Cypress.$(el).text())

  texts = texts.get()
  expect(texts).to.have.length(3)
  expect(texts).to.deep.eq([
    'Some text from first p',
    'More text from second p',
    'And even more text from third p',
  ])
})
```
---

### Demo
