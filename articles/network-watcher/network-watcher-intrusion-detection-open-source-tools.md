---
title: Wykonywanie wykrywania nieautoryzowanych sieci za pomocą narzędzi open source
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób korzystania z usługi Azure Network Watcher i narzędzi open source w celu wykrywania nieautoryzowanych sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 6a7b4d8c3d2e2b33d8e2a9936670992b1c922b6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84737363"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Wykrywanie intruzów w sieci za pomocą Network Watcher i narzędzi open source

Przechwytywanie pakietów to kluczowy składnik służący do implementowania systemów wykrywania dostępu do sieci (identyfikatorów) i wykonywania monitorowania zabezpieczeń sieci (NSM). Istnieje kilka narzędzi identyfikatorów Open Source, które przetwarzają przechwycenia pakietów i szukają podpisów możliwych intruzów sieci i złośliwych działań. Za pomocą przechwytywania pakietów dostarczonych przez Network Watcher można analizować sieć pod kątem wszelkich szkodliwych intruzów lub luk w zabezpieczeniach.

Jednym z takich narzędzi typu "open source" jest suricata, ponieważ aparat identyfikatorów używa zestawów reguł do monitorowania ruchu sieciowego i wyzwalania alertów po wystąpieniu podejrzanych zdarzeń. Suricata oferuje aparat wielowątkowy, co oznacza, że może przeprowadzać analizę ruchu sieciowego dzięki zwiększonej szybkości i wydajności. Aby uzyskać więcej informacji na temat Suricata i jego możliwości, odwiedź witrynę sieci Web pod adresem https://suricata-ids.org/ .

## <a name="scenario"></a>Scenariusz

W tym artykule wyjaśniono, jak skonfigurować środowisko do wykrywania wtargnięcia sieci przy użyciu Network Watcher, Suricata i elastycznego stosu. Network Watcher udostępnia przechwycenia pakietów służące do przeprowadzania wykrywania nieautoryzowanych sieci. Suricata przetwarza przechwycenia pakietu i wyzwala alerty w oparciu o pakiety zgodne z danym zestawem zagrożeń. Te alerty są przechowywane w pliku dziennika na komputerze lokalnym. Korzystając z elastycznego stosu, dzienniki wygenerowane przez Suricata mogą być indeksowane i używane do tworzenia pulpitu nawigacyjnego Kibana, zapewniając wizualizację wizualną dzienników i metodę szybkiego uzyskiwania wglądu w potencjalne luki w zabezpieczeniach sieci.  

![prosty scenariusz aplikacji sieci Web][1]

Oba narzędzia Open Source można skonfigurować na maszynie wirtualnej platformy Azure, co pozwala na przeprowadzanie tej analizy w ramach własnego środowiska sieciowego platformy Azure.

## <a name="steps"></a>Kroki

### <a name="install-suricata"></a>Zainstaluj Suricata

W przypadku wszystkich innych metod instalacji odwiedź witrynę https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. W terminalu wiersza polecenia maszyny wirtualnej Uruchom następujące polecenia:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Aby sprawdzić poprawność instalacji, uruchom polecenie, `suricata -h` Aby wyświetlić pełną listę poleceń.

### <a name="download-the-emerging-threats-ruleset"></a>Pobierz zestaw reguł zagrożeń

Na tym etapie nie mamy żadnych reguł do uruchomienia Suricata. Możesz utworzyć własne reguły, jeśli istnieją określone zagrożenia dla sieci, które mają być wykrywane, lub można również użyć utworzonych zestawów reguł z wielu dostawców, takich jak nowe zagrożenia lub VRT reguły z snort. Korzystamy z dostępnego w tym miejscu zestawu nowych zagrożeń:

Pobierz zestaw reguł i skopiuj go do katalogu:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Przetwarzaj przechwycenia pakietu za pomocą Suricata

Aby przetworzyć przechwycone pakiety przy użyciu Suricata, uruchom następujące polecenie:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Aby sprawdzić wyniki alertów, zapoznaj się z plikiem Fast. log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Konfigurowanie elastycznego stosu

Chociaż dzienniki wytwarzane przez program Suricata zawierają cenne informacje o tym, co się dzieje w naszej sieci, te pliki dzienników nie są najłatwiej czytać i zrozumieć. Łącząc Suricata z elastycznym stosem, możemy utworzyć pulpit nawigacyjny Kibana, co umożliwia nam wyszukiwanie, Graph, analizowanie i uzyskiwanie szczegółowych informacji z naszych dzienników.

#### <a name="install-elasticsearch"></a>Zainstaluj Elasticsearch

