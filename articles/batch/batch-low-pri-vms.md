---
title: Uruchamianie obciążeń na opłacalnych maszynach wirtualnych o niskim priorytecie
description: Dowiedz się, jak zainicjować obsługę maszyn wirtualnych o niskim priorytecie, aby zmniejszyć koszty obciążeń Azure Batch.
author: mscurrell
ms.topic: how-to
ms.date: 02/02/2021
ms.custom: seodec18
ms.openlocfilehash: 9214ef83ec9b8bef4fb7bc7489aa0ab388f67c0d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507280"
---
# <a name="use-low-priority-vms-with-batch"></a>Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch

Azure Batch oferuje maszyny wirtualne o niskim priorytecie, aby zmniejszyć koszty obciążeń wsadowych. Maszyny wirtualne o niskim priorytecie umożliwiają tworzenie nowych typów obciążeń usługi Batch przez umożliwienie użycia dużej ilości mocy obliczeniowej na potrzeby bardzo niskich kosztów.

Maszyny wirtualne o niskim priorytecie wykorzystują nadwyżkę pojemności na platformie Azure. W przypadku określania maszyn wirtualnych o niskim priorytecie w pulach Azure Batch mogą korzystać z tej nadwyżki, jeśli jest dostępna.

Użycie maszyn wirtualnych o niskim priorytecie polega na tym, że te maszyny wirtualne mogą nie zawsze być dostępne do przydzielenia lub mogą być w dowolnej chwili zastępująne, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie są najbardziej odpowiednie dla obciążeń wsadowych i przetwarzania asynchronicznego, w których czas ukończenia zadania jest elastyczny, a praca jest dystrybuowana na wielu maszynach wirtualnych.

