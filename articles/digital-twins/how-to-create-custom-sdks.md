---
title: Tworzenie niestandardowych zestawów SDK języka za pomocą funkcji AutoRest
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak używać funkcji AutoRest do generowania niestandardowych zestawów SDK języka na Azure Digital Twins kodu w innych językach, które nie mają opublikowanych zestawów SDK.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751116"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Tworzenie niestandardowych zestawów SDK języka dla Azure Digital Twins przy użyciu funkcji AutoRest

Jeśli musisz pracować z usługą Azure Digital Twins przy użyciu języka, który nie ma opublikowanego zestawu SDK usługi [Azure Digital Twins,](how-to-use-apis-sdks.md)w tym artykule opisano sposób używania funkcji AutoRest do generowania własnego zestawu SDK w języku, który chcesz wybrać. 

W przykładach w tym artykule przedstawiono tworzenie zestawu [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)płaszczyzny danych, ale ten proces będzie również działać w przypadku generowania zestawu  [SDK płaszczyzny](how-to-use-apis-sdks.md#overview-control-plane-apis) sterowania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wygenerować zestaw SDK, najpierw musisz wykonać następującą konfigurację na komputerze lokalnym:
* Zainstaluj [**program AutoRest**](https://github.com/Azure/autorest)w wersji 2.0.4413 (wersja 3 nie jest obecnie obsługiwana)
* Zainstaluj [**Node.js**](https://nodejs.org), który jest warunkiem wstępnym korzystania z funkcji AutoRest
* Instalowanie [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Pobierz najnowszy plik Azure Digital Twins danych **programu Swagger** (OpenAPI) z [folderu struktury Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)płaszczyzny danych wraz z towarzyszącym folderem przykładów. Plik programu Swagger jest plikiem o nazwie *digitaltwins.jsw systemie*.

>[!TIP]
> Aby zamiast tego utworzyć zestaw **SDK** płaszczyzny sterowania, wykonaj kroki opisane w tym artykule przy użyciu najnowszego pliku **struktury Swagger** (OpenAPI) płaszczyzny sterowania z [folderu swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) płaszczyzny sterowania zamiast z płaszczyzny danych.

Gdy maszyna będzie zawierała wszystkie dane z powyższej listy, możesz użyć funkcji AutoRest do utworzenia zestawu SDK.

## <a name="create-the-sdk-using-autorest"></a>Tworzenie zestawu SDK przy użyciu funkcji AutoRest 

Po zainstalowaniu Node.js możesz uruchomić to polecenie, aby upewnić się, że masz zainstalowaną wymaganą wersję funkcji AutoRest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Aby uruchomić funkcję AutoRest dla Azure Digital Twins programu Swagger, wykonaj następujące kroki:
1. Skopiuj plik Azure Digital Twins Swagger i towarzyszący mu folder z przykładami do katalogu roboczego.
2. Użyj okna wiersza polecenia, aby przełączyć się do tego katalogu roboczego.
3. Uruchom autorest za pomocą następującego polecenia. Zastąp symbol `<language>` zastępczy swoim językiem: `python` , , i tak `java` `go` dalej. (Pełną listę opcji można znaleźć w sekcji [AutoRest README).](https://github.com/Azure/autorest)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

W rezultacie w katalogu roboczy zostanie wyświetlony nowy folder o nazwie *DigitalTwinsApi.* Wygenerowane pliki zestawu SDK będą mieć przestrzeń nazw *DigitalTwinsApi.* Będziesz nadal używać tej przestrzeni nazw w pozostałych przykładach użycia w tym artykule.

Funkcja AutoRest obsługuje szeroką gamę generatorów kodu języka.

## <a name="make-the-sdk-into-a-class-library"></a>Sprawić, aby zestaw SDK był w bibliotece klas

Pliki wygenerowane przez funkcję AutoRest można uwzględnić bezpośrednio w rozwiązaniu .NET. Jednak istnieje prawdopodobieństwo, że zestaw SDK usługi Azure Digital Twins będzie uwzględniany w kilku oddzielnych projektach (aplikacjach klienckich, aplikacjach Azure Functions i innych). Z tego powodu przydatne może być skompilowanie oddzielnego projektu (biblioteki klas .NET) na podstawie wygenerowanych plików. Następnie można uwzględnić ten projekt biblioteki klas w kilku rozwiązaniach jako odwołanie do projektu.

Ta sekcja zawiera instrukcje dotyczące sposobu kompilowania zestawu SDK jako biblioteki klas, która jest własnym projektem i może być uwzględniona w innych projektach. Te kroki opierają się **na Visual Studio**.

Oto konkretne kroki:

1. Tworzenie nowego Visual Studio dla biblioteki klas
2. Użyj *nazwy projektu DigitalTwinsApi*
3. W Eksplorator rozwiązań prawym przyciskiem e-> projekt *DigitalTwinsApi* wygenerowanego rozwiązania i wybierz pozycję Dodaj > *istniejący element...*
4. Znajdź folder, w którym wygenerowano zestaw SDK, i wybierz pliki na poziomie głównym
5. Naciśnij przycisk "OK"
6. Dodaj folder do projektu (wybierz prawym przyciskiem pozycję projektu w Eksplorator rozwiązań, a następnie wybierz > *Nowy folder*)
7. Nadaj folderowi *nazwę Models*
8. Wybierz prawym przyciskiem pozycję w *folderze Models* w Eksplorator rozwiązań a następnie wybierz *pozycję Dodaj > istniejący element...*
9. Wybierz pliki w folderze *Models* wygenerowanego zestawu SDK i naciśnij przycisk "OK"

Aby pomyślnie skompilować zestaw SDK, projekt będzie potrzebować tych odwołań:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Aby je dodać, otwórz narzędzie Narzędzia > NuGet Menedżer pakietów > Zarządzaj pakietami *NuGet dla rozwiązania...*.

1. Upewnij się, że na panelu wybrano *kartę* Przeglądaj
2. Wyszukaj *microsoft.rest*
3. Wybierz pakiety `ClientRuntime` i i dodaj je do `ClientRuntime.Azure` rozwiązania

Teraz możesz skompilować projekt i dołączyć go jako odwołanie do projektu w dowolnej Azure Digital Twins aplikacji, która zostanie napisana.

## <a name="tips-for-using-the-sdk"></a>Porady dotyczące korzystania z zestawu SDK

Ta sekcja zawiera ogólne informacje i wskazówki dotyczące korzystania z wygenerowanego zestawu SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Wywołania synchroniczne i asynchroniczne

Wszystkie funkcje zestawu SDK są dostępne w wersjach synchronicznych i asynchronicznych.

### <a name="typed-and-untyped-data"></a>Typowane i nietypowane dane

Wywołania interfejsu API REST zwykle zwracają silnie typowane obiekty. Jednak ponieważ Azure Digital Twins umożliwiają użytkownikom definiowanie własnych typów niestandardowych dla bliźniaczych reprezentacji, nie ma możliwości wstępnego zdefiniowania statycznych danych zwrotnych dla wielu wywołań Azure Digital Twins niestandardowych. Zamiast tego interfejsy API zwracają silnie typy otoki tam, gdzie ma to zastosowanie, a niestandardowe dane bliźniaczej reprezentacji są w obiektach Json.NET (używane wszędzie tam, gdzie typ danych "object" pojawia się w sygnaturach interfejsu API). Te obiekty można rzutować odpowiednio w kodzie.

### <a name="error-handling"></a>Obsługa błędów

Za każdym razem, gdy w zestawie SDK wystąpi błąd (w tym błędy HTTP, takie jak 404), zestaw SDK zda wyjątek. Z tego powodu ważne jest hermetyzowanie wszystkich wywołań interfejsu API w blokach try/catch.

Oto fragment kodu, który próbuje dodać bliźniacze reprezentacji i przechwytuje wszelkie błędy w tym procesie:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Stronicowanie

Funkcja AutoRest generuje dwa typy wzorców stronicowania dla zestawu SDK:
* Jeden dla wszystkich interfejsów API z wyjątkiem interfejsu API zapytań
* Jeden dla interfejsu API zapytań

W wzorcu stronicowania bez zapytania poniżej znajduje się przykładowa metoda, która pokazuje, jak pobrać stronicowane listy relacji wychodzących z Azure Digital Twins:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Drugi wzorzec jest generowany tylko dla interfejsu API zapytań. Używa `continuationToken` jawnie.

>[!TIP]
> Głównym powodem uzyskiwania stron jest obliczanie opłat za [jednostkę zapytania dla](concepts-query-units.md) wywołania interfejsu API zapytań.

Oto przykład z tym wzorcem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Następne kroki

Opis kroków tworzenia aplikacji klienckiej, w której można używać zestawu SDK:
* [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)
