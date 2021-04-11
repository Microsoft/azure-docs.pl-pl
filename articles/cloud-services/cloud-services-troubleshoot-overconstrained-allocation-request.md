---
title: Rozwiązywanie problemów z OverconstrainedAllocationRequest podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure | Microsoft Docs
description: W tym artykule opisano sposób rozwiązywania wyjątku OverconstrainedAllocationRequest podczas wdrażania usługi w chmurze (klasycznej) na platformie Azure.
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746647"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Rozwiązywanie problemów z OverconstrainedAllocationRequest podczas wdrażania usług Cloud Services (Classic) na platformie Azure

W tym artykule opisano Rozwiązywanie problemów dotyczących nieograniczonego przydziału błędów, które uniemożliwiają wdrożenie Cloud Services platformy Azure (klasyczne).

W przypadku wdrażania wystąpień do usługi w chmurze lub dodawania nowych wystąpień roli sieci Web lub procesu roboczego Microsoft Azure przydziela zasoby obliczeniowe.

Czasami możesz otrzymywać błędy podczas tych operacji nawet przed osiągnięciem limitu subskrypcji platformy Azure.

> [!TIP]
> Te informacje mogą być również przydatne podczas planowania wdrożenia usług.

## <a name="symptom"></a>Objaw

![Obraz przedstawia blok dziennik operacji (klasyczny).](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Typ wyjątku  |Komunikat o błędzie  |
|---------|---------|
|OverconstrainedAllocationRequest |Nie można zainicjować obsługi rozmiaru maszyny wirtualnej (lub kombinacji rozmiarów maszyn wirtualnych) wymaganej przez to wdrożenie z powodu ograniczeń żądania wdrożenia. Jeśli to możliwe, spróbuj użyć ograniczeń złagodzeniu wymagań dotyczących, takich jak powiązania sieci wirtualnej, wdrożyć w hostowanej usłudze bez innych wdrożeń oraz do innej grupy koligacji lub bez żadnej grupy koligacji lub spróbuj przeprowadzić wdrożenie w innym regionie.|

## <a name="cause"></a>Przyczyna

Główna przyczyna zależy od tego, czy usługa w chmurze jest **przypięta** , czy **nie**.

- [**Nie przypięto:** Błędy z pierwszego wdrożenia nowej usługi w chmurze (klasycznej)](#not-pinned-to-a-cluster)
- [**Przypięty:** Błędy istniejącej usługi w chmurze (klasycznej)](#pinned-to-a-cluster)

> [!NOTE]
> Po wdrożeniu pierwszego wystąpienia w usłudze w chmurze (w ramach przemieszczania lub produkcji) usługa w chmurze jest przypięta do klastra.
>
> W miarę upływu czasu zasoby w klastrze mogą być w pełni wykorzystane. Jeśli usługa w chmurze (klasyczna) wykonuje żądanie alokacji dodatkowych zasobów, gdy w przypiętym klastrze są dostępne niewystarczające zasoby, żądanie spowoduje [Niepowodzenie alokacji](cloud-services-allocation-failures.md).

## <a name="solution"></a>Rozwiązanie

Postępuj zgodnie ze wskazówkami dotyczącymi błędów alokacji w następujących scenariuszach.

### <a name="not-pinned-to-a-cluster"></a>Przypięty do klastra

Przy pierwszym wdrożeniu usługi w chmurze (klasycznej) klaster nie został jeszcze wybrany, więc usługa w chmurze nie jest *przypięta*. Na platformie Azure może wystąpić błąd wdrażania, ponieważ:

- Wybrano konkretny rozmiar, który nie jest dostępny w regionie.
- Kombinacja rozmiarów, które są zbędne w różnych rolach, nie jest dostępna w regionie.

W przypadku wystąpienia błędu alokacji w tym scenariuszu zalecanym sposobem działania jest sprawdzenie dostępnych rozmiarów w regionie i zmiana określonego wcześniej rozmiaru.

1. Dostępne rozmiary w regionie można sprawdzić na stronie [produktów w chmurze (klasycznych)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) .

    > [!NOTE]
    > Na stronie *produkty* nie zostanie wyświetlona dostępna pojemność. W przypadku każdej nowej alokacji platforma Azure powinna być w stanie wybrać optymalny klaster w Twoim regionie w tym momencie.

1. Zaktualizuj plik definicji usługi dla usługi w chmurze (klasyczny), aby określić inny [Rozmiar produktu](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) w Twoim regionie.

### <a name="pinned-to-a-cluster"></a>Przypięte do klastra

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
