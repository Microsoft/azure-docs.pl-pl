---
title: Projekt aplikacji iteracyjnej — LUIS
titleSuffix: Azure Cognitive Services
description: LUIS uczy się najlepiej w iteracyjnym cyklu zmian modelu, wypowiedź przykładów, publikowaniu i zbieraniu danych z zapytań punktów końcowych.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: cc994875e39410ecf01417c4d08248d5bc7d034a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589826"
---
# <a name="iterative-app-design-for-luis"></a>Projekt aplikacji iteracyjnej dla LUIS

Aplikacja Language Understanding (LUIS) uczy się i wydajniej wykonuje iteracje. Oto typowy cykl iteracji:

* Utwórz nową wersję
* Edytuj schemat aplikacji LUIS. Obejmuje to:
    * Intencje z przykładem wyrażenia długości
    * Jednostki
    * Funkcje
* Uczenie, testowanie i publikowanie
    * Testowanie w punkcie końcowym przewidywania dla aktywnego uczenia
* Zbieranie danych z zapytań dotyczących punktów końcowych

![Cykl tworzenia](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Kompilowanie schematu LUIS

Schemat aplikacji definiuje, do czego służy użytkownik ( _intencja_ lub _cel_ ) i jakie części zamiaru zawierają szczegóły (nazywane _jednostkami_), które są używane do określenia odpowiedzi.

Schemat aplikacji musi być specyficzny dla domen aplikacji, aby określić słowa i frazy, które są istotne, a także określić typowe porządkowanie wyrazów.

Przykład wyrażenia długości przedstawia dane wejściowe użytkownika, na przykład rozpoznaną mowę lub tekst, że aplikacja oczekuje w czasie wykonywania.

Schemat wymaga intencji i _powinny mieć_ jednostki.

### <a name="example-schema-of-intents"></a>Przykładowy schemat intencji

Najbardziej typowym schematem jest schemat intencji zorganizowany z intencjami. Ten typ schematu używa LUIS do określenia zamiaru użytkownika.

Typ schematu intencji może mieć jednostki, jeśli ułatwia LUIS określenie zamiaru użytkownika. Na przykład jednostka wysyłkowa (jako _Funkcja uczenia maszynowego_ do zamiaru) pomaga Luis ustalić zamiar dostawy.

### <a name="example-schema-of-entities"></a>Przykładowy schemat jednostek

Schemat jednostki koncentruje się na jednostkach, czyli danych wyodrębnionych z wyrażenia długości użytkownika. Na przykład, jeśli użytkownik miał powiedzieć, "Chcę zamówić trzy pizzami". Istnieją dwie jednostki, które zostaną wyodrębnione: _trzy_ i _pizzami_. Są one używane do zaspokojenia zamiaru, który miał być realizowany jako zamówienie.

W przypadku schematu jednostki zamiara wypowiedź jest mniej ważna dla aplikacji klienckiej.

Wspólna Metoda organizowania schematu jednostki polega na dodaniu wszystkich przykładów wyrażenia długości do zamiaru **Brak** .

### <a name="example-of-a-mixed-schema"></a>Przykład schematu mieszanego

Najbardziej zaawansowanym i dojrzałym schematem jest schemat konwersji z pełnym zakresem jednostek i funkcji. Schemat ten może zaczynać się od schematu zamiaru lub jednostki i zwiększać się w celu uwzględnienia koncepcji obu tych elementów, ponieważ aplikacja kliencka wymaga tych informacji.

## <a name="add-example-utterances-to-intents"></a>Dodawanie przykładu wyrażenia długości do intencji

LUIS potrzebuje kilku przykładowych wyrażenia długości w każdym **zamierzeniu**. Przykład wyrażenia długości potrzebuje wystarczającej wariacji wyboru wyrazu i kolejności wyrazów, aby można było określić, której opcji ma dotyczyć wypowiedź.

> [!CAUTION]
> Nie dodawaj przykładu wyrażenia długości zbiorczo. Zacznij od od 15 do 30 konkretnych i różnych przykładów.

Każdy przykład wypowiedź musi mieć **wymagane dane do wyodrębnienia** zaprojektowana i oznaczona przy użyciu **jednostek**.

|Element klucza|Przeznaczenie|
|--|--|
|Intencja|**Klasyfikowanie** wyrażenia długości użytkowników w ramach jednego zamiaru lub akcji. Przykłady obejmują `BookFlight` i `GetWeather` .|
|Jednostka|**Wyodrębnij** dane z wypowiedź wymagane do ukończenia zamiaru. Przykłady obejmują datę i godzinę podróży oraz lokalizację.|

Aplikacja LUIS może być zaprojektowana w celu ignorowania wyrażenia długości, które nie są istotne dla domeny aplikacji przez przypisanie wypowiedź do zamiaru **Brak** .

## <a name="test-and-train-your-app"></a>Testowanie i uczenie aplikacji

Po otrzymaniu od 15 do 30 różnych przykładowych wyrażenia długości w każdym zamiarze z wymaganymi jednostkami z etykietą należy przetestować i [szkolić](luis-how-to-train.md) aplikację Luis.

## <a name="publish-to-a-prediction-endpoint"></a>Publikowanie w punkcie końcowym przewidywania

Aplikacja LUIS musi być opublikowana, aby była dostępna dla Ciebie w [regionach punktu końcowego przewidywania](luis-reference-regions.md)listy.

## <a name="test-your-published-app"></a>Testowanie opublikowanej aplikacji

Opublikowaną aplikację LUIS można przetestować za pomocą punktu końcowego przewidywania protokołu HTTPS. Testowanie z punktu końcowego przewidywania umożliwia LUIS wybranie dowolnego wyrażenia długości z niską pewnością do [przeglądu](luis-how-to-review-endpoint-utterances.md).

## <a name="create-a-new-version-for-each-cycle"></a>Utwórz nową wersję dla każdego cyklu

Każda wersja jest migawką w czasie aplikacji LUIS. Przed wprowadzeniem zmian w aplikacji Utwórz nową wersję. Łatwiej jest wrócić do starszej wersji niż próbować usunąć intencje i wyrażenia długości do poprzedniego stanu.

Identyfikator wersji składa się z znaków, cyfr lub "." i nie może być dłuższa niż 10 znaków.

Wersja początkowa (0,1) jest domyślną wersją aktywną.

### <a name="begin-by-cloning-an-existing-version"></a>Zacznij od klonowania istniejącej wersji

Sklonuj istniejącą wersję do użycia jako punkt wyjścia dla każdej nowej wersji. Po sklonowaniu wersji Nowa wersja zostanie **uaktywniona** .

### <a name="publishing-slots"></a>Publikowanie miejsc

Możesz publikować na etapie i/lub w gniazdach produkcyjnych. Każde gniazdo może mieć inną wersję lub tę samą wersję. Jest to przydatne w przypadku sprawdzania zmian przed opublikowaniem w środowisku produkcyjnym, które jest dostępne dla botów lub innych aplikacji wywołujących LUIS.

Przeszkolone wersje nie są automatycznie dostępne w [punkcie końcowym](luis-glossary.md#endpoint)aplikacji Luis. Musisz [opublikować](luis-how-to-publish-app.md) lub ponownie opublikować wersję, aby była dostępna w punkcie końcowym aplikacji Luis. Możesz publikować w ramach **przemieszczania** i **produkcji**, udostępniając dwie wersje aplikacji dostępnych w punkcie końcowym. Jeśli więcej wersji aplikacji musi być dostępnych w punkcie końcowym, należy wyeksportować wersję i ponownie zaimportować ją do nowej aplikacji. Nowa aplikacja ma inny identyfikator aplikacji.

### <a name="import-and-export-a-version"></a>Importowanie i eksportowanie wersji

Wersję można zaimportować na poziomie aplikacji. Ta wersja jest wersją aktywną i używa identyfikatora wersji we `versionId` właściwości pliku aplikacji. Możesz również zaimportować do istniejącej aplikacji na poziomie wersji. Nowa wersja zostanie uaktywniona.

Wersję można wyeksportować również na poziomie aplikacji lub wersji. Jedyną różnicą jest to, że wersja wyeksportowana na poziomie aplikacji jest obecnie aktywna, a na poziomie wersji można wybrać dowolną wersję do eksportowania na stronie **[Ustawienia](luis-how-to-manage-versions.md)** .

Wyeksportowany plik **nie** zawiera:

* Informacje o maszynach, ponieważ aplikacja jest ponownie przeszkolna po zaimportowaniu
* Informacje o współautorze

Aby utworzyć kopię zapasową schematu aplikacji LUIS, wyeksportuj wersję z [portalu Luis](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Zarządzanie zmianami współautora przy użyciu wersji i współautorów

LUIS używa koncepcji współautorów do aplikacji, dostarczając uprawnienia na poziomie zasobów platformy Azure. Połącz tę koncepcję z wersją, aby zapewnić skierowaną do współpracy.

Użyj następujących technik, aby zarządzać zmianami współautora w aplikacji.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Zarządzanie wieloma wersjami w ramach tej samej aplikacji

Zacznij od [klonowania](luis-how-to-manage-versions.md#clone-a-version) z wersji podstawowej dla każdego autora.

Każdy autor wprowadza zmiany w swojej wersji aplikacji. Gdy autor jest zadowolony z modelu, wyeksportuj nowe wersje do plików JSON.

Wyeksportowane aplikacje (pliki JSON lub Lu) można porównać ze zmianami. Połącz pliki, aby utworzyć jeden plik nowej wersji. Zmień właściwość tak, `versionId` Aby oznacza nową scaloną wersję. Zaimportuj tę wersję do oryginalnej aplikacji.

Ta metoda umożliwia posiadanie jednej aktywnej wersji, jednej wersji etapu i jednej opublikowanej wersji. Wyniki aktywnej wersji można porównać z opublikowaną wersją (etap lub produkcja) w [okienku testowanie interaktywne](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Zarządzanie wieloma wersjami jako aplikacjami

[Wyeksportuj](luis-how-to-manage-versions.md#export-version) wersję bazową. Każdy autor importuje wersję. Osoba, która importuje aplikację, jest właścicielem wersji. Po zakończeniu modyfikowania aplikacji wyeksportuj wersję.

Wyeksportowane aplikacje to pliki w formacie JSON, które można porównać z eksportem podstawowym dla zmian. Połącz pliki, aby utworzyć pojedynczy plik JSON nowej wersji. Zmień właściwość **versionId** w kodzie JSON, aby wyznaczać nową scaloną wersję. Zaimportuj tę wersję do oryginalnej aplikacji.

Dowiedz się więcej o tworzeniu udziałów od [współpracowników](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Zapoznaj się z punktem końcowym wyrażenia długości, aby rozpocząć nowy cykl iteracyjny

Po zakończeniu cyklu iteracji można powtórzyć ten proces. Zacznij od [przejrzenia punktu końcowego przewidywania wyrażenia długości](luis-how-to-review-endpoint-utterances.md) Luis oznaczonego niską pewnością. Sprawdź te wyrażenia długości pod kątem prawidłowych przewidzianych zamierzeń i poprawnych i kompletnych wyodrębnionych jednostek. Po przejrzeniu i zaakceptowaniu zmian lista przeglądów powinna być pusta.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pojęciami dotyczącymi [współpracy](luis-concept-keys.md).
