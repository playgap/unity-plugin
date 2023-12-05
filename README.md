
# Playgap SDK Unity Plugin

The Playgap SDK enables mobile app developers to monetize offline gameplay via advertisements.

Playgap software design prioritizes stability, compatibility, transparency and customer needs. If you have any questions, our engineering and business teams would be happy to support you over email and messengers via hello@playgap.io or through your account manager.

## Test Your Integration

For testing and integrating the Playgap SDK without setting up an account, use the Universal Testing API Key: `PlaygapTestID123`.

This ID is configured with Test Mode enabled, meaning it will always send test-specific advertisements so that the integration within an application can be tested consistently with 100% fill, while also being separated from production reporting.

Use this opportunity to understand how the Playgap SDK fits within your application flow, and how to best utilize it for your application use cases. All that is required is configuring a production API Key to swap this testing setup to a production setup.

## Launch Your Integration

To receive an API Key for your own application(s) to start monetising your offline gameplay:
- Fill out the [Playgap Customer Signup Form](https://docs.google.com/forms/d/e/1FAIpQLScNPmxlOJrGPYSIyILDt7c2rtjdIBByo3TRsLVJ9z2tjuGeDg/viewform?usp=sf_link)
- Follow the instructions sent to your configured email(s) about Adding Applications to the Playgap Network

Applications configured within Playgap will always begin in Test Mode (equivalent behavior to the Universal Testing API Key), and can later be enabled for production advertisements through contacting Playgap.

If you have any questions about this process, please reach out to your account manager or drop a message at hello@playgap.io.

## This plugin is built for

-   iOS Playgap SDK  **v1.0.7**

## Installation

### Using .unitypackage


1. Clone / download the [plugin repository](https://github.com/playgap/unity-plugin/releases).
2. [Import](https://docs.unity3d.com/Manual/AssetPackages.html) the `playgap-*.unitypackage` into your Unity project.
3. Go to Assets -> Import Package -> Custom Package.
4. Select the `playgap-*.unitypackage` file.

## Usage

### Initialize SDK

All of the SDK APIs require the SDK to successfully initialize prior to use. The SDK will not execute any functionality without being initialized, so it is always recommended to initialize the SDK as soon as possible once the application started.

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

### Claim Rewards

The online reward mechanic displays the app sheet associated with the video ad, which is when you earn revenue as a publisher.

It is only possible to successfully display the Claim Rewards Dialog while both:
- Connection to a network is established
- Unclaimed Rewards have been accumulated offline

The number of unclaimed rewards can be checked via the Check Rewards API after the SDK has been initialized successfully.

```csharp
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
