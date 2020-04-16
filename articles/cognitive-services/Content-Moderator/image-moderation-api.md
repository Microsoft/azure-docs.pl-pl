---
title: Moderowanie obrazu - Moderator treści
titleSuffix: Azure Cognitive Services
description: Użyj narzędzia Content Moderator's machine-assisted image moderation and human-in-the-loop Review tool to moderate images for adult and racy content.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 36777208dc8ac179f1aaf345c374a33001e3f8bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404261"
---
# <a name="learn-image-moderation-concepts"></a>Poznaj pojęcia dotyczące moderowania obrazu

Użyj narzędzia Content Moderator's machine-assisted image moderation and [human-in-the-loop Review tool](Review-Tool-User-Guide/human-in-the-loop.md) to moderate images for adult and racy content. Skanuj obrazy w poszukiwaniu zawartości tekstowej i wyodrębnij ten tekst oraz wykrywaj twarze. Obrazy można dopasować do list niestandardowych i podjąć dalsze działania.

## <a name="evaluating-for-adult-and-racy-content"></a>Ocena treści dla dorosłych i rasistowskich

Operacja **Oceń** zwraca wynik zaufania z przedziału od 0 do 1. Zwraca również dane logiczne równe wartości true lub false. Te wartości przewidują, czy obraz zawiera potencjalne treści dla dorosłych lub rasistowskie. Po wywołaniu interfejsu API z obrazem (plikiem lub adresem URL) zwrócona odpowiedź zawiera następujące informacje:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - Obiekt `isImageAdultClassified` reprezentuje potencjalną obecność obrazów, które mogą być uznane za seksualne lub zawierające treści dla dorosłych w niektórych sytuacjach.
> - Obiekt `isImageRacyClassified` reprezentuje potencjalną obecność obrazów, które mogą być uznane za potencjalnie seksualne lub zawierające treści dla dorosłych w niektórych sytuacjach.
> - Wyniki są od 0 do 1. Im wyższy wynik, tym wyższy model przewiduje, że kategoria może mieć zastosowanie. Ta wersja zapoznawcza opiera się na modelu statystycznym, a nie ręcznie kodowane wyniki. Zalecamy przetestowanie z własną zawartością, aby określić, jak każda kategoria jest zgodna z Twoimi wymaganiami.
> - Wartości logiczne są prawdziwe lub false w zależności od wewnętrznych progów wynik. Klienci powinni ocenić, czy użyć tej wartości lub zdecydować o progach niestandardowych na podstawie ich zasad zawartości.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Wykrywanie tekstu za pomocą optycznego rozpoznawania znaków (OCR)

Operacja **optycznego rozpoznawania znaków (OCR)** przewiduje obecność zawartości tekstowej w obrazie i wyodrębnia ją do moderowania tekstu, między innymi. Można określić język. Jeśli język nie zostanie określony, domyślne ustawienie wykrywania na angielski.

Odpowiedź zawiera następujące informacje:
- Oryginalny tekst.
- Wykryte elementy tekstu z ich wynikami zaufania.

Przykładowy wyciąg:

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


## <a name="detecting-faces"></a>Wykrywanie twarzy

Wykrywanie twarzy pomaga wykryć dane osobowe, takie jak twarze na obrazach. Wykrywamy potencjalne twarze i liczbę potencjalnych twarzy na każdym obrazie.

Odpowiedź zawiera te informacje:

- Liczba ścian
- Lista wykrytych lokalizacji twarzy

Przykładowy wyciąg:


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

## <a name="creating-and-managing-custom-lists"></a>Tworzenie list niestandardowych i zarządzanie nimi

W wielu społecznościach internetowych po przesłaniu przez użytkowników obrazów lub innego rodzaju treści obraźliwe elementy mogą być udostępniane wielokrotnie w kolejnych dniach, tygodniach i miesiącach. Koszty wielokrotnego skanowania i filtrowania tego samego obrazu lub nawet nieznacznie zmodyfikowanych wersji obrazu z wielu miejsc mogą być kosztowne i podatne na błędy.

Zamiast moderować ten sam obraz wiele razy, dodajesz obraźliwe obrazy do niestandardowej listy zablokowanych treści. W ten sposób system moderowania zawartości porównuje przychodzące obrazy z listami niestandardowymi i zatrzymuje dalsze przetwarzanie.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

Moderator zawartości udostępnia kompletny [interfejs API zarządzania listami obrazów](try-image-list-api.md) z operacjami zarządzania listami obrazów niestandardowych. Zacznij od [konsoli interfejsu API listy obrazów](try-image-list-api.md) i użyj przykładów kodu interfejsu API REST. Sprawdź również [listę obrazów .NET Szybki start,](image-lists-quickstart-dotnet.md) jeśli znasz visual studio i C#.

## <a name="matching-against-your-custom-lists"></a>Dopasowywanie do list niestandardowych

Operacja Dopasuj umożliwia rozmyte dopasowywanie obrazów przychodzących do dowolnej listy niestandardowej, utworzonej i zarządzanej przy użyciu operacji Lista.

Jeśli zostanie znaleziony dopasowania, operacja zwraca identyfikator i moderowanie tagów dopasowanego obrazu. Odpowiedź zawiera następujące informacje:

- Wynik meczu (od 0 do 1)
- Dopasowany obraz
- Znaczniki obrazu (przypisane podczas poprzedniego moderacji)
- Etykiety obrazów

Przykładowy wyciąg:

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

## <a name="review-tool"></a>Narzędzie do przeglądu

W przypadku bardziej zniuansowanych przypadków użyj [narzędzia Content](Review-Tool-User-Guide/human-in-the-loop.md) Moderator Review i jego interfejsu API, aby wyeksliwać wyniki moderacji i zawartość w recenzji dla moderatorów ludzkich. Przeglądają tagi przypisane maszynie i potwierdzają swoje ostateczne decyzje.

![Przeglądanie obrazu przez moderatorów-ludzi](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Następne kroki

Przetestuj dysk [konsoli interfejsu API moderowania obrazu](try-image-api.md) i użyj przykładów kodu interfejsu API REST. Sprawdź również sekcję moderowanie obrazu w [programie Szybki start .NET SDK,](dotnet-sdk-quickstart.md) jeśli znasz program Visual Studio i C#.
