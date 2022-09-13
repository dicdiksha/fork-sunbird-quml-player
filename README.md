# :diamond_shape_with_a_dot_inside: QuML-player library for Sunbird platform
The QUML player library components are powered by Angular. These components are designed to be used in sunbird consumption platforms *(mobile app, web portal, offline desktop app)* to drive reusability, maintainability hence reducing the redundant development effort significantly.

# :bookmark_tabs: Getting Started
For help getting started with a new Angular app, check out the [Angular CLI](https://angular.io/cli).
If you have an Angular ≥ 9 CLI project, you could simply use our schematics to add sunbird-quml-player library to it.

For existing apps, follow below-mentioned steps:  

## :label: Installation
Just run the following:
```red
ng add @project-sunbird/sunbird-quml-player-v9
```
It will install sunbird-quml-player for the default application specified in your `angular.json`. If you have multiple projects and you want to target a specific application, you could specify the `--project` option

```red
ng add @project-sunbird/sunbird-quml-player-v9 --project myProject
```
Shematics will create `question-cursor-implementation.service.ts`. Please update the `listUrl` in it. For more information refer [question-cursor-implementation.service.ts](https://github.com/project-sunbird/sunbird-quml-player/blob/main/projects/quml-demo-app/src/app/question-cursor-implementation.service.ts)

### Manual installation
If you prefer not to use schematics or want to add `sunbird-quml-player-v9` to an older project, you'll need to do the following:

<details>
  <summary>Click here to show detailed instructions!</summary>

  ### :label: Step 1: Install Packages
  These are the peerDependencies of the library, need to be installed in order to use this library.

    npm install @project-sunbird/sunbird-quml-player-v9 --save
    npm install @project-sunbird/sb-styles --save
    npm install @project-sunbird/client-services --save
    npm install bootstrap@4.1.1 --save
    npm install jquery --save
    npm install katex --save
    npm install lodash-es --save
    npm install ngx-bootstrap@5.6.2 --save


  Note: *As QuML library is build with angular version 12, we are using **bootstrap@4.1.1** and **ngx-bootstrap@5.6.2** which are the compatible versions.
  For more reference Check compatibility document for ng-bootstrap [here](https://valor-software.com/ngx-bootstrap/#/documentation#compatibility)*  

  ### :label: Step 2: Add CSS
  Copy CSS code from the below given `styles.css` to app's default `styles.css` or `styles.scss`
  keep `quml-carousel.css` in root folder.

    - [styles.css](https://github.com/project-sunbird/sunbird-quml-player/blob/main/projects/quml-demo-app/src/styles.css)  
    - [quml-carousel.css](https://github.com/project-sunbird/sunbird-quml-player/blob/main/quml-carousel.css)  

  ## :label: Step 3: Add question-cursor-implementation.service
  Create a **question-cursor-implementation.service.ts** in a project and which will implement the `QuestionCursor` abstract class.  
  `QuestionCursor` is an abstract class, exported from the library, which needs to be implemented. Basically it has some methods which should make an API request over HTTP  

  For more information refer [question-cursor-implementation.service.ts](https://github.com/project-sunbird/sunbird-quml-player/blob/main/projects/quml-demo-app/src/app/question-cursor-implementation.service.ts) and do not forgot to add your question list API URL here, for example: [https://staging.sunbirded.org/api/question/v1/list](https://staging.sunbirded.org/api/question/v1/list)

  ### :label: Step 3: Include the styles, scripts and assets in angular.json
  Add the following under `architect.build.assets` for default project  
```javascript
{
  ...
  "build": {
    "builder": "@angular-devkit/build-angular:browser",
    "options": {
      ...
      ...
      "assets": [
        ...
        ...
        {
         "glob": "**/*.*",
         "input": "./node_modules/@project-sunbird/sunbird-quml-player-v9/lib/assets/",
         "output": "/assets/"
        }
      ],
      "styles": [
        ...
        "src/styles.css",
        "./node_modules/@project-sunbird/sb-styles/assets/_styles.scss",
        "./quml-carousel.css",
        "./node_modules/katex/dist/katex.min.css"
      ],
      "scripts": [
        ...
        "./node_modules/katex/dist/katex.min.js",
        "./node_modules/jquery/dist/jquery.min.js"
      ]
    }
  }
  ...
  ...
},
```
  
  ### :label: Step 4: Import the modules and components
  Import the required modules such as **CarouselModule**, **QumlLibraryModule**, **HttpClientModule** and **question-cursor-implementation.service** as below:

```javascript
  import { HttpClientModule } from '@angular/common/http';
  import { QumlLibraryModule, QuestionCursor } from '@project-sunbird/sunbird-quml-player-v9';
  import { CarouselModule } from 'ngx-bootstrap/carousel';
  import { QuestionCursorImplementationService } from './question-cursor-implementation.service';

  @NgModule({
   ...

   imports: [ QumlLibraryModule, CarouselModule.forRoot(), HttpClientModule ],
   providers: [{
     provide: QuestionCursor,
     useClass: QuestionCursorImplementationService
   }]

   ...
  })

 export class AppModule { }
```

</details>

Note: To avoid CORS errors add proxy configuration for API's refer - [proxy.conf.json](https://github.com/project-sunbird/sunbird-quml-player/blob/release-5.1.0/projects/quml-demo-app/src/proxy.conf.json)

## :label: Send input to render Quml player
User can get a response from the `api/questionset/v1/hierarchy/:do_id` or can use the provided mock config for demo

Use the mock config in your component to send input to Quml player as `playerConfig`  
Click to see the mock - [samplePlayerConfig](https://github.com/project-sunbird/sunbird-quml-player/blob/release-5.1.0/projects/quml-demo-app/src/app/quml-library-data.ts#L495)  

```html
<quml-main-player [playerConfig]="samplePlayerConfig" ><quml-main-player>
```


## :orange_circle: Available components
|Feature| Notes| Selector|Code|Input|Output
|--|--|--|------------------------------------------------------------------------------------------|---|--|
| Quml Player | Can be used to render Quml | quml-main-player| *`<quml-main-player [playerConfig]="playerConfig"><quml-main-player>`*|playerConfig|playerEvent, telemetryEvent|

### :small_red_triangle_down: Input Parameters
1. playerConfig: Object - [`Required`]  
```javascript
{
  context: Object   // Information about the telemetry and default settings for quml API requests
  metadata: Object  // Question hierarchy response
  config: Object    // default player config such as sidebar menu list
}
```

### :small_red_triangle_down: Output Events
1. playerEvent()    - It provides heartbeat event for each action performed in the player.
2. telemetryEvent() - It provides the sequence of telemetry events such as `START, INTERACT, IMPRESSION, SUMMARY, END`

---
   

# Use as web components :earth_asia:
QuML Library can also be used as web component which means user can import this library in any web application and use these custom component.
Follow below-mentioned steps to use it in plain javascript project:

- Insert [library](https://github.com/project-sunbird/sunbird-quml-player/blob/main/web-component/sunbird-quml-player.js) as below:
	```javascript
	<script  type="text/javascript"  src="sunbird-quml-player.js"></script>
	```
- Create a asset folder and copy all the files from [here](https://github.com/project-sunbird/sunbird-quml-player/tree/main/web-component/assets), library requires these assets internally to work well.

- Get sample playerConfig from here: [samplePlayerConfig](https://github.com/project-sunbird/sunbird-quml-player/blob/release-5.1.0/projects/quml-demo-app/src/app/quml-library-data.ts)

- Pass the QuestionListAPI baseUrl for eg. [https://staging.sunbirded.org/api/question/v1/list](https://staging.sunbirded.org/api/question/v1/list)

- Create a custom html element: `sunbird-quml-player`
	```javascript
	const  qumlPlayerElement = document.createElement('sunbird-quml-player');
	```

- Pass data using `player-config`
	```javascript
	qumlPlayerElement.setAttribute('player-config', JSON.stringify(playerConfig));
	```
	**Note:** Attribute should be in **string** type
- Listen for the output events: **playerEvent** and **telemetryEvent**

  ```javascript
  qumlPlayerElement.addEventListener('playerEvent', (event) => {
    console.log("On playerEvent", event);
  });
  qumlPlayerElement.addEventListener('telemetryEvent', (event) => {
    console.log("On telemetryEvent", event);
  });
  ```

- Append this element to existing element
  ```javascript
  const myPlayer = document.getElementById("my-player");
  myPlayer.appendChild(qumlPlayerElement);
  ```
- :arrow_forward: Refer demo [example](https://github.com/project-sunbird/sunbird-quml-player/blob/main/web-component/index.html)  

---

# :bookmark_tabs: QuML Player Contribution Guide  
## Repo Setup  
  - Install Node 12.x and Angular 10
  - Clone the Repo with desired release-branch - https://github.com/project-sunbird/sunbird-quml-player
  - Add the your baseUrl in the *environment.ts* and *proxy.conf.json* files
  - If there any changes in API endpoints, update the *app.constant.ts* file
  - Change the default content ID in *app.component.ts* file if pointing to different baseUrl
  - Run `npm i` in root folder
  - Run `npm i` in `projects/quml-library` 
  - Open two terminal windows (on root folder)
  - Run `npm run build` once this run completes, run the next command - let it be running on 1st terminal window
  - Run `npm run serve` on second terminal window (This will copy assets from the `quml-library` to the library dist folder)
  - Now it will be served on `http://localhost:4200/`
  - To run the web-component `npm run build-web-component`
  - To run the the library run `npm run test-lib`


