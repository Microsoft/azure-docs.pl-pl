---
title: Konfigurowanie diagnostyki
titleSuffix: Azure Digital Twins
description: Zobacz jak włączyć rejestrowanie przy użyciu ustawień diagnostycznych.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 11a7b4876c773922d4b0ed28f7047912b738ee6a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091739"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji: rejestrowanie diagnostyczne

Usługa Azure Digital bliźniaczych reprezentacji zbiera [metryki](troubleshoot-metrics.md) dla wystąpienia usługi, które zawierają informacje o stanie zasobów. Za pomocą tych metryk można ocenić ogólną kondycję usługi Azure Digital bliźniaczych reprezentacji i połączone z nią zasoby. Te dane statystyczne związane z użytkownikiem pomagają zobaczyć, co się dzieje z usługą Azure Digital bliźniaczych reprezentacji, oraz jak przeprowadzić analizę głównych przyczyn problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

W tym artykule pokazano, jak włączyć **rejestrowanie diagnostyczne** dla danych metryk z wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Dzienników tych można użyć do rozwiązywania problemów z usługą i konfigurowania ustawień diagnostycznych w celu wysyłania metryk usługi Azure Digital bliźniaczych reprezentacji do różnych miejsc docelowych. Więcej informacji o tych ustawieniach można znaleźć w temacie [*Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Włączanie ustawień diagnostycznych przy użyciu Azure Portal

Poniżej przedstawiono sposób włączania ustawień diagnostycznych dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Można go znaleźć, wpisując jego nazwę na pasku wyszukiwania portalu. 

2. Z menu wybierz pozycję **Ustawienia diagnostyczne** , a następnie **Dodaj ustawienie diagnostyczne** .

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

3. Na następnej stronie wypełnij następujące wartości:
     * **Nazwa ustawienia diagnostycznego** : nadaj nazwę ustawień diagnostycznych.
     * **Szczegóły kategorii** : Wybierz operacje, które chcesz monitorować, i zaznacz pola wyboru, aby włączyć diagnostykę tych operacji. Dla operacji, które mogą być zgłaszane przez ustawienia diagnostyczne, są następujące:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - Queryoperation obiektu DataService
        - AllMetrics
        
        Aby uzyskać więcej informacji na temat tych opcji, zobacz sekcję [*szczegóły kategorii*](#category-details) poniżej.
     * **Szczegóły lokalizacji docelowej** : Wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:
        - Wysyłanie do usługi Log Analytics
        - Zarchiwizuj na koncie magazynu
        - Przesyłaj strumieniowo do centrum zdarzeń

        Może zostać wyświetlony monit o podanie dodatkowych informacji, jeśli są one niezbędne do wyboru miejsca docelowego.  
    
4. Zapisz nowe ustawienia. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym z powrotem na stronie **Ustawienia diagnostyczne** dla danego wystąpienia. 

## <a name="category-details"></a>Szczegóły kategorii

Poniżej znajdują się dodatkowe szczegóły dotyczące kategorii dziennika, które można wybrać w obszarze **szczegóły kategorii** podczas konfigurowania ustawień diagnostycznych.

| Kategoria dziennika | Opis |
| --- | --- |
| ADTModelsOperation | Rejestruj wszystkie wywołania interfejsu API odnoszące się do modeli |
| ADTQueryOperation | Rejestruj wszystkie wywołania interfejsu API odnoszące się do zapytań |
| ADTEventRoutesOperation | Rejestruj wszystkie wywołania interfejsu API dotyczące tras zdarzeń oraz wychodzące zdarzenia z usługi Azure Digital bliźniaczych reprezentacji do usługi punktu końcowego, takiej jak Event Grid, Event Hubs i Service Bus |
| ADTDigitalTwinsOperation | Rejestruj wszystkie wywołania interfejsu API związane z usługą Azure Digital bliźniaczych reprezentacji |

Każda kategoria dziennika składa się z operacji zapisu, odczytu, usuwania i akcji.  Te mapy do wywołań interfejsu API REST są następujące:

| Typ zdarzenia | Operacje interfejsu API REST |
| --- | --- |
| Zapisywanie | PUT i PATCH |
| Odczyt | GET |
| Usuń | DELETE |
| Akcja | POST |

Poniżej znajduje się kompleksowa Lista operacji i odpowiednich [wywołań interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji](/rest/api/azure-digitaltwins/) , które są rejestrowane w każdej kategorii. 

>[!NOTE]
> Każda kategoria dziennika zawiera kilka wywołań operacji/interfejsu API REST. W poniższej tabeli każda kategoria dziennika jest mapowana na wszystkie operacje/wywołania interfejsu API REST, dopóki nie zostanie wyświetlona Następna kategoria dziennika. 

| Kategoria dziennika | Operacja | Wywołania interfejsu API REST i inne zdarzenia |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modele/zapis | Interfejs API aktualizacji modeli dwuosiowych |
|  | Microsoft. DigitalTwins/modele/odczyt | Cyfrowe modele przędzy uzyskiwane według identyfikatorów i interfejsów API listy |
|  | Microsoft. DigitalTwins/modele/usuwanie | Cyfrowe modele dwuosiowe — Usuwanie interfejsu API |
|  | Microsoft. DigitalTwins/modele/akcja | Modele Digital bliźniaczy Add API |
| ADTQueryOperation | Microsoft. DigitalTwins/kwerenda/akcja | Interfejs API bliźniaczych reprezentacji zapytań |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Dodawanie interfejsu API tras zdarzeń |
|  | Microsoft. DigitalTwins/eventroutes/odczyt | Trasy zdarzeń Get według identyfikatorów i interfejsów API |
|  | Microsoft. DigitalTwins/eventroutes/Delete | Interfejs API usuwania tras zdarzeń |
|  | Microsoft. DigitalTwins/eventroutes/Action | Wystąpił błąd podczas próby opublikowania zdarzeń w usłudze punktu końcowego (nie wywołania interfejsu API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digital bliźniaczych reprezentacji Add, Dodawanie relacji, aktualizowanie, aktualizowanie składnika |
|  | Microsoft. DigitalTwins/DigitalTwins/odczyt | Digital bliźniaczych reprezentacji Get według identyfikatora, Pobierz składnik, uzyskaj relację według identyfikatora, Wyświetl listę relacji przychodzących, relacje list |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Digital bliźniaczych reprezentacji Delete, usuwanie relacji |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Dane telemetryczne składnika Digital bliźniaczych reprezentacji Send, wysyłanie danych telemetrycznych |

## <a name="log-schemas"></a>Schematy dzienników 

Każda kategoria dziennika zawiera schemat, który definiuje sposób zgłaszania zdarzeń w tej kategorii. Każdy pojedynczy wpis dziennika jest przechowywany jako tekst i sformatowany jako obiekt BLOB JSON. Pola dziennika i przykładowe treści JSON są dostępne dla każdego typu dziennika poniżej. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` i `ADTQueryOperation` używają spójnego schematu dziennika interfejsu API; `ADTEventRoutesOperation` ma własny oddzielny schemat.

### <a name="api-log-schemas"></a>Schematy dzienników interfejsu API

Ten schemat dziennika jest spójny dla `ADTDigitalTwinsOperation` , `ADTModelsOperation` i `ADTQueryOperation` . Zawiera informacje dotyczące wywołań interfejsu API do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Oto opisy pól i właściwości dzienników interfejsu API.

| Nazwa pola | Typ danych | Opis |
|-----|------|-------------|
| `Time` | DateTime | Data i godzina wystąpienia tego zdarzenia (UTC) |
| `ResourceID` | String | Azure Resource Manager identyfikator zasobu dla zasobu, w którym miało miejsce zdarzenie |
| `OperationName` | String  | Typ akcji wykonywanej w ramach zdarzenia |
| `OperationVersion` | String | Wersja interfejsu API wykorzystana podczas zdarzenia |
| `Category` | String | Typ emitowanego zasobu |
| `ResultType` | String | Wynik zdarzenia |
| `ResultSignature` | String | Kod stanu HTTP dla zdarzenia |
| `ResultDescription` | String | Dodatkowe szczegóły dotyczące zdarzenia |
| `DurationMs` | String | Czas trwania zdarzenia w milisekundach |
| `CallerIpAddress` | String | Maskowany źródłowy adres IP dla zdarzenia |
| `CorrelationId` | Guid (identyfikator GUID) | Klient dostarczył unikatowy identyfikator dla zdarzenia |
| `Level` | String | Ważność rejestrowania zdarzenia |
| `Location` | String | Region, w którym miało miejsce zdarzenie |
| `RequestUri` | Adresu | Punkt końcowy użyty podczas zdarzenia |

Poniżej przedstawiono przykładowe treści JSON dla tego typu dzienników.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>Schematy dzienników ruchu wychodzącego

Jest to schemat `ADTEventRoutesOperation` dzienników. Zawierają one informacje o wyjątkach i operacjach interfejsu API wokół punktów końcowych wychodzących podłączonych do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

|Nazwa pola | Typ danych | Opis |
|-----|------|-------------|
| `Time` | DateTime | Data i godzina wystąpienia tego zdarzenia (UTC) |
| `ResourceId` | String | Azure Resource Manager identyfikator zasobu dla zasobu, w którym miało miejsce zdarzenie |
| `OperationName` | String  | Typ akcji wykonywanej w ramach zdarzenia |
| `Category` | String | Typ emitowanego zasobu |
| `ResultDescription` | String | Dodatkowe szczegóły dotyczące zdarzenia |
| `Level` | String | Ważność rejestrowania zdarzenia |
| `Location` | String | Region, w którym miało miejsce zdarzenie |
| `EndpointName` | String | Nazwa punktu końcowego ruchu wychodzącego utworzonego w usłudze Azure Digital bliźniaczych reprezentacji |

Poniżej przedstawiono przykładowe treści JSON dla tego typu dzienników.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [*zbieranie i korzystanie z danych dzienników z zasobów platformy Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Aby uzyskać informacje na temat metryk bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*Rozwiązywanie problemów: wyświetlanie metryk z Azure monitor*](troubleshoot-metrics.md).
* Aby dowiedzieć się, jak włączyć alerty dla metryk, zobacz [*Rozwiązywanie problemów: Konfigurowanie alertów*](troubleshoot-alerts.md).