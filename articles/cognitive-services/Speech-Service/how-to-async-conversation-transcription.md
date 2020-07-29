---
title: Zapis konwersacji asynchronicznej — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać asynchronicznego transkrypcji konwersacji przy użyciu usługi mowy. Dostępne tylko dla języków Java i C#.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 65a6fd7e0e1e64a459a46c0f97a2afdbc03573a9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284226"
---
# <a name="asynchronous-conversation-transcription"></a>Asynchroniczna transkrypcja konwersacji

W tym artykule, transkrypcja konwersacji asynchronicznej jest wykazana przy użyciu interfejsu API **RemoteConversationTranscriptionClient** . Jeśli skonfigurowano transkrypcję konwersacji w celu przeprowadzania asynchronicznego transkrypcji i masz `conversationId` , możesz uzyskać transkrypcję skojarzoną z tym, `conversationId` używając interfejsu API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroniczny a w czasie rzeczywistym + asynchronicznie

Dzięki operacji transkrypcji asynchronicznej można przesyłać strumieniowo konwersacje audio, ale nie musi istnieć transkrypcja zwrócona w czasie rzeczywistym. Zamiast tego, po wysłaniu dźwięku, należy użyć `conversationId` programu `Conversation` do zapytania o stan asynchronicznego transkrypcji. Gdy asynchroniczne transkrypcje jest gotowe, uzyskasz `RemoteConversationTranscriptionResult` .

W czasie rzeczywistym i asynchronicznie można uzyskać transkrypcję w czasie rzeczywistym, ale również uzyskać transkrypcję, wykonując zapytania z `conversationId` (podobnie jak w scenariuszu asynchronicznym).

Do wykonania asynchronicznego transkrypcji wymagane są dwa kroki. Pierwszym krokiem jest przekazanie dźwięku, wybranie opcji tylko asynchroniczne lub w czasie rzeczywistym, a asynchronicznie. Drugim krokiem jest uzyskanie wyników transkrypcji.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
