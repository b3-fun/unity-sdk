# B3 Unity SDK
The all-in-one package that takes your Unity games to the next level with basement integrations.

## Prerequisites / Dependencies
- Package built and tested on unity engine version ``2021.3.45f1``. Some functionalities might not be available in versions prior to 2021
- NewtonSoft JSON

## How to install
- Download ``b3-unity-sdk.unitypackage`` from this repository.
- Open Unity and install the package either through package manager, or by double clicking the .unitypackage file and importing all the assets.
- For WebGL, the webgl template for B3 must be used IF webhook or window message APIs are used:
  - Open the player settings, and select the template 
![image](https://github.com/user-attachments/assets/3ae811fd-ebff-43d7-b9af-691d159a709d)

## Features
- Automatic management of basement user session and handling of auth token.
  - WebGL support: retrieve the JWT from token parameter and maintain for API use.
  - Native support: retrieve the JWT using sign in with browser, deep links, and automatic heartbeat for session persistance.
- Basement APIs:
  - All basement launcher APIs are available as C# functions, with support for both await and callback-based asynchronous operations.
    - Additional support for launcher modals such as tip, mint or swap via window message APIs (WebGL only, use http requests for native)
  - Webhook support
    - Webhooks in WebGL using window message API
    - Webhooks natively, via deep links (WIP)
- B3 Global Accounts
  - Access to B3 Ecosystem Wallet via ThirdWeb SDK

# How to Use

## Setup
- In your first scene, drag and drop the B3Instance prefab from the ``b3-sdk/Prefabs`` folder.
- On the B3Instance GameObject, click on the B3 Config asset, select and, and fill in any details.

## Session Management
In order to call the B3 Launcher APIs for the user currently playing the game, a JWT token is needed for authentication.
The SDK takes care of session management as follows, based on the target build platform:

| Platform | Session Management | What's Required |
| ---- | ---- | ---- |
| WebGL | Fully Automated | If the game opens in an external link from basement, make sure the isWebGLGameEmbedded flag is disabled on the config<br/> ![image](https://github.com/user-attachments/assets/4dd084e2-a5ef-45e2-a8cb-c075efc905e5) |
| Any non WebGL (Sign in with BSMNT) | Using deeplinks | You can use the ``LogInWithSSO`` functionality of the SDK to let a user sign in with basement |
| Any non WebGL (Your own auth) | Using wallet signature | Use the ``createUnverifiedChannel`` and ``verifyUnverifiedChannel`` APIs to create the game session for your user |

## Calling APIs
All the available launcher APIs are defined in ``B3LauncherClient`` (must import namespace ``BasementSDK``). They are configured to work either in asynhronous context with await, or via callback Actions.
Async/Await:
```cs
channelStatus = await B3LauncherClient.GetChannelStatus(new B3LauncherClient.GetChannelStatusBody { launcherJwt = SessionJWT }, null);
// your code
```
Callback Action:
```cs
B3LauncherClient.GetChannelStatus(new B3LauncherClient.GetChannelStatusBody { launcherJwt = SessionJWT }, (channelStatus) =>
{
    // your code
});
```
Each API endpoint has its own body type and return type definitions, which use NewtonSoft JSON for serialization/deserialization.

## Using Webhooks (WebGL only for now)
Webhooks are handled by the B3WebhooksHandler object. Attach your listeners to B3WebhooksHandler.OnWebhookReceived which will be called for every webhook received by the launcher.
The default data returned is defined as 
```cs
public class BaseWebhookData{
    public string callbackType;
    public string callbackUuid;
    public string ruleActionType;
    public object data;
}
```
But you can further extend the class based on your needs and webhooks used (for example for sent custom activity the data object returned by the webhook contains the data of the new activity

## Opening basement modals
The basement launcher is enriched with a lot of functionalities that can be triggered from within a game, enabling access to things like the tipping functionality, minting of assets, and other experiences.
The modals can be opened via two methods, supporting both web requests, as well as faster propagation with the window messaging API.
The following methods perform the same task:
```cs
B3LauncherClient.OpenTipModal(String.IsNullOrEmpty(otherWalletInput.text) ? "0xC5Ace087f703398F64FF9efdf9101edeF6390c9a" : otherWalletInput.text);
```
```cs
B3LauncherClient.TriggerRulesEngine(new B3LauncherClient.TriggerRulesEngineBody
{
    launcherJwt = B3Instance.Instance.SessionJWT ?? jwtInput.text,
    trigger = "open-tip-modal",
    otherWallet = otherWalletInput.text,
}, null);
```
Of course the recommended approach is the messaging API (first example), while the second is more optimized for future usecases where there might be needed a more advanced UX.

## Using B3 Ecosystem Wallet
- To use the B3 ecosystem wallet, you must first obtain your partner ID, and enter it on the B3 Config.
- You can then call ``B3EcosystemWallet.Instance.LoginViaMethod()``, with a provided authProvider, which will begin the necessary flow to log in your user with ThirdWeb.
- You can access the signed in ecosystem wallet via ``B3EcosystemWallet.Instance.EcosystemWallet``, which can be used with thirdweb APIs
