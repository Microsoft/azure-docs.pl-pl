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
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91356608"
---
# <a name="what-is-a-keyword"></a>Co to jest słowo kluczowe?

Słowo kluczowe jest słowem lub krótką frazą umożliwiającą produktowi aktywację głosu. Na przykład "Hey Cortana" jest słowem kluczowym dla Asystenta Cortany. Aktywacja głosowa umożliwia użytkownikom rozpoczęcie pracy z produktem całkowicie bezpłatnym, po prostu mówiąc słowa kluczowego. Gdy produkt ciągle nasłuchuje słowa kluczowego, cały dźwięk jest przetwarzany lokalnie na urządzeniu użytkownika do momentu wykrycia, aby zapewnić, że dane będą przechowywane jako prywatne, jak to możliwe.

## <a name="core-features-of-custom-keyword"></a>Podstawowe funkcje niestandardowego słowa kluczowego

Za pomocą funkcji dostosowywania, wydajności i integracji niestandardowego słowa kluczowego można dostosować aktywację głosu, aby najlepiej odpowiadała potrzebom użytkownika i użytkownikom.

| Cecha | Opis |
|----------|----------|
| Dostosowywanie słowa kluczowego | Jako rozszerzenie marki, słowo kluczowe wzmacnia udział, który został skompilowany przez klientów. Portal niestandardowych słów kluczowych w programie Speech Studio umożliwia określenie dowolnego wyrazu lub krótkiej frazy, która najlepiej reprezentuje swoją markę. Możesz dodatkowo personalizować słowo kluczowe, wybierając odpowiednie wymowy, które będą honorowane przez wygenerowany model słów kluczowych.
| Weryfikacja słowa kluczowego | Gdy słowo kluczowe o wysokim poziomie pewności jest wykrywane lokalnie, dźwięk jest wysyłany do chmury w celu dalszej weryfikacji, że użytkownik wypowiedział słowo kluczowe. Weryfikacja słowa kluczowego stanowi dodatkową warstwę zabezpieczeń, skracając wpływ nieprawidłowego wykrywania lokalnego i ochrony prywatności użytkowników.
| Asystent głosowy & integrację z zestawem SDK mowy | Słowa kluczowe generowane na podstawie niestandardowego słowa kluczowego w programie Speech Studio można łatwo zintegrować z urządzeniem lub aplikacją za pomocą zestawu Speech SDK. Po prostu wskaż zestaw SDK modelem słowa kluczowego dostarczanym przez program Speech Studio, a Twój produkt zostanie aktywowany głosem, a następnie spróbuj wykonać weryfikację słowa kluczowego. Możesz dokończyć pracę z dźwiękiem swojego produktu, tworząc własny [asystenta głosowego](voice-assistants.md).

## <a name="get-started-with-custom-keywords"></a>Wprowadzenie do niestandardowych słów kluczowych

* Zapoznaj się z [niestandardowymi słowami kluczowymi](custom-keyword-basics.md) dotyczącymi podstawowych zastosowań i wzorców projektu.
* Jak [głosować produkt za pomocą zestawu Speech SDK przy użyciu języka C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>Wybierz efektywne słowo kluczowe

Tworzenie efektywnego słowa kluczowego jest niezbędne do zapewnienia spójnej i dokładnej reakcji urządzenia. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania. Podczas wybierania słowa kluczowego należy wziąć pod uwagę następujące wytyczne:

> [!div class="checklist"]
> * Słowo kluczowe powinno być słowem lub frazą w języku angielskim.
> * Nie może upłynąć dłużej niż dwa sekundy.
> * Słowa z 4 do 7 sylab działają najlepiej. Na przykład "Hej, Computer" jest dobrym słowem kluczowym. Tylko "Hej" jest słabo.
> * Słowa kluczowe powinny być zgodne z typowymi regułami wymowy w języku angielskim
> * Unikatowy lub nawet utworzony wyraz, który jest zgodny z typowymi regułami wymowy w języku angielskim, może obniżyć liczbę fałszywie dodatnich. Na przykład "computerama" może być dobrym słowem kluczowym.
> * Nie wybieraj wspólnego wyrazu. Na przykład "Eat" i "go" są słowami, które ludzie często mówią w zwykłych konwersacjach. Mogą to być fałszywe wyzwalacze dla Twojego urządzenia.
> * Unikaj używania słowa kluczowego, które może mieć alternatywne wymowy. Użytkownicy będą musieli znać wymowę "Right", aby uzyskać odpowiedzi na swoje urządzenia. Na przykład "509" może być wymawiane "5 0 9", "5 0 9" lub "509". "R.E.I." może być wymawiany "r-e-i" lub "ray". "Na żywo" może być wymawiane "/līv/" lub "/Liv/".
> * Nie należy używać znaków specjalnych, symboli ani cyfr. Na przykład "go #" i "20 + koty" mogą być problematycznymi słowami kluczowymi. Jednak może się zdarzyć, że "nie tylko" lub "dwadzieścia i koty". Można nadal używać symboli w znakowaniu i korzystać z marketingu i dokumentacji, aby wzmocnić odpowiednią wymowę.

> [!NOTE]
> Jeśli wybierzesz słowo znakowe jako słowo kluczowe, upewnij się, że jesteś właścicielem tego znaku towarowego lub że masz uprawnienia od właściciela znaku towarowego do korzystania z tego słowa. Firma Microsoft nie ponosi odpowiedzialności za wszelkie problemy prawne, które mogą wystąpić przy wyborze słowa kluczowego.

## <a name="see-samples-on-github"></a>Zobacz przykłady w witrynie GitHub

* [Rozpoznawanie słów kluczowych przy użyciu zestawu Speech SDK na platforma uniwersalna systemu Windows przy użyciu języka C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Rozpoznawanie słów kluczowych za pomocą zestawu Speech SDK w systemie Android przy użyciu języka Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Dowiedz się więcej na temat asystentów głosowych](voice-assistants.md)
