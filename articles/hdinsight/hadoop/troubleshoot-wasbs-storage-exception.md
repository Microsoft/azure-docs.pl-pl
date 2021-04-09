---
title: Konto, do którego uzyskiwany jest dostęp, nie obsługuje błędu HTTP w usłudze Azure HDInsight
description: W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943085"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Konto, do którego uzyskiwany jest dostęp, nie obsługuje błędu HTTP w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Przyczyna

Istnieje wiele powodów, dla których komunikat o błędzie został odebrany:

* Konto magazynu ma włączony [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md) i jest używany nieprawidłowy [schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) .

* Klaster został utworzony przy użyciu konta magazynu, które spowodowało *wyłączenie* bezpiecznego transferu. Następnie włączono bezpieczny transfer na koncie magazynu.

## <a name="resolution"></a>Rozwiązanie

Jeśli włączono bezpieczny transfer dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI będzie `wasbs://` odpowiednio lub `abfss://` .  Zobacz również [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

W przypadku nowych klastrów Użyj konta magazynu, które ma już odpowiednie ustawienie bezpiecznego transferu. Nie należy zmieniać ustawienia bezpiecznego transferu dla konta magazynu używanego przez istniejący klaster.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).