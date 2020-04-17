---
title: Klastry usługi Azure HDInsight z szyfrowaniem dysku tracą dostęp do usługi Key Vault
description: Rozwiązywanie problemów z instrukcjami i możliwymi rozwiązaniami problemów podczas interakcji z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461535"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scenariusz: klastry usługi Azure HDInsight z szyfrowaniem dysku tracą dostęp do usługi Key Vault

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Alert `The HDInsight cluster is unable to access the key for BYOK encryption at rest`Centrum kondycji zasobów (RHC) jest wyświetlany dla klastrów Bring Your Own Key (BYOK), w których węzły klastra utraciły dostęp do klientów, aby przechowalnia kluczy (KV). Podobne alerty można również zobaczyć na Apache Ambari UI.

## <a name="cause"></a>Przyczyna

Alert zapewnia, że KV jest dostępny z węzłów klastra, zapewniając w ten sposób połączenie sieciowe, kondycję KV i zasady dostępu dla użytkownika przypisanego tożsamości zarządzanej. Ten alert jest tylko ostrzeżenie o zbliżającym się zamknięciu brokera po kolejnych ponownych rozruchów węzła, klaster nadal działa do ponownego uruchomienia węzłów.

Przejdź do interfejsu apache Ambari, aby uzyskać więcej informacji na temat alertu ze **stanu magazynu kluczy szyfrowania dysku**. Ten alert będzie miał szczegółowe informacje o przyczynie niepowodzenia weryfikacji.

## <a name="resolution"></a>Rozwiązanie

### <a name="kvaad-outage"></a>Awaria KV/AAD

Więcej informacji znajdziesz na [stronie dostępności i nadmiarowości](../../key-vault/general/disaster-recovery-guidance.md) usługi Azure Key Vault oraz stronie stanu usługi Azurehttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>Przypadkowe usunięcie KV

* Przywróć usunięty klucz na KV, aby automatycznie odzyskać. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie usuniętego klucza](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Skontaktuj się z zespołem KV, aby odzyskać siły po przypadkowych usunięciach.

### <a name="kv-access-policy-changed"></a>Zmieniono zasady dostępu kv

Przywróć zasady dostępu dla użytkownika przypisanego do tożsamości zarządzanej przypisanej do klastra HDI w celu uzyskania dostępu do kv.

### <a name="key-permitted-operations"></a>Kluczowe dozwolone operacje

Dla każdego klucza w KV, można wybrać zestaw dozwolonych operacji. Upewnij się, że dla klucza BYOK włączono operacje zawijania i odwijania

### <a name="expired-key"></a>Wygasły klucz

Jeśli wygaśnięcie minęło, a klucz nie zostanie obrócony, przywróć klucz z kopii zapasowej modułu HSM lub skontaktuj się z zespołem KV, aby wyczyścić datę wygaśnięcia.

### <a name="kv-firewall-blocking-access"></a>Dostęp do zapory KV

Napraw ustawienia zapory KV, aby umożliwić węzłom klastra BYOK dostęp do KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Zasady sieciowej sieciowej dotyczące blokowania dostępu do sieci wirtualnej

Sprawdź reguły sieciowej grupy sieciowej skojarzone z siecią wirtualną dołączoną do klastra.

## <a name="mitigation-and-prevention-steps"></a>Działania łagodzące i zapobiegawcze

### <a name="kv-accidental-deletion"></a>Przypadkowe usunięcie KV

* Skonfiguruj magazyn [kluczy](../../azure-resource-manager/management/lock-resources.md)za pomocą zestawu blokad zasobów .
* Śmiuj zapas kluczy do sprzętowego modułu zabezpieczeń.

### <a name="key-deletion"></a>Usuwanie kluczy

Klaster powinien zostać usunięty przed usunięciem klucza.

### <a name="kv-access-policy-changed"></a>Zmieniono zasady dostępu kv

Regularne inspekcje i testowanie zasad dostępu.

### <a name="expired-key"></a>Wygasły klucz

* Śmiuj zapas kluczy do modułu HSM.
* Użyj klucza bez zestawu wygaśnięcia.
* Jeśli należy ustawić wygaśnięcie, obróć klucze przed datą wygaśnięcia.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
