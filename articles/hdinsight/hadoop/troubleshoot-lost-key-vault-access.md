---
title: Klastry usługi Azure HDInsight z szyfrowaniem dysków tracą dostęp Key Vault
description: Kroki rozwiązywania problemów i możliwe rozwiązania Key Vault problemów z dostępem w przypadku korzystania z klastrów usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: ce2929ca84746de1ab8b51882f3004c3699f17ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943120"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scenariusz: klastry usługi Azure HDInsight z szyfrowaniem dysków tracą dostęp Key Vault

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Alert Resource Health Center (systemie RHC występuje), `The HDInsight cluster is unable to access the key for BYOK encryption at rest` ,, jest pokazywany dla klastrów Bring Your Own Key (BYOK), w których węzły klastra utraciły dostęp do klientów Key Vault (KV). Podobne alerty mogą być również widoczne w interfejsie użytkownika Apache Ambari.

## <a name="cause"></a>Przyczyna

Alert zapewnia dostęp do elementu KV z węzłów klastra, dzięki czemu zapewnianie połączenia sieciowego, kondycji KV i zasad dostępu dla tożsamości zarządzanej przypisanej przez użytkownika. Ten alert jest tylko ostrzeżeniem o zbliżającym się zamykaniu brokera w kolejnych ponownych uruchomieniach węzła, klaster będzie działał do momentu ponownego uruchomienia węzłów.

Przejdź do interfejsu użytkownika Apache Ambari, aby uzyskać więcej informacji na temat alertu na podstawie **stanu szyfrowania dysku Key Vault**. Ten alert zawiera szczegółowe informacje o przyczynie niepowodzenia weryfikacji.

## <a name="resolution"></a>Rozwiązanie

### <a name="kvaad-outage"></a>Awaria KV/AAD

Zapoznaj się z informacjami na [Azure Key Vault dostępności i nadmiarowości](../../key-vault/general/disaster-recovery-guidance.md) oraz na stronie stanu platformy Azure, aby uzyskać więcej szczegółów https://status.azure.com/

### <a name="kv-accidental-deletion"></a>Przypadkowe usunięcie

* Przywróć usunięty klucz z KV do automatycznego odzyskiwania. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie usuniętego klucza](/rest/api/keyvault/recoverdeletedkey).
* Skontaktuj się z zespołem KV, aby odzyskać sprawność po przypadkowym usunięciu.

### <a name="kv-access-policy-changed"></a>Zmieniono zasady dostępu KV

Przywróć zasady dostępu dla tożsamości zarządzanej przypisanej przez użytkownika przypisanej do klastra HDI, aby uzyskać dostęp do KV.

### <a name="key-permitted-operations"></a>Operacje dozwolone dla klucza

Dla każdego klucza w KV można wybrać zestaw dozwolonych operacji. Upewnij się, że masz włączone operacje zawijania i odpakowania dla klucza BYOK

### <a name="expired-key"></a>Klucz wygasły

Jeśli upłynął okres ważności i klucz nie jest obrócony, Przywróć klucz z modułu HSM kopii zapasowej lub skontaktuj się z zespołem KV, aby wyczyścić datę wygaśnięcia.

### <a name="kv-firewall-blocking-access"></a>Dostęp do blokowania zapory KV

Popraw ustawienia zapory KV, aby zezwolić BYOK węzłom klastra na dostęp do KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>SIECIOWEJ grupy zabezpieczeń reguły dotyczące dostępu blokowania sieci wirtualnej

Sprawdź reguły sieciowej grupy zabezpieczeń skojarzone z siecią wirtualną dołączoną do klastra.

## <a name="mitigation-and-prevention-steps"></a>Kroki zaradcze i zapobiegania

### <a name="kv-accidental-deletion"></a>Przypadkowe usunięcie

* Skonfiguruj Key Vault z [zestawem blokad zasobów](../../azure-resource-manager/management/lock-resources.md).
* Wykonaj kopię zapasową kluczy do ich sprzętowego modułu zabezpieczeń.

### <a name="key-deletion"></a>Usuwanie klucza

Klaster powinien zostać usunięty przed usunięciem klucza.

### <a name="kv-access-policy-changed"></a>Zmieniono zasady dostępu KV

Regularnie przeprowadzaj inspekcję i testowanie zasad dostępu.

### <a name="expired-key"></a>Klucz wygasły

* Wykonaj kopię zapasową kluczy w module HSM.
* Użyj klucza bez żadnego zestawu wygasania.
* Jeśli należy ustawić wygaśnięcie, Obróć klucze przed datą wygaśnięcia.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).