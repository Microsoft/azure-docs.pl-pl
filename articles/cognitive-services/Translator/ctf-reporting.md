---
title: Raportowanie współpracy między platformą COLLABORATIVE) — translator
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b0de40940fa75dd4eb4e1572405f31ce1c22b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934382"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak korzystać z raportowania na platformie CTF (Collaborative Translation Framework)

> [!NOTE]
> Ta metoda jest przestarzała. Nie jest dostępny w wersji 3.0 usługi Translator.
> 
> Platforma tłumaczenia do współpracy (COLLABORATIVE), wcześniej dostępna dla usługi Translator w wersji 2.0, była przestarzała od 1 lutego 2018. Funkcja addtranslation i AddTranslationArray umożliwiają użytkownikom włączanie poprawek za pośrednictwem platformy tłumaczenia współpracy. Po 31 stycznia 2018 te dwie funkcje nie akceptują nowych przesłanych zdań i użytkownicy otrzymają komunikat o błędzie. Te funkcje zostały wycofane i nie zostaną zastąpione.

Interfejs API raportowania usługi współpracy między środowiskami (COLLABORATIVE) zwraca statystyki i rzeczywistą zawartość w magazynie COLLABORATIVE. Ten interfejs API różni się od metody gettranslations (), ponieważ:
* Zwraca przetłumaczoną zawartość i jej łączną liczbę tylko z Twojego konta (Identyfikator aplikacji lub konto portalu Azure Marketplace).
* Zwraca przetłumaczoną zawartość i jej łączną liczbę bez konieczności dopasowania zdania źródłowego.
* Nie zwraca automatycznego tłumaczenia (tłumaczenie maszynowe).

## <a name="endpoint"></a>Punkt końcowy
Punkt końcowy interfejsu API raportowania COLLABORATIVE to https://api.microsofttranslator.com/v2/beta/ctfreporting.svc .

## <a name="methods"></a>Metody
| Nazwa | Opis|
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

**Składnia**

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
| na wartość | **Opcjonalne** Ciąg reprezentujący kod języka służący do tłumaczenia tekstu.|
| minRating| **Opcjonalne** Wartość całkowita reprezentująca minimalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalne** Wartość całkowita reprezentująca maksymalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| użytkownik | **Opcjonalne** Ciąg, który służy do filtrowania wyniku na podstawie nadawcy przesłania. |
| category| **Opcjonalne** Ciąg zawierający kategorię lub domenę tłumaczenia. Ten parametr obsługuje tylko domyślną opcję Ogólne.|
| minDateUtc| **Opcjonalne** Data, z której ma zostać pobrane tłumaczenie. Data musi być w formacie UTC. |
| maxDateUtc| **Opcjonalne** Data do pobrania tłumaczeń. Data musi być w formacie UTC. |
| Pomiń| **Opcjonalne** Liczba wyników, które mają zostać pominięte na stronie. Na przykład, jeśli chcesz pominąć pierwsze 20 wierszy wyników i widok z 21 rekordu wynikowego, należy określić 20 dla tego parametru. Wartość domyślna tego parametru to 0.|
| take (pobierz) | **Opcjonalne** Liczba wyników, które mają zostać pobrane. Maksymalna liczba żądań wynosi 100. Wartość domyślna to 100.|

> [!NOTE]
> Parametry Skip i Take Request umożliwiają stronicowanie w przypadku dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslationCount**. Każdy UserTranslationCount ma następujące elementy:

| Pole | Opis |
|:---|:---|
| Liczba| Liczba pobranych wyników|
| Źródło | Język źródłowy|
| Klasyfikacja| Klasyfikacja stosowana przez wysyłającego w wywołaniu metody addtranslation ()|
| Działanie| Język docelowy|
| Adresu| Identyfikator URI zastosowany w wywołaniu metody addtranslation ()|
| Użytkownik| Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Wiadomość | Warunki |
|:---|:---|:---|
| Wyjątku ArgumentOutOfRangeException | Parametr "**maxDateUtc**" musi być większy lub równy "**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP przekracza limit przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania pozostaje ograniczony do 10000 znaków.</li><li>Co godzinę i dzienny limit przydziału ogranicza liczbę znaków akceptowanych przez translatora.</li></ul>|
| TranslateApiException | Identyfikator AppId przekracza limit przydziału.| Identyfikator aplikacji został przekroczony co godzinę lub dzienny limit przydziału.|

