---?color=linear-gradient(to right, #0750c4, #f4ab4b)
@title[Cypress.io]

@snap[west headline text-white span-70]
Cypress.io<br>*An overview*
@snapend

---

## What is it?

---

## Why are we using it?

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

- Doesn't support parallelization (unless you pay, and even then only in CI)
  - But its primarily a local tool, so that's not a huge loss

+++

- Doesn't support categorisation-by-config

+++

- Doesn't support async/await

+++

- Code for working with values, is... funky.  E.g. Instead of
```
expect(myElement1.text).not.to.eq(myElement2.text)
```
 you have
```
myElement1.invoke('text').then((text1) => {
  myElement2.invoke('text').should((text2) => {
    expect(text1).not.to.eq(text2)
  })
})
```

---

## Strengths

+++

- Tests run on a watch
  - Handy for quick feedback

+++

- Harness take DOM snapshots of each step
  - Handy for debugging

+++

- Can access the console (local only, not in CI)
  - Handy for debugging & asserting lack of js errors

---

## Code

what a test looks like

what a model object looks like

what assertions look like

---

### Demo