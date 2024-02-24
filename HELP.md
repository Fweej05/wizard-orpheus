# Wizard Orpheus Docs!

WizardOrpheus has 3 functions:

- `var game = new WizardOrpheus(apiKey, gameEnginePrompt`)
  - The Wizard Orpheus function will intitialize your game. You're able to input a prompt that will be fed to the bot and set the rules for the game. 

```
var game = new WizardOrpheus("OPENAI_KEY", `
You're a merchant and you have a customer who is trying to get you to sell your rug for only $100. You need to sell it for $200
`)
```

- `game.variable(variableName, variablePrompt, startingValue)`
  - Create a game variable. Wizard Orpheus will automatically update this variable's value as the game is played, and include its current value in `botAction` functions

```
game.variable('playerHealth', 'Current health of the player, from 0 to 100. Every time something happens where they get hurt (which happens often), this should decrease. They die at 0.', 100)
game.variable('merchantAngerLevel', 'How angry the merchant is, on a scale from 0 to 50. He is very tempermental.', 0)
```

- `game.createUserAction({ name: 'functionName', parameters: ['sentence descriptions of each variable you will pass'], howBotShouldHandle: "description of what the bot should do" })`
  - This generates a function you can call later in your code to trigger actions that happen in the game. Example: "sendMessage", "attack", "explore".

```
game.createUserAction({
  name: 'message',
  parameters: ['The user's message to the merchant'],
  howBotShouldHandle: 'Reply to the user with your own message'
})

game.message('Hello merchant! I will offer 150 for this rug.')

game.createUserAction({
  name: 'attackMerchant',
  parameters: ['The amount of damage the player inflicts on the merchant', 'What the user did to attack'],
  howBotShouldHandle: 'Reply to the user and update variables as needed.'
})

game.attackMerchant(10, 'knife')
```

- `game.botAction(actionName, descriptionOfAction, actionParameters, functionToCall)`
  - This defines something that the bot can do. After each user action, the bot will decide to use one or more of these actions to respond. The `data` object includes all of the variables defined in actionParameters, and also has `data.currentVariables`, which has all of the previously declared `game.variable` vars in it. The way to access them is `data.currentVariables.playerHealth.value` (you need `.value`, you can't do just `data.currentVariables.playerHealth`)

```
game.botAction('reply', 'Send a message to the user', { message: 'The message to display on the screen' }, data => {
  document.body.innerHTML += '<p>' + data.message + '</p>'
})

game.botAction('merchantWifeReply', "The merchant's wife joins the conversation, replies to the user, and calls the merchant an idiot.", { wifeReply: "The wife's reply to the user" }, data => {
  document.body.innerHTML += "<p><i>Merchant's Wife:</i> " + data.wifeReply + "</p>"
})

game.botAction('merchantAttack', "The merchant attacks the user, inflicting damage", {
  attackMethod: "A two sentence description of how the merchant attacks the user.",
  attackWeapon: "A single noun of what the merchant used to attack. Ex. 'candlestick'",
  damage: "A number of how much damage the merchant inflicted on the user"
}, data => {
  document.body.innerHTML += '<p><i>Merchant attacks using <strong>' + data.attackWeapon + '</strong> inflicting <strong>' + data.damage + '</strong></i> - ' + data.attackMethod + '</p>'
})
```