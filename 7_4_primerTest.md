Great job! Now that we have a shell for our first test, let me walk you through how testing works.

Usually, every test has the following phases:

1. set up: in which we define the initial state and initialize the inputs.

2. act: where we actually test the code. Always make sure you test only one thing.

3. assert: where we check the results.

Lets look at what our test should do in some more detail.

In Chapter 2, you learned to create a contract abstraction. 
However, a contract abstraction, as its name says, is just an abstraction. 
In order to actually interact with our smart contract, 
we have to create a JavaScript object that will act as 
an instance of the contract. Continuing our example with 
MyAwesomeContract, we can use the contract abstraction to 
initialize our instance like this:

`const contractInstance = await MyAwesomeContract.new();`

Good, so what's next?

Calling createRandomZombie requires us to pass it the zombie's name as a parameter. So, the next step would be to give a name to Alice's zombie. Something like “Alice’s Awesome Zombie”.

However, if we do this for each test, our code is not going to look pretty. A better approach is to initialize a global array as follows:

const zombieNames = ["Zombie #1", "Zombie #2"];
And then, call the contract's methods like so:

contractInstance.createRandomZombie(zombieNames[0]);
Note: Using an array to store zombies' names comes in handy if you want, for example, to write a test that creates 1000 zombies instead of just one or two😉.

Put it to the test
We've gone ahead and initialized the zombieNames array for you.

Let's create an instance of our contract. Declare a new const named contractInstance, and set it equal to the result of the CryptoZombies.new() function.

CryptoZombies.new() "talks" to the blockchain. This means that it's an asynchronous function. Let's add the await keyword before the function call.


```s
const CryptoZombies = artifacts.require("CryptoZombies");
const zombieNames = ["Zombie 1", "Zombie 2"];
contract("CryptoZombies", (accounts) => {
    let [alice, bob] = accounts;
    it("should be able to create a new zombie", async () => {
        const contractInstance = await CryptoZombies.new()
    })
})

```