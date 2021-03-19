---
title: Plik dyrektywy include
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83653221"
---
Poznaj [koncepcje](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) normalizacji i jak używać interfejsów API [wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) do aktualizowania tych ustawień lub korzystania z sekcji **zarządzania** portalu Luis, strony **ustawień** .


|Ustawienia interfejsu użytkownika|Ustawienie interfejsu API|Informacje|
|--|--|--|
|Użyj szkolenia niedeterministycznego|`UseAllTrainingData`|Szkolenie zużywa niewielką część próbkowania negatywnego. Jeśli chcesz używać wszystkich danych zamiast małego próbkowania negatywnego, ustaw wartość `true` . |
|Normalizowanie znaków diakrytycznych|`NormalizeDiacritics`|Normalizacja znaków diakrytycznych zastępuje znaki znakami diakrytycznymi w wyrażenia długości z regularnymi znakami. To ustawienie jest dostępne tylko w [językach](../luis-reference-application-settings.md#diacritics-normalization) , które obsługują znaki diakrytyczne.|
|Normalizowanie interpunkcji|`NormalizePunctuation`|Normalizacja interpunkcji oznacza, że zanim modele są przeszkolone i zanim zapytania punktu końcowego zostaną przewidywalne, interpunkcja zostanie usunięta z wyrażenia długości.|
|Normalizowanie formularzy wyrazów|`NormalizeWordForm`|Ignoruj formularze programu Word poza folderem głównym.|
