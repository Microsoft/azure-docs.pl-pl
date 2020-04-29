---
title: Azure Stream Analytics pól JobConfig. JSON
description: W tym artykule wymieniono pola obsługiwane dla pliku Azure Stream Analytics JobConfig. JSON, który służy do tworzenia zadań w programie Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617959"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics pól JobConfig. JSON

Następujące pola są obsługiwane w pliku *JobConfig. JSON* , który służy do [tworzenia zadania Azure Stream Analytics przy użyciu Visual Studio Code](quick-create-vs-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Nazwa|Typ|Wymagany|Wartość|
|----|----|--------|-----|
|Lokalna|ciąg|Nie|Ustawienia regionalne danych zadania usługi Stream Analytics. Wartość powinna być nazwą obsługiwanej. Wartość domyślna to "pl-US", jeśli nie została określona.|
|OutputErrorPolicy|ciąg|Nie|Wskazuje zasady, które mają być stosowane do zdarzeń, które docierają do danych wyjściowych i nie mogą być zapisywane w magazynie zewnętrznym ze względu na nieprawidłową wartość kolumny (brakujące wartości kolumn, wartości kolumn o nieprawidłowym typie lub rozmiarze). -Zatrzymaj lub upuść|
|EventsLateArrivalMaxDelayInSeconds|liczba całkowita|Nie|Maksymalne opóźnienie (w sekundach), w którym można uwzględnić zdarzenia docierające do późnego czasu. Obsługiwany zakres to-1 do 1814399 (20.23:59:59 dni) i-1 jest używany do określenia oczekiwania na czas nieokreślony. Jeśli właściwość jest nieobecna, jest interpretowana jako wartość-1.|
|EventsOutOfOrderMaxDelayInSeconds|liczba całkowita|Nie|Maksymalne opóźnienie (w sekundach), w którym można korygować zdarzenia poza kolejnością w celu przywrócenia ich wartości.|
|EventsOutOfOrderPolicy|ciąg|Nie|Wskazuje zasady, które mają być stosowane do zdarzeń, które odbierają się poza kolejnością w strumieniu zdarzeń wejściowych. -Dostosuj lub upuść|
|StreamingUnits|liczba całkowita|Tak|Określa liczbę jednostek przesyłania strumieniowego używanych przez zadanie przesyłania strumieniowego.|
|CompatibilityLevel|ciąg|Nie|Steruje pewnymi zachowaniami środowiska uruchomieniowego zadania przesyłania strumieniowego. -Akceptowalne wartości to "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|wartość logiczna|Nie|Ustaw wartość true, aby włączyć to zadanie do komunikowania się z innymi usługami platformy Azure za pomocą tożsamości zarządzanej Azure Active Directory.|
|GlobalStorage. AccountName|ciąg|Nie|Globalne konto magazynu jest używane do przechowywania zawartości powiązanej z zadaniem usługi Stream Analytics, na przykład migawek danych referencyjnych SQL.|
|GlobalStorage. AccountKey|ciąg|Nie|Odpowiedni klucz konta magazynu globalnego.|
|DataSourceCredentialDomain|ciąg|Nie|Właściwość zastrzeżona dla lokalnego magazynu poświadczeń.|
|ScriptType|ciąg|Tak|Właściwość zastrzeżona do wskazywania typu tego pliku źródłowego. Akceptowalna wartość to "JobConfig" dla JobConfig. JSON.|
|Tagi|Pary klucz-wartość JSON|Nie|Tagi to pary nazwa/wartość, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu dla wielu zasobów i grup zasobów. W nazwach tagów jest rozróżniana wielkość liter i w wartościach tagów jest uwzględniana wielkość liter.|

## <a name="next-steps"></a>Następne kroki

* [Utwórz zadanie Azure Stream Analytics w Visual Studio Code](quick-create-vs-code.md)
* [Testowanie Stream Analytics zapytań lokalnie z przykładowymi danymi przy użyciu Visual Studio Code](visual-studio-code-local-run.md)
* [Przetestuj Stream Analytics zapytań lokalnie względem danych wejściowych strumienia na żywo za pomocą Visual Studio Code](visual-studio-code-local-run-live-input.md)
*[Wdróż zadanie Azure Stream Analytics przy użyciu pakietu Ci/CD npm](setup-cicd-vs-code.md)