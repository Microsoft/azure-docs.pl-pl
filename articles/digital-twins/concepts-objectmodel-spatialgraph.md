---
title: Zrozumienie modeli obiektów i grafu analizy przestrzennej — Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Korzystanie z usługi Azure Digital bliźniaczych reprezentacji do modelowania relacji między osobami, miejscami i urządzeniami
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265209"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Zrozumienie modeli obiektów Digital bliźniaczych reprezentacji i grafu analizy przestrzennej

Azure Digital bliźniaczych reprezentacji to usługa Azure IoT, która zapewnia kompleksowe wirtualne reprezentacje środowisk fizycznych i skojarzonych urządzeń, czujników i osób. Usprawnia to Programowanie poprzez organizowanie koncepcji specyficznych dla domeny w przydatne modele. Modele są następnie zlokalizowane w grafie analizy przestrzennej. Takie pojęcia wiernie modeluje relacje i interakcje między osobami, miejscami i urządzeniami.

Modele obiektów Digital bliźniaczych reprezentacji opisują koncepcje, kategorie i właściwości specyficzne dla domeny. Modele są wstępnie zdefiniowane przez użytkowników, którzy chcą dostosować rozwiązanie do konkretnych potrzeb. Razem te wstępnie zdefiniowane modele obiektów bliźniaczych reprezentacji cyfrowych składają się na _Ontology_. Ontology Smart budynku opisuje regiony, miejsca, piętra, biura, strefy, pokoje konferencyjne i pokoje fokusu. Ontology w sieci energetycznej zawiera opis różnych stacji zasilania, podstacji, zasobów energii i klientów. Za pomocą cyfrowych modeli obiektów bliźniaczych reprezentacji i ontologie, można dostosować różne scenariusze i potrzeby.

Korzystając z modeli obiektów Digital bliźniaczych reprezentacji i Ontology na miejscu, można wypełnić _Wykres przestrzenny_. Wykresy przestrzenne to wirtualne reprezentacje wielu relacji między spacjami, urządzeniami i osobami, które są istotne dla rozwiązania IoT. Ten diagram przedstawia przykład wykresu przestrzennego, który używa inteligentnego kompilowania Ontology.

