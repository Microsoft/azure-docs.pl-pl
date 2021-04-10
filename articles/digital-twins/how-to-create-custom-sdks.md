---
title: Tworzenie zestawów SDK języka niestandardowego za pomocą AutoRest
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak używać usługi AutoRest do generowania zestawów SDK języka niestandardowego na potrzeby pisania kodu bliźniaczych reprezentacji Digital w innych językach, które nie mają opublikowanych zestawów SDK.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561844"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Tworzenie zestawów SDK języka niestandardowego dla usługi Azure Digital bliźniaczych reprezentacji przy użyciu funkcji AutoRest

Jeśli musisz pracować z usługą Azure Digital bliźniaczych reprezentacji przy użyciu języka, który nie ma [opublikowanego zestawu SDK Digital bliźniaczych reprezentacji platformy Azure](how-to-use-apis-sdks.md), w tym artykule przedstawiono sposób użycia funkcji AutoRest w celu wygenerowania własnego zestawu SDK w wybranym języku. 

W przykładach w tym artykule przedstawiono tworzenie [zestawu SDK płaszczyzny danych](how-to-use-apis-sdks.md#overview-data-plane-apis), ale ten proces będzie działał również w celu WYGENEROWANIA  [zestawu SDK płaszczyzny kontroli](how-to-use-apis-sdks.md#overview-control-plane-apis) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby wygenerować zestaw SDK, należy najpierw wykonać następujące czynności konfiguracyjne na komputerze lokalnym:
* Zainstaluj program [**AutoRest**](https://github.com/Azure/autorest), wersja 2.0.4413 (wersja 3 nie jest obecnie obsługiwana)
* Zainstaluj [**Node.js**](https://nodejs.org), która jest warunkiem wstępnym korzystania z funkcji AutoRest
* Zainstaluj [ **program Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Pobierz najnowszą wersję pliku **struktury Swagger** (openapi) usługi Azure Digital bliźniaczych reprezentacji z [folderu płaszczyzny danych](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Plik struktury Swagger jest tym, *digitaltwins.json*.

>[!TIP]
> Aby zamiast tego utworzyć **zestaw SDK płaszczyzny kontroli** , wykonaj kroki opisane w tym artykule przy użyciu najnowszego pliku z płaszczyzną kontroli **Swagger** (openapi) z poziomu [folderu Swagger płaszczyzny kontroli](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) zamiast z płaszczyzny danych.

Gdy Twoja maszyna jest wyposażona w wszystko z powyższej listy, możesz użyć AutoRest do utworzenia zestawu SDK.

## <a name="create-the-sdk-using-autorest"></a>Tworzenie zestawu SDK przy użyciu funkcji AutoRest 

Po zainstalowaniu Node.js można uruchomić to polecenie, aby upewnić się, że jest zainstalowana wymagana wersja narzędzia AutoRest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Aby uruchomić polecenie AutoRest dla pliku programu Azure Digital bliźniaczych reprezentacji Swagger, wykonaj następujące kroki:
1. Skopiuj plik programu Azure Digital bliźniaczych reprezentacji Swagger i dołączony do niego folder przykładów do katalogu roboczego.
2. Użyj okna wiersza polecenia, aby przełączyć się do tego katalogu roboczego.
3. Uruchom polecenie AutoRest przy użyciu poniższego polecenia. Zastąp `<language>` symbol zastępczy wybranym językiem: `python` , `java` , `go` , i tak dalej. (Pełną listę opcji można znaleźć w [pliku Readme AutoRest](https://github.com/Azure/autorest)).

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

W związku z tym zobaczysz nowy folder o nazwie *DigitalTwinsApi* w katalogu roboczym. Wygenerowane pliki zestawu SDK będą mieć *DigitalTwinsApi* przestrzeni nazw. Ta przestrzeń nazw będzie nadal używana przez pozostałe przykłady użycia w tym artykule.

AutoRest obsługuje szeroką gamę generatorów kodu języka.

## <a name="make-the-sdk-into-a-class-library"></a>Tworzenie zestawu SDK w bibliotece klas

Pliki generowane przez program AutoRest można uwzględnić bezpośrednio w rozwiązaniu platformy .NET. Istnieje jednak możliwość dołączenia zestawu SDK usługi Azure Digital bliźniaczych reprezentacji do kilku oddzielnych projektów (aplikacji klienckich, Azure Functions aplikacji i innych). Z tego powodu pomocne może być skompilowanie oddzielnego projektu (biblioteki klas .NET) z wygenerowanych plików. Następnie można dołączyć ten projekt biblioteki klas do kilku rozwiązań jako odwołanie do projektu.

Ta sekcja zawiera instrukcje dotyczące sposobu tworzenia zestawu SDK jako biblioteki klas, która jest własnym projektem i może być uwzględniona w innych projektach. Te kroki są zależne od **programu Visual Studio**.

Oto konkretne kroki:

1. Tworzenie nowego rozwiązania programu Visual Studio dla biblioteki klas
2. Użyj *DigitalTwinsApi* jako nazwy projektu
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt *DigitalTwinsApi* wygenerowanego rozwiązania i wybierz polecenie *Dodaj > istniejący element...*
4. Znajdź folder, w którym został wygenerowany zestaw SDK, a następnie wybierz pliki na poziomie głównym
5. Naciśnij przycisk "OK"
6. Dodaj folder do projektu (Zaznacz projekt prawym przyciskiem myszy w Eksplorator rozwiązań i wybierz polecenie *dodaj > nowy folder*)
7. Nazwij *modele* folderów
8. Kliknij prawym przyciskiem myszy folder *modele* w Eksploratorze rozwiązań i wybierz polecenie *Dodaj > istniejący element...*
9. Wybierz pliki w folderze *modele* wygenerowanego zestawu SDK i naciśnij przycisk "OK"

Aby pomyślnie skompilować zestaw SDK, potrzebne są następujące odwołania:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Aby je dodać, Otwórz *narzędzia > Menedżer pakietów nuget > zarządzanie pakietami NuGet dla rozwiązania.*...

1. Na panelu upewnij się, że wybrana jest karta *Przeglądaj*
2. Wyszukaj *firmę Microsoft. REST*
3. Wybierz `ClientRuntime` pakiety i `ClientRuntime.Azure` Dodaj je do rozwiązania

Teraz można skompilować projekt i dołączyć go jako odwołanie do projektu w dowolnej zapisanej aplikacji Digital bliźniaczych reprezentacji systemu Azure.

## <a name="tips-for-using-the-sdk"></a>Porady dotyczące korzystania z zestawu SDK

Ta sekcja zawiera ogólne informacje i wskazówki dotyczące korzystania z wygenerowanego zestawu SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Wywołania synchroniczne i asynchroniczne

Wszystkie funkcje zestawu SDK są w wersji synchronicznej i asynchronicznej.

### <a name="typed-and-untyped-data"></a>Dane wpisane i nietypu

Wywołania interfejsu API REST zwykle zwracają obiekty silnie wpisane. Jednak ponieważ usługa Azure Digital bliźniaczych reprezentacji umożliwia użytkownikom Definiowanie własnych typów niestandardowych dla bliźniaczych reprezentacji, nie ma możliwości wstępnego definiowania statycznych danych zwrotnych dla wielu wywołań cyfrowych bliźniaczych reprezentacji platformy Azure. Zamiast tego interfejsy API zwracają typy otoki o jednoznacznie określonym typie, gdy ma to zastosowanie, a dane o niestandardowym typie są w Json.NET obiekty (używane wszędzie tam, gdzie typ danych "Object" pojawia się w sygnaturach interfejsów API). Te obiekty można rzutować odpowiednio w kodzie.

### <a name="error-handling"></a>Obsługa błędów

Za każdym razem, gdy w zestawie SDK wystąpi błąd (w tym błędy HTTP, takie jak 404), zestaw SDK zgłosi wyjątek. Z tego powodu ważne jest Hermetyzowanie wszystkich wywołań interfejsu API w blokach try/catch.

Oto fragment kodu, który próbuje dodać sznurek i przechwytuje wszystkie błędy w tym procesie:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Stronicowanie

AutoRest generuje dwa typy wzorców stronicowania dla zestawu SDK:
* Jeden dla wszystkich interfejsów API z wyjątkiem interfejsu API zapytań
* Jeden dla interfejsu API zapytania

W przypadku wzorca stronicowania niezwiązanego z kwerendą poniżej przedstawiono przykładową metodę pobierania stronicowanej listy relacji wychodzących z usługi Azure Digital bliźniaczych reprezentacji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Drugi wzorzec jest generowany tylko dla interfejsu API zapytań. Używa `continuationToken` jawnie.

>[!TIP]
> Głównym powodem pobierania stron jest obliczenie [opłat za jednostkę zapytania](concepts-query-units.md) dla wywołania interfejsu API zapytań.

Oto przykład z tym wzorcem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z instrukcjami, aby utworzyć aplikację kliencką, w której można korzystać z zestawu SDK:
* [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)
