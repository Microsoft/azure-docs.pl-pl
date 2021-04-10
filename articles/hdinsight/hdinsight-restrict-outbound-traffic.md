---
title: Konfigurowanie ograniczenia ruchu wychodzącego w sieci — Azure HDInsight
description: Dowiedz się, jak skonfigurować ograniczenie ruchu sieciowego ruchu wychodzącego dla klastrów usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 06990a5bd1d6619f07952e84870a01f5cd5068df
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384429"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurowanie wychodzącego ruchu sieciowego dla klastrów usługi Azure HDInsight przy użyciu zapory

W tym artykule przedstawiono procedurę zabezpieczania ruchu wychodzącego z klastra usługi HDInsight przy użyciu zapory platformy Azure. W poniższych krokach przyjęto założenie, że konfigurujesz zaporę platformy Azure dla istniejącego klastra. W przypadku wdrażania nowego klastra za zaporą najpierw należy utworzyć klaster usługi HDInsight i podsieć. Następnie wykonaj kroki opisane w tym przewodniku.

## <a name="background"></a>Tło

Klastry usługi HDInsight są zwykle wdrażane w sieci wirtualnej. Klaster ma zależności od usług spoza tej sieci wirtualnej.

Ruch przychodzący zarządzania nie może być wysyłany przez zaporę. Możesz użyć tagów usługi sieciowej grupy zabezpieczeń dla ruchu przychodzącego, jak opisano [tutaj](./hdinsight-service-tags.md). 

Zależności ruchu wychodzącego usługi HDInsight są prawie całkowicie zdefiniowane za pomocą nazw FQDN. Które nie mają za sobą statycznych adresów IP. Brak adresów statycznych oznacza, że sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) nie mogą blokować ruchu wychodzącego z klastra. Adresy IP, które często zmieniają się, nie mogą ustawiać reguł na podstawie bieżącego rozpoznawania nazw i używania.

