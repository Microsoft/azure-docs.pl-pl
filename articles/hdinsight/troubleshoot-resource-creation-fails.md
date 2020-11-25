---
title: Rozwiązywanie problemów z błędami tworzenia zasobów w usłudze Azure HDInsight
description: W tym artykule przedstawiono błędy typowych problemów z pojemnością i techniki zaradcze.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 2e9f6898dba559f557f49cc0ee9ff39644da243f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000695"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Rozwiązywanie problemów z błędami tworzenia zasobów w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących podczas korzystania z usługi Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Błąd: wdrożenie spowodowałoby przekroczenie limitu przydziału "800"

Platforma Azure ma limit przydziału, który wynosi 800 wdrożeń na grupę zasobów. Do poszczególnych grup zasobów, subskrypcji, kont i innych zakresów są stosowane różne przydziały. Na przykład subskrypcja może być skonfigurowana w taki sposób, aby ograniczyć liczbę rdzeni dla regionu. Podjęto próbę wdrożenia maszyny wirtualnej o większej liczbie rdzeni niż dozwolone wyniki w komunikacie o błędzie z informacją o przekroczeniu limitu przydziału.

Aby rozwiązać ten problem, Usuń wdrożenia, które nie są już potrzebne, przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell.

Aby uzyskać więcej informacji, zobacz [Resolve errors for resource quotas (Rozwiązywanie błędów z limitami przydziałów zasobów)](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Błąd: maksymalny węzeł przekracza dostępne rdzenie w tym regionie

Twoja subskrypcja może być skonfigurowana w taki sposób, aby ograniczyć liczbę rdzeni dla regionu. Próba wdrożenia zasobu o większej liczbie rdzeni niż dozwolone wyniki w komunikacie o błędzie z informacją o przekroczeniu limitu przydziału.

Aby zażądać zwiększenia limitu przydziału, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com)i wybierz pozycję **Pomoc i obsługa techniczna**.

1. Wybierz pozycję **Nowe żądanie obsługi**.

1. Na karcie **podstawowe** na stronie **nowe żądanie obsługi** podaj następujące informacje:

   * **Typ problemu:** Wybierz pozycję **usługi i limity subskrypcji (przydziały)**.
   * **Subskrypcja:** Wybierz subskrypcję, którą chcesz zmodyfikować.
   * **Typ limitu przydziału:** Wybierz pozycję **HDInsight**.

Aby uzyskać więcej informacji, zobacz [Tworzenie biletu pomocy technicznej w celu zwiększenia liczby rdzeni](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]