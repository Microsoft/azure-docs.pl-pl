---
title: 'Szybki Start: konfiguracja zestawu mowy SDK dla języka C# .NET Core — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby skonfigurować platformę dla języka C# w środowisku .NET Core w systemie Windows lub macOS z zestawem SDK usługi Speech Service.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6f29fa55389e181eaabf1d6e7c51e0862702c63e
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552755"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla języka C# .NET Core. Jeśli chcesz, aby nazwa pakietu została już uruchomiona, uruchom polecenie `Install-Package Microsoft.CognitiveServices.Speech` w konsoli programu NuGet.

> [!NOTE]
> .NET Core jest międzyplatformową platformą .NET typu open source, która wdraża specyfikację [.NET Standard](/dotnet/standard/net-standard).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* W systemie Windows wymagany jest [Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) dla danej platformy. Zainstalowanie tego po raz pierwszy może wymagać ponownego uruchomienia.
* [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download)
* [Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) lub nowszy

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Tworzenie projektu programu Visual Studio i Instalowanie zestawu Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

Teraz możesz przejść do [kolejnych kroków](#next-steps) poniżej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]