Zabezpiecz adresy wychodzące z zaporą, która może kontrolować ruch wychodzący na podstawie nazw FQDN. Zapora platformy Azure ogranicza ruch wychodzący na podstawie nazwy FQDN tagów lokalizacji docelowej lub [nazwy FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurowanie zapory platformy Azure z usługą HDInsight

Podsumowanie kroków służących do blokowania ruchu wychodzącego z istniejącej usługi HDInsight przy użyciu zapory platformy Azure:

1. Utwórz podsieć.
1. Utwórz zaporę.
1. Dodaj reguły aplikacji do zapory.
1. Dodaj reguły sieci do zapory.
1. Utwórz tabelę routingu.

### <a name="create-new-subnet"></a>Utwórz nową podsieć

Utwórz podsieć o nazwie **AzureFirewallSubnet** w sieci wirtualnej, w której znajduje się klaster.

### <a name="create-a-new-firewall-for-your-cluster"></a>Tworzenie nowej zapory dla klastra

Utwórz zaporę o nazwie **test-FW01** , wykonując kroki opisane w sekcji **wdrażanie zapory** w [samouczku: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurowanie zapory przy użyciu reguł aplikacji

Utwórz kolekcję reguł aplikacji, która umożliwia klastrowi wysyłanie i odbieranie ważnej komunikacji.

1. Wybierz nową zaporę **test-FW01** z Azure Portal.

1. Przejdź do **ustawień**  >  **reguły**  >  **aplikacja Kolekcja reguł aplikacji**  >  **+ Dodaj kolekcję reguł aplikacji**.

    :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png" alt-text="Title: Dodawanie kolekcji reguł aplikacji":::

1. Na ekranie **Dodawanie kolekcji reguł aplikacji** podaj następujące informacje:

    **Górna sekcja**

    | Właściwość|  Wartość|
    |---|---|
    |Nazwa| FwAppRule|
    |Priorytet|200|
    |Akcja|Zezwalaj|

    **Sekcja tagów nazwy FQDN**

    | Nazwa | Adres źródłowy | Tag FQDN | Uwagi |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate i HDInsight | Wymagane dla usług HDI Services |

    **Sekcja docelowych nazw FQDN**

    | Nazwa | Adresy źródłowe | Protokół:port | Docelowe nazwy FQDN | Uwagi |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Zezwala na działanie logowania systemu Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Zezwala na działanie logowania systemu Windows |
    | Rule_4 | * | https:443 | storage_account_name. blob. Core. Windows. NET | Zamień `storage_account_name` na rzeczywistą nazwę konta magazynu. Upewnij się, że na koncie magazynu jest włączone [żądanie bezpiecznego transferu](../storage/common/storage-require-secure-transfer.md) . W przypadku korzystania z prywatnego punktu końcowego w celu uzyskania dostępu do kont magazynu ten krok nie jest wymagany, a ruch magazynu nie jest przekazywany do zapory.|

   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png" alt-text="Title: Wprowadź szczegóły kolekcji reguł aplikacji":::

1. Wybierz pozycję **Dodaj**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurowanie zapory przy użyciu reguł sieci

Utwórz reguły sieciowe w celu poprawnego skonfigurowania klastra usługi HDInsight. 

1. Kontynuując poprzedni krok, przejdź do sekcji **Kolekcja reguł sieciowych**  >  **+ Dodawanie kolekcji reguł sieci**.

1. Na ekranie **Dodawanie kolekcji reguł sieci** podaj następujące informacje:

    **Górna sekcja**

    | Właściwość|  Wartość|
    |---|---|
    |Nazwa| FwNetRule|
    |Priorytet|200|
    |Akcja|Zezwalaj|

    **Sekcja tagów usługi**

    | Nazwa | Protokół | Adresy źródłowe | Tagi usługi | Porty docelowe | Uwagi |
    | --- | --- | --- | --- | --- | --- |
    | Rule_5 | TCP | * | SQL | 1433, 11000-11999 | Jeśli używasz domyślnych serwerów SQL udostępnianych przez usługę HDInsight, skonfiguruj regułę sieci w sekcji Tagi usług dla programu SQL, która umożliwi rejestrowanie i inspekcję ruchu SQL. O ile punkty końcowe usługi nie zostały skonfigurowane dla SQL Server w podsieci usługi HDInsight, co spowoduje ominięcie zapory. Jeśli używasz niestandardowego programu SQL Server do obsługi Ambari, Oozie, Ranger i metadanych Hive, musisz zezwolić na ruch tylko do własnych niestandardowych serwerów SQL. Zapoznaj się z tematem [Architektura łączności Azure SQL Database i usługi Azure Synapse Analytics](../azure-sql/database/connectivity-architecture.md) , aby zobaczyć, dlaczego jest również wymagany zakres portów 11000-11999 oprócz 1433. |
    | Rule_6 | TCP | * | Azure Monitor | * | obowiązkowe Klienci, którzy planują korzystanie z funkcji automatycznego skalowania, powinni dodać tę regułę. |
    
   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png" alt-text="Title: wprowadzanie kolekcji reguł aplikacji":::

1. Wybierz pozycję **Dodaj**.

### <a name="create-and-configure-a-route-table"></a>Tworzenie i Konfigurowanie tabeli tras 

Utwórz tabelę tras z następującymi wpisami:

* Wszystkie adresy IP z [usług kondycji i zarządzania](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) z typem następnego przeskoku **Internet**. Powinien zawierać 4 adresy IP regionów ogólnych, a także 2 adresy IP dla danego regionu. Ta reguła jest wymagana tylko wtedy, gdy ResourceProviderConnection jest ustawiona na wartość *przychodzące*. Jeśli ResourceProviderConnection jest ustawiony na *wychodzące* , te adresy IP nie są konieczne w UDR. 

* Jedna trasa wirtualnego urządzenia dla adresu IP 0.0.0.0/0 z następnym przeskokiem jako prywatny adres IP zapory platformy Azure.

Aby na przykład skonfigurować tabelę tras dla klastra utworzonego w regionie US USA, należy wykonać następujące czynności:

1. Wybierz test zapory platformy Azure **— FW01**. Skopiuj **prywatny adres IP** wymieniony na stronie **Przegląd** . W tym przykładzie użyjemy **przykładowego adresu 10.0.2.4**.

1. Następnie przejdź do **wszystkich usług**  >  **sieciowych** usługi  >  **trasy tabele** i **Utwórz tabelę tras**.

1. W nowej trasie przejdź do **ustawień**  >  **trasy**  >  **+ Dodaj**. Dodaj następujące trasy:

| Nazwa trasy | Prefiks adresu | Typ następnego przeskoku | Adres następnego skoku |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Urządzenie wirtualne | 10.0.2.4 |

Ukończ konfigurację tabeli tras:

1. Przypisz utworzoną tabelę tras do podsieci usługi HDInsight, wybierając pozycję **podsieci** w obszarze **Ustawienia**.

1. Wybierz pozycję **+ Skojarz**.

1. Na ekranie **Skojarz podsieć** wybierz sieć wirtualną, w której został utworzony klaster. I **podsieć** użyta dla klastra usługi HDInsight.

1. Wybierz przycisk **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Ruch graniczny węzła lub aplikacji niestandardowej

Powyższe kroki pozwolą, aby klaster mógł działać bez problemów. W razie potrzeby nadal trzeba skonfigurować zależności, aby uwzględnić aplikacje niestandardowe działające w węzłach brzegowych.

Zależności aplikacji muszą być identyfikowane i dodane do zapory platformy Azure lub tabeli tras.

Trasy muszą zostać utworzone dla ruchu aplikacji, aby uniknąć problemów z routingiem asymetrycznym.

Jeśli aplikacje mają inne zależności, należy je dodać do zapory platformy Azure. Utwórz reguły aplikacji, aby umożliwić ruch HTTP/HTTPS i reguły sieciowe dla wszystkich innych elementów.

## <a name="logging-and-scale"></a>Rejestrowanie i skalowanie

Zapora platformy Azure może wysyłać dzienniki do kilku różnych systemów magazynowania. Aby uzyskać instrukcje dotyczące konfigurowania rejestrowania dla zapory, wykonaj kroki opisane w [samouczku: monitorowanie dzienników i metryk zapory platformy Azure](../firewall/firewall-diagnostics.md).

Po zakończeniu konfiguracji rejestrowania, jeśli używasz Log Analytics, można wyświetlić zablokowany ruch z użyciem zapytania, takiego jak:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integracja zapory platformy Azure z dziennikami Azure Monitor jest przydatna podczas pierwszego działania aplikacji. Szczególnie wtedy, gdy nie ma informacji o wszystkich zależnościach aplikacji. Więcej informacji na temat dzienników Azure Monitor można znaleźć [w temacie Analizowanie danych dzienników w Azure monitor](../azure-monitor/logs/log-query-overview.md)

Aby dowiedzieć się więcej o granicach skalowania zapory platformy Azure i zwiększania żądań, zobacz [ten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokument lub zapoznaj się z [często zadawanymi pytaniami](../firewall/firewall-faq.yml).

## <a name="access-to-the-cluster"></a>Dostęp do klastra

Po pomyślnym skonfigurowaniu zapory można użyć wewnętrznego punktu końcowego (), `https://CLUSTERNAME-int.azurehdinsight.net` Aby uzyskać dostęp do Ambari z poziomu sieci wirtualnej.

Aby móc używać publicznego punktu końcowego ( `https://CLUSTERNAME.azurehdinsight.net` ) lub punktu końcowego SSH ( `CLUSTERNAME-ssh.azurehdinsight.net` ), upewnij się, że masz odpowiednie trasy w tabeli tras i reguły sieciowej grupy zabezpieczeń, aby uniknąć problemu z routingiem asymetrycznym w [tym miejscu](../firewall/integrate-lb.md). W tym przypadku należy zezwolić na adres IP klienta w regułach sieciowej grupy zabezpieczeń dla ruchu przychodzącego, a także dodać go do tabeli tras zdefiniowanych przez użytkownika z następnym przeskokiem ustawionym jako `internet` . Jeśli Routing nie jest prawidłowo skonfigurowany, zobaczysz błąd limitu czasu.

## <a name="next-steps"></a>Następne kroki

* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Konfigurowanie wirtualnego urządzenia sieciowego](./network-virtual-appliance.md)
