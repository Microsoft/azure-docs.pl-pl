---
title: Limity i przydziały dotyczące usługi
description: Informacje o domyślnych przydziałach Azure Batch, limitach i ograniczeniach oraz o sposobach zwiększania limitu przydziału
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 8ca08d43f07633b58cf6f7067c1a8fcd58350678
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107542"
---
# <a name="batch-service-quotas-and-limits"></a>Limity przydziału i limity usługi Batch

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów skojarzonych z usługą Batch. Wiele z tych limitów to domyślne Przydziały stosowane przez platformę Azure na poziomie subskrypcji lub konta.

Te przydziały należy uwzględnić podczas projektowania i skalowania obciążeń związanych z przetwarzaniem wsadowym. Na przykład jeśli Pula nie osiągnie docelowej liczby węzłów obliczeniowych, być może osiągnięto limit przydziału rdzeni dla konta usługi Batch.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozpowszechnić obciążenia między kontami usługi Batch, które znajdują się w tej samej subskrypcji, ale w różnych regionach platformy Azure.

Jeśli planujesz uruchamianie obciążeń produkcyjnych w usłudze Batch, może być konieczne zwiększenie co najmniej jednego przydziału powyżej wartości domyślnej. Jeśli chcesz podnieść limit przydziału, możesz bezpłatnie otworzyć [żądanie obsługi klienta](#increase-a-quota) w trybie online.

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Przydział jest limitem kredytowym, a nie gwarancją wydajności. Jeśli masz zapotrzebowanie na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure.

Należy również pamiętać, że limity przydziału nie są gwarantowane wartości. Przydziały mogą się różnić w zależności od zmian w usłudze Batch lub żądania użytkownika w celu zmiany wartości limitu przydziału.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Liczba rdzeni w trybie subskrypcji użytkownika

Jeśli utworzono konto usługi [Batch](accounts.md) z trybem alokacji puli ustawionym na **subskrypcję użytkownika**, przydziały są stosowane inaczej. W tym trybie maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Przydziały rdzeni Azure Batch nie mają zastosowania do konta utworzonego w tym trybie. Zamiast tego przydziały w ramach subskrypcji dla regionalnych rdzeni obliczeniowych i innych zasobów są stosowane.

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

Dodatkowe limity ustawiane przez usługę Batch. W przeciwieństwie do [przydziałów zasobów](#resource-quotas)te wartości nie mogą być zmieniane.

| **Zasób** | **Limit maksymalny** |
| --- | --- |
| [Zadania współbieżne](batch-parallel-node-tasks.md) na węzeł obliczeniowy | 4 x liczba rdzeni węzłów |
| [Aplikacje](batch-application-packages.md) na konto wsadowe | 20 |
| Pakiety aplikacji na aplikację | 40 |
| Pakiety aplikacji na pulę | 10 |
| Maksymalny okres istnienia zadania | 180 dni<sup>1</sup> |
| [Instalacje](virtual-file-mount.md) na węzeł obliczeniowy | 10 |

<sup>1</sup> maksymalny okres istnienia zadania, od momentu jego dodania do zadania do momentu jego zakończenia, to 180 dni. Zadania ukończone są przechowywane przez siedem dni; dane dla zadań nieukończonych w maksymalnym okresie istnienia są niedostępne.

## <a name="view-batch-quotas"></a>Wyświetlanie przydziałów partii

Aby wyświetlić limity przydziału konta w usłudze Batch w [Azure Portal](https://portal.azure.com):

1. Wybierz pozycję konta w usłudze **Batch**, a następnie wybierz interesujące Cię konto w usłudze Batch.
1. Wybierz pozycję **przydziały** w menu konta zadania wsadowego.
1. Wyświetl przydziały aktualnie stosowane do konta programu Batch.

    ![Przydziały kont wsadowych][account_quotas]

## <a name="increase-a-quota"></a>Zwiększ limit przydziału

Możesz poprosić o zwiększenie limitu przydziału dla konta usługi Batch lub subskrypcji przy użyciu [Azure Portal](https://portal.azure.com). Typ zwiększenia limitu przydziału zależy od trybu alokacji puli konta usługi Batch. Aby zażądać zwiększenia limitu przydziału, należy dołączyć serię maszyn wirtualnych, dla której chcesz zwiększyć przydział. Po zastosowaniu wzrostu przydziału zostanie on zastosowany do wszystkich serii maszyn wirtualnych.

1. Wybierz kafelek **Pomoc i obsługa techniczna** na pulpicie nawigacyjnym portalu lub znak zapytania (**?**) w prawym górnym rogu portalu.
1. Wybierz pozycję **nowe**  >  **Informacje podstawowe**żądania obsługi.
1. **Podstawowe informacje**:
   
    1. **Typ problemu**  >  **Limity usług i subskrypcji (przydziały)**
   
    1. Wybierz subskrypcję.
   
    1. **Typ**  >  limitu przydziału **Partia zadań**
      
       Wybierz pozycję **Dalej**.
    
1. W obszarze **Szczegóły**:
      
    1. W obszarze **Podaj szczegóły**Określ lokalizację, typ przydziału i konto w usłudze Batch.
    
       ![Wzrost przydziału partii][quota_increase]

       Typy przydziałów obejmują:

       * **Za konto wsadowe**  
         Wartości specyficzne dla pojedynczego konta partii, w tym dedykowane i o niskim priorytecie rdzenie oraz liczbę zadań i pul.
        
       * **Na region**  
         Wartości, które mają zastosowanie do wszystkich kont usługi Batch w regionie i obejmują liczbę kont usługi Batch na region na subskrypcję.

       Przydział o niskim priorytecie to pojedyncza wartość dla wszystkich serii maszyn wirtualnych. Jeśli potrzebujesz ograniczonych jednostek SKU, musisz wybrać **rdzenie o niskim priorytecie** i dołączyć rodziny maszyn wirtualnych do żądania.

    1. Wybierz **wagę** zgodną z Twoim [wpływem na firmę](https://aka.ms/supportseverity).

       Wybierz pozycję **Dalej**.

1. W **informacjach kontaktowych**:
   
    1. Wybierz **preferowaną metodę kontaktu**.
   
    1. Sprawdź i wprowadź wymagane szczegóły kontaktu.
   
       Wybierz pozycję **Utwórz** , aby przesłać żądanie obsługi.

Po przesłaniu żądania pomocy technicznej systemu Azure skontaktuje się z Tobą. Żądania limitu przydziału mogą być wykonywane w ciągu kilku minut lub do dwóch dni roboczych.

## <a name="related-quotas-for-vm-pools"></a>Powiązane przydziały dla pul maszyn wirtualnych

Pule usługi Batch w konfiguracji maszyny wirtualnej wdrożonej w sieci wirtualnej platformy Azure automatycznie przydzielą dodatkowe zasoby sieci platformy Azure. W przypadku każdego węzła puli 50 w sieci wirtualnej są niezbędne następujące zasoby:

- Jedna [sieciowa Grupa zabezpieczeń](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Jeden [publiczny adres IP](../virtual-network/public-ip-addresses.md)
- Jeden [moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md)

Te zasoby są przydzieleni do subskrypcji zawierającej sieć wirtualną podaną podczas tworzenia puli usługi Batch. Te zasoby są ograniczone przez [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. Jeśli planujesz wdrożenia dużych pul w sieci wirtualnej, sprawdź przydziały dla tych zasobów. W razie konieczności Poproś o zwiększenie Azure Portal, wybierając pozycję **Pomoc i obsługa techniczna**.

## <a name="next-steps"></a>Następne kroki

* [Utwórz konto Azure Batch przy użyciu Azure Portal](batch-account-create-portal.md).
* Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
* Dowiedz się więcej o [limitach subskrypcji i usług platformy Azure, przydziałów i ograniczeniach](../azure-resource-manager/management/azure-subscription-service-limits.md).

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png