[![Kompilowanie wykresu przestrzennego Digital bliźniaczych reprezentacji](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Wykres przestrzenny zawiera razem miejsca, urządzenia, czujniki i użytkowników. Każda z nich jest połączona w sposób, który modeluje świecie rzeczywistym. W tym przykładzie miejsce 43 ma cztery piętra, każdy z wielu różnych obszarów. Użytkownicy są powiązani ze swoimi stacjami roboczymi i mają dostęp do fragmentów grafu. Administrator ma uprawnienia do wprowadzania zmian w grafie przestrzennym, podczas gdy odwiedzający ma uprawnienia do wyświetlania tylko niektórych danych kompilacji.

## <a name="digital-twins-object-models"></a>Modele obiektów Digital bliźniaczych reprezentacji

Modele obiektów Digital bliźniaczych reprezentacji obsługują następujące główne kategorie obiektów:

- **Spacje** są lokalizacjami wirtualnymi lub fizycznymi `Tenant`, `Customer`na `Region`przykład, `Venue`,, i.
- **Urządzenia** są wirtualnymi lub fizycznymi elementami sprzętu, na przykład `AwesomeCompany Device` i `Raspberry Pi 3`.
- **Czujniki** są obiektami, które wykrywają zdarzenia, na `AwesomeCompany Temperature Sensor` przykład `AwesomeCompany Presence Sensor`i.
- **Użytkownicy** identyfikują użytkowników i ich cechy.

Inne kategorie obiektów to:

- **Zasoby** są dołączane do obszaru i zazwyczaj reprezentują zasoby platformy Azure, które mają być używane przez obiekty na wykresie przestrzennym `IoTHub`, na przykład.
- Obiekty **BLOB** są dołączone do obiektów (takich jak spacje, urządzenia, czujniki i użytkownicy). Są one używane jako pliki z typem MIME i metadanymi, na przykład `maps` `pictures`,, i. `manuals`
- **Rozszerzone typy** to rozszerzalne wyliczenia, które rozszerzają jednostki o konkretnej charakterystyce `SpaceType` , `SpaceSubtype`na przykład i.
- **Ontologie** reprezentuje zestaw rozszerzonych typów `Default`, na przykład `Building` `BACnet`,,, i. `EnergyGrid`
- **Klucze właściwości i wartości** to niestandardowe właściwości spacji, urządzeń, czujników i użytkowników. Mogą one być używane razem z wbudowaną charakterystyką, na przykład `DeltaProcessingRefreshTime` jako klucz i `10` jako wartość.
- **Role** są zestawami uprawnień przypisanych do użytkowników i urządzeń w grafie przestrzennym, na `Space Administrator`przykład `User Administrator`,, `Device Administrator`i.
- **Przypisania ról** są skojarzeniami między rolą a obiektem w grafie przestrzennym. Na przykład użytkownikowi lub jednostce usługi można udzielić uprawnienia do zarządzania przestrzenią w grafie przestrzennym.
- **Magazyny kluczy zabezpieczeń** zapewniają klucze zabezpieczeń dla wszystkich urządzeń w hierarchii pod danym obiektem obszaru, aby umożliwić bezpieczne komunikowanie się urządzenia z bliźniaczych reprezentacji cyfrowym.
- **Funkcje zdefiniowane przez użytkownika** (UDF) umożliwiają dostosowywalne przetwarzanie danych telemetrycznych czujnika w ramach wykresu przestrzennego. Na przykład UDF może:
  - Ustaw wartość czujnika.
  - Wykonaj logikę niestandardową opartą na odczytach czujników i ustaw dane wyjściowe na spację.
  - Dołącz metadane do obszaru.
  - Wysyłaj powiadomienia po spełnieniu wstępnie zdefiniowanych warunków. Obecnie UDF można napisać w języku JavaScript.
- **Dopasowania** są obiektami, które określają, które UDF są wykonywane dla danego komunikatu telemetrii.
- **Punkty końcowe** to lokalizacje, w których można kierować komunikaty telemetryczne i cyfrowe zdarzenia bliźniaczych reprezentacji, `Event Hub`na `Service Bus`przykład, `Event Grid`, i.

## <a name="spatial-intelligence-graph"></a>Wykres analizy przestrzennej

Wykres przestrzenny to hierarchiczny wykres obszarów, urządzeń i osób zdefiniowanych w modelu obiektów Digital bliźniaczych reprezentacji. Wykres przestrzenny obsługuje dziedziczenie, filtrowanie, przechodzenie, skalowalność i rozszerzalność. Możesz zarządzać wykresem przestrzennym i korzystać z niego przy użyciu kolekcji interfejsów API REST.

Jeśli w subskrypcji zostanie wdrożona usługa Digital bliźniaczych reprezentacji, staje się ona administratorem globalnym węzła głównego. Następnie zostanie automatycznie udzielony pełny dostęp do całej struktury. Udostępnianie spacji na grafie przy użyciu interfejsu API Space. Udostępnianie usług przy użyciu interfejsu API i czujników urządzenia przy użyciu interfejsu API czujnika. [Narzędzia Open Source](https://github.com/Azure-Samples/digital-twins-samples-csharp) są również dostępne do zbiorczego udostępniania wykresu.

**Dziedziczenie grafu**. Dziedziczenie ma zastosowanie do uprawnień i właściwości, które są podrzędne od węzła nadrzędnego do wszystkich węzłów znajdujących się poniżej. Na przykład po przypisaniu roli do użytkownika w danym węźle użytkownik ma uprawnienia tej roli do danego węzła i każdego węzła poniżej. Każdy klucz właściwości i rozszerzony typ zdefiniowany dla danego węzła jest dziedziczony przez wszystkie węzły znajdujące się pod tym węzłem.

**Filtrowanie grafu**. Filtrowanie służy do zawężania wyników żądania. Można filtrować według identyfikatorów, nazw, typów, podtypów, przestrzeni nadrzędnej i skojarzonych spacji. Można również filtrować według typów danych czujników, kluczy właściwości i wartości, *przechodzenia*, *minLevel*, *MaxLevel*i innych parametrów filtru OData.

**Przechodzenie grafu**. Wykres przestrzenny można przechodzenie przez jego głębokość i szerokość. Aby uzyskać głębokość, przechodzenie wykresu w dół lub w dół przy użyciu parametrów *przechodzących*, *minLevel*i *maxLevel*. Przechodzenie wykresu, aby uzyskać węzły równorzędne bezpośrednio dołączone do obszaru nadrzędnego lub jednego z jego elementów podrzędnych dla szerokości. Podczas wykonywania zapytania względem obiektu można uzyskać wszystkie powiązane obiekty, które mają relacje z tym obiektem za pomocą parametru *include* interfejsów API.

**Skalowalność grafu**. Program Digital bliźniaczych reprezentacji gwarantuje skalowalność wykresu, dzięki czemu może obsługiwać rzeczywiste obciążenia. Digital bliźniaczych reprezentacji może służyć do reprezentowania dużych portfeli rzeczywistych, infrastruktury, urządzeń, czujników, telemetrii i innych.

**Rozszerzalność grafu**. Użyj rozszerzalności, aby dostosować podstawowe modele obiektów bliźniaczych reprezentacji przy użyciu nowych typów i ontologie. Dane cyfrowego bliźniaczych reprezentacji można także wzbogacać o rozszerzalne właściwości i wartości.

### <a name="spatial-intelligence-graph-management-apis"></a>Interfejsy API zarządzania wykresami w ramach analizy przestrzennej

Po wdrożeniu Digital bliźniaczych reprezentacji z [Azure Portal](https://portal.azure.com), zostanie automatycznie wygenerowany adres URL programu [Swagger](https://swagger.io/tools/swagger-ui/) interfejsów API zarządzania. Jest on wyświetlany w Azure Portal w sekcji **Przegląd** w następującym formacie.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nazwa | Zamień na |
| --- | --- |
| YOUR_INSTANCE_NAME | Nazwa używanego wystąpienia usługi Digital Twins |
| YOUR_LOCATION | Region serwera, w którym jest hostowane używane wystąpienie |

 Pełny format adresu URL zostanie wyświetlony na tym obrazie.

[![Interfejs API zarządzania portalem Digital bliźniaczych reprezentacji](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Aby uzyskać więcej informacji na temat korzystania z wykresów analizy przestrzennej, odwiedź stronę usługi Azure Digital bliźniaczych reprezentacji Management Apis zobaczyć Preview.

> [!TIP]
> W wersji zapoznawczej programu Swagger zobaczyć przedstawiono zestaw funkcji interfejsu API.
> Jest on hostowany pod adresem [docs.westcentralus.azuresmartspaces.NET/Management/Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Dowiedz się więcej [na temat korzystania z struktury Swagger](how-to-use-swagger.md).

Wszystkie wywołania interfejsu API muszą zostać uwierzytelnione przy użyciu protokołu [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Interfejsy API przestrzegają [Konwencji wskazówek dotyczących interfejsu API REST firmy Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Większość interfejsów API, które zwracają kolekcje, obsługują opcje zapytania systemu [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) .

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o łączności urządzeń i sposobach wysyłania komunikatów telemetrycznych do bliźniaczych reprezentacji cyfrowych, odczytaj informacje o [łączności i danych telemetrycznych usługi Azure Digital bliźniaczych reprezentacji](concepts-device-ingress.md).

- Aby dowiedzieć się więcej o ograniczeniach i ograniczaniu wydajności interfejsu API zarządzania, przeczytaj temat [usługa Azure Digital bliźniaczych reprezentacji API Management i ograniczenia](concepts-service-limits.md).
