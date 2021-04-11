---
title: Tworzenie klastrów HBase Virtual Network na platformie Azure
description: Rozpocznij korzystanie z usługi HBase w usłudze Azure HDInsight. Dowiedz się, jak tworzyć klastry HBase usługi HDInsight w usłudze Azure Virtual Network.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: a9a1788473cb31f8e78aac0bbd5979b3d681ad32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867597"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Tworzenie klastrów Apache HBase w usłudze HDInsight na platformie Azure Virtual Network

Dowiedz się, jak tworzyć klastry Apache HBase usługi Azure HDInsight w usłudze [azure Virtual Network](https://azure.microsoft.com/services/virtual-network/).

Integracja z siecią wirtualną umożliwia wdrażanie klastrów Apache HBase w tej samej sieci wirtualnej co aplikacje, dzięki czemu aplikacje mogą komunikować się bezpośrednio z HBase. Korzyści:

* Bezpośrednie połączenie aplikacji sieci Web z węzłami klastra HBase, co umożliwia komunikację za pośrednictwem interfejsów API zdalnego wywoływania procedur (RPC) języka Java.
* Zwiększona wydajność dzięki braku ruchu w wielu bramach i modułach równoważenia obciążenia.
* Możliwość przetwarzania poufnych informacji w bezpieczniejszy sposób bez ujawniania publicznego punktu końcowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Tworzenie klastra Apache HBase w sieci wirtualnej

W tej sekcji utworzysz oparty na systemie Linux Klaster Apache HBase z zależnym kontem usługi Azure Storage w sieci wirtualnej platformy Azure przy użyciu [szablonu Azure Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Aby poznać inne metody tworzenia klastra i zrozumieć ustawienia, zobacz [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji o używaniu szablonu do tworzenia klastrów Apache Hadoop w usłudze HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Niektóre właściwości są trwale kodowane w szablonie. Na przykład:
>
> * **Lokalizacja**: Wschodnie stany USA 2
> * **Wersja klastra**: 3.6
> * **Liczba węzłów procesu roboczego klastra**: 2
> * **Domyślne konto magazynu**: unikatowy ciąg
> * **Nazwa sieci wirtualnej**: ClusterName-VNET
> * **Przestrzeń adresowa sieci wirtualnej**: 10.0.0.0/16
> * **Nazwa podsieci**: subnet1
> * **Zakres adresów podsieci**: 10.0.0.0/24
>
> `CLUSTERNAME` jest zastępowana nazwą klastra, którą podano podczas korzystania z szablonu.

1. Wybierz Poniższy obraz, aby otworzyć szablon w Azure Portal. Szablon znajduje się w szablonach [szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. W oknie dialogowym **wdrożenie niestandardowe** wybierz pozycję **Edytuj szablon**.

1. W wierszu 165 Zmień wartość `Standard_A3` na `Standard_A4_V2` . Następnie wybierz pozycję **Zapisz**.

1. Ukończ pozostałe szablony z następującymi informacjami:

    |Właściwość |Wartość |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure używaną do tworzenia klastra usługi HDInsight, zależnego konta magazynu i sieci wirtualnej platformy Azure.|
    Grupa zasobów|Wybierz pozycję **Utwórz nową**, a następnie określ nową nazwę grupy zasobów.|
    |Lokalizacja|Wybierz lokalizację dla grupy zasobów.|
    |Nazwa klastra|Wprowadź nazwę klastra Hadoop, który ma zostać utworzony.|
    |Nazwa użytkownika i hasło logowania klastra|Domyślna nazwa użytkownika to **admin**. Podaj hasło.|
    |Nazwa użytkownika i hasło ssh|Domyślna nazwa użytkownika to **sshuser**.  Podaj hasło.|

    Wybierz pozycję **Zgadzam się na warunki i warunki podane powyżej**.

1. Wybierz pozycję **Kup**. Utworzenie klastra trwa około 20 minut. Po utworzeniu klastra możesz wybrać klaster w portalu, aby go otworzyć.

Po zakończeniu tego artykułu możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Instrukcje usuwania klastra znajdują się [w temacie Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Aby rozpocząć pracę z nowym klastrem HBase, możesz użyć procedur znajdujących się w temacie Wprowadzenie do korzystania z platformy [Apache HBase z Apache Hadoop w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Nawiązywanie połączenia z klastrem Apache HBase przy użyciu interfejsów API protokołu Java platformy Apache HBase

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną "infrastruktura jako usługa" (IaaS) w tej samej sieci wirtualnej platformy Azure i tej samej podsieci. Instrukcje dotyczące tworzenia nowej maszyny wirtualnej IaaS można znaleźć w sekcji [Tworzenie maszyny wirtualnej z systemem Windows Server](../../virtual-machines/windows/quick-create-portal.md). Wykonując kroki opisane w tym dokumencie, należy użyć następujących wartości konfiguracji sieci:

* **Sieć wirtualna**: ClusterName-VNET
* **Podsieć**: subnet1

> [!IMPORTANT]  
> Zamień na `CLUSTERNAME` nazwę użytą podczas tworzenia klastra usługi HDInsight w poprzednich krokach.

Korzystając z tych wartości, maszyna wirtualna jest umieszczana w tej samej sieci wirtualnej i podsieci co klaster usługi HDInsight. Ta konfiguracja pozwala im bezpośrednio komunikować się ze sobą. Istnieje możliwość utworzenia klastra usługi HDInsight z pustym węzłem krawędzi. Węzeł brzegowy może służyć do zarządzania klastrem.  Aby uzyskać więcej informacji, zobacz [używanie pustych węzłów brzegowych w usłudze HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Uzyskaj w pełni kwalifikowaną nazwę domeny

W przypadku korzystania z aplikacji Java do zdalnego łączenia się z usługą HBase należy użyć w pełni kwalifikowanej nazwy domeny (FQDN). Aby to ustalić, należy uzyskać sufiks DNS konkretnego połączenia klastra HBase. W tym celu można użyć jednej z następujących metod:

* Użyj przeglądarki sieci Web, aby nawiązać połączenie [Apache Ambari](https://ambari.apache.org/) :

    Przejdź na stronę `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Zwraca plik JSON z sufiksami DNS.

* Użyj witryny sieci Web Ambari:

    1. Przejdź na stronę `https://CLUSTERNAME.azurehdinsight.net`.
    2. Wybierz pozycję **hosty** z górnego menu.

* Użyj Zwinięciea, aby nawiązywać wywołania REST:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

W zwróconych danych JavaScript Object Notation (JSON) Znajdź wpis "host_name". Zawiera nazwę FQDN dla węzłów w klastrze. Na przykład:

```json
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Częścią nazwy domeny rozpoczynającą się nazwą klastra jest sufiks DNS. Na przykład `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

### <a name="verify-communication-inside-virtual-network"></a>Weryfikowanie komunikacji wewnątrz sieci wirtualnej

Aby sprawdzić, czy maszyna wirtualna może komunikować się z klastrem HBase, użyj polecenia `ping headnode0.<dns suffix>` z maszyny wirtualnej. Na przykład `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Aby użyć tych informacji w aplikacji Java, można wykonać kroki opisane w temacie Korzystanie z platformy [Apache Maven do kompilowania aplikacji Java, które korzystają z platformy Apache HBase z usługą HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) w celu utworzenia. Aby aplikacja łączyła się ze zdalnym serwerem HBase, należy zmodyfikować plik **hbase-site.xml** w tym przykładzie, aby użyć nazwy FQDN dla dozorcy. Na przykład:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Aby uzyskać więcej informacji na temat rozpoznawania nazw w sieciach wirtualnych platformy Azure, w tym sposobu korzystania z własnego serwera DNS, zobacz [rozpoznawanie nazw (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia klastra Apache HBase. Aby dowiedzieć się więcej, zobacz:

* [Rozpoczynanie pracy z usługą HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Używanie pustych węzłów brzegowych w usłudze HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurowanie replikacji Apache HBase w usłudze HDInsight](apache-hbase-replication.md)
* [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Rozpoczynanie pracy z usługą Apache HBase z usługą Apache Hadoop w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Przegląd Virtual Network](../../virtual-network/virtual-networks-overview.md)