Maszyny wirtualne o niskim priorytecie są oferowane w znacznie obniżonej cenie w porównaniu z dedykowanymi maszynami wirtualnymi. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Batch](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> Na maszynach wirtualnych z [pojedynczym wystąpieniem](../virtual-machines/spot-vms.md) i w [zestawach skalowania maszyn](../virtual-machine-scale-sets/use-spot.md)wirtualnych są teraz dostępne [maszyny wirtualne](https://azure.microsoft.com/pricing/spot/) . Maszyny wirtualne są rozwojem maszyn wirtualnych o niskim priorytecie, ale różnią się w zależności od cen, a opcjonalna maksymalna cena może być ustawiana podczas alokowania dodatkowych maszyn wirtualnych.
>
> Pule Azure Batch rozpoczną obsługę maszyn wirtualnych w ciągu kilku miesięcy, które są ogólnie dostępne, z nowymi wersjami [interfejsów API i narzędzi wsadowych](./batch-apis-tools.md). Po udostępnieniu obsługi maszyn wirtualnych o niskim priorytecie zostaną zaniechane — będą one nadal obsługiwane przy użyciu bieżących interfejsów API i wersji narzędzi przez co najmniej 12 miesięcy, aby zapewnić wystarczającą ilość czasu na przeprowadzenia migracji do maszyn wirtualnych.
>
> Maszyny wirtualne na miejscu nie będą obsługiwane dla pul [konfiguracji usługi w chmurze](/rest/api/batchservice/pool/add#cloudserviceconfiguration) . Aby można było korzystać z maszyn wirtualnych, pule usług w chmurze muszą zostać zmigrowane do pul [konfiguracji maszyny wirtualnej](/rest/api/batchservice/pool/add#virtualmachineconfiguration) .

## <a name="batch-support-for-low-priority-vms"></a>Obsługa usługi Batch w przypadku maszyn wirtualnych o niskim priorytecie

Azure Batch oferuje kilka funkcji, które ułatwiają korzystanie z maszyn wirtualnych o niskim priorytecie i korzystanie z nich:

- Pule usługi Batch mogą zawierać zarówno dedykowane maszyny wirtualne, jak i maszyny wirtualne o niskim priorytecie. Liczba poszczególnych typów maszyn wirtualnych może być określona podczas tworzenia lub zmieniania puli w dowolnej chwili dla istniejącej puli, przy użyciu jawnej operacji zmiany rozmiaru lub przy użyciu funkcji automatycznego skalowania. Przesyłanie zadania i zadania może pozostawać niezmienione, niezależnie od typów maszyn wirtualnych w puli. Istnieje również możliwość skonfigurowania puli, aby całkowicie używać maszyn wirtualnych o niskim priorytecie do uruchamiania zadań, jak to możliwe, ale na rozłożenia dedykowanych maszyn wirtualnych, jeśli pojemność spadnie poniżej minimalnego progu, aby zachować uruchomione zadania.
- Pule wsadowe automatycznie poszukują docelowej liczby maszyn wirtualnych o niskim priorytecie. Jeśli maszyny wirtualne są przemienione lub niedostępne, Sekwencja wsadowa próbuje zastąpić utraconą pojemność i powrócić do celu.
- Gdy zadania są przerywane, program Batch wykrywa i automatycznie ponownie przystąpi do ponownego uruchomienia zadań.
- Maszyny wirtualne o niskim priorytecie mają oddzielny przydział vCPU, który różni się od jednego dla dedykowanych maszyn wirtualnych. Przydział maszyn wirtualnych o niskim priorytecie jest wyższy niż przydział dla dedykowanych maszyn wirtualnych, ponieważ maszyny wirtualne o niskim priorytecie są tańsze. Aby uzyskać więcej informacji, zobacz [przydziały i limity usługi Batch](batch-quota-limit.md#resource-quotas).

> [!NOTE]
> Maszyny wirtualne o niskim priorytecie nie są obecnie obsługiwane w przypadku kont usługi Batch utworzonych w [trybie subskrypcji użytkownika](accounts.md).

## <a name="considerations-and-use-cases"></a>Zagadnienia i przypadki użycia

Wiele obciążeń usługi Batch jest dobrym dopasowaniem do maszyn wirtualnych o niskim priorytecie. Należy rozważyć ich użycie, gdy zadania są podzielone na wiele zadań równoległych lub gdy istnieje wiele zadań, które są skalowane i rozproszone na wielu maszynach wirtualnych.

Niektóre przykłady przypadków użycia przetwarzania wsadowego dobrze nadają się do korzystania z maszyn wirtualnych o niskim priorytecie:

- **Programowanie i testowanie**: w szczególności w przypadku opracowywania rozwiązań o dużej skali istotne oszczędności mogą być zrealizowane. Wszystkie typy testów mogą korzystać z zalet, ale testowanie obciążenia na dużą skalę i testowanie regresji są doskonałe.
- **Uzupełnienie pojemności na żądanie**: maszyny wirtualne o niskim priorytecie mogą służyć do uzupełniania zwykłych dedykowanych maszyn wirtualnych. W miarę dostępności zadania mogą być skalowane i w związku z tym uzupełniane w celu obniżenia kosztów. gdy nie jest dostępny, linia bazowa dedykowanych maszyn wirtualnych pozostaje dostępna.
- **Elastyczny czas wykonywania zadania**: w przypadku, gdy zadania muszą zostać wykonane w czasie, można tolerować potencjalne przerwy w pojemności; jednak dodanie zadań maszyn wirtualnych o niskim priorytecie przebiega szybciej i przy niższych kosztach.

Pule wsadowe można skonfigurować tak, aby używały maszyn wirtualnych o niskim priorytecie na kilka sposobów:

- Pula może korzystać tylko z maszyn wirtualnych o niskim priorytecie. W takim przypadku program Batch odzyska wszystkie przeniesiona pojemność, jeśli są dostępne. Ta konfiguracja jest najtańszym sposobem wykonywania zadań.
- Maszyny wirtualne o niskim priorytecie mogą być używane w połączeniu z ustaloną linią bazową dedykowanych maszyn wirtualnych. Stała liczba dedykowanych maszyn wirtualnych zapewnia, że zawsze będzie można utrzymywać postęp zadania.
- Pula może korzystać z dynamicznych różnych maszyn wirtualnych o niskim priorytecie, dzięki czemu tańsze maszyny wirtualne o niskim priorytecie są używane wyłącznie wtedy, gdy są dostępne, ale pełne, dedykowane maszyny wirtualne są skalowane w razie potrzeby. Ta konfiguracja pozwala zapewnić minimalną ilość dostępnej pojemności, aby zachować postępy zadań.

Podczas planowania użycia maszyn wirtualnych o niskim priorytecie należy pamiętać o następujących kwestiach:

- Aby zmaksymalizować wykorzystanie nadwyżkowej pojemności na platformie Azure, odpowiednie zadania można skalować w poziomie.
- Czasami maszyny wirtualne mogą nie być dostępne lub są zastępujące, co skutkuje zmniejszeniem wydajności zadań i może prowadzić do przerwania zadań i uruchomienia ponownie.
- Zadania o krótszym czasie wykonywania najlepiej współpracują z maszynami wirtualnymi o niskim priorytecie. Zadania o dłuższych zadaniach mogą mieć wpływ na więcej, jeśli zostały przerwane. Jeśli zadania długotrwałe implementują punkty kontrolne, aby zaoszczędzić postęp podczas wykonywania, ten wpływ może ulec zmniejszeniu. 
- Długotrwałe zadania MPI, które korzystają z wielu maszyn wirtualnych, nie są dobrze dopasowane do korzystania z maszyn wirtualnych o niskim priorytecie, ponieważ jedna z przeniesiona maszyn wirtualnych może prowadzić do powtórnego uruchomienia całego zadania.
- Węzły o niskim priorytecie mogą być oznaczone jako bezużyteczne, jeśli [reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) są niepoprawnie skonfigurowane.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Tworzenie pul z maszynami wirtualnymi o niskim priorytecie i zarządzanie nimi

Pula usługi Batch może zawierać maszyny wirtualne zarówno dedykowane, jak i o niskim priorytecie (nazywane również węzłami obliczeniowymi). Można ustawić docelową liczbę węzłów obliczeniowych dla maszyn wirtualnych zarówno dedykowanych, jak i o niskim priorytecie. Docelowa liczba węzłów określa liczbę maszyn wirtualnych, które mają być w puli.

Na przykład, aby utworzyć pulę przy użyciu usługi Azure Virtual Machines (w tym przypadku maszyn wirtualnych z systemem Linux) z elementem docelowym 5 dedykowanych maszyn wirtualnych i 20 maszyn wirtualnych o niskim priorytecie:

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

Maszyny wirtualne mogą być okresowo przeniesiona. W takim przypadku zadania, które były uruchomione na maszynach wirtualnych z przeniesionam węzłem, są ponownie kolejkowane i uruchamiane.

W przypadku pul konfiguracji maszyny wirtualnej usługa Batch wykonuje również następujące czynności:

- Stan przeniesiona maszyn wirtualnych został zaktualizowany do **zastępujący**. 
- Maszyna wirtualna jest skutecznie usuwana, co prowadzi do utraty danych przechowywanych lokalnie na maszynie wirtualnej.
- Operacja listy węzłów w puli nadal będzie zwracać zastępujące węzły.
- Pula ciągle próbuje uzyskać dostęp do docelowej liczby węzłów o niskim priorytecie. Po znalezieniu pojemności zastępczej węzły zachowują swoje identyfikatory, ale są ponownie inicjowane, przechodząc przez proces **tworzenia** i **uruchamiania** , zanim staną się dostępne do planowania zadań.
- Liczniki zastępujące są dostępne jako metryki w Azure Portal.

## <a name="scale-pools-containing-low-priority-vms"></a>Pule skalowania zawierające maszyny wirtualne o niskim priorytecie

Podobnie jak w przypadku pul składających się wyłącznie z dedykowanych maszyn wirtualnych, możliwe jest skalowanie puli zawierającej maszyny wirtualne o niskim priorytecie przez wywołanie metody zmiany rozmiaru lub przy użyciu funkcji automatycznego skalowania.

Operacja zmiany rozmiaru puli przyjmuje drugi opcjonalny parametr, który aktualizuje wartość **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Formuła automatycznego skalowania puli obsługuje maszyny wirtualne o niskim priorytecie w następujący sposób:

- Można pobrać lub ustawić wartość zmiennej zdefiniowanej przez usługę **$TargetLowPriorityNodes**.
- Możesz uzyskać wartość zmiennej zdefiniowanej przez usługę **$CurrentLowPriorityNodes**.
- Możesz uzyskać wartość zmiennej zdefiniowanej przez usługę **$PreemptedNodeCount**. Ta zmienna zwraca liczbę węzłów w stanie przeniesiona i pozwala na skalowanie w górę lub w dół liczby dedykowanych węzłów, w zależności od liczby niedostępnych węzłów, które są niedostępne.

## <a name="configure-jobs-and-tasks"></a>Konfigurowanie zadań i zadań

Zadania i zadania wymagają niewielkiej konfiguracji dla węzłów o niskim priorytecie. Należy pamiętać o następujących elementach:

- Właściwość JobManagerTask zadania ma właściwość **AllowLowPriorityNode** . Gdy ta właściwość ma wartość true, zadanie Menedżera zadań można zaplanować w węźle dedykowanym lub o niskim priorytecie. Jeśli wartość jest równa false, zadanie Menedżera zadań jest zaplanowane tylko do dedykowanego węzła.
- [Zmienna środowiskowa](batch-compute-node-environment-variables.md) AZ_BATCH_NODE_IS_DEDICATED jest dostępna dla aplikacji zadania, dzięki czemu można określić, czy jest uruchomiona w przypadku niskiego priorytetu, czy w dedykowanym węźle.

## <a name="view-metrics-for-low-priority-vms"></a>Wyświetlanie metryk dla maszyn wirtualnych o niskim priorytecie

Nowe metryki są dostępne w [Azure Portal](https://portal.azure.com) dla węzłów o niskim priorytecie. Te metryki są następujące:

- Liczba węzłów Low-Priority
- Liczba rdzeni Low-Priority
- Liczba przeniesiona węzłów

Aby wyświetlić te metryki w Azure Portal

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch.
2. Wybierz pozycję **metryki** z sekcji **monitorowanie** .
3. Wybierz żądane metryki z listy **metryk** .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
- Rozpocznij planowanie przenoszenia z maszyn wirtualnych o niskim priorytecie do maszyn wirtualnych. W przypadku używania maszyn wirtualnych o niskim priorytecie z pulami **konfiguracji usługi w chmurze** należy zaplanować migrację do [pul **konfiguracji maszyny wirtualnej**](nodes-and-pools.md#configurations) .
