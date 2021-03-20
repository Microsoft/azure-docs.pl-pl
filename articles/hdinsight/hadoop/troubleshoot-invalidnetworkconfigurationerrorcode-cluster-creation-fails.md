---
title: Błąd InvalidNetworkConfigurationErrorCode — usługa Azure HDInsight
description: Różne przyczyny niepowodzenia tworzenia klastrów z InvalidNetworkConfigurationErrorCode w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 83d4819ecb1da91bda5fb4f1cb445bbc34fd007f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927009"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Tworzenie klastra kończy się niepowodzeniem z InvalidNetworkConfigurationErrorCode w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

Jeśli widzisz kod błędu `InvalidNetworkConfigurationErrorCode` z opisem "Virtual Network konfiguracja nie jest zgodna z wymaganiem usługi HDInsight", zwykle wskazuje to problem z [konfiguracją sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) dla klastra. W oparciu o resztę opisu błędu, postępuj zgodnie z poniższymi sekcjami, aby rozwiązać problem.

## <a name="hostname-resolution-failed"></a>"Rozpoznawanie nazwy hosta nie powiodło się"

### <a name="issue"></a>Problem

Opis błędu zawiera komunikat "rozpoznawanie nazwy hosta nie powiodło się".

### <a name="cause"></a>Przyczyna

Ten błąd wskazuje na problem z niestandardową konfiguracją DNS. Serwery DNS w sieci wirtualnej mogą przekazywać zapytania DNS do tłumaczeń cyklicznych platformy Azure w celu rozpoznania nazw hostów w tej sieci wirtualnej (zobacz [rozpoznawanie nazw w sieciach wirtualnych](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) , aby uzyskać szczegółowe informacje). Dostęp do cyklicznych tłumaczeń platformy Azure jest udostępniany za pośrednictwem wirtualnego adresu IP 168.63.129.16. Ten adres IP jest dostępny tylko z maszyn wirtualnych platformy Azure. Dlatego nie będzie to możliwe, jeśli używany jest serwer DNS lokalnego lub serwer DNS jest MASZYNą wirtualną platformy Azure, która nie jest częścią sieci wirtualnej klastra.

### <a name="resolution"></a>Rozwiązanie

1. Użyj protokołu SSH do maszyny wirtualnej, która jest częścią klastra, i uruchom polecenie `hostname -f` . Spowoduje to zwrócenie w pełni kwalifikowanej nazwy domeny hosta (nazywanej `<host_fqdn>` w poniższych instrukcjach).

