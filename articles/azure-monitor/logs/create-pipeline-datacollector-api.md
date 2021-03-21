---
title: Tworzenie potoku danych przy użyciu interfejsu API modułu zbierającego dane
description: Korzystając z Azure Monitor interfejsu API modułu zbierającego dane HTTP, można dodać dane po JSON do obszaru roboczego Log Analytics z dowolnego klienta, który może wywołać interfejs API REST. W tym artykule opisano sposób przekazywania danych przechowywanych w plikach w zautomatyzowany sposób.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ab2e9c691f17b8f0891ecbc82ff42cd3529a1328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031195"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Tworzenie potoku danych za pomocą interfejsu API modułu zbierającego dane

[Interfejs API modułu zbierającego dane Azure monitor](data-collector-api.md) umożliwia importowanie wszelkich niestandardowych danych dzienników do log Analytics obszaru roboczego w Azure monitor. Jedyne wymagania polegają na tym, że dane są sformatowane w formacie JSON i dzielone na 30 MB lub mniej segmentów. Jest to całkowicie elastyczny mechanizm, który można podłączyć na wiele sposobów: dane wysyłane bezpośrednio z aplikacji do jednej z przeciążeń ad hoc. W tym artykule przedstawiono konspekt niektórych punktów początkowych dla typowego scenariusza: konieczność regularnego przekazywania danych przechowywanych w plikach. Gdy potok przedstawiony w tym miejscu nie będzie najbardziej wydajny lub w inny sposób zoptymalizowany, ma służyć jako punkt wyjścia do tworzenia potoku produkcyjnego.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Przykładowy problem
W pozostałej części tego artykułu sprawdzimy dane widoku strony w Application Insights. W naszym hipotetycznym scenariuszu chcemy skorelować informacje geograficzne zbierane domyślnie przez zestaw SDK Application Insights, aby uzyskać niestandardowe dane zawierające populację każdego kraju/regionu na świecie, z celem ustalenia, gdzie należy wystawić większość dolarów marketingowych. 

