---
title: Limity i przydziały dotyczące usługi
description: Informacje o domyślnych przydziałach Azure Batch, limitach i ograniczeniach oraz o sposobach zwiększania limitu przydziału
ms.topic: conceptual
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: f47f2df38a601c6359ed257a9787130ea95d2ac8
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726574"
---
# <a name="batch-service-quotas-and-limits"></a>Limity przydziału i limity usługi Batch

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów skojarzonych z usługą Batch. Wiele z tych limitów to domyślne Przydziały stosowane przez platformę Azure na poziomie subskrypcji lub konta. W tym artykule omówiono te ustawienia domyślne oraz sposób, w jaki można zażądać zwiększenia limitu przydziału.

Te przydziały należy uwzględnić podczas projektowania i skalowania obciążeń związanych z przetwarzaniem wsadowym. Na przykład jeśli Pula nie osiągnie docelowej liczby węzłów obliczeniowych, być może osiągnięto limit przydziału rdzeni dla konta usługi Batch.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia pomiędzy konta tej usługi znajdujące się w jednej subskrypcji, ale różnych regionach świadczenia usługi Azure.

Jeśli planujesz uruchamianie obciążeń produkcyjnych w usłudze Batch, może być konieczne zwiększenie co najmniej jednego przydziału powyżej wartości domyślnej. Jeśli chcesz podnieść limit przydziału, możesz bezpłatnie otworzyć [żądanie obsługi klienta](#increase-a-quota) w trybie online.

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Przydział jest limitem kredytowym, a nie gwarancją wydajności. Jeśli masz zapotrzebowanie na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure.

Należy również pamiętać, że limity przydziału nie są gwarantowane wartości. Przydziały mogą się różnić w zależności od zmian w usłudze Batch lub żądania użytkownika w celu zmiany wartości limitu przydziału.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Liczba rdzeni w trybie subskrypcji użytkownika

Jeśli utworzono konto usługi Batch z trybem alokacji puli ustawionym na **subskrypcję użytkownika**, przydziały są stosowane inaczej. W tym trybie maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Przydziały rdzeni Azure Batch nie mają zastosowania do konta utworzonego w tym trybie. Zamiast tego przydziały w ramach subskrypcji dla regionalnych rdzeni obliczeniowych i innych zasobów są stosowane. Dowiedz się więcej na temat tych przydziałów w [ramach subskrypcji platformy Azure i ograniczeń usługi, przydziałów i ograniczeń](../azure-resource-manager/management/azure-subscription-service-limits.md).

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

Wyświetl przydziały kont wsadowych w [Azure Portal][portal].

1. Wybierz pozycję konta w usłudze **Batch** w portalu, a następnie wybierz odpowiednie konto w usłudze Batch.
1. Wybierz pozycję **przydziały** w menu konta zadania wsadowego.
1. Wyświetl przydziały aktualnie stosowane do konta w usłudze Batch

    ![Przydziały kont wsadowych][account_quotas]

## <a name="increase-a-quota"></a>Zwiększ limit przydziału

Wykonaj następujące kroki, aby zażądać zwiększenia przydziału dla konta usługi Batch lub subskrypcji przy użyciu [Azure Portal][portal]. Typ zwiększenia limitu przydziału zależy od trybu alokacji puli konta usługi Batch. Aby zażądać zwiększenia limitu przydziału, należy dołączyć serię maszyn wirtualnych, dla której chcesz zwiększyć przydział. Po zastosowaniu wzrostu przydziału zostanie on zastosowany do wszystkich serii maszyn wirtualnych.

### <a name="increase-cores-quota-in-batch"></a>Zwiększ limit przydziału rdzeni w partii 

1. Wybierz kafelek **Pomoc i obsługa techniczna** na pulpicie nawigacyjnym portalu lub znak zapytania (**?**) w prawym górnym rogu portalu.
1. Wybierz pozycję **nowe**  >  **Informacje podstawowe**żądania obsługi.
1. **Podstawowe informacje**:
   
    a. **Typ problemu**  >  **Limity usług i subskrypcji (przydziały)**
   
    b. Wybierz subskrypcję.
   
    c. **Typ**  >  limitu przydziału **Partia zadań**
      
    Wybierz pozycję **Dalej**.
    
1. W obszarze **Szczegóły**:
      
    a. W obszarze **Podaj szczegóły**Określ lokalizację, typ przydziału i konto w usłudze Batch.
    
    ![Wzrost przydziału partii][quota_increase]

    Typy przydziałów obejmują:

    * **Za konto wsadowe**  
        Wartości specyficzne dla pojedynczego konta partii, w tym dedykowane i o niskim priorytecie rdzenie oraz liczbę zadań i pul.
        
    * **Na region**  
        Wartości, które mają zastosowanie do wszystkich kont usługi Batch w regionie i obejmują liczbę kont usługi Batch na region na subskrypcję.

    Przydział o niskim priorytecie to pojedyncza wartość dla wszystkich serii maszyn wirtualnych. Jeśli potrzebujesz ograniczonych jednostek SKU, musisz wybrać **rdzenie o niskim priorytecie** i dołączyć rodziny maszyn wirtualnych do żądania.

    b. Wybierz **wagę** zgodną z Twoim [wpływem na firmę][support_sev].

    Wybierz pozycję **Dalej**.

1. W **informacjach kontaktowych**:
   
    a. Wybierz **preferowaną metodę kontaktu**.
   
    b. Sprawdź i wprowadź wymagane szczegóły kontaktu.
   
    Wybierz pozycję **Utwórz** , aby przesłać żądanie obsługi.

Po przesłaniu żądania pomocy technicznej systemu Azure skontaktuje się z Tobą. Żądania limitu przydziału mogą być wykonywane w ciągu kilku minut lub do dwóch dni roboczych.

## <a name="related-quotas-for-vm-pools"></a>Powiązane przydziały dla pul maszyn wirtualnych

Pule usługi Batch w konfiguracji maszyny wirtualnej wdrożonej w sieci wirtualnej platformy Azure automatycznie przydzielą dodatkowe zasoby sieci platformy Azure. W przypadku każdego węzła puli 50 w sieci wirtualnej są niezbędne następujące zasoby:

* Jedna [sieciowa Grupa zabezpieczeń](../virtual-network/security-overview.md#network-security-groups)
* Jeden [publiczny adres IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Jeden [moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md)

Te zasoby są przydzieleni do subskrypcji zawierającej sieć wirtualną podaną podczas tworzenia puli usługi Batch. Te zasoby są ograniczone przez [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. Jeśli planujesz wdrożenia dużych pul w sieci wirtualnej, sprawdź przydziały dla tych zasobów. W razie konieczności Poproś o zwiększenie Azure Portal, wybierając pozycję **Pomoc i obsługa techniczna**.


## <a name="related-topics"></a>Powiązane tematy
* [Tworzenie konta usługi Azure Batch w witrynie Azure Portal](batch-account-create-portal.md)
* [Omówienie funkcji Azure Batch](batch-api-basics.md)
* [Azure subscription and service limits, quotas, and constraints (Limity subskrypcji i usług, limity przydziału oraz ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
