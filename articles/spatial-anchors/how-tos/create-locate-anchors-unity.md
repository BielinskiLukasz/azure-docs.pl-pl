---
title: Jak utworzyć i zlokalizować kotwice przy użyciu kotwic Azure przestrzennego w środowisku Unity | Microsoft Docs
description: Szczegółowe wyjaśnienie sposobu tworzenia i lokalizowania kotwic przy użyciu kotwic przestrzennych platformy Azure w środowisku Unity.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 343635db03273888fe1bd3747dfbe2502a23bff9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561452"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-unity"></a>Jak utworzyć i zlokalizować kotwice przy użyciu kotwic Azure przestrzennego w środowisku Unity

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Usługa Azure Spatial Anchors umożliwia udostępnianie kotwic na całym świecie między różnymi urządzeniami. Obsługuje kilka różnych środowisk programistycznych. W tym artykule szczegółowemy, jak używać zestawu Azure przestrzenny zakotwiczenias w środowisku Unity, aby:

- Poprawna konfiguracja i zarządzanie sesją zakotwiczeń przestrzennych platformy Azure.
- Tworzenie i Ustawianie właściwości dla kotwic lokalnych.
- Przekaż je do chmury.
- Znajdź i Usuń kotwice przestrzenne w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, upewnij się, że masz:

- Zapoznaj się z [omówieniem zakotwiczenia przestrzennego platformy Azure](../overview.md).
- Ukończono jedno z [5-minutowych przewodników szybki start](../index.yml).
- Podstawowa wiedza na C# temat platformy i aparatu Unity.
- Podstawowa wiedza na temat <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> , jeśli chcesz użyć systemu Android lub <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> , jeśli chcesz użyć systemu iOS.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Dowiedz się więcej o klasie [CloudSpatialAnchorSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession) .

```csharp
    CloudSpatialAnchorSession cloudSession;
    // In your view handler
    this.cloudSession = new CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Dowiedz się więcej o klasie [SessionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionconfiguration) .

```csharp
    this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```csharp
    this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Dowiedz się więcej na temat delegata [TokenRequiredDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.tokenrequireddelegate) .

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AccessToken = @"MyAccessToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AccessToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```csharp
    this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AuthenticationToken = @"MyAuthenticationToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AuthenticationToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

Dowiedz się więcej o metodzie [Start](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.start) .

```csharp
#if UNITY_ANDROID || UNITY_IOS
    this.cloudSession.Session = aRSession.subsystem.nativePtr.GetPlatformPointer();
#elif UNITY_WSA || WINDOWS_UWP
    // No need to set a native session pointer for HoloLens.
#else
    throw new NotSupportedException("The platform is not supported.");
#endif

    this.cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Dowiedz się więcej o metodzie [ProcessFrame](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.processframe) .

```csharp
#if UNITY_ANDROID || UNITY_IOS
    XRCameraFrame xRCameraFrame;
    if (aRCameraManager.subsystem.TryGetLatestFrame(cameraParams, out xRCameraFrame))
    {
        long latestFrameTimeStamp = xRCameraFrame.timestampNs;
        
        bool newFrameToProcess = latestFrameTimeStamp > lastFrameProcessedTimeStamp;

        if (newFrameToProcess)
        {
            session.ProcessFrame(xRCameraFrame.nativePtr.GetPlatformPointer());
            lastFrameProcessedTimeStamp = latestFrameTimeStamp;
        }
    }
#endif
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Dowiedz się więcej na temat delegata [SessionUpdatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionupdateddelegate) .

```csharp
    this.cloudSession.SessionUpdated += (object sender, SessionUpdatedEventArgs args)
    {
        var status = args.Status;
        if (status.UserFeedback == SessionUserFeedback.None) return;
        this.feedback = $"Feedback: {Enum.GetName(typeof(SessionUserFeedback), status.UserFeedback)} -" +
            $" Recommend Create={status.RecommendedForCreateProgress: 0.#%}";
    };
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Dowiedz się więcej o klasie [CloudSpatialAnchor](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor) .

```csharp
    // Create a local anchor, perhaps by hit-testing and spawning an object within the scene
    Vector3 hitPosition = new Vector3();
#if UNITY_ANDROID || UNITY_IOS
    List<ARRaycastHit> aRRaycastHits = new List<ARRaycastHit>();
    if(arRaycastManager.Raycast(touch.position, aRRaycastHits) && aRRaycastHits.Count > 0)
    {
        ARRaycastHit hit = aRRaycastHits[0];
        hitPosition = hit.pose.position;
    }
#elif WINDOWS_UWP || UNITY_WSA
    RaycastHit hit;
    if (this.TryGazeHitTest(out hit))
    {
        hitPosition = hit.point;
    }
#endif

    Quaternion rotation = Quaternion.AngleAxis(0, Vector3.up);
    this.localAnchor = GameObject.Instantiate(/* some prefab */, hitPosition, rotation);
    this.localAnchor.AddARAnchor();

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
    cloudAnchor.LocalAnchor = this.localAnchor.GetNativeAnchorPointer();
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
    this.feedback = $"Created a cloud anchor with ID={cloudAnchor.Identifier}");
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Dowiedz się więcej o metodzie [GetSessionStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getsessionstatusasync) .

```csharp
    SessionStatus value = await this.cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Dowiedz się więcej o właściwości [AppProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.appproperties) .

```csharp
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor() { LocalAnchor = localAnchor };
    cloudAnchor.AppProperties[@"model-type"] = @"frame";
    cloudAnchor.AppProperties[@"label"] = @"my latest picture";
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Dowiedz się więcej o metodzie [UpdateAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.updateanchorpropertiesasync) .

```csharp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties[@"last-user-access"] = @"just now";
    await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Dowiedz się więcej o metodzie [GetAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getanchorpropertiesasync) .

```csharp
    var anchor = await cloudSession.GetAnchorPropertiesAsync(@"anchorId");
    if (anchor != nullptr)
    {
        anchor.AppProperties[@"last-user-access"] = @"just now";
        await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Dowiedz się więcej o właściwości [wygaśnięcia](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.expiration) .

```csharp
    cloudAnchor.Expiration = DateTimeOffset.Now.AddDays(7);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Dowiedz się więcej [o metodzie](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.createwatcher) .

```csharp
    AnchorLocateCriteria criteria = new AnchorLocateCriteria();
    criteria.Identifiers = new string[] { @"id1", @"id2", @"id3" };
    this.cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Dowiedz się więcej na temat delegata [AnchorLocatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.anchorlocateddelegate) .

```csharp
    this.cloudSession.AnchorLocated += (object sender, AnchorLocatedEventArgs args) =>
    {
        switch (args.Status)
        {
            case LocateAnchorStatus.Located:
                CloudSpatialAnchor foundAnchor = args.Anchor;
                // Go add your anchor to the scene...
                break;
            case LocateAnchorStatus.AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus.NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus.NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Dowiedz się więcej o metodzie [DeleteAnchorAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.deleteanchorasync) .

```csharp
    await this.cloudSession..DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Dowiedz się więcej o metodzie [stop](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.stop) .

```csharp
    this.cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Dowiedz się więcej [](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.reset) o metodzie resetowania.

```csharp
    this.cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-unity.md)]

Dowiedz się więcej o metodzie [Dispose](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.dispose) .

```csharp
    this.cloudSession.Dispose();
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
