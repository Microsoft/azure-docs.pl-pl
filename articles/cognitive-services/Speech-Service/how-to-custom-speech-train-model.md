---
title: Uczenie modelu dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Uczenie modelu zamiany mowy na tekst może poprawić dokładność rozpoznawania dla modelu linii bazowej firmy Microsoft lub modelu niestandardowego. Model jest szkolony z zastosowaniem transkrypcji i powiązanego tekstu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: bf9209e0c256412ccb06ea62a197046a7b012e00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84629018"
---
# <a name="train-a-model-for-custom-speech"></a>Trenowanie modelu dla usługi Custom Speech

Uczenie modelu zamiany mowy na tekst może poprawić dokładność rozpoznawania dla modelu bazowego firmy Microsoft. Model jest szkolony z zastosowaniem transkrypcji i powiązanego tekstu. Te zestawy danych wraz z wcześniej przekazanymi danymi audio są używane do uściślania i uczenia modelu zamiany mowy na tekst.

## <a name="use-training-to-resolve-accuracy-issues"></a>Rozwiązywanie problemów z dokładnością przy użyciu szkoleń

Jeśli napotykasz problemy z rozpoznawaniem z modelem, używanie transkrypcji z etykietami i powiązane dane dla dodatkowego szkolenia może pomóc poprawić dokładność. Użyj tej tabeli, aby określić zestaw danych, który ma być używany do rozwiązywania problemów:

| Przypadek użycia | Typ danych |
| -------- | --------- |
| Popraw dokładność rozpoznawania w przypadku słownictwa i gramatyki właściwych dla branży, takich jak Terminologia medyczna lub żargon IT. | Pokrewny tekst (zdania/wyrażenia długości) |
| Zdefiniuj tekst fonetyczny i wyświetloną wyrazu lub terminu, który ma niestandardową wymowę, taką jak nazwy produktów lub akronimy. | Pokrewny tekst (wymowa) |
| Popraw dokładność rozpoznawania w przypadku stylów, akcentów lub określonych szumów w tle. | Zapisy audio + oznakowane przez człowieka |

> [!IMPORTANT]
> Jeśli zestaw danych nie został przekazany, zapoznaj się [z tematem przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md). Ten dokument zawiera instrukcje dotyczące przekazywania danych oraz wskazówki dotyczące tworzenia zestawów danych o wysokiej jakości.

## <a name="train-and-evaluate-a-model"></a>Trenowanie i ocenianie modelu

Pierwszym krokiem do uczenia modelu jest przekazanie danych szkoleniowych. Użyj opcji [Przygotuj i przetestuj dane](how-to-custom-speech-test-data.md) , aby uzyskać instrukcje krok po kroku w celu przygotowania oznakowania i powiązanego tekstu (wyrażenia długości i wymowy). Po przesłaniu danych szkoleniowych postępuj zgodnie z poniższymi instrukcjami, aby rozpocząć uczenie modelu:

1. Zaloguj się do [portalu Custom Speech](https://speech.microsoft.com/customspeech).
2. Przejdź do **> zamiany mowy na tekst Custom Speech > [nazwa projektu] > szkolenia**.
3. Kliknij pozycję **uczenie modelu**.
4. Następnie Nadaj swojemu szkoleniowi **nazwę** i **Opis**.
5. Z menu rozwijanego **model scenariusza i linia bazowa** Wybierz scenariusz, który najlepiej pasuje do domeny. Jeśli nie masz pewności, który scenariusz wybrać, wybierz pozycję **Ogólne**. Model linii bazowej jest punktem początkowym szkolenia. Najnowszym modelem jest zazwyczaj najlepszy wybór.
6. Na stronie **Wybierz dane szkoleniowe** wybierz jeden lub wiele zestawów danych, które mają być używane do szkoleń.
7. Po zakończeniu szkolenia możesz wybrać przeprowadzenie testowania dokładności dla nowo nauczonego modelu. Ta czynność jest opcjonalna.
8. Wybierz pozycję **Utwórz** , aby skompilować niestandardowy model.

W tabeli szkoleń zostanie wyświetlony nowy wpis, który odnosi się do nowo utworzonego modelu. W tabeli jest również wyświetlany stan: przetwarzanie, zakończone powodzeniem, zakończone niepowodzeniem.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Oceń dokładność przeszkolonego modelu

Możesz sprawdzić dane i oszacować dokładność modeli przy użyciu tych dokumentów:

- [Inspekcja danych](how-to-custom-speech-inspect-data.md)
- [Oceń dane](how-to-custom-speech-evaluate-data.md)

Jeśli zdecydowano się na przetestowanie dokładności, ważne jest, aby wybrać akustyczny zestaw danych, który różni się od tego, który był używany z modelem w celu uzyskania realistycznego sensu wydajności modelu.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
- [Inspekcja danych](how-to-custom-speech-inspect-data.md)
- [Oceń dane](how-to-custom-speech-evaluate-data.md)
- [Szkolenie modelu](how-to-custom-speech-train-model.md)
