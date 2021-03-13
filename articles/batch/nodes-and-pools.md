---
title: Węzły i pule w Azure Batch
description: Zapoznaj się z węzłami obliczeniowymi i pulami oraz sposobem ich użycia w przepływie pracy Azure Batch z punktu widzenia rozwoju.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e1edcc805e0e8c59d189a4622e494101fb31bb6d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200226"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Węzły i pule w Azure Batch

W przepływie pracy Azure Batch *węzeł obliczeniowy* (lub *węzeł*) jest maszyną wirtualną, która przetwarza część obciążenia aplikacji. *Pula* jest kolekcją tych węzłów, na których aplikacja ma działać. W tym artykule opisano więcej informacji o węzłach i pulach wraz z zagadnieniami dotyczącymi tworzenia i używania ich w przepływie pracy Azure Batch.

## <a name="nodes"></a>Węzły

Węzeł to maszyna wirtualna platformy Azure lub maszyna wirtualna usługi w chmurze, która jest przeznaczona do przetwarzania części obciążenia aplikacji. Rozmiar węzła określa liczbę rdzeni procesora, pojemność pamięci oraz rozmiar lokalnego systemu plików przydzielony do tego węzła.

Pule węzłów systemu Windows lub Linux można tworzyć przy użyciu usługi Azure Cloud Services, obrazów z witryny [Marketplace usługi Azure Virtual Machines](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) lub przygotowanych samodzielnie obszarów niestandardowych.

Węzły można uruchamiać przy użyciu dowolnego pliku wykonywalnego lub skryptu, który jest obsługiwany przez środowisko systemu operacyjnego węzła. Pliki wykonywalne lub skrypty obejmują \* skrypty exe, \* cmd, \* bat i PowerShell (dla systemu Windows) oraz pliki binarne, powłoki i skrypty języka Python (w systemie Linux).

Wszystkie węzły obliczeniowe usługi Batch obejmują również:

