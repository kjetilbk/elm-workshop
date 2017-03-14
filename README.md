# Elm Arcade 
# Getting Started With Elm and Typed Functional Programming

Welcome to this workshop! Today we're learning Elm and typed functional programming techniques through creating the classic game Memory.

The workshop will cover the following topics:

* Tuples
* Records
* Type Inference
* Type Signatures
* Union Types
* Type Aliases
* Pattern Matching
* Functions
* Partial Application
* Currying

Some of these concepts may be unfamiliar and somewhat confusing to begin with, so please do ask us if and when you get stuck, or simply have a question. That's what we're here for!

## Prerequisites

1. Install `elm`. This can be done with `npm install -g elm`, `brew install elm` (if on MacOS) or an old-school file download from [elm-lang.org](https://guide.elm-lang.org/install.html).

1. Install a [`plugin`](https://guide.elm-lang.org/install.html#configure-your-editor) for your editor. At the time of writing, Atom's Elm integration seems the best so we **strongly** recommend you use that, even if Atom is not usually your main editor of choice.

    *  [Atom editor setup](https://github.com/halohalospecial/atom-elmjutsu#setup)

1. [`elm-format`](github.com/avh4/elm-format#for-elm-018) is a crucial tool to make your Elm experience more enjoyable.
    * Remember to ake sure that `elm-format` is available on your PATH or that you tell your editor where to find it
    * In Atom, this can be done under package settings for the `elm-format` package: input the path to the `elm-format` binary. (If you for example installed it via `brew` on MacOS, the path should be along the lines of `/urs/local/bin/elm-format-0.18`)
    * We also recommend you enable `Format on save`

1. Clone this repo to your computer

1. Start your local application enviroment with `npm start` in the root folder of this repo. This should open a new browser window with `localhost:3000` and a nice compilation error.

Congratulations, you're now ready to begin learning Elm!

## Level 1 - Hello, world!
File: _Main.elm_

Main.elm should look like this:

```elm
module Main exposing (..)

import Html exposing (..)

main =
    "Hello, world!"
```

As you can see in your browser, the app will fill the screen with an error message then your code does not compile.
This might be unfamiliar to you if you're coming from JavaScript to Elm. With JavaScript you have to run your code in the browser to discover any programming mistakes you might have made, while with Elm these errors will be caught right as you hit save in your editor!

> #### A note on Elm's compiler:
>
> Elm is famous for it's compiler errors.
>If you get stuck with your program not compiling, please read the error message carefully.
>The creators of Elm have put a lot of energy into making these error messages helpful, and they are!
>Most times they tell you exactly what you have to do to make your program work.

Now, study the on-screen error message.

Our app is now telling us that the value of `main` has the wrong type: it is a `String` but it should be either `Html`, `Svg` or `Program`.

To change the `String` (`"Hello, world!"`) into some `Html` we have a function that does exactly that; [`text : String -> Html msg`](http://package.elm-lang.org/packages/evancz/elm-html/4.0.1/Html#text). 

>#### Note:
>* The official docs has a nice chapter on ["Reading types in Elm"](https://guide.elm-lang.org/types/reading_types.html)
>* Elm-tutorial has a nice chapter on functions in Elm: ["Function basics"](https://www.elm-tutorial.org/en/01-foundations/02-functions.html)

Use this to make your program compile and print "Hello, world!" to the screen.

### Creating a greeting function

Now we want to create a function that takes a name and greets.
It should have this type signature: `greet: String -> String`.

Called with "Erik", it should produce the string "Hello, Erik". Thus:

`greet "Erik" == "Hello, Erik"`

Here is an example of a function that takes two numbers and returns the sum of those numbers:

```
add x y =
	x + y
```

There are several things to note here:

* There's no `return` keyword - the evaluated value of the function body is automatically returned
* The parameters are named and come after the function name
* You don't have to specify the types for the parameters - they are _inferred_! This can be done because Elm sees the addition operator (`+`) and knows that it only works on numbers. Therefore, `x` and `y` must be numbers!

Go ahead and make the `greet` function. The string concatenation operator in Elm is `++`.

### Adding type signatures

Before we finish of the first level, try adding the type signature for your `greet` function.
As mentioned, type signatures are not needed, as the compiler can infer them, but it is good practice to add them anyways.
This makes the code easier to read and can help you get better error messages.


## Level 2 - Learning types

From here on we'll move in small steps, writing small chunks of code that will be a part of our final game, while adding more and more features from functional programming and Elm along the way. Ready, set, go!

### It's a new record!

We are going to create a representation of a "card" - something that is hiding a picture and can be flipped by the player. We'll start off with creating the equivalent data structure of a JavaScript object - a _record_ .

```
// JavaScript

var card = {
	name: 'Tom Cruise',
	expensiveShoes: true
}

-- Elm
card : { name: String, fancyShoes: Bool }
card = {
	name = "Tom Cruise",
	expensiveShoes = true
}
```

Our Elm record should contain a single field `id` of type String - this string will refer to the file name of the image our card will be hiding. Start with `id = "1"`.

Next up - let's render our card to the screen. Write the following function:

`viewCard: { id: String ] -> Html msg`

### About the scary type..

Don't worry about that scary type `Html msg` - we'll learn more about that later! Simply put, it's just saying that "hey, our Html will emit some actions later on, and they'll be of type _a_ (which is a type placeholder)


### Rendering HTML to the screen

Oh, right, we didn't tell you about HTML yet! If you're familiar with the library React.js, the following section might feel familiar to you.

```
// JavaScript with React
<div class="ninja">
	<span>Banzai!</span>
</div>


-- Elm
div [ class "ninja" ] [ 
	span [] [ text "Banzai!" ] 
]
```

All HTML tags have their own functions in Elm, and they all accept two parameters:

1. a list of `Html.Attribute`
2. a list of zero or more `Html` nodes

Here, we want you to represent a card with the following Html:

```
<div>
	<img src="/static/cats/" + card.id + ".jpg" />
</div>
```

Make sure you import `Html.Attributes` and use the string concatenation operator `++`!

You should now see a beautiful little kitten on you screen.

### Union Types: Representing card state

Memory requires us to flip a card and reveal it's image when clicked. This means we need a way to represent card state, as a card can be in one of three potential states: `Open | Closed | Matched`.

Think about how we'd store this state in JS. Most likely, we'd reach for a string:

```
{
	id: '1',
	state: 'open'
}
```

This is obviously not very safe. This doesn't constrain us to using only the three possible values, and there's nothing to avoid typing errors. Elm and other ML-languages have a great feature for this use case: _Union Types_.

A union type is like a Java or C# enumerable - a union type is a value that may be one of a fixed set of values. Like black pieces may only be white or black.

```
type PieceColor = White | Black
```

`PieceColor` is now treated a fullworthy type in our system, just as `String` or `Bool`. `White` or `Black` are _constructor functions_, functions that take _zero_ arguments and return a value of type `PieceColor`. Or, said with a type signature:

```
White : PieceColor
Black : PieceColor
```

Union types may also carry data. This means that the _constructor functions_ for such union type values aren't zero argument functions:

```
type CustomerAge = Unknown | Age Int

Unknown: CustomerAge
Age: Int -> CustomerAge
```

Let's say we either have an age value for a given customer, or we don't. 
This _accompanying data_ that is wrapped within a union type may be of any type, and they don't have to the same for all value types within a union.

Some people say that _union types_ can be seen as _enums on stereoids_. In a way, that's a fitting description.

Let's create a union type called `CardState` that can be either `Open`, `Closed` or `Matched` (union type values are always capitalized).

Enrich our previous `card` record with a field `state` that carries a `CardState` value.
You'll also have to update the signature of `viewCard`.
Our `card` should now have the following type signature:

```
card: { id: String, state: CardState }
```

By now it should become clear that our signature for `card` is getting unwieldy. Imagine maintaining signatures for our card objects all around the codebase as we add more fields!

### Type Alias (alias slayer) 

_Type aliases_ allow us to define a record with a specified data structure as a new type. Let's model everyone's favourite data structure using a type alias:

```
type alias Person = {
	name: String,
	age: CustomerAge
}
```

The above code tells the Elm compiler that a `Person` is a record with a field `name` of type `String`, and a field `age` of the type `CustomerAge` (that we defined earlier).

This allows to use this type throughout our code:

```
getName : Person -> String
getName person = 
	person.name
```

Imagine calling this function with an object without a name field. In JavaScript, this would obviously crash hard, but in Elm - the code won't even compile! This moves the time of discovering the error from compiletime to runtime, which is a huge deal.

Create a type alias `Card` that defines the card data structure from before. Use this new type in the signatures of `viewCard` and `card`

### Rendering all the states

Let's expand our single card to three cards, each representing one of the three possible values of `CardState`.

Next, we're going to create this function: `viewCards : List Card -> Html msg`.

Notice how the type signature helps in communicating what the function does! Type signatures are a very powerful tool, as you will discover throughout this workshop.

Make sure you render the correct image source for each card (`{id}.jpg`)

Hint: `List.map viewCards cards`

### Matching all the patterns

The next languge feature we will be using is `Pattern Matching`. It can best be described as a switch-statement on stereoids, allowing us to do more than simple matching on a value:

```
isAdult : CustomerAge -> Bool
isAdult age =
    case age of
        CustomerAge age ->
            age > 18
        Unknown ->
            false
```

This is a powerful technique, and is almost always used whenever there's a union type around. In this case, it is handy for rendering different stuff based on the `CardState` of a card.

For rendering cards, use the following logic:

- For `Closed`, show `"/static/cats/closed.png"`
- For `Open` and `Matched`, show `"/static/cats/{cardId}.jpg"`
- Add the respective css classes `open`, `closed` and `matched` to the `img` tags

## Level 3 - Beginner Program!

In this section, we will take our first steps toward learning The Elm Architecture (TEA), the architecture that inspired Dan Abramov to write Redux. 

We've made it this far without TEA because we have a simple, static app. Now we want to start responding to user input, and TEA is the way Elm structures applications and handles interactivity.

The goal of the section is to implement card clicking: all cards should start as `Closed`, and change to `Open` when clicked. Don't worry about `Matched` for now - we'll deal with that later.

[Begin by reading the official docs on `Html.beginnerProgram`](http://package.elm-lang.org/packages/elm-lang/html/1.1.0/Html-App#beginnerProgram)

[You may also find the docs on The Elm Architecture interesting.](https://guide.elm-lang.org/architecture/)

Now that you're getting warm, we will be giving you fewer specific instructions and more high-level requirements. Use the workshop hosts if you have questions and don't forget to make use of the helpfulness of the compiler.

Section outline:

1. Create a helper function `setCard: CardState -> Card -> Card`. The function should update the `CardState` of the passed `Card` and return a new `Card`. See the docs for [updating a record](http://elm-lang.org/docs/records#updating-records)
2. Change `main` to `Html.beginnerProgram { ... }`. Read the docs to see what parameters it accepts!
3. Create a type alias `Model` that has the following type: `{ cards : List Card }`
4. Create the type `Msg` with the value `CardClick`
5. Use pattern matching in `update` on the type of `Msg`
6. Import `Html.Events` and add an `onClick` event handler on closed cards.

When this section is complete, you should render three closed cards, each of them opening when clicked.

## Level 4 - The game!

We are now going to implement our game logic.

In memory, as you may know, the player opens two cards, and if they match they stay open.
If they do not match, both cards are closed again.
This repeats until all cards on the board are open.

Our game implementation will have three states:
  
  1. `Choosing` - the player chooses the first card
  1. `Matching` - the player chooses the second card to match with the first
  1. `GameOver` - all cards are matched and the player has won

The game logic will flow like this:
  
  1. When the player chooses the first card:
    1. Set all unmatched cards to `Closed`
    1. Set the chosen card to `Open`
    1. Go to `Matching` state
  1. When the player chooses the second card:
    1. If it matches the first card, then set the two cards to `Matched`, else set the second card to `Open`
	1. If all cards are `Matched`, then go to `GameOver` state, else go to `Choosing` state
    

Let's get back to the code.

Our deck of cards is a list of `Card`s and we will be passing them around in our program.
Therefore, instead of having to write `List Card` everywhere, we want to be able to write `Deck`.

1. Create a `type alias` for `List Card` called `Deck`
1. Update `Model` to have `deck`

In the game we will be matching pairs of cards with each other, and will need some way to distinguish between two cards with the same image.

1. Create a type `Group` that is either `A` or `B`
1. Add that as a field in our `Card` type

Now we are able to check if two cards are of one pair by comparing their `id` and `group` fields!

By now our `Main.elm` file is getting quite big, so we should probably do something about that.
It is common in Elm projects to have the application's models in their own file, so let's try that:

1. Move all types and type aliases to a new file: `Model.elm`
  * A file and it's module name must match, so in our case `Model.elm` should start with `module Model exposing (..)`
1. To use our types in `Main.elm` we also need to import them. This is done in the same way as we import the `Html` module; `import Html exposing (..)`


Let's pretend we're famous TV chefs and cheat a little bit. We have prepared a module `DeckGenerator` that can be used to generate a deck of cards.

1. Add file `DeckGenerator.elm` with the following contents:

```elm
module DeckGenerator exposing (static, random)

import Random
import Random.List
import Model exposing (Deck, CardState(..), Group(..))

static : Deck
static =
    let
        urls =
            [ "1"
            , "2"
            , "3"
            , "4"
            , "5"
            , "6"
            ]

        groupA =
            urls |> List.map (\id -> { id = id, state = Closed, group = A })

        groupB =
            urls |> List.map (\id -> { id = id, state = Closed, group = B })
    in
        List.concat [ groupA, groupB ]

random : Random.Generator Deck
random =
    Random.List.shuffle static
```

Use this by importing `DeckGenerator` in `Main.elm` and using the `static` value as `model`'s initial value.

Have fun clicking cards for about 20 minutes.


## Level 5 - The game 2

Let's now implement the game logic!

As we've established, our game has three states: `Choosing`, `Matching` and `GameOver`.
Let's implement this as a union type called `GameState`.

The `GameOver` state does not need any extra data, but `Choosing` needs a `Deck` (the deck we are choosing from), and `Matching` needs both a `Deck` (the deck we are choosing from) and a `Card` (the card we are trying to match with).



To recap our implementation:
* In `Choosing` state
  1. Close all unmatched cards
  2. Open the clicked card
* In `Matching` state
  1. When the two cards match , set both cards to `Matched`. When the two cards do not match, set the clicked card to `Open`.
  1. If all cards are now matched, go to `GameOver`. If not, go to `Choosing`.
* In `GameOver` state, do nothing


You will also have to update the `view` function to accomodate for the new shape of our model.
Refreshing the page every time you want to play another game is boring, so try to add a "restart game" button in the "Game over" view. Hint: it is common to have a top-level value called `init`.

Now, take a minute to pat yourself on the back for making an awesome game in Elm!

## Level 6 - Let's get random!

You might have noticed that our game is kind of easy; the cards are in the same spots every time, and that's no fun!
Let's make things more interesting by shuffling the deck of cards at the start of each game.

Shuffling a list of something includes randomness, and generating random numbers is an effectful operation. (Short explanation?)
If you look through the type signatures of the functions we have written so far, there is no way to express side effects because Elm is a _pure_ functional language. Luckily, we have a way to do that.

To generate something random, we can to use the built-in function `Random.generate`, which has the signature: `Random.generate : (a -> msg) -> Generator a -> Cmd msg`.
The `Generator a` part is covered by `DeckGenerator.random : Generator Deck`, so that means you have to supply a function that takes a `Deck` and returns a `Msg`.

Now you're probably wondering what that `Cmd` thingy is, so take a minute and head on over to the official documentation, which has nice, short explanations of [_subscriptions_](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Platform-Sub#Sub) and [_commands_](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Platform-Cmd#Cmd).

Since we're now not longer _beginners_ we should change `Html.beginnerProgram` to `Html.program`.

There are a couple of changes we have to make this official transition from _beginners_ to _adepts_.

* The argument to `Html.program` differ slightly from the argument to `Html.beginnerProgram`:
  1. `model` is now called `init`, and it's type is now `(Model, Cmd Msg)`
  1. The record should have a new field called `subscriptions : Model -> Sub Msg`.
* `update` now has the type signature `update : Msg -> Model -> (Model, Cmd Msg)`

Hint: in your code you can use `Sub.none` and `Cmd.none` when you don't have any _subscriptions_ or _commands_ you want to perform.

So, to summarize:

1. Change `beginnerProgram` to `program` and modify it's argument:
  * Rename `model` to `init`
  * Change `init` and `update` according to new type sigs (`Cmd.none`)
  * Add the `subscriptions` field in the record
2. Create a function that takes a `Deck` and returns a `Msg`
1. When starting the game, return the command to generate the random deck along with the initial `model`

Once done, you have nothing left to do but congratulate yourself on making an awesome memory game in Elm! Congratulations!

## Bonus levels
* Count the number of attempts the player uses, use that as score
* Let the player enter a name
* Save each game's score and show a high score table
* Count how long the player takes to finish the game. Use [Time.now](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Time#now) together with [Task.perform](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Task#perform) to get the current time
