---
title: Uczenie i wdrażanie Custom Speech model-Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak szkolić i wdrażać modele Custom Speech. Uczenie modelu zamiany mowy na tekst może poprawić dokładność rozpoznawania dla modelu linii bazowej firmy Microsoft lub modelu niestandardowego.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 166fb94f5a3aea505c0d20df861b8bc4de9ad8aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491116"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Trenowanie i wdrażanie modelu usługi Custom Speech

W tym artykule dowiesz się, jak uczenie i wdrażanie modeli Custom Speech. Uczenie modelu zamiany mowy na tekst może poprawić dokładność rozpoznawania dla modelu linii bazowej firmy Microsoft. Do uczenia modelu stosowane są transkrypcje i powiązane z nimi teksty. Te zestawy danych, wraz z wcześniej przekazanymi danymi audio, są używane do uściślania i uczenia modelu zamiany mowy na tekst.

## <a name="use-training-to-resolve-accuracy-problems"></a>Rozwiązywanie problemów z dokładnością przy użyciu szkoleń

Jeśli napotykasz problemy z rozpoznawaniem z modelem podstawowym, możesz użyć transkrypcji z etykietami ludzkimi i powiązanych danych do uczenia modelu niestandardowego i poprawić dokładność. Użyj tej tabeli, aby określić zestaw danych, który ma być używany do rozwiązywania problemów:

| Przypadek użycia | Typ danych |
| -------- | --------- |
| Poprawianie dokładności rozpoznawania w przypadku słownictwa i gramatyki właściwych dla branży, takich jak Terminologia medyczna lub żargon IT | Pokrewny tekst (zdania/wyrażenia długości) |
| Zdefiniuj tekst fonetyczny i wyświetlany w postaci wyrazu lub terminu, który ma niestandardową wymowę, np. nazwy produktów lub akronimy | Pokrewny tekst (wymowa) |
| Poprawianie dokładności rozpoznawania w przypadku stylów, akcentów lub określonych szumów tła | Zapisy audio + oznakowane przez człowieka |

## <a name="train-and-evaluate-a-model"></a>Trenowanie i ocenianie modelu

Pierwszym krokiem do uczenia modelu jest przekazanie danych szkoleniowych. Zobacz [przygotowanie i przetestowanie danych](./how-to-custom-speech-test-and-train.md) , aby uzyskać instrukcje krok po kroku dotyczące przygotowywania transkrypcji z etykietami i powiązanego tekstu (wyrażenia długości i wymowy). Po przekazaniu danych szkoleniowych postępuj zgodnie z poniższymi instrukcjami, aby rozpocząć uczenie modelu:

