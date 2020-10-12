---
title: Konfigurowanie usługi Remote Rendering dla środowiska Unity
description: Jak zainicjować zdalne renderowanie na platformie Azure w projekcie środowiska Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a0be44d8709726e159e17e703566c6c576bc18f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018981"
---
# <a name="set-up-remote-rendering-for-unity"></a>Konfigurowanie usługi Remote Rendering dla środowiska Unity

Aby włączyć zdalne renderowanie na platformie Azure (ARR) w środowisku Unity, udostępniamy dedykowane metody, które uwzględniają pewne aspekty specyficzne dla aparatu Unity.

## <a name="startup-and-shutdown"></a>Uruchamianie i zamykanie

Aby zainicjować renderowanie zdalne, użyj `RemoteManagerUnity` . Ta klasa wywołuje ogólne, `RemoteManager` ale już implementuje szczegółowe dane specyficzne dla aparatu Unity. Na przykład środowisko Unity używa określonego układu współrzędnych. Podczas wywoływania `RemoteManagerUnity.Initialize` odpowiednia Konwencja zostanie skonfigurowana. Wywołanie wymaga również podania aparatu Unity, który ma być używany do wyświetlania zdalnie renderowanej zawartości.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Aby zamknąć zdalne renderowanie, wywołaj polecenie `RemoteManagerStatic.ShutdownRemoteRendering()` .

Po `AzureSession` utworzeniu i wybraniu jako podstawowej sesji renderowania należy zarejestrować ją w `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Pełny przykładowy kod

Poniższy kod przedstawia wszystkie kroki niezbędne do zainicjowania zdalnego renderowania platformy Azure w środowisku Unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funkcje wygodne

### <a name="session-state-events"></a>Zdarzenia stanu sesji

`RemoteManagerUnity.OnSessionUpdate` emituje zdarzenia dotyczące czasu zmiany stanu sesji, zobacz dokumentację kodu, aby uzyskać szczegółowe informacje.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` jest opcjonalnym składnikiem do usprawniania instalacji i zarządzania sesją. Zawiera opcje automatycznego zatrzymania sesji, gdy aplikacja jest zamykana lub tryb odtwarzania zostanie zakończony w edytorze, a także automatycznie odnawia dzierżawę sesji w razie potrzeby. W pamięci podręcznej dane takie jak właściwości sesji (patrz jego `LastProperties` zmienna) i ujawniają zdarzenia dotyczące zmian stanu sesji i błędów sesji.

Nie może istnieć więcej niż jedno wystąpienie `ARRServiceUnity` naraz. Jest to przeznaczone do szybszego uruchamiania przez implementację niektórych typowych funkcji. W przypadku większej aplikacji może być zalecane Samodzielne wykonywanie tych czynności.

Aby zapoznać się z przykładem sposobu konfigurowania i używania, `ARRServiceUnity` Zobacz [Samouczek: wyświetlanie zdalnie renderowane modele](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Następne kroki

* [Instalowanie pakietu usługi Remote Rendering dla środowiska Unity](install-remote-rendering-unity-package.md)
* [Samouczek: Wyświetlanie modeli renderowanych zdalnie](../../tutorials/unity/view-remote-models/view-remote-models.md)
