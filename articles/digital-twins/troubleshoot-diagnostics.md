---
title: Włączanie dzienników diagnostycznych i wykonywanie dotyczących ich zapytań
titleSuffix: Azure Digital Twins
description: Zobacz jak włączyć rejestrowanie przy użyciu ustawień diagnostycznych i wysyłać zapytania do dzienników w celu natychmiastowego wyświetlenia.
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c600ced8896a3847b80d854c9e230310cca4c98d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100588604"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji: rejestrowanie diagnostyczne

Usługa Azure Digital bliźniaczych reprezentacji może zbierać dzienniki dla wystąpienia usługi, aby monitorować jego wydajność, dostęp i inne dane. Za pomocą tych dzienników można uzyskać informacje o tym, co dzieje się w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, i przeprowadzić analizę głównych przyczyn problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

W tym artykule opisano sposób [**konfigurowania ustawień diagnostycznych**](#turn-on-diagnostic-settings) w [Azure Portal](https://portal.azure.com) , aby rozpocząć zbieranie dzienników z wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Możesz również określić, gdzie mają być przechowywane dzienniki (na przykład Log Analytics lub wybrane konto magazynu).

Ten artykuł zawiera również listę wszystkich [kategorii dzienników](#log-categories) i [schematów dzienników](#log-schemas) zbieranych przez usługę Azure Digital bliźniaczych reprezentacji.

Po skonfigurowaniu dzienników można także [**wysyłać zapytania do dzienników**](#view-and-query-logs) , aby szybko zbierać szczegółowe informacje.

## <a name="turn-on-diagnostic-settings"></a>Włączanie ustawień diagnostycznych 

Włącz ustawienia diagnostyczne, aby rozpocząć zbieranie dzienników w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Możesz również wybrać miejsce docelowe, w którym mają być przechowywane eksportowane dzienniki. Poniżej przedstawiono sposób włączania ustawień diagnostycznych dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Można go znaleźć, wpisując jego nazwę na pasku wyszukiwania portalu. 

2. Z menu wybierz pozycję **Ustawienia diagnostyczne** , a następnie **Dodaj ustawienie diagnostyczne**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Na następnej stronie wypełnij następujące wartości:
     * **Nazwa ustawienia diagnostycznego**: nadaj nazwę ustawień diagnostycznych.
     * **Szczegóły kategorii**: Wybierz operacje, które chcesz monitorować, i zaznacz pola wyboru, aby włączyć diagnostykę tych operacji. Dla operacji, które mogą być zgłaszane przez ustawienia diagnostyczne, są następujące:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - Queryoperation obiektu DataService
        - AllMetrics
        
        Aby uzyskać więcej informacji na temat tych kategorii i zawartych w nich informacji, zobacz sekcję [*kategorie dzienników*](#log-categories) poniżej.
     * **Szczegóły lokalizacji docelowej**: Wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:
        - Wysyłanie do usługi Log Analytics
        - Zarchiwizuj na koncie magazynu
        - Przesyłaj strumieniowo do centrum zdarzeń

        Może zostać wyświetlony monit o podanie dodatkowych informacji, jeśli są one niezbędne do wyboru miejsca docelowego.  
    
4. Zapisz nowe ustawienia. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych, na której użytkownik wypełnił nazwę ustawienia diagnostycznego i wprowadził pewne wybory pola wyboru dla szczegółów kategorii i szczegółów miejsca docelowego. Przycisk Zapisz jest wyróżniony." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym z powrotem na stronie **Ustawienia diagnostyczne** dla danego wystąpienia. 

Aby uzyskać bardziej szczegółowe informacje na temat ustawień diagnostycznych i ich opcji instalacji, można odwiedzić stronę [*Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych*](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="log-categories"></a>Kategorie dzienników

Poniżej znajdują się więcej szczegółów na temat kategorii dzienników zbieranych przez usługę Azure Digital bliźniaczych reprezentacji.

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
| `ResourceID` | Ciąg | Azure Resource Manager identyfikator zasobu dla zasobu, w którym miało miejsce zdarzenie |
| `OperationName` | Ciąg  | Typ akcji wykonywanej w ramach zdarzenia |
| `OperationVersion` | Ciąg | Wersja interfejsu API wykorzystana podczas zdarzenia |
| `Category` | Ciąg | Typ emitowanego zasobu |
| `ResultType` | Ciąg | Wynik zdarzenia |
| `ResultSignature` | Ciąg | Kod stanu HTTP dla zdarzenia |
| `ResultDescription` | Ciąg | Dodatkowe szczegóły dotyczące zdarzenia |
| `DurationMs` | Ciąg | Czas trwania zdarzenia w milisekundach |
| `CallerIpAddress` | Ciąg | Maskowany źródłowy adres IP dla zdarzenia |
| `CorrelationId` | Guid (identyfikator GUID) | Klient dostarczył unikatowy identyfikator dla zdarzenia |
| `Level` | Ciąg | Ważność rejestrowania zdarzenia |
| `Location` | Ciąg | Region, w którym miało miejsce zdarzenie |
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
| `ResourceId` | Ciąg | Azure Resource Manager identyfikator zasobu dla zasobu, w którym miało miejsce zdarzenie |
| `OperationName` | Ciąg  | Typ akcji wykonywanej w ramach zdarzenia |
| `Category` | Ciąg | Typ emitowanego zasobu |
| `ResultDescription` | Ciąg | Dodatkowe szczegóły dotyczące zdarzenia |
| `Level` | Ciąg | Ważność rejestrowania zdarzenia |
| `Location` | Ciąg | Region, w którym miało miejsce zdarzenie |
| `EndpointName` | Ciąg | Nazwa punktu końcowego ruchu wychodzącego utworzonego w usłudze Azure Digital bliźniaczych reprezentacji |

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

## <a name="view-and-query-logs"></a>Wyświetlanie dzienników i wykonywanie zapytań

Wcześniej w tym artykule zostały skonfigurowane typy dzienników do przechowywania i określenia ich lokalizacji przechowywania.

W celu rozwiązywania problemów i generowania szczegółowych informacji z tych dzienników można generować **niestandardowe zapytania**. Aby rozpocząć pracę, możesz również skorzystać z kilku przykładowych zapytań dostarczonych przez usługę, które zawierają odpowiedzi na często zadawane pytania dotyczące ich wystąpienia.

Poniżej przedstawiono sposób wykonywania zapytań dotyczących dzienników dla danego wystąpienia.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Można go znaleźć, wpisując jego nazwę na pasku wyszukiwania portalu. 

2. Wybierz pozycję **dzienniki** z menu, aby otworzyć stronę zapytania dziennika. Strona zostanie otwarta w oknie o nazwie *zapytania*.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Zrzut ekranu przedstawiający stronę dzienników wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Jest ona nadana za pomocą okna zapytania, w którym wyświetlane są wstępnie skompilowane zapytania o nazwach w różnych opcjach dziennika, takich jak opóźnienie interfejsu API DigitalTwin i opóźnienie interfejsu API modelu." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Są to wstępnie zbudowane przykładowe zapytania przeznaczone dla różnych dzienników. Można wybrać jedno z zapytań, aby załadować je do edytora zapytań i uruchomić w celu wyświetlenia tych dzienników dla danego wystąpienia.

    Możesz również zamknąć okno *zapytania* bez uruchamiania niczego, aby przejść bezpośrednio do strony edytora zapytań, gdzie można napisać lub edytować niestandardowy kod zapytania.

3. Po opuszczeniu okna *zapytania* zostanie wyświetlona strona głównej edytora zapytań. W tym miejscu możesz wyświetlać i edytować tekst przykładowych zapytań lub pisać własne zapytania od początku.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Zrzut ekranu przedstawiający stronę dzienników wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Okno zapytania zostało usunięte, a zamiast tego znajduje się lista różnych dzienników, w okienku Edycja wyświetlana jest edytowalny kod zapytania i okienko pokazujące historię zapytań." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    W okienku po lewej stronie 
    - Karta *tabele* zawiera różne [kategorie dzienników](#log-categories) bliźniaczych reprezentacji cyfrowych platformy Azure, które są dostępne do użycia w zapytaniach. 
    - Karta *zapytania* zawiera przykładowe zapytania, które można załadować do edytora.
    - Karta *Filtr* pozwala dostosować filtrowany widok danych zwracanych przez zapytanie.

Aby uzyskać bardziej szczegółowe informacje na temat zapytań dzienników i sposobu ich zapisywania, można odwiedzić [*Przegląd zapytań dzienników w Azure monitor*](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [*zbieranie i korzystanie z danych dzienników z zasobów platformy Azure*](../azure-monitor/essentials/platform-logs-overview.md).
* Aby uzyskać informacje na temat metryk bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*Rozwiązywanie problemów: wyświetlanie metryk z Azure monitor*](troubleshoot-metrics.md).
* Aby dowiedzieć się, jak włączyć alerty dla metryk, zobacz [*Rozwiązywanie problemów: Konfigurowanie alertów*](troubleshoot-alerts.md).