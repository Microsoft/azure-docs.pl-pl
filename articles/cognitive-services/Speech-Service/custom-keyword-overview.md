---
title: Niestandardowe słowa kluczowe — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń niestandardowych słów kluczowych przy użyciu zestawu Speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 60bcdac0d7e19c424b007980294898638814d586
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044869"
---
# <a name="what-is-a-keyword"></a>Co to jest słowo kluczowe?

Słowo kluczowe jest słowem lub krótką frazą umożliwiającą produktowi aktywację głosu. Na przykład "Hey Cortana" jest słowem kluczowym dla Asystenta Cortany. Aktywacja głosowa umożliwia użytkownikom rozpoczęcie pracy z produktem całkowicie bezpłatnym, po prostu mówiąc słowa kluczowego. Gdy produkt ciągle nasłuchuje słowa kluczowego, cały dźwięk jest przetwarzany lokalnie na urządzeniu użytkownika do momentu wykrycia, aby zapewnić, że dane będą przechowywane jako prywatne, jak to możliwe.

## <a name="core-features-of-custom-keyword"></a>Podstawowe funkcje niestandardowego słowa kluczowego

Za pomocą funkcji dostosowywania, wydajności i integracji niestandardowego słowa kluczowego można dostosować aktywację głosu, aby najlepiej odpowiadała potrzebom użytkownika i użytkownikom.

| Cechy | Opis |
|----------|----------|
| Dostosowywanie słowa kluczowego | Jako rozszerzenie marki, słowo kluczowe wzmacnia udział, który został skompilowany przez klientów. Portal niestandardowych słów kluczowych w programie Speech Studio umożliwia określenie dowolnego wyrazu lub krótkiej frazy, która najlepiej reprezentuje swoją markę. Możesz dodatkowo personalizować słowo kluczowe, wybierając odpowiednie wymowy, które będą honorowane przez wygenerowany model słów kluczowych.
| Weryfikacja słowa kluczowego | Gdy słowo kluczowe o wysokim poziomie pewności jest wykrywane lokalnie, dźwięk jest wysyłany do chmury w celu dalszej weryfikacji, że użytkownik wypowiedział słowo kluczowe. Weryfikacja słowa kluczowego stanowi dodatkową warstwę zabezpieczeń, skracając wpływ nieprawidłowego wykrywania lokalnego i ochrony prywatności użytkowników.
| Asystent głosowy & integrację z zestawem SDK mowy | Słowa kluczowe generowane na podstawie niestandardowego słowa kluczowego w programie Speech Studio można łatwo zintegrować z urządzeniem lub aplikacją za pomocą zestawu Speech SDK. Po prostu wskaż zestaw SDK modelem słowa kluczowego dostarczanym przez program Speech Studio, a Twój produkt zostanie aktywowany głosem, a następnie spróbuj wykonać weryfikację słowa kluczowego. Możesz dokończyć pracę z dźwiękiem swojego produktu, tworząc własny [asystenta głosowego](voice-assistants.md).

## <a name="get-started-with-custom-keywords"></a>Wprowadzenie do niestandardowych słów kluczowych

* Zapoznaj się z [niestandardowymi słowami kluczowymi](custom-keyword-basics.md) dotyczącymi podstawowych zastosowań i wzorców projektu.
* Jak [głosować produkt za pomocą zestawu Speech SDK przy użyciu języka C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="see-samples-on-github"></a>Zobacz przykłady w witrynie GitHub

* [Rozpoznawanie słów kluczowych przy użyciu zestawu Speech SDK na platforma uniwersalna systemu Windows przy użyciu języka C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Rozpoznawanie słów kluczowych za pomocą zestawu Speech SDK w systemie Android przy użyciu języka Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Dowiedz się więcej na temat asystentów głosowych](voice-assistants.md)
