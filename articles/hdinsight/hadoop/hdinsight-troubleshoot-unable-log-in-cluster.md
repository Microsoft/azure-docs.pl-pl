---
title: Nie można zalogować się do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów z nie można zalogować się do klastra Apache Hadoop w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944315"
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