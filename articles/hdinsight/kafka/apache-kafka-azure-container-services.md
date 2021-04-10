---
title: Korzystanie z usługi Azure Kubernetes Service z usługą Kafka w usłudze HDInsight
description: Dowiedz się, jak używać Kafka w usłudze HDInsight z obrazów kontenerów hostowanych w usłudze Azure Kubernetes Service (AKS).
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 2216eb5893b77761f4d31c5819d152ceeb985abc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869654"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Korzystanie z usługi Azure Kubernetes Service z usługą Apache Kafka w usłudze HDInsight

Dowiedz się, jak używać usługi Azure Kubernetes Service (AKS) z [Apache Kafka](https://kafka.apache.org/) w klastrze usługi HDInsight. W procedurach przedstawionych w tym dokumencie użyto aplikacji Node.js hostowanej w programie AKS do sprawdzenia łączności z usługą Kafka. Ta aplikacja używa pakietu [Kafka](https://www.npmjs.com/package/kafka-node) do komunikowania się z Kafka. Używa [Socket.IO](https://socket.io/) do obsługi komunikatów opartych na zdarzeniach między klientem przeglądarki a zapleczem hostowanym w AKS.

[Apache Kafka](https://kafka.apache.org) to rozproszona platforma przesyłania strumieniowego typu „open source”, która umożliwia tworzenie aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym. Usługa Azure Kubernetes zarządza hostowanym środowiskiem Kubernetes i umożliwia szybkie i łatwe wdrażanie aplikacji kontenerowych. Korzystając z Virtual Network platformy Azure, można połączyć te dwie usługi.

> [!NOTE]  
> Na tym dokumencie przedstawiono kroki wymagane do włączenia usługi Azure Kubernetes do komunikacji z usługą Kafka w usłudze HDInsight. Przykładem jest tylko podstawowy klient Kafka, który pokazuje, że konfiguracja działa.

## <a name="prerequisites"></a>Wymagania wstępne

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Subskrypcja platformy Azure

W tym dokumencie przyjęto założenie, że wiesz już, jak tworzyć i korzystać z następujących usług platformy Azure:

* Usługa Kafka w usłudze HDInsight
* Azure Kubernetes Service
* Sieci wirtualne platformy Azure

W tym dokumencie założono również, że zawarto Przewodnik po [samouczku dotyczącym usługi Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md). W tym artykule opisano tworzenie usługi kontenera, tworzenie klastra Kubernetes, rejestru kontenerów i Konfigurowanie `kubectl` Narzędzia.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Topologia sieci

Zarówno HDInsight, jak i AKS używają Virtual Network platformy Azure jako kontenera dla zasobów obliczeniowych. Aby umożliwić komunikację między usługą HDInsight i AKS, należy włączyć komunikację między sieciami. Kroki opisane w tym dokumencie używają komunikacji równorzędnej Virtual Network sieci. Inne połączenia, takie jak sieci VPN, powinny również być wykonane. Aby uzyskać więcej informacji na temat komunikacji równorzędnej, zobacz dokument [Komunikacja równorzędna sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md) .

Na poniższym diagramie przedstawiono topologię sieci używaną w tym dokumencie:

:::image type="content" source="./media/apache-kafka-azure-container-services/kafka-aks-architecture.png" alt-text="Usługa HDInsight w jednej sieci wirtualnej, AKS w innej, przy użyciu komunikacji równorzędnej" border="false":::

> [!IMPORTANT]  
> Rozpoznawanie nazw nie jest włączone między sieciami równorzędnymi, więc używane jest adresowanie IP. Domyślnie usługa Kafka w usłudze HDInsight jest skonfigurowana do zwracania nazw hostów zamiast adresów IP, gdy klienci nawiązują połączenie. Kroki opisane w tym dokumencie modyfikują Kafka do korzystania z reklam IP.

## <a name="create-an-azure-kubernetes-service-aks"></a>Tworzenie usługi Azure Kubernetes Service (AKS)

Jeśli nie masz jeszcze klastra AKS, użyj jednego z następujących dokumentów, aby dowiedzieć się, jak go utworzyć:

* [Wdrażanie klastra usługi Azure Kubernetes Service (AKS) — Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Wdrażanie klastra usługi Azure Kubernetes Service (AKS) — interfejs wiersza polecenia](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS tworzy sieć wirtualną podczas instalacji w **dodatkowej** grupie zasobów. Dodatkowa grupa zasobów jest zgodna z konwencją nazewnictwa **MC_resourceGroup_AKSclusterName_location**.  
> Ta sieć jest połączona z siecią utworzoną dla usługi HDInsight w następnej sekcji.

## <a name="configure-virtual-network-peering"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej

### <a name="identify-preliminary-information"></a>Zidentyfikuj informacje wstępne

1. Na [Azure Portal](https://portal.azure.com)odszukaj dodatkową **grupę zasobów** zawierającą sieć wirtualną dla klastra AKS.

2. Z grupy zasobów wybierz zasób __sieci wirtualnej__ . Zapisz tę nazwę do późniejszego użycia.

3. W obszarze **Ustawienia** wybierz pozycję __Przestrzeń adresowa__. Zanotuj podaną przestrzeń adresową.

### <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

1. Aby utworzyć sieć wirtualną dla usługi HDInsight, przejdź do __+ Utwórz zasób__  >  __Sieć__  >  __wirtualna sieci__.

1. Utwórz sieć, korzystając z następujących wskazówek dotyczących niektórych właściwości:

    |Właściwość | Wartość |
    |---|---|
    |Przestrzeń adresowa|Należy użyć przestrzeni adresowej, która nie nakłada się na sieć klastrów AKS.|
    |Lokalizacja|Użyj tej samej __lokalizacji__ dla sieci wirtualnej, która została użyta dla klastra AKS.|

1. Przed przejściem do następnego kroku poczekaj na utworzenie sieci wirtualnej.

### <a name="configure-peering"></a>Konfigurowanie komunikacji równorzędnej

1. Aby skonfigurować komunikację równorzędną między siecią usługi HDInsight a siecią klastra AKS, wybierz sieć wirtualną, a następnie wybierz pozycję __Komunikacja równorzędna__.

1. Wybierz pozycję __+ Dodaj__ i użyj następujących wartości, aby wypełnić formularz:

    |Właściwość |Wartość |
    |---|---|
    |Nazwa komunikacji równorzędnej z usługi \<this VN> do zdalnej sieci wirtualnej|Wprowadź unikatową nazwę tej konfiguracji komunikacji równorzędnej.|
    |Sieć wirtualna|Wybierz sieć wirtualną dla **klastra AKS**.|
    |Nazwa komunikacji równorzędnej od \<AKS VN> do \<this VN>|Wprowadź unikatową nazwę.|

    Pozostaw wszystkie inne pola w wartości domyślnej, a następnie wybierz przycisk __OK__ , aby skonfigurować komunikację równorzędną.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Tworzenie klastra Apache Kafka w usłudze HDInsight

Podczas tworzenia Kafka w klastrze usługi HDInsight należy dołączyć do sieci wirtualnej utworzonej wcześniej dla usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra Kafka, zapoznaj się z dokumentem [Tworzenie klastra Apache Kafka](apache-kafka-get-started.md) .

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurowanie reklamy Apache Kafka IP

Wykonaj następujące kroki, aby skonfigurować Kafka do anonsowania adresów IP zamiast nazw domen:

1. Korzystając z przeglądarki internetowej, przejdź do adresu `https://CLUSTERNAME.azurehdinsight.net`. Zastąp wartość CLUSTERname nazwą Kafka w klastrze usługi HDInsight.

    Po wyświetleniu monitu użyj nazwy użytkownika i hasła protokołu HTTPS dla klastra. Zostanie wyświetlony interfejs użytkownika sieci Web Ambari dla klastra.

2. Aby wyświetlić informacje o Kafka, wybierz pozycję __Kafka__ z listy po lewej stronie.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-service.png" alt-text="Lista usług z wyróżnioną pozycją Kafka" border="true":::

3. Aby wyświetlić konfigurację Kafka __, wybierz pozycję__ konfiguracje w górnej części.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-config1.png" alt-text="Konfiguracja usług Apache Ambari Services" border="true":::

4. Aby znaleźć konfigurację __Kafka-ENV__ , wprowadź `kafka-env` w polu __filtru__ w prawym górnym rogu.

    :::image type="content" source="./media/apache-kafka-azure-container-services/search-for-kafka-env.png" alt-text="Konfiguracja Kafka dla Kafka-ENV" border="true":::

5. Aby skonfigurować Kafka do anonsowania adresów IP, Dodaj następujący tekst na dole pola __Kafka-ENV-Template__ :

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, który Kafka nasłuchuje, wprowadź `listeners` w polu __filtru__ w prawym górnym rogu.

7. Aby skonfigurować Kafka do nasłuchiwania na wszystkich interfejsach sieciowych, Zmień wartość w polu __detektory__ na `PLAINTEXT://0.0.0.0:9092` .

8. Aby zapisać zmiany konfiguracji, użyj przycisku __Zapisz__ . Wprowadź wiadomość tekstową opisującą zmiany. Po zapisaniu zmian wybierz __przycisk OK__ .

    :::image type="content" source="./media/apache-kafka-azure-container-services/save-configuration-button.png" alt-text="Konfiguracja oprogramowania Apache Ambari" border="true":::

9. Aby zapobiec błędom podczas ponownego uruchamiania Kafka, użyj przycisku __Akcje usługi__ i wybierz pozycję __Włącz tryb konserwacji__. Wybierz przycisk OK, aby ukończyć tę operację.

    :::image type="content" source="./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png" alt-text="Akcje usługi z opcją Włącz konserwację" border="true":::

10. Aby ponownie uruchomić Kafka, użyj przycisku __Uruchom ponownie__ , a następnie wybierz pozycję __Uruchom ponownie wszystkie uwzględnione__. Potwierdź ponowne uruchomienie, a następnie użyj przycisku __OK__ po zakończeniu operacji.

    :::image type="content" source="./media/apache-kafka-azure-container-services/restart-required-button.png" alt-text="Przycisk Uruchom ponownie z wyróżnionym ponownym użyciem" border="true":::

11. Aby wyłączyć tryb konserwacji, użyj przycisku __Akcje usługi__ i wybierz polecenie Wyłącz __tryb konserwacji__. Wybierz **przycisk OK** , aby ukończyć tę operację.

## <a name="test-the-configuration"></a>Testowanie konfiguracji

W tym momencie Kafka i usługa Azure Kubernetes są w trakcie komunikacji za pomocą równorzędnych sieci wirtualnych. Aby przetestować to połączenie, wykonaj następujące czynności:

1. Utwórz temat Kafka używany przez aplikację testową. Aby uzyskać informacje na temat tworzenia tematów Kafka, zobacz artykuł [Tworzenie klastra Apache Kafka](apache-kafka-get-started.md) .

2. Pobierz przykładową aplikację z [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) .

3. Edytuj `index.js` plik i Zmień następujące wiersze:

    * `var topic = 'mytopic'`: Zastąp `mytopic` nazwą tematu Kafka używanego przez tę aplikację.
    * `var brokerHost = '176.16.0.13:9092`: Zastąp `176.16.0.13` wewnętrzny adres IP jednego z hostów brokera klastra.

        Aby znaleźć wewnętrzny adres IP hostów brokera (workernodes) w klastrze, zobacz dokument [interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) . Wybierz adres IP jednego z wpisów, w którym rozpoczyna się nazwa domeny `wn` .

4. W wierszu polecenia w `src` katalogu Zainstaluj zależności i użyj platformy Docker, aby skompilować obraz do wdrożenia:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Pakiety wymagane przez tę aplikację są sprawdzane w repozytorium, więc nie trzeba używać `npm` Narzędzia do ich instalowania.

5. Zaloguj się do Azure Container Registry (ACR) i Znajdź nazwę loginServer:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Jeśli nie znasz nazwy Azure Container Registry lub nie znasz interfejsu wiersza polecenia platformy Azure do pracy z usługą Azure Kubernetes, zobacz [samouczki AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Oznacz obraz lokalny `kafka-aks-test` przy użyciu LOGINSERVER ACR. Dodaj również `:v1` do końca, aby wskazać wersję obrazu:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Wypchnij obraz do rejestru:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Wykonanie tej operacji może potrwać kilka minut.

8. Edytuj plik manifestu Kubernetes ( `kafka-aks-test.yaml` ) i Zastąp ciąg `microsoft` ACR loginServer, który został pobrany w kroku 4.

9. Użyj następującego polecenia, aby wdrożyć ustawienia aplikacji z manifestu:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Użyj następującego polecenia, aby obejrzeć `EXTERNAL-IP` aplikację:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Po przypisaniu zewnętrznego adresu IP Użyj __klawiszy CTRL + C__ , aby wyjść z czujki

11. Otwórz przeglądarkę internetową i wprowadź zewnętrzny adres IP dla usługi. Dotrzesz do strony podobnej do poniższej ilustracji:

    :::image type="content" source="./media/apache-kafka-azure-container-services/test-web-page-image1.png" alt-text="Apache Kafka testowy obraz strony sieci Web" border="true":::

12. Wprowadź tekst do pola, a następnie wybierz przycisk __Wyślij__ . Dane są wysyłane do Kafka. Następnie konsument Kafka w aplikacji odczytuje komunikat i dodaje go do __wiadomości z sekcji Kafka__ .

    > [!WARNING]  
    > Może zostać wyświetlonych wiele kopii komunikatu. Ten problem zwykle występuje w przypadku odświeżenia przeglądarki po nawiązaniu połączenia lub otwarcia wielu połączeń przeglądarki z aplikacją.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z platformy Apache Kafka w usłudze HDInsight, użyj następujących linków:

* [Wprowadzenie do Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md)

* [Tworzenie repliki Apache Kafka w usłudze HDInsight przy użyciu programu narzędzia MirrorMaker](apache-kafka-mirroring.md)

* [Używanie Apache Storm z Apache Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Używanie Apache Spark z Apache Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Nawiązywanie połączenia z usługą Apache Kafka za pomocą usługi Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
