---
title: Rejestrowanie i diagnostyka
description: Szczegółowe wyjaśnienie sposobu generowania i pobierania rejestrowania i diagnostyki w zakotwiczeniach przestrzennych platformy Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494645"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Rejestrowanie i Diagnostyka w kotwicach przestrzennych platformy Azure

Kotwice przestrzenne platformy Azure udostępniają standardowy mechanizm rejestrowania przydatny do tworzenia aplikacji. Tryb rejestrowania diagnostyki zakotwiczenia przestrzennego jest przydatny, gdy potrzebne są więcej informacji na potrzeby debugowania. Rejestrowanie diagnostyczne przechowuje obrazy środowiska.

## <a name="standard-logging"></a>Rejestrowanie standardowe
W interfejsie API kotwic przestrzennych można subskrybować mechanizm rejestrowania, aby uzyskać przydatne dzienniki do tworzenia i debugowania aplikacji. Standardowe interfejsy API rejestrowania nie przechowują zdjęć środowiska na dysku urządzenia. Zestaw SDK udostępnia te dzienniki jako wywołania zwrotne zdarzeń. Można zintegrować te dzienniki z mechanizmem rejestrowania aplikacji.

### <a name="configuration-of-log-messages"></a>Konfiguracja komunikatów dziennika
Istnieją dwa wywołania zwrotne, które są istotne dla użytkownika. Poniższy przykład pokazuje, jak skonfigurować sesję.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Zdarzenia i właściwości

Te wywołania zwrotne zdarzeń są udostępniane w celu przetworzenia dzienników i błędów z sesji:

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): określa poziom szczegółowości zdarzeń, które mają zostać odebrane ze środowiska uruchomieniowego.
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): zawiera standardowe zdarzenia dziennika debugowania.
- [Błąd](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): zawiera zdarzenia dziennika, których środowisko uruchomieniowe traktuje jako błędy.

## <a name="diagnostics-logging"></a>Rejestrowanie diagnostyczne

Poza standardowym trybem operacji rejestrowania, kotwice przestrzenne mają również tryb diagnostyki. Tryb diagnostyczny przechwytuje obrazy środowiska i rejestruje je na dysku. Tego trybu można używać do debugowania niektórych rodzajów problemów, takich jak nieoczekiwane zlokalizowanie zakotwiczenia. Włącz rejestrowanie diagnostyczne tylko w celu odtworzenia konkretnego problemu. Następnie go wyłączyć. Nie włączaj diagnostyki, gdy aplikacje są normalnie uruchamiane.

Podczas interakcji z firmą Microsoft przedstawiciel firmy Microsoft może zadawać, czy chcemy przesłać pakiet diagnostyczny w celu dalszej analizy. W takim przypadku użytkownik może zdecydować się na włączenie diagnostyki i odtworzenie problemu, aby można było przesłać pakiet diagnostyczny.

Jeśli przesyłasz dziennik diagnostyczny do firmy Microsoft bez wcześniejszego potwierdzenia od przedstawiciela firmy Microsoft, przesyłanie nie zostanie odebrane.

W poniższych sekcjach pokazano, jak włączyć tryb diagnostyczny, a także przesłać dzienniki diagnostyczne do firmy Microsoft.

### <a name="enable-diagnostics-logging"></a>Włączanie rejestrowania diagnostycznego

Po włączeniu sesji w celu zarejestrowania diagnostyki wszystkie operacje w sesji mają odpowiednie rejestrowanie diagnostyczne w lokalnym systemie plików. Podczas rejestrowania obrazy środowiska są zapisywane na dysku.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Prześlij pakiet diagnostyczny

Poniższy fragment kodu pokazuje, jak przesłać pakiet diagnostyczny do firmy Microsoft. Ten pakiet będzie zawierać obrazy środowiska przechwycone przez sesję po włączeniu diagnostyki.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Części zbioru diagnostyki
Pakiet diagnostyczny może zawierać następujące informacje:

- **Obrazy klatek kluczowych**: obrazy środowiska przechwycone podczas sesji, podczas gdy Diagnostyka została włączona.
- **Dzienniki**: zdarzenia dziennika zarejestrowane przez środowisko uruchomieniowe.
- **Metadane sesji**: metadane identyfikujące sesję.