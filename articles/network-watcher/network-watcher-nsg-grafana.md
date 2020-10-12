---
title: Zarządzanie dziennikami przepływu sieciowej grupy zabezpieczeń za pomocą Grafana
titleSuffix: Azure Network Watcher
description: Zarządzanie i analizowanie dzienników przepływu sieciowych grup zabezpieczeń na platformie Azure przy użyciu Network Watcher i Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: 5ec4b9343efab9ae501b2b3dcc606e6e3f5fe826
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399690"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Zarządzanie dziennikami przepływu sieciowych grup zabezpieczeń i analizowanie ich przy użyciu Network Watcher i Grafana

[Dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](network-watcher-nsg-flow-logging-overview.md) zawierają informacje, które mogą służyć do zrozumienia ruchu przychodzącego i wychodzącego IP w interfejsach sieciowych. Te dzienniki przepływu przedstawiają przepływy wychodzące i przychodzące dla każdej reguły sieciowej grupy zabezpieczeń, karta sieciowa przepływu odnosi się do, 5-informacje o spójnej kolekcji przepływu (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub zabroniony.

W sieci można mieć wiele sieciowych grup zabezpieczeń z włączonym rejestrowaniem przepływu. Ta ilość danych rejestrowania ułatwia analizowanie i uzyskiwanie szczegółowych informacji z dzienników. Ten artykuł zawiera rozwiązanie umożliwiające centralne zarządzanie tymi dziennikami przepływu sieciowej grupy zabezpieczeń za pomocą Grafana, narzędzia do tworzenia wykresów typu open source, ElasticSearch, rozproszonego aparatu wyszukiwania i analizy oraz logstash, który jest potokiem przetwarzania danych po stronie serwera typu open source.  

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu sieciowej grupy zabezpieczeń są włączane przy użyciu Network Watcher i są przechowywane w usłudze Azure Blob Storage. Wtyczka logstash służy do łączenia i przetwarzania dzienników przepływów z usługi BLOB Storage i wysyłania ich do ElasticSearch.  Po zapisaniu dzienników przepływu w programie ElasticSearch można je analizować i wizualizować w dostosowanych pulpitach nawigacyjnych w Grafana.

![SIECIOWEJ grupy zabezpieczeń Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Kroki instalacji

### <a name="enable-network-security-group-flow-logging"></a>Włącz rejestrowanie przepływu sieciowych grup zabezpieczeń

W tym scenariuszu należy włączyć rejestrowanie przepływu sieciowej grupy zabezpieczeń dla co najmniej jednej sieciowej grupy zabezpieczeń na Twoim koncie. Instrukcje dotyczące włączania dzienników przepływu zabezpieczeń sieci znajdują się w następującym artykule [wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Zagadnienia dotyczące instalacji

W tym przykładzie Grafana, ElasticSearch i logstash są skonfigurowane na serwerze Ubuntu 16,04 LTS wdrożonym na platformie Azure. Ta minimalna konfiguracja jest używana do uruchamiania wszystkich trzech składników — wszystkie są uruchomione na tej samej maszynie wirtualnej. Tej konfiguracji należy używać tylko do testowania i obciążeń niekrytycznych. Logstash, Elasticsearch i Grafana mogą być skalowane do niezależnego skalowania w wielu wystąpieniach. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dla każdego z tych składników.

### <a name="install-logstash"></a>Zainstaluj logstash

Za pomocą logstash można spłaszczyć dzienniki przepływów w formacie JSON do poziomu krotki przepływu.

1. Aby zainstalować logstash, uruchom następujące polecenia:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Skonfiguruj logstash do analizowania dzienników przepływu i wyślij je do ElasticSearch. Utwórz plik logstash. conf przy użyciu:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Dodaj następującą zawartość do pliku. Zmień nazwę konta magazynu i klucz dostępu, aby odzwierciedlić szczegóły konta magazynu:

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

Dostarczony plik konfiguracyjny logstash składa się z trzech części: danych wejściowych, filtrów i danych wyjściowych.
Sekcja Input określa źródło danych wejściowych dzienników, które logstash będzie przetwarzać — w tym przypadku będziemy używać wtyczki wejściowej "azureblob" (zainstalowanej w następnych krokach), która umożliwi nam dostęp do plików JSON dziennika przepływu sieciowej grupy zabezpieczeń przechowywanych w usłudze BLOB Storage. 

Następnie sekcja filtru spłaszcza każdy plik dziennika przepływu, tak aby każda spójna kolekcja przepływu i jej skojarzone właściwości stały się oddzielnym zdarzeniem logstash.

Na koniec sekcja Output przekazuje każde zdarzenie logstash do serwera ElasticSearch. Zmodyfikuj plik konfiguracji logstash, aby odpowiadał Twoim konkretnym potrzebom.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Zainstaluj wtyczkę wejściową logstash dla usługi Azure Blob Storage

Ta wtyczka logstash umożliwia bezpośredni dostęp do dzienników przepływu ze wskazanych kont usługi BLOB Storage. Aby zainstalować tę wtyczkę, z domyślnego katalogu instalacji logstash (w tym przypadku/usr/share/logstash/bin) Uruchom polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tego dodatku, zobacz [dodatek plug-in logstash Input dla obiektów BLOB usługi Azure Storage](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Zainstaluj ElasticSearch

Aby zainstalować ElasticSearch, można użyć następującego skryptu. Aby uzyskać informacje o instalowaniu ElasticSearch, zobacz [elastyczny stos](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Zainstaluj Grafana

Aby zainstalować i uruchomić Grafana, uruchom następujące polecenia:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Dodatkowe informacje o instalacji można znaleźć [w temacie Installing in Debian/Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Dodawanie serwera ElasticSearch jako źródła danych

Następnie należy dodać indeks ElasticSearch zawierający dzienniki przepływu jako źródło danych. Możesz dodać źródło danych, wybierając pozycję **Dodaj źródło danych** i wypełniając formularz odpowiednimi informacjami. Przykładową konfigurację można znaleźć na poniższym zrzucie ekranu:

![Dodawanie źródła danych](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Teraz, po pomyślnym skonfigurowaniu Grafana do odczytu z indeksu ElasticSearch zawierającego dzienniki przepływów sieciowej grupy zabezpieczeń, można tworzyć i personalizować pulpity nawigacyjne. Aby utworzyć nowy pulpit nawigacyjny, wybierz pozycję **Utwórz swój pierwszy pulpit nawigacyjny**. Poniższa przykładowa konfiguracja grafu przedstawia przepływy segmentu według reguły sieciowej grupy zabezpieczeń:

![Wykres pulpitu nawigacyjnego](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Poniższy zrzut ekranu przedstawia wykres i wykres przedstawiający najlepsze przepływy i ich częstotliwość. Przepływy są również wyświetlane przez regułę sieciowej grupy zabezpieczeń i przepływy według decyzji. Grafana jest wysoce dostosowywalny, dlatego zaleca się tworzenie pulpitów nawigacyjnych zgodnie z określonymi potrzebami monitorowania. Poniższy przykład przedstawia typowy pulpit nawigacyjny:

![Zrzut ekranu przedstawiający przykładową konfigurację grafu z przepływami, które są dzielone przez regułę sieciowej grupy zabezpieczeń.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Podsumowanie

Integrując Network Watcher z ElasticSearch i Grafana, masz teraz wygodny i scentralizowany sposób zarządzania i wizualizacji dzienników przepływów sieciowej grupy zabezpieczeń oraz innych danych. Grafana ma wiele innych zaawansowanych funkcji grafowania, które mogą również służyć do dalszej obsługi dzienników przepływów i lepszego zrozumienia ruchu sieciowego. Teraz, gdy masz już Grafana wystąpienie i połączono z platformą Azure, możesz nadal poznać inne funkcje, które oferuje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o korzystaniu z [Network Watcher](network-watcher-monitoring-overview.md).