W tym celu używamy publicznego źródła danych, takiego jak w przypadku w tym celu [prospektów dla Nieświatowej populacji](https://esa.un.org/unpd/wpp/) . Dane będą miały następujący prosty schemat:

![Przykładowy schemat prosty](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

W naszym przykładzie przyjęto założenie, że przekażemy nowy plik z najnowszymi danymi roku, gdy tylko staną się dostępne.

## <a name="general-design"></a>Projekt ogólny
Korzystamy z klasycznej logiki typu ETL do projektowania naszego potoku. Architektura będzie wyglądać następująco:

![Architektura potoku zbierania danych](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

W tym artykule nie opisano sposobu tworzenia danych ani [przekazywania ich do konta usługi Azure Blob Storage](../../storage/blobs/storage-upload-process-images.md). Zamiast tego wybieramy przepływ aż do momentu przekazania nowego pliku do obiektu BLOB. Z tego miejsca:

1. Proces wykryje, że nowe dane zostały przekazane.  Nasz przykład używa [aplikacji logiki platformy Azure](../../logic-apps/logic-apps-overview.md), która ma wyzwalacz do wykrywania nowych danych przekazywanych do obiektu BLOB.

2. Procesor odczytuje te nowe dane i konwertuje je na format JSON, w tym przykładzie w formacie wymaganym przez Azure Monitor jest używana [Funkcja platformy Azure](../../azure-functions/functions-overview.md) jako lekki i ekonomiczny sposób wykonywania kodu przetwarzania. Funkcja jest uruchamiana przez tę samą aplikację logiki, która została użyta do wykrycia nowych danych.

3. Na koniec, gdy obiekt JSON jest dostępny, jest wysyłany do Azure Monitor. Ta sama aplikacja logiki wysyła dane do Azure Monitor przy użyciu wbudowanego Log Analytics działania modułu zbierającego dane.

Podczas szczegółowej konfiguracji magazynu obiektów blob, aplikacji logiki lub funkcji platformy Azure nie ma w tym artykule szczegółowe instrukcje są dostępne na stronach określonych produktów.

Aby monitorować ten potok, użyjemy Application Insights do monitorowania szczegółowych informacji o usłudze Azure Functions w [tym miejscu](../../azure-functions/functions-monitoring.md)i Azure monitor monitorowania w [tym miejscu szczegółów](../../logic-apps/monitor-logic-apps-log-analytics.md)aplikacji logiki. 

## <a name="setting-up-the-pipeline"></a>Konfigurowanie potoku
Aby ustawić potok, najpierw upewnij się, że utworzono i skonfigurowano kontener obiektów BLOB. Podobnie upewnij się, że utworzono obszar roboczy Log Analytics, do którego chcesz wysłać dane.

## <a name="ingesting-json-data"></a>Pozyskiwanie danych JSON
Pozyskiwanie danych JSON jest proste w Logic Apps i ponieważ nie ma potrzeby transformacji, możemy obejmować cały potok w pojedynczej aplikacji logiki. Po skonfigurowaniu zarówno kontenera obiektów blob, jak i obszaru roboczego Log Analytics Utwórz nową aplikację logiki i skonfiguruj ją w następujący sposób:

![Przykład przepływu pracy aplikacji logiki](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Zapisz aplikację logiki i przetestuj ją.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Pozyskiwanie plików XML, CSV lub innych formatów danych
Logic Apps dzisiaj nie ma wbudowanych funkcji umożliwiających łatwe przekształcanie plików XML, CSV i innych typów w formacie JSON. W związku z tym musimy użyć innego środka do ukończenia tej transformacji. W tym artykule używamy możliwości obliczeniowych bezserwerowych Azure Functions jako bardzo lekki i przyjazny dla tego sposób. 

W tym przykładzie analizujemy plik CSV, ale każdy inny typ pliku może być przetworzony w podobny sposób. Wystarczy zmodyfikować deserializacji części funkcji platformy Azure w celu odzwierciedlenia odpowiedniej logiki dla określonego typu danych.

1.  Tworzenie nowej funkcji platformy Azure przy użyciu środowiska uruchomieniowego w wersji 1 i opartej na wykorzystaniu po wyświetleniu monitu.  Wybierz szablon **wyzwalacza http** przeznaczony dla języka C# jako punkt początkowy, który konfiguruje powiązania zgodnie z potrzebami. 
2.  Na karcie **Wyświetlanie plików** w okienku po prawej stronie Utwórz nowy plik o nazwie **project.jsna** i wklej następujący kod z pakietów NuGet, z których korzystamy:

    ![Przykładowy projekt Azure Functions](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Przejdź do opcji **Run. CSX** w okienku po prawej stronie i Zastąp kod domyślny poniższym. 

    >[!NOTE]
    >W przypadku projektu należy zastąpić model rekordu (klasę "PopulationRecord") własnym schematem danych.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Zapisz funkcję.
5. Przetestuj funkcję, aby upewnić się, że kod działa poprawnie. Przejdź do karty **test** w okienku po prawej stronie, aby skonfigurować test w następujący sposób. Umieść link do obiektu BLOB z przykładowymi danymi do pola tekstowego **treści żądania** . Po kliknięciu przycisku **Uruchom** w polu **dane wyjściowe** powinny pojawić się dane wyjściowe JSON:

    ![Kod testu aplikacji funkcji](./media/create-pipeline-datacollector-api/functions-test-01.png)

Teraz musimy wrócić i zmodyfikować aplikację logiki rozpoczętą wcześniej, aby uwzględnić dane pozyskiwane i konwertowane na format JSON.  Za pomocą projektanta widoków skonfiguruj następujące polecenie, a następnie Zapisz swoją aplikację logiki:

![Przykład kończenia przepływu pracy Logic Apps](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testowanie potoku
Teraz można przekazać nowy plik do obiektu BLOB skonfigurowanego wcześniej i monitorować go przez aplikację logiki. Wkrótce powinno być widoczne nowe wystąpienie aplikacji logiki, wywołać funkcję platformy Azure, a następnie pomyślnie wysłać dane do Azure Monitor. 

>[!NOTE]
>Wyświetlenie danych w Azure Monitor podczas pierwszego wysyłania nowego typu danych może potrwać do 30 minut.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korelacja z innymi danymi w Log Analytics i Application Insights
Aby dokończyć cel skorelowania Application Insights danych widoku strony z danymi populacji pozyskanymi z naszego niestandardowego źródła danych, uruchom następujące zapytanie w oknie Application Insights Analytics lub Log Analytics obszarze roboczym:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Dane wyjściowe powinny zawierać dwa źródła danych, które są teraz dołączone.  

![Przykład skorelowania odłączonych danych w wynikach wyszukiwania](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Sugerowane ulepszenia dla potoku produkcyjnego
W tym artykule przedstawiono roboczy prototyp, w odniesieniu do którego można zastosować do rzeczywistego rozwiązania z jakością produkcyjną. W przypadku takich rozwiązań dotyczących jakości produkcji zalecane są następujące udoskonalenia:

* Dodaj obsługę błędów i logikę ponowień w aplikacji logiki i funkcji.
* Dodaj logikę, aby upewnić się, że limit wywołań interfejsu API pozyskiwania 30MB/jednego Log Analytics nie zostanie przekroczony. W razie konieczności Podziel dane na mniejsze segmenty.
* Skonfiguruj zasady oczyszczania w magazynie obiektów BLOB. Po pomyślnym wysłaniu do obszaru roboczego Log Analytics, chyba że chcesz zachować dostęp do danych pierwotnych w celach archiwalnych, nie ma powodów, aby kontynuować ich przechowywanie. 
* Sprawdź, czy monitorowanie jest włączone w pełnym potoku, dodając punkty śledzenia i alerty odpowiednio do potrzeb.
* Skorzystaj z kontroli źródła, aby zarządzać kodem funkcji i aplikacji logiki.
* Upewnij się, że są spełnione odpowiednie zasady zarządzania zmianami, takie jak w przypadku zmiany schematu, funkcja i Logic Apps są odpowiednio modyfikowane.
* W przypadku przekazywania wielu różnych typów danych należy podzielić je na poszczególne foldery w kontenerze obiektów blob i utworzyć logikę w celu rozróżnienia logiki na podstawie typu danych. 


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat  [interfejsu API modułu zbierającego dane](data-collector-api.md) , aby zapisać dane do log Analytics obszaru roboczego z dowolnego klienta interfejsu API REST.
