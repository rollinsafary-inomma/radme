# Scrabble

## Description

The **Scrabble** game web version

Game Engine: **Phaser 3 - WebGL renderer**  <br>

Language: **TypeScript**  <br>

Packer: **Webpack 5**  <br>

## How to run

  

- make sure you've installed `node` and `npm` on your PC (to check that use `node --version`, `npm --version` commands)

- run `npm i`

- run `npm run local` for auto sign in with development account and just `npm run dev` for normal start in not signed in guest mode (this will start webserver with SSL on port 8080 in dev mode)

## More info

The project is based on a modular architecture, used [MVC](https://www.npmjs.com/package/@rollinsafary/mvc) design pattern. <br>


### Plugins used

  
- Phaser 3 [i18n](https://github.com/RollinSafary/phaser3-i18n-plugin) plugin - [npm url](https://www.npmjs.com/package/@rollinsafary/phaser3-i18n-plugin)

- Phaser 3 [nine-patch](https://github.com/RollinSafary/phaser3-ninepatch-plugin) plugin - [npm url](https://www.npmjs.com/package/@rollinsafary/phaser3-ninepatch-plugin)

- Phaser 3 [doms](https://github.com/RollinSafary/phaser3-doms-plugin) plugin - [npm url](https://www.npmjs.com/package/@rollinsafary/phaser3-doms-plugin)

 
## Scripts

- `build:prod` makes production build (without a console and disabled context menu and developer tools, uglified and minified)
- `build:stage`makes production build with `staging` env
- `build:dev` makes development build with `development` env
- `build:local` makes development build with `local` env
- `dev` runs local server by default to `https://localhost:8080` with `dev` env
- `local` runs local server by default to `https://localhost:8080` with `local` env

- `assets` generates `assets.ts` file based on resources stored in `./assets` folder
- `translations` generates `translations.ts` file based on keys in `./assets/locales/en.json`

  

## Localization (i18n plugin)

  
We used the `Phaser 3 I18n plugin` for the localization, which allows dynamic string translations and font switch on language change if configured.

To fill localization strings, please start from `./assets/locales/en.json`, to add new language localizations, please make a copy of `en.json` for example with the name `ru.json` and fill values for translation keys in the JSON file. <br><br>

NOTE: if there's no `translation key` in any language JSON in place of translated text will be displayed given `translation key`  <br><br>

The available languages will be added automatically to the game based on the localization JSON files. <br><br>

NOTE: if you're adding a language that needs to have a specific font to use, please add it into `Game.ts` in `fontMappings` array.

  

## Nine-Patch

A plugin that allows work with images during runtime, allows nine-patch ready images scaling without losing the quality (example: tooltips).

<br> For more info navigate to https://github.com/koreezgames/phaser3-ninepatch-plugin

## Doms
A plugin that provides main HTML components that are acceptable for game scenes and in-game positioning, mutations, event handling and all other transformations that are allowed for native game objects except physics support! This plugin automatically registers these dom elements as game objects and their *factory* and *creator* methods in Scene Factory and Creator

## Structure


- `./assets` - all assets that are being used in the game

- `./config` - webpack configs, all `envs` are provided in `./config/webpack.parts.config.js` file.

- `./raw_assets` - the source images that will be packed via TexturePacker and saved as Atlases in `./assets` folder

- `scripts` - the scripts for `assets.ts` and `translations.ts` generation, which allows to use asset names in code.

- `src` - source folder

- `template` - the template files for the `webpack` config


# Code structure
As already told the project structure is based on MVC deseign pattern, so you can find all views in `./src/views` folder, all commands in `./src/controller` folder, all models in `./src/model` folder and all guards in `/src/guards` folder.
## Little bit about this MVC implementation
**Model-View-Controller** core components are

 - **Facade** is the parent component which collects all implementations of design patterns in it and provides connection interfaces for them. The `sendNotification` function which acts as event emitting function is being provided by the `Facade`.
 - **Model** consist of **Proxy**-s that are providing functional interface to the local database imitation called **VO** (*value-object*)
 - **View** consist of **Mediator**-s that are acting  as connectors for main game *visible* components as **viewComponent**s.
 - **Controller** registers **Commands** to the **notification**-s
 - **notification** is **MVC**'s inner event, which acts as simple event, but has it's own usage pattern!

All `MVC` components have function **sendNotification** which allows them to send *events* to other components.

## Vews and ViewComponents
**View**s are game objects that are connected to whole system via **Mediator**s. They consist of **ViewComponent**s and they don't have their own **Mediator**s and are being controlled via parent **View**. If you see a game object that has a `View` or `Scene` word at the end, this means it has it's own **Mediator** too.

## Models and VOs
As mentioned before the **VO** *(value-object)* is used as JSON-like data container. **Proxy** has reference to it, and is the only component allowed to mutate **VO**'s content and values. **Proxy** provides functionality to mutate **VO**, but this allowed only from the **Commands**. The **Proxy** and **VO** togather are one implementation of model and can be added to the core **Model** pattern.

## Controller, Commands and Guards
There are different types of **Commands** that are being used in the project: **SimpleCommand** **MacroCommand** **AsyncMacroCommand**. **MacroCommand** and **AsyncMacroCommand** can add *sub commands*. 
**Guards** are acting as if statement for the **Command** execution, which means that if there are added **Guard**s to the **Command** it's `execute` function will be called only when all **Guard**s are executed with success *(returned true)*. In cases when one of them is failed it's handled in `onAnyGuardDenied` function in **Command** and in case if all of them are failed there is `onAllGuardsDenied`and `onAnyGuardApproved` if atleast one **Guard** succeeded.
**Guards** are allowed for all types of **Commands**.


# Game Components - Views
There are 2 types of **Game Components** : **Scene**s and **View**s
Some of the scenes have `canvas` components and html/css based dom components. The dom components' implementation increases performance because these components aren't being rendered via `canvas` and providing native behavior for different devices and operating systems.

## Scenes
- **BootScene** is for preloading **LoadingScene** assets and initalizing the plugins if needed.
- **LoadingScene** is for loading all assets that are being used in the game, and makes calls to the `Server` to collect all neccessary data for normal launch. Can be switched to **MenuScene** *(if there's no unfinished game)* or to **GameScene** *(if there's unfinished game)*.
- **MenuScene** the scene that provides a menu for  **Play Online**, **Play a Friend** and **Play the AI** game starting options for game starting. Left side is `canvas` rendered, which contains board preview and a message and right side is the main panel that contains these options and created as html/css based dom components and aren't being rendered via `canvas`.
- **GameScene** the main gameplay scene, left side is the board and control panel, both of them are `canvas` rendered and the right part fully dom and consists of users panel, game history, chat and tilebag.
- **PopupScene** is supporting scene that provides separate layer for displaying popus and modals.
- **ServiceScene** doesn't display/render anything, works as main supporting scene, is being used to have one sound control system, and provides *notification*s about out-game events. 

# Proxies and Services - Models
There are used **ApiService** and **PusherService** services, and 4 proxies: **UserVOProxy**,**GameVOProxy**, **PusherVOProxy** and **UiVOProxy**.
## ApiService
provides interface for calls to the `Server` *(is not a MVC component)*
## PusherService
provides interface for connecting to the server's `pusher` and subscribing to it's channels, such like user private channel and chat channels.
## UserVOProxy
provides functional interface for the **UserVO** which is json representation of user sign in data received from the server.
## GameVOProxy
provides functional interface for the **GameVO** which is the game state at the moment. Provides functions and filtering methods for the state to make it's usage more comfortable.
## PusherVOProxy
provides functional interface for the **PusherVO** which is Pusher Client related data  and bridging pusher events to game system.
## UiVOProxy
provides function interface for the **UiVO** which is the state of most data based components. Even gameplay components' visible state and keyboard typing or swapping mechanics state are in this **VO**.
Getting updates from user actions, and game state changes and provides events to update **View**s too.
