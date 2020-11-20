---
title: Analizowanie dzienników przepływu sieciowych grup zabezpieczeń platformy Azure — Z narzędzia graylog | Microsoft Docs
description: Dowiedz się, jak zarządzać i analizować dzienniki przepływu sieciowych grup zabezpieczeń na platformie Azure przy użyciu Network Watcher i Z narzędzia graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965548"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Zarządzanie i analizowanie dzienników przepływu sieciowych grup zabezpieczeń na platformie Azure przy użyciu Network Watcher i Z narzędzia graylog

[Dzienniki przepływu sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md) zawierają informacje, których można użyć do zrozumienia ruchu przychodzącego i wychodzącego IP dla interfejsów sieciowych platformy Azure. Dzienniki przepływu przedstawiają przepływy wychodzące i przychodzące dla każdej reguły sieciowej grupy zabezpieczeń, interfejsu sieciowego, do którego odnosi się przepływ, do 5 informacji o spójnej kolekcji (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) o przepływie, a także w przypadku, gdy ruch był dozwolony lub zabroniony.

W sieci można mieć wiele grup zabezpieczeń sieci z włączonym rejestrowaniem przepływu. Niektóre sieciowe grupy zabezpieczeń z włączoną obsługą rejestrowania przepływu mogą ułatwić analizowanie i uzyskiwanie szczegółowych informacji z dzienników. Ten artykuł zawiera rozwiązanie umożliwiające centralne zarządzanie tymi dziennikami przepływów sieciowych grup zabezpieczeń przy użyciu Z narzędzia graylog, narzędzia do zarządzania i analizy dzienników Open Source oraz logstash — potoku przetwarzania danych po stronie serwera typu open source.

> [!Warning]
> Poniższe kroki współpracują z dziennikami przepływów w wersji 1. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do rejestrowania przepływów dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md). Poniższe instrukcje nie będą działały z wersją 2 plików dziennika bez modyfikacji.

## <a name="scenario"></a>Scenariusz

Dzienniki przepływu sieciowych grup zabezpieczeń są włączane przy użyciu Network Watcher. Dzienniki przepływu przepływają w usłudze Azure Blob Storage. Wtyczka logstash służy do łączenia i przetwarzania dzienników przepływów z usługi BLOB Storage i wysyłania ich do Z narzędzia graylog. Po zapisaniu dzienników przepływu w programie Z narzędzia graylog można je analizować i wizualizować w dostosowanych pulpitach nawigacyjnych.

