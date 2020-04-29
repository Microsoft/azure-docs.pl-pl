---
title: Konfigurowanie usługi Remote Rendering dla środowiska Unity
description: Jak zainicjować zdalne renderowanie na platformie Azure w projekcie środowiska Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681144"
---
# <a name="set-up-remote-rendering-for-unity"></a>Konfigurowanie usługi Remote Rendering dla środowiska Unity

Aby włączyć zdalne renderowanie na platformie Azure (ARR) w środowisku Unity, udostępniamy dedykowane metody, które uwzględniają pewne aspekty specyficzne dla aparatu Unity.

## <a name="startup-and-shutdown"></a>Uruchamianie i zamykanie

Aby zainicjować renderowanie zdalne, `RemoteManagerUnity`Użyj. Ta klasa wywołuje ogólne `RemoteManager` , ale już implementuje szczegółowe dane specyficzne dla aparatu Unity. Na przykład środowisko Unity używa określonego układu współrzędnych. Podczas wywoływania `RemoteManagerUnity.Initialize`odpowiednia Konwencja zostanie skonfigurowana. Wywołanie wymaga również podania aparatu Unity, który ma być używany do wyświetlania zdalnie renderowanej zawartości.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Aby zamknąć zdalne renderowanie, wywołaj `RemoteManagerStatic.ShutdownRemoteRendering()`polecenie.

`AzureSession` Po utworzeniu i wybraniu jako podstawowej sesji renderowania należy zarejestrować ją w `RemoteManagerUnity`:

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

`RemoteManagerUnity.OnSessionUpdate`emituje zdarzenia dotyczące czasu zmiany stanu sesji, zobacz dokumentację kodu, aby uzyskać szczegółowe informacje.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`jest opcjonalnym składnikiem do usprawniania instalacji i zarządzania sesją. Zawiera opcje automatycznego zatrzymania sesji, gdy aplikacja jest zamykana lub tryb odtwarzania zostanie zakończony w edytorze, a także automatycznie odnawia dzierżawę sesji w razie potrzeby. W pamięci podręcznej dane takie jak właściwości sesji (patrz `LastProperties` jego zmienna) i ujawniają zdarzenia dotyczące zmian stanu sesji i błędów sesji.

Nie może istnieć więcej niż jedno wystąpienie `ARRServiceUnity` naraz. Jest to przeznaczone do szybszego uruchamiania przez implementację niektórych typowych funkcji. W przypadku większej aplikacji może być zalecane Samodzielne wykonywanie tych czynności.

Aby zapoznać się z przykładem sposobu konfigurowania i `ARRServiceUnity` używania, zobacz [Samouczek: Konfigurowanie projektu Unity od podstaw](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>Następne kroki

* [Instalowanie pakietu usługi Remote Rendering dla środowiska Unity](install-remote-rendering-unity-package.md)
* [Samouczek: Konfigurowanie projektu Unity od podstaw](../../tutorials/unity/project-setup.md)
