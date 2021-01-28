---
title: Lustrzane tematy Apache Kafka — Azure HDInsight
description: Dowiedz się, jak używać funkcji dublowania Apache Kafka, aby zachować replikę Kafka w klastrze usługi HDInsight przez tematy dotyczące dublowania w klastrze pomocniczym.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: c2fce6d4ee95a56cc087d50184fcd69ac113620f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940850"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Replikowanie tematów platformy Apache Kafka przy użyciu platformy Kafka w usłudze HDInsight za pomocą narzędzia MirrorMaker

Dowiedz się, jak za pomocą funkcji dublowania Apache Kafka replikować tematy do klastra pomocniczego. Dublowanie może być uruchamiane jako ciągły proces lub używany sporadycznie jako metoda migrowania danych z jednego klastra do drugiego.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku *dozwolonych*, termin, przez który firma Microsoft już nie używa. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

W tym przykładzie dublowanie jest używane do replikowania tematów między dwoma klastrami usługi HDInsight. Oba klastry znajdują się w różnych sieciach wirtualnych w różnych centrach danych.

> [!WARNING]  
> Funkcja dublowania nie powinna być traktowana jako środek w celu uzyskania odporności na uszkodzenia. Przesunięcie do elementów w temacie różni się między klastrem podstawowym i pomocniczym, dzięki czemu klienci nie mogą korzystać z dwóch zamiennie.
>
> Jeśli chodzi o odporność na uszkodzenia, należy ustawić replikację tematów w ramach klastra. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak działa dublowanie Apache Kafka

