Question- Say Alice wants to send her zombie to Bob. Shall we test this?

For sure!

If you've been following along with the previous lessons, you should know that, amongst other things, our zombies inherit from ERC721. And the ERC721 specification has 2 different ways to transfer tokens:

`function transferFrom(address _from, address _to, uint256 _tokenId) external payable;`


The first way has Alice (the owner) calling `transferFrom` with her address as the _from parameter, Bob’s address as the _to parameter, and the zombieId she wants to transfer.

(2)


`function approve(address _approved, uint256 _tokenId) external payable;`

followed by

`function transferFrom(address _from, address _to, uint256 _tokenId) external payable;`

The second way has Alice first calling approve with Bob’s address and the zombieId. The contract then stores that Bob is approved to take the zombie. Next, when Alice or Bob calls transferFrom, the contract checks if that msg.sender is equal to Alice’s or Bob’s address. If so, it transfers the zombie to Bob.

We’ll call these two ways of transferring zombies "scenarios". In order to test each scenario, we would want to create two different groups of tests and give them meaningful descriptions.

Why group them? We only have a few tests...

Yes, right now our logic is pretty straightforward, but this might not always be the case. Still, the second scenario (that is approve followed by transferFrom) requires at least two tests:

- first, we must check if Alice herself is able to transfer the zombie.

- second, we also have to check if Bob is allowed to run transferFrom.

Moreover, in the future, you might want to add other functionalities that would require different tests. We believe it is best to put a scaleable structure in place from the very beginning😉. It makes understanding your code much easier for outsiders, or for yourself if you've spent time concentrating on something else for a little while.


> Note: If you end up in a position where you're working with other coders, you'll find they're more likely to follow whichever conventions you've laid down in your initial code. Being able to collaborate effectively is one of the key skills you'll need if you ever want to work on big, successful projects. Getting good habits that help you do this as early on as possible will make your life as a coder easier and more successful.


# The context function
To group tests, Truffle provides a function called context. Let me quickly show you how use it in order to better structure our code:

```javascript
context("with the single-step transfer scenario", async () => {
    it("should transfer a zombie", async () => {
      // TODO: Test the single-step transfer scenario.
    })
})


context("with the two-step transfer scenario", async () => {
    it("should approve and then transfer a zombie when the approved address calls transferFrom", async () => {
      // TODO: Test the two-step scenario.  The approved address calls transferFrom
    })
    it("should approve and then transfer a zombie when the owner calls transferFrom", async () => {
        // TODO: Test the two-step scenario.  The owner calls transferFrom
     })
})

```
If we add this to our CryptoZombies.js file and then run truffle test the output would look similar to this:



