---
title: Raportowanie współdziałające z platformą Translation (COLLABORATIVE) — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak korzystać z funkcji raportowania w programie COLLABORATIVE (współpracownik tłumaczenia).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595942"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak korzystać z raportowania na platformie CTF (Collaborative Translation Framework)

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępna w wersji 3.0 interfejs API tłumaczenia tekstu w usłudze Translator.
> 
> Platforma tłumaczenia do współpracy (COLLABORATIVE), wcześniej dostępna dla wersji V 2.0 interfejs API tłumaczenia tekstu w usłudze Translator, była przestarzała od 1 lutego 2018. Funkcja addtranslation i AddTranslationArray umożliwiają użytkownikom włączanie poprawek za pośrednictwem platformy tłumaczenia współpracy. Po 31 stycznia 2018 te dwie funkcje nie akceptują nowych przesłanych zdań i użytkownicy otrzymają komunikat o błędzie. Te funkcje zostały wycofane i nie zostaną zastąpione.

Interfejs API raportowania usługi współpracy między środowiskami (COLLABORATIVE) zwraca statystyki i rzeczywistą zawartość w magazynie COLLABORATIVE. Ten interfejs API różni się od metody gettranslations (), ponieważ:
* Zwraca przetłumaczoną zawartość i jej łączną liczbę tylko z Twojego konta (Identyfikator aplikacji lub konto portalu Azure Marketplace).
* Zwraca przetłumaczoną zawartość i jej łączną liczbę bez konieczności dopasowania zdania źródłowego.
* Nie zwraca automatycznego tłumaczenia (tłumaczenie maszynowe).

## <a name="endpoint"></a>Endpoint
Punkt końcowy interfejsu API raportowania COLLABORATIVE tohttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metody
| Nazwa |    Opis|
|:---|:---|
| GetUserTranslationCounts, Metoda | Pobiera liczbę tłumaczeń, które są tworzone przez użytkownika. |
| GetUserTranslations, Metoda | Pobiera tłumaczenia, które są tworzone przez użytkownika. |

Te metody umożliwiają:
* Pobierz pełny zbiór tłumaczeń i korekt użytkowników w ramach identyfikatora konta do pobrania.
* Uzyskaj listę częstych współautorów. Upewnij się, że w polu addtranslation () znajduje się poprawna nazwa użytkownika.
* Utwórz interfejs użytkownika, który umożliwia zaufanym użytkownikom wyświetlanie wszystkich dostępnych kandydatów, w razie potrzeby ograniczonych do części witryny, na podstawie prefiksu identyfikatora URI.

> [!NOTE]
> Obie metody są stosunkowo wolne i kosztowne. Zalecane jest korzystanie z nich oszczędnie.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts, Metoda

Ta metoda pobiera liczbę tłumaczeń, które są tworzone przez użytkownika. Zawiera listę liczb tłumaczeń pogrupowanych według parametrów żądania uriPrefix, from, to, User, minRating i maxRating.

**Obowiązuje**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli jest używany nagłówek autoryzacji, pozostaw pole AppID puste, a następnie wpisz ciąg zawierający "Bearer" + "" + token dostępu.|
| uriPrefix | **Opcjonalne** Ciąg zawierający prefiks identyfikatora URI tłumaczenia.|
| wniosek | **Opcjonalne** Ciąg reprezentujący kod języka dla tekstu tłumaczenia. |
| na | **Opcjonalne** Ciąg reprezentujący kod języka służący do tłumaczenia tekstu.|
| minRating| **Opcjonalne** Wartość całkowita reprezentująca minimalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalne** Wartość całkowita reprezentująca maksymalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| użytkownik | **Opcjonalne** Ciąg, który służy do filtrowania wyniku na podstawie nadawcy przesłania. |
| category| **Opcjonalne** Ciąg zawierający kategorię lub domenę tłumaczenia. Ten parametr obsługuje tylko domyślną opcję Ogólne.|
| minDateUtc| **Opcjonalne** Data, z której ma zostać pobrane tłumaczenie. Data musi być w formacie UTC. |
| maxDateUtc| **Opcjonalne** Data do pobrania tłumaczeń. Data musi być w formacie UTC. |
| Pomiń| **Opcjonalne** Liczba wyników, które mają zostać pominięte na stronie. Na przykład, jeśli chcesz pominąć pierwsze 20 wierszy wyników i widok z 21 rekordu wynikowego, należy określić 20 dla tego parametru. Wartość domyślna tego parametru to 0.|
| czasochłonn | **Opcjonalne** Liczba wyników, które mają zostać pobrane. Maksymalna liczba żądań wynosi 100. Wartość domyślna to 100.|

> [!NOTE]
> Parametry Skip i Take Request umożliwiają stronicowanie w przypadku dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslationCount**. Każdy UserTranslationCount ma następujące elementy:

