---
id:overwolf-games-events
title: overwolf.games.events API
sidebar_label: overwolf.games.events
---

Notify you when something interesting happens while playing a particular game.  

The full list of supported games with their Game ID’s is always up to date and can be found [here](games-ids).

:::important
Please read all the info about how to use game events in your app in our [using game events](../topics/using-events) guide.
:::

## Methods Reference

* [overwolf.games.events.setRequiredFeatures()](#setrequiredfeaturesfeatures-callback)
* [overwolf.games.events.getInfo()](#getinfocallback)

## Events Reference

* [overwolf.games.events.onError](#onerror)
* [overwolf.games.events.onInfoUpdates2](#oninfoupdates2)
* [overwolf.games.events.onNewEvents](#onnewevents)

## Types Reference

* [overwolf.games.events.SetRequiredFeaturesResult](#setrequiredfeaturesresult-object) Object
* [overwolf.games.events.GetInfoResult](#getinforesult-object) Object

## Sample App

You can find an example of overwolf.games.events API usage [here](https://github.com/overwolf/events-sample-apps/tree/master/lol-events-sample-app).  
This one notifies whenever a relevant event has happened in League of Legends.

## setRequiredFeatures(features, callback)

#### Version added: 0.93

> Sets the required features from the provider.

Parameter | Type     | Description |
--------- | ---------| ------------ |
features  | string[] | String array of features to utilize |
callback  | [(Result:SetRequiredFeaturesResult)](##setrequiredfeaturesresult-object) => void | all available features for the registered games declared in the manifest|

#### Usage Example

Example for setting League of Legends required features:

```javascript
var g_interestedInFeatures = [
  'summoner_info',
  'gameMode',
  'teams',
  'matchState',
  'kill'
];

overwolf.games.events.setRequiredFeatures(g_interestedInFeatures, function(info) {
    if (info.success == false)
    {
      console.log("Could not set required features: " + info.error);
      return;
    }
}
```

:::note
it's important to wait for the **success** status to make sure that required features will be registered and trigger events properly.
:::

Example for setting required features and waiting for 'success':

```js
  async setRequiredFeatures() {
    let tries = 1;
    let result;

    while ( tries <= MAX_RETRIES ) {
      result = await new Promise(resolve => {
        overwolf.games.events.setRequiredFeatures(FEATURES_ARRAY, resolve);
      })

      if ( result.success === true ) {
        // make sure our required features were registered
        return (result.supportedFeatures.length > 0); 
      }

      // wait 3 sec before retry
      await new Promise(resolve => {
        setTimeout(resolve, 3000);
      });
      tries++;
    }

    console.warn('setRequiredFeatures(): failure after '+ tries +' tries'+ JSON.stringify(result, null, 2));
    return false;
  }
```

## getInfo(callback)

#### Version added: 0.95

> Gets the current game info.

Parameter | Type                                                    | Description                    |
--------- | --------------------------------------------------------| ------------------------------ |
callback  | [(Result:GetInfoResult)](#getinforesult-object) => void | Provides the current game info |

#### Usage Example

```javascript
overwolf.games.events.getInfo(function(info) {
   console.log(info);
});
```

## onError

#### Version added: 0.78

> Fired when an error occured in the Game Event system.

## onInfoUpdates2

#### Version added: 0.96

> Fired when there are game info updates with a JSON object of the updates.

**Our best practice is removing event listeners and then adding the listener back to prevent accidental multiple listeners.**

#### Usage Example

```javascript
overwolf.games.events.onInfoUpdates2.addListener(function(info) {
      console.log("Info UPDATE: " + JSON.stringify(info));
});
```

#### Event data example

```json
{  
   "info":{  
      "game_info":{  
         "minionKills":"3"
      }
   },
   "feature":"minions"
}
```

## onNewEvents

#### Version added: 0.96

> Fired when there are new game events with a JSON object of events information.

**Our best practice is removing event listeners and then adding the listener back to prevent accidental multiple listeners.**

#### Usage Example

```javascript
overwolf.games.events.onNewEvents.addListener(function(info) {
   console.log('EVENT FIRED: ' + JSON.stringify(info));  
});
```

#### Event data example

```json
{
  "events": [
    {
      "name": "death",
      "data": "{`count`: `2`}"
    }
  ]
}
```

## SetRequiredFeaturesResult Object

Parameter          | Type     | Description                                                              |
-------------------| ---------| ------------------------------------------------------------------------ |
success            | boolean  |                                                                          |
error              | string   | null if success is true                                                  |
supportedFeatures  | string[] | all available features for the registered games declared in the manifest |   

#### Example data: Success

```json
{  
   "success":true,
   "supportedFeatures":[  
      "summoner_info",
      "teams",
      "kill"
   ]
}
```

## GetInfoResult Object

Parameter          | Type     | Description                                                              |
-------------------| ---------| ------------------------------------------------------------------------ |
success            | boolean  |                                                                          |
error              | string   | null if success is true                                                  |
res                | object   | Provides the current game info                                           |   

#### Example data: Success

The current game's info, registered features, and all available info for the current game session.

```json
{  
   "success":true,
   "res":{  
      "summoner_info":{  
         "id":"79489298",
         "name":"itaygl",
         "region":"EUW",
         "champion":"Rengar"
      },
      "game_info":{  
         "match_started":"True",
         "matchStarted":"True",
         "teams":"%5B%7B%22team%22:%22100%22,%22champion%22:%22Rengar%22",
         "gameMode":"custom",
         "game_mode":"custom",
         "minionKills":"5",
         "minions_kills":"5",
         "gold":"1002"
      },
      "features":{  
         "kill":"True",
         "assist":"True",
         "minions":"True",
         "deathAndRespawn":"True",
         "death":"True",
         "minion":"True",
         "gold":"True",
         "level":"True",
         "abilities":"True",
         "gameMode":"True",
         "game_mode":"True"
      },
      "level":{  
         "level":"3"
      }
   }
}
```