![Przepływ pracy z narzędzia graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Kroki instalacji

### <a name="enable-network-security-group-flow-logging"></a>Włącz rejestrowanie przepływu sieciowych grup zabezpieczeń

W tym scenariuszu należy włączyć rejestrowanie przepływu sieciowej grupy zabezpieczeń dla co najmniej jednej sieciowej grupy zabezpieczeń na Twoim koncie. Aby uzyskać instrukcje dotyczące włączania dzienników przepływu sieciowych grup zabezpieczeń, zapoznaj się z artykułem [wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Konfigurowanie Z narzędzia graylog

W tym przykładzie zarówno Z narzędzia graylog, jak i logstash są konfigurowane na serwerze Ubuntu 14,04, wdrożonym na platformie Azure.

- Zapoznaj się z [dokumentacją](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) z z narzędzia graylog, aby uzyskać instrukcje krok po kroku dotyczące sposobu instalowania programu na Ubuntu.
- Upewnij się, że skonfigurowano również interfejs sieci Web Z narzędzia graylog, postępując zgodnie z [dokumentacją](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

W tym przykładzie jest stosowana minimalna konfiguracja Z narzędzia graylog (tj. pojedyncze wystąpienie elementu Z narzędzia graylog), ale Z narzędzia graylog można skalować w celu skalowania między zasobami w zależności od potrzeb systemu i środowiska produkcyjnego. Aby uzyskać więcej informacji na temat zagadnień dotyczących architektury lub głębokiego przewodnika dotyczącego architektury, zobacz [dokumentację](https://docs.graylog.org/en/2.2/pages/architecture.html) i [Przewodnik po](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)architekturze z narzędzia graylog.

Z narzędzia graylog można instalować na wiele sposobów, w zależności od platformy i preferencji. Aby uzyskać pełną listę możliwych metod instalacji, zapoznaj się z oficjalną [dokumentacją](https://docs.graylog.org/en/2.2/pages/installation.html)z narzędzia graylog. Aplikacja serwera Z narzędzia graylog jest uruchamiana w dystrybucjach systemu Linux i ma następujące wymagania wstępne:

-  Java SE 8 lub nowszy — [Dokumentacja usługi Azul Azure JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Wyszukiwanie elastyczne 2. x (2.1.0 lub nowsze) — [Dokumentacja instalacji Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 lub nowsza — [Dokumentacja dotycząca instalacji MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Zainstaluj logstash

Logstash służy do spłaszczania dzienników przepływów w formacie JSON do poziomu krotki przepływu. Spłaszczenie dzienników przepływów ułatwia porządkowanie dzienników i wyszukiwanie w Z narzędzia graylog.

1. Aby zainstalować logstash, uruchom następujące polecenia:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Skonfiguruj logstash do analizowania dzienników przepływu i wyślij je do Z narzędzia graylog. Utwórz plik logstash. conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Dodaj następującą zawartość do pliku. Zmień wyróżnione wartości, aby odzwierciedlały szczegóły konta magazynu:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Dostarczony plik konfiguracyjny logstash składa się z trzech części: danych wejściowych, filtrów i danych wyjściowych. Sekcja Input określa źródło danych wejściowych dzienników, które logstash będzie przetwarzać — w tym przypadku zostanie użyty dodatek wejściowy blogu platformy Azure (zainstalowany w następnych krokach), który umożliwia dostęp do plików JSON dziennika przepływu sieciowych w usłudze BLOB Storage.

Następnie sekcja filtru spłaszcza każdy plik dziennika przepływu, tak aby każda spójna kolekcja przepływu i jej skojarzone właściwości stały się oddzielnym zdarzeniem logstash.

Na koniec sekcja Output przekazuje każde zdarzenie logstash do serwera Z narzędzia graylog. Aby dostosować się do konkretnych potrzeb, zmodyfikuj plik konfiguracji logstash zgodnie z wymaganiami.

   > [!NOTE]
   > W poprzednim pliku konfiguracyjnym założono, że serwer Z narzędzia graylog został skonfigurowany w adresie IP sprzężenia zwrotnego hosta lokalnego 127.0.0.1. Jeśli nie, upewnij się, że parametr hosta w sekcji Output został zmieniony na prawidłowy adres IP.

Dalsze instrukcje dotyczące instalowania logstash można znaleźć w [dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)logstash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Zainstaluj wtyczkę wejściową logstash dla usługi Azure Blob Storage

Wtyczka logstash umożliwia bezpośredni dostęp do dzienników przepływów z poziomu wydanych kont usługi BLOB Storage. Aby zainstalować wtyczkę, z domyślnego katalogu instalacji logstash (w tym przypadku/usr/share/logstash/bin) Uruchom następujące polecenie:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Aby uzyskać więcej informacji na temat tego dodatku, zapoznaj się z [dokumentacją](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Skonfiguruj połączenie od logstash do Z narzędzia graylog

Po nawiązaniu połączenia z dziennikami przepływów przy użyciu usługi logstash i skonfigurowania serwera Z narzędzia graylog należy skonfigurować Z narzędzia graylog w celu akceptowania przychodzących plików dziennika.

1. Przejdź do interfejsu sieci Web serwera Z narzędzia graylog przy użyciu adresu URL, który został przez Ciebie skonfigurowany. Dostęp do interfejsu można uzyskać, kierując do przeglądarki `http://<graylog-server-ip>:9000/`

2. Aby przejść do strony konfiguracji, wybierz menu rozwijane **system** na górnym pasku nawigacyjnym po prawej stronie, a następnie kliknij pozycję **dane wejściowe**.
   Alternatywnie przejdź do `http://<graylog-server-ip>:9000/system/inputs`

   ![Wprowadzenie](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Aby uruchomić nowe dane wejściowe, wybierz pozycję *GELF UDP* na liście rozwijanej **Wybierz dane wejściowe** i wypełnij formularz. GELF oznacza rozszerzony format dziennika Z narzędzia graylog. Format GELF jest opracowywany przez Z narzędzia graylog. Aby dowiedzieć się więcej na temat jego korzyści, zobacz [dokumentację](https://docs.graylog.org/en/2.2/pages/gelf.html)z narzędzia graylog.

   Upewnij się, że dane wejściowe zostały powiązane z adresem IP skonfigurowanym na serwerze Z narzędzia graylog. Adres IP powinien być zgodny z polem **host** w danych wyjściowych protokołu UDP pliku konfiguracji logstash. Domyślnym portem powinna być *12201*. Upewnij się, że port jest zgodny z polem **port** w danych wyjściowych UDP wskazanym w pliku konfiguracji logstash.

   ![Zrzut ekranu przedstawia dane wejściowe Z narzędzia graylog z opcjami uruchamiania i znajdowania danych wejściowych.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Po uruchomieniu danych wejściowych powinna zostać wyświetlona w sekcji **lokalne dane wejściowe** , jak pokazano na poniższej ilustracji:

   ![Zrzut ekranu pokazujący lokalną sekcję Inputs, która zawiera dane wejściowe, które zostały uruchomione.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Aby dowiedzieć się więcej na temat danych wejściowych komunikatów Z narzędzia graylog, zapoznaj się z [dokumentacją](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Po dokonaniu tych konfiguracji można uruchomić logstash, aby rozpocząć odczytywanie dzienników przepływu za pomocą następującego polecenia: `sudo systemctl start logstash.service` .

### <a name="search-through-graylog-messages"></a>Wyszukaj w wiadomościach Z narzędzia graylog

Po przeprowadzeniu pewnego czasu serwer Z narzędzia graylog może zbierać komunikaty, możesz przeszukiwać komunikaty. Aby sprawdzić, czy komunikaty są wysyłane do serwera Z narzędzia graylog, na stronie Konfiguracja **danych wejściowych** kliknij przycisk "**Pokaż odebrane komunikaty**" w utworzonym programie GELF UDP. Nastąpi przekierowanie do ekranu, który wygląda podobnie do poniższej ilustracji: 

![Zrzut ekranu przedstawia serwer Z narzędzia graylog, który wyświetla wynik wyszukiwania, histogram i komunikaty.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Kliknięcie niebieskiego linku "% {Message}" rozszerza każdy komunikat, aby pokazać parametry każdej kolekcji przepływu, jak pokazano na poniższej ilustracji:

![Zrzut ekranu przedstawia szczegóły komunikatu z serwera Z narzędzia graylog.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Domyślnie wszystkie pola komunikatów są uwzględniane w wyszukiwaniu, jeśli nie wybrano konkretnego pola wiadomości do wyszukania. Jeśli chcesz wyszukać określone komunikaty (tj. — przepływaj kolekcje z określonego źródłowego adresu IP) możesz użyć języka zapytań wyszukiwania Z narzędzia graylog zgodnie z [opisem](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analizowanie dzienników przepływu sieciowych grup zabezpieczeń przy użyciu Z narzędzia graylog

Teraz, po skonfigurowaniu Z narzędzia graylog, możesz użyć niektórych funkcji, aby lepiej zrozumieć dane dziennika przepływu. Taki sposób polega na użyciu pulpitów nawigacyjnych w celu utworzenia określonych widoków danych.

### <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

1. Na górnym pasku nawigacyjnym wybierz pozycję **pulpity nawigacyjne** lub przejdź do `http://<graylog-server-ip>:9000/dashboards/`

2. W tym miejscu kliknij przycisk zielony **pulpit nawigacyjny** , a następnie Wypełnij krótką formą tytuł i opis pulpitu nawigacyjnego. Naciśnij przycisk **Zapisz** , aby utworzyć nowy pulpit nawigacyjny. Zobaczysz pulpit nawigacyjny podobny do następującego:

    ![Zrzut ekranu przedstawia pulpity nawigacyjne serwera Z narzędzia graylog z opcjami tworzenia i edytowania pulpitów nawigacyjnych.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Dodaj widżety

Możesz kliknąć tytuł pulpitu nawigacyjnego, aby go zobaczyć, ale teraz jest pusty, ponieważ nie dodaliśmy żadnych elementów widget. Łatwy i przydatny widżet typu, który ma zostać dodany do pulpitu nawigacyjnego, to **szybkie wartości** wykresów, które wyświetlają listę wartości wybranego pola i ich rozkład.

1. Przejdź wstecz do wyników wyszukiwania danych wejściowych UDP, które otrzymują dzienniki przepływów, wybierając pozycję **Wyszukaj** na górnym pasku nawigacyjnym.

2. W panelu **wyników wyszukiwania** z lewej strony ekranu Znajdź kartę **pola** , która wyświetla różne pola każdego przychodzącego komunikatu krotki przepływu.

3. Wybierz dowolny żądany parametr do wizualizacji (w tym przykładzie jest wybrane źródło IP). Aby wyświetlić listę możliwych widżetów, kliknij niebieską strzałkę listy rozwijanej z lewej strony pola, a następnie wybierz pozycję **szybkie wartości** w celu wygenerowania widżetu. Powinieneś zobaczyć coś podobnego do poniższej ilustracji:

   ![Źródłowy adres IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. W tym miejscu możesz wybrać przycisk **Dodaj do pulpitu nawigacyjnego** w prawym górnym rogu widżetu i wybrać odpowiedni pulpit nawigacyjny do dodania.

5. Przejdź z powrotem do pulpitu nawigacyjnego, aby zobaczyć widżet, który właśnie został dodany.

   Możesz dodać różne inne widżety, takie jak histogramy i liczby do pulpitu nawigacyjnego, aby śledzić ważne metryki, takie jak przykładowy pulpit nawigacyjny, widoczny na poniższej ilustracji:

   ![Pulpit nawigacyjny Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Aby uzyskać więcej informacji na temat pulpitów nawigacyjnych i innych typów widżetów, zapoznaj się z [dokumentacją](https://docs.graylog.org/en/2.2/pages/dashboards.html)z narzędzia graylog.

Dzięki integracji Network Watcher z usługą Z narzędzia graylog masz teraz wygodny i scentralizowany sposób zarządzania i wizualizacji dzienników przepływu sieciowych grup zabezpieczeń. Z narzędzia graylog ma wiele innych zaawansowanych funkcji, takich jak strumienie i alerty, które mogą być również używane do dalszej obsługi dzienników przepływów i lepszego zrozumienia ruchu sieciowego. Teraz, gdy masz już Z narzędzia graylog skonfigurowany i połączony z platformą Azure, możesz dalej korzystać z innych funkcji oferowanych przez tę usługę.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dzienniki przepływu sieciowych grup zabezpieczeń przy użyciu Power BI, odwiedzając [wizualizacje sieciowej grupy zabezpieczeń przepływy dzienniki z Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).