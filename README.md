Welcome to Strikingly Interview Test
=====================================

Introduction
------------

This test is based on the famous [Hangman Game](http://en.wikipedia.org/wiki/Hangman_(game)).
Your task is to write a program to play Hangman, guessing words from our server through a RESTful API.

### Requirements

* **For back-end applicants:** write a Ruby program according to the following
  specifications. When you run the program, the program should play the game
  automatically. When you're happy with your score, submit your score to us.
* **For data engineer applicants:** write a program with your preferred language 
  according to the following specifications. When you run the program, the program 
  should play the game automatically. When you're happy with your score, submit 
  your score to us.
* **For front-end applicants:** write a JavaScript/CoffeeScript program according
  to the following specifications. When you run the program, the program should
  play the game automatically. When you're happy with your score, submit your
  score to us.
* **For iOS/Android applicants:** write an iOS/Android App according to the
  following specifications. Users of your app should be able to play the hangman
  game and they should be able to view and submit their score.
* You can use ANY libraries relevant for this task. Use libraries to save dev
  time and improve your score.
* Email us your finished program in a ZIP file. Make sure to name the file in this format: firstname_lastname.zip. i.e. Han_Meimei.zip
* Be sure to include a README explaining the cool things you did.
  Walk us through your algorithm, how it works, etc.

### Our Expectations

Through this programming test, you should be able to demonstrate:

* Good understanding of Web/Mobile App development technology.
* Good understanding of the programming language you use.
* Good programming practices. Code quality and maintainability.
* Apply suitable algorithms to solve problems.
* Creativity.
* A good score! **Our average score is about 1000.** See how well you can do.


The Game Flow and Specification
--------------------------------

The overall workflow is in 5 stages: "Start Game", "Give Me A Word",
"Make A Guess", "Get Your Result" and "Submit Your Result" You can play and
submit as many times as you want, but we only store the LATEST submitted score.
When you're satisfied with your score, don't submit any more!

### HTTP Request

* URL: http://www.domain-name.com/game/on
* Method: POST
* Headers:
  - Content-Type:application/json
* Body: JSON Hash

```javascript
{
  "key1": "value1",
  "key2": "value2"
}
```

Note: You can get the `URL` from your invitation email.

Example:

`curl -X POST http://www.domain-name.com/game/on -H "Content-Type:application/json" --data '{"playerId":"YOUR_EMAIL", "action":"startGame"}'`

### 1. Start Game

You send a request to the server to start game. Get the ID of game session from
the server. It identifies the game session and you need include it in the
following communications.

#### Request and Response

**Request:**

```javascript
{
  "playerId": "test@example.com",
  "action" : "startGame"
}
```

* `action` is the action you want the server to take.
* `playerId` is your identity.

Note: You can get your `playerId` from your invitation email.

**Response:**

```javascript
{
  "message": "THE GAME IS ON",
  "sessionId": "04ccda515d152cbe630e156af9095104",
  "data": {
     "numberOfWordsToGuess": 80,
     "numberOfGuessAllowedForEachWord": 10
  }
}
```

* `message` - shows you a welcome message.
* `sessionId` - gives you the ID of the game session which you just started.
* `data`
  * `numberOfWordsToGuess` - tells you how many words you will have to guess to
    finish the game.
  * `numberOfGuessAllowedForEachWord` - tells you how many INCORRECT guess you
    may have for each word.


### 2. Give Me A Word

After getting the session ID, you can ask the server to give you a word.
Remember to include your session ID in the request.

You will get a `word` in the response JSON. The "\*" indicates the letters that
you have to guess in a word. The number of "\*" in the word tells you the number
of letters the word contains.

**Word Difficulty:**

* 1st  to 20th word : length <= 5  letters
* 21st to 40th word : length <= 8  letters
* 41st to 60th word : length <= 12 letters
* 61st to 80th word : length  > 12 letters

#### Request and Response

**Request:**

```javascript
{
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "action" : "nextWord"
}
```

**Response:**

```javascript
{
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "data": {
    "word": "*****",
    "totalWordCount": 1,
    "wrongGuessCountOfCurrentWord": 0
  }
}
```

* `data`
  * `word` - gives you the word you need to guess.
  * `totalWordCount` - tells you the number of words that you have tried.
  * `wrongGuessCountOfCurrentWord` - tells you the number of wrong guess you
    already made on this word.


### 3. Make A Guess

You can make a guess on those letters shown as "\*" in a word.

**IMPORTANT:** You can only guess ONE character per request and only CAPITAL
letter is accepted.

Let's say that the word (`*****`) is indeed "HAPPY". If your letter does exist
in the word, then the response will return you the word with the corresponding
"\*" unveiled (`**PP*`).

But if your guess is incorrect, it will return you the same word. Say that you
make your next guess as "K" and "K" does NOT exist in "HAPPY", you will get the
response as "**PP*".

If you get the word without any "\*", it means that you guess the word correctly.
And you can then get a new word by sending "Give Me A Word" request.

#### Request and Response

**Request:**

```javascript
{
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "action" : "guessWord",
  "guess" : "P"
}
```

**Response:**

```javascript
{
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "data": {
    "word": "**PP*",
    "totalWordCount": 1,
    "wrongGuessCountOfCurrentWord": 0
  }
}
```

### 4. Get Your Result

You can get your result at anytime after starting the game.

#### Request and Response

**Request:**

```javascript
{
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "action" : "getResult"
}
```

**Response:**

```javascript
{
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "data": {
    "totalWordCount": 40,
    "correctWordCount": 20,
    "totalWrongGuessCount": 100,
    "score": 300
  }
}
```

* `data`
  * `totalWordCount` - the total number of words you tried.
  * `correctWordCount` - the total number of words you guess correctly.
  * `totalWrongGuessCount` - the total number of Wrong guess you have made.
  * `score` - your score!


### 5. Submit Your Result

When you finished all the words, you can submit your result.

#### Request and Response

**Request:**

```javascript
{
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "action" : "submitResult"
}
```

**Response:**

```javascript
{
  "message": "GAME OVER",
  "sessionId": "3f0421bb5cb56631c170a35da90161d2",
  "data": {
    "playerId": "test@example.com",
    "sessionId": "3f0421bb5cb56631c170a35da90161d2",
    "totalWordCount": 80,
    "correctWordCount": 77,
    "totalWrongGuessCount": 233,
    "score": 1307,
    "datetime": "2014-10-28 11:45:58"
  }
}
```

### Errors

If something goes wrong, you will receive an error response.

The HTTP status code will be 400, 401, 404, 422 or 500.

The response body contains the error message:

```javascript
{
  "message": "The error message here"
}
```

### Tips

Use a Chrome extension to send request and get response to get yourself familiar
with the flow.


Q&A
---

### Can I skip a word?

Yes! Send another "Give Me A Word" request.

### Can I always start a new game?

Yes! you can always start a new game and play the game again and again.
BUT always remember to submit your results if you are happy with your
performance on a certain game.

### I receive Internal Server Error.

We have a lot of applicants playing the game. Unfortunately, our server is not
made to handle a lot of load at the same time, so it could timeout. As you are
writing a client program, you should handle this case. This is just something
you have to deal with when you write production software.

### Your hangman server is buggy!

We get this comments sometimes. In most cases, it's because the applicants
didn't handle certain cases. Close to a thousand of applicants have played the
game with no issue. So please read this document carefully before you jump into
this conclusion. No software is bug-free though, so if you have carefully
thought about it and still can't proceed, email us.

### Further Questions?

If you have any questions, please email to joyce@strikingly.com. Send us your
HTTP request and response information (if any).


**HAVE FUN!**
