---?color=linear-gradient(to right, #0750c4, #f4ab4b)
@title[Cypress.io]

@snap[west headline text-white span-70]
Cypress.io<br>*An overview*
@snapend

---

## What is it?

Javascript based test framework

---

## Why are we using it?

Large test suites caused extremely large run times, which, coupled with a lack of testing in pipeline have resulted in the tests not being ran, which has resulted in escapes.

In adopting Cypress we hope to increase the likelihood that tests will be executed and regressions caught, pre-merge.

---

## Weaknesses

+++

- Doesn't support Firefox / IE / Edge / Safari
  - But we can still use wdio/sauce for x-browser

+++

- Doesn't support iframes / tabs / windows
  - Any existing tests needing tabs (e.g. SSO/IDP tests) won't work

+++

- Doesn't support GRID architecture / Saucelabs
  - Isn't really a huge deal, except for parallelization loss

+++

- Doesn't support xpath
  - So existing xpaths will need to be converted to css selectors or similar, may require markup modification for unlocatable elements

+++

- Doesn't support parallelization (unless you pay, and then only in CI)
  - But its primarily a local tool, so that's not a huge loss

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
  - Handy for quick feedback

+++

- The Cypress harness takes DOM snapshots at each step of a test
  - Handy for debugging

+++

- You can access the console from code, and manually (local only, not in CI)
  - Handy for debugging & asserting on console state

+++

- It's quicker than standalone & sequential webdriver

---

## Code

+++?color=black
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
@[10,14,18,22](Standard model interaction pattern)
+++?color=black
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

@snap[west split-screen-heading text-orange span-50]
```
element.click()
```
@snapend

@snap[east text-black span-45]
Unlike all the automation code you've ever written, in Cypress, this does _not_ click the element.  It records your _intent_ to click the element, which actually happens later.
@snapend

+++
```
element.click()
```
Unlike all the automation code you've ever written, in Cypress, this does _not_ click the element.  It records your _intent_ to click the element, which actually happens later.

+++

Which means you can't, for example, do this

```javascript
expect(myElement1.text).not.to.eq(myElement2.text)
```

if you want to branch on a value (e.g. compare two values, work with arrays, etc), you have to do something like this

```javascript
myElement1.invoke('text').then((text1) => {
  myElement2.invoke('text').should((text2) => {
    expect(text1).not.to.eq(text2)
  })
})
```
+++
Similarly, instead of
```javascript
expect(myPage.assertions().to.have.lengthOf(3));
expect(myPage.assertions()[0].text()).to.equal('Some text from first p');
expect(myPage.assertions()[1].text()).to.equal('More text from second p');
expect(myPage.assertions()[2].text()).to.equal('And even more text from third p');
```
+++
you have something like

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