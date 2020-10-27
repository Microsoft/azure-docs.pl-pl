---
title: Nie można zalogować się do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów z nie można zalogować się do klastra Apache Hadoop w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 059fee89fdb7f0bbc046c98e36c6386d06653fff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540418"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenariusz: nie można zalogować się do klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można zalogować się do klastra usługi Azure HDInsight.

## <a name="cause"></a>Przyczyna

Przyczyny mogą się różnić. Należy pamiętać, że podczas logowania się do pulpitów nawigacyjnych klastra lub aplikacji należy użyć poświadczeń logowania klastra lub protokołu HTTP. Podczas zdalnego nawiązywania połączenia użyj poświadczeń Secure Shell (SSH) lub poświadczeń pulpitu zdalnego.

## <a name="resolution"></a>Rozwiązanie

Aby rozwiązać typowe problemy, spróbuj wykonać poniższe czynności.

* Spróbuj otworzyć pulpit nawigacyjny klastra na nowej karcie przeglądarki w trybie prywatności.

* Jeśli nie można odwołać poświadczeń SSH, można [zresetować poświadczenia w interfejsie użytkownika Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).