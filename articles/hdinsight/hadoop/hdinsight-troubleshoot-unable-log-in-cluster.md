---
title: Nie można zalogować się do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów z nie można zalogować się do klastra Apache Hadoop w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289019"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]