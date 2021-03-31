---
title: Wizualizacja dzienników przepływu sieciowej grupy zabezpieczeń — elastyczny stos
titleSuffix: Azure Network Watcher
description: Zarządzanie i analizowanie dzienników przepływu sieciowych grup zabezpieczeń na platformie Azure przy użyciu Network Watcher i elastycznego stosu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: aca8c75f262e472cbc770c052b86d6e760ee449a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026475"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Wizualizowanie dzienników przepływu sieciowych grup zabezpieczeń usługi Azure Network Watcher przy użyciu narzędzi open source

Dzienniki przepływu sieciowych grup zabezpieczeń zawierają informacje, które mogą być używane do obsługi ruchu przychodzącego i wychodzącego IP w sieciowych grupach zabezpieczeń. Te dzienniki przepływu pokazują przepływy wychodzące i przychodzące dla każdej reguły, karta sieciowa przepływu ma zastosowanie do, 5 informacji o kolekcji przepływu (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub zabroniony.

Te dzienniki przepływu mogą być trudne do ręcznego analizowania i uzyskiwania szczegółowych informacji z programu. Istnieje jednak kilka narzędzi open source, które mogą ułatwić wizualizację tych danych. W tym artykule podano rozwiązanie do wizualizacji tych dzienników przy użyciu elastycznego stosu, co umożliwi szybkie indeksowanie i wizualizację dzienników przepływów na pulpicie nawigacyjnym Kibana.

## <a name="scenario"></a>Scenariusz

W tym artykule skonfigurujemy rozwiązanie, które umożliwi wizualizację dzienników przepływu sieciowych grup zabezpieczeń przy użyciu elastycznego stosu.  Wtyczka wejściowa logstash uzyska dzienniki przepływów bezpośrednio z obiektu blob magazynu skonfigurowanego dla zawierającego dzienniki przepływów. Następnie przy użyciu elastycznego stosu dzienniki przepływu będą indeksowane i używane do tworzenia pulpitu nawigacyjnego Kibana w celu wizualizacji informacji.

![Diagram przedstawia scenariusz, który umożliwia wizualizację dzienników przepływu sieciowych grup zabezpieczeń przy użyciu elastycznego stosu.][scenario]

## <a name="steps"></a>Kroki

### <a name="enable-network-security-group-flow-logging"></a>Włącz rejestrowanie przepływu sieciowych grup zabezpieczeń
W tym scenariuszu należy włączyć rejestrowanie przepływu sieciowej grupy zabezpieczeń dla co najmniej jednej sieciowej grupy zabezpieczeń na Twoim koncie. Instrukcje dotyczące włączania dzienników przepływu zabezpieczeń sieci znajdują się w następującym artykule [wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Konfigurowanie elastycznego stosu
Łącząc dzienniki przepływu sieciowej grupy zabezpieczeń z elastycznym stosem, możemy utworzyć pulpit nawigacyjny Kibana, co umożliwia nam wyszukiwanie, Graph, analizowanie i uzyskiwanie szczegółowych informacji z naszych dzienników.

#### <a name="install-elasticsearch"></a>Zainstaluj Elasticsearch

1. Stos elastyczny z wersji 5,0 i nowszej wymaga języka Java 8. Uruchom polecenie, `java -version` Aby sprawdzić wersję. Jeśli nie masz zainstalowanego języka Java, zapoznaj się z dokumentacją na [platformie Azure-Suppored zestawy JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support).
2. Pobierz poprawny pakiet binarny dla Twojego systemu:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Inne metody instalacji można znaleźć w [instalacji Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Sprawdź, czy Elasticsearch działa z poleceniem:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Powinna zostać wyświetlona odpowiedź podobna do tej:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Aby uzyskać więcej informacji na temat instalowania wyszukiwania elastycznego, zapoznaj się z [instrukcjami instalacji](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Zainstaluj logstash

1. Aby zainstalować logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Następnie musimy skonfigurować logstash, aby uzyskać dostęp i analizować dzienniki przepływów. Utwórz plik logstash. conf przy użyciu:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Dodaj następującą zawartość do pliku:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Dalsze instrukcje dotyczące instalowania logstash można znaleźć w [oficjalnej dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Zainstaluj wtyczkę wejściową logstash dla usługi Azure Blob Storage

Ta wtyczka logstash umożliwia bezpośredni dostęp do dzienników przepływu ze wskazanych kont magazynu. Aby zainstalować tę wtyczkę, z domyślnego katalogu instalacji logstash (w tym przypadku/usr/share/logstash/bin) Uruchom polecenie:

```bash
logstash-plugin install logstash-input-azureblob
```

Aby rozpocząć logstash, uruchom polecenie:

```bash
sudo /etc/init.d/logstash start
```

Aby uzyskać więcej informacji na temat tej wtyczki, zapoznaj się z [dokumentacją](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalowanie narzędzia Kibana

1. Uruchom następujące polecenia, aby zainstalować Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Aby uruchomić Kibana, Użyj poleceń:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Aby wyświetlić interfejs sieci Web Kibana, przejdź do `http://localhost:5601`
4. W tym scenariuszu wzorzec indeksu używany do rejestrowania przepływu to "sieciowej grupy zabezpieczeń-Flow-Logs". Wzorzec indeksu można zmienić w sekcji "output" pliku logstash. conf.
5. Jeśli chcesz zdalnie wyświetlić pulpit nawigacyjny Kibana, Utwórz regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego zezwalającą na dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Kibana

Przykładowy pulpit nawigacyjny do wyświetlania trendów i szczegółów w alertach przedstawiono na poniższej ilustracji:

![rysunek 1][1]

Pobierz [plik pulpitu nawigacyjnego](https://aka.ms/networkwatchernsgflowlogdashboard), [plik wizualizacji](https://aka.ms/networkwatchernsgflowlogvisualizations)i [zapisany plik wyszukiwania](https://aka.ms/networkwatchernsgflowlogsearch).

Na karcie **Zarządzanie** w programie Kibana przejdź do pozycji **zapisane obiekty** i zaimportuj wszystkie trzy pliki. Następnie na karcie **pulpit nawigacyjny** możesz otworzyć i załadować przykładowy pulpit nawigacyjny.

Możesz również tworzyć własne wizualizacje i pulpity nawigacyjne dostosowane do metryk własnych zainteresowań. Dowiedz się więcej na temat tworzenia wizualizacji Kibana z [oficjalnej dokumentacji](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)programu Kibana.

### <a name="visualize-nsg-flow-logs"></a>Wizualizacja dzienników przepływu sieciowej grupy zabezpieczeń

Przykładowy pulpit nawigacyjny zawiera kilka wizualizacji dzienników przepływów:

1. Przepływy według decyzji/kierunku na wykresach szeregów czasowych przedstawiające liczbę przepływów w danym okresie. Można edytować jednostkę czasu i zakres obu tych wizualizacji. Przepływy według decyzji przedstawiają liczbę podjętych decyzji dotyczących zezwalania lub odmowy, podczas gdy przepływy według kierunku pokazują udział ruchu przychodzącego i wychodzącego. Te wizualizacje umożliwiają Badanie trendów ruchu w czasie i wyszukiwanie wszelkich skoków lub nietypowych wzorców.

   ![Zrzut ekranu przedstawia przykładowy pulpit nawigacyjny z przepływami według decyzji i kierunku w czasie.][2]

2. Przepływy według portów docelowy/źródłowy — wykresy kołowe pokazujące podział przepływów na odpowiednie porty. Dzięki temu widokowi można zobaczyć najczęściej używane porty. Jeśli klikniesz określony port na wykresie kołowym, pozostała część pulpitu nawigacyjnego zostanie odfiltrowana do przepływów tego portu.

   ![Zrzut ekranu przedstawia przykładowy pulpit nawigacyjny z przepływami według portów docelowych i źródłowych.][3]

3. Liczba przepływów i Najwcześniejszy czas dziennika — metryki pokazujące liczbę zarejestrowanych przepływów oraz datę najwcześniejszego przechwycenia dziennika.

   ![Zrzut ekranu przedstawia przykładowy pulpit nawigacyjny o liczbie przepływów i najwcześniejszym czasie logowania.][4]

4. Przepływy według sieciowej grupy zabezpieczeń i reguły — wykres słupkowy przedstawiający rozkład przepływów w poszczególnych sieciowej grupy ZABEZPIECZEŃach, a także rozkład reguł w ramach każdego sieciowej grupy ZABEZPIECZEŃu. W tym miejscu możesz zobaczyć, które sieciowej grupy zabezpieczeń i reguły wygenerowały najwięcej ruchu.

   ![Zrzut ekranu przedstawia przykładowy pulpit nawigacyjny z przepływami według N S G i zasady.][5]

5. 10 pierwszych źródłowych i docelowych adresów IP — wykresy słupkowe pokazujące 10 źródłowych i docelowych adresów IP. Można dostosować te wykresy, aby pokazać więcej lub mniej najpopularniejszych adresów IP. W tym miejscu można zobaczyć najczęściej występujące adresy IP, a także decyzję o ruchu (Zezwalaj lub Odmów) na każdym z adresów IP.

   ![Zrzut ekranu przedstawia przykładowy pulpit nawigacyjny z przepływami według dziesięciu pierwszych adresów źródłowych i docelowych i P.][6]

6. Kolekcje przepływów — w tej tabeli przedstawiono informacje zawarte w każdej kolekcji przepływów, a także odpowiadające jej przyrządy i zasady.

   ![Zrzut ekranu przedstawia kolekcje przepływu w tabeli.][7]

Korzystając z paska zapytania w górnej części pulpitu nawigacyjnego, można filtrować pulpit nawigacyjny na podstawie dowolnego parametru przepływów, takich jak identyfikator subskrypcji, grupy zasobów, reguła lub jakakolwiek inna interesująca zmienna. Aby uzyskać więcej informacji o zapytaniach i filtrach Kibana, zapoznaj się z [oficjalną dokumentacją](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Podsumowanie

Łącząc dzienniki przepływu sieciowych grup zabezpieczeń z elastycznym stosem, mamy zaawansowaną i dostosowywalną metodę wizualizacji ruchu w sieci. Te pulpity nawigacyjne umożliwiają szybkie uzyskiwanie i udostępnianie szczegółowych informacji o ruchu sieciowym, a także filtrowanie i badanie wszelkich potencjalnych anomalii. Za pomocą Kibana można dostosować te pulpity nawigacyjne i utworzyć konkretne wizualizacje w celu spełnienia wszelkich potrzeb związanych z zabezpieczeniami, inspekcją i zgodnością.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą Power BI, odwiedzając [wizualizacje sieciowej grupy zabezpieczeń przepływy dzienników z Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png