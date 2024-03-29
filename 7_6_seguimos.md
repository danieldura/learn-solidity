# Capítulo 6: Keeping the Fun in the Game
Awesome work so far! Now we know for sure our users can create new zombies👌🏻.

However, if they could keep calling this function to create unlimited zombies in their army, the game wouldn't be very fun. Thus, in Chapter 4 of Lesson 2 we've added a require statement to the createZombieFunction() that makes sure each user can't own more than one zombie:

`require(ownerZombieCount[msg.sender] == 0)`

Let's test this feature and see if it works.

Hooks
In just a few minutes🤞, we'll have more than one test and the way this works is that each test should start with a clean sheet. Thus, for every single test we'll have to create a new instance of our smart contract like so:

`const contractInstance = await CryptoZombies.new();`

Wouldn't be nice if you could write this just once and have Truffle run it automatically for every test?

Well... one of Mocha's (and Truffle's) features is the ability to have some snippets of code called hooks run before or after a test. To run something before a test gets executed, the code should be put inside a function named beforeEach().

So, instead of writing contract.new() several times, you just do it once like this:


```s
beforeEach(async () => {
  // let's put here the code that creates a new contract instance
});

```


Then, Truffle will take care of everything. That's pretty sweet, isn't it?


# Put it to the test
1. Below the line of code that initializes alice and bob, let's declare a variable named `contractInstance`. Don't assign it to anything.

Note: We want contractInstance to be limited in scope to the block in which it's defined. Use let instead of var.

2. Next, copy/paste the snippet from above for defining the beforeEach() function.

3. Let's fill in the body of our new function. Go ahead and move the line of code that creates a new contract instance inside of the beforeEach() function. Now that we've defined contractInstance somewhere else, you can remove the const qualifier.

4. We're going to want a new empty it function for our test. Set the name of the test (that is the first parameter we're passing to the it function) equal to "should not allow two zombies".

We'll continue fleshing out this function in the next chapter!


## 🧟‍♂️Here be... zombies of every kind!!!🧟‍♂️
If you really, really want to achieve mastery, go ahead and read on. Otherwise... just click next and off you go to the next chapter.

You still around?😁

Awesome! After all, why would you want to deny yourself a whole lot of awesomeness?

Now, let's circle back to how contract.new works. Basically, every time we call this function, Truffle makes it so that a new contract gets deployed.

On one side, this is helpful because it lets us start each test with a clean sheet.

On the other side, if everybody would create countless contracts the blockchain will become bloated. We want you to hang around, but not your old test contracts!

We would want to prevent this from happening, right?

Happily, the solution is pretty straightforward... our contract should selfdestruct once it's no longer needed.

The way this works is as follows:

- first, we would want to add a new function to the CryptoZombies smart contract like so:

```javascript
function kill() public onlyOwner {
   selfdestruct(owner());
}
```


Note: If you want to learn more about selfdestruct(), you can read the Solidity docs here https://docs.soliditylang.org/en/v0.4.21/introduction-to-smart-contracts.html#self-destruct. The most important thing to bear in mind is that selfdestruct is the only way for code at a certain address to be removed from the blockchain. This makes it a pretty important feature!

next, somewhat similar to the beforeEach() function explained above, we'll make a function called afterEach():

```javascript
afterEach(async () => {
   await contractInstance.kill();
});
```

- Lastly, Truffle will make sure this function is called after a test gets executed.

And voila, the smart contract removed itself!

We have a lot of ground to cover in this lesson, and implementing this feature will likely require at least 2 additional chapters. So, we trust you to add it.💪🏻

```javascript

const CryptoZombies = artifacts.require("CryptoZombies");
const zombieNames = ["Zombie 1", "Zombie 2"];
contract("CryptoZombies", (accounts) => {
    let [alice, bob] = accounts;

    // start here
    let contractInstance;

    beforeEach(async () => {
        contractInstance = await CryptoZombies.new();
    });
    it("should be able to create a new zombie", async () => {
       
        const result = await contractInstance.createRandomZombie(zombieNames[0], {from: alice});
        assert.equal(result.receipt.status, true);
        assert.equal(result.logs[0].args.name,zombieNames[0]);
    })

    //define the new it() function
    it("should not allow two zombies", async () => {

    })
})

```