Dublowanie działa przy użyciu narzędzia [Narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (część Apache Kafka) do korzystania z rekordów z tematów w klastrze podstawowym, a następnie tworzenia kopii lokalnej w klastrze pomocniczym. Narzędzia MirrorMaker używa jednego (lub więcej) *konsumentów* odczytanych z klastra podstawowego i *producenta* , który zapisuje w lokalnym (pomocniczym) klastrze.

Najbardziej przydatna konfiguracja dublowania odzyskiwania po awarii korzysta z klastrów Kafka w różnych regionach świadczenia usługi Azure. Aby to osiągnąć, sieci wirtualne, w których znajdują się klastry, są połączone za pomocą komunikacji równorzędnej.

Na poniższym diagramie przedstawiono proces dublowania oraz sposób przepływu komunikacji między klastrami:

![Diagram procesu dublowania](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Klaster podstawowy i pomocniczy mogą być różne w zależności od liczby węzłów i partycji, a przesunięcia w tematach są również inne. Funkcja dublowania utrzymuje wartość klucza, która jest używana do partycjonowania, więc kolejność rekordów jest zachowywana na podstawie klucza.

### <a name="mirroring-across-network-boundaries"></a>Dublowanie między granicami sieci

Jeśli potrzebujesz dublowania między klastrami Kafka w różnych sieciach, istnieją następujące kwestie dodatkowe:

* **Bramy**: sieci muszą mieć możliwość komunikowania się na poziomie protokołu TCP/IP.

* **Adresowanie serwera**: możesz wybrać adresowanie węzłów klastra przy użyciu adresów IP lub w pełni kwalifikowanych nazw domen.

    * **Adresy IP**: w przypadku skonfigurowania klastrów Kafka do korzystania z reklamy adresów IP można kontynuować konfigurację dublowania przy użyciu adresów IP węzłów brokera i węzłów dozorcy.
    
    * **Nazwy domen**: Jeśli nie skonfigurujesz klastrów Kafka na potrzeby reklamy adresów IP, klastry muszą mieć możliwość nawiązania połączenia ze sobą przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Wymaga to serwera systemu nazw domen (DNS) w każdej sieci skonfigurowanej do przesyłania dalej żądań do innych sieci. Podczas tworzenia Virtual Network platformy Azure zamiast korzystania z automatycznego systemu DNS dostarczonego z siecią należy określić niestandardowy serwer DNS i adres IP dla serwera. Po utworzeniu Virtual Network należy utworzyć maszynę wirtualną platformy Azure, która używa tego adresu IP, a następnie zainstalować i skonfigurować na nim oprogramowanie DNS.

    > [!WARNING]  
    > Utwórz i skonfiguruj niestandardowy serwer DNS przed zainstalowaniem usługi HDInsight w Virtual Network. Nie jest wymagana dodatkowa konfiguracja usługi HDInsight do korzystania z serwera DNS skonfigurowanego dla Virtual Network.

Aby uzyskać więcej informacji na temat łączenia dwóch sieci wirtualnych platformy Azure, zobacz [Konfigurowanie połączenia Sieć wirtualna-sieć](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)wirtualna.

## <a name="mirroring-architecture"></a>Architektura dublowania

Ta architektura zawiera dwa klastry w różnych grupach zasobów i sieci wirtualnych: **podstawowe** i **pomocnicze**.

### <a name="creation-steps"></a>Kroki tworzenia

1. Utwórz dwie nowe grupy zasobów:

    |Grupa zasobów | Lokalizacja |
    |---|---|
    | Kafka — podstawowy — RG | Central US |
    | Kafka-pomocniczy — RG | Północno-środkowe stany USA |

1. Utwórz nową sieć wirtualną **Kafka-Primary-VNET** w **Kafka-Primary-RG**. Pozostaw ustawienia domyślne.
1. Utwórz nową sieć wirtualną **Kafka-dodatkową-VNET** w **Kafka-pomocnicz-RG**, również z ustawieniami domyślnymi.

1. Utwórz dwa nowe klastry Kafka:

    | Nazwa klastra | Grupa zasobów | Virtual Network | Konto magazynu |
    |---|---|---|---|
    | Kafka — klaster podstawowy | Kafka — podstawowy — RG | Kafka — Sieć wirtualna | kafkaprimarystorage |
    | Kafka — klaster pomocniczy | Kafka-pomocniczy — RG | Kafka — pomocnicza Sieć wirtualna | kafkasecondarystorage |

1. Utwórz wirtualne sieci równorzędne. Ten krok spowoduje utworzenie dwóch komunikacji równorzędnych: jeden z **Kafka-Primary-VNET** - **Kafka-------** ------ ------------------- **--**
    1. Wybierz sieć wirtualną **Kafka-Primary-VNET** .
    1. Wybierz pozycję **Komunikacja równorzędna** w obszarze **Ustawienia**.
    1. Wybierz pozycję **Dodaj**.
    1. Na ekranie **Dodawanie komunikacji równorzędnej** wprowadź szczegóły, jak pokazano na poniższym zrzucie ekranu.

        ![Usługa HDInsight Kafka Dodawanie komunikacji równorzędnej sieci wirtualnej](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Konfigurowanie reklamy adresów IP

Skonfiguruj anonsowanie adresów IP, aby umożliwić klientowi łączenie się przy użyciu adresów IP brokera zamiast nazw domen.

1. Przejdź do pulpitu nawigacyjnego Ambari dla klastra podstawowego: `https://PRIMARYCLUSTERNAME.azurehdinsight.net` .
1. Wybierz pozycję **usługi**  >  **Kafka**. CliSelectck kartę **konfiguracje** .
1. Dodaj następujące wiersze konfiguracji do dolnej sekcji **szablonu Kafka-ENV** . Wybierz pozycję **Zapisz**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Wprowadź uwagę na ekranie **Zapisywanie konfiguracji** i kliknij przycisk **Zapisz**.
1. Jeśli zostanie wyświetlony monit z ostrzeżeniem o konfiguracji, kliknij przycisk **Kontynuuj mimo to**.
1. W obszarze **Zapisz zmiany konfiguracji** wybierz pozycję **OK** .
1. Wybierz pozycję **Uruchom ponownie**  >  **Uruchom ponownie wszystkie zmiany, których dotyczy** powiadomienie **wymagane do ponownego uruchomienia** . Wybierz pozycję **Potwierdź ponowne uruchomienie wszystkich**.

    ![System Apache Ambari](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Skonfiguruj Kafka do nasłuchiwania na wszystkich interfejsach sieciowych.
    
1. Pozostań na **karcie konfiguracje w** obszarze **usługi**  >  **Kafka**. W sekcji **brokera Kafka** ustaw właściwość **detektory** na `PLAINTEXT://0.0.0.0:9092` .
1. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Uruchom ponownie** i **Potwierdź wszystkie ponowne uruchomienie**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Rejestruj adresy IP brokera i adresy dozorcy dla klastra podstawowego.

1. Wybierz pozycję **hosty** na pulpicie nawigacyjnym Ambari.
1. Zanotuj adresy IP dla brokerów i Dozorcami. Węzły brokera mają wartość **WN** jako pierwsze dwie litery nazwy hosta, a węzły dozorcy mają **ZK** jako pierwsze dwie litery nazwy hosta.

    ![Adresy IP węzłów widoku Apache Ambari](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Powtórz poprzednie trzy kroki dla drugiego klastra **Kafka-dodatkowy** klaster: Skonfiguruj anonsowanie adresów IP, ustaw odbiorniki i zanotuj adresy IP brokera i dozorcy.

## <a name="create-topics"></a>Tworzenie tematów

1. Nawiązywanie połączenia z klastrem **podstawowym** przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** nazwą użytkownika ssh używaną podczas tworzenia klastra. Zastąp **PRIMARYCLUSTER** nazwą podstawową używaną podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Użyj następującego polecenia, aby utworzyć zmienną z hostami Apache dozorcy dla klastra podstawowego. Ciągi takie jak `ZOOKEEPER_IP_ADDRESS1` muszą zostać zastąpione rzeczywistymi zarejestrowanymi adresami IP, takimi jak `10.23.0.11` i `10.23.0.7` . Jeśli używasz rozpoznawania nazwy FQDN z niestandardowym serwerem DNS, wykonaj [następujące kroki](apache-kafka-get-started.md#getkafkainfo) , aby uzyskać nazwy brokera i dozorcy.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Aby utworzyć temat o nazwie `testtopic` , użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Użyj następującego polecenia, aby sprawdzić, czy temat został utworzony:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Odpowiedź zawiera `testtopic` .

1. Aby wyświetlić informacje o hoście dozorcy dla tego ( **podstawowego**) klastra, wykonaj następujące czynności:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Zwraca informacje podobne do następującego tekstu:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Zapisz te informacje. Jest on używany w następnej sekcji.

## <a name="configure-mirroring"></a>Konfigurowanie dublowania

1. Połącz się z klastrem **pomocniczym** przy użyciu innej sesji SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Zastąp **sshuser** nazwą użytkownika ssh używaną podczas tworzenia klastra. Zastąp **SECONDARYCLUSTER** nazwą użytą podczas tworzenia klastra.

    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. `consumer.properties`Plik jest używany do konfigurowania komunikacji z klastrem **podstawowym** . Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano consumer.properties
    ```

    Użyj następującego tekstu jako zawartości `consumer.properties` pliku:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Zastąp **PRIMARY_ZKHOSTS** adresami IP dozorcy z klastra **podstawowego** .

    Ten plik opisuje informacje o konsumentach, które mają być używane podczas odczytywania z podstawowego klastra Kafka. Aby uzyskać więcej informacji, zobacz Konfiguracje [odbiorców](https://kafka.apache.org/documentation#consumerconfigs) w witrynie Kafka.Apache.org.

    Aby zapisać plik, użyj **kombinacji klawiszy Ctrl + X**, **Y**, a następnie klawisz **Enter**.

1. Przed skonfigurowaniem producenta, który komunikuje się z klastrem pomocniczym, należy skonfigurować zmienną dla adresów IP brokera klastra **pomocniczego** . Użyj następujących poleceń, aby utworzyć tę zmienną:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Polecenie `echo $SECONDARY_BROKERHOSTS` powinno zwracać informacje podobne do następującego tekstu:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. `producer.properties`Plik jest używany do komunikacji z klastrem **pomocniczym** . Aby utworzyć plik, użyj następującego polecenia:

    ```bash
    nano producer.properties
    ```

    Użyj następującego tekstu jako zawartości `producer.properties` pliku:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Zastąp **SECONDARY_BROKERHOSTS** adresami IP brokera używanymi w poprzednim kroku.

    Aby uzyskać więcej informacji, zobacz Konfiguracje [producentów](https://kafka.apache.org/documentation#producerconfigs) w witrynie Kafka.Apache.org.

1. Użyj następujących poleceń, aby utworzyć zmienną środowiskową z adresami IP hostów dozorcy dla klastra pomocniczego:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Domyślna konfiguracja Kafka w usłudze HDInsight nie zezwala na automatyczne tworzenie tematów. Przed rozpoczęciem procesu dublowania należy użyć jednej z następujących opcji:

    * **Tworzenie tematów w klastrze pomocniczym**: Ta opcja umożliwia również ustawienie liczby partycji i współczynnika replikacji.

        Możesz tworzyć tematy wcześniej za pomocą następującego polecenia:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Zamień `testtopic` na nazwę tematu, który ma zostać utworzony.

    * **Skonfiguruj klaster na potrzeby automatycznego tworzenia tematu**: Ta opcja umożliwia narzędzia mirrormakerom automatyczne tworzenie tematów, ale może je utworzyć przy użyciu innej liczby partycji lub współczynnika replikacji niż w temacie podstawowym.

        Aby skonfigurować klaster pomocniczy do automatycznego tworzenia tematów, wykonaj następujące kroki:

        1. Przejdź do pulpitu nawigacyjnego Ambari dla klastra pomocniczego: `https://SECONDARYCLUSTERNAME.azurehdinsight.net` .
        1. Kliknij pozycję **usługi**  >  **Kafka**. Kliknij kartę **konfiguracje** .
        1. W polu __Filtr__ wprowadź wartość `auto.create` . Spowoduje to przefiltrowanie listy właściwości i wyświetlenie `auto.create.topics.enable` Ustawienia.
        1. Zmień wartość `auto.create.topics.enable` na true, a następnie wybierz pozycję __Zapisz__. Dodaj notatkę, a następnie wybierz pozycję __Zapisz__ ponownie.
        1. Wybierz usługę __Kafka__ , wybierz pozycję __Uruchom ponownie__, a następnie wybierz pozycję __Uruchom ponownie wszystkie uwzględnione__. Po wyświetleniu monitu wybierz pozycję __Potwierdź ponowne uruchomienie wszystkich__.

        ![Kafka Włącz tematy AutoCreate](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Uruchom narzędzia MirrorMaker

1. Z poziomu połączenia SSH z klastrem **pomocniczym** Użyj następującego polecenia, aby uruchomić proces narzędzia MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry używane w tym przykładzie są następujące:

    |Parametr |Opis |
    |---|---|
    |--consumer.config|Określa plik, który zawiera właściwości konsumenta. Te właściwości są używane do tworzenia konsumenta, który odczytuje z *podstawowego* klastra Kafka.|
    |--producer.config|Określa plik, który zawiera właściwości producenta. Te właściwości są używane do tworzenia producenta, który zapisuje dane do *pomocniczego* klastra Kafka.|
    |--dozwolonych|Lista tematów, które narzędzia MirrorMaker replikuje z klastra podstawowego do pomocniczego.|
    |--NUM. strumieni|Liczba wątków klienta do utworzenia.|

    Odbiorca w węźle pomocniczym teraz czeka na odbieranie komunikatów.

2. Z poziomu połączenia SSH z klastrem **podstawowym** Użyj następującego polecenia, aby uruchomić producenta i wysłać komunikaty do tematu:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Po dodaniu do pustego wiersza z kursorem wpisz kilka wiadomości tekstowych. Komunikaty są wysyłane do tematu w klastrze **podstawowym** . Po zakończeniu Użyj **klawiszy CTRL + C** , aby zakończyć proces produkcji.

3. Z poziomu połączenia SSH z klastrem **pomocniczym** Użyj **kombinacji klawiszy CTRL + C** , aby zakończyć proces narzędzia MirrorMaker. Zakończenie procesu może potrwać kilka sekund. Aby sprawdzić, czy komunikaty zostały zreplikowane do pomocniczego, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Lista tematów zawiera teraz `testtopic` , które są tworzone, gdy MirrorMaster odzwierciedla temat z podstawowego klastra do pomocniczego. Komunikaty pobrane z tematu są takie same jak te wprowadzone w klastrze podstawowym.

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Kroki przedstawione w tym dokumencie tworzą klastry w różnych grupach zasobów platformy Azure. Aby usunąć wszystkie utworzone zasoby, można usunąć dwie utworzone grupy zasobów: **Kafka-Primary-RG** i **Kafka-secondary_rg**. Usunięcie grup zasobów powoduje usunięcie wszystkich zasobów utworzonych przez następujące dokumenty, w tym klastrów, sieci wirtualnych i kont magazynu.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób użycia programu [Narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do utworzenia repliki klastra [Apache Kafka](https://kafka.apache.org/) . Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Kafka:

* [Apache Kafka Dokumentacja narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.Apache.org.
* [Najlepsze rozwiązania w zakresie tworzenia duplikatów Kafka](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Wprowadzenie do Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md)
* [Używanie Apache Spark z Apache Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Nawiązywanie połączenia z usługą Apache Kafka za pomocą usługi Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
