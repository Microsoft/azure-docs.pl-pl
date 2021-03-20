---
title: Testowanie aplikacji z poleceniami niestandardowymi
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono różne podejścia do testowania aplikacji poleceń niestandardowych.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021817"
---
# <a name="test-your-custom-commands-application"></a>Testowanie aplikacji poleceń niestandardowych

W tym artykule przedstawiono różne podejścia do testowania aplikacji poleceń niestandardowych.

## <a name="test-in-the-portal"></a>Testowanie w portalu

Testowanie w portalu jest najprostszym i najszybszym sposobem sprawdzenia, czy aplikacja polecenia niestandardowego działa zgodnie z oczekiwaniami. Po pomyślnym przeszkoleniu aplikacji kliknij `Test` przycisk, aby rozpocząć testowanie.

> [!div class="mx-imgBorder"]
> ![Testowanie w portalu](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Testowanie za pomocą klienta asystenta głosowego systemu Windows

Klient asystenta głosowego systemu Windows to aplikacja Windows Presentation Foundation (WPF) w języku C#, która ułatwia testowanie interakcji z bot przed utworzeniem niestandardowej aplikacji klienckiej.

Narzędzie może akceptować identyfikator aplikacji polecenia niestandardowego. Pozwala ona testować ukończenie zadania lub scenariusz kontroli poleceń i sterowania obsługiwanego w usłudze poleceń niestandardowych.

Aby skonfigurować klienta, Wyewidencjonuj [klienta asystenta głosowego systemu Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![WVAC Utwórz profil](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Testowanie przy użyciu aplikacji klienckich korzystających z zestawu Speech SDK 
Zestaw Speech Software Development Kit (SDK) ujawnia wiele możliwości usługi mowy, które umożliwiają tworzenie aplikacji obsługujących mowę. Jest ona również dostępna w wielu językach programowania i na wszystkich platformach.

Aby skonfigurować aplikację kliencką platforma uniwersalna systemu Windows (platformy UWP) przy użyciu zestawu Speech SDK i zintegrować ją z aplikacją polecenia niestandardowego:  
- [Instrukcje: Integrowanie z aplikacją kliencką przy użyciu zestawu Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)
- [Instrukcje: wysyłanie działania do aplikacji klienckiej](./how-to-custom-commands-send-activity-to-client.md)
- [Instrukcje: Konfigurowanie punktów końcowych sieci Web](./how-to-custom-commands-setup-web-endpoints.md)

W przypadku innych języków programowania i platform:
- [Języki programowania zestawu Speech SDK, platformy, możliwości scenariusza](./speech-sdk.md)
- [Przykładowe kody asystenta głosowego](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zobacz przykłady w witrynie GitHub](https://aka.ms/speech/cc-samples)