1. Stos elastyczny z wersji 5,0 i nowszej wymaga języka Java 8. Uruchom polecenie, `java -version` Aby sprawdzić wersję. Jeśli nie masz zainstalowanego języka Java, zapoznaj się z dokumentacją na [platformie Azure-Suppored zestawy JDK](https://aka.ms/azure-jdks).

1. Pobierz poprawny pakiet binarny dla Twojego systemu:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Inne metody instalacji można znaleźć w [instalacji Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Sprawdź, czy Elasticsearch działa z poleceniem:

    ```
    curl http://127.0.0.1:9200
    ```

    Powinna zostać wyświetlona odpowiedź podobna do tej:

    ```
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

Dalsze instrukcje dotyczące instalowania wyszukiwania elastycznego można znaleźć w [instalacji](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html) strony.

### <a name="install-logstash"></a>Zainstaluj logstash

1. Aby zainstalować logstash, uruchom następujące polecenia:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Następnie należy skonfigurować logstash do odczytu z danych wyjściowych eve.jspliku. Utwórz plik logstash. conf przy użyciu:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Dodaj następującą zawartość do pliku (Upewnij się, że ścieżka do eve.jsw pliku jest poprawna):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Upewnij się, że nadajesz odpowiednie uprawnienia do eve.jsw pliku, aby logstash mógł pozyskiwać plik.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Aby rozpocząć logstash, uruchom polecenie:

    ```
    sudo /etc/init.d/logstash start
    ```

Dalsze instrukcje dotyczące instalowania logstash można znaleźć w [oficjalnej dokumentacji](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) .

### <a name="install-kibana"></a>Instalowanie narzędzia Kibana

1. Uruchom następujące polecenia, aby zainstalować Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Aby uruchomić Kibana, Użyj poleceń:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Aby wyświetlić interfejs sieci Web Kibana, przejdź do `http://localhost:5601`
1. W tym scenariuszu wzorzec indeksu używany dla dzienników Suricata ma wartość "logstash-*"

1. Jeśli chcesz zdalnie wyświetlić pulpit nawigacyjny Kibana, Utwórz regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego zezwalającą na dostęp do **portu 5601**.

### <a name="create-a-kibana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Kibana

W tym artykule udostępniono przykładowy pulpit nawigacyjny do wyświetlania trendów i szczegółów w alertach.

1. Pobierz tutaj plik pulpitu [nawigacyjnego, plik](https://aka.ms/networkwatchersuricatadashboard)wizualizacji [tutaj](https://aka.ms/networkwatchersuricatavisualization)i zapisany plik wyszukiwania [tutaj](https://aka.ms/networkwatchersuricatasavedsearch).

1. Na karcie **Zarządzanie** w programie Kibana przejdź do pozycji **zapisane obiekty** i zaimportuj wszystkie trzy pliki. Następnie na karcie **pulpit nawigacyjny** możesz otworzyć i załadować przykładowy pulpit nawigacyjny.

Możesz również tworzyć własne wizualizacje i pulpity nawigacyjne dostosowane do metryk własnych zainteresowań. Dowiedz się więcej na temat tworzenia wizualizacji Kibana z [oficjalnej dokumentacji](https://www.elastic.co/guide/en/kibana/current/visualize.html)programu Kibana.

![Pulpit nawigacyjny Kibana][2]

### <a name="visualize-ids-alert-logs"></a>Wizualizuj identyfikatory dzienników alertów

Przykładowy pulpit nawigacyjny zawiera kilka wizualizacji dzienników alertów Suricata:

1. Alerty według GeoIP — Mapa przedstawiająca dystrybucję alertów według kraju/regionu pochodzenia na podstawie lokalizacji geograficznej (określony przez adres IP)

    ![geograficznie IP][3]

1. 10 najważniejszych alertów — podsumowanie 10 najczęściej wyzwalanych alertów i ich opis. Kliknięcie pojedynczego alertu powoduje odfiltrowanie pulpitu nawigacyjnego do informacji odnoszących się do tego konkretnego alertu.

    ![obraz 4][4]

1. Liczba alertów — łączna liczba alertów wyzwalanych przez zestaw reguł

    ![obraz 5][5]

1. 20 najpopularniejszych adresów IP/portów docelowych — wykresy kołowe przedstawiające 20 najważniejszych adresów IP i portów, na których zostały wyzwolone alerty. Możesz odfiltrować określone adresy IP/porty, aby sprawdzić, ile i jakiego rodzaju alerty są wyzwalane.

    ![obraz 6][6]

1. Podsumowanie alertu — tabela zawiera podsumowanie konkretnych szczegółów poszczególnych alertów. Możesz dostosować tę tabelę, aby wyświetlić inne parametry, które są istotne dla każdego alertu.

    ![obraz 7][7]

Aby uzyskać więcej dokumentacji dotyczącej tworzenia niestandardowych wizualizacji i pulpitów nawigacyjnych, zobacz [oficjalną dokumentację Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Podsumowanie

Łącząc przechwycenia pakietów udostępniane przez Network Watcher i identyfikatory Open Source, takie jak Suricata, można przeprowadzić wykrywanie wtargnięcia w sieci dla szerokiego zakresu zagrożeń. Te pulpity nawigacyjne pozwalają szybko wyszukiwać trendy i anomalie w sieci, a także Dig dane w celu odnalezienia głównych przyczyn alertów, takich jak złośliwi agenci użytkowników lub zagrożone porty. Korzystając z tych wyodrębnionych danych, można podejmować świadome decyzje dotyczące sposobu reagowania i ochrony sieci przed wszelkimi szkodliwymi próbami włamania oraz utworzyć reguły zapobiegające przyszłym włamaniom do sieci.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wyzwolić przechwytywanie pakietów na podstawie alertów, odwiedzając [użycie funkcji przechwytywania pakietów do aktywnego monitorowania sieci przy użyciu Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, jak wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą Power BI, odwiedzając [wizualizacje sieciowej grupy zabezpieczeń przepływy dzienników z Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
