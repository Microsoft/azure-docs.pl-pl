---
title: Plik dyrektywy include
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590994"
---
|Poziom|Ustawienia interfejsu użytkownika|Ustawienie interfejsu API|Informacje|
|--|--|--|--|
|Aplikacja|Ustaw punkty końcowe jako publiczne|`Public`|Każdy użytkownik może uzyskać dostęp do publicznej aplikacji, jeśli ma klucz predykcyjny i zna identyfikator aplikacji. |
|Wersja|Użyj szkolenia niedeterministycznego|`UseAllTrainingData`|Szkolenie zużywa niewielką część próbkowania negatywnego. Jeśli chcesz używać wszystkich danych zamiast małego próbkowania negatywnego, ustaw wartość `true` . |
|Wersja|Normalizowanie znaków diakrytycznych|`NormalizeDiacritics`|Normalizacja znaków diakrytycznych zastępuje znaki znakami diakrytycznymi w wyrażenia długości z regularnymi znakami.|
|Wersja|Normalizowanie interpunkcji|`NormalizePunctuation`|Normalizacja interpunkcji oznacza, że zanim modele są przeszkolone i zanim zapytania punktu końcowego zostaną przewidywalne, interpunkcja zostanie usunięta z wyrażenia długości.|
|Wersja|Normalizowanie formularzy wyrazów|`NormalizeWordForm`|Ignoruj formularze programu Word poza folderem głównym.|

Poznaj [koncepcje](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) normalizacji oraz sposób używania interfejsów API [aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) i [wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) do aktualizowania tych ustawień lub korzystania z sekcji **zarządzania** portalu Luis, strony **ustawień aplikacji** .