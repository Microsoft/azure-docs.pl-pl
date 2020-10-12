---
title: Skalowanie serwera hiveserver2 w usłudze Azure HDInsight
description: Skalowanie w poziomie serwera hiveserver2 w klastrach usługi Azure HDInsight przy użyciu węzłów brzegowych w celu zwiększenia odporności na uszkodzenia i dostępności.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227399"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>Skalowanie serwera hiveserver2 w klastrach usługi Azure HDInsight w celu zapewnienia wysokiej dostępności

Dowiedz się, jak wdrożyć dodatkowe serwera hiveserver2 w klastrze w celu zwiększenia dostępności i dystrybucji obciążeń. W przypadku zwiększenia rozmiaru węzła głównego można także użyć węzłów krawędzi do wdrożenia serwera hiveserver2. 

> [!NOTE]
> W zależności od użycia zwiększenie liczby serwera hiveserver2 może zwiększyć liczbę połączeń z magazyn metadanych Hive. Upewnij się również, że baza danych Azure SQL Database ma odpowiednie rozmiary.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przewodnika, należy zapoznać się z następującym artykułem:
- [Używanie pustych węzłów brzegowych w klastrach Apache Hadoop w usłudze HDInsight](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>Zainstaluj serwera hiveserver2

W tej sekcji zainstalujesz dodatkowe serwera hiveserver2 na hostach docelowych.

1. Otwórz Ambari w przeglądarce i kliknij hosta docelowego.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Menu hosts elementu Ambari.":::

2. Kliknij przycisk Dodaj, a następnie kliknij pozycję serwera hiveserver2

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Menu hosts elementu Ambari.":::

3. Potwierdź, a proces zostanie uruchomiony. Powtórz 1-3 dla wszystkich żądanych hostów.

4. Po zakończeniu instalacji uruchom ponownie wszystkie usługi ze starymi konfiguracjami i uruchom serwera hiveserver2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Menu hosts elementu Ambari.":::

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób instalowania serwera hiveserver2 w klastrze. Aby dowiedzieć się więcej o węzłach i aplikacjach brzegowych, zobacz następujące artykuły:

* [Zainstaluj węzeł brzegowy](hdinsight-apps-use-edge-node.md): Dowiedz się, jak zainstalować węzeł brzegowy w klastrze usługi HDInsight.
* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md): dowiedz się, jak instalować aplikacje usługi HDInsight w klastrach.
* [Limity połączeń jednostek DTU usługi Azure SQL](../azure-sql/database/resource-limits-dtu-single-databases.md): informacje na temat limitów usługi Azure SQL Database przy użyciu jednostek DTU.
* [Limity połączeń usługi Azure SQL rdzeń wirtualny](../azure-sql/database/resource-limits-vcore-elastic-pools.md): informacje na temat limitów usługi Azure SQL Database przy użyciu rdzeni wirtualnych.