- Standardową [strukturę folderów](files-and-directories.md) i skojarzone [zmienne środowiskowe](jobs-and-tasks.md) dostępne do użycia jako odwołania w zadaniach podrzędnych.
- Ustawienia **Zapory** skonfigurowane do kontroli dostępu.
- [Dostęp zdalny](error-handling.md#connect-to-compute-nodes) do węzłów systemu Windows (Remote Desktop Protocol (RDP)) i Linux (Secure Shell (SSH)) (o ile nie zostanie [utworzona Pula z wyłączonym dostępem zdalnym](pool-endpoint-configuration.md)).

Domyślnie węzły mogą komunikować się ze sobą, ale nie mogą komunikować się z maszynami wirtualnymi, które nie są częścią tej samej puli. Aby umożliwić węzłom bezpieczne komunikowanie się z innymi maszynami wirtualnymi lub z siecią lokalną, można zainicjować obsługę administracyjną puli [w podsieci sieci wirtualnej platformy Azure](batch-virtual-network.md). Po wykonaniu tej czynności Twoje węzły będą dostępne za poorednictwem publicznych adresów IP. Te publiczne adresy IP są tworzone przez partię i mogą ulec zmianie w okresie istnienia puli. Istnieje również możliwość [utworzenia puli ze statycznymi publicznymi adresami IP](create-pool-public-ip.md) , co gwarantuje, że nie ulegną zmianie.

## <a name="pools"></a>Pule

Pula jest kolekcją węzłów, na których aplikacja jest uruchamiana.

Pule usługi Batch są oparte na podstawowej platformie obliczeniowej Azure. Zapewniają one alokację na dużą skalę, instalację aplikacji, dystrybucję danych, monitorowanie kondycji i elastyczne dostosowywanie ([skalowanie](#automatic-scaling-policy)) liczby węzłów obliczeniowych w puli.

Do każdego węzła, który jest dodawany do puli zostaje przypisana unikatowa nazwa i adres IP. Gdy węzeł zostanie usunięty z puli, wszelkie zmiany wprowadzone w systemie operacyjnym lub w plikach zostaną utracone, a jego nazwa i adres IP zostaną zwolnione do użytku w przyszłości. Gdy węzeł opuści pulę, oznacza to, że zakończył się jego okres istnienia.

Pula może być używana tylko na koncie usługi Batch, w ramach którego ją utworzono. Konto wsadowe może utworzyć wiele pul, aby spełnić wymagania dotyczące zasobów aplikacji, które zostaną uruchomione.

Pulę można utworzyć ręcznie lub [automatycznie przez usługę Batch](#autopools) po określeniu pracy do wykonania. Podczas tworzenia puli można określić następujące atrybuty:

- [System operacyjny i wersja węzła](#operating-system-and-version)
- [Typ węzła i docelowa liczba węzłów](#node-type-and-target)
- [Rozmiar węzła](#node-size)
- [Zasady automatycznego skalowania](#automatic-scaling-policy)
- [Zasady planowania zadań podrzędnych](#task-scheduling-policy)
- [Stan komunikacji](#communication-status)
- [Zadania uruchamiania](#start-tasks)
- [Pakiety aplikacji](#application-packages)
- [Konfiguracja sieci wirtualnej i zapory](#virtual-network-vnet-and-firewall-configuration)
- [Okres istnienia](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Konta usługi Batch mają domyślny limit przydziału, który ogranicza liczbę rdzeni na koncie usługi Batch. Liczba rdzeni odpowiada liczbie węzłów obliczeniowych. Domyślne limity przydziału oraz instrukcje dotyczące [zwiększania limitów przydziału](batch-quota-limit.md#increase-a-quota) znajdują się w artykule [Quotas and limits for the Azure Batch service (Limity przydziału i limity dla usługi Azure Batch)](batch-quota-limit.md). Przyczyną tego, że pula nie osiągnęła docelowej liczby węzłów, może być przydział rdzeni.

## <a name="operating-system-and-version"></a>System operacyjny i wersja

Podczas tworzenia puli usługi Batch należy określić konfigurację maszyny wirtualnej platformy Azure oraz typ systemu operacyjnego, który ma być uruchamiany w każdym węźle obliczeniowym w puli.

## <a name="configurations"></a>Konfiguracje

Istnieją dwa typy konfiguracji puli dostępne w usłudze Batch.

> [!IMPORTANT]
> Chociaż obecnie można tworzyć pule przy użyciu jednej z konfiguracji, nowe pule należy skonfigurować przy użyciu konfiguracji maszyny wirtualnej, a nie Cloud Services konfiguracji. Wszystkie bieżące i nowe funkcje wsadowe będą obsługiwane przez pule konfiguracji maszyny wirtualnej. Pule konfiguracji Cloud Services nie obsługują wszystkich funkcji i nie są planowane żadne nowe możliwości. Nie będzie można tworzyć nowych pul "CloudServiceConfiguration" ani dodawać nowych węzłów do istniejących pul [po 29 lutego 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

### <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej

**Konfiguracja maszyny wirtualnej** określa, że Pula składa się z maszyn wirtualnych platformy Azure. Te maszyny wirtualne można tworzyć na podstawie obrazów systemu Windows albo Linux.

[Agent węzła usługi Batch](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) jest programem uruchamianym w każdym węźle w puli i udostępnia interfejs poleceń i kontroli między węzłem a usługą Batch. Istnieją różne implementacje agenta węzła, nazywane jednostkami SKU dla różnych systemów operacyjnych. Podczas tworzenia puli na podstawie konfiguracji usługi Virtual Machines należy określić nie tylko rozmiar węzłów i obrazów użytych do ich utworzenia, ale także **odwołanie do obrazu maszyny wirtualnej** i **jednostkę SKU węzła agenta** usługi Batch do zainstalowania w węzłach. Więcej informacji na temat określania powyższych właściwości puli znajduje się w artykule [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Aprowizowanie węzłów obliczeniowych systemu Linux w pulach usługi Azure Batch). Możesz opcjonalnie dołączyć co najmniej jeden pusty dysk danych do puli maszyn wirtualnych utworzonej z obrazów witryny Marketplace lub uwzględnić dyski danych w niestandardowych obrazach używanych do tworzenia maszyn wirtualnych. W przypadku dołączania dysków z danymi należy zainstalować i sformatować dyski z poziomu maszyny wirtualnej w celu ich użycia.

### <a name="cloud-services-configuration"></a>Konfiguracja Cloud Services

> [!WARNING]
> Pule konfiguracji Cloud Services są [przestarzałe](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Zamiast tego użyj pul konfiguracji maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Migrowanie konfiguracji puli partii z Cloud Services do maszyny wirtualnej](batch-pool-cloud-service-to-virtual-machine-configuration.md).

**Konfiguracja Cloud Services** określa, że Pula składa się z węzłów Cloud Services platformy Azure. Cloud Services udostępnia tylko węzły obliczeniowe systemu Windows.

Dostępne systemy operacyjne dla pul konfiguracji Cloud Services są wymienione w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawu SDK](../cloud-services/cloud-services-guestos-update-matrix.md), a dostępne rozmiary węzłów obliczeniowych są wymienione w obszarze [rozmiary dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Podczas tworzenia puli zawierającej węzły Cloud Services należy określić rozmiar węzła i jego *rodzinę systemów operacyjnych* (który określa, które wersje platformy .NET są instalowane z systemem operacyjnym). Cloud Services jest wdrażana na platformie Azure szybciej niż w przypadku maszyn wirtualnych z systemem Windows. Jeśli potrzebujesz pul systemu Windows, może się okazać, że usługi Cloud Services oferują korzystną wydajność związaną z czasem wdrażania.

Podobnie jak w przypadku ról procesów roboczych w ramach Cloud Services, można określić *wersję systemu operacyjnego*. Zalecamy określenie `Latest (*)` dla *wersji systemu operacyjnego* , aby węzły były automatycznie uaktualniane i nie jest konieczne wykonanie żadnych czynności w celu zapewnienia obsługi nowo wydanych wersji. Podstawowym warunkiem wybrania określonej wersji systemu operacyjnego jest upewnienie się, czy została zachowana zgodność aplikacji, przez zezwolenie na testowanie zgodności z poprzednimi wersjami przed zezwoleniem na aktualizację wersji. Po sprawdzeniu poprawności można zaktualizować *wersję systemu operacyjnego* dla puli i zainstalować nowy obraz systemu operacyjnego. Wszystkie uruchomione zadania będą przerywane i ponownie kolejkowane.

### <a name="node-agent-skus"></a>Jednostki SKU agenta węzła

W przypadku tworzenia puli musisz wybrać odpowiednią wartość elementu **nodeAgentSkuId** w zależności od systemu operacyjnego podstawowego obrazu dysku VHD. Można uzyskać mapowanie dostępnych identyfikatorów jednostek SKU agenta węzła na odwołania do obrazów systemu operacyjnego, wywołując [listę obsługiwanych jednostek SKU agenta węzła](/rest/api/batchservice/list-supported-node-agent-skus) .

### <a name="custom-images-for-virtual-machine-pools"></a>Niestandardowe obrazy dla pul usługi Virtual Machines

Aby dowiedzieć się, jak utworzyć pulę z obrazami niestandardowymi, zobacz [Używanie galerii obrazów udostępnionych do tworzenia puli niestandardowej](batch-sig-images.md).

Alternatywnie można utworzyć niestandardową pulę maszyn wirtualnych przy użyciu zasobu [obrazu zarządzanego](batch-custom-images.md) . Aby uzyskać informacje dotyczące przygotowywania niestandardowych obrazów systemu Linux na podstawie maszyn wirtualnych platformy Azure, zobacz [How to create an image of a virtual machine or VHD](../virtual-machines/linux/capture-image.md) (Jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego). Aby uzyskać informacje na temat przygotowywania niestandardowych obrazów systemu Windows na podstawie maszyn wirtualnych platformy Azure, zobacz [Create a managed image of a generalized VM in Azure (Tworzenie zarządzanego obrazu uogólnionej maszyny wirtualnej na platformie Azure)](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Obsługa kontenerów w pulach maszyn wirtualnych

Podczas tworzenia puli konfiguracji maszyny wirtualnej za pomocą interfejsów API usługi Batch możesz skonfigurować pulę do uruchamiania zadań w kontenerach platformy Docker. Obecnie musisz utworzyć pulę przy użyciu obrazu, który obsługuje kontenery platformy Docker. Użyj systemu Windows Server 2016 Datacenter z obrazem kontenerów z witryny Azure Marketplace lub podać niestandardowy obraz maszyny wirtualnej obejmujący platformę Docker Community Edition lub Enterprise Edition i wszystkie wymagane sterowniki. Ustawienia puli muszą zawierać [konfigurację kontenera](/rest/api/batchservice/pool/add), która kopiuje obrazy kontenera do maszyn wirtualnych po utworzeniu puli. Zadania uruchamiane na puli mogą następnie odwoływać się do obrazów kontenera i opcji uruchamiania kontenera.

Aby uzyskać więcej informacji, zobacz [Run Docker container applications on Azure Batch](batch-docker-container-workloads.md) (Uruchamianie aplikacji kontenera platformy Docker w usłudze Azure Batch).

## <a name="node-type-and-target"></a>Typ węzła i element docelowy

Podczas tworzenia puli można określić, które typy węzłów mają być i dla każdego z nich. Dwa typy węzłów:

- **Węzły dedykowane.** Dedykowane węzły obliczeniowe są zarezerwowane dla konkretnych obciążeń. Są one droższe niż węzły o niskim priorytecie, ale mają gwarancję, że nigdy nie zostaną przerwane.
- **Węzły o niskim priorytecie.** Węzły o niskim priorytecie wykorzystują nadwyżkę wydajności na platformie Azure do uruchamiania obciążeń usługi Batch. Węzły o niskim priorytecie są mniej kosztowne na godzinę niż w przypadku węzłów dedykowanych i umożliwiają korzystanie z obciążeń wymagających znacznej mocy obliczeniowej. Aby uzyskać więcej informacji, zobacz [Use low-priority VMs with Batch](batch-low-pri-vms.md) (Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch).

Węzły o niskim priorytecie mogą zostać przeniesiona, gdy platforma Azure ma niewystarczającą wydajność. Jeśli węzeł zostanie przerwany podczas przetwarzania zadań, zadania te są ponownie umieszczane w kolejce, a następnie ponownie uruchamiane, kiedy węzeł obliczeniowy znowu stanie się dostępny. Węzły o niskim priorytecie są dobrą opcją w przypadku obciążeń, dla których czas ukończenia zadania jest elastyczny, a praca jest rozproszona na wielu węzłach. Przed podjęciem decyzji o użyciu węzłów niskiego priorytetu dla danego scenariusza upewnij się, że w wyniku nadpisania praca zostanie utracona w minimalnym zakresie i że będzie można ją łatwo odtworzyć.

W tej samej puli mogą istnieć węzły obliczeniowe o niskim priorytecie i węzły dedykowane. Każdy typ węzła ma własne ustawienie docelowe, dla którego można określić żądaną liczbę węzłów.

Liczbę węzłów obliczeniowych określa się jako *docelową*, ponieważ w niektórych sytuacjach wybrana liczba węzłów w puli nie zostanie osiągnięta. Na przykład pula może nie osiągnąć wartości docelowej, jeśli wcześniej zostanie osiągnięty [podstawowy przydział](batch-quota-limit.md) dla konta usługi Batch. Lub Pula może nie osiągnąć celu, jeśli zastosowano formułę automatycznego skalowania do puli, która ogranicza maksymalną liczbę węzłów.

Aby uzyskać informacje o cenach dla węzłów o niskim priorytecie i dedykowanych, zobacz [Cennik usługi Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Rozmiar węzła

Podczas tworzenia puli usługi Azure Batch możesz wybierać spośród prawie wszystkich rodzin i rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje szereg rozmiarów maszyn wirtualnych dla różnych obciążeń, w tym specjalizowane rozmiary maszyn wirtualnych [HPC](../virtual-machines/sizes-hpc.md) lub [z obsługą procesorów GPU](../virtual-machines/sizes-gpu.md). Należy zauważyć, że rozmiary węzłów można wybrać tylko w momencie utworzenia puli. Inaczej mówiąc, po utworzeniu puli nie można zmienić jej rozmiaru węzła.

Aby uzyskać więcej informacji, zobacz [Choose a VM size for compute nodes in an Azure Batch pool](batch-pool-vm-sizes.md) (Wybieranie rozmiaru maszyn wirtualnych dla węzłów obliczeniowych w puli usługi Azure Batch).

## <a name="automatic-scaling-policy"></a>Zasady automatycznego skalowania

W przypadku obciążeń dynamicznych można zastosować zasady skalowania automatycznego do puli. Usługa Batch okresowo oblicza formułę i dynamicznie dostosowuje liczbę węzłów w puli zgodnie z bieżącym obciążeniem i użyciem zasobów scenariusza obliczeniowego. Dzięki temu można zmniejszyć całkowity koszt działania aplikacji przy użyciu tylko potrzebnych zasobów, zwalniając te zbędne.

Automatyczne skalowanie można włączyć, pisząc [formułę automatycznego skalowania](batch-automatic-scaling.md#autoscale-formulas) i kojarząc ją z pulą. Usługa Batch używa formuły do określenia docelowej liczby węzłów w puli dla kolejnego interwału skalowania (interwału, który możesz skonfigurować). Ustawienia automatycznego skalowania puli można wybrać podczas jej tworzenia. Można również włączyć skalowanie puli później. Masz również możliwość aktualizowania ustawień skalowania już włączonych w puli.

Na przykład może być wymagane zadanie przesyłania dużej liczby zadań do wykonania. Do puli można przypisać formułę skalowania, która dostosowuje liczbę węzłów w puli w oparciu o bieżącą liczbę zadań podrzędnych w kolejce oraz szybkość ich wykonywania w ramach zadania. Usługa Batch okresowo ocenia formułę i zmienia rozmiar puli w oparciu o obciążenie i innych ustawień formuły. Usługa dodaje węzły w przypadku dużej liczby zadań podrzędnych w kolejce i usuwa węzły w przypadku braku zadań podrzędnych, które znajdują się w kolejce lub są uruchomione.

Formuła skalowania może opierać się na następujących metrykach:

- **Metryki czasu** — na podstawie danych statystycznych zbieranych co pięć minut przez określoną liczbę godzin.
- **Metryki zasobów** — na podstawie użycia procesora, wykorzystania przepustowości, użycia pamięci i liczby węzłów.
- **Metryki zadań podrzędnych** — na podstawie stanu zadania podrzędnego, takiego jak *Aktywne* (w kolejce), *Uruchomione* lub *Ukończone*.

Ponieważ automatyczne skalowanie zmniejsza liczbę węzłów obliczeniowych w puli, należy rozważyć sposób obsługi zadań podrzędnych wykonywanych w czasie operacji zmniejszania tej liczby. Aby to umożliwić, w usłudze Batch jest dostępna [*opcja cofnięcia przydziału węzła*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) , którą można uwzględnić w formułach. Możesz na przykład zdecydować, że przed usunięciem węzła z puli uruchomione zadania podrzędne będą zatrzymywane natychmiast i ponownie umieszczane w kolejce do wykonania w innym węźle albo ich wykonywanie zostanie najpierw ukończone. Należy pamiętać, że ustawienie opcji cofnięcia alokacji węzła `taskcompletion` lub `retaineddata` uniemożliwi operacje zmiany rozmiaru puli, dopóki nie zostaną zakończone wszystkie zadania lub wygasły wszystkie okresy przechowywania zadań.

Więcej informacji na temat automatycznego skalowania aplikacji znajduje się w temacie [Automatically scale compute nodes in an Azure Batch pool](batch-automatic-scaling.md) (Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch).

> [!TIP]
> Aby maksymalnie zwiększyć wykorzystanie zasobów obliczeniowych, ustaw docelową liczbę węzłów na zero na końcu zadania, ale zezwól, aby uruchomione zadania zostały ukończone.

## <a name="task-scheduling-policy"></a>Zasady planowania zadań podrzędnych

Opcja konfiguracji [maksymalnej liczby zadań podrzędnych na węzeł](batch-parallel-node-tasks.md) określa maksymalną liczbę zadań podrzędnych, które można uruchomić równolegle na poszczególnych węzłach obliczeniowych w ramach puli.

Domyślna konfiguracja polega na tym, że w danym momencie w węźle jest uruchamiane jedno zadanie podrzędne, ale istnieją scenariusze, w których korzystne jest, aby w danym momencie w węźle było wykonywanych więcej zadań podrzędnych. Zobacz [przykładowy scenariusz](batch-parallel-node-tasks.md#example-scenario) w artykule dotyczącym [równoczesnych zadań podrzędnych węzła](batch-parallel-node-tasks.md), aby dowiedzieć się, jak można korzystać z wielu zadań podrzędnych w jednym węźle.

Można również określić *Typ wypełnienia*, który określa, czy wsadowe rozprasza zadania równomiernie we wszystkich węzłach w puli, czy pakiety z każdym węzłem z maksymalną liczbą zadań przed przypisaniem zadań do innego węzła.

## <a name="communication-status"></a>Stan komunikacji

W większości przypadków zadania podrzędne działają niezależnie i nie muszą komunikować się ze sobą. Jednak w niektórych aplikacjach będzie wymagana komunikacja między zadaniami podrzędnymi (np. w [scenariuszach MPI](batch-mpi.md)).

Można skonfigurować pulę, aby umożliwić **komunikację międzywęzłową** , aby węzły w puli mogły komunikować się w czasie wykonywania. Po włączeniu komunikacji międzywęzłowej węzły w pulach konfiguracji usług Cloud Services mogą komunikować się ze sobą na portach większych niż 1100, a w przypadku pul konfiguracji usługi Virtual Machines ruch nie jest ograniczony do żadnego portu.

Włączenie komunikacji między węzłami wpływa również na rozmieszczenie węzłów w klastrach i może ograniczyć maksymalną liczbę węzłów w puli z powodu ograniczeń wdrożenia. Jeśli aplikacja nie wymaga komunikacji między węzłami, usługa Batch może przydzielić potencjalnie dużą liczbę węzłów do puli z wielu różnych klastrów i centrów danych, aby umożliwić zwiększoną równoległą moc przetwarzania.

## <a name="start-tasks"></a>Zadania uruchamiania

W razie potrzeby można dodać [zadanie uruchamiania](jobs-and-tasks.md#start-task) , które zostanie wykonane w każdym węźle, gdy ten węzeł zostanie dołączony do puli, oraz za każdym razem, gdy węzeł jest ponownie uruchamiany lub odtwarzany z obrazu. Zadanie podrzędne uruchamiania jest szczególnie przydatne w przypadku przygotowywania węzłów obliczeniowych do wykonywania zadań podrzędnych, takich jak instalowanie aplikacji, które będą uruchamiane przez zadania podrzędne w tych węzłach.

## <a name="application-packages"></a>Pakiety aplikacji

Można wybrać pakiety aplikacji do wdrożenia w węzłach obliczeniowych w puli. Pakiety aplikacji umożliwiają uproszczone wdrażanie aplikacji uruchamianych w ramach zadań podrzędnych oraz zarządzanie ich wersjami. Pakiety aplikacji wybrane dla puli są instalowane w każdym węźle dołączanym do puli oraz za każdym razem, gdy węzeł jest ponownie uruchamiany lub odtwarzany z obrazu.

Aby uzyskać więcej informacji o używaniu pakietów aplikacji do wdrażania aplikacji w węzłach usługi Batch, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Konfiguracja sieci wirtualnej i zapory

Podczas aprowizowania puli węzłów obliczeniowych w usłudze Batch możesz ją skojarzyć z podsiecią [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) platformy Azure. Aby użyć sieci wirtualnej platformy Azure, interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md).

### <a name="vnet-requirements"></a>Wymagania dotyczące sieci wirtualnej

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Aby uzyskać więcej informacji na temat konfigurowania puli usługi Batch w sieci wirtualnej, zobacz [Create a pool of virtual machines with your virtual network](batch-virtual-network.md) (Tworzenie puli maszyn wirtualnych przy użyciu sieci wirtualnej).

> [!TIP]
> Aby mieć pewność, że publiczne adresy IP używane do dostępu do węzłów nie zmieniają się, można [utworzyć pulę z określonymi publicznymi adresami IP, które kontrolujesz](create-pool-public-ip.md).

## <a name="pool-and-compute-node-lifetime"></a>Okres istnienia puli i węzła obliczeniowego

Podczas projektowania rozwiązania Azure Batch należy określić sposób i czas tworzenia pul oraz czas dostępności węzłów obliczeniowych w ramach tych pul.

Z jednej strony można utworzyć pulę dla każdego przesyłanego zadania i usunąć pulę, gdy tylko zakończy się wykonywanie zadań podrzędnych. To maksymalizuje wykorzystanie, ponieważ węzły są przyliczane tylko w razie konieczności i są zamykane, gdy są w stanie bezczynności. Chociaż oznacza to, że zadanie musi oczekiwać na przydzielenie węzłów, ważne jest, aby pamiętać, że zadania są zaplanowane do wykonania zaraz po przydzieleniu węzłów, a zadanie uruchamiania zostało zakończone. Usługa Batch *nie* oczekuje, aż wszystkie węzły w puli zostaną udostępnione przed przypisaniem zadań podrzędnych do węzłów. Dzięki temu zapewnia maksymalne wykorzystanie wszystkich dostępnych węzłów.

Z drugiej strony, jeśli natychmiastowe uruchomienie zadań ma najwyższy priorytet, pula może zostać utworzona przed czasem, a jej węzły mogą zostać udostępnione przed przesłaniem zadań. W tym scenariuszu zadania podrzędne mogą być uruchamiane natychmiast, ale podczas oczekiwania na ich przypisanie węzły mogą być w stanie bezczynności.

Połączone podejście jest zwykle używane do obsługi zmiennej, ale trwającego obciążenia. Możesz mieć pulę, w której przesyłane są wiele zadań, i można skalować liczbę węzłów w górę lub w dół zgodnie z obciążeniem zadania. Można to zrobić w sposób reaktywny, w oparciu o bieżące obciążenie, lub aktywny, jeśli obciążenie można przewidzieć. Aby uzyskać więcej informacji, zobacz [zasady skalowania automatycznego](#automatic-scaling-policy).

## <a name="autopools"></a>Autopule

[Autopula](/rest/api/batchservice/job/add#autopoolspecification) jest pulą utworzoną przez usługę Batch, gdy zadanie zostanie przesłane zamiast tworzenia przed zadaniami, które zostaną uruchomione w puli. Usługa Batch będzie zarządzać okresem istnienia puli Auto, zgodnie ze specyfiką określoną przez użytkownika. W większości przypadków te pule są również ustawiane automatycznie po zakończeniu zadań.

## <a name="security-with-certificates"></a>Zabezpieczenia oparte na certyfikatach

Zazwyczaj certyfikatów należy użyć podczas szyfrowania i odszyfrowywania poufnych informacji dotyczących zadań podrzędnych, np. klucza [konta usługi Azure Storage](accounts.md#azure-storage-accounts). Aby to umożliwić, można zainstalować certyfikaty w węzłach. Zaszyfrowane klucze tajne są przekazywane do zadań za pomocą parametrów wiersza polecenia lub osadzane w jednym z zasobów zadań, a zainstalowanych certyfikatów można użyć do ich odszyfrowania.

Aby dodać certyfikat do konta usługi Batch, należy użyć operacji [Dodaj certyfikat](/rest/api/batchservice/certificate/add) (interfejs API REST usługi Batch) lub metody [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) (platforma .NET usługi Batch). Następnie można skojarzyć certyfikat z nową lub istniejącą pulą.

Gdy certyfikat zostaje skojarzony z pulą, usługa Batch instaluje certyfikat w każdym węźle w puli. Usługa Batch instaluje odpowiednie certyfikaty podczas uruchamiania węzła, przed uruchomieniem jakichkolwiek zadań (w tym zadania podrzędnego [Uruchom zadanie](jobs-and-tasks.md#start-task) i [Menedżer zadań](jobs-and-tasks.md#job-manager-task)).

W przypadku dodania certyfikatu do istniejącej puli należy ponownie uruchomić jego węzły obliczeniowe w celu zastosowania certyfikatu do węzłów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zadaniach i zadaniach](jobs-and-tasks.md).
