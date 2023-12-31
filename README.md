
# Playgap SDK Unity Plugin

The Playgap SDK enables mobile app developers to monetize offline gameplay via advertisements.

Playgap software design prioritizes stability, compatibility, transparency and customer needs. If you have any questions, our engineering and business teams would be happy to support you over email and messengers via hello@playgap.io or through your account manager.

Check out all of our supported plaforms:
- [Unity](https://github.com/playgap/unity-plugin)
- [iOS](https://github.com/playgap/ios-sdk)
- Android (under construction)

## Test Your Integration

For testing and integrating the Playgap SDK without setting up an account, use the Universal Testing API Key: `PlaygapTestID123`.

This ID is configured with Test Mode enabled, meaning it will always send test-specific advertisements so that the integration within an application can be tested consistently with 100% fill, while also being separated from production reporting.

Use this opportunity to understand how the Playgap SDK fits within your application flow, and how to best utilize it for your application use cases. All that is required is configuring a production API Key to swap this testing setup to a production setup.

## Launch Your Integration

To receive an API Key for your own application(s) to start monetising your offline gameplay, either:
- Reach out to your Account Manager at Playgap
- Or; Fill out the [Playgap Customer Signup Form](https://docs.google.com/forms/d/e/1FAIpQLScNPmxlOJrGPYSIyILDt7c2rtjdIBByo3TRsLVJ9z2tjuGeDg/viewform?usp=sf_link) and following the instructions sent to your configured email(s)

Applications configured within Playgap will always begin in Test Mode (equivalent behavior to the Universal Testing API Key), and can later be enabled for production advertisements through contacting Playgap.

If you have any questions about this process, please reach out to your account manager or drop a message at hello@playgap.io.

## This plugin is built for

-   iOS Playgap SDK  **v1.0.7**

## Rewarded and Interstitial Formats

You can show Playgap Ads in both Rewarded Ad surfacing points and Interstitial Ad surfacing points within the same application. This would cover both cases of User-Initiated and Application-Initiated Ads.

In both cases, the user must receive a Reward. Using the "Skip" functionality, users can choose to skip the ad and lose the reward or complete the view to receive the reward.

The APIs offered by the SDK allow for the game to have full discretion over the manner in which ads are displayed to the user. Whether the ad is user-initiated via a button, or the ad is an interstitial after a level completion, the Playgap SDK offers the flexibility for the application to achieve both simultaneously.

## The Flow and User Experience

The Playgap SDK offers a user experience which can be split into three parts: Preparation, Transition to Offline, and Return to Online

### Preparation

Upon opening your application, users who often transition offline are connected to the internet for around 10 seconds. It's vital to use this window of opportunity to ensure that offline gameplay can be monetized.

Use this window of time to initialize the Playgap SDK to prepare itself for monetizing this offline gameplay. It's recommended that the SDK is always initialized on application start to never miss out on this opportunity. Even in the event that the user launches the application offline, the Playgap SDK can be initialized without internet connection after it was previously initialized successfully.

### Transition to Offline

During offline gameplay, the Playgap SDK can be used to Load and Show ads to users.

The Playgap SDK will continue to allow Loading and Showing of advertisements at the applications discretion. The APIs allow for the number of claimed and unclaimed rewards obtained offline to be checked at any stage during gameplay. This allows for correct flexibility and limiting use of the SDK to the applications requirements.

### Return to Online

Once the user returns to online gameplay, the application has the opportunity to check the rewards that were accumulated offline, and present the user with a dialog to claim these rewards.
The APIs offered allow the game to make a smart decision about when and where in the application flow the Playgap "Claim Rewards" Dialog is shown to the user.

On this dialog, upon claiming the reward, the user will be briefly presented with an Appsheet associated with the ad they watched offline. Playgap counts the reward for the impression as claimed after this Appsheet is dismissed.

We recommend that this dialog is displayed via the Claim Rewards API as soon as the connection is re-established for a user with unclaimed rewards. The Claim Rewards sheet can be launched whenever the user is connected to the internet. The SDK will not allow the Claim Rewards sheet to be opened without connection, so we recommend calling the Claim Rewards Screen whenever it's convenient during the application flow to maximise the amount of Rewards the User Claims.

**There are multiple options to issue rewards to users:**

The Playgap SDK offers a clear set of IDs to match Impressions, to Rewarded Views, to Unclaimed Rewards. This offers a multitude of methods to tailor the user-rewarding experience correctly for your application.

Below you'll find our recommended rewarding mechanisms which align with our expected use of the SDK.

**Deliver Partial Reward on Video Completion (recommended):**

The application will receive the identifier for the unclaimed reward from the Playgap SDK at the moment the video completes. It is recommended that the reward is divided into two parts, to cover both the offline and the subsequent online session. This results in the best user experience as the user is partially rewarded during their offline gameplay, which is consistent with their typical online experience.

Example 1: A reward for viewing an online rewarded advertisement is 10 coins. For an offline user, this could be split into 5 coins delivered at video completion offline, and 5 coins delivered once the user returns online and claims them.

Example 2: Where splitting the reward is not possible, the user may be granted one type of reward offline, for example a life, and another type of reward once the user is back online, for example a booster or 2 coins.

**Deliver Full Reward on Claiming Reward:**

Allow the user to store rewards during offline gameplay without granting the rewards until the user claims them online. This incentivises the user to return online sooner.

Once the user returns online and claims the rewards, grant the full rewards.

### Note about Connection

The Playgap SDK is developed with offline-first approach. However, the SDK does not prohibit any of its flows to execute while connected to the internet. All of the flows explained above will work under the assumption that the user is considered offline while the advertisement is playing. The Playgap SDK will still expect the "Return to Online" flow to consider the rewards from the advertisement as claimed.

## Installation

### Using .unitypackage

1. Clone / download the [plugin repository](https://github.com/playgap/unity-plugin/releases).
2. [Import](https://docs.unity3d.com/Manual/AssetPackages.html) the `playgap-*.unitypackage` into your Unity project.
3. Go to Assets -> Import Package -> Custom Package.
4. Select the `playgap-*.unitypackage` file.

## Usage

### Initialize SDK

All of the SDK APIs require the SDK to successfully initialize prior to use. The SDK will not execute any functionality without being initialized, so it is always recommended to initialize the SDK as soon as possible once the application started.

Once the SDK has been initialized successfully once while connected to the internet, then it can be used to load and show advertisements, and even initialize in following user sessions without connection to the internet.

```csharp
PlaygapAds.OnInitializationComplete = (string error) => {
    if (error != null)
    {
        Debug.Log("Initialzation failed triggered: " + error);
    }
    else
    {
        Debug.Log("Initialization completed triggered");
    }
};
PlaygapAds.Initialize("YOUR_API_KEY");
```

### Load Rewarded Video Ad

In order to display an advertisement, one must first Load an ad object to be shown.

Multiple ads can be loaded simultaneously to handle different scenarios within your application, but an individual Playgap Ad object can only be shown once.

The PlaygapAds.loadRewarded function should be used for both Rewarded and Rewarded Interstitial Formats.

```csharp
PlaygapAds.OnLoadComplete = (string adId, string error) => {
    if (error != null)
    {
        Debug.Log("Load failed triggered: " + error);
    }
    else
    {
        loadedAdId = adId;
        Debug.Log("Load successful triggered with id: " + adId);
    }
};
PlaygapAds.LoadRewarded();
```

### Display Loaded Ad

Displaying an Ad will result in a fullscreen advertisement showing on the user's device. This fullscreen ad contains in-app controls for the user to skip over the reward if they desire, and to control the volume and mute status of the advertisement. If the user attempts to skip the advertisement, they will be prompted that this will result in losing the reward if they continue.

```csharp
if (loadedAdId != null)
{
    PlaygapAds.OnShowFailed = (string error) => {
        Debug.Log("Show failed triggered: " + error);
    };
    PlaygapAds.OnShowImpression = (string impressionId) => {
        Debug.Log("Impression triggered for id: " + impressionId);
    };
    PlaygapAds.OnShowPlaybackEvent = (string period) => {
        Debug.Log("Playback event triggered: " + period);
    };
    PlaygapAds.OnShowCompleted = (string rewardId) => {
        Debug.Log("Show completed triggered with reward id: " + rewardId);
    };
    PlaygapAds.Show(loadedAdId);
}
```

### Check and Claim Rewards

The online reward mechanic displays the app sheet associated with the video ad, which is when you earn revenue as a publisher.

It is only possible to successfully display the Claim Rewards Dialog while both:
- Connection to a network is established
- Unclaimed Rewards have been accumulated offline

The Claim Rewards screen can be shown whenever connection to the internet is available, even within application sessions where the SDK was initialized offline. The Claim Rewards sheet will not be shown if the user does not have a valid connection, so we recommend calling to Claim Rewards as frequently as possible where it fits within your application flow. This ensures that the user is rewarded as frequently as they should be.

The number of unclaimed rewards can be checked via the Check Rewards API after the SDK has been initialized successfully.

```csharp
var rewards = PlaygapAds.CheckRewards();
Debug.Log("Check Rewards " + rewards.unclaimed);

// and then call claim rewards if needed

PlaygapAds.OnRewardScreenShown = () => {
    Debug.Log("Claim Reward screen shown triggered");
};
PlaygapAds.OnRewardScreenFailed = (string error) => {
    Debug.Log("Claim Reward screen failed to show triggered: " + error);
};
PlaygapAds.OnRewardScreenClosed = () => {
    Debug.Log("Claim Reward screen closed triggered");
};
PlaygapAds.OnStoreClick = () => {
    Debug.Log("Store click triggered");
};
PlaygapAds.OnUserClaimedReward = (string rewardId) => {
    Debug.Log("User claimed reward triggered with id: " + rewardId);
};
PlaygapAds.ClaimRewards();
```

## Recommendations

Below are some useful recommendations for utilizing features of the Playgap SDK to ensure that you can use the SDK with the greatest flexibility to maximize the effectiveness of your use cases.

### Initialize the SDK Early Every User Session

It's important to initialize the SDK early in the user session for every new user session. The SDK relies on being connected to the internet at a certain point in time, but will be able to handle required functions once successfully initialized.

Upon opening your application, users that often transition offline are connected to the internet for around 10 seconds. This window of opportunity is vital to capitalize on to ensure that offline gameplay can be monetized.

To capitalize correctly, the application must initialize the Playgap SDK to prepare itself for monetizing this offline gameplay. It's recommended that the SDK is always initialized on application start to never miss out on this opportunity. Even in the event that the user launches the application offline, the Playgap SDK can be initialized without internet connection for a certain duration (24-48 hours) if it was previously initialized successfully.

### Claim Rewards while Connection is Established

Displaying users with the opportunity to install the ad while online is required to generate revenue. Therefore, it is important that the Claim Rewards Dialog is displayed to users when they have claimed rewards while offline, and while the connection to a network is established.

The amount of unclaimed rewards can be accessed via the Check Rewards API after the SDK has been initialized successfully.

### Show Ads in the Order they were Loaded In

Playgap will always attempt to provide the maximum value for the ads shown within the user session. It achieves this by loading the most valuable ads as soon as possible.

It is possible your integration requires both loading and showing multiple ads in row. This behavior is covered by the SDK, but it is important to prioritize displaying ads in the correct order, as this can lead to the best monetisation outcomes.

### How ID Tracking Works

The Playgap SDK outputs certain identifiers on:
- The Loaded Playgap Ad => Object ID
- The Impression once the ad is shown => Impression ID
- The Reward collected offline when the ad completes => Reward ID
    - At this stage, the Playgap SDK considers this an "Unclaimed" Reward
- The Claimed Reward once the user returns online => Reward ID
    - At this stage, the Playgap SDK considers this a "Claimed" Reward

All of these IDs will be identical at the different stages of the ad lifecycle, and are exposed via the SDK APIs. These can be used for any purpose, such as aligned event tracking, fraud prevention, or unique rewarding solutions.

The PlaygapAds.CheckRewards API can used to check both claimed and unclaimed rewards:
- Claimed rewards are all of the Reward IDs the user has received since the most recent update of the Playgap SDK
- Unclaimed rewards are all of the rewards from advertisements which have not yet been claimed from the Claim Rewards Dialog
