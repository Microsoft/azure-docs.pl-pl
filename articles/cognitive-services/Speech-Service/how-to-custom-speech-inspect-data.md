---
title: Sprawdzanie jakości danych dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech udostępnia narzędzia umożliwiające wizualne badanie jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania. Możesz odtworzyć przekazany dźwięk i określić, czy podany wynik rozpoznawania jest poprawny.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: 9ce0d3a06846cbc3aa37ab836564150e6f2c34ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100362815"
---
# <a name="inspect-custom-speech-data"></a>Inspekcja danych usługi Custom Speech

> [!NOTE]
> Na tej stronie założono, że załączono [przygotowanie danych testowych dla Custom Speech](./how-to-custom-speech-test-and-train.md) i przekazano zestaw danych do inspekcji.

Custom Speech udostępnia narzędzia umożliwiające wizualne badanie jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania. Za pomocą programu [Speech Studio](https://speech.microsoft.com/customspeech)można odtworzyć przekazana dźwięk i określić, czy podany wynik rozpoznawania jest poprawny. To narzędzie ułatwia sprawdzenie jakości modelu zamiany mowy na tekst linii bazowej firmy Microsoft, sprawdzenie przeszkolonego modelu niestandardowego lub porównanie transkrypcji przez dwa modele.

W tym dokumencie dowiesz się, jak wizualnie zbadać jakość linii bazowej tekstu i/lub modeli niestandardowych, które zostały przeszkolone. Dowiesz się również, jak za pomocą edytora transkrypcji online tworzyć i udoskonalać zestawy danych audio z etykietami.

## <a name="create-a-test"></a>Tworzenie testu

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć test:

1. Zaloguj się do programu [Speech Studio](https://speech.microsoft.com/customspeech).
2. Przejdź do **> zamiany mowy na tekst Custom Speech > [nazwa projektu] > testowanie**.
3. Kliknij przycisk **Dodaj test**.
4. Wybierz pozycję **Inspekcja jakości (dane audio)**. Nadaj testowi nazwę, opis i wybierz zestaw danych audio.
5. Wybierz maksymalnie dwa modele, które chcesz przetestować.
6. Kliknij pozycję **Utwórz**.

Po pomyślnym utworzeniu testu można zobaczyć, jak model przekształca określony zestaw danych audio, lub Porównaj wyniki z dwóch modeli obok siebie.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Porównania modeli obok siebie

Gdy test stanu zakończy _się pomyślnie_, kliknij w nazwie elementu testowego, aby zobaczyć szczegóły testu. Ta strona szczegółów zawiera listę wszystkich wyrażenia długości w zestawie danych i pokazuje wyniki rozpoznawania dwóch porównywanych modeli.

Aby pomóc w sprawdzeniu porównania obok siebie, można przełączać różne typy błędów, w tym Wstawianie, usuwanie i podstawianie. Nasłuchiwanie dźwięku i porównywanie wyników rozpoznawania w każdej kolumnie (pokazującej transkrypcję i wyniki dwóch modeli zamiany mowy na tekst) można zdecydować, który model spełnia Twoje potrzeby i gdzie są potrzebne ulepszenia.

Testowanie modelu side-by-Side jest przydatne do sprawdzania, który model rozpoznawania mowy jest najlepszy dla aplikacji. Aby określić obiektywną miarę dokładności wymagającą uzyskanego audio, postępuj zgodnie z instrukcjami znajdującymi się w [ocenie dokładności](how-to-custom-speech-evaluate-data.md).

## <a name="online-transcription-editor"></a>Edytor transkrypcji online

Edytor transkrypcji online pozwala łatwo korzystać z transkrypcji audio w Custom Speech. Główne przypadki użycia edytora są następujące: 

* Masz tylko dane audio, ale chcesz utworzyć dokładne zestawy danych audio + z oznaczeniem od nowa, aby użyć ich w szkoleniu modeli.
* Masz już zestawy danych audio + z etykietami ludzkimi, ale występują błędy lub wady w transkrypcji. Edytor umożliwia szybkie modyfikowanie transkrypcji w celu uzyskania najlepszej dokładności szkolenia.

Jedynym wymaganiem do korzystania z edytora transkrypcji jest przekazanie danych audio (tylko audio lub transkrypcja).

### <a name="import-datasets-to-editor"></a>Importuj zestawy danych do edytora

Aby zaimportować dane do edytora, najpierw przejdź do **Custom Speech > [Twój projekt] > Edytor**.

![Karta edytora](media/custom-speech/custom-speech-editor-detail.png)

Następnie wykonaj następujące kroki, aby zaimportować dane.

1. Kliknij pozycję **Importuj dane**
1. Utwórz nowe zestawy danych i nadaj im opis
1. Wybierz pozycję zestawy danych. Obsługiwane jest wiele zaznaczeń i można wybrać tylko dane audio, a także dane audio + z etykietami personalnymi.
1. W przypadku danych tylko audio można opcjonalnie użyć domyślnych modeli, aby automatycznie generować transkrypcję maszyn po zaimportowaniu do edytora
1. Kliknij pozycję **Importuj**

Po pomyślnym zaimportowaniu danych możesz kliknąć w zestawach i rozpocząć edycję.

> [!TIP]
> Możesz również zaimportować zestawy danych do edytora bezpośrednio, wybierając pozycję zestawy danych i klikając pozycję **Eksportuj do edytora** .

### <a name="edit-transcription-by-listening-to-audio"></a>Edytuj transkrypcję, nasłuchiwanie na dźwięk

Po pomyślnym przekazaniu danych kliknij każdą nazwę elementu, aby wyświetlić szczegóły danych. Można również użyć **poprzednich** i **następnych** do przechodzenia między każdym plikiem.

Na stronie szczegółów są wyświetlane wszystkie segmenty w każdym pliku audio i można kliknąć w żądanym wypowiedź. Dla każdego wypowiedźu można odtworzyć dźwięk i sprawdzić transkrypcje oraz edytować transkrypcje, jeśli znajdziesz jakiekolwiek błędy wstawiania, usuwania lub podstawiania. Zapoznaj się z informacjami na [temat oceny danych](how-to-custom-speech-evaluate-data.md) , aby uzyskać więcej szczegółów na temat typów błędów.

![Strona edytora](media/custom-speech/custom-speech-editor.png)

Po dokonaniu edycji kliknij przycisk **Zapisz** .

### <a name="export-datasets-from-the-editor"></a>Eksportowanie zestawów danych z edytora

Aby wyeksportować zestawy danych z powrotem do karty **dane** , przejdź do strony Szczegóły danych, a następnie kliknij przycisk **Eksportuj** , aby wyeksportować wszystkie pliki jako nowy zestaw danych. Można również filtrować pliki według czasu ostatniego edytowania, czasów trwania audio itp., aby częściowo wybierać żądane pliki. 

![Eksportowanie danych](media/custom-speech/custom-speech-editor-export.png)

Pliki eksportowane do danych będą używane jako zestaw danych marki i nie wpłyną na żadne istniejące dane/szkolenia/jednostki testowe.

## <a name="next-steps"></a>Następne kroki

- [Oceń dane](how-to-custom-speech-evaluate-data.md)
- [Szkolenie modelu](how-to-custom-speech-train-model.md)
- [Ulepszanie modelu](./how-to-custom-speech-evaluate-data.md)
- [Wdrażanie modelu](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Przygotuj dane testowe dla Custom Speech](./how-to-custom-speech-test-and-train.md)