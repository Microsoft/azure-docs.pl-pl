---
title: Umiarkowany tekst przy użyciu interfejsu API moderowania tekstu — Content Moderator
titleSuffix: Azure Cognitive Services
description: Moderowanie tekstu dysku testowego za pomocą interfejsu API moderowania tekstu w konsoli online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912875"
---
# <a name="moderate-text-from-the-api-console"></a>Umiarkowany tekst z konsoli interfejsu API

Użyj [interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) w usłudze Azure Content moderator, aby przeskanować zawartość tekstową pod kątem niewulgarności i porównać ją z niestandardowymi i udostępnionymi listami.

## <a name="get-your-api-key"></a>Pobierz klucz interfejsu API

Aby można było przetestować interfejs API w konsoli online, musisz mieć swój klucz subskrypcji. Ta lokalizacja znajduje się na karcie **Ustawienia** w polu **OCP-APIM-Subscription-Key** . Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="navigate-to-the-api-reference"></a>Przejdź do odwołania do interfejsu API

Przejdź do [dokumentacji interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Zostanie otwarta strona **ekran tekstu** .

## <a name="open-the-api-console"></a>Otwórz konsolę interfejsu API

W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 

  ![Wybór obszaru strony ekranu tekstu](images/test-drive-region.png)

  Zostanie otwarta konsola interfejsu API **tekstu** .

## <a name="select-the-inputs"></a>Wybierz dane wejściowe

### <a name="parameters"></a>Parametry

Wybierz parametry zapytania, które mają być używane na ekranie tekstowym. W tym przykładzie Użyj wartości domyślnej dla **języka** . Możesz również pozostawić to pole puste, ponieważ operacja automatycznie wykryje prawdopodobnie język w ramach jego wykonywania.

> [!NOTE]
> W polu parametr **języka** Przypisz `eng` lub pozostaw to pole puste, aby wyświetlić odpowiedź **klasyfikacji** z obsługą maszyn (funkcja w wersji zapoznawczej). **Ta funkcja obsługuje tylko język angielski** .
>
> W przypadku wykrywania **warunków wulgarnych** Użyj [kodu ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dla obsługiwanych języków wymienionych w tym artykule lub pozostaw to pole puste.

W przypadku funkcji **Autokorekta** , dane **osobowe** i **klasyfikowanie (wersja zapoznawcza)** wybierz pozycję **prawda** . Pozostaw puste pole **ListId** .

  ![Parametry zapytania konsoli ekranowej](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ zawartości

W **polu Typ zawartości** wybierz typ zawartości, która ma być wykorzystana. Na potrzeby tego przykładu Użyj domyślnego typu zawartości **tekst/zwykły** . W polu **OCP-APIM-Subscription-Key** wprowadź swój klucz subskrypcji.

### <a name="sample-text-to-scan"></a>Przykładowy tekst do skanowania

W polu **treść żądania** wprowadź tekst. W poniższym przykładzie przedstawiono zamierzone literówki w tekście.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analizowanie odpowiedzi

W poniższej odpowiedzi przedstawiono różne informacje z interfejsu API. Zawiera potencjalne nieżywotność, dane osobowe, klasyfikację (wersja zapoznawcza) i automatycznie poprawioną wersję.

> [!NOTE]
> Funkcja klasyfikacji oparta na maszynach jest w wersji zapoznawczej i obsługuje tylko język angielski.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Szczegółowe wyjaśnienie wszystkich sekcji odpowiedzi JSON można znaleźć w przewodniku koncepcyjnym [moderowania tekstu](text-moderation-api.md) .

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub postępuj zgodnie z [przewodnikiem Szybki Start dla zestawu .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) , aby przeprowadzić integrację z aplikacją.