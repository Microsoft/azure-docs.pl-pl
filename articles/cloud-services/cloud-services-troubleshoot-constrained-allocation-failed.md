---
title: Rozwiązywanie problemów z usługą ConstrainedAllocationFailed podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure | Microsoft Docs
description: W tym artykule pokazano, jak rozwiązać wyjątek ConstrainedAllocationFailed podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 4a92246f81ddd10840bb0dcf7edf2b01853fd148
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876602"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Rozwiązywanie problemów z usługą ConstrainedAllocationFailed podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure

W tym artykule rozwiążesz problemy z błędami alokacji, których nie można wdrożyć w usługach Azure Cloud Services (wersja klasyczna) z powodu ograniczeń alokacji.

Podczas wdrażania wystąpień w usłudze w chmurze (klasycznej) lub dodawania nowych wystąpień roli Sieć Web lub Proces roboczy Microsoft Azure zasoby obliczeniowe.

Czasami możesz otrzymywać błędy podczas tych operacji nawet przed osiągnięciem limitu subskrypcji platformy Azure.

> [!TIP]
> Informacje te mogą być również przydatne podczas planowania wdrożenia usług.

## <a name="symptom"></a>Objaw

W Azure Portal przejdź do usługi w chmurze (klasycznej) i na pasku bocznym wybierz pozycję *Dziennik operacji (klasyczny),* aby wyświetlić dzienniki.

![Obraz przedstawia blok Dziennik operacji (klasyczny).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Podczas inspekcji dzienników usługi w chmurze (klasycznej) zostanie wyświetlony następujący wyjątek:

|Typ wyjątku  |Komunikat o błędzie  |
|---------|---------|
|ConstrainedAllocationFailed     |Operacja platformy Azure " `{Operation ID}` " nie powiodła się z kodem Compute.ConstrainedAllocationFailed. Szczegóły: Alokacja nie powiodła się; nie można spełnić ograniczeń w żądaniu. Zażądane nowe wdrożenie usługi jest związane z grupą koligacji, dotyczy sieci wirtualnej albo w ramach tej usługi hostowanej występuje już wdrożenie. Każdy z tych warunków ogranicza nowe wdrożenie do określonych zasobów platformy Azure. Spróbuj ponownie później lub spróbuj zmniejszyć rozmiar maszyny wirtualnej lub liczbę wystąpień roli. Ewentualnie usuń w miarę możliwości wspomniane ograniczenia lub spróbuj wdrożyć w innym regionie.|

## <a name="cause"></a>Przyczyna

Gdy pierwsze wystąpienie jest wdrażane w usłudze w chmurze (w środowisku przejściowym lub produkcyjnym), usługa w chmurze jest przypinana do klastra.

Z czasem zasoby w tym klastrze mogą stać się w pełni wykorzystywane. Jeśli usługa w chmurze (klasyczna) wykonuje żądanie alokacji dotyczące większej ilości zasobów, gdy w przypiętym klastrze są niewystarczające zasoby, żądanie spowoduje błąd alokacji. Aby uzyskać więcej informacji, zobacz typowe problemy [z błędami alokacji.](cloud-services-allocation-failures.md#common-issues)

## <a name="solution"></a>Rozwiązanie

Istniejące usługi w chmurze *są przypinane* do klastra. Wszelkie dalsze wdrożenia usługi w chmurze (klasycznej) będą odbywać się w tym samym klastrze.

Jeśli w tym scenariuszu wystąpi błąd alokacji, zalecanym kursem działania jest ponowne uaktualnienie do nowej usługi w chmurze (klasycznej) (i zaktualizowanie *rekordu CNAME).*

> [!TIP]
> To rozwiązanie ma największą szansę na powodzenie, ponieważ pozwala platformie na wybór spośród wszystkich klastrów w tym regionie.

> [!NOTE]
> To rozwiązanie powinno nie wiązać się z przestojami.

1. Wdrażanie obciążenia w nowej usłudze w chmurze (klasycznej).
    - Dalsze instrukcje można znaleźć w przewodniku How [to create and deploy a Cloud service (classic) (Jak](cloud-services-how-to-create-deploy-portal.md) utworzyć i wdrożyć usługę w chmurze (klasyczną).

    > [!WARNING]
    > Jeśli nie chcesz utracić adresu IP skojarzonego z tym miejscem wdrożenia, możesz użyć rozwiązania 3 — zachowaj [adres IP.](cloud-services-allocation-failures.md#solutions)

1. Zaktualizuj rekord *CNAME* lub *A,* aby wskazać ruch do nowej usługi w chmurze (klasycznej).
    - Dalsze instrukcje można znaleźć w przewodniku Konfigurowanie niestandardowej nazwy domeny dla usługi [Azure Cloud Service (wersja](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) klasyczna).

1. Gdy zerowy ruch będzie trafiać do starej witryny, możesz usunąć starą usługę w chmurze (klasyczną).
    - Dalsze [instrukcje można znaleźć w](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) przewodniku Usuwanie wdrożeń i usługi w chmurze (wersja klasyczna).
    - Aby wyświetlić ruch sieciowy w usłudze w chmurze (klasycznej), zobacz Wprowadzenie do monitorowania usługi [w chmurze (klasycznej).](cloud-services-how-to-monitor.md)

Zobacz Troubleshooting Cloud service (classic) allocation failures (Rozwiązywanie problemów [z błędami alokacji usługi w chmurze (klasycznej) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) dalsze kroki korygowania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązań błędów alokacji i informacji w tle:

> [!div class="nextstepaction"]
> [Błędy alokacji — usługa w chmurze (klasyczna)](cloud-services-allocation-failures.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w witrynie [MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Swój problem możesz opublikować na tych forach lub opublikować w [ @AzureSupport serwisie Twitter.](https://twitter.com/AzureSupport) Możesz również przesłać wniosek o pomoc techniczną platformy Azure. Aby przesłać wniosek o pomoc techniczną, na stronie pomocy technicznej [platformy Azure](https://azure.microsoft.com/support/options/) wybierz pozycję Uzyskaj *pomoc techniczną.*