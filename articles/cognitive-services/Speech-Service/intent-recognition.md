---
title: Przegląd rozpoznawania intencji — usługa mowy
titleSuffix: Azure Cognitive Services
description: Rozpoznawanie intencji pozwala na rozpoznawanie zamierzeń użytkowników wstępnie zdefiniowanych. Ten artykuł zawiera omówienie zalet i możliwości usługi rozpoznawania intencji.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: Rozpoznawanie intencji
ms.openlocfilehash: 0d718459e0fd0ea410232d3a165b560aa8c59cd1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174642"
---
# <a name="what-is-intent-recognition"></a>Co to jest rozpoznawanie intencji?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Ten przegląd zawiera informacje na temat zalet i możliwości rozpoznawania intencji. Zestaw Speech SDK usługi Cognitive Services jest integrowany z usługą Language Understanding Service (LUIS) w celu udostępnienia funkcji rozpoznawania intencji. Intencja to coś, co użytkownik chce zrobić: zarezerwować lot, sprawdzić prognozę pogody lub zadzwonić.
Korzystając z rozpoznawania intencji, Twoje aplikacje, narzędzia i urządzenia mogą określić, co użytkownik chce inicjować lub na podstawie opcji zdefiniowanych w LUIS.

## <a name="luis-key-required"></a>Wymagany klucz LUIS

* Usługa LUIS integruje się z usługą rozpoznawania mowy, aby rozpoznawać intencje z mowy. Nie potrzebujesz subskrypcji usługi rozpoznawania mowy, tylko usługi LUIS.
* Funkcja rozpoznawania zamierzeń mowy jest zintegrowana z zestawem SDK. Za pomocą usługi mowy można użyć klucza LUIS.
* Rozpoznawanie intencji za pomocą zestawu Speech SDK jest [oferowane w podzestawie regionów obsługiwanych przez Luis](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#intent-recognition).

## <a name="get-started"></a>Wprowadzenie

Zapoznaj się z [przewodnikiem Szybki Start](quickstarts/intent-recognition.md) , aby rozpocząć pracę z rozpoznawaniem intencji.

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod do rozpoznawania intencji:

* [Szybki start: używanie wstępnie utworzonej aplikacji Home Automation](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)
* [rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK dla języka C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)
* [Rozpoznawanie intencji i inne usługi mowy korzystające z aparatu Unity w języku C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Rozpoznawanie intencji przy użyciu zestawu Speech SDK dla języka Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Rozpoznawanie intencji i inne usługi mowy przy użyciu zestawu Speech SDK dla języka C++ w systemie Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Rozpoznawanie intencji i inne usługi mowy przy użyciu zestawu Speech SDK dla języka Java w systemie Windows lub Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Rozpoznawanie intencji i inne usługi mowy przy użyciu zestawu Speech SDK dla języka JavaScript w przeglądarce internetowej](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)

## <a name="next-steps"></a>Następne kroki

* Ukończ [Przewodnik Szybki Start](quickstarts/intent-recognition.md) dotyczący rozpoznawania intencji
* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
