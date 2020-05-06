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
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802182"
---
# <a name="what-is-a-keyword"></a>Co to jest słowo kluczowe?

Słowo kluczowe jest słowem lub krótką frazą umożliwiającą produktowi aktywację głosu. Na przykład "Hey Cortana" jest słowem kluczowym dla Asystenta Cortany. Aktywacja głosowa umożliwia użytkownikom rozpoczęcie pracy z produktem całkowicie bezpłatnym, po prostu mówiąc słowa kluczowego. Gdy produkt ciągle nasłuchuje słowa kluczowego, cały dźwięk jest przetwarzany lokalnie na urządzeniu użytkownika do momentu wykrycia, aby zapewnić, że dane będą przechowywane jako prywatne, jak to możliwe.

## <a name="core-features-of-custom-keyword"></a>Podstawowe funkcje niestandardowego słowa kluczowego

Za pomocą funkcji dostosowywania, wydajności i integracji niestandardowego słowa kluczowego można dostosować aktywację głosu, aby najlepiej odpowiadała potrzebom użytkownika i użytkownikom.

| Funkcja | Opis |
|----------|----------|
| Dostosowywanie słowa kluczowego | Jako rozszerzenie marki, słowo kluczowe wzmacnia udział, który został skompilowany przez klientów. Portal niestandardowych słów kluczowych w programie Speech Studio umożliwia określenie dowolnego wyrazu lub krótkiej frazy, która najlepiej reprezentuje swoją markę. Możesz dodatkowo personalizować słowo kluczowe, wybierając odpowiednie wymowy, które będą honorowane przez wygenerowany model słów kluczowych.
| Weryfikacja słowa kluczowego | Gdy słowo kluczowe o wysokim poziomie pewności jest wykrywane lokalnie, dźwięk jest wysyłany do chmury w celu dalszej weryfikacji, że użytkownik wypowiedział słowo kluczowe. Weryfikacja słowa kluczowego stanowi dodatkową warstwę zabezpieczeń, skracając wpływ nieprawidłowego wykrywania lokalnego i ochrony prywatności użytkowników.
| Asystent głosowy & integrację z zestawem SDK mowy | Słowa kluczowe generowane na podstawie niestandardowego słowa kluczowego w programie Speech Studio można łatwo zintegrować z urządzeniem lub aplikacją za pomocą zestawu Speech SDK. Po prostu wskaż zestaw SDK modelem słowa kluczowego dostarczanym przez program Speech Studio, a Twój produkt zostanie aktywowany głosem, a następnie spróbuj wykonać weryfikację słowa kluczowego. Możesz dokończyć pracę z dźwiękiem swojego produktu, tworząc własny [asystenta głosowego](voice-assistants.md).

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod służący do integrowania modelu słów kluczowych jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację kliencką służącą do integracji słowa kluczowego w kilku popularnych językach programowania.

* [Samouczek: głos — Aktywacja produktu przy użyciu zestawu Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)

## <a name="tutorial"></a>Samouczek

* Jak [utworzyć niestandardowe słowo kluczowe przy użyciu programu Speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
* Jak [głosować produkt przy użyciu zestawu Speech SDK](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Niestandardowe wskazówki dotyczące nazewnictwa słów kluczowych](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
* [Tworzenie niestandardowego słowa kluczowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Dowiedz się więcej na temat asystentów głosowych](voice-assistants.md)
