---
title: Przewodnik Szybki Start z transkrypcją w czasie rzeczywistym — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać transkrypcji konwersacji w czasie rzeczywistym za pomocą zestawu Speech SDK. Transkrypcja konwersacji umożliwia transkrypcja spotkań i innych konwersacji z możliwością dodawania, usuwania i identyfikowania wielu uczestników za pomocą przesyłania strumieniowego audio do usługi mowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 48cd4c7996eabad7293aa2429c76b8943e0ab3da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100368476"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Wprowadzenie do transkrypcji konwersacji w czasie rzeczywistym

Interfejs API **ConversationTranscriber** zestawu mowy SDK umożliwia transkrypcja spotkań i innych konwersacji z możliwością dodawania, usuwania i identyfikowania wielu uczestników przez przesyłanie strumieniowe plików audio do usługi mowy przy użyciu `PullStream` lub `PushStream` . Najpierw utwórz podpisy głosowe dla każdego uczestnika przy użyciu interfejsu API REST, a następnie użyj podpisów głosowych z zestawem SDK, aby transkrypcja konwersacje. Aby uzyskać więcej informacji, zobacz [Omówienie](conversation-transcription.md) transkrypcji konwersacji.

## <a name="limitations"></a>Ograniczenia

* Dostępne tylko w następujących regionach subskrypcji: `centralus` ,, `eastasia` `eastus` , `westeurope`
* Wymaga cyklicznej macierzy wielomikrofonowej z 7 mikrofonem. Tablica mikrofonów powinna być zgodna z [naszą specyfikacją](./speech-devices-sdk-microphone.md).
* [Zestaw SDK urządzeń mowy](speech-devices-sdk.md) udostępnia odpowiednie urządzenia i przykładową aplikację pokazującą transkrypcję konwersacji.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Transkrypcja](how-to-async-conversation-transcription.md) 
>  konwersacji asynchronicznej [Przykładowy kod](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  urządzenia ROOBO [Przykładowy kod usługi Azure urządzenia Kinect dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)