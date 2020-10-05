---
title: 'Szybki Start: Konfiguracja platformy Speech SDK dla języka C# — usługa rozpoznawania mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby skonfigurować platformę dla środowiska C# Unity z zestawem SDK usługi Speech Service.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 7678ffa1684fb1fe1ea198e9e1836a152cd51b4b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88926682"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla [aparatu Unity](https://unity3d.com/).

> [!NOTE]
> Zestaw Speech SDK for Unity obsługuje Pulpity systemu Windows (x86 i x64) lub platforma uniwersalna systemu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) i iOS (x64 symulator, ARM32 i ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

- Aparat [unity 2018,3 lub nowszy](https://store.unity.com/) z funkcją [Unity 2019,1 Dodawanie obsługi platformy UWP arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Wersja 15,9 lub nowsza programu Visual Studio 2017 jest również akceptowalna.
- Aby uzyskać pomoc techniczną dla systemu Windows ARM64, zainstaluj [opcjonalne narzędzia kompilacji dla arm64 i zestaw Windows 10 SDK dla arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Aby zainstalować zestaw Speech SDK dla aparatu Unity, wykonaj następujące kroki:

1. Pobierz i Otwórz [zestaw Speech SDK dla aparatu Unity](https://aka.ms/csspeech/unitypackage), który jest spakowany jako pakiet zasobów aparatu Unity (. UNITYPACKAGE) i powinien być już skojarzony z systemem Unity. Po otwarciu pakietu zasobów zostanie wyświetlone okno dialogowe **Importuj pakiet Unity** . Aby ten krok działał, może być konieczne utworzenie i otwarcie pustego projektu.

   [![Okno dialogowe Importowanie pakietu Unity w edytorze aparatu Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Upewnij się, że wszystkie pliki są zaznaczone, a następnie wybierz pozycję **Importuj**. Po kilku chwilach pakiet zasobów aparatu Unity zostanie zaimportowany do projektu.

Więcej informacji o importowaniu pakietów zasobów do aparatu Unity znajduje się w [dokumentacji aparatu Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Teraz możesz przejść do [kolejnych kroków](#next-steps) poniżej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
