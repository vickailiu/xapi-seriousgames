# Note

This is the WIP specification for serious game. It is adapted from the generic xAPI Profile. Resources useful:

- xAPI Profile (https://github.com/adlnet/xapi-profiles)
- xAPI Profile for Serious game (http://xapi.vocab.pub/describe/?url=https://w3id.org/xapi/seriousgames)
- Overview (https://github.com/e-ucm/rage-analytics/wiki/Experience-API-%28xAPI%29-Statements)
- Experience API Serious Games (xAPI-SG) Profile (https://github.com/e-ucm/rage-analytics/wiki/xAPI-SG-Profile)

The game developer are required to share **list of objects and corresponding actions**, and **game score logic** for review.

# Serious Games Interactions Model

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [1. Interactions Model](#1-interactions-model)
- [2. Overview](#2-overview)
- [3. xAPI Mapping](#3-xapi-mapping)
  - [3.1. Objects](#31-objects)
  - [3.2. Verbs](#32-verbs)
  - [3.3 Values](#33-values)
  - [3.4 Result](#34-result)
- [4. Objects](#4-objects)
  - [4.1. Completable](#41-completable)
    - [4.1.1. Predefined types](#411-predefined-types)
    - [4.1.2. Actions](#412-actions)
      - [4.1.2.1. initialized](#4121-initialized)
      - [4.1.2.2. progressed _progress_](#4122-progressed-_progress_)
      - [4.1.2.3. completed _ending_](#4123-completed-_ending_)
    - [4.1.3. Requirements and considerations](#413-requirements-and-considerations)
    - [4.1.4. Metrics](#414-metrics)
  - [4.2. Reachable](#42-reachable)
    - [4.2.1. Predefined types](#421-predefined-types)
    - [4.2.2. Actions](#422-actions)
      - [4.2.2.1. accessed](#4221-accessed)
      - [4.2.2.2 skipped](#4222-skipped)
    - [4.2.3. Requirements and considerations](#423-requirements-and-considerations)
    - [4.2.4. Metrics](#424-metrics)
  - [4.3. Variable](#43-variable)
  - [4.3.1. Predefined types](#431-predefined-types)
    - [4.3.2. Actions](#432-actions)
      - [4.3.2.1. set _value_](#4321-set-_value_)
      - [4.3.2.2. increased/decreased _value_](#4322-increaseddecreased-_value_)
    - [4.3.3. Requirements and considerations](#433-requirements-and-considerations)
    - [4.3.4. Metrics](#434-metrics)
  - [4.4. Alternative](#44-alternative)
    - [4.4.1. Predefined types](#441-predefined-types)
    - [4.4.2. Actions](#442-actions)
      - [4.4.2.1. selected](#4421-selected)
      - [4.4.2.2. unlocked](#4422-unlocked)
    - [4.4.3. Requirements and considerations](#443-requirements-and-considerations)
    - [4.4.4. Metrics](#444-metrics)
  - [4.5. Device](#45-device)
    - [4.5.1. Predefined types](#451-predefined-types)
    - [4.5.2. Actions](#452-actions)
      - [4.5.2.1. pressed _button/key/position_](#4521-pressed-_buttonkeyposition_)
      - [4.5.2.2. released _button/key/position_](#4522-released-_buttonkeyposition_)
    - [4.5.3. Requirements and considerations](#453-requirements-and-considerations)
    - [4.5.4. Metrics](#454-metrics)
  - [4.6. Target](#46-target)
    - [4.6.1. Predefined Types](#461-predefined-types)
    - [4.6.2. Actions](#462-actions)
      - [4.6.2.1. interacted](#4621-interacted)
      - [4.6.2.2. used](#4622-used)
      - [4.6.2.3. touched _position_](#4623-touched-_position_)
      - [4.6.2.4. collected](#4624-collected)
    - [4.6.3. Requirements and considerations](#463-requirements-and-considerations)
    - [4.6.4. Metrics](#464-metrics)
  - [4.7. Event](#47-event)
    - [4.7.1. Predefined types](#471-predefined-types)
    - [4.7.2. Actions](#472-actions)
      - [4.7.2.1. performed](#4721-performed)
    - [4.7.3. Requirements and considerations](#473-requirements-and-considerations)
    - [4.7.4. Metrics](#474-metrics)
  - [4.8. Compound interaction](#48-compound-interaction)
    - [4.8.1. Predefined types](#481-predefined-types)
    - [4.8.2. Actions](#482-actions)
      - [4.8.2.1 began](#4821-began)
      - [4.8.2.2 ended](#4822-ended)
    - [4.8.3. Requirements and considerations](#483-requirements-and-considerations)
    - [4.8.4. Metrics](#484-metrics)
  - [4.9. Measure](#49-measure)
    - [4.9.1. Predefined types](#491-predefined-types)
    - [4.9.2. Actions](#492-actions)
      - [4.9.2.1. measured](#4921-measured)
    - [4.9.3. Requirements and considerations](#493-requirements-and-considerations)
    - [4.9.4. Metrics](#494-metrics)
  - [4.10. Game Message](#410-game-message)
    - [4.10.1. Predefined types](#4101-predefined-types)
    - [4.10.2. Actions](#4102-actions)
      - [4.10.2.1 threw](#41021-threw)
    - [4.10.3. Requirements and considerations](#4103-requirements-and-considerations)
    - [4.10.4. Metrics](#4104-metrics)
- [5. Example statements](#5-example-statements)
  - [5.1 Completable](#51-completable)
  - [5.2 Reachable](#52-reachable)
  - [5.3. Variables](#53-variables)
  - [5.4. Alternatives](#54-alternatives)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 1. Interactions Model

A serious game is defined by a finite set of **game objects**. A game object represents an element of the game on which players can perform one or several types of interactions. Some examples of player's interactions are:

* start or complete (interaction) a level (game object)
* increase or decrease (interaction) the number of coins (game object)
* select or unlock (interaction) a power-up (game object)

A **gameplay** is the flow of interactions that a player performs over these game objects in a sequential order.

Using [JsonSchema](http://json-schema.org/), a single interaction has the following structure:

```json
{
	"title": "Interaction",
	"type": "object",
	"properties": {
		"player": {
			"type": "object",
			"description": "The player that generated the interaction"
		},
		"action": {
			"type": "string",
			"description": "The type of interaction performed by the player"
		},
		"object": {
			"type": "string",
			"description": "Objective of the player's action"
		},
		"value": {
			"type": "object",
			"description": "Parameters of the action"
		},
		"timestamp": {
			"type": "string",
			"description": "Date and time at which the interaction occurred, formatted according to ISO 8601 including its time zone."
		},
		"result": {
			"score": {
				"scaled": 1,
				"min": 0,
				"max": 100,
				"raw": 100
			},
			"completion": true,
			"success": true,
			"response": "Example string",
			"duration": "P1DT12H",
			"extensions": {
				"http://www.example.com/ext": true
			}
		},
	},
	"required": ["player", "action", "object", "timestamp"]
}
```

The rest of the document defines the set of game objects and interactions considered by the RAGE Serious Games Interactions Model.


# 2. Overview

**Actions included in the xAPI profile**

Object | Action | Value | Value mandatory | Value Type | Value constraints | Result |
---------|--------|-------|----------|------|------------|--------|
**Completable**| _initialized_ | No
 | | _progressed_ | Progress | Yes | Float | between `[0, 1]`
 | | _completed_ | Ending Identifier | No | String | - |
**Reachable**| _accessed_ | No
 | | _skipped_ | No
**Alternative** | _selected_ | Option Id. | Yes | String |
 | | _unlocked_ | Option Id. | Yes | String | 
**Device** | _pressed_ | Press value (button, key, position) | Yes | Integer, Position | Position must be in world coordinates |
 | | _released_ | Press value (button, key, position) | Yes | String, Position | Position must be in world coordinates |
**Target** | _interacted_ | No
 | | _used_ | No

**Actions under consideration**

Object | Action | Value | Value mandatory | Value Type | Value constraints | Result |
---------|--------|-------|----------|------|------------|--------|
**Variable** | _set_ | value | Yes | Boolean, Number, String |
 | | _increased_ | value | Yes | Number |
 | | _decreased_ | value | Yes | Number |
**Target** | _touched_ (clicked) | Touch position | No | Position | Position must be in world coordinates |
 | | _collected_ | No
**Event** | _performed_ | Custom value
**Compound interaction**| _began_ | Compound interaction Id. | Yes | String 
 | | _ended_ | No
**Measure**| _measured_ | Measure value | Yes | String
**Game Message**| _threw_ | Error message | No | String

# 3. xAPI Mapping

## 3.1. Objects

**Objects included in the xAPI Profile**

Identifier | IRI|
-------|----|
Serious-game | https://w3id.org/xapi/seriousgames/activity-types/serious-game |
Level | https://w3id.org/xapi/seriousgames/activity-types/level |
Quest | https://w3id.org/xapi/seriousgames/activity-types/quest |
Screen | https://w3id.org/xapi/seriousgames/activity-types/screen |
Area | https://w3id.org/xapi/seriousgames/activity-types/area |
Zone | https://w3id.org/xapi/seriousgames/activity-types/zone |
Cutscene | https://w3id.org/xapi/seriousgames/activity-types/cutscene |
Health | https://w3id.org/xapi/seriousgames/extensions/health |
Position | https://w3id.org/xapi/seriousgames/extensions/position |
Question | http://adlnet.gov/expapi/activities/question |
Menu | https://w3id.org/xapi/seriousgames/activity-types/menu |
Dialog-tree | https://w3id.org/xapi/seriousgames/activity-types/dialog-tree |
Mouse | https://w3id.org/xapi/seriousgames/activity-types/mouse |
Keyboard | https://w3id.org/xapi/seriousgames/activity-types/keyboard |
Controller | https://w3id.org/xapi/seriousgames/activity-types/controller |
Touchscreen | https://w3id.org/xapi/seriousgames/activity-types/touchscreen |
Enemy | https://w3id.org/xapi/seriousgames/activity-types/enemy |
Non-player-character | https://w3id.org/xapi/seriousgames/activity-types/non-player-character |
Item | https://w3id.org/xapi/seriousgames/activity-types/item |

**Objects under consideration**

Identifier | IRI|
-------|----|
Session | https://rage.e-ucm.es/xapi/seriousgames/activities/Session |
Stage | https://rage.e-ucm.es/xapi/seriousgames/activities/Stage |
Combat | https://rage.e-ucm.es/xapi/seriousgames/activities/Combat |
Story Node | https://rage.e-ucm.es/xapi/seriousgames/activities/StoryNode |
Race | https://rage.e-ucm.es/xapi/seriousgames/activities/Race |
Completable | https://rage.e-ucm.es/xapi/seriousgames/activities/Completable |
Reachable | https://rage.e-ucm.es/xapi/seriousgames/activities/Reachable |
Score | https://rage.e-ucm.es/xapi/seriousgames/activities/Score |
Currency | https://rage.e-ucm.es/xapi/seriousgames/activities/Currency |
Attempt | https://rage.e-ucm.es/xapi/seriousgames/activities/Attempt |
Variable | https://rage.e-ucm.es/xapi/seriousgames/activities/Variable |
Path | https://rage.e-ucm.es/xapi/seriousgames/activities/Path |
Arena | https://rage.e-ucm.es/xapi/seriousgames/activities/Arena |
Alternative | https://rage.e-ucm.es/xapi/seriousgames/activities/Alternative |
Mouse Button 1 | https://rage.e-ucm.es/xapi/seriousgames/activities/MouseButton1 |
Mouse Button 2 | https://rage.e-ucm.es/xapi/seriousgames/activities/MouseButton2 |
Touch Screen N | https://rage.e-ucm.es/xapi/seriousgames/activities/TouchScreenN |
UI | https://rage.e-ucm.es/xapi/seriousgames/activities/UI |
Weapon | https://rage.e-ucm.es/xapi/seriousgames/activities/Weapon |
Vehicle | https://rage.e-ucm.es/xapi/seriousgames/activities/Vehicle |
Memory | https://rage.e-ucm.es/xapi/seriousgames/activities/Memory |
CPU | https://rage.e-ucm.es/xapi/seriousgames/activities/CPU |
Frame rate | https://rage.e-ucm.es/xapi/seriousgames/activities/Framerate |
Load time | https://rage.e-ucm.es/xapi/seriousgames/activities/Loadtime |
Info | https://rage.e-ucm.es/xapi/seriousgames/activities/Info |
Debug | https://rage.e-ucm.es/xapi/seriousgames/activities/Debug |
Warning | https://rage.e-ucm.es/xapi/seriousgames/activities/Warning |
Error | https://rage.e-ucm.es/xapi/seriousgames/activities/Error |
Critical | https://rage.e-ucm.es/xapi/seriousgames/activities/Critical |

## 3.2. Verbs

**Verbs included in the xAPI Profile**

Identifier | IRI|
-------|----|
initialized | http://adlnet.gov/expapi/verbs/initialized |
progressed | http://adlnet.gov/expapi/verbs/progressed |
completed | http://adlnet.gov/expapi/verbs/completed |
accessed | https://w3id.org/xapi/seriousgames/verbs/accessed |
skipped | http://id.tincanapi.com/verb/skipped |
selected | https://w3id.org/xapi/adb/verbs/selected |
unlocked | https://w3id.org/xapi/seriousgames/verbs/unlocked |
pressed | https://w3id.org/xapi/seriousgames/verbs/pressed |
released | https://w3id.org/xapi/seriousgames/verbs/released |
interacted | http://adlnet.gov/expapi/verbs/interacted |
used | https://w3id.org/xapi/seriousgames/verbs/used |

**Verbs under consideration**

Identifier | IRI|
-------|----|
set | https://rage.e-ucm.es/xapi/seriousgames/verbs/set |
increased | https://rage.e-ucm.es/xapi/seriousgames/verbs/increased |
decreased | https://rage.e-ucm.es/xapi/seriousgames/verbs/decreased |
touched | http://future-learning.info/xAPI/verb/pressed |
killed | https://rage.e-ucm.es/xapi/seriousgames/verbs/killed |
died because | https://rage.e-ucm.es/xapi/seriousgames/verbs/died |
collected | https://rage.e-ucm.es/xapi/seriousgames/verbs/collected |
performed | https://rage.e-ucm.es/xapi/seriousgames/verbs/performed |
began | https://rage.e-ucm.es/xapi/seriousgames/verbs/began |
ended | https://rage.e-ucm.es/xapi/seriousgames/verbs/ended |
measured | https://rage.e-ucm.es/xapi/seriousgames/verbs/measured |
threw | https://rage.e-ucm.es/xapi/seriousgames/verbs/threw |

## 3.3 Values

**Values included in the xAPI Profile**

Parameter | Extension IRI | Value |
----------|---------------|-------|
Health | https://w3id.org/xapi/seriousgames/extensions/health | Number between [0, 1]|
Position | https://w3id.org/xapi/seriousgames/extensions/position | Object with attributes x, y and z |
Progress | https://w3id.org/xapi/seriousgames/extensions/progress | Number between [0, 1]|

**Values under consideration**

Parameter | Extension IRI | Value |
----------|---------------|-------|
Variable value / Device button | https://rage.e-ucm.es/xapi/ext/value | Number, String, Boolean, Object |
Variable increase/decrease | https://rage.e-ucm.es/xapi/ext/value | Number |
Measure label | https://rage.e-ucm.es/xapi/ext/label | String |

## 3.4 Result

Result statements can include a number of optional details:

- Score
	- scaled (a percentage between -1 and 1)
	- raw (the actual score)
	- min (minimum score achievable)
	- max (the maximum score achievable)
- Success – Pass or Fail
- Completion – Yes or No
- Response – e.g. the actual comment/answer
- Duration –  e.g. how long it took to complete the activity
- Extensions – e.g. more detail about the result
	- **it should contain information related to game logic**
	- the values inside leave the game developers to set

# 4. Objects

## 4.1. Completable

A **completable** is something a player can start, progress and complete in a game, maybe several times.

### 4.1.1. Predefined types

**Types included in the xAPI Profile**

|Identifier|Definition|
|----------|----------|
|_Serious-game_|Represents the game as a whole. A game is started the first time you play it, and is completed when you complete a basic story loop.|
|_Level_|Represents a level within a structure level in the game.|
|_Quest_| Represents an accomplishable challenge or mission presented inside a game. |

**Types under consideration**

|Identifier|Definition|
|----------|----------|
|_Session_|Represents a play session. Starts when the player connects to the game and ends with she disconnects.|
|_Stage_| ... |
|_Combat_| ... |
|_Story Node_| ... |
|_Race_| ... |
|_Completable_| A completable with no special semantics.|

### 4.1.2. Actions

**Actions included in the xAPI Profile**

#### 4.1.2.1. initialized

The player initialized the completable.

```
	John Doe initialized "Levels/World 1-1" at "May 24, 2016 15:03:17 UTC"
```

#### 4.1.2.2. progressed _progress_

The player made progress in a completable.

* `value` is a mandatory float indicating the absolute progress in the completion. Its value must be between `[0, 1] `.

```
	John Doe progressed 0.5 in "Levels/World 1-1" at "May 24, 2016 15:05:45 UTC"
```

#### 4.1.2.3. completed _ending_

The player finished a completable.

* `value` is an optional identifier indicating the ending of the completion, in the case a completable can be finished in several ways. Each identifier should represent and independent ending.

```
	// John Doe completed the game accessing to a good ending
	John Doe completed with "Princess Rescued" in "Super Mario Bros." at "Jan 20, 2016 12:35:13 UTC"

	// John Doe game overed in the game
	John Doe completed with "Game Over" in "Super Mario Bros." at "Jan 20, 2016 12:35:13 UTC"
```

### 4.1.3. Requirements and considerations

* A _completed_ action MUST be preceded by a _started_ action of the same completable object.
* A _completed_ action MUST be emitted before emitting a _started_ action of an already _started_ completable object.
* A _progressed_ action with value `0` is not equivalent to a _started_ action.
* A _progressed_ action with value `1` is not equivalent to a _completed_ action.

### 4.1.4. Metrics

* Is completed / Times completed
* Progress evolution
* Endings count
* Times to complete
* Mean, max and min time to complete

## 4.2. Reachable

A **reachable** is a virtual space inside the game world a player can access or skip once or multiple times.

### 4.2.1. Predefined types

**Types included in the xAPI Profile**

|Identifier|Definition|
|----------|----------|
| _Screen_ | A screen in the game, e.g., the start menu, the options menu. |
| _Area_ | A general area within the game, that can contain several zones. |
| _Zone_ | A concrete zone within the game. |
| _Cutscene_ | A non-interactive cutscene in the game (e.g., a video). |
| _Reachable_ | A reachable with no special semantics. |

### 4.2.2. Actions

**Actions included in the xAPI Profile**

#### 4.2.2.1. accessed

The player entered in the reachable.


```
	John Doe accessed "Screens/Sound Menu" at "Jan 10, 2016 7:47:47 UTC"
```

#### 4.2.2.2 skipped

The player skipped a reachable deliberately.

```	
	John Doe skipped "Cutscenes/Intro video" at "Sep 3, 2016 9:17:37 UTC"
```

### 4.2.3. Requirements and considerations

* A _skipped_ action MUST be preceded by a _accessed_ action of the same reachable.

### 4.2.4. Metrics

* Times accessed
* Time spent in it
* Times skipped
* Time to be skipped
* Access order (navigation tree)

## 4.3. Variable

A **variable** is a meaningful value inside the game world a player can set, increase or decrease.

## 4.3.1. Predefined types

**Types included in the xAPI Profile**

|Identifier|Definition|
|----------|----------|
| _Health_ | Value indicating health of the player (e.g., number of hearts, energy bar). |
| _Position_ | x, y, z the position in the map. |

**Types under consideration**

|Identifier|Definition|
|----------|----------|
| _Score_ | Value indicating the level of success of the player in the game. |
| _Currency_ | E.g., coins. |
| _Attempt_ | E.g., remaining lives. |
| _Variable_ | A variable with no special semantics. |

### 4.3.2. Actions

**Actions under consideration**

#### 4.3.2.1. set _value_

The player set a value in a variable.

* `value` is a string, a boolean, a number or an object, with the current value for the variable.

```
	// John Doe turned off music
	John Doe set false "Preferences/Music" at "May 7, 2016 11:22:57 UTC"
```

#### 4.3.2.2. increased/decreased _value_

The player increased/decreased a value in a variable.

* `value` is a number with the increase/decrease for the variable.

```
	// John Doe took 2 coins
	John Doe increased 2 "Currencies/Coins" at "May 7, 2016 11:22:57 UTC"
	// John Doe lost a life
	John Doe decreased 1 "Attempts/Lives" at "May 7, 2016 11:24:17 UTC"
```

### 4.3.3. Requirements and considerations

### 4.3.4. Metrics

## 4.4. Alternative

An **alternative** is a decision the player faces in the game, where she has to choose only one option among several. Options in alternatives can be unlocked.

### 4.4.1. Predefined types

**Types included in the xAPI Profile**

|Identifier|Definition|
|----------|----------|
| _Question_ | A question with several options. |
| _Menu_ | An options menu. |
| _Dialog-tree_ | An alternative presented during a conversation with an non-playable character. |

**Types under consideration**

|Identifier|Definition|
|----------|----------|
| _Path_ | ... |
| _Arena_ | E.g., the race in course in a race game, the stadium in a football game, a mini-game in Mario Party. |
| _Alternative_ | An alternative with no special semantics. |

### 4.4.2. Actions

**Actions included in the xAPI Profile**

#### 4.4.2.1. selected

The player selected an option in an alternative.

* `value` is the identifier of the selected option.

```
	John Doe selected "Tutorial Mode" "Menu/Start" at "Dec 31, 2016 13:05:12 UTC"	
```

#### 4.4.2.2. unlocked

The player unlocked an unavailable option in an alternative.

* `value` is the identifier of the unlocked option.

```
	John Doe unlocked "Combat Mode" "Menues/Start" at "Sep 13, 2016 14:13:12 UTC"
```

### 4.4.3. Requirements and considerations

### 4.4.4. Metrics

## 4.5. Device

A **device** is a piece of hardware the player interacts with to control the outputs of the game.

### 4.5.1. Predefined types

**Types included in the xAPI Profile**

|Identifier|Definition|
|----------|----------|
|_Mouse_| A mouse device whose clicks and movement affects the action in an activity. |
|_Keyboard_| A keyboard with keys. |
|_Controller_| A game pad with several buttons and pads. |
|_Touchscreen_| A touchscreen the player can press. |

**Types under consideration**

|Identifier|Definition|
|----------|----------|
|_Mouse Button 1_| Main button in a mouse (usually left button). |
|_Mouse Button 2_| Secondary button in a mouse (usually right button). |
|_Touch Screen N_| A touch screen, usually in a mobile device. N is the finger index (for multi touch). |

### 4.5.2. Actions

**Actions included in the xAPI Profile**

#### 4.5.2.1. pressed _button/key/position_

The player pressed a button, a key or a position in a device.

* `value` is the value of the button, key or position pressed by the player. If the value is a position, it should be in the game coordinates system, not in the screen coordinates system.

```
	John Doe pressed (50, 247) "Mouse Button 1"  at "Jan 21, 2016 19:43:22 UTC"
	John Doe pressed "Button_X" "Controller"  at "Jan 21, 2016 19:43:42 UTC"
```

#### 4.5.2.2. released _button/key/position_

The player released a button, a key or a position in a device.

* `value` of the button, key or position released by the player. If the value is a position, it should be in the game coordinates system, not in the screen coordinates system.

```
	John Doe released (59, 267) "Mouse Button 1" at "Jan 21, 2016 19:43:13 UTC"
	John Doe released "Button_X" "Controller" at "Jan 21, 2016 19:43:43 UTC"
```

### 4.5.3. Requirements and considerations

* A _pressed_ action must be eventually followed by a _released_ action over the same device. A _pressed_ cannot be emitted if there is a pending _released_ action.

### 4.5.4. Metrics

## 4.6. Target

A **target** is a game element the player can interact with.

### 4.6.1. Predefined Types

**Types included in the xAPI Profile**

|Identifier|Definition|
|----------|----------|
|_Enemy_| An opponent inside the game. |
|_Non-player-character_| Non-player character. |
|_Item_| A collectable. |

**Types under consideration**

|Identifier|Definition|
|----------|----------|
|_UI_| A control within the UI. |
|_Weapon_| ... |
|_Vehicle_| ... |

### 4.6.2. Actions

**Actions included in the xAPI Profile**

#### 4.6.2.1. interacted 

The player interacted with a target inside the game world.

```
	John Doe interacted "NPC/Villager" at "May 1, 2016 19:43:48 UTC"
```

#### 4.6.2.2. used

The player used a a target inside the game world.

```
	John Doe used "Item/HealthPotion" at "May 24, 2016 19:43:31 UTC"
```

**Actions under consideration**

#### 4.6.2.3. touched _position_

The player touched (or clicked) a target inside the game world (e.g., an UI control).

* `value` is the optional position of the player touch/click. It should be in the game coordinates system.

```
	John Doe touched "UI/StartButton" at "May 15, 2016 19:43:31 UTC"
```

#### 4.6.2.4. collected

The player collected a target inside the game world.

```
	John Doe collected "Weapon/LightSword" at "May 24, 2016 19:43:51 UTC"
```

### 4.6.3. Requirements and considerations

### 4.6.4. Metrics

## 4.7. Event

This type of action is intended to cover those custom interactions not covered in the rest of the model.

### 4.7.1. Predefined types

None.

### 4.7.2. Actions

**Actions under consideration**

#### 4.7.2.1. performed

The player executed the custom interaction.

```
	John Doe performed "Event/Jump"	
```

### 4.7.3. Requirements and considerations

### 4.7.4. Metrics

## 4.8. Compound interaction

To express a complex interaction, formed by simple interactions.

### 4.8.1. Predefined types

None.

### 4.8.2. Actions

**Actions under consideration**

#### 4.8.2.1 began

The complex interaction began. All subsequent interactions will belong to the compound interaction, until an ended action is emitted.

#### 4.8.2.2 ended

Marks the end of tye complex interaction.

```
	// John Doe bought a health potion and recovered health
	John Doe began "Buy health potion" at "May 24, 2016 19:43:11 UTC"
	John Doe decreased 20 "Currency/Coins" at "May 24, 2016 19:43:11 UTC"
	John Doe collected "Items/Health Potion" at "May 24, 2016 19:43:11 UTC"
	John Doe used "Items/Health Potion" at "May 24, 2016 19:43:11 UTC"
	John Doe increased 5 "Health/HP" at "May 24, 2016 19:43:11 UTC"
	John Doe ended "Buy health potion" at "May 24, 2016 19:43:11 UTC"
```

### 4.8.3. Requirements and considerations

* All the interactions are emitted at the same exact timestamp
* A compound interaction can have sub-compound interaction

### 4.8.4. Metrics

## 4.9. Measure

Used by the game engine to log debug and performance data.

### 4.9.1. Predefined types

**Types under consideration**

|Identifier|Definition|
|----------|----------|
|_Memory_| Memory usage. Value should be an absolute measure. |
|_CPU_| CPU usage. Value should be a percentage measure. |
|_Frame rate_| Frame rate of the game at a given moment. |
|_Load time_| Load time for a concrete task, in seconds. |

### 4.9.2. Actions

**Actions under consideration**

#### 4.9.2.1. measured

The game engine measured a value for a given performance metric.

```
	John Doe measured "LoadTime/Scene1" 0.2 at "May 24, 2016 19:43:37 UTC"
```

### 4.9.3. Requirements and considerations

### 4.9.4. Metrics

## 4.10. Game Message

Used by the game engine to log some error in the game.

### 4.10.1. Predefined types

**Types under consideration**

|Identifier|Definition|
|----------|----------|
|_Info_| Relevent events with no meaningful consequences for the game.|
|_Debug_| A message with debug purposes. |
|_Warning_| An undesired happening in the game. |
|_Error_| Something that was not supposed to happen. |
|_Critical_| Something that should never happen, and it is critical for the correct functioning of the game.|

### 4.10.2. Actions

**Actions under consideration**

#### 4.10.2.1 threw

```
	John Doe threw "Error/Exception" "ArrayIndexOutBounds es.eucm.countrix.Countrix.java:90"
```

### 4.10.3. Requirements and considerations

### 4.10.4. Metrics

# 5. Example statements

## 5.1 Completable

**initialized**

```
	John Doe initialized "Levels/World 1-1" at "May 24, 2016 15:03:47 UTC"
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "http://adlnet.gov/expapi/verbs/initialized"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Levels/World1-1",
		"definition": {
			"type": "http://curatr3.com/define/type/level"
		}
	},
	"timestamp": "2016-05-24T15:03:47Z"	
}
```

**progressed**

```
	John Doe progressed 0.5 in "Levels/World 1-1" at "May 24, 2016 15:05:49 UTC"
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "http://adlnet.gov/expapi/verbs/progressed"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Levels/World1-1",
		"definition": {
			"type": "http://curatr3.com/define/type/level"
		}
	},
	"result": {
		"extensions": {
			"https://rage.e-ucm.es/xapi/ext/progress": 0.5
		}
	},
	"timestamp": "2016-05-24T15:05:49Z"	
}
```

**completed**

```
	John Doe completed with "Game Over" in "Super Mario Bros." at "Jan 20, 2016 12:35:13 UTC"
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "http://adlnet.gov/expapi/verbs/completed"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Levels/World1-1",
		"definition": {
			"type": "http://curatr3.com/define/type/level"
		}
	},
	"result": {
		"extensions": {
			"https://rage.e-ucm.es/xapi/ext/value": "Game Over"
		}
	},
	"timestamp": "2016-01-20T12:35:13Z"
}
```

## 5.2 Reachable

**accessed**

```	
	John Doe accessed "Screens/Sound Menu" at "Jan 10, 2016 7:47:47 UTC"
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "http://activitystrea.ms/schema/1.0/access"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Screens/SoundMenu",
		"definition": {
			"type": "https://rage.e-ucm.es/xapi/seriousgames/activities/Screen"
		}
	},
	"timestamp": "2016-01-10T07:47:47Z"
}
```

**skipped**

```	
	John Doe skipped "Cutscenes/Intro video" at "Sep 3, 2016 9:17:37 UTC"
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "http://id.tincanapi.com/verb/skipped"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Cutscenes/IntroVideo",
		"definition": {
			"type": "https://rage.e-ucm.es/xapi/seriousgames/activities/Cutscene"
		}
	},
	"timestamp": "2016-09-03T09:17:37Z"
}
```

## 5.3. Variables

**set**

```
	John Doe set false "Preferences/Music" at "May 7, 2016 11:22:57 UTC"
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "https://rage.e-ucm.es/xapi/seriousgames/verbs/set"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Preferences/Music",
		"definition": {
			"type": "https://rage.e-ucm.es/xapi/seriousgames/activities/Preference"
		}
	},
	"result": {
		"extensions": {
			"https://rage.e-ucm.es/xapi/ext/value": false
		}
	},
	"timestamp": "2016-05-07T11:22:57Z"
}
```

**decreased**

```
	John Doe decreased 1 "Attempts/Lives" at "May 7, 2016 11:24:47 UTC"
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "https://rage.e-ucm.es/xapi/seriousgames/verbs/decreased"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Attempts/Lives",
		"definition": {
			"type": "https://rage.e-ucm.es/xapi/seriousgames/activities/Attempt"
		}
	},
	"result": {
		"extensions": {
			"https://rage.e-ucm.es/xapi/ext/value": 1
		}
	},
	"timestamp": "2016-05-07T11:24:47Z"
}
```

## 5.4. Alternatives

**selected**

```
	John Doe selected "Tutorial Mode" "Menu/Start" at "Dec 31, 2016 13:05:12 UTC"	
```

```json
{
	"actor": { "..." },
	"verb": {
		"id": "http://adlnet.gov/expapi/verbs/preferred"
	},
	"object": {
		"id": "http://example.com/games/SuperMarioBros/Alternatives/Menu_Start",
		"definition": {
			"type": "https://rage.e-ucm.es/xapi/seriousgames/activities/Alternative"
		}
	},
	"result": {
		"response": "Tutorial Mode"
	},
	"timestamp": "2016-12-31T13:05:12Z"
}
```
