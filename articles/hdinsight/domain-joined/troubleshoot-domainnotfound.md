---
title: Tworzenie klastra kończy się niepowodzeniem z powodu błędu DomainNotFound w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas pracy z klastrami usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776237"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenariusz: Tworzenie klastra kończy się niepowodzeniem z powodu błędu DomainNotFound w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Tworzenie klastra HDI bezpiecznego (pakiet Enterprise Security) kończy się niepowodzeniem z `DomainNotFound` komunikatem o błędzie.

## <a name="cause"></a>Przyczyna

Nieprawidłowe ustawienia DNS.

## <a name="resolution"></a>Rozwiązanie

Po wdrożeniu klastrów przyłączonych do domeny HDI tworzy wewnętrzną nazwę użytkownika i hasło w usłudze AAD DS (dla każdego klastra) i łączy wszystkie węzły klastra z tą domeną. Przyłączanie do domeny jest realizowane przy użyciu narzędzi programu Samba. Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Nazwa domeny powinna być rozpoznawana przez system DNS.
* Adres IP kontrolerów domeny należy ustawić w ustawieniach DNS sieci wirtualnej, w której jest wdrażany klaster.
* Jeśli sieć wirtualna jest połączona z siecią wirtualną usługi AAD DS, należy ją wykonać ręcznie.
* Jeśli używasz usług przesyłania dalej DNS, nazwa domeny musi być poprawnie rozpoznawana w sieci wirtualnej.
* Zasady zabezpieczeń (sieciowych grup zabezpieczeń) nie powinny blokować przyłączania do domeny.

### <a name="additional-debugging-steps"></a>Dodatkowe kroki debugowania

* Wdróż maszynę wirtualną z systemem Windows w tej samej podsieci, Dołącz do niej domenę przy użyciu nazwy użytkownika i hasła (można to zrobić za pomocą interfejsu użytkownika panelu sterowania) lub

* Wdróż maszynę wirtualną Ubuntu w tej samej podsieci i domenie przyłączania do maszyny
  * Użyj protokołu SSH do maszyny
  * sudo Su
  * Uruchom skrypt przy użyciu nazwy użytkownika i hasła
  * Skrypt wyśle polecenie ping, utworzy wymagane pliki konfiguracji, a następnie domenę. Jeśli to się powiedzie, ustawienia DNS są prawidłowe.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
