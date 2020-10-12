---
title: Azure Stream Analytics JobConfig.jspól
description: W tym artykule wymieniono pola obsługiwane dla JobConfig.jsAzure Stream Analytics w pliku używanym do tworzenia zadań w Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: f2dd759203655746601699f665436c78ee0758f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885492"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.jspól

Następujące pola są obsługiwane w *JobConfig.js* pliku używanym do [tworzenia zadania Azure Stream Analytics przy użyciu Visual Studio Code](quick-create-visual-studio-code.md).

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

|Nazwa|Typ|Wymagane|Wartość|
|----|----|--------|-----|
|Lokalna|ciąg|Nie|Ustawienia regionalne danych zadania usługi Stream Analytics. Wartość powinna być nazwą obsługiwanej. Wartość domyślna to "pl-US", jeśli nie została określona.|
|OutputErrorPolicy|ciąg|Nie|Wskazuje zasady, które mają być stosowane do zdarzeń, które docierają do danych wyjściowych i nie mogą być zapisywane w magazynie zewnętrznym ze względu na nieprawidłową wartość kolumny (brakujące wartości kolumn, wartości kolumn o nieprawidłowym typie lub rozmiarze). -Zatrzymaj lub upuść|
|EventsLateArrivalMaxDelayInSeconds|liczba całkowita|Nie|Maksymalne opóźnienie (w sekundach), w którym można uwzględnić zdarzenia docierające do późnego czasu. Obsługiwany zakres to-1 do 1814399 (20.23:59:59 dni) i-1 jest używany do określenia oczekiwania na czas nieokreślony. Jeśli właściwość jest nieobecna, jest interpretowana jako wartość-1.|
|EventsOutOfOrderMaxDelayInSeconds|liczba całkowita|Nie|Maksymalne opóźnienie (w sekundach), w którym można korygować zdarzenia poza kolejnością w celu przywrócenia ich wartości.|
|EventsOutOfOrderPolicy|ciąg|Nie|Wskazuje zasady, które mają być stosowane do zdarzeń, które odbierają się poza kolejnością w strumieniu zdarzeń wejściowych. -Dostosuj lub upuść|
|StreamingUnits|liczba całkowita|Tak|Określa liczbę jednostek przesyłania strumieniowego używanych przez zadanie przesyłania strumieniowego.|
|CompatibilityLevel|ciąg|Nie|Steruje pewnymi zachowaniami środowiska uruchomieniowego zadania przesyłania strumieniowego. -Akceptowalne wartości to "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|boolean|Nie|Ustaw wartość true, aby włączyć to zadanie do komunikowania się z innymi usługami platformy Azure za pomocą tożsamości zarządzanej Azure Active Directory.|
|GlobalStorage. AccountName|ciąg|Nie|Globalne konto magazynu jest używane do przechowywania zawartości powiązanej z zadaniem usługi Stream Analytics, na przykład migawek danych referencyjnych SQL.|
|GlobalStorage. AccountKey|ciąg|Nie|Odpowiedni klucz konta magazynu globalnego.|
|DataSourceCredentialDomain|ciąg|Nie|Właściwość zastrzeżona dla lokalnego magazynu poświadczeń.|
|ScriptType|ciąg|Tak|Właściwość zastrzeżona do wskazywania typu tego pliku źródłowego. Wartość akceptowalna to "JobConfig" dla JobConfig.js.|
|Tagi|Pary klucz-wartość JSON|Nie|Tagi to pary nazwa/wartość, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu dla wielu zasobów i grup zasobów. W nazwach tagów jest rozróżniana wielkość liter i w wartościach tagów jest uwzględniana wielkość liter.|

## <a name="next-steps"></a>Następne kroki

* [Utwórz zadanie Azure Stream Analytics w Visual Studio Code](quick-create-visual-studio-code.md)
* [Testowanie Stream Analytics zapytań lokalnie z przykładowymi danymi przy użyciu Visual Studio Code](visual-studio-code-local-run.md)
* [Przetestuj Stream Analytics zapytań lokalnie względem danych wejściowych strumienia na żywo za pomocą Visual Studio Code](visual-studio-code-local-run-live-input.md) 
* [Wdróż zadanie Azure Stream Analytics przy użyciu pakietu Ci/CD npm](setup-cicd-vs-code.md)