1. Zaloguj się do [portalu Custom Speech](https://speech.microsoft.com/customspeech). Jeśli planujesz uczenie modelu z zestawami danych w formacie audio + z etykietami ludzkimi, wybierz subskrypcję mowy w [regionie z dedykowanym sprzętem](custom-speech-overview.md#set-up-your-azure-account) do szkoleń.
2. Przejdź do pozycji **Zamiana mowy na tekst**  >  **Custom Speech**  >  **[nazwa projektu]**  >  **szkolenie**.
3. Wybierz pozycję **Testuj model**.
4. Nadaj swojemu szkoleniowi **nazwę** i **Opis**.
5. Z listy **model scenariusza i linia bazowa** Wybierz scenariusz, który najlepiej pasuje do domeny. Jeśli nie masz pewności, który scenariusz wybrać, wybierz pozycję **Ogólne**. Model linii bazowej jest punktem początkowym szkolenia. Najnowszym modelem jest zazwyczaj najlepszy wybór.
6. Na stronie **Wybierz dane szkoleniowe** wybierz jeden lub więcej powiązanych zestawów danych tekstowych lub zestawy danych do transkrypcji audio + z etykietami, które mają być używane do szkoleń.

> [!NOTE]
> Podczas uczenia nowego modelu Zacznij od pokrewnego tekstu; szkolenie z użyciem zapisu z dźwiękiem i ludzkich etykiet może trwać znacznie dłużej **(do [kilku dni](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)**).

> [!NOTE]
> Nie wszystkie modele podstawowe obsługują szkolenia z dźwiękiem. Jeśli model podstawowy nie obsługuje tego elementu, usługa mowy będzie używać tylko tekstu z transkrypcji i ignorować dźwięk. Zobacz [Obsługa języka](language-support.md#speech-to-text) , aby zapoznać się z listą modeli podstawowych, które obsługują szkolenia z danymi audio.

> [!NOTE]
> W przypadkach, gdy zmieniasz model podstawowy używany do szkolenia i masz dźwięk w zestawie danych szkoleniowych, *zawsze* sprawdzaj, czy nowy wybrany model podstawowy [obsługuje szkolenia z danymi audio](language-support.md#speech-to-text). Jeśli wcześniej użyty model podstawowy nie obsługiwał szkolenia z danymi audio, a zestaw danych szkoleniowych zawiera dźwięk, czas uczenia z nowym modelem podstawowym zostanie **znacząco** zwiększony i może być łatwo przeszedł z kilku godzin do kilku dni i więcej. Jest to szczególnie prawdziwe, jeśli subskrypcja usługi mowy **nie** znajduje się w [regionie z dedykowanym sprzętem](custom-speech-overview.md#set-up-your-azure-account) do szkoleń.
>
> Jeśli problem opisany w powyższym akapicie, możesz szybko skrócić czas uczenia, zmniejszając ilość dźwięku w zestawie danych lub usuwając ją całkowicie i pozostawiając tylko tekst. Ta ostatnia opcja jest zdecydowanie zalecana, jeśli subskrypcja usługi mowy **nie** znajduje się w [regionie z dedykowanym sprzętem](custom-speech-overview.md#set-up-your-azure-account) do szkoleń.

7. Po zakończeniu szkolenia można przeprowadzić testy dokładności dla nowo przeszkolonego modelu. Ta czynność jest opcjonalna.
8. Wybierz pozycję **Utwórz** , aby skompilować niestandardowy model.

W tabeli **szkoleń** zostanie wyświetlony nowy wpis odpowiadający nowemu modelowi. W tabeli jest również wyświetlany stan: **Przetwarzanie**, **powodzenie** lub **Niepowodzenie**.

Zobacz, [jak](how-to-custom-speech-evaluate-data.md) oceniać i usprawnić Custom Speech dokładność modelu. Jeśli zdecydujesz się na przetestowanie dokładności, ważne jest, aby wybrać akustyczny zestaw danych, który jest inny niż ten, który był używany z modelem, aby uzyskać realistyczny sens wydajności modelu.

> [!NOTE]
> Modele podstawowe i modele niestandardowe mogą być używane tylko do określonej daty (zobacz [cykl życia modelu i punktu końcowego](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). Program Speech Studio Wyświetla tę datę w kolumnie **wygaśnięcia** dla każdego modelu i punktu końcowego. Po upływie tego żądania do punktu końcowego lub transkrypcji wsadowej może się nie powieść lub powróci do modelu podstawowego.
>
> Ponownie nauczenie modelu przy użyciu najnowszego modelu podstawowego, aby skorzystać z ulepszeń dokładności i uniknąć tego, że Twój model wygaśnie.

## <a name="deploy-a-custom-model"></a>Wdrażanie modelu niestandardowego

Po przekazaniu i sprawdzeniu danych, ocenie dokładności i uczeniu modelu niestandardowego można wdrożyć niestandardowy punkt końcowy do użycia z aplikacjami, narzędziami i produktami. 

Aby utworzyć niestandardowy punkt końcowy, zaloguj się do [portalu Custom Speech](https://speech.microsoft.com/customspeech). Wybierz pozycję **wdrożenie** w menu **Custom Speech** w górnej części strony. Jeśli jest to pierwsze uruchomienie, Zauważ, że w tabeli nie ma punktów końcowych. Po utworzeniu punktu końcowego należy użyć tej strony do śledzenia wszystkich wdrożonych punktów końcowych.

Następnie wybierz pozycję **Dodaj punkt końcowy** i wprowadź **nazwę** i **Opis** niestandardowego punktu końcowego. Następnie wybierz model niestandardowy, który ma zostać skojarzony z punktem końcowym.  Możesz również włączyć rejestrowanie na tej stronie. Rejestrowanie pozwala monitorować ruch punktu końcowego. Jeśli rejestrowanie jest wyłączone, ruch nie zostanie zapisany.

![Zrzut ekranu przedstawiający stronę nowy punkt końcowy.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nie zapomnij zaakceptować warunków użytkowania i szczegółów cennika.

Następnie wybierz pozycję **Utwórz**. Ta akcja powoduje powrót do strony **wdrożenia** . Tabela zawiera teraz wpis odpowiadający niestandardowemu punktowi końcowemu. Stan punktu końcowego pokazuje jego bieżący stan. Utworzenie wystąpienia nowego punktu końcowego przy użyciu modeli niestandardowych może potrwać do 30 minut. Gdy stan wdrożenia zmieni się na **ukończone**, punkt końcowy jest gotowy do użycia.

Po wdrożeniu punktu końcowego nazwa punktu końcowego jest wyświetlana jako link. Wybierz link, aby wyświetlić informacje specyficzne dla danego punktu końcowego, takie jak klucz punktu końcowego, adres URL punktów końcowych i przykładowy kod. Zanotuj datę wygaśnięcia i zaktualizuj model punktu końcowego przed tą datą, aby zapewnić nieprzerwane działanie usługi.

## <a name="view-logging-data"></a>Wyświetlanie danych rejestrowania

Dane rejestrowania są dostępne do eksportowania, jeśli przejdziesz do strony punktu końcowego w obszarze **wdrożenia**.
> [!NOTE]
>Dane rejestrowania są dostępne przez 30 dni w magazynie należącym do firmy Microsoft. Zostanie on później usunięty. Jeśli konto magazynu należące do klienta jest połączone z subskrypcją Cognitive Services, dane rejestrowania nie zostaną automatycznie usunięte.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak korzystać z niestandardowego modelu](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Przygotowywanie i testowanie danych](./how-to-custom-speech-test-and-train.md)
- [Inspekcja danych](how-to-custom-speech-inspect-data.md)
- [Oceń dane](how-to-custom-speech-evaluate-data.md)
