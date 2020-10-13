---
title: Uruchamianie obciążeń na opłacalnych maszynach wirtualnych o niskim priorytecie
description: Dowiedz się, jak zainicjować obsługę maszyn wirtualnych o niskim priorytecie, aby zmniejszyć koszty obciążeń Azure Batch.
author: mscurrell
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: seodec18
ms.openlocfilehash: bd5b73cf55110985a2e7eecbc161c77ca6d645cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568459"
---
# <a name="use-low-priority-vms-with-batch"></a>Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch

Azure Batch oferuje maszyny wirtualne o niskim priorytecie, aby zmniejszyć koszty obciążeń wsadowych. Maszyny wirtualne o niskim priorytecie umożliwiają tworzenie nowych typów obciążeń usługi Batch przez umożliwienie użycia dużej ilości mocy obliczeniowej na potrzeby bardzo niskich kosztów.

Maszyny wirtualne o niskim priorytecie wykorzystują nadwyżkę pojemności na platformie Azure. W przypadku określania maszyn wirtualnych o niskim priorytecie w pulach Azure Batch mogą korzystać z tej nadwyżki, jeśli jest dostępna.

Użycie maszyn wirtualnych o niskim priorytecie polega na tym, że te maszyny wirtualne mogą nie zawsze być dostępne do przydzielenia lub mogą być w dowolnej chwili zastępująne, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie są najbardziej odpowiednie dla niektórych typów obciążeń. Używaj maszyn wirtualnych o niskim priorytecie do obsługi wsadowych i asynchronicznych obciążeń obliczeniowych, w których czas ukończenia zadania jest elastyczny, a praca jest dystrybuowana na wielu maszynach wirtualnych.

