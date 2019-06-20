# discord-giveaways
A complete framework to facilitate the creation of giveaways using discord.js
# Discord Giveaways

Discord Giveaways is a powerful [Node.js](https://nodejs.org) module that allows you to easily create giveaways!

*   The duration of the Giveaway is customizable!
*   Automatic restart after bot crash
*   Update of the timer every X seconds!
*   The strings are fully customizable so you can adapt them to your language!
*   And customizable prize, customizable number of winners, customizable ignored members, and more!

## Installation

```js
npm install --save discord-giveaways
```

## Changelog

*   [2.0.0](https://www.npmjs.com/package/discord-giveaways/v/2.0.0) Full code update
*   [2.0.2](https://www.npmjs.com/package/discord-giveaways/v/2.0.1) Fix error `DiscordAPIError: Invalid Form Body
embed.timestamp`
*   [2.0.3](https://www.npmjs.com/package/discord-giveaways/v/2.0.3) Add `fetch()` function to get the complete list of the giveaways!
*   [2.0.4](https://www.npmjs.com/package/discord-giveaways/v/2.0.4) Add `reroll()` function to reroll a giveaway!
*   [2.0.5](https://www.npmjs.com/package/discord-giveaways/v/2.0.4) Add `edit()` function to edit a giveaway!

## Examples

### Launch of the module

```js
const Discord = require("discord.js"),
giveaways = require("discord-giveaways"),
client = new Discord.Client(),
settings = {
    prefix: "g!",
    token: "Your Discord Token"
};

client.on("ready", () => {
    console.log("I'm ready !");
    giveaways.launch(client, {
        updateCountdownEvery: 5000,
        botsCanWin: false,
        ignoreIfHasPermission: [
            "MANAGE_MESSAGES",
            "MANAGE_GUILD",
            "ADMINISTRATOR"
        ],
        embedColor: "#FF0000",
        reaction: "🎉"
    });
});
```

After that, giveaways that are not yet completed will start to be updated again and new giveaways can be launched.
You can pass a list of options to this method to customize the giveaway. Here is a list of them:

*   **client**: the discord client (your discord bot instance)
*   **options.updateCountdownEvery**: the number of seconds it will take to update the timers
*   **options.botsCanWin**: whether the bots can win a giveaway
*   **options.ignoreIfHasPermission**: an array of discord permissions. Members who have at least one of these permissions will not be able to win a giveaway even if they react to it.
*   **options.embedColor**: a hexadecimal color for the embeds of giveaways.
*   **options.reaction**: the reaction that users will have to react to in order to participate!

### Start a giveaway

```js
client.on("message", (message) => {

    const ms = require("ms"); // npm install ms
    const args = message.content.slice(settings.prefix.length).trim().split(/ +/g);
    const command = args.shift().toLowerCase();

    if(command === "start-giveaway"){
        // g!start-giveaway 2d 1 Awesome prize !
        // will create a giveaway with a duration of two days, with one winner and the prize will be "Awesome prize"

        giveaways.start(message.channel, {
            time: ms(args[0]),
            prize: args.slice(2).join(" "),
            winnersCount: parseInt(args[1])
        }).then((gData) => {
            console.log(gData); // {...} (messageid, end date and more)
        });
        // And the giveaway is started!
    }
});
```

**options.time**: the giveaway duration
**options.prize**: the giveaway prize
**options.winnersCount**: the number of giveaway winners

This allows you to launch a giveaway. Once the `start()` function is called, the giveaway starts and you only have to observe the result, the module does the rest!

<a href="http://zupimages.net/viewer.php?id=19/23/5h0s.png">
    <img src="https://zupimages.net/up/19/23/5h0s.png"/>
</a>

## Fetch the giveaways

```js
    // The list of all the giveaways
    let allGiveaways = giveaways.fetch();

    // The list of all the giveaways on the server with ID "1909282092"
    let onServer = allGiveaways.filter((g) => g.guildID === "1909282092");

    // The list of the current giveaways (not ended)
    let notEnded = allGiveaways.filter((g) => !g.ended);
```
## Reroll a giveaway

```js
client.on("message", (message) => {

    const ms = require("ms"); // npm install ms
    const args = message.content.slice(settings.prefix.length).trim().split(/ +/g);
    const command = args.shift().toLowerCase();

    if(command === "reroll"){
        let messageID = args[0];
        giveaways.reroll(messageID).catch((err) => {
            message.channel.send("No giveaway found for "+messageID+", please check and try again");
        });
    }

});
```

<a href="http://zupimages.net/viewer.php?id=19/24/mhuo.png">
    <img src="https://zupimages.net/up/19/24/mhuo.png"/>
</a>

## Edit a giveaway

```js
client.on("message", (message) => {

    const ms = require("ms"); // npm install ms
    const args = message.content.slice(settings.prefix.length).trim().split(/ +/g);
    const command = args.shift().toLowerCase();

    if(command === "edit"){
        let messageID = args[0];
        let newWinnersCount = args[1];
        let newPrize = args.slice(2).join(" ");
        giveaways.edit(messageID, {
            winnersCount: newWinnersCount,
            prize: newPrize
        });
        message.channel.send("Giveaway updated!");
    }

});
```

**options.winnersCount**: the new number of winners
**options.prize**: the new prize
**options.time**: the new giveaway duration
**options.messages**: the new translation (more information below on translations)

### 🇫🇷 Translation

You can also pass a `messages` parameter for `start()` function, if you want to translate the bot text :

* **options.messages.giveaway**: the message that will be displayed above the embeds
* **options.messages.giveawayEnded**: the message that will be displayed above the embeds when the giveaway is terminated
* **options.messages.timeRemaining**: the message that displays the remaining time (the timer)
* **options.messages.inviteToParticipate**: the message that invites users to participate
* **options.messages.winMessage**: the message that will be displayed to congratulate the winner(s) when the giveaway is terminated
* **options.messages.embedFooter**: the message displayed at the bottom of the embeds
* **options.messages.noWinner**: the message that is displayed if no winner can be drawn
* **options.messages.winners**: simply the word "winner" in your language
* **options.messages.endedAt**: simply the words "Ended at" in your language
* **options.messages.units.seconds**: simply the word "seconds" in your language
* **options.messages.units.minutes**: simply the word "minutes" in your language
* **options.messages.units.hours**: simply the word "hours" in your language
* **options.messages.units.days**: simply the word "days" in your language

For example :

```js
giveaways.start(message.channel, {
    time: ms(args[0]),
    prize: args.slice(2).join(" "),
    winnersCount: parseInt(args[1]),
    messages: {
        giveaway: "@everyone\n\n🎉🎉 **GIVEAWAY** 🎉🎉",
        giveawayEnded: "@everyone\n\n🎉🎉 **GIVEAWAY ENDED** 🎉🎉",
        timeRemaining: "Time remaining: **{duration}**!",
        inviteToParticipate: "React with 🎉 to participate!",
        winMessage: "Congratulations, {winners}! You won **{prize}**!",
        embedFooter: "Giveaways",
        noWinner: "Giveaway cancelled, no valid participations.",
        winners: "winner(s)",
        endedAt: "Ended at",
        units: {
            seconds: "seconds",
            minutes: "minutes",
            hours: "hours",
            days: "days"
        }
    }
});
```

And for the `reroll()` function:

```js
giveaways.reroll(messageID, {
    congrat: ":tada: New winner(s) : {winners}! Congratulations!",
    error: "No valid participations, no winners can be chosen!"
}).catch((err) => {
    message.channel.send("No giveaway found for "+messageID+", please check and try again");
});
```

**options.congrat**: the message of congratulations
**options.error**: the error message if there is no valid participations.