1. Następnie uruchom polecenie `nslookup <host_fqdn>` (na przykład `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Jeśli to polecenie rozwiąże nazwę z adresem IP, oznacza to, że serwer DNS działa prawidłowo. W takim przypadku Zgłoś sprawę pomocy technicznej z usługą HDInsight i zbadamy swój problem. W przypadku pomocy technicznej należy uwzględnić wykonane kroki rozwiązywania problemów. Pomoże nam to przyspieszyć rozwiązywanie problemów.

1. Jeśli powyższe polecenie nie zwraca adresu IP, uruchom polecenie `nslookup <host_fqdn> 168.63.129.16` (na przykład `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` ). Jeśli to polecenie jest w stanie rozpoznać adres IP, oznacza to, że serwer DNS nie przekazuje zapytania do usługi DNS systemu Azure lub nie jest to maszyna wirtualna będąca częścią tej samej sieci wirtualnej co klaster.

1. Jeśli nie masz maszyny wirtualnej platformy Azure, która może działać jako niestandardowy serwer DNS w sieci wirtualnej klastra, musisz najpierw ją dodać. Utwórz MASZYNę wirtualną w sieci wirtualnej, która zostanie skonfigurowana jako usługa przesyłania dalej DNS.

1. Po wdrożeniu maszyny wirtualnej w sieci wirtualnej należy skonfigurować reguły przekazywania DNS na tej maszynie wirtualnej. Przekazuj wszystkie żądania rozpoznawania nazw IDN do 168.63.129.16, a pozostałe do serwera DNS. [Oto](../hdinsight-plan-virtual-network-deployment.md) przykład tego Instalatora dla NIESTANDARDOWEGO serwera DNS.

1. Dodaj adres IP tej maszyny wirtualnej jako pierwszy wpis DNS dla Virtual Network konfiguracji DNS.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Nie można nawiązać połączenia z kontem usługi Azure Storage"

### <a name="issue"></a>Problem

Opis błędu zawiera komunikat "nie można nawiązać połączenia z kontem usługi Azure Storage" lub "nie można nawiązać połączenia z usługą Azure SQL".

### <a name="cause"></a>Przyczyna

Usługi Azure Storage i SQL nie mają stałych adresów IP, dlatego musimy zezwolić na połączenia wychodzące ze wszystkimi adresami-IP, aby zezwolić na dostęp do tych usług. Dokładne kroki rozwiązania zależą od tego, czy skonfigurowano sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń), czy reguły User-Defined (UDR). Szczegółowe informacje o tych konfiguracjach można znaleźć w sekcji Sprawdzanie [ruchu sieciowego za pomocą usługi HDInsight z grupami zabezpieczeń sieci i trasami zdefiniowanymi przez użytkownika](../control-network-traffic.md) .

### <a name="resolution"></a>Rozwiązanie

* Jeśli klaster używa [sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Przejdź do Azure Portal i zidentyfikuj sieciowej grupy zabezpieczeń, która jest skojarzona z podsiecią, w której jest wdrażany klaster. W sekcji **reguły zabezpieczeń dla ruchu wychodzącego** Zezwalaj na dostęp wychodzący do Internetu bez ograniczenia (należy zauważyć, że w tym miejscu jest wyższy numer **priorytetu** ). Ponadto w sekcji **podsieci** Sprawdź, czy ta sieciowej grupy zabezpieczeń jest stosowana do podsieci klastra.

* Jeśli klaster używa [tras zdefiniowanych przez użytkownika (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Przejdź do Azure Portal i zidentyfikuj tabelę tras skojarzoną z podsiecią, w której jest wdrażany klaster. Po znalezieniu tabeli tras dla podsieci Sprawdź w niej sekcję **trasy** .

    Jeśli istnieją zdefiniowane trasy, upewnij się, że istnieją trasy dla adresów IP dla regionu, w którym wdrożono klaster, a **NextHopType** dla każdej trasy to **Internet**. Powinna istnieć trasa zdefiniowana dla każdego wymaganego adresu IP opisanego w wymienionym artykule.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"Nie można nawiązać połączenia wychodzącego z klastra w celu komunikacji z dostawcą zasobów usługi HDInsight. Upewnij się, że łączność wychodząca jest dozwolona. "

### <a name="issue"></a>Problem

Opis błędu zawiera "nie można nawiązać połączenia wychodzącego z klastra w celu komunikacji z dostawcą zasobów usługi HDInsight. Upewnij się, że łączność wychodząca jest dozwolona. "

### <a name="cause"></a>Przyczyna

W przypadku korzystania z prywatnych połączonych klastrów usługi HDInsight dostęp wychodzący z klastra musi być skonfigurowany tak, aby zezwalał na nawiązywanie połączeń z dostawcą zasobów usługi HDInsight.

### <a name="resolution"></a>Rozwiązanie

* Aby rozwiązać ten problem, zapoznaj się z instrukcjami w sekcji Konfigurowanie linku prywatnego usługi HDInsight w [ustawieniach linku prywatnego](../hdinsight-private-link.md)
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Konfiguracja sieci wirtualnej jest niezgodna z wymaganiem usługi HDInsight"

### <a name="issue"></a>Problem

Opisy błędów zawierają komunikaty podobne do następujących:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Przyczyna

Jest to problem z konfiguracją niestandardowej usługi DNS.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy 168.63.129.16 znajduje się w niestandardowym łańcuchu DNS. Serwery DNS w sieci wirtualnej mogą przekazywać zapytania DNS do tłumaczeń cyklicznych platformy Azure w celu rozpoznania nazw hostów w tej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw w sieciach wirtualnych](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Dostęp do cyklicznych tłumaczeń platformy Azure jest udostępniany za pośrednictwem wirtualnego adresu IP 168.63.129.16.

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uruchom następujące polecenie:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Powinny zostać wyświetlone informacje podobne do następujących:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Na podstawie wyniku — wybierz jeden z następujących kroków, aby wykonać następujące czynności:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 nie znajduje się na tej liście

**Opcja 1**  
Dodaj 168.63.129.16 jako pierwszy niestandardowy serwer DNS dla sieci wirtualnej, korzystając z procedury opisanej w temacie [Planowanie sieci wirtualnej dla usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Te kroki są stosowane tylko wtedy, gdy niestandardowy serwer DNS działa w systemie Linux.

**Opcja 2**  
Wdróż MASZYNę wirtualną serwera DNS dla sieci wirtualnej. Obejmuje to następujące czynności:

* Utwórz MASZYNę wirtualną w sieci wirtualnej, która zostanie skonfigurowana jako usługa przesyłania dalej DNS (może to być maszyna wirtualna z systemem Linux lub Windows).
* Skonfiguruj reguły przekazywania DNS na tej maszynie wirtualnej (Przekazuj wszystkie żądania rozpoznawania nazw IDN do 168.63.129.16, a pozostałe do serwera DNS).
* Dodaj adres IP tej maszyny wirtualnej jako pierwszy wpis DNS dla Virtual Network konfiguracji DNS.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 znajduje się na liście

W takim przypadku należy utworzyć zgłoszenie do pomocy technicznej w usłudze HDInsight i zbadać swój problem. Uwzględnij wynik poniższych poleceń w przypadku pomocy technicznej. Pomoże nam to zbadać i rozwiązać problem.

W sesji SSH w węźle głównym Edytuj, a następnie uruchom następujące polecenie:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>Przyczyna

Inną przyczyną tego `InvalidNetworkConfigurationErrorCode` kodu błędu może być użycie przestarzałego parametru `EnableVmProtection` w programie PowerShell lub elemencie Runbook platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Użyj prawidłowych parametrów dla programu `Get-AzVirtualNetwork` zgodnie z opisem w programie [AZ PowerShell SDK](/powershell/module/az.network/get-azvirtualnetwork)

---

### <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
