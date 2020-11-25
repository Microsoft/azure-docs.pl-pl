---
title: Nie można dodać węzłów do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów z nie można dodać węzłów do Apache Hadoop klastra w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014673"
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