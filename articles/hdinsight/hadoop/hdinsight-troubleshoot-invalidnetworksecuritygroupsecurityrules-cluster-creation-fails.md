---
title: Błąd InvalidNetworkSecurityGroupSecurityRules — usługa Azure HDInsight
description: Tworzenie klastra kończy się niepowodzeniem z błędem ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 7e0b984b3ec4a203f8a1118c0e6a166c5a9e1125
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944378"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenariusz: InvalidNetworkSecurityGroupSecurityRules — Tworzenie klastra kończy się niepowodzeniem w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Kod błędu jest wyświetlany `InvalidNetworkSecurityGroupSecurityRules` z opisem podobnym do "reguły zabezpieczeń w sieciowej grupie zabezpieczeń skonfigurowanej z podsiecią, nie zezwala na dostęp do wymaganych połączeń przychodzących i/lub wychodzących".

## <a name="cause"></a>Przyczyna

Przyczyną jest problem z regułami [sieciowej grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) dla ruchu przychodzącego skonfigurowanych dla klastra.

## <a name="resolution"></a>Rozwiązanie

Przejdź do Azure Portal i zidentyfikuj sieciowej grupy zabezpieczeń, która jest skojarzona z podsiecią, w której jest wdrażany klaster. W sekcji **reguły zabezpieczeń ruchu przychodzącego** upewnij się, że reguły zezwalają na dostęp przychodzący do portu 443 dla adresów IP wymienionych w [tym miejscu](../control-network-traffic.md).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]