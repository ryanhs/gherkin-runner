# Gherkin Runner

<center markdown="1">

![Gherkin Runner](https://raw.githubusercontent.com/ryanhs/gherkin-runner/master/logo.png)

> Run your Gherkin as a runner/flow logic. Simple, Elegant.

</center>

## Example A -  Basic

```javascript
const { createContext, runner, parser } = require('gherkin-runner');
const { pickles } = parser(`
Feature: Test Foo bar
  Scenario: Is it real?
    Given this is first expression
    And this is second expression
`);

const context = createContext({ user: "someuser" });
const [scenario1, /* scenario12, ... */] = await runner(expressions, pickles, () => context);
```

## Example A - create expressions

```javascript
const { worldCreator } = require('gherkin-runner');

const expressions = worldCreator();
expressions.addExpression('Foo is Bar', () => 'FOoooooo!');
expressions.addExpression('Baz is Bar Also', () => 'BaaaZZZZ!');
```

## Example B - Resolvers

Resolver in this runner follow schematic of graphql resolver `resolver(parent, context, args, info)`.

_\*parent & info for now is not really used, may be usefull in the future, planned to use the pickle._

```javascript
const world = worldCreator();
world.addExpression('foo exists', ($, c) => {
  if (!c.foo) {
    throw new Error('Foo must be exists');
  }
});
world.addExpression('bar exists', ($, c) => {
  if (!c.bar) {
    throw new Error('bar must be exists');
  }
});
world.addExpression('send notif', (_, c) => {
  c.notifSent = true;
});
world.addExpression('bla bla bla', (_, c) => {
  c.blablabla = true;
});
```

## Example C - Args

To use arguments in your expressions, you may use cucumber expressions format.
reference: https://cucumber.io/docs/cucumber/cucumber-expressions/

Example:

```javascript
const world = worldCreator();

world.addExpression('my name is {string}', {
  description: 'add name to context',
  resolver: ($, context, [name]) => {
    context.name = name.slice(1, -1);
  },
});


// test it
const context = createContext({});
const name = 'foo bar';
await world.executeStep({ text: `my name is "${name}"` }, context);
expect(context.name).toBe(name);
```

## Extensions

Some of feature may have more than just a description. maybe you need TnC? or something else to fit in to your features.
Extensions may bridge it.

example:

```sh
Feature: test feature title

  Description:
    this can be description

  TnC:
    this line is TnC too

  Scenario: this is scenario title
    Given this is first expression
    And this is second expression
    Then this is third expression
```

it will be in `gherkinDocument.feature.extensions`, as following:

```javascript
expect(gherkinDocument.feature.extensions.description).toMatch(/this can be description/);
expect(gherkinDocument.feature.extensions.TnC).toMatch(/this line is TnC too/);
```

> description always in this lowercase format.



## !! Still Early Development !!


## License

MIT

## Thanks to

_*ascii art from: https://www.oocities.org/spunk1111/food.htm_
