---
title: Moderowanie obrazów — Content Moderator
titleSuffix: Azure Cognitive Services
description: Content Moderator korzystaj z narzędzia do oceny obrazów z erotyczneją maszynową i w pętli, aby uzyskać umiarkowane obrazy dotyczące zawartości dla dorosłych i.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800999"
---
# <a name="learn-image-moderation-concepts"></a>Poznaj pojęcia związane z moderowaniem obrazu

Za pomocą [Narzędzia do oceny](Review-Tool-User-Guide/human-in-the-loop.md) i erotycznej obrazów wspieranych maszynowo można Content moderator używać narzędzi do moderowania obrazów dla dorosłych i zawartości. Skanuj obrazy pod kątem zawartości tekstowej i Wyodrębnij ten tekst oraz wykrywaj twarze. Obrazy można dopasować do list niestandardowych i podejmować dalsze działania.

## <a name="evaluating-for-adult-and-racy-content"></a>Ocenianie zawartości dla osób dorosłych i erotycznej

Operacja **szacowania** zwraca wynik zaufania z przedziału od 0 do 1. Zwraca również dane logiczne o wartości true lub false. Te wartości przewidują, czy obraz zawiera potencjalną zawartość dla dorosłych lub erotycznej. Po wywołaniu interfejsu API za pomocą obrazu (pliku lub adresu URL) zwrócona odpowiedź zawiera następujące informacje:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - Obiekt `isImageAdultClassified` reprezentuje potencjalną obecność obrazów, które mogą być uznane za seksualne lub zawierające treści dla dorosłych w niektórych sytuacjach.
> - Obiekt `isImageRacyClassified` reprezentuje potencjalną obecność obrazów, które mogą być uznane za potencjalnie seksualne lub zawierające treści dla dorosłych w niektórych sytuacjach.
> - Wyniki są z przedziału od 0 do 1. Im wyższy wynik, tym większy jest przewidywanie, że Kategoria może być stosowana. Ta wersja zapoznawcza polega na modelu statystycznym zamiast ręcznie zakodowanych wyników. Zalecamy testowanie przy użyciu własnej zawartości, aby określić, jak każda kategoria jest wyrównana do wymagań.
> - Wartości logiczne to true lub false, w zależności od wewnętrznych progów wyniku. Klienci powinni ocenić, czy należy używać tej wartości, czy też decydować o niestandardowych progach na podstawie ich zasad dotyczących zawartości.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Wykrywanie tekstu za pomocą optycznego rozpoznawania znaków (OCR)

Operacja **optycznego rozpoznawania znaków (OCR)** przewiduje obecność zawartości tekstowej w obrazie i wyodrębnia ją na potrzeby moderowania tekstu, między innymi. Możesz określić język. Jeśli nie określisz języka, wykrycie zostanie domyślnie użyte w języku angielskim.

Odpowiedź zawiera następujące informacje:
- Oryginalny tekst.
- Wykryte elementy tekstowe z ich wynikami zaufania.

Przykładowe wyodrębnienie:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>Wykrywanie twarzy

Wykrywanie twarzy pomaga wykrywać dane osobowe, takie jak twarze na obrazach. Wykrywasz potencjalną twarze i liczbę potencjalnych twarzy w każdym obrazie.

Odpowiedź obejmuje następujące informacje:

- Liczba twarzy
- Lista lokalizacji wykrytych twarzy

Przykładowe wyodrębnienie:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>Tworzenie list niestandardowych i zarządzanie nimi

W wielu społecznościach online, gdy Użytkownicy przekazują obrazy lub zawartość innego typu, obraźliwe elementy mogą być udostępniane wiele razy w ciągu następujących dni:, tygodnie i miesiące. Koszty wielokrotnego skanowania i filtrowania tego samego obrazu lub nawet nieco zmodyfikowane wersje obrazu z wielu miejsc mogą być kosztowne i podatne na błędy.

Zamiast moderowania tego samego obrazu wiele razy Dodaj obraźliwe obrazy do niestandardowej listy zablokowanych zawartości. Dzięki temu system moderowania zawartości porównuje obrazy przychodzące z listami niestandardowymi i zakończy dalsze przetwarzanie.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

Content Moderator zawiera kompletny [interfejs API zarządzania listami obrazów](try-image-list-api.md) z operacjami do zarządzania listami obrazów niestandardowych. Zacznij od [obrazu konsola interfejsu](try-image-list-api.md) API i użyj przykładów kodu interfejsu API REST. Zapoznaj się również z [listą obrazów programu .NET — szybki start](image-lists-quickstart-dotnet.md) , jeśli znasz programy Visual Studio i C#.

## <a name="matching-against-your-custom-lists"></a>Dopasowanie do list niestandardowych

Operacja dopasowania umożliwia rozmyte Dopasowywanie obrazów przychodzących do dowolnych list niestandardowych utworzonych i zarządzanych przy użyciu operacji na liście.

W przypadku znalezienia dopasowania operacja zwraca identyfikator i Tagi moderowania dopasowanego obrazu. Odpowiedź obejmuje następujące informacje:

- Wynik dopasowania (od 0 do 1)
- Dopasowany obraz
- Tagi obrazu (przypisane podczas poprzedniej moderowania)
- Etykiety obrazów

Przykładowe wyodrębnienie:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>Narzędzie do przeglądu

Aby uzyskać więcej złożonychych przypadków, użyj [Narzędzia do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md) Content moderator i jego interfejsu API, aby przedstawić wyniki moderowania i zawartość w przeglądzie dla moderatorów ludzkich. Sprawdzają one znaczniki przypisane do maszyn i potwierdzają ich ostateczne decyzje.

![Przeglądanie obrazu przez moderatorów-ludzi](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Następne kroki

Przetestuj [konsolę interfejsu API moderowania obrazów](try-image-api.md) i użyj przykładów kodu interfejsu API REST. Zobacz też [przeglądy, przepływy pracy i zadania,](./review-api.md) aby dowiedzieć się, jak skonfigurować Recenzje przez człowieka.