| Pole | Opis |
|:---|:---|
| Liczba| Liczba pobranych wyników|
| Z | Język źródłowy|
| Klasyfikacja| Klasyfikacja stosowana przez wysyłającego w wywołaniu metody addtranslation ()|
| Do| Język docelowy|
| Adresu| Identyfikator URI zastosowany w wywołaniu metody addtranslation ()|
| Użytkownik| Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Wiadomość | Warunki |
|:---|:---|:---|
| Wyjątku ArgumentOutOfRangeException | Parametr "**maxDateUtc**" musi być większy lub równy "**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP przekracza limit przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania pozostaje ograniczony do 10000 znaków.</li><li>Co godzinę i dzienny limit przydziału ogranicza liczbę znaków akceptowanych przez interfejs API usługi Microsoft Translator.</li></ul>|
| TranslateApiException | Identyfikator AppId przekracza limit przydziału.| Identyfikator aplikacji został przekroczony co godzinę lub dzienny limit przydziału.|

> [!NOTE]
> Przydział zostanie dostosowany w celu zapewnienia sprawiedliwości wśród wszystkich użytkowników usługi.

**Wyświetl przykłady kodu w witrynie GitHib**
* [S #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations, Metoda

Ta metoda pobiera tłumaczenia, które są tworzone przez użytkownika. Udostępnia tłumaczenia pogrupowane według parametrów żądania uriPrefix, from, to, User i minRating i maxRating.

**Obowiązuje**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parametry**

| Parametr | Opis |
|:---|:---|
| appId | **Wymagane** Jeśli jest używany nagłówek autoryzacji, pozostaw pole AppID puste, a następnie wpisz ciąg zawierający "Bearer" + "" + token dostępu.|
| uriPrefix| **Opcjonalne** Ciąg zawierający prefiks identyfikatora URI tłumaczenia.|
| wniosek| **Opcjonalne** Ciąg reprezentujący kod języka dla tekstu tłumaczenia.|
| na| **Opcjonalne** Ciąg reprezentujący kod języka służący do tłumaczenia tekstu.|
| minRating| **Opcjonalne** Wartość całkowita reprezentująca minimalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalne** Wartość całkowita reprezentująca maksymalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| użytkownik| **Obowiązkowe. Ciąg, który jest używany do filtrowania wyniku na podstawie nadawcy przesłania**|
| category| **Opcjonalne** Ciąg zawierający kategorię lub domenę tłumaczenia. Ten parametr obsługuje tylko domyślną opcję Ogólne.|
| minDateUtc| **Opcjonalne** Data, z której ma zostać pobrane tłumaczenie. Data musi być w formacie UTC.|
| maxDateUtc| **Opcjonalne** Data do pobrania tłumaczeń. Data musi być w formacie UTC.|
| Pomiń| **Opcjonalne** Liczba wyników, które mają zostać pominięte na stronie. Na przykład, jeśli chcesz pominąć pierwsze 20 wierszy wyników i widok z 21 rekordu wynikowego, należy określić 20 dla tego parametru. Wartość domyślna tego parametru to 0.|
| czasochłonn| **Opcjonalne** Liczba wyników, które mają zostać pobrane. Maksymalna liczba żądań wynosi 100. Wartość domyślna to 50.|

> [!NOTE]
> Parametry Skip i Take Request umożliwiają stronicowanie w przypadku dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslation**. Każdy UserTranslation ma następujące elementy:

| Pole | Opis |
|:---|:---|
| CreatedDateUtc| Data utworzenia wpisu przy użyciu addtranslation ()|
| Z| Język źródłowy|
| OriginalText| Tekst języka źródłowego, który jest używany podczas przesyłania żądania.|
|Klasyfikacja |Klasyfikacja stosowana przez wysyłającego w wywołaniu metody addtranslation ()|
|Do|    Język docelowy|
|TranslatedText|    Tłumaczenie przesłane w wywołaniu metody addtranslation ()|
|Adresu|   Identyfikator URI zastosowany w wywołaniu metody addtranslation ()|
|Użytkownik   |Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Wiadomość | Warunki |
|:---|:---|:---|
| Wyjątku ArgumentOutOfRangeException | Parametr "**maxDateUtc**" musi być większy lub równy "**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP przekracza limit przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania pozostaje ograniczony do 10000 znaków.</li><li>Co godzinę i dzienny limit przydziału ogranicza liczbę znaków akceptowanych przez interfejs API usługi Microsoft Translator.</li></ul>|
| TranslateApiException | Identyfikator AppId przekracza limit przydziału.| Identyfikator aplikacji został przekroczony co godzinę lub dzienny limit przydziału.|

> [!NOTE]
> Przydział zostanie dostosowany w celu zapewnienia sprawiedliwości wśród wszystkich użytkowników usługi.

**Wyświetl przykłady kodu w witrynie GitHib**
* [S #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
