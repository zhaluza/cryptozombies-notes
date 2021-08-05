# Cryptozombies Notes

Outlines core Solidity concepts covered in the
[Cryptozombies online course](https://cryptozombies.io/). Consists of a mixture
of lesson text and original summaries.

Check out the [Solidity Cheatsheet](cheatsheet.md) for a quick summary of some
of these concepts.

## Resources

- [Official Solidity Cheatsheet](https://docs.soliditylang.org/en/v0.8.6/cheatsheet.html)

## Table of Contents

- [Lesson 1](#lesson-1-solidity-basics)
- [Lesson 2](#lesson-2)

## Lesson 1: Solidity Basics

### [Contracts (Chapter 2)](https://cryptozombies.io/en/lesson/1/chapter/2)

Solidity's code is encapsulated in contracts. A contract is the fundamental
building block of Ethereum applications — all variables and functions belong to
a contract, and this will be the starting point of all your projects.

An empty contract named HelloWorld would look like this:

```solidity
contract HelloWorld {

}
```

All solidity source code should start with a **"version pragma"** — a
declaration of the version of the Solidity compiler this code should use. This
is to prevent issues with future compiler versions potentially introducing
changes that would break your code.

Here's an example of a smart contract that will be compiled by any compiler
with a version 0.7.3 or higher:

```solidity
pragma solidity >=0.7.3;

contract HelloWorld {

}
```

### [State Variables & Integers (Chapter 3)](https://cryptozombies.io/en/lesson/1/chapter/3)

#### State Variables

State variables are _permanently stored_ in contract storage. They're written
to the Ethereum blockchain. Permanently. (Unlike data stored in `memory`, which
we'll discuss later.)

When we work with smart contracts, we store and access data from the blockchain
rather than traditional databases. Therefore, using state variables in our
contracts is like the equivalent of storing this information in a database.

```solidity
contract Example {
  // This will be stored permanently in the blockchain
  uint myUnsignedInteger = 100;
}
```

In this example contract, we created a `uint` called `myUnsignedInteger` and
set it equal to 100.

#### Unsigned Integers: `uint`

The `uint` data type is an _unsigned integer_, meaning its value must be
_non-negative_. For signed integers, you can use the `int` type.

`uint` is actually equivalent to `uint256`, a 256-bit unsigned integer. You can
also use uints with fewer bits (`uint8`, `uint16`, `uint32`...) to be more
cost-efficient, which we'll also discuss later.

### [Math Operations (Chapter 4)](https://cryptozombies.io/en/lesson/1/chapter/4)

Solidity supports the same math operations you'd expect from a programming
language:

- Addition: x + y
- Subtraction: x - y,
- Multiplication: x \* y
- Division: x / y
- Modulus / remainder: x % y
- Exponents: x \*\* 2

### [Structs (Chapter 5)](https://cryptozombies.io/en/lesson/1/chapter/5)

A `struct` is a complex data type that has multiple properties:

```solidity
struct Person {
  uint age;
  string name;
}
```

### [Arrays (Chapter 6)](https://cryptozombies.io/en/lesson/1/chapter/6)

Arrays in Solidity are similar to arrays in JavaScript.

Solidity has two types of arrays:

- **Fixed arrays**: Have fixed lengths.
- **Dynamic arrays**: Have no fixed size and can contain any number of
  elements.

```solidity
// Array with a fixed length of 2 elements:
uint[2] fixedArray;
// another fixed Array, can contain 5 strings:
string[5] stringArray;
// a dynamic Array - has no fixed size, can keep growing:
uint[] dynamicArray;
```

You can also create an array of structs. Remember the `Person` struct from the
last lesson?

```solidity
Person[] people; // dynamic Array, we can keep adding to it
```

Since state variables are stored permanently in the blockchain, creating a
dynamic array of structs like this can be useful for storing structured data in
your contract — like a database, remember?

#### Public Arrays

You can declare an array as `public` , and Solidity will automatically create a
`getter` method for it. Here's what the syntax looks like:

```solidity
Person[] public people;
```

Other contracts could then **read** from this array, but _not_ **write** to it.
This makes it a useful pattern for storing public data in your contracts.

### [Function Declarations](https://cryptozombies.io/en/lesson/1/chapter/7)

Function declarations in Solidity look like this:

```solidity
function eatHamburgers(string memory _name, uint _amount) public {

}
```

The above function, `eatHamburgers`, takes two parameters:

- A `string` called `_name`
- A `uint` called `_amount`

Note that the function is `public`, meaning that it can be called internally or
via messages (externally).

_Why do both parameters start with an underscore?_ It's convention to start a
function parameter variable name with an underscore (e.g. `_name` instead of
`name`) _to differentiate them from global variables._

#### Reference Types

In Solidity, a `string` is a reference type, while a `uint` is a value type.

There are two ways to pass arguments to Solidity functions:

- By **value** - the solidity creates a new copy of the param's value and
  passes it to the function. The value of the original parameter is not
  changed.
- By **reference** - The functions is called with a reference to the original
  value. If the function changes the value of the variable received, the
  original variable is also mutated.

### [Working with Structs & Arrays](https://cryptozombies.io/en/lesson/1/chapter/8)

Let's look back at the `Person` struct we created earlier:

```solidity
struct Person {
  uint age;
  string name;
}

Person[] public people;
```

Now let's create new `Person`s and add them to the `people` array:

```solidity
// create a New Person:
Person satoshi = Person(172, "Satoshi");

// Add that person to the Array:
people.push(satoshi);
```

Let's do this all in one line:

```solidity
people.push(Person(172, "Satoshi"));
```

Note that `array.push()` adds items to the end of an array.

```solidity
uint[] numbers;
numbers.push(5);
numbers.push(10);
numbers.push(15);
// numbers is now equal to [5, 10, 15]
```

### [Private & Public Functions](https://cryptozombies.io/en/lesson/1/chapter/9)

In Solidity, functions are `public` by default. This means that anyone (or any
other contract) can call your contract's function and execute its code.

This can create security concerns, as it potentially leaves your contract
vulnerable to attacks. It's a good practice to make your functions `private` by
default (meaning that they can only be accessed from within the same contract)
and then only mark a function public if you want to expose it to everyone.

Here's an example of how to declare a private function:

```solidity
uint[] numbers;

function _addToArray(uint _number) private {
  numbers.push(_number);
}
```

Only functions within this same contract will be able to call `_addToArray` and
add to the `numbers` array.

Note the `_` at the beginning of `\_addToArray. Just like with function
parameters, it's customary to start private functions with an underscore.

### [Internal and External Functions](https://cryptozombies.io/en/lesson/2/chapter/9)

Solidity also provides us with two more types of visibility for functions:
`internal` and `external`.

`internal` is the same as `private`, but with one bonus: it's also accessible
to contracts that inherit from this contract.

`external` is similar to `public`, but `external` functions can **only** be
called outside the contract. Unlike `public`, they can't be called by other
functions inside that contract.

### [More on Functions](https://cryptozombies.io/en/lesson/1/chapter/10)

#### Return values

If a function returns a value, you should also specify the type when declaring
the function:

```solidity
string greeting = "What's up dog";

function sayHello() public returns (string memory) {
  return greeting;
}
```

#### Function modifiers

The above function (`sayHello`) doesn't modify state — it just returns a
pre-existing state variable. Therefore, it can be declared a `view` function:

```solidity
string greeting = "What's up dog";

function sayHello() public view returns (string memory) {
  return greeting;
}
```

You can also write `pure` functions, which don't access any data in the app.

```solidity
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

### [Keccak256 & Typecasting](https://cryptozombies.io/en/lesson/1/chapter/11)

#### `keccak256`

`keccak256` is a hash function built into Ethereum and is a version of SHA3. It
has many purposes in Ethereum, but we'll use it to randomly generate a number.

Note hat `keccak256` expects a single parameter of type `bytes`. This requires
us to "pack" our parameters before calling `keccak256`:

```solidity
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256(abi.encodePacked("aaaab"));
//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9
keccak256(abi.encodePacked("aaaac"));
```

### Typecasting

Solidity lets you convert between data types. For example:

```solidity
uint8 a = 5;
uint b = 6;
// throws an error because a * b returns a uint, not uint8:
uint8 c = a * b;
// we have to typecast b as a uint8 to make it work:
uint8 c = a * uint8(b);
```

## [Events](https://cryptozombies.io/en/lesson/1/chapter/13)

Events let your contract communicate with your app's frontend, which can listen
for certain events and perform actions when they happen.

```solidity
// declare the event
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
  // fire an event to let the app know the function was called:
  emit IntegersAdded(_x, _y, result);
  return result;
}
```

A JavaScript implementation for this event on the frontend could look something
like this:

```javascript
YourContract.IntegersAdded(function (error, result) {
  // do something with result
});
```

## [Web3.js](https://cryptozombies.io/en/lesson/1/chapter/14)

Ethereum has a JavaScript library called
[Web3.js](https://web3js.readthedocs.io/en/v1.2.11/index.html).

We'll go into this later, but here's an example of how a frontend could use the
library to interact with a smart contract.

**Contract**

```solidity
pragma solidity ^0.4.25;

contract ZombieFactory {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        emit NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(abi.encodePacked(_str)));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }

}
```

**Frontend, using Web3.js**

```javascript
// Here's how we would access our contract:
var abi = /* abi generated by the compiler */
var ZombieFactoryContract = web3.eth.contract(abi)
var contractAddress = /* our contract address on Ethereum after deploying */
var ZombieFactory = ZombieFactoryContract.at(contractAddress)
// `ZombieFactory` has access to our contract's public functions and events

// some sort of event listener to take the text input:
$("#ourButton").click(function(e) {
  var name = $("#nameInput").val()
  // Call our contract's `createRandomZombie` function:
  ZombieFactory.createRandomZombie(name)
})

// Listen for the `NewZombie` event, and update the UI
var event = ZombieFactory.NewZombie(function(error, result) {
  if (error) return
  generateZombie(result.zombieId, result.name, result.dna)
})

// take the Zombie dna, and update our image
function generateZombie(id, name, dna) {
  let dnaStr = String(dna)
  // pad DNA with leading zeroes if it's less than 16 characters
  while (dnaStr.length < 16)
    dnaStr = "0" + dnaStr

  let zombieDetails = {
    // first 2 digits make up the head. We have 7 possible heads, so % 7
    // to get a number 0 - 6, then add 1 to make it 1 - 7. Then we have 7
    // image files named "head1.png" through "head7.png" we load based on
    // this number:
    headChoice: dnaStr.substring(0, 2) % 7 + 1,
    // 2nd 2 digits make up the eyes, 11 variations:
    eyeChoice: dnaStr.substring(2, 4) % 11 + 1,
    // 6 variations of shirts:
    shirtChoice: dnaStr.substring(4, 6) % 6 + 1,
    // last 6 digits control color. Updated using CSS filter: hue-rotate
    // which has 360 degrees:
    skinColorChoice: parseInt(dnaStr.substring(6, 8) / 100 * 360),
    eyeColorChoice: parseInt(dnaStr.substring(8, 10) / 100 * 360),
    clothesColorChoice: parseInt(dnaStr.substring(10, 12) / 100 * 360),
    zombieName: name,
    zombieDescription: "A Level 1 CryptoZombie",
  }
  return zombieDetails
}
```

## Lesson 2

### [Mappings & Addresses](https://cryptozombies.io/en/lesson/2/chapter/2)

#### Addresses

The Ethereum blockchain is made up of accounts (like bank accounts). Each
account has a balance of [Ether](https://en.wikipedia.org/wiki/Ethereum#Ether).
You can send and receive Ether payments to other accounts (just like a bank
account can wire money to and receive transfers from other accounts).

Each account has an `address` — similar to an account number. It looks like
this:

```
0x0cE446255506E92DF41614C46F1d6df9Cc969183
```

For now, know that each address is owned by a specific **user** or **smart
contract**.

#### Mappings

We've already covered **structs** and **arrays**. **Mappings** are another way
to store organized data in Solidity. They're essentially key-value pairs, like
JavaScript objects with only one entry.

```solidity
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

In the first example, the _key_ is an `address`, and the _value_ is a `uint`.
In the second example, the _key_ is a `uint`, and the _value_ is a `string`.

### [Msg.sender](https://cryptozombies.io/en/lesson/2/chapter/3)

In Solidity, there are certain **global variables** that are available to all
functions. One of these is `msg.sender`, which refers to the `address` of the
_person_ or _smart contract_ who called the current function.

`msg.sender` will always exist when calling a function, since all Solidity
function executions start with an external caller.

Here's an example of using `msg.sender` and updating a mapping:

```solidity
mapping (address => uint) favoriteNumber;

function setMyNumber(uint _myNumber) public {
  // Update our `favoriteNumber` mapping to store `_myNumber` under `msg.sender`
  favoriteNumber[msg.sender] = _myNumber;
  // ^ The syntax for storing data in a mapping is just like with arrays
}

function whatIsMyNumber() public view returns (uint) {
  // Retrieve the value stored in the sender's address
  // Will be `0` if the sender hasn't called `setMyNumber` yet
  return favoriteNumber[msg.sender];
}
```

In this example, anyone could call `setMyNumber` and store a `uint` in our
contract, which would then be mapped to their address. When they then call
`whatIsMyNumber`, the function will return the `uint` that they stored.

Using `msg.sender` gives you the security of the Ethereum blockchain. A
malicious party can only modify someone else's data if they steal the private
key associated with their Ethereum address.

### [Require](https://cryptozombies.io/en/lesson/2/chapter/4)

A `require` statement forces a function to throw an error and stop executing if
some condition is not true.

```solidity
function sayHiToVitalik(string memory _name) public returns (string memory) {
  // Compares if _name equals "Vitalik". Throws an error and exits if not true.
  // (Side note: Solidity doesn't have native string comparison, so we
  // compare their keccak256 hashes to see if the strings are equal)
  require(keccak256(abi.encodePacked(_name)) == keccak256(abi.encodePacked("Vitalik")));
  // If it's true, proceed with the function:
  return "Hi!";
}
```

If you call this function with `sayHiToVitalik("Vitalik")`, it will return
"Hi!". If you call it with any other input, it will throw an error and not
execute.

Therefore require is quite useful for verifying certain conditions that must be
true before running a function.

### [Inheritance](https://cryptozombies.io/en/lesson/2/chapter/5)

To avoid cramming a single contract full of related functions and logic, you
can split your code across multiple contracts. Solidity lets you do this easily
with contract **inheritance**.

```solidity
contract Doge {
  function catchphrase() public returns (string memory) {
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Doge {
  function anotherCatchphrase() public returns (string memory) {
    return "Such Moon BabyDoge";
  }
}
```

BabyDoge inherits from Doge. That means if you compile and deploy BabyDoge, it
will have access to both catchphrase() and anotherCatchphrase() (and any other
public functions we may define on Doge).

This can be used for logical inheritance (such as with a subclass, a Cat is an
Animal). But it can also be used simply for organizing your code by grouping
similar logic together into different contracts.

### [Import](https://cryptozombies.io/en/lesson/2/chapter/6)

If you need to refer to functions in another contract (e.g. if you're creating
a contract that inherits from another, or if you need to access a public
function from a contract), you can use the `import` keyword.

```solidity
import "./someothercontract.sol";

contract newContract is SomeOtherContract {

}
```

### [Storage vs. Memory (Data Location)](https://cryptozombies.io/en/lesson/2/chapter/7)

In Solidity, there are two locations you can store variables — in storage and
in memory.

Storage refers to variables stored permanently on the blockchain. Memory
variables are temporary, and are erased between external function calls to your
contract. Think of it like your computer's hard disk vs RAM.

Solidity handles these by default, so you _usually_ don't need to use them.

- _State variables_ (variables declared outside functions) are by default
  `storage` and written permanently to the blockchain.
- _Variables declared inside functions_ are `memory` and will disappear when
  the function call ends.

However, you need to use these keywords when dealing with **structs** and
**arrays** within functions:

```solidity
contract SandwichFactory {
  struct Sandwich {
    string name;
    string status;
  }

  Sandwich[] sandwiches;

  function eatSandwich(uint _index) public {
    // Sandwich mySandwich = sandwiches[_index];

    // ^ Seems pretty straightforward, but solidity will give you a warning
    // telling you that you should explicitly declare `storage` or `memory` here.

    // So instead, you should declare with the `storage` keyword, like:
    Sandwich storage mySandwich = sandwiches[_index];
    // ...in which case `mySandwich` is a pointer to `sandwiches[_index]`
    // in storage, and...
    mySandwich.status = "Eaten!";
    // ...this will permanently change `sandwiches[_index]` on the blockchain.

    // If you just want a copy, you can use `memory`:
    Sandwich memory anotherSandwich = sandwiches[_index + 1];
    // ...in which case `anotherSandwich` will simply be a copy of the
    // data in memory, and...
    anotherSandwich.status = "Eaten!";
    // ...will just modify the temporary variable and have no effect
    // on `sandwiches[_index + 1]`. But you can do this:
    sandwiches[_index + 1] = anotherSandwich;
    // ...if you want to copy the changes back into blockchain storage.
  }
}
```

### [Interacting With Other Contracts](https://cryptozombies.io/en/lesson/2/chapter/10)

For one of your contracts to talk to another contract on the blockchain that
you don't own, you need to define an **interface**.

Here's a simple example. Let's imagine that there's a contract on the
blockchain called `LuckyNumber`:

```solidity
contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}
```

This is a simple contract where you can store your lucky number and associate
it with your Ethereum address. Anyone could then look up your lucky number
using your address.

Now let's imagine we have a contract that wants to read the data in this
contract using the _public_ `getNum` function.

First, we need to define an **interface** of the `LuckyNumber` contract:

```solidity
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
```

This looks _a lot_ like we're defining a contract, but there are a few
differences:

- We're only declaring the functions we want to interact with — in this case
  `getNum` — and we don't mention any of the other functions or state
  variables.
- We're not defining the function bodies. Instead of curly braces (`{` and
  `}`), we're simply ending the function declaration with a semi-colon (`;`).

It does look like a contract skeleton. This is how the compiler knows it's an
interface.

By including this interface in our dapp's code our contract knows what the
other contract's functions look like, how to call them, and what sort of
response to expect.

### [Using an Interface](https://cryptozombies.io/en/lesson/2/chapter/11)

Now that we've defined our `NumberInterface` (see the above section), we can
use it in a contract:

```solidity
contract MyContract {
  address NumberInterfaceAddress = 0xab38...
  // ^ The address of the FavoriteNumber contract on Ethereum
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
  // Now `numberContract` is pointing to the other contract

  function someFunction() public {
    // Now we can call `getNum` from that contract:
    uint num = numberContract.getNum(msg.sender);
    // ...and do something with `num` here
  }
}
```

Using this pattern, our contracts can interact with any other contract on the
Ethereum blockchain, as long as they expose functions as `public` or
`external`.
