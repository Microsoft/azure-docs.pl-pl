---
title: Limity i przydziały dotyczące usługi
description: Informacje o domyślnych przydziałach Azure Batch, limitach i ograniczeniach oraz o sposobach zwiększania limitu przydziału
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: seodec18
ms.openlocfilehash: 6e17a90cc573205bcb964a0428e0b7320323b8a6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553553"
---
# <a name="batch-service-quotas-and-limits"></a>Limity przydziału i limity usługi Batch

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów skojarzonych z usługą Batch. Wiele z tych limitów to domyślne Przydziały stosowane przez platformę Azure na poziomie subskrypcji lub konta.

Te przydziały należy uwzględnić podczas projektowania i skalowania obciążeń związanych z przetwarzaniem wsadowym. Na przykład jeśli Pula nie osiągnie docelowej liczby węzłów obliczeniowych, być może osiągnięto limit przydziału rdzeni dla konta usługi Batch.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozpowszechnić obciążenia między kontami usługi Batch, które znajdują się w tej samej subskrypcji, ale w różnych regionach platformy Azure.

Jeśli planujesz uruchamianie obciążeń produkcyjnych w usłudze Batch, może być konieczne zwiększenie co najmniej jednego przydziału powyżej wartości domyślnej. Aby podnieść limit przydziału, możesz [poprosić o zwiększenie limitu przydziału](#increase-a-quota) bez opłat.

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Przydział jest limitem, a nie gwarancją wydajności. Jeśli masz zapotrzebowanie na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure.

Należy również pamiętać, że limity przydziału nie są gwarantowane wartości. Przydziały mogą się różnić w zależności od zmian w usłudze Batch lub żądania użytkownika w celu zmiany wartości limitu przydziału.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Limity przydziału rdzeni

### <a name="cores-quotas-in-batch-service-mode"></a>Liczba przydziałów rdzeni w trybie usługi Batch

Istnieją przydziały podstawowe dla każdej serii maszyn wirtualnych obsługiwanej przez usługi Batch. Te przydziały podstawowe są wyświetlane na stronie **przydziały** w Azure Portal. Limity przydziału serii maszyn wirtualnych można aktualizować za pomocą żądania obsługi, jak opisano poniżej. W przypadku węzłów dedykowanych usługi Batch wymuszają podstawowe limity przydziału dla każdej serii maszyn wirtualnych, a także łączny limit przydziału rdzeni dla całego konta usługi Batch. W przypadku węzłów o niskim priorytecie zadanie wsadowe wymusza jedynie łączny limit przydziału rdzeni dla konta usługi Batch bez różnic między różnymi seriami maszyn wirtualnych.

### <a name="cores-quotas-in-user-subscription-mode"></a>Liczba rdzeni w trybie subskrypcji użytkownika

Jeśli utworzono konto usługi [Batch](accounts.md) z trybem alokacji puli ustawionym na **subskrypcję użytkownika**, maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli lub zmianie rozmiaru. Przydziały podstawowe Azure Batch nie mają zastosowania i przydziały w subskrypcji dla regionalnych rdzeni obliczeniowych, rdzeni obliczeniowych na serię i inne zasoby są używane i wymuszane.

Aby dowiedzieć się więcej na temat tych przydziałów, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limity rozmiaru puli

Limity rozmiaru puli są ustawiane przez usługę Batch. W przeciwieństwie do [przydziałów zasobów](#resource-quotas)te wartości nie mogą być zmieniane. Tylko pule z komunikacją między węzłami i obrazami niestandardowymi mają ograniczenia inne niż w przypadku standardowego limitu przydziału.

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| **Węzły obliczeniowe w [puli z włączoną obsługą komunikacji między węzłami](batch-mpi.md)**  ||
| Tryb alokacji puli usługi Batch | 100 |
| Tryb alokacji puli subskrypcji usługi Batch | 80 |
| **Węzły obliczeniowe w [puli utworzone przy użyciu zasobu obrazu zarządzanego](batch-custom-images.md)**<sup>1</sup> ||
| Węzły dedykowane | 2000 |
| Węzły o niskim priorytecie | 1000 |

<sup>1</sup> w przypadku pul, które nie obsługują komunikacji między węzłami.

## <a name="other-limits"></a>Inne limity

Te dodatkowe limity są ustawiane przez usługę Batch. W przeciwieństwie do [przydziałów zasobów](#resource-quotas)te wartości nie mogą być zmieniane.

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| [Zadania współbieżne](batch-parallel-node-tasks.md) na węzeł obliczeniowy | 4 x liczba rdzeni węzłów |
| [Aplikacje](batch-application-packages.md) na konto wsadowe | 20 |
| Pakiety aplikacji na aplikację | 40 |
| Pakiety aplikacji na pulę | 10 |
| Maksymalny okres istnienia zadania | 180 dni<sup>1</sup> |
| [Instalacje](virtual-file-mount.md) na węzeł obliczeniowy | 10 |
| Certyfikaty na pulę | 12 |

<sup>1</sup> maksymalny okres istnienia zadania, od momentu jego dodania do zadania do momentu jego zakończenia, to 180 dni. Zadania ukończone są przechowywane przez siedem dni; dane dla zadań nieukończonych w maksymalnym okresie istnienia są niedostępne.

## <a name="view-batch-quotas"></a>Wyświetlanie przydziałów partii

Aby wyświetlić limity przydziału konta w usłudze Batch w [Azure Portal](https://portal.azure.com):

1. Wybierz pozycję konta w usłudze **Batch**, a następnie wybierz interesujące Cię konto w usłudze Batch.
1. Wybierz pozycję **przydziały** w menu konta zadania wsadowego.
1. Wyświetl przydziały aktualnie stosowane do konta programu Batch.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Zrzut ekranu przedstawiający przydziały kont wsadowych w Azure Portal.":::

## <a name="increase-a-quota"></a>Zwiększ limit przydziału

Możesz poprosić o zwiększenie limitu przydziału dla konta usługi Batch lub subskrypcji przy użyciu [Azure Portal](https://portal.azure.com) lub [interfejsu API REST przydziałów platformy Azure](#azure-quota-rest-api).

Typ zwiększenia limitu przydziału zależy od trybu alokacji puli konta usługi Batch. Aby zażądać zwiększenia limitu przydziału, należy dołączyć serię maszyn wirtualnych, dla której chcesz zwiększyć przydział. Po zastosowaniu wzrostu przydziału zostanie on zastosowany do wszystkich serii maszyn wirtualnych.

Po przesłaniu żądania pomocy technicznej systemu Azure skontaktuje się z Tobą. Żądania limitu przydziału mogą być wykonywane w ciągu kilku minut lub do dwóch dni roboczych.

### <a name="azure-portal"></a>Azure Portal

1. Na stronie **przydziały** wybierz pozycję **Zwiększ przydział limitu przydziału**. Alternatywnie możesz wybrać kafelek **Pomoc i obsługa techniczna** na pulpicie nawigacyjnym portalu (lub z znaku zapytania (**?**) w prawym górnym rogu portalu), a następnie wybrać pozycję **nowe żądanie obsługi.**

1. Na stronie **Podstawy**:

    1. W obszarze **typ problemu** wybierz pozycję **usługi i limity subskrypcji (przydziały)**.
    1. Wybierz subskrypcję.
    1. W obszarze **Typ limitu przydziału** wybierz pozycję **Batch**.
    1. Wybierz przycisk **Dalej**, aby kontynuować.

1. W obszarze **Szczegóły**:

    1. W sekcji **Podaj szczegóły** Określ lokalizację, typ przydziału i konto wsadowe (jeśli dotyczy), a następnie wybierz przydziały do zwiększenia.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Zrzut ekranu przedstawiający ekran Szczegóły przydziału podczas żądania zwiększenia limitu przydziału.":::

       Typy przydziałów obejmują:

       - **Za konto wsadowe**  
         Użyj tej opcji, aby zażądać zwiększenia przydziału dla pojedynczego konta wsadowego, w tym dla rdzeni dedykowanych i o niskim priorytecie oraz liczby zadań i pul.

         W przypadku wybrania tej opcji Określ konto wsadowe, do którego ma być stosowane to żądanie, a następnie wybierz przydziały, które chcesz zaktualizować. Podaj nowy limit, który jest żądany dla każdego zasobu.

         Przydział o niskim priorytecie to pojedyncza wartość dla wszystkich serii maszyn wirtualnych. Jeśli potrzebujesz ograniczonych jednostek SKU, musisz wybrać **rdzenie o niskim priorytecie** i dołączyć rodziny maszyn wirtualnych do żądania.

       - **Wszystkie konta w tym regionie**  
         Użyj tej opcji, aby zażądać zwiększenia limitu przydziału, które mają zastosowanie do wszystkich kont usługi Batch w danym regionie, takich jak liczba kont usługi Batch na region na subskrypcję.

    1. W obszarze **Metoda pomocy technicznej** wybierz **ważność** zgodnie z Twoim [wpływem na firmę](https://aka.ms/supportseverity) oraz preferowaną metodę kontaktu i językiem pomocy technicznej.

    1. W polu **informacje kontaktowe** Sprawdź i wprowadź wymagane szczegóły dotyczące kontaktu.

1. Wybierz pozycję **Recenzja + Utwórz**, a następnie wybierz pozycję **Utwórz** , aby przesłać żądanie obsługi.

### <a name="azure-quota-rest-api"></a>Interfejs API REST przydziałów platformy Azure

Możesz użyć interfejsu API REST przydziałów platformy Azure, aby zażądać zwiększenia limitu przydziału na poziomie subskrypcji lub na poziomie konta usługi Batch.

Aby uzyskać szczegółowe informacje i przykłady, zobacz [żądanie zwiększenia limitu przydziału przy użyciu interfejsu API REST usługi Azure support](/rest/api/support/quota-payload#azure-batch).

## <a name="related-quotas-for-vm-pools"></a>Powiązane przydziały dla pul maszyn wirtualnych

[Pule usługi Batch w konfiguracji maszyny wirtualnej wdrożonej w sieci wirtualnej platformy Azure](batch-virtual-network.md) automatycznie przydzielą dodatkowe zasoby sieci platformy Azure. Te zasoby są tworzone w ramach subskrypcji zawierającej sieć wirtualną podaną podczas tworzenia puli usługi Batch.

Następujące zasoby są tworzone dla każdego węzła puli 100 w sieci wirtualnej:

- Jedna [sieciowa Grupa zabezpieczeń](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Jeden [publiczny adres IP](../virtual-network/public-ip-addresses.md)
- Jeden [moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md)

Te zasoby są ograniczone przez [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. Jeśli planujesz wdrożenia dużych pul w sieci wirtualnej, może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z tych zasobów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Dowiedz się więcej o [limitach subskrypcji i usług platformy Azure, przydziałów i ograniczeniach](../azure-resource-manager/management/azure-subscription-service-limits.md).