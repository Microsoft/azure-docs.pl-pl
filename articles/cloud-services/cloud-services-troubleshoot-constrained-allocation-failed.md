---
title: Rozwiązywanie problemów z ConstrainedAllocationFailed podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure | Microsoft Docs
description: W tym artykule opisano sposób rozwiązywania wyjątku ConstrainedAllocationFailed podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738291"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Rozwiązywanie problemów z ConstrainedAllocationFailed podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure

W tym artykule opisano Rozwiązywanie problemów z błędami alokacji w przypadku, gdy usługi Azure Cloud Services (klasyczny) nie mogą zostać wdrożone ze względu na ograniczenia alokacji.

W przypadku wdrażania wystąpień do usługi w chmurze (klasycznej) lub dodawania nowych wystąpień roli sieci Web lub procesu roboczego Microsoft Azure przydziela zasoby obliczeniowe.

Czasami możesz otrzymywać błędy podczas tych operacji nawet przed osiągnięciem limitu subskrypcji platformy Azure.

> [!TIP]
> Te informacje mogą być również przydatne podczas planowania wdrożenia usług.

## <a name="symptom"></a>Objaw

W Azure Portal przejdź do usługi w chmurze (klasycznej), a następnie na pasku bocznym wybierz pozycję *dziennik operacji (klasyczny)* , aby wyświetlić dzienniki.

![Obraz przedstawia blok dziennik operacji (klasyczny).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Podczas inspekcji dzienników usługi w chmurze (klasycznej) zobaczysz następujący wyjątek:

|Typ wyjątku  |Komunikat o błędzie  |
|---------|---------|
|ConstrainedAllocationFailed     |Operacja platformy Azure " `{Operation ID}` " nie powiodła się z kodem COMPUTE. ConstrainedAllocationFailed. Szczegóły: Alokacja nie powiodła się; nie można spełnić ograniczeń w żądaniu. Zażądane nowe wdrożenie usługi jest związane z grupą koligacji, dotyczy sieci wirtualnej albo w ramach tej usługi hostowanej występuje już wdrożenie. Każdy z tych warunków ogranicza nowe wdrożenie do określonych zasobów platformy Azure. Spróbuj ponownie później lub spróbuj zmniejszyć rozmiar maszyny wirtualnej lub liczbę wystąpień roli. Ewentualnie usuń w miarę możliwości wspomniane ograniczenia lub spróbuj wdrożyć w innym regionie.|

## <a name="cause"></a>Przyczyna

Po wdrożeniu pierwszego wystąpienia w usłudze w chmurze (w ramach przemieszczania lub produkcji) usługa w chmurze jest przypięta do klastra.

W miarę upływu czasu zasoby w tym klastrze mogą być w pełni wykorzystane. Jeśli usługa w chmurze (klasyczna) wykonuje żądanie alokacji w celu uzyskania większej ilości zasobów w przypadku, gdy w przypiętym klastrze są dostępne niewystarczające zasoby, żądanie spowoduje niepowodzenie alokacji. Aby uzyskać więcej informacji, zobacz [typowe problemy związane z błędami alokacji](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Rozwiązanie

Istniejące usługi w chmurze są *przypięte* do klastra. Wszelkie dalsze wdrożenia usługi w chmurze (klasycznej) będą wykonywane w tym samym klastrze.

W przypadku wystąpienia błędu alokacji w tym scenariuszu zalecanym sposobem działania jest ponowne wdrożenie do nowej usługi w chmurze (klasycznej) (i aktualizacja *rekordu CNAME*).

> [!TIP]
> To rozwiązanie ma największą szansę na powodzenie, ponieważ pozwala platformie na wybór spośród wszystkich klastrów w tym regionie.

> [!NOTE]
> To rozwiązanie powinno mieć zero przestojów.

1. Wdróż obciążenie w nowej usłudze w chmurze (klasycznej).
    - Dalsze instrukcje można znaleźć w przewodniku [jak utworzyć i wdrożyć usługę w chmurze (klasyczną)](cloud-services-how-to-create-deploy-portal.md) .

    > [!WARNING]
    > Jeśli nie chcesz stracić adresu IP skojarzonego z tym miejscem wdrożenia, możesz użyć [rozwiązania 3 — Zachowaj adres IP](cloud-services-allocation-failures.md#solutions).

1. Zaktualizuj rekord *CNAME* lub *A* , aby wskazywał ruch do nowej usługi w chmurze (klasycznej).
    - Dalsze instrukcje można znaleźć w temacie [Konfigurowanie niestandardowej nazwy domeny dla przewodnika usługi Azure Cloud Service (klasyczny)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) .

1. Gdy zerowy ruch przechodzi do starej lokacji, można usunąć starą usługę w chmurze (klasyczną).
    - Dalsze instrukcje można znaleźć w przewodniku [usuwanie wdrożeń i usługi w chmurze (klasycznej)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) .
    - Aby wyświetlić ruch sieciowy w usłudze w chmurze (klasyczny), zobacz [wprowadzenie do monitorowania usługi w chmurze (klasycznego)](cloud-services-how-to-monitor.md).

Zobacz [Rozwiązywanie problemów z błędami alokacji usługi w chmurze (klasycznej) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) w celu wykonania dalszych czynności naprawczych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o rozwiązaniach błędów alokacji i tle:

> [!div class="nextstepaction"]
> [Błędy alokacji — usługa w chmurze (klasyczna)](cloud-services-allocation-failures.md)

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź fora platformy Azure w [witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz ogłosić swój problem na tych forach lub opublikować go w serwisie [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Możesz również przesłać żądanie pomocy technicznej platformy Azure. Aby przesłać żądanie pomocy technicznej, na stronie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) wybierz pozycję *Uzyskaj pomoc techniczną*.