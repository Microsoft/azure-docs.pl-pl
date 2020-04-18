---
title: Konfigurowanie ograniczenia ruchu wychodzącego sieciowego — usługa Azure HDInsight
description: Dowiedz się, jak skonfigurować ograniczenie ruchu sieciowego dla klastrów usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: d4bf2d1d4beeb00325d54e091a00438073509eef
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641308"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurowanie wychodzącego ruchu sieciowego dla klastrów usługi Azure HDInsight przy użyciu zapory

Ten artykuł zawiera kroki, aby zabezpieczyć ruch wychodzący z klastra USŁUGI HDInsight przy użyciu zapory platformy Azure. Poniższe kroki zakładają, że konfigurujesz zaporę platformy Azure dla istniejącego klastra. Jeśli wdrażasz nowy klaster za zaporą, najpierw utwórz klaster i podsieć HDInsight. Następnie postępuj zgodnie z instrukcjami w tym przewodniku.

## <a name="background"></a>Tło

Klastry HDInsight są zwykle wdrażane w sieci wirtualnej. Klaster ma zależności od usług poza tą siecią wirtualną.

Istnieje kilka zależności, które wymagają ruchu przychodzącego. Przychodzącego ruchu zarządzania nie można wysyłać za pośrednictwem urządzenia zapory. Adresy źródłowe dla tego ruchu są znane i są publikowane [tutaj](hdinsight-management-ip-addresses.md). Można również utworzyć reguły sieciowej grupy zabezpieczeń (NSG) z tymi informacjami, aby zabezpieczyć ruch przychodzący do klastrów.

Zależności ruchu wychodzącego HDInsight są prawie całkowicie zdefiniowane za pomocą sieci FQDN. Które nie mają statycznych adresów IP za nimi. Brak adresów statycznych oznacza, że sieciowe grupy zabezpieczeń nie mogą blokować ruchu wychodzącego z klastra. Adresy zmieniają się wystarczająco często, że nie można skonfigurować reguł na podstawie bieżącego rozpoznawania nazw i użycia.

Zabezpiecz adresy wychodzące za pomocą zapory, która może kontrolować ruch wychodzący na podstawie nazw domen. Zapora azure ogranicza ruch wychodzący na podstawie fqdn tagów docelowych lub [FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurowanie zapory platformy Azure za pomocą usługi HDInsight

Podsumowanie kroków, aby zablokować wyjście z istniejącego usługi HDInsight z Zaporą platformy Azure są:

1. Tworzenie podsieci.
1. Utwórz zaporę.
1. Dodawanie reguł aplikacji do zapory
1. Dodaj reguły sieciowe do zapory.
1. Tworzenie tabeli marszruty.

### <a name="create-new-subnet"></a>Tworzenie nowej podsieci

Utwórz podsieć o nazwie **AzureFirewallSubnet** w sieci wirtualnej, w której istnieje klaster.

### <a name="create-a-new-firewall-for-your-cluster"></a>Tworzenie nowej zapory dla klastra

Utwórz zaporę o nazwie **Test-FW01,** wykonując czynności opisane w temacie **Wdrażanie zapory** z [samouczka: Wdrażanie i konfigurowanie zapory platformy Azure przy użyciu portalu Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurowanie zapory przy regułach aplikacji

Utwórz kolekcję reguł aplikacji, która umożliwia klastrowi wysyłanie i odbieranie ważnych komunikatów.

1. Wybierz nową zaporę **Test-FW01** z witryny Azure portal.

1. Przejdź do**kolekcji** > reguł**aplikacji Reguły** >  **ustawień** > **+ Dodaj kolekcję reguł aplikacji**.

    ![Tytuł: Dodawanie kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Na ekranie **Zbieranie reguł dodawania aplikacji** podaj następujące informacje:

    **Górna część**

    | Właściwość|  Wartość|
    |---|---|
    |Nazwa| Reguła FwApp|
    |Priorytet|200|
    |Akcja|Zezwalaj|

    **Sekcja znaczników FQDN**

    | Nazwa | Adres źródłowy | Tag FQDN | Uwagi |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate i HDInsight | Wymagane dla usług HDI |

    **Docelowa sekcja FQDN**

    | Nazwa | Adresy źródłowe | `Protocol:Port` | Docelowe usługi FQDNS | Uwagi |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Umożliwia aktywność logowania systemu Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Umożliwia aktywność logowania systemu Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Zamień `storage_account_name` na rzeczywistą nazwę konta magazynu. Jeśli klaster jest wspierany przez WASB, dodaj regułę dla WASB. Aby korzystać tylko z połączeniami https, upewnij się, że na koncie magazynu jest włączona [opcja "wymagany bezpieczny transfer".](../storage/common/storage-require-secure-transfer.md) |

   ![Tytuł: Wprowadź szczegóły kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Wybierz pozycję **Dodaj**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurowanie zapory przy regułach sieciowych

Utwórz reguły sieciowe, aby poprawnie skonfigurować klaster HDInsight.

1. Kontynuując poprzedni krok, przejdź do **pozycji Kolekcja** > reguł**sieciowych + Dodaj kolekcję reguł sieciowych**.

1. Na ekranie **zbierania reguł dodawania sieci** podaj następujące informacje:

    **Górna część**

    | Właściwość|  Wartość|
    |---|---|
    |Nazwa| Reguła FwNet|
    |Priorytet|200|
    |Akcja|Zezwalaj|

    **Sekcja Adresy IP**

    | Nazwa | Protocol (Protokół) | Adresy źródłowe | Adresy docelowe | Porty docelowe | Uwagi |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Obsługa czasu |
    | Rule_2 | Dowolne | * | DC_IP_Address_1, DC_IP_Address_2 | * | Jeśli używasz pakietu zabezpieczeń przedsiębiorstwa (ESP), dodaj regułę sieci w sekcji Adresy IP, która umożliwia komunikację z usługą AAD-DS dla klastrów ESP. Adresy IP kontrolerów domeny można znaleźć w sekcji AAD-DS w portalu |
    | Rule_3 | TCP | * | Adres IP twojego konta Data Lake Storage | * | Jeśli używasz usługi Azure Data Lake Storage, możesz dodać regułę sieci w sekcji Adresy IP, aby rozwiązać problem sni z usługami ADLS Gen1 i Gen2. Ta opcja spowoduje skierowanie ruchu do zapory. Co może spowodować wyższe koszty dla dużych obciążeń danych, ale ruch będzie rejestrowany i podlegać inspekcji w dziennikach zapory. Określ adres IP konta Usługi Data Lake Storage. Można użyć polecenia programu PowerShell, takiego jak `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` rozpoznanie pliku FQDN na adres IP.|
    | Rule_4 | TCP | * | * | 12000 | (Opcjonalnie) Jeśli używasz usługi Log Analytics, utwórz regułę sieciową w sekcji Adresy IP, aby włączyć komunikację z obszarem roboczym usługi Log Analytics. |

    **Sekcja Znaczniki usług**

    | Nazwa | Protocol (Protokół) | Adresy źródłowe | Tagi usługi | Porty docelowe | Uwagi |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Skonfiguruj regułę sieci w sekcji Znaczniki usług dla języka SQL, która umożliwia rejestrowanie i inspekcję ruchu SQL. Chyba że skonfigurowano punkty końcowe usługi dla programu SQL Server w podsieci HDInsight, która ominie zaporę. |

   ![Tytuł: Wprowadź kolekcję reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Wybierz pozycję **Dodaj**.

### <a name="create-and-configure-a-route-table"></a>Tworzenie i konfigurowanie tabeli marszruty

Utwórz tabelę tras z następującymi wpisami:

* Wszystkie adresy IP z [usług kondycji i zarządzania: Wszystkie regiony](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) z następnym typem przeskoku **Internet**.

* Dwa adresy IP dla regionu, w którym klaster jest tworzony z [usług kondycji i zarządzania: Określone regiony](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) z następnym typem przeskoku **Internet**.

* Jedna trasa urządzenia wirtualnego dla adresu IP 0.0.0.0/0, przy czym następnym przeskokiem jest prywatny adres IP zapory platformy Azure.

Na przykład, aby skonfigurować tabelę tras dla klastra utworzonego w regionie Stanów Zjednoczonych "Wschodnie stany USA", należy wykonać następujące kroki:

1. Wybierz zaporę platformy Azure **Test-FW01**. Skopiuj **prywatny adres IP** wymieniony na stronie **Przegląd.** W tym przykładzie użyjemy **przykładowego adresu 10.0.2.4**.

1. Następnie przejdź do **pozycji Wszystkie usługi** > **Tabele tras** **sieciowych** > i **Utwórz tabelę tras**.

1. W nowej trasie przejdź do **pozycji Ustawienia** > **tras** > **+ Dodaj**. Dodaj następujące trasy:

| Nazwa trasy | Prefiks adresu | Typ następnego skoku | Adres następnego skoku |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Nie dotyczy |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Nie dotyczy |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Nie dotyczy |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Nie dotyczy |
| 13.82.225.233 | 13.82.225.233/32 | Internet | Nie dotyczy |
| 40.71.175.99 | 40.71.175.99/32 | Internet | Nie dotyczy |
| 0.0.0.0 | 0.0.0.0/0 | Urządzenie wirtualne | 10.0.2.4 |

Ukończ konfigurację tabeli tras:

1. Przypisz utworzoną tabelę tras do podsieci HDInsight, wybierając **podsieci** w obszarze **Ustawienia**.

1. Wybierz **+ Skojarz**.

1. Na ekranie **Skojarz podsieć** wybierz sieć wirtualną, w na którą został utworzony klaster. I **podsieci** używanej dla klastra HDInsight.

1. Kliknij przycisk **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Ruch w węźle krawędzi lub aplikacji niestandardowej

Powyższe kroki umożliwią klastra do pracy bez problemów. Nadal należy skonfigurować zależności, aby pomieścić aplikacje niestandardowe uruchomione na węzłach brzegowych, jeśli ma to zastosowanie.

Zależności aplikacji muszą być identyfikowane i dodawane do zapory platformy Azure lub tabeli tras.

Trasy muszą być tworzone dla ruchu aplikacji, aby uniknąć problemów z routingiem asymetrycznym.

Jeśli aplikacje mają inne zależności, należy je dodać do zapory platformy Azure. Utwórz reguły aplikacji, aby zezwolić na ruch HTTP/HTTPS i reguły sieciowe dla wszystkich innych.

## <a name="logging-and-scale"></a>Rejestrowanie i skalowanie

Zapora azure może wysyłać dzienniki do kilku różnych systemów magazynu. Aby uzyskać instrukcje dotyczące konfigurowania rejestrowania zapory, wykonaj kroki opisane w [samouczku: Monitorowanie dzienników i metryk zapory platformy Azure](../firewall/tutorial-diagnostics.md).

Po zakończeniu konfiguracji rejestrowania, jeśli używasz usługi Log Analytics, możesz wyświetlić zablokowany ruch za pomocą kwerendy, takiej jak:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integracja zapory platformy Azure z dziennikami usługi Azure Monitor jest przydatna podczas pierwszego działania aplikacji. Zwłaszcza, gdy nie są świadomi wszystkich zależności aplikacji. Więcej informacji o dziennikach usługi Azure Monitor można uzyskać na podstawie [analizy danych dziennika w usłudze Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Aby dowiedzieć się więcej o ograniczeniach skali Zapory platformy Azure i zwiększaniu żądań, zobacz [ten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokument lub zapoznaj się z [często zadawanymi pytaniami](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Dostęp do klastra

Po pomyślnym skonfigurowaniu zapory można użyć wewnętrznego punktu końcowego (`https://CLUSTERNAME-int.azurehdinsight.net`) w celu uzyskania dostępu do Ambari z wewnątrz sieci wirtualnej.

Aby użyć publicznego`https://CLUSTERNAME.azurehdinsight.net`punktu końcowego (`CLUSTERNAME-ssh.azurehdinsight.net`) lub punktu końcowego ssh ( ), upewnij się, że masz odpowiednie trasy w tabeli tras i reguły sieciowej sieciowej sieciowej, aby uniknąć wyjaśnionego [tutaj](../firewall/integrate-lb.md)problemu routingu asymetrycznego . W szczególności w tym przypadku należy zezwolić na adres IP klienta w regułach przychodzącej grupy sieciowej sieciowej, a także dodać go do tabeli routistyki zdefiniowanej przez użytkownika z następnym zestawem przeskoku jako `internet`. Jeśli routing nie jest poprawnie skonfigurowany, zostanie wyświetlony błąd limitu czasu.

## <a name="configure-another-network-virtual-appliance"></a>Konfigurowanie innego wirtualnego urządzenia sieciowego

> [!Important]
> Poniższe informacje są wymagane **tylko** wtedy, gdy chcesz skonfigurować wirtualne urządzenie sieciowe (NVA) inne niż Zapora azure.

Poprzednie instrukcje ułatwiają konfigurowanie Zapory platformy Azure w celu ograniczenia ruchu wychodzącego z klastra usługi HDInsight. Zapora azure jest automatycznie skonfigurowana, aby umożliwić ruch dla wielu typowych ważnych scenariuszy. Użycie innego wirtualnego urządzenia sieciowego wymaga skonfigurowania szeregu dodatkowych funkcji. Podczas konfigurowania urządzenia wirtualnego sieci należy pamiętać o następujących czynnikach:

* Usługi obsługujące punkt końcowy usługi powinny być skonfigurowane z punktami końcowymi usługi.
* Zależności adresów IP dotyczą ruchu innego niż HTTP/S (zarówno ruchu TCP, jak i UDP).
* Punkty końcowe HTTP/HTTPS FQDN można umieszczać w urządzeniu NVA.
* Punkty końcowe HTTP/HTTPS z symbolami wieloznaczowymi to zależności, które mogą się różnić w zależności od liczby kwalifikatorów.
* Przypisz tabelę tras utworzyną do podsieci HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Zależności z punktu końcowego usługi

| **Punktu końcowego** |
|---|
| Azure SQL |
| Azure Storage |
| Usługa Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Zależności adresów IP

| **Punktu końcowego** | **Szczegóły** |
|---|---|
| \*:123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na porcie 123 |
| Ip opublikowane [tutaj](hdinsight-management-ip-addresses.md) | Te usługi IP są usługą HDInsight |
| Prywatne adresy IP AAD-DS dla klastrów ESP |
| \*:16800 dla aktywacji systemu Windows KMS |
| \*12000 dla analizy dzienników |

#### <a name="fqdn-httphttps-dependencies"></a>Zależności HTTP/HTTPS usługi FQDN

> [!Important]
> Poniższa lista zawiera tylko kilka najważniejszych FQDN. Do skonfigurowania urządzenia WUS [w tym pliku](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)można uzyskać dodatkowe nazwy FQDN (głównie usługi Azure Storage i usługi Azure Service Bus).

| **Punktu końcowego**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Następne kroki

* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)