> [!NOTE]
> Przydział zostanie dostosowany w celu zapewnienia sprawiedliwości wśród wszystkich użytkowników usługi.

**Wyświetl przykłady kodu w witrynie GitHub**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)

## <a name="getusertranslations-method"></a>GetUserTranslations, Metoda

Ta metoda pobiera tłumaczenia, które są tworzone przez użytkownika. Udostępnia tłumaczenia pogrupowane według parametrów żądania uriPrefix, from, to, User i minRating i maxRating.

**Składnia**

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
| na wartość| **Opcjonalne** Ciąg reprezentujący kod języka służący do tłumaczenia tekstu.|
| minRating| **Opcjonalne** Wartość całkowita reprezentująca minimalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| maxRating| **Opcjonalne** Wartość całkowita reprezentująca maksymalną ocenę jakości przetłumaczonego tekstu. Prawidłowa wartość jest z przedziału od-10 do 10. Wartość domyślna to 1.|
| użytkownik| **Obowiązkowe. Ciąg, który jest używany do filtrowania wyniku na podstawie nadawcy przesłania**|
| category| **Opcjonalne** Ciąg zawierający kategorię lub domenę tłumaczenia. Ten parametr obsługuje tylko domyślną opcję Ogólne.|
| minDateUtc| **Opcjonalne** Data, z której ma zostać pobrane tłumaczenie. Data musi być w formacie UTC.|
| maxDateUtc| **Opcjonalne** Data do pobrania tłumaczeń. Data musi być w formacie UTC.|
| Pomiń| **Opcjonalne** Liczba wyników, które mają zostać pominięte na stronie. Na przykład, jeśli chcesz pominąć pierwsze 20 wierszy wyników i widok z 21 rekordu wynikowego, należy określić 20 dla tego parametru. Wartość domyślna tego parametru to 0.|
| take (pobierz)| **Opcjonalne** Liczba wyników, które mają zostać pobrane. Maksymalna liczba żądań wynosi 100. Domyślna wartość wynosi 50.|

> [!NOTE]
> Parametry Skip i Take Request umożliwiają stronicowanie w przypadku dużej liczby rekordów wyników.

**Wartość zwracana**

Zestaw wyników zawiera tablicę **UserTranslation**. Każdy UserTranslation ma następujące elementy:

| Pole | Opis |
|:---|:---|
| CreatedDateUtc| Data utworzenia wpisu przy użyciu addtranslation ()|
| Źródło| Język źródłowy|
| OriginalText| Tekst języka źródłowego, który jest używany podczas przesyłania żądania.|
|Klasyfikacja |Klasyfikacja stosowana przez wysyłającego w wywołaniu metody addtranslation ()|
|Działanie|    Język docelowy|
|TranslatedText|    Tłumaczenie przesłane w wywołaniu metody addtranslation ()|
|Adresu|   Identyfikator URI zastosowany w wywołaniu metody addtranslation ()|
|Użytkownik   |Nazwa użytkownika|

**Wyjątki**

| Wyjątek | Wiadomość | Warunki |
|:---|:---|:---|
| Wyjątku ArgumentOutOfRangeException | Parametr "**maxDateUtc**" musi być większy lub równy "**minDateUtc**".| Wartość parametru **maxDateUtc** jest mniejsza niż wartość parametru **minDateUtc**.|
| TranslateApiException | Adres IP przekracza limit przydziału.| <ul><li>Osiągnięto limit liczby żądań na minutę.</li><li>Rozmiar żądania pozostaje ograniczony do 10000 znaków.</li><li>Co godzinę i dzienny limit przydziału ogranicza liczbę znaków akceptowanych przez translatora.</li></ul>|
| TranslateApiException | Identyfikator AppId przekracza limit przydziału.| Identyfikator aplikacji został przekroczony co godzinę lub dzienny limit przydziału.|

> [!NOTE]
> Przydział zostanie dostosowany w celu zapewnienia sprawiedliwości wśród wszystkich użytkowników usługi.

**Wyświetl przykłady kodu w witrynie GitHib**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)