Maszyny wirtualne o niskim priorytecie są oferowane w znacznie obniżonej cenie w porównaniu z dedykowanymi maszynami wirtualnymi. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Batch](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> Na maszynach wirtualnych z [pojedynczym wystąpieniem](../virtual-machines/spot-vms.md) i w [zestawach skalowania maszyn](../virtual-machine-scale-sets/use-spot.md)wirtualnych są teraz dostępne [maszyny wirtualne](https://azure.microsoft.com/pricing/spot/) . Maszyny wirtualne są rozwojem maszyn wirtualnych o niskim priorytecie, ale różnią się w zależności od cen, a opcjonalna maksymalna cena może być ustawiana podczas alokowania dodatkowych maszyn wirtualnych.
>
> Pule Azure Batch rozpoczną obsługę maszyn wirtualnych w ciągu kilku miesięcy, które są ogólnie dostępne, z nowymi wersjami [interfejsów API i narzędzi wsadowych](./batch-apis-tools.md). Po udostępnieniu obsługi maszyn wirtualnych o niskim priorytecie zostaną zaniechane — będą one nadal obsługiwane przy użyciu bieżących interfejsów API i wersji narzędzi przez co najmniej 12 miesięcy, aby zapewnić wystarczającą ilość czasu na przeprowadzenia migracji do maszyn wirtualnych. 
>
> Maszyny wirtualne na miejscu nie będą obsługiwane dla pul [konfiguracji usługi w chmurze](/rest/api/batchservice/pool/add#cloudserviceconfiguration) . Aby można było korzystać z maszyn wirtualnych, pule usług w chmurze muszą zostać zmigrowane do pul [konfiguracji maszyny wirtualnej](/rest/api/batchservice/pool/add#virtualmachineconfiguration) .

## <a name="use-cases-for-low-priority-vms"></a>Przypadki użycia dla maszyn wirtualnych o niskim priorytecie

Uwzględniając charakterystykę maszyn wirtualnych o niskim priorytecie, które obciążenia mogą i nie mogą z nich korzystać? Ogólnie rzecz biorąc, obciążenia przetwarzania wsadowego są dobrym dopasowaniem, ponieważ zadania są podzielone na wiele zadań równoległych lub istnieje wiele zadań, które są skalowane i rozproszone na wielu maszynach wirtualnych.

-   Aby zmaksymalizować wykorzystanie nadwyżkowej pojemności na platformie Azure, odpowiednie zadania można skalować w poziomie.

-   Czasami maszyny wirtualne mogą nie być dostępne lub są zastępujące, co skutkuje zmniejszeniem wydajności zadań i może prowadzić do przerwania zadań i uruchomienia ponownie. W związku z tym zadania muszą być elastyczne w czasie, w którym mogą być wykonywane.

-   Zadania o dłuższych zadaniach mogą mieć wpływ na więcej, jeśli zostały przerwane. Jeśli zadania długotrwałe implementują Tworzenie punktów kontrolnych w celu zaoszczędzenia postępów podczas ich wykonywania, zmniejsza to wpływ przerwy. Zadania o krótszym czasie wykonywania najlepiej współpracują z maszynami wirtualnymi o niskim priorytecie, ponieważ wpływ przerwania jest znacznie mniejszy.

-   Długotrwałe zadania MPI, które korzystają z wielu maszyn wirtualnych, nie są dobrze dopasowane do korzystania z maszyn wirtualnych o niskim priorytecie, ponieważ jedna z przeniesiona maszyn wirtualnych może prowadzić do powtórnego uruchomienia całego zadania.

Niektóre przykłady przypadków użycia przetwarzania wsadowego dobrze nadają się do korzystania z maszyn wirtualnych o niskim priorytecie:

-   **Programowanie i testowanie**: w szczególności w przypadku opracowywania rozwiązań o dużej skali istotne oszczędności mogą być zrealizowane. Wszystkie typy testów mogą korzystać z zalet, ale testowanie obciążenia na dużą skalę i testowanie regresji są doskonałe.

-   **Uzupełnienie pojemności na żądanie**: maszyny wirtualne o niskim priorytecie mogą służyć do uzupełniania zwykłych dedykowanych maszyn wirtualnych — jeśli są dostępne, zadania mogą być skalowane i w związku z tym szybko uzupełniane w celu obniżenia kosztów. gdy nie jest dostępny, linia bazowa dedykowanych maszyn wirtualnych pozostaje dostępna.

-   **Elastyczny czas wykonywania zadania**: w przypadku, gdy zadania muszą zostać wykonane w czasie, można tolerować potencjalne przerwy w pojemności; jednak dodanie zadań maszyn wirtualnych o niskim priorytecie przebiega szybciej i przy niższych kosztach.

Pule wsadowe można skonfigurować do używania maszyn wirtualnych o niskim priorytecie na kilka sposobów, w zależności od elastyczności czasu wykonywania zadania:

-   Maszyny wirtualne o niskim priorytecie mogą być używane wyłącznie w puli. W takim przypadku program Batch odzyska wszystkie przeniesiona pojemność, jeśli są dostępne. Ta konfiguracja jest najtańszym sposobem wykonywania zadań, ponieważ używane są tylko maszyny wirtualne o niskim priorytecie.

-   Maszyny wirtualne o niskim priorytecie mogą być używane w połączeniu z ustaloną linią bazową dedykowanych maszyn wirtualnych. Stała liczba dedykowanych maszyn wirtualnych zapewnia, że zawsze będzie można utrzymywać postęp zadania.

-   Może istnieć dynamiczna kombinacja maszyn wirtualnych dedykowanych i o niskim priorytecie, dzięki czemu tańsze maszyny wirtualne o niskim priorytecie są używane wyłącznie wtedy, gdy są dostępne, ale pełne, dedykowane maszyny wirtualne są skalowane w razie potrzeby. Ta konfiguracja pozwala zapewnić minimalną ilość dostępnej pojemności, aby zachować postępy zadań.

## <a name="batch-support-for-low-priority-vms"></a>Obsługa usługi Batch w przypadku maszyn wirtualnych o niskim priorytecie

Azure Batch oferuje kilka funkcji, które ułatwiają korzystanie z maszyn wirtualnych o niskim priorytecie i korzystanie z nich:

-   Pule usługi Batch mogą zawierać zarówno dedykowane maszyny wirtualne, jak i maszyny wirtualne o niskim priorytecie. Liczba poszczególnych typów maszyn wirtualnych może być określona podczas tworzenia lub zmieniania puli w dowolnej chwili dla istniejącej puli, przy użyciu jawnej operacji zmiany rozmiaru lub przy użyciu funkcji automatycznego skalowania. Przesyłanie zadania i zadania może pozostawać niezmienione, niezależnie od typów maszyn wirtualnych w puli. Istnieje również możliwość skonfigurowania puli, aby całkowicie używać maszyn wirtualnych o niskim priorytecie do uruchamiania zadań, jak to możliwe, ale na rozłożenia dedykowanych maszyn wirtualnych, jeśli pojemność spadnie poniżej minimalnego progu, aby zachować uruchomione zadania.

-   Pule wsadowe automatycznie poszukują docelowej liczby maszyn wirtualnych o niskim priorytecie. Jeśli maszyny wirtualne zostaną przełączone, usługa Batch próbuje zastąpić utraconą pojemność i powrócić do celu.

-   Gdy zadania są przerywane, program Batch wykrywa i automatycznie ponownie przystąpi do ponownego uruchomienia zadań.

-   Maszyny wirtualne o niskim priorytecie mają oddzielny przydział vCPU, który różni się od jednego dla dedykowanych maszyn wirtualnych. 
    Przydział maszyn wirtualnych o niskim priorytecie jest wyższy niż przydział dla dedykowanych maszyn wirtualnych, ponieważ maszyny wirtualne o niskim priorytecie są tańsze. Aby uzyskać więcej informacji, zobacz [przydziały i limity usługi Batch](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Maszyny wirtualne o niskim priorytecie nie są obecnie obsługiwane w przypadku kont usługi Batch utworzonych w [trybie subskrypcji użytkownika](accounts.md).

## <a name="create-and-update-pools"></a>Tworzenie i aktualizowanie pul

Pula usługi Batch może zawierać maszyny wirtualne zarówno dedykowane, jak i o niskim priorytecie (nazywane również węzłami obliczeniowymi). Można ustawić docelową liczbę węzłów obliczeniowych dla maszyn wirtualnych zarówno dedykowanych, jak i o niskim priorytecie. Docelowa liczba węzłów określa liczbę maszyn wirtualnych, które mają być w puli.

Na przykład, aby utworzyć pulę przy użyciu maszyn wirtualnych usługi w chmurze platformy Azure z elementem docelowym 5 dedykowanych maszyn wirtualnych i 20 maszyn wirtualnych o niskim priorytecie:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Aby utworzyć pulę przy użyciu usługi Azure Virtual Machines (w tym przypadku maszyn wirtualnych z systemem Linux) z elementem docelowym 5 dedykowanych maszyn wirtualnych i 20 maszyn wirtualnych o niskim priorytecie:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Bieżącą liczbę węzłów można uzyskać zarówno dla maszyn wirtualnych dedykowanych, jak i o niskim priorytecie:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Węzły puli mają właściwość wskazującą, czy węzeł jest maszyną wirtualną lub o niskim priorytecie:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

W przypadku maszyn wirtualnych pule konfiguracji, gdy co najmniej jeden węzeł zostanie przeniesiona, operacja listy węzłów w puli nadal zwraca te węzły. Bieżąca liczba węzłów o niskim priorytecie pozostaje niezmieniona, ale te węzły mają ustawiony stan **zastępujący** . Usługa Batch próbuje znaleźć zamienione maszyny wirtualne, a jeśli to się powiedzie, węzły przechodzą przez **Tworzenie** , a następnie Stany **uruchamiania** , zanim staną się dostępne do wykonania zadania, podobnie jak nowe węzły.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skalowanie puli zawierającej maszyny wirtualne o niskim priorytecie

Podobnie jak w przypadku pul składających się wyłącznie z dedykowanych maszyn wirtualnych, możliwe jest skalowanie puli zawierającej maszyny wirtualne o niskim priorytecie przez wywołanie metody zmiany rozmiaru lub przy użyciu funkcji automatycznego skalowania.

Operacja zmiany rozmiaru puli przyjmuje drugi opcjonalny parametr, który aktualizuje wartość **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Formuła automatycznego skalowania puli obsługuje maszyny wirtualne o niskim priorytecie w następujący sposób:

-   Można pobrać lub ustawić wartość zmiennej zdefiniowanej przez usługę **$TargetLowPriorityNodes**.

-   Możesz uzyskać wartość zmiennej zdefiniowanej przez usługę **$CurrentLowPriorityNodes**.

-   Możesz uzyskać wartość zmiennej zdefiniowanej przez usługę **$PreemptedNodeCount**. 
    Ta zmienna zwraca liczbę węzłów w stanie przeniesiona i pozwala na skalowanie w górę lub w dół liczby dedykowanych węzłów, w zależności od liczby niedostępnych węzłów, które są niedostępne.

## <a name="jobs-and-tasks"></a>Zadania

Zadania i zadania wymagają niewielkiej konfiguracji dla węzłów o niskim priorytecie; Jedyna pomoc techniczna jest następująca:

-   Właściwość JobManagerTask zadania ma nową właściwość **AllowLowPriorityNode**. 
    Gdy ta właściwość ma wartość true, zadanie Menedżera zadań można zaplanować w węźle dedykowanym lub o niskim priorytecie. Jeśli ta właściwość ma wartość false, zadanie Menedżera zadań jest zaplanowana tylko do dedykowanego węzła.

-   [Zmienna środowiskowa](batch-compute-node-environment-variables.md) jest dostępna dla aplikacji zadania, dzięki czemu można określić, czy działa ona w węźle o niskim priorytecie czy w trybie dedykowanym. Zmienna środowiskowa jest AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Obsługa zastępujący

Maszyny wirtualne mogą być okresowo przeniesiona. W takim przypadku zadania, które były uruchomione na maszynach wirtualnych z przeniesionam węzłem, są ponownie kolejkowane i uruchamiane.

W przypadku pul konfiguracji maszyny wirtualnej usługa Batch wykonuje również następujące czynności:

-   Stan przeniesiona maszyn wirtualnych został zaktualizowany do **zastępujący**.
-   Maszyna wirtualna jest skutecznie usuwana, co prowadzi do utraty danych przechowywanych lokalnie na maszynie wirtualnej.
-   Pula ciągle próbuje uzyskać dostęp do docelowej liczby węzłów o niskim priorytecie. Po znalezieniu pojemności zastępczej węzły zachowują swoje identyfikatory, ale są ponownie inicjowane, przechodząc przez proces **tworzenia** i **uruchamiania** , zanim staną się dostępne do planowania zadań.
-   Liczniki zastępujące są dostępne jako metryki w Azure Portal.

## <a name="metrics"></a>Metryki

Nowe metryki są dostępne w [Azure Portal](https://portal.azure.com) dla węzłów o niskim priorytecie. Te metryki są następujące:

- Liczba węzłów Low-Priority
- Liczba rdzeni Low-Priority
- Liczba przeniesiona węzłów

Aby wyświetlić metryki w Azure Portal:

1. Przejdź do konta usługi Batch w portalu i sprawdź ustawienia dla konta usługi Batch.
2. Wybierz pozycję **metryki** z sekcji **monitorowanie** .
3. Wybierz żądane metryki z listy **Dostępne metryki** .

![Zrzut ekranu przedstawiający wybór metryki dla węzłów o niskim priorytecie.](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
- Rozpocznij planowanie przenoszenia z maszyn wirtualnych o niskim priorytecie do maszyn wirtualnych. W przypadku używania maszyn wirtualnych o niskim priorytecie z pulami **konfiguracji usługi w chmurze** należy zaplanować przejście do pul **konfiguracji maszyny wirtualnej** .
