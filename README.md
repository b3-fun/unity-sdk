# B3 Unity SDK
The all-in-one package that takes your Unity games to the next level with basement integrations.

## Prerequisites / Dependencies
- Package built and tested on unity engine version ``2021.3.45f1``. Some functionalities might not be available in versions prior to 2021

## How to install
- Download ``b3-unity-sdk.unitypackage`` from this repository.
- Open Unity and install the package either through package manager, or by double clicking the .unitypackage file and importing all the assets.
- For WebGL, the webgl template for B3 must be used IF webhook or window message APIs are used:
  - Open the player settings, and select the template ![image](https://github.com/user-attachments/assets/3ae811fd-ebff-43d7-b9af-691d159a709d)


## Features
- Automatic management of basement user session and handling of auth token.
  - WebGL support: retrieve the JWT from token parameter and maintain for API use.
  - Native support: retrieve the JWT using sign in with browser, deep links, and automatic heartbeat for session persistance. (WIP)
- Basement APIs:
  - All basement launcher APIs are available as C# functions, with support for both await and callback-based asynchronous operations.
    - Additional support for launcher modals such as tip, mint or swap via window message APIs (WebGL only, use http requests for native)
  - Webhook support
    - Webhooks in WebGL using window message API
    - Webhooks natively, via deep links (WIP)
