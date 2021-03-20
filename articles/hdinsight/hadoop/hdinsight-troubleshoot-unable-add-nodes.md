---
title: Nie można dodać węzłów do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów z nie można dodać węzłów do Apache Hadoop klastra w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944334"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenariusz: nie można dodać węzłów do klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można dodać węzłów do klastra usługi Azure HDInsight.

## <a name="cause"></a>Przyczyna

Przyczyny mogą się różnić.

## <a name="resolution"></a>Rozwiązanie

Korzystając z funkcji [rozmiar klastra](../hdinsight-scaling-best-practices.md) , Oblicz liczbę dodatkowych rdzeni wymaganych przez klaster. Zależy to od łącznej liczby rdzeni w nowych węzłach procesów roboczych. Następnie spróbuj wykonać co najmniej jedną z następujących czynności:

* Sprawdź, czy są dostępne rdzenie w lokalizacji klastra.

* Sprawdź liczbę dostępnych rdzeni w innych lokalizacjach. Rozważ ponowne utworzenie klastra w innej lokalizacji z odpowiednią liczbą dostępnych rdzeni.

* Aby zwiększyć limit przydziału rdzeni dla określonej lokalizacji, wyślij bilet pomocy technicznej dotyczący zwiększenia limitu przydziału rdzeni w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]