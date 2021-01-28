---
title: Tworzenie klastra kończy się niepowodzeniem z powodu błędu DomainNotFound w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas pracy z klastrami usługi Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 54096e6d1741a7be9f50d4b4f7f0fcabbe880588
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943359"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]