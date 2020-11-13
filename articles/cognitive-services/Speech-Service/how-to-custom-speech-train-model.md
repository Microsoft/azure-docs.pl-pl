---
title: Uczenie i wdrażanie Custom Speech model-Speech Service
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak nauczyć i wdrożyć modele Custom Speech. Uczenie modelu zamiany mowy na tekst może poprawić dokładność rozpoznawania dla modelu linii bazowej firmy Microsoft lub modelu niestandardowego. Model jest szkolony z zastosowaniem transkrypcji i powiązanego tekstu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 34c0703ee7c335ca904a21bcce6ed44abc6dc13f
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555791"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Uczenie i wdrażanie modelu Custom Speech

W tym artykule dowiesz się, jak nauczyć i wdrożyć modele Custom Speech. Uczenie modelu zamiany mowy na tekst może poprawić dokładność rozpoznawania dla modelu bazowego firmy Microsoft. Model jest szkolony z zastosowaniem transkrypcji i powiązanego tekstu. Te zestawy danych wraz z wcześniej przekazanymi danymi audio są używane do uściślania i uczenia modelu zamiany mowy na tekst.

## <a name="use-training-to-resolve-accuracy-issues"></a>Rozwiązywanie problemów z dokładnością przy użyciu szkoleń

Jeśli napotykasz problemy z rozpoznawaniem z modelem podstawowym, używanie transkrypcji z etykietami i pokrewnymi danymi do uczenia modelu niestandardowego może pomóc poprawić dokładność. Użyj tej tabeli, aby określić zestaw danych, który ma być używany do rozwiązywania problemów:

| Przypadek użycia | Typ danych |
| -------- | --------- |
| Popraw dokładność rozpoznawania w przypadku słownictwa i gramatyki właściwych dla branży, takich jak Terminologia medyczna lub żargon IT. | Pokrewny tekst (zdania/wyrażenia długości) |
| Zdefiniuj tekst fonetyczny i wyświetloną wyrazu lub terminu, który ma niestandardową wymowę, taką jak nazwy produktów lub akronimy. | Pokrewny tekst (wymowa) |
| Popraw dokładność rozpoznawania w przypadku stylów, akcentów lub określonych szumów w tle. | Zapisy audio + oznakowane przez człowieka |

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

Zobacz, [jak](how-to-custom-speech-evaluate-data.md) oceniać i usprawnić Custom Speech dokładność modelu. Jeśli zdecydowano się na przetestowanie dokładności, ważne jest, aby wybrać akustyczny zestaw danych, który różni się od tego, który był używany z modelem w celu uzyskania realistycznego sensu wydajności modelu.

## <a name="deploy-a-custom-model"></a>Wdrażanie modelu niestandardowego

Po przekazaniu i sprawdzeniu danych, ocenie dokładności i przeszkoleniu modelu niestandardowego można wdrożyć niestandardowy punkt końcowy do użycia z aplikacjami, narzędziami i produktami. 

Aby utworzyć nowy niestandardowy punkt końcowy, zaloguj się do [portalu Custom Speech](https://speech.microsoft.com/customspeech) i wybierz pozycję **wdrożenie** z menu Custom Speech w górnej części strony. Jeśli jest to pierwsze uruchomienie, Zauważ, że w tabeli nie ma punktów końcowych. Po utworzeniu punktu końcowego należy użyć tej strony do śledzenia wszystkich wdrożonych punktów końcowych.

Następnie wybierz pozycję **Dodaj punkt końcowy** i wprowadź **nazwę** i **Opis** niestandardowego punktu końcowego. Następnie wybierz model niestandardowy, który chcesz skojarzyć z tym punktem końcowym. Na tej stronie można również włączyć rejestrowanie. Rejestrowanie pozwala monitorować ruch punktu końcowego. W przypadku wyłączenia ruch nie będzie przechowywany.

![Jak wdrożyć model](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nie zapomnij zaakceptować warunków użytkowania i szczegółów cennika.

Następnie wybierz pozycję **Utwórz**. Ta akcja powoduje powrót do strony **wdrożenia** . Tabela zawiera teraz wpis odpowiadający niestandardowemu punktowi końcowemu. Stan punktu końcowego pokazuje jego bieżący stan. Utworzenie wystąpienia nowego punktu końcowego przy użyciu modeli niestandardowych może potrwać do 30 minut. Gdy stan wdrożenia zmieni się na **ukończone** , punkt końcowy jest gotowy do użycia.

Po wdrożeniu punktu końcowego nazwa punktu końcowego jest wyświetlana jako link. Kliknij link, aby wyświetlić informacje specyficzne dla danego punktu końcowego, takie jak klucz punktu końcowego, adres URL punktu końcowego i przykładowy kod.

## <a name="view-logging-data"></a>Wyświetlanie danych rejestrowania

Dane rejestrowania są dostępne do pobrania w obszarze **punkt końcowy > szczegóły**.
> [!NOTE]
>Dane rejestrowania są dostępne przez 30 dni w magazynie firmy Microsoft i zostaną później usunięte. W przypadku, gdy konto magazynu należące do klienta jest połączone z subskrypcją usług poznawczej, dane rejestrowania nie zostaną automatycznie usunięte.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak używać modelu niestandardowego](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
- [Inspekcja danych](how-to-custom-speech-inspect-data.md)
- [Oceń dane](how-to-custom-speech-evaluate-data.md)
