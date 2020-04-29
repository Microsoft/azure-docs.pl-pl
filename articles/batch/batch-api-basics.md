---
title: Przegląd dla deweloperów
description: Opis funkcji usługi Batch i jej interfejsów API z punktu widzenia programowania.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: seodec18
ms.openlocfilehash: fdc04c49521c9d91ef836c4d1dba76091db8f16a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115384"
---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch

W tym omówieniu podstawowych składników usługi Azure Batch przedstawiamy główne funkcje oraz zasoby, z których deweloperzy usługi Batch mogą korzystać w przypadku kompilowania rozwiązań do przetwarzania równoległego na dużą skalę.

Bez względu na to, czy tworzysz aplikację do przetwarzania rozproszonego, usługę obsługującą bezpośrednie wywołania interfejsu [API REST][batch_rest_api], czy też korzystasz z jednego z [zestawów SDK usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development), będziesz używać wielu zasobów oraz funkcji omówionych w tym artykule.

> [!TIP]
> Bardziej szczegółowe wprowadzenie do usługi Batch można znaleźć w temacie [Podstawy usługi Azure Batch](batch-technical-overview.md). Zobacz też najnowsze [aktualizacje usługi Batch](https://azure.microsoft.com/updates/?product=batch).
>
>

## <a name="batch-service-workflow"></a>Przepływ pracy usługi Batch

Poniższy ogólny przepływ pracy to typowy przykład dla niemal wszystkich aplikacji i usług, które korzystają z usługi Batch w przypadku obciążeń przetwarzania równoległego:

1. Przekaż **pliki danych** do przetworzenia na konto usługi [Azure Storage][azure_storage]. Usługa Batch oferuje wbudowaną obsługę dostępu do usługi Azure Blob Storage, a podczas wykonywania zadań podrzędnych można pobierać te pliki do [węzłów obliczeniowych](#compute-node).
2. Przekaż **pliki aplikacji**, które będą uruchamiane w ramach zadań podrzędnych. Mogą być to pliki binarne lub skrypty i ich zależności. Są one wykonywane w ramach zadań podrzędnych w zadaniach. W trakcie wykonywania zadań podrzędnych można pobierać te pliki z konta usługi Storage lub używać funkcji [pakietów aplikacji](#application-packages) usługi Batch na potrzeby zarządzania aplikacjami i ich wdrażania.
3. Utwórz [pulę](#pool) węzłów obliczeniowych. Podczas tworzenia puli określ liczbę jej węzłów obliczeniowych, ich rozmiar oraz system operacyjny. Każde zadanie podrzędne uruchomione w ramach zadania jest przypisywane do wykonania w jednym z węzłów puli.
4. Utwórz [zadanie](#job). Zadanie umożliwia zarządzanie kolekcją zadań podrzędnych. Każde zadanie jest kojarzone z określoną pulą, w której będą uruchamiane zadania podrzędne powiązane z tym zadaniem.
5. Dodaj [podzadania](#task) do zadania. Każde zadanie podrzędne uruchamia aplikację lub skrypt przekazany do przetwarzania plików danych, które są pobierane z konta usługi Storage. Po zakończeniu każdego zadania podrzędnego dane wyjściowe mogą być przekazywane do usługi Azure Storage.
6. Monitoruj postęp zadania i pobieraj dane wyjściowe zadania podrzędnego z usługi Azure Storage.

W poniższych sekcjach omówiono zasoby partii, które umożliwiają dystrybuowany scenariusz obliczeniowy.

> [!NOTE]
> Do korzystania z usługi Batch niezbędne jest [konto usługi Batch](#account). Większość rozwiązań usługi Batch używa też skojarzonego konta usługi [Azure Storage][azure_storage] do przechowywania i pobierania plików.
>
>

## <a name="batch-service-resources"></a>Zasoby usługi Batch

Niektóre z poniższych zasobów — konta, węzły obliczeniowe, pule, zadania i zadania podrzędne — są wymagane przez wszystkie rozwiązania, które korzystają z usługi Batch. Inne, takie jak harmonogramy zadań i pakiety aplikacji, to funkcje przydatne, ale opcjonalne.

* [Konto](#account)
* [Węzeł obliczeniowy](#compute-node)
* [Pula](#pool)
* [Zadanie](#job)
  * [Harmonogramy zadań](#scheduled-jobs)
* [Zadanie](#task)
  * [Zadanie podrzędne uruchamiania](#start-task)
  * [Zadanie podrzędne Menedżera zadań](#job-manager-task)
  * [Zadania podrzędne przygotowania i zwolnienia zadań](#job-preparation-and-release-tasks)
  * Zadanie podrzędne obejmujące wiele wystąpień (MPI)
  * [Zależności zadań](#task-dependencies)
* [Pakiety aplikacji](#application-packages)

## <a name="account"></a>Konto

Konto usługi Batch jest jednoznacznie zdefiniowanym obiektem w ramach usługi Batch. Całe przetwarzanie jest skojarzone z kontem usługi Batch.

Konto usługi Azure Batch możesz utworzyć za pomocą witryny [Azure Portal](batch-account-create-portal.md) lub programowo, na przykład za pomocą [biblioteki Batch Management .NET](batch-management-dotnet.md). Podczas tworzenia konta możesz skojarzyć konto magazynu platformy Azure w celu przechowywania związanych z pracą danych wejściowych i wyjściowych lub aplikacji.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia pomiędzy konta tej usługi znajdujące się w jednej subskrypcji, ale różnych regionach świadczenia usługi Azure.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

## <a name="azure-storage-account"></a>Konto usługi Azure Storage

Większość rozwiązań partii usługi Batch używa usługi Azure Storage do przechowywania plików zasobów i plików wyjściowych. Na przykład zadania podrzędne usługi Batch (w tym standardowe, uruchamiania oraz przygotowania i zwolnienia zadań) muszą określać pliki zasobów, które znajdują się na koncie magazynu.

Usługa Batch obsługuje następujące typy kont usługi Azure Storage:

* Konta ogólnego przeznaczenia, wersja 2 (GPv2) 
* Konta ogólnego przeznaczenia, wersja 1 (GPv1)
* Konta magazynu obiektów blob (aktualnie obsługiwane w przypadku pul w konfiguracji maszyny wirtualnej)

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../storage/common/storage-account-overview.md).

Konto magazynu można skojarzyć z kontem usługi Batch podczas tworzenia konta usługi Batch lub później. Wybierając konto magazynu, należy wziąć pod uwagę wymagania dotyczące kosztów i wydajności. Na przykład opcje konta magazynu GPv2 i Blob Storage obsługują większe [limity wydajności i skalowalności](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) w porównaniu z wersją GPv1. (Skontaktuj się z pomocą techniczną platformy Azure, aby zażądać zwiększenia limitu magazynu). Te opcje konta mogą zwiększyć wydajność rozwiązań usługi Batch, które zawierają dużą liczbę zadań równoległych, które odczytują lub zapisują dane na koncie magazynu.

## <a name="compute-node"></a>Węzeł obliczeniowy

Węzeł obliczeniowy jest maszyną wirtualną platformy Azure lub maszyną wirtualną dla usługi w chmurze przeznaczoną do przetwarzania części obciążenia danej aplikacji. Rozmiar węzła określa liczbę rdzeni procesora, pojemność pamięci oraz rozmiar lokalnego systemu plików przydzielony do tego węzła. Pule węzłów systemu Windows lub Linux można tworzyć przy użyciu usługi Azure Cloud Services, obrazów z witryny [Marketplace usługi Azure Virtual Machines][vm_marketplace] lub przygotowanych samodzielnie obszarów niestandardowych. Aby uzyskać więcej informacji na temat tych opcji, zobacz poniżej sekcję [Pula](#pool).

Węzły można uruchamiać przy użyciu dowolnego pliku wykonywalnego lub skryptu, który jest obsługiwany przez środowisko systemu operacyjnego węzła. Pliki wykonywalne lub skrypty \*zawierają skrypty exe \*, cmd \*, bat i PowerShell dla systemu Windows--oraz plików binarnych, powłoki i skryptów języka Python w systemie Linux.

Wszystkie węzły obliczeniowe usługi Batch obejmują również:

* Standardową [strukturę folderów](#files-and-directories) i skojarzone [zmienne środowiskowe](#environment-settings-for-tasks) dostępne do użycia jako odwołania w zadaniach podrzędnych.
* Ustawienia **Zapory** skonfigurowane do kontroli dostępu.
* [Dostęp zdalny](#connecting-to-compute-nodes) do węzłów systemu Windows (protokół RDP (Remote Desktop)) i Linux (SSH (Secure Shell)).

## <a name="pool"></a>Pula

Pula to kolekcja węzłów, w których jest uruchamiana aplikacja. Pula może być tworzona ręcznie przez użytkownika lub przez usługę Batch automatycznie po określeniu pracy do wykonania. Możesz utworzyć pulę spełniającą wymagania dotyczące zasobów aplikacji i zarządzać nią. Pula może być używana tylko na koncie usługi Batch, w ramach którego ją utworzono. Konto usługi Batch może zawierać więcej niż jedną pulę.

Pule usługi Batch są oparte na podstawowej platformie obliczeniowej Azure. Oferują one alokację na dużą skalę, instalowanie aplikacji, dystrybucję danych i monitorowanie kondycji, jak również elastyczne dostosowanie liczby węzłów obliczeniowych w puli ([skalowanie](#scaling-compute-resources)).

Do każdego węzła, który jest dodawany do puli zostaje przypisana unikatowa nazwa i adres IP. Gdy węzeł zostanie usunięty z puli, wszelkie zmiany wprowadzone w systemie operacyjnym lub w plikach zostaną utracone, a jego nazwa i adres IP zostaną zwolnione do użytku w przyszłości. Gdy węzeł opuści pulę, oznacza to, że zakończył się jego okres istnienia.

Podczas tworzenia puli można określić następujące atrybuty:

* Wersja i system operacyjny węzła obliczeniowego
* Typ węzła obliczeniowego i docelowa liczba węzłów
* Rozmiar węzłów obliczeniowych
* Zasady skalowania
* Zasady planowania zadań podrzędnych
* Stan komunikacji węzłów obliczeniowych
* Zadanie podrzędne uruchamiania dla węzłów obliczeniowych
* Pakiety aplikacji
* Konfiguracja sieci

Każde z tych ustawień zostało dokładniej opisane w poniższych sekcjach.

> [!IMPORTANT]
> Konta usługi Batch mają domyślny limit przydziału, który ogranicza liczbę rdzeni na koncie usługi Batch. Liczba rdzeni odpowiada liczbie węzłów obliczeniowych. Domyślne limity przydziału oraz instrukcje dotyczące [zwiększania limitów przydziału](batch-quota-limit.md#increase-a-quota) znajdują się w artykule [Quotas and limits for the Azure Batch service (Limity przydziału i limity dla usługi Azure Batch)](batch-quota-limit.md). Przyczyną tego, że pula nie osiągnęła docelowej liczby węzłów, może być przydział rdzeni.
>

### <a name="compute-node-operating-system-and-version"></a>Wersja i system operacyjny węzła obliczeniowego

Podczas tworzenia puli usługi Batch można określić konfigurację maszyny wirtualnej platformy Azure i typ systemu operacyjnego, który chcesz uruchomić w każdym węźle obliczeniowym w puli. Dwa typy konfiguracji dostępne w usłudze Batch to:

* **Konfiguracja maszyny wirtualnej**, która określa, że Pula składa się z maszyn wirtualnych platformy Azure. Te maszyny wirtualne można tworzyć na podstawie obrazów systemu Windows albo Linux.

    Podczas tworzenia puli na podstawie konfiguracji usługi Virtual Machines należy określić nie tylko rozmiar węzłów i obrazów użytych do ich utworzenia, ale także **odwołanie do obrazu maszyny wirtualnej** i **jednostkę SKU węzła agenta** usługi Batch do zainstalowania w węzłach. Więcej informacji na temat określania powyższych właściwości puli znajduje się w artykule [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Aprowizowanie węzłów obliczeniowych systemu Linux w pulach usługi Azure Batch). Możesz opcjonalnie dołączyć co najmniej jeden pusty dysk danych do puli maszyn wirtualnych utworzonej z obrazów witryny Marketplace lub uwzględnić dyski danych w niestandardowych obrazach używanych do tworzenia maszyn wirtualnych. W przypadku dołączania dysków z danymi należy zainstalować i sformatować dyski z poziomu maszyny wirtualnej w celu ich użycia.

* **Konfiguracja Cloud Services**, która określa, że Pula składa się z węzłów Cloud Services platformy Azure. Cloud Services udostępnia *tylko*węzły obliczeniowe systemu Windows.

    Listę systemów operacyjnych dostępnych dla pul konfiguracji usług Cloud Services można znaleźć w temacie [Azure Guest OS releases and SDK compatibility matrix](../cloud-services/cloud-services-guestos-update-matrix.md) (Macierz zgodności zestawów SDK i wersji systemów operacyjnych gościa platformy Azure). Podczas tworzenia puli, która zawiera węzły usług Cloud Services, należy określić rozmiar węzła i jego *rodzinę systemów operacyjnych*. Cloud Services jest wdrażana na platformie Azure szybciej niż w przypadku maszyn wirtualnych z systemem Windows. Jeśli potrzebujesz pul systemu Windows, może się okazać, że usługi Cloud Services oferują korzystną wydajność związaną z czasem wdrażania.

    * *Rodzina systemów operacyjnych* określa też, które wersje platformy .NET są instalowane z systemem operacyjnym.
    * Podobnie jak w przypadku ról procesów roboczych w ramach usług Cloud Services można określić *wersję systemu operacyjnego* (więcej informacji o rolach procesów roboczych można znaleźć w sekcji [Cloud Services overview (Omówienie usług Cloud Services)](../cloud-services/cloud-services-choose-me.md)).
    * Tak samo jak w przypadku ról procesów roboczych zaleca się określenie `*` dla *wersji systemu operacyjnego*, aby węzły były uaktualniane automatycznie, a niedawno wydane wersje nie wymagały żadnej pracy. Podstawowym warunkiem wybrania określonej wersji systemu operacyjnego jest upewnienie się, czy została zachowana zgodność aplikacji, przez zezwolenie na testowanie zgodności z poprzednimi wersjami przed zezwoleniem na aktualizację wersji. Po weryfikacji można zaktualizować *wersję systemu operacyjnego* dla puli i zainstalować nowy obraz systemu operacyjnego — wszystkie uruchomione zadania podrzędne zostaną przerwane i ponownie umieszczone w kolejce.

W przypadku tworzenia puli musisz wybrać odpowiednią wartość elementu **nodeAgentSkuId**w zależności od systemu operacyjnego podstawowego obrazu dysku VHD. Można uzyskać mapowanie dostępnych identyfikatorów jednostek SKU agenta węzła na odwołania do obrazów systemu operacyjnego, wywołując [listę obsługiwanych jednostek SKU agenta węzła](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus) .

#### <a name="custom-images-for-virtual-machine-pools"></a>Niestandardowe obrazy dla pul usługi Virtual Machines

Aby dowiedzieć się, jak utworzyć pulę z obrazami niestandardowymi, zobacz [Używanie galerii obrazów udostępnionych do tworzenia puli niestandardowej](batch-sig-images.md).

Alternatywnie można utworzyć niestandardową pulę maszyn wirtualnych przy użyciu zasobu [obrazu zarządzanego](batch-custom-images.md) . Aby uzyskać informacje dotyczące przygotowywania niestandardowych obrazów systemu Linux na podstawie maszyn wirtualnych platformy Azure, zobacz [How to create an image of a virtual machine or VHD](../virtual-machines/linux/capture-image.md) (Jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego). Aby uzyskać informacje na temat przygotowywania niestandardowych obrazów systemu Windows na podstawie maszyn wirtualnych platformy Azure, zobacz [Create a managed image of a generalized VM in Azure (Tworzenie zarządzanego obrazu uogólnionej maszyny wirtualnej na platformie Azure)](../virtual-machines/windows/capture-image-resource.md).

#### <a name="container-support-in-virtual-machine-pools"></a>Obsługa kontenerów w pulach maszyn wirtualnych

Podczas tworzenia puli konfiguracji maszyny wirtualnej za pomocą interfejsów API usługi Batch możesz skonfigurować pulę do uruchamiania zadań w kontenerach platformy Docker. Obecnie musisz utworzyć pulę przy użyciu obrazu, który obsługuje kontenery platformy Docker. Użyj systemu Windows Server 2016 Datacenter z obrazem kontenerów z witryny Azure Marketplace lub podać niestandardowy obraz maszyny wirtualnej obejmujący platformę Docker Community Edition lub Enterprise Edition i wszystkie wymagane sterowniki. Ustawienia puli muszą zawierać [konfigurację kontenera](/rest/api/batchservice/pool/add), która kopiuje obrazy kontenera do maszyn wirtualnych po utworzeniu puli. Zadania uruchamiane na puli mogą następnie odwoływać się do obrazów kontenera i opcji uruchamiania kontenera.

Aby uzyskać więcej informacji, zobacz [Run Docker container applications on Azure Batch](batch-docker-container-workloads.md) (Uruchamianie aplikacji kontenera platformy Docker w usłudze Azure Batch).

## <a name="compute-node-type-and-target-number-of-nodes"></a>Typ węzła obliczeniowego i docelowa liczba węzłów

Podczas tworzenia puli można określić pożądane typy węzłów obliczeniowych i docelową liczbę każdego z nich. Istnieją dwa typy węzłów obliczeniowych:

* **Dedykowane węzły obliczeniowe.** Dedykowane węzły obliczeniowe są zarezerwowane dla konkretnych obciążeń. Są one droższe niż węzły o niskim priorytecie, ale mają gwarancję, że nigdy nie zostaną przerwane.

* **Węzły obliczeniowe o niskim priorytecie.** Węzły o niskim priorytecie wykorzystują nadwyżkę wydajności na platformie Azure do uruchamiania obciążeń usługi Batch. Węzły o niskim priorytecie są mniej kosztowne na godzinę niż w przypadku węzłów dedykowanych i umożliwiają korzystanie z obciążeń wymagających znacznej mocy obliczeniowej. Aby uzyskać więcej informacji, zobacz [Use low-priority VMs with Batch](batch-low-pri-vms.md) (Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch).

    Węzły obliczeniowe o niskim priorytecie mogą być przerywane, jeśli na platformie Azure nie będzie wystarczającej nadwyżki wydajności. Jeśli węzeł zostanie przerwany podczas przetwarzania zadań, zadania te są ponownie umieszczane w kolejce, a następnie ponownie uruchamiane, kiedy węzeł obliczeniowy znowu stanie się dostępny. Węzły o niskim priorytecie są dobrą opcją w przypadku obciążeń, dla których czas ukończenia zadania jest elastyczny, a praca jest rozproszona na wielu węzłach. Przed podjęciem decyzji o użyciu węzłów o niskim priorytecie w danym scenariuszu upewnij się, że wszelkie zadania utracone ze względu na wywłaszczania są minimalne i łatwe do odtworzenia.

W tej samej puli mogą istnieć węzły obliczeniowe o niskim priorytecie i węzły dedykowane. Każdy typ węzła &mdash; o niskim priorytecie i dedykowany &mdash; ma swoje własne ustawienie docelowe, dla którego można określić żądaną liczbę węzłów.

Liczbę węzłów obliczeniowych określa się jako *docelową*, ponieważ w niektórych sytuacjach wybrana liczba węzłów w puli nie zostanie osiągnięta. Na przykład pula może nie osiągnąć wartości docelowej, jeśli wcześniej zostanie osiągnięty [podstawowy przydział](batch-quota-limit.md) dla konta usługi Batch. Lub Pula może nie osiągnąć celu, jeśli zastosowano formułę skalowania automatycznego do puli, która ogranicza maksymalną liczbę węzłów.

Aby uzyskać informacje dotyczące cen węzłów obliczeniowych o niskim priorytecie i dedykowanych węzłów obliczeniowych, zobacz [Batch — cennik](https://azure.microsoft.com/pricing/details/batch/).

### <a name="size-of-the-compute-nodes"></a>Rozmiar węzłów obliczeniowych

Podczas tworzenia puli usługi Azure Batch możesz wybierać spośród prawie wszystkich rodzin i rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje szereg rozmiarów maszyn wirtualnych dla różnych obciążeń, w tym specjalizowane rozmiary maszyn wirtualnych [HPC](../virtual-machines/linux/sizes-hpc.md) lub [z obsługą procesorów GPU](../virtual-machines/linux/sizes-gpu.md). 

Aby uzyskać więcej informacji, zobacz [Choose a VM size for compute nodes in an Azure Batch pool](batch-pool-vm-sizes.md) (Wybieranie rozmiaru maszyn wirtualnych dla węzłów obliczeniowych w puli usługi Azure Batch).

### <a name="scaling-policy"></a>Zasady skalowania

W przypadku obciążeń dynamicznych można napisać i zastosować [formułę skalowania](#scaling-compute-resources) automatycznego do puli. Usługa Batch okresowo oblicza formułę i dostosowuje liczbę węzłów w ramach puli na podstawie różnych wybieranych parametrów puli, zadań i zadań podrzędnych.

### <a name="task-scheduling-policy"></a>Zasady planowania zadań podrzędnych

Opcja konfiguracji [maksymalnej liczby zadań podrzędnych na węzeł](batch-parallel-node-tasks.md) określa maksymalną liczbę zadań podrzędnych, które można uruchomić równolegle na poszczególnych węzłach obliczeniowych w ramach puli.

Domyślna konfiguracja polega na tym, że w danym momencie w węźle jest uruchamiane jedno zadanie podrzędne, ale istnieją scenariusze, w których korzystne jest, aby w danym momencie w węźle było wykonywanych więcej zadań podrzędnych. Zobacz [przykładowy scenariusz](batch-parallel-node-tasks.md#example-scenario) w artykule dotyczącym [równoczesnych zadań podrzędnych węzła](batch-parallel-node-tasks.md), aby dowiedzieć się, jak można korzystać z wielu zadań podrzędnych w jednym węźle.

Można również określić *Typ wypełnienia*, który określa, czy wsadowe rozprasza zadania równomiernie we wszystkich węzłach w puli, czy pakiety z każdym węzłem z maksymalną liczbą zadań przed przypisaniem zadań do innego węzła.

### <a name="communication-status-for-compute-nodes"></a>Stan komunikacji węzłów obliczeniowych

W większości przypadków zadania podrzędne działają niezależnie i nie muszą komunikować się ze sobą. Jednak w niektórych aplikacjach będzie wymagana komunikacja między zadaniami podrzędnymi (np. w [scenariuszach MPI](batch-mpi.md)).

Możesz skonfigurować pulę w celu umożliwienia **komunikacji międzywęzłowej**, aby węzły w danej puli mogły komunikować się w czasie wykonywania. Po włączeniu komunikacji międzywęzłowej węzły w pulach konfiguracji usług Cloud Services mogą komunikować się ze sobą na portach większych niż 1100, a w przypadku pul konfiguracji usługi Virtual Machines ruch nie jest ograniczony do żadnego portu.

Włączenie komunikacji między węzłami wpływa również na rozmieszczenie węzłów w klastrach i może ograniczyć maksymalną liczbę węzłów w puli z powodu ograniczeń wdrożenia. Jeśli aplikacja nie wymaga komunikacji między węzłami, usługa Batch może przydzielić potencjalnie dużą liczbę węzłów do puli z wielu różnych klastrów i centrów danych, aby umożliwić zwiększoną równoległą moc przetwarzania.

### <a name="start-tasks-for-compute-nodes"></a>Zadanie podrzędne uruchamiania dla węzłów obliczeniowych

Opcjonalne *zadanie podrzędne uruchamiania* jest wykonywane w każdym węźle po dołączeniu go do puli oraz za każdym razem, gdy węzeł zostanie ponownie uruchomiony lub odtworzony z obrazu. Zadanie podrzędne uruchamiania jest szczególnie przydatne w przypadku przygotowywania węzłów obliczeniowych do wykonywania zadań podrzędnych, takich jak instalowanie aplikacji, które będą uruchamiane przez zadania podrzędne w tych węzłach.

### <a name="application-packages"></a>Pakiety aplikacji

Można wybrać [pakiety aplikacji](#application-packages) do wdrożenia w węzłach obliczeniowych w puli. Pakiety aplikacji umożliwiają uproszczone wdrażanie aplikacji uruchamianych w ramach zadań podrzędnych oraz zarządzanie ich wersjami. Pakiety aplikacji wybrane dla puli są instalowane w każdym węźle dołączanym do puli oraz za każdym razem, gdy węzeł jest ponownie uruchamiany lub odtwarzany z obrazu.

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji. Aby uzyskać więcej informacji o używaniu pakietów aplikacji do wdrażania aplikacji w węzłach usługi Batch, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).
>
>

### <a name="network-configuration"></a>Konfiguracja sieci

Możesz określić podsieć [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) platformy Azure, w której powinny zostać utworzone węzły obliczeniowe puli. Zobacz sekcję Konfiguracja sieciowa puli, aby uzyskać więcej informacji.

## <a name="job"></a>Zadanie

Zadanie to kolekcja zadań podrzędnych. Umożliwia ono zarządzanie sposobem wykonywania obliczeń przez zadania podrzędne w węzłach obliczeniowych puli.

* Zadanie określa **pulę**, w której będzie uruchamiana praca. Możesz utworzyć nową pulę dla każdego zadania lub używać jednej puli dla wielu zadań. Możesz utworzyć pulę dla każdego zadania skojarzonego z harmonogramem zadań lub dla wszystkich zadań skojarzonych z harmonogramem zadań.
* Możesz określić opcjonalny **priorytet zadania**. Po przesłaniu zadania o wyższym priorytecie niż zadania w toku zadania podrzędne zadania o wyższym priorytecie zostaną wstawione do kolejki przed zadaniami podrzędnymi zadań o niższym priorytecie. Zadania podrzędne w zadaniach o niższym priorytecie, które zostały już uruchomione, nie są przerywane.
* **Ograniczenia** zadania umożliwiają określenie pewnych limitów dla zadań:

    Możesz ustawić **maksymalny czas zegarowy**, dzięki czemu zadanie działające dłużej niż wybrany maksymalny czas zegarowy oraz jego zadania podrzędne zostaną przerwane.

    Usługa Batch może wykrywać zadania podrzędne zakończone niepowodzeniem, a następnie ponownie próbować je uruchomić. Możesz określić **maksymalną liczbę ponownych prób wykonania zadania podrzędnego** jako ograniczenie decydujące m.in. o tym, czy ponowna próba wykonania zadania jest podejmowana *zawsze* czy *nigdy*. Ponowienie próby wykonania zadania oznacza, że zadanie jest ponownie kolejkowane do uruchomienia.

* Aplikacja kliencka może dodawać zadania podrzędne do zadania. Można również wybrać [zadanie podrzędne Menedżera zadań](#job-manager-task). Zadanie podrzędne Menedżera zadań zawiera informacje niezbędne do utworzenia wymaganych zadań podrzędnych danego zadania. Jest ono uruchamiane na jednym z węzłów obliczeniowych w puli. Zadanie podrzędne Menedżera zadań jest obsługiwane w usłudze Batch w sposób specjalny — jest umieszczane w kolejce natychmiast po utworzeniu zadania i uruchamiane ponownie, jeśli zakończy się niepowodzeniem. Zadanie podrzędne Menedżera zadań jest *wymagane* w przypadku zadań tworzonych w [harmonogramie zadań](#scheduled-jobs), ponieważ jest jedynym sposobem definiowania zadań podrzędnych przed utworzeniem wystąpienia zadania.
* Domyślnie zadania pozostają aktywne do momentu ukończenia zdań podrzędnych odpowiadających danemu zadaniu. To zachowanie można zmienić tak, aby zadanie było automatycznie przerywane po ukończeniu wszystkich powiązanych z nim zadań podrzędnych. Ustaw dla właściwości **onAllTasksComplete** zadania ([OnAllTasksComplete][net_onalltaskscomplete] na platformie .NET w usłudze Batch) wartość *terminatejob*, aby automatycznie zakończyć zadanie po ukończeniu wszystkich powiązanych z nim zadań podrzędnych.

    Usługa Batch traktuje zadanie *bez* zadań do wykonania wszystkich zadań. W związku z tym ta opcja jest najczęściej używana w przypadku [zadania podrzędnego Menedżera zadań](#job-manager-task). Jeśli chcesz użyć opcji automatycznego przerywania zadań bez Menedżera zadań, musisz początkowo ustawić właściwość **onAllTasksComplete** nowego zadania na wartość *noaction*. Po dodaniu wszystkich podrzędnych do zadania zmień tę wartość na *terminatejob*.

### <a name="job-priority"></a>Priorytet zadań

Zadaniom tworzonym w usłudze Batch można nadać priorytet. Usługa Batch używa wartości priorytetu zadania do określania kolejności planowania zadań w ramach konta (nie należy mylić tego pojęcia z [zadaniem zaplanowanym](#scheduled-jobs)). Wartości priorytetu mieszczą się w zakresie od -1000 do 1000, gdzie -1000 oznacza najniższy priorytet, a 1000 najwyższy. Aby zaktualizować priorytet zadania, wywołaj operację [Aktualizuj właściwości zadania][rest_update_job] (interfejs REST usługi Batch) lub zmień właściwość [CloudJob.Priority][net_cloudjob_priority] (platforma .NET usługi Batch).

W ramach tego samego konta zadania o wyższym priorytecie mają pierwszeństwo planowania nad zadaniami o niższym priorytecie. Zadanie o wyższym priorytecie na jednym koncie nie ma pierwszeństwa planowania nad innym zadaniem o niższym priorytecie na innym koncie.

Planowanie zadań między pulami odbywa się niezależnie. Między różnymi pulami nie ma żadnej gwarancji, że zadanie o wyższym priorytecie zostanie zaplanowane jako pierwsze, jeśli w skojarzonej z nim puli brakuje bezczynnych węzłów. W tej samej puli zadania o tym samym priorytecie mają równe szanse na zaplanowanie.

### <a name="scheduled-jobs"></a>Zaplanowane zadania

[Harmonogramy zadań][rest_job_schedules] umożliwiają tworzenie zadań cyklicznych w ramach usługi Batch. Harmonogram zadań określa, kiedy uruchamiać zadania i zawiera specyfikacje zadań do uruchomienia. Możesz wybrać czas trwania harmonogramu — jak długo i kiedy obowiązuje harmonogram — oraz jak często w tym okresie powinny być tworzone zadania.

## <a name="task"></a>Zadanie

Zadanie podrzędne to jednostka obliczeniowa skojarzona z zadaniem. Jest ono uruchamiane w węźle. Zadania są przypisywane do węzła w celu wykonania lub są umieszczane w kolejce, dopóki węzeł nie zostanie zwolniony. Mówiąc prosto, zadanie podrzędne służy do uruchamiania co najmniej jednego programu lub skryptu w węźle obliczeniowym w celu wykonania założonej pracy.

Podczas tworzenia zadania podrzędnego można określić:

* **Wiersz polecenia** zadania podrzędnego. Jest to wiersz polecenia, który powoduje uruchomienie aplikacji lub skryptu w węźle obliczeniowym.

    Należy pamiętać, że wiersz polecenia nie jest uruchamiany w ramach powłoki. W związku z tym nie może natywnie korzystać z zalet funkcji powłoki, takich jak rozszerzenie [zmiennej środowiskowej](#environment-settings-for-tasks) (w tym `PATH`). Aby korzystać z tych funkcji, należy wywołać powłokę w wierszu polecenia — na przykład uruchamiając plik `cmd.exe` w węzłach systemu Windows lub polecenie `/bin/sh` w systemie Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Jeśli w ramach zadań podrzędnych należy uruchomić aplikację lub skrypt, który nie należy do elementu `PATH` lub zmiennych środowiskowych odwołania, wywołaj powłokę jawnie w wierszu polecenia zadania podrzędnego.
* **Pliki zasobów** zawierające dane do przetworzenia. Te pliki są automatycznie kopiowane do węzła z usługi Blob Storage na koncie usługi Azure Storage przed wykonaniem wiersza polecenia zadania podrzędnego. Więcej informacji znajduje się w sekcjach [Zadanie podrzędne uruchamiania](#start-task) oraz [Pliki i katalogi](#files-and-directories).
* **Zmienne środowiskowe** wymagane w aplikacji. Więcej informacji znajduje się w sekcji [Ustawienia środowiska dla zadań podrzędnych](#environment-settings-for-tasks).
* **Ograniczenia**, zgodnie z którymi powinno działać zadanie podrzędne. Na przykład ograniczenia obejmują maksymalny czas działania zadania podrzędnego, maksymalna liczba prób ponownego wykonania zadania podrzędnego zakończonego niepowodzeniem i maksymalny czas przechowywania plików w katalogu roboczym zadania podrzędnego.
* **Pakiety aplikacji** do wdrożenia w obrębie węzła obliczeniowego, w których zgodnie z harmonogramem będzie uruchamiane zadanie podrzędne. [Pakiety aplikacji](#application-packages) umożliwiają uproszczone wdrażanie aplikacji uruchamianych w ramach zadań podrzędnych oraz zarządzanie ich wersjami. Pakiety aplikacji na poziomie zadania podrzędnego są szczególnie użyteczne w środowiskach z udostępnioną pulą, w których różne zadania są uruchamiane w jednej puli, a pula nie jest usuwana po ukończeniu zadania. Jeśli liczba zadań podrzędnych w zadaniu jest mniejsza niż liczba węzłów w puli, pakiety aplikacji zadania podrzędnego mogą minimalizować ilość transferowanych danych, ponieważ aplikacja jest wdrażana tylko dla węzłów, w których odbywa się uruchamianie zadań podrzędnych.
* Odwołanie do **obrazu kontenera** w usłudze Docker Hub lub prywatny rejestr i dodatkowe ustawienia do tworzenia kontenera platformy Docker, w którym zadanie jest uruchamiane w węźle. Te informacje musisz podać tylko wtedy, gdy pula została skonfigurowana za pomocą konfiguracji kontenera.

> [!NOTE]
> Maksymalny okres istnienia zadania podrzędnego od momentu dodania go do zadania do jego ukończenia wynosi 180 dni. Ukończone zadania podrzędne są utrwalone przez 7 dni. Dane dla zadań podrzędnych nieukończonych w ciągu maksymalnego okresu istnienia nie są dostępne.

Oprócz zadań podrzędnych zdefiniowanych do wykonywania obliczeń w węźle w usłudze Batch dostępne są również następujące specjalne zadania podrzędne:

* [Zadanie podrzędne uruchamiania](#start-task)
* [Zadanie podrzędne Menedżera zadań](#job-manager-task)
* [Zadania podrzędne przygotowania i zwolnienia zadań](#job-preparation-and-release-tasks)
* Zadania obejmujące wiele wystąpień
* [Zależności zadań](#task-dependencies)

### <a name="start-task"></a>Zadanie uruchamiania

Kojarząc **zadanie podrzędne uruchamiania** z pulą, można przygotować środowisko operacyjne dla jego węzłów. Na przykład można wykonać akcje, takie jak instalowanie aplikacji, które są uruchamiane w ramach zadań podrzędnych, i uruchamianie procesów w tle. Zadanie podrzędne uruchamiania jest uruchamiane za każdym razem, gdy zostaje uruchomiony węzeł, pod warunkiem, że pozostaje on w puli, również w przypadku gdy węzeł zostaje dodany do puli po raz pierwszy albo jest ponownie uruchamiany bądź odtwarzany z obrazu.

Główną korzyścią płynącą z zadania podrzędnego uruchamiania jest to, że może ono zawierać wszystkie informacje niezbędne do konfiguracji węzła obliczeniowego oraz instalacji aplikacji potrzebnych do wykonania zadania podrzędnego. Dlatego zwiększenie liczby węzłów w puli jest proste jak określenie nowej liczby węzłów docelowych. Zadanie podrzędne uruchamiania udostępnia usłudze Batch informacje wymagane do skonfigurowania nowych węzłów i przygotowania ich do akceptowania zadań podrzędnych.

Podobnie jak w przypadku dowolnego zadania podrzędnego usługi Azure Batch, można określić listę **plików zasobów** w usłudze [Azure Storage][azure_storage] (oprócz **wiersza polecenia**) do wykonania. Usługa Batch najpierw kopiuje pliki zasobów do węzła z usługi Azure Storage, a następnie uruchamia wiersz polecenia. W przypadku zadania podrzędnego uruchamiania w puli lista plików zawiera zazwyczaj aplikacje zadania podrzędnego i jego zależności.

Jednak zadanie podrzędne uruchamiania może również uwzględnić dane odwołania do użycia przez wszystkie zadania podrzędne, które są uruchamiane w węźle obliczeniowym. Można na przykład w wierszu polecenia zadania podrzędnego uruchamiania wykonać operację `robocopy` w celu skopiowania plików aplikacji (które zostały określone jako pliki zasobów i pobrane do węzła) z poziomu [katalogu roboczego](#files-and-directories) zadania podrzędnego uruchamiania do [folderu udostępnionego](#files-and-directories), a następnie uruchomić instalatora MSI lub `setup.exe`.

Zwykle najlepiej jest, jeśli usługa Batch będzie oczekiwać na zakończenie zadania podrzędnego uruchamiania, zanim będzie można uznać węzeł za gotowy do przypisania do niego zadań podrzędnych, ale można to ustawienie skonfigurować.

Jeśli zadanie podrzędne uruchamiania w węźle obliczeniowym zakończy się niepowodzeniem, stan węzła zostanie zaktualizowany w celu poinformowania o awarii i węzeł nie będzie przypisany do żadnych zadań podrzędnych. Zadanie podrzędne uruchamiania może zakończyć się niepowodzeniem, jeśli wystąpi problem z kopiowaniem plików zasobów z magazynu lub jeśli proces wykonywany przez wiersz polecenia zwróci kod zakończenia różny od zera.

W przypadku dodawania lub aktualizacji zadania podrzędnego uruchamiania do istniejącej puli należy ponownie uruchomić jego węzły obliczeniowe w celu zastosowania zadania podrzędnego uruchamiania do węzłów.

>[!NOTE]
> W usłudze Azure Batch wprowadzono ograniczenia dotyczące łącznego rozmiaru zadania uruchamiania, który obejmuje pliki zasobów oraz zmienne środowiskowe. Jeśli musisz zmniejszyć rozmiar zadania uruchomienia, masz do dyspozycji dwie metody:
>
> 1. Można rozpowszechniać dane lub aplikacje w poszczególnych węzłach puli usługi Batch za pomocą aplikacji. Aby uzyskać więcej informacji na temat pakietów aplikacji, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).
> 2. Możesz ręcznie utworzyć skompresowane archiwum zawierające pliki aplikacji. Przekaż skompresowane archiwum do usługi Azure Storage jako obiekt blob. Określ skompresowane archiwum jako plik zasobów dla zadania podrzędnego uruchamiania. Przed uruchomieniem wiersza polecenia zadania podrzędnego uruchamiania rozpakuj archiwum z wiersza polecenia. 
>
>    Aby rozpakować archiwum, można użyć wybranego narzędzia do archiwizacji. Narzędzie użyte do rozpakowywania archiwum trzeba będzie uwzględnić jako plik zasobów zadania podrzędnego uruchamiania.
>
>

### <a name="job-manager-task"></a>Zadanie Menedżera zadań

**Zadanie podrzędne Menedżera zadań** jest zazwyczaj używane do kontrolowania i/lub monitorowania wykonywania zadań — na przykład w celu utworzenia i przesłania zadań podrzędnych powiązanych z zadaniem należy określić dodatkowe zadania podrzędne do uruchomienia i wybrać termin zakończenia pracy. Zadanie podrzędne Menedżera zadań nie jest jednak ograniczone do tych działań. Jest to w pełni użyteczne zadanie podrzędne, które może wykonywać wszystkie akcje wymagane dla zadania. Na przykład zadanie podrzędne Menedżera zadań może pobrać plik określony jako parametr, przeanalizować zawartość tego pliku i przesłać dodatkowe zadania podrzędne na podstawie tej zawartości.

Zadanie podrzędne Menedżera zadań jest uruchamiane przed innymi zadaniami podrzędnymi. Oferuje ono następujące funkcje:

* Zostaje automatycznie przesyłane jako zadanie przez usługę Batch po utworzeniu zadania.
* Zostaje zaplanowane do wykonania przed innymi zadaniami w ramach zadania.
* Jego skojarzony węzeł jest ostatnim do usunięcia z puli w przypadku zmniejszania puli.
* Jego zakończenie może mieć związek z zakończeniem wszystkich zadań podrzędnych w zadaniu.
* Zadanie podrzędne Menedżera zadań otrzymuje najwyższy priorytet, jeśli musi zostać ponownie uruchomione. Jeśli węzeł bezczynny jest niedostępny, usługa Batch może przerwać jedno z innych uruchomionych zadań podrzędnych w puli, aby zwolnić miejsce na uruchomienie zadania podrzędnego Menedżera zadań.
* Zadanie podrzędne Menedżera zadań w ramach jednego zadania nie ma pierwszeństwa przed zadaniami podrzędnymi innych zadań. W zadaniach przestrzegane są tylko priorytety na poziomie zadań.

### <a name="job-preparation-and-release-tasks"></a>Zadania przygotowania i zwolnienia zadań

Usługa Batch oferuje zadania podrzędne przygotowywania zadania na potrzeby konfiguracji przed wykonaniem zadania. Zadania podrzędne zwolnienia zadania to zadania konserwacji lub czyszczenia po wykonaniu zadania.

* **Zadanie podrzędne przygotowania zadania** — zadanie podrzędne przygotowania zadania jest uruchamiane na wszystkich węzłach obliczeniowych zaplanowanych do uruchamiania zadań podrzędnych, zanim zostaną wykonane inne zadania podrzędne zadania. Zadanie podrzędne przygotowania zadania umożliwia kopiowanie danych udostępnionych wszystkim zadaniom podrzędnym, ale na przykład unikatowych dla zadania.
* **Zadanie podrzędne zwolnienia zadania** — po zakończeniu zadania zadanie podrzędne zwolnienia zadania jest uruchamiane w każdym węźle w puli, który wykonał przynajmniej jedno zadanie podrzędne. Zadanie podrzędne zwolnienia zadania umożliwia usuwanie danych skopiowanych przy użyciu zadania podrzędnego przygotowania zadania lub kompresję i przekazywanie na przykład danych dzienników diagnostycznych.

Zadania podrzędne przygotowania i zwolnienia zadania pozwalają na wybranie wiersza polecenia do uruchomienia po wywołaniu zadania podrzędnego. Oferują one takie funkcje jak pobieranie plików, wykonywanie z podwyższonym poziomem uprawnień, niestandardowe zmienne środowiskowe, maksymalny czas trwania wykonywania, liczba ponownych prób i czas przechowywania pliku.

Więcej informacji na temat zadań przygotowania i zwolnienia zadań znajduje się w temacie [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Uruchamianie zadań przygotowania i ukończenia zadań w węzłach obliczeniowych w usłudze Azure Batch).

### <a name="multi-instance-task"></a>Zadanie podrzędne obejmujące wiele wystąpień

[Zadanie obejmujące wiele wystąpień](batch-mpi.md) jest zadaniem, które jest konfigurowane do uruchamiania w więcej niż jednym węźle obliczeniowym równocześnie. Zadania podrzędne obejmujące wiele wystąpień umożliwiają wdrożenie scenariuszy obliczeń o wysokiej wydajności, które wymagają grupy węzłów obliczeniowych alokowanych wspólnie do przetwarzania pojedynczego obciążenia (np. interfejs MPI).

Szczegółowe omówienie dotyczące uruchamiania zadań MPI w usłudze Batch przy użyciu biblioteki usługi Batch dla platformy .NET znajdują się w temacie [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md) (Używanie zadań podrzędnych obejmujących wiele wystąpień do uruchamiania aplikacji MPI w usłudze Azure Batch).

### <a name="task-dependencies"></a>Zależności zadań

[Zależności zadań podrzędnych](batch-task-dependencies.md), jak sama nazwa wskazuje, pozwalają na określenie, że wykonanie zadania podrzędnego zależy od ukończenia innych zadań tego typu. Ta funkcja zapewnia obsługę w sytuacjach, w których zadanie „podrzędne” pobiera dane wyjściowe zadania „nadrzędnego” lub gdy zadanie nadrzędne wykonuje inicjowanie wymagane przez zadanie podrzędne. Aby użyć tej funkcji, należy najpierw włączyć zależności zadania w zadaniu w usłudze Batch. Następnie dla każdego zadania, które jest zależne od innego (lub wielu innych), należy określić zadania, od których zależy zadanie.

Zależności zadań podrzędnych umożliwiają konfigurację takich scenariuszy jak poniższe:

* *zadanie_podrzędne_B* zależy od *zadania_podrzędnego_A* (wykonywanie *zadania_podrzędnego_B* nie rozpocznie się, dopóki nie zostanie ukończone *zadanie_podrzędne_A*).
* *zadanie_podrzędne_C* zależy od *zadania_podrzędnego_A* i *zadania_podrzędnego_B*.
* *zadanie_podrzędne_D* zależy od wcześniejszego wykonania zakresu zadań podrzędnych, np. zadań od *1* do *10*.

Zapoznaj się z tematem [Task dependencies in Azure Batch](batch-task-dependencies.md) (Zależności zadań podrzędnych w usłudze Azure Batch) oraz przykładem kodu [TaskDependencies][github_sample_taskdeps] w repozytorium GitHub [azure-batch-samples][github_samples] w celu uzyskania szczegółowych informacji o tej funkcji.

## <a name="environment-settings-for-tasks"></a>Ustawienia środowiska dla zadań

Każde zadanie podrzędne wykonywane przez usługę Batch ma dostęp do zmiennych środowiskowych ustawionych w węzłach obliczeniowych. Obejmuje to zmienne środowiskowe zdefiniowane przez usługę Batch ([zdefiniowane przez usługę][msdn_env_vars]) i niestandardowe zmienne środowiskowe, które można zdefiniować dla zadań podrzędnych. Aplikacje i skrypty wykonywane przez zadania podrzędne mają dostęp do tych zmiennych środowiskowych podczas wykonywania.

Można ustawić niestandardowe zmienne środowiskowe na poziomie zadania podrzędnego lub zadania, podając informacje o właściwości *ustawień środowiska* dla tych jednostek. Zobacz na przykład operację [Dodaj zadanie podrzędne do zadania][rest_add_task] (interfejs API REST usługi Batch) lub właściwości [CloudTask.EnvironmentSettings][net_cloudtask_env] i [CloudJob.CommonEnvironmentSettings][net_job_env] na platformie .NET usługi Batch.

Usługa lub aplikacja kliencka może pobrać zmienne środowiskowe zadania podrzędnego, zdefiniowane przez usługę i niestandardowe, za pomocą operacji [Uzyskaj informacje o zadaniu podrzędnym][rest_get_task_info] (interfejs API REST usługi Batch) lub uzyskując dostęp do właściwości [CloudTask.EnvironmentSettings][net_cloudtask_env] (platforma .NET usługi Batch). Procesy wykonywane w węźle obliczeniowym mają również dostęp do wszystkich zmiennych środowiskowych, np. przy użyciu znanej składni `%VARIABLE_NAME%` (Windows) lub `$VARIABLE_NAME` (Linux).

Pełna lista wszystkich zmiennych środowiskowych zdefiniowanych przez usługę jest dostępna w artykule dotyczącym [zmiennych środowiskowych węzłów obliczeniowych][msdn_env_vars].

## <a name="files-and-directories"></a>Pliki i katalogi

Każde zadanie podrzędne ma *katalog roboczy*, w którym tworzy pliki i katalogi (ich liczba może również wynosić zero). Ten katalog roboczy może służyć do przechowywania programu uruchamianego przez zadanie podrzędne, przetwarzania danych oraz danych końcowych przetwarzania. Wszystkie pliki i katalogi zadania podrzędnego należą do użytkownika zadania podrzędnego.

Usługa Batch przedstawia część systemu plików w węźle w postaci *katalogu głównego*. Zadania podrzędne mogą uzyskać dostęp do katalogu głównego, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_ROOT_DIR`. Więcej informacji na temat korzystania ze zmiennych środowiskowych znajduje się w temacie[Environment settings for tasks](#environment-settings-for-tasks) (Ustawienia środowiska dla zadań).

Katalog główny zawiera następującą strukturę katalogu:

![Struktura katalogu węzła obliczeniowego][1]

* **aplikacje**: zawiera informacje o szczegółach pakietów aplikacji zainstalowanych w węźle obliczeniowym. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_APP_PACKAGE`.

* **fsmounts**: katalog zawiera wszystkie systemy plików, które są zainstalowane w węźle obliczeniowym. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_MOUNTS_DIR`. Aby uzyskać więcej informacji, zobacz [Instalowanie wirtualnego systemu plików w puli usługi Batch](virtual-file-mount.md).

* **shared**: ten katalog zapewnia prawa do odczytu i zapisu dla *wszystkich* zadań podrzędnych wykonywanych w węźle. Każde zadanie podrzędne uruchamiane w węźle może tworzyć, odczytywać, aktualizować i usuwać pliki w tym katalogu. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_SHARED_DIR`.

* **startup**: ten katalog jest używany jako katalog roboczy przez zadanie podrzędne uruchamiania. W tym miejscu są przechowywane wszystkie pliki pobrane do węzła przez zadanie podrzędne uruchamiania. Zadanie podrzędne uruchamiania może tworzyć, odczytywać, aktualizować i usuwać pliki w tym katalogu. Zadania podrzędne mogą uzyskać dostęp do tego katalogu, odwołując się do zmiennej środowiskowej `AZ_BATCH_NODE_STARTUP_DIR`.

* **nietrwały**: ten katalog jest przeznaczony do użytku wewnętrznego. Nie ma gwarancji, że wszystkie pliki w tym katalogu lub sam katalog będą istnieć w przyszłości.

* elementy **robocze**: ten katalog zawiera katalogi zadań i ich zadań w węźle obliczeniowym.

* **Zadania**: w katalogu elementy **robocze** katalog jest tworzony dla każdego zadania, które jest uruchamiane w węźle. Jest on dostępny przez odwołanie do `AZ_BATCH_TASK_DIR` zmiennej środowiskowej.

    W ramach każdego katalogu zadań usługa Batch tworzy katalog roboczy (`wd`), którego unikatowa ścieżka jest określana przez zmienną środowiskową `AZ_BATCH_TASK_WORKING_DIR`. Ten katalog zapewnia prawa do odczytu i zapisu zadania. Zadanie podrzędne może tworzyć, odczytywać, aktualizować i usuwać pliki w tym katalogu. Ten katalog jest zachowywany na podstawie ograniczenia *RetentionTime* wybranego dla zadania podrzędnego.

    `stdout.txt` i `stderr.txt`: te pliki są zapisywane w folderze zadań podrzędnych podczas wykonywania zadania tego typu.

> [!IMPORTANT]
> Gdy węzeł zostanie usunięty z puli, *wszystkie* pliki, które są przechowywane w węźle, zostaną usunięte.
>
>

## <a name="application-packages"></a>Pakiety aplikacji

Funkcja [pakietów aplikacji](batch-application-packages.md) umożliwia łatwe zarządzanie aplikacjami oraz ich wdrażanie do węzłów obliczeniowych w pulach. Można przekazywać wiele wersji aplikacji uruchamianych przez zadania podrzędne, w tym pliki binarne i pliki obsługi, oraz zarządzać tymi wersjami. Następnie można automatycznie wdrożyć co najmniej jedną z tych aplikacji w węzłach obliczeniowych puli.

Pakiety aplikacji może wybierać na poziomie puli i zadania podrzędnego. Po określeniu pakietów aplikacji w puli aplikacja jest wdrażana w każdym węźle puli. Po określeniu zadań pakietów aplikacji dla zadania podrzędnego aplikacja jest wdrażana tylko w węzłach, w których zaplanowano co najmniej jedno z zadań podrzędnych zadania, przed uruchomieniem wiersza polecenia zadania podrzędnego.

Usługa Batch obsługuje szczegółowe informacje o pracy z usługą Azure Storage, aby przechowywać i wdrażać pakiety aplikacji w węzłach obliczeniowych w celu uproszczenia kodu oraz pracy związanej z zarządzaniem.

Aby uzyskać więcej informacji o funkcji pakietów aplikacji, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).

> [!NOTE]
> W przypadku dodawania pakietów aplikacji na poziomie puli do *istniejącej* puli musisz ponownie uruchomić jej węzły obliczeniowe dla pakietów aplikacji do wdrożenia w węzłach.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Okres istnienia puli i węzła obliczeniowego

Podczas opracowywania rozwiązania usługi Azure Batch należy podjąć decyzję projektową dotyczącą tego, jak i kiedy będą tworzone pule i jak długo będą dostępne w tych pulach węzły obliczeniowe.

Z jednej strony można utworzyć pulę dla każdego przesyłanego zadania i usunąć pulę, gdy tylko zakończy się wykonywanie zadań podrzędnych. Pozwala to maksymalnie zwiększyć użycie, ponieważ węzły są przydzielane tylko wtedy, gdy to konieczne, i są zamykane, gdy tylko przejdą w stan bezczynności. Oznacza to, że zadanie musi oczekiwać na przydzielenie węzłów, jednak należy pamiętać, że zadania podrzędne zostaną zaplanowane do wykonania, gdy tylko poszczególne węzły zostaną udostępnione, przydzielone, a zadanie podrzędne uruchamiania zostanie ukończone. Usługa Batch *nie* oczekuje, aż wszystkie węzły w puli zostaną udostępnione przed przypisaniem zadań podrzędnych do węzłów. Dzięki temu zapewnia maksymalne wykorzystanie wszystkich dostępnych węzłów.

Z drugiej strony, jeśli natychmiastowe uruchomienie zadań ma najwyższy priorytet, pula może zostać utworzona przed czasem, a jej węzły mogą zostać udostępnione przed przesłaniem zadań. W tym scenariuszu zadania podrzędne mogą być uruchamiane natychmiast, ale podczas oczekiwania na ich przypisanie węzły mogą być w stanie bezczynności.

W przypadku zmiennego, ale ciągłego obciążenia zwykle jest stosowane rozwiązanie mieszane. Możesz korzystać z puli, do której przesyła się wiele zadań, ale która może skalować liczbę węzłów w górę lub w dół zgodnie z obciążeniem zadania (zobacz [Skalowanie zasobów obliczeniowych](#scaling-compute-resources) w poniższej sekcji). Można to zrobić w sposób reaktywny, w oparciu o bieżące obciążenie, lub aktywny, jeśli obciążenie można przewidzieć.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Konfiguracja sieci wirtualnej i zapory 

Podczas aprowizowania puli węzłów obliczeniowych w usłudze Batch możesz ją skojarzyć z podsiecią [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) platformy Azure. Aby użyć sieci wirtualnej platformy Azure, interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md).  

### <a name="vnet-requirements"></a>Wymagania dotyczące sieci wirtualnej

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Aby uzyskać więcej informacji na temat konfigurowania puli usługi Batch w sieci wirtualnej, zobacz [Create a pool of virtual machines with your virtual network](batch-virtual-network.md) (Tworzenie puli maszyn wirtualnych przy użyciu sieci wirtualnej).

## <a name="scaling-compute-resources"></a>Skalowanie zasobów obliczeniowych

[Skalowanie automatyczne](batch-automatic-scaling.md) pozwala na to, by usługa Batch dynamicznie dostosowywała liczbę węzłów obliczeniowych w puli zgodnie z bieżącym obciążeniem i użyciem zasobów w ramach scenariusza obliczeniowego. Dzięki temu można zmniejszyć całkowity koszt działania aplikacji przy użyciu tylko potrzebnych zasobów, zwalniając te zbędne.

Automatyczne skalowanie można włączyć, pisząc [formułę automatycznego skalowania](batch-automatic-scaling.md#automatic-scaling-formulas) i kojarząc ją z pulą. Usługa Batch używa formuły do określenia docelowej liczby węzłów w puli dla kolejnego interwału skalowania (interwału, który możesz skonfigurować). Ustawienia automatycznego skalowania puli można wybrać podczas jej tworzenia. Można również włączyć skalowanie puli później. Masz również możliwość aktualizowania ustawień skalowania już włączonych w puli.

Na przykład może być wymagane zadanie przesyłania dużej liczby zadań do wykonania. Do puli można przypisać formułę skalowania, która dostosowuje liczbę węzłów w puli w oparciu o bieżącą liczbę zadań podrzędnych w kolejce oraz szybkość ich wykonywania w ramach zadania. Usługa Batch okresowo ocenia formułę i zmienia rozmiar puli w oparciu o obciążenie i innych ustawień formuły. Usługa dodaje węzły w przypadku dużej liczby zadań podrzędnych w kolejce i usuwa węzły w przypadku braku zadań podrzędnych, które znajdują się w kolejce lub są uruchomione.

Formuła skalowania może opierać się na następujących metrykach:

* **Metryki czasu** — na podstawie danych statystycznych zbieranych co pięć minut przez określoną liczbę godzin.
* **Metryki zasobów** — na podstawie użycia procesora, wykorzystania przepustowości, użycia pamięci i liczby węzłów.
* **Metryki zadań podrzędnych** — na podstawie stanu zadania podrzędnego, takiego jak *Aktywne* (w kolejce), *Uruchomione* lub *Ukończone*.

Ponieważ automatyczne skalowanie zmniejsza liczbę węzłów obliczeniowych w puli, należy rozważyć sposób obsługi zadań podrzędnych wykonywanych w czasie operacji zmniejszania tej liczby. Aby to umożliwić, usługa Batch oferuje *opcje cofnięcia alokacji węzłów* umieszczanych w formułach. Możesz na przykład zdecydować, że przed usunięciem węzła z puli uruchomione zadania podrzędne będą zatrzymywane natychmiast i ponownie umieszczane w kolejce do wykonania w innym węźle albo ich wykonywanie zostanie najpierw ukończone.

Więcej informacji na temat automatycznego skalowania aplikacji znajduje się w temacie [Automatically scale compute nodes in an Azure Batch pool](batch-automatic-scaling.md) (Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch).

> [!TIP]
> Aby maksymalnie zwiększyć wykorzystanie zasobów obliczeniowych, ustaw docelową liczbę węzłów na zero na końcu zadania, ale zezwól, aby uruchomione zadania zostały ukończone.
>
>

## <a name="security-with-certificates"></a>Zabezpieczenia oparte na certyfikatach

Zazwyczaj certyfikatów należy użyć podczas szyfrowania i odszyfrowywania poufnych informacji dotyczących zadań podrzędnych, np. klucza [konta usługi Azure Storage][azure_storage]. Aby to umożliwić, można zainstalować certyfikaty w węzłach. Zaszyfrowane klucze tajne są przekazywane do zadań za pomocą parametrów wiersza polecenia lub osadzane w jednym z zasobów zadań, a zainstalowanych certyfikatów można użyć do ich odszyfrowania.

Aby dodać certyfikat do konta usługi Batch, należy użyć operacji [Dodaj certyfikat][rest_add_cert] (interfejs API REST usługi Batch) lub metody [CertificateOperations.CreateCertificate][net_create_cert] (platforma .NET usługi Batch). Następnie można skojarzyć certyfikat z nową lub istniejącą pulą. Gdy certyfikat zostaje skojarzony z pulą, usługa Batch instaluje certyfikat w każdym węźle w puli. Usługa Batch instaluje odpowiednie certyfikaty podczas uruchamiania węzła przed uruchomieniem dowolnych zadań podrzędnych (w tym zadania podrzędnego uruchamiania i zadania podrzędnego Menedżera zadań).

W przypadku dodawania certyfikatów do *istniejącej* puli należy ponownie uruchomić jej węzły obliczeniowe w celu zastosowania certyfikatów do węzłów.

## <a name="error-handling"></a>Obsługa błędów

Może okazać się konieczne, aby obsługiwać błędy zadań i aplikacji w ramach rozwiązania usługi Batch.

### <a name="task-failure-handling"></a>Obsługa błędów zadań

Błędy zadań można podzielić na następujące kategorie:

* **Błędy przetwarzania wstępnego**

    Jeśli nie można uruchomić zadania, dla tego zadania zostanie ustawiony błąd przetwarzania wstępnego.  

    Błędy przetwarzania wstępnego mogą być spowodowane tym, że pliki zasobów zadania podrzędnego zostały przeniesione, konto usługi Storage nie jest już dostępne lub wystąpił inny problem uniemożliwiający pomyślne skopiowanie plików do węzła.

* **Błędy przekazywania plików**

    Jeśli przekazywanie plików określonych dla zadania podrzędnego nie powiedzie się z jakiegokolwiek powodu, dla tego zadania zostanie ustawiony błąd przekazywania plików.

    Błędy przekazywania plików mogą wystąpić, jeśli sygnatura dostępu współdzielonego podana na potrzeby dostępu do usługi Azure Storage jest nieprawidłowa lub nie oferuje uprawnień do zapisu, jeśli konto magazynu jest już niedostępne, lub jeśli wystąpił inny problem uniemożliwiający pomyślne skopiowanie plików z węzła.    

* **Błędy aplikacji**

    Proces określony przez wiersz polecenia zadania podrzędnego może również zakończyć się niepowodzeniem. Proces zostaje uznany za nieudany, gdy proces wykonywany w ramach zadania zwróci kod zakończenia różny od zera (zobacz *Kody zakończenia zadania podrzędnego* w następnej sekcji).

    W przypadku błędów aplikacji można skonfigurować usługę Batch do automatycznego ponawiania próby wykonania zadania podrzędnego określoną liczbę razy.

* **Błędy ograniczenia**

    Można ustawić ograniczenie określające maksymalny czas trwania zadania, *maxWallClockTime*. Może to być przydatne do zakończenia zadań, które nie mogą kontynuować działania.

    Po przekroczeniu maksymalnego czasu zadanie podrzędne jest oznaczane jako *ukończone*, ale kod zakończenia ma wartość `0xC000013A`, a pole *schedulingError* jest oznaczane jako `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Błędy debugowania aplikacji

* `stderr` i `stdout`

    W czasie wykonywania aplikacja może tworzyć diagnostyczne dane wyjściowe, których można użyć do rozwiązywania problemów. Jak wspomniano we wcześniejszej sekcji [Pliki i katalogi](#files-and-directories), usługa Batch wysyła zapisuje standardowe dane wyjściowe i standardowe dane wyjściowe błędów w plikach `stdout.txt` i `stderr.txt` znajdujących się w katalogu zadań podrzędnych w węźle obliczeniowym. Aby pobrać te pliki, można użyć portalu Azure lub jednego z zestawów SDK usługi Batch. Te pliki można pobrać w celu rozwiązania problemów za pomocą elementów [ComputeNode.GetNodeFile][net_getfile_node] i [CloudTask.GetNodeFile][net_getfile_task] w bibliotece platformy .NET usługi Batch.

* **Kody zakończenia zadania podrzędnego**

    Jak wspomniano wcześniej, zadanie podrzędne jest oznaczone jako nieudane przez usługę Batch, jeśli proces wykonywany przez zadanie podrzędne zwraca kod zakończenia różny od zera. Gdy zadanie podrzędne wykonuje proces, usługa Batch wypełnia właściwości kodu zakończenia zadania podrzędnego przy użyciu *zwracanego kodu procesu*. Należy pamiętać, że kod zakończenia zadania podrzędnego **nie** jest określany przez usługę Batch. Kod zakończenia zadania podrzędnego jest określany przez sam proces lub system operacyjny, w którym proces jest wykonywany.

### <a name="accounting-for-task-failures-or-interruptions"></a>Uwzględnianie błędów zadań lub przerw w zadaniach 

Od czasu do czasu zadania podrzędne mogą zakończyć się niepowodzeniem lub zostać przerwane. Awarii może ulec sama aplikacja zadania podrzędnego, może zostać przeprowadzony ponowny rozruch węzła, w którym zostało uruchomione zadanie podrzędne lub węzeł może zostać usunięty z puli podczas operacji zmiany rozmiaru, jeśli zasady cofania alokacji puli zostały ustawione na natychmiastowe usuwanie węzłów bez oczekiwania na zakończenie zadań podrzędnych. We wszystkich przypadkach zadanie podrzędne może zostać automatycznie ponownie umieszczone w kolejce przy użyciu usługi Batch do wykonania w innym węźle.

Istnieje również możliwość, że sporadyczny problem może przestać odpowiadać lub trwać zbyt długo. Można ustawić maksymalny interwał wykonywania zadania podrzędnego. Po jego przekroczeniu usługa Batch przerywa działanie aplikacji zadania podrzędnego.

### <a name="connecting-to-compute-nodes"></a>Nawiązywanie połączenia z węzłami obliczeniowymi

Aby przeprowadzić dodatkowe debugowanie i rozwiązywanie problemów, można zalogować się zdalnie do węzła obliczeniowego. W portalu Azure można pobrać plik protokołu RDP w przypadku węzłów systemu Windows oraz uzyskać informacje o połączeniu z protokołem SSH w przypadku węzłów systemu Linux. Można to również zrobić przy użyciu interfejsów API usługi Batch — na przykład przy użyciu [platformy .NET w usłudze Batch][net_rdpfile] lub [języka Python w usłudze Batch](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Aby połączyć się z węzłem za pośrednictwem protokołu RDP lub SSH, musisz najpierw utworzyć użytkownika w węźle. W tym celu możesz użyć witryny Azure Portal, [dodać konto użytkownika do węzła][rest_create_user] za pomocą interfejsu API REST usługi Batch, wywołać metodę [ComputeNode.CreateComputeNodeUser][net_create_user] na platformie .NET usługi Batch lub wywołać metodę [add_user][py_add_user] w module języka Python usługi Batch.
>
>

Jeśli chcesz ograniczyć lub wyłączyć dostęp do węzłów obliczeniowych za pośrednictwem protokołu RDP lub SSH, zobacz [Configure or disable remote access to compute nodes in an Azure Batch pool (Konfigurowanie lub wyłączanie dostępu zdalnego do węzłów obliczeniowych w puli usługi Azure Batch)](pool-endpoint-configuration.md).

### <a name="troubleshooting-problematic-compute-nodes"></a>Rozwiązywanie problemów z węzłami obliczeniowymi

W sytuacjach, w których niektóre z zadań kończą się niepowodzeniem, aplikacja kliencka lub usługa Batch mogą badać metadane nieudanych zadań w celu identyfikacji nieprawidłowo funkcjonującego węzła. Każdemu węzłowi w puli zostaje nadany unikatowy identyfikator, a węzeł, w którym jest uruchomione zadanie, jest dołączany do metadanych zadania. Po zidentyfikowaniu problemu dotyczącego węzła można wykonać kilka powiązanych czynności:

* **Ponownie uruchom węzeł** ([REST][rest_reboot] | [.NET][net_reboot])

    Ponowne uruchomienie węzła może czasami usunąć ukryte problemy, takie jak zablokowane procesy lub procesy, które uległy awarii. Jeśli pula używa zadania uruchamiania, a zadanie zawiera zadanie przygotowania zadania, zostaną one wykonane po ponownym uruchomieniu węzła.
* **Odtwórz węzeł z obrazu** ([REST][rest_reimage] | [.NET][net_reimage])

    Spowoduje to ponowne zainstalowanie systemu operacyjnego w węźle. Podobnie jak w przypadku ponownego rozruchu węzła zadania uruchamiania i zadania przygotowania zadania są uruchamiane ponownie po odtworzeniu węzła z obrazu.
* **Usuń węzeł z puli** ([REST][rest_remove] | [.NET][net_remove])

    Czasami konieczne jest całkowite usunięcie węzła z puli.
* **Wyłącz planowanie zadań podrzędnych w węźle** ([REST][rest_offline] | [.NET][net_offline])

    Ta czynność przełącza węzeł w tryb „offline”, aby nie zostały do niego przypisane żadne dalsze zadania podrzędne, ale pozwala na dalsze działanie węzła i jego obecność w puli. Dzięki temu można dalej badać przyczyny błędów bez utraty danych nieudanego zadania, podczas gdy węzeł nie powoduje dodatkowych błędów zadania. Można na przykład wyłączyć planowanie zadań podrzędnych w węźle, a następnie [zalogować się zdalnie](#connecting-to-compute-nodes), aby sprawdzić dzienniki zdarzeń węzła lub wykonać inne operacje związane z rozwiązywaniem problemów. Po zakończeniu badania można przenieść węzeł z powrotem do trybu online, włączając planowanie zadań ([rest][rest_online] | [.NET][net_online]) lub wykonując jedną z innych czynności omówionych wcześniej.

> [!IMPORTANT]
> Wszystkie akcje opisane w tej sekcji — ponowny rozruch, odtwarzanie z obrazu, usuwanie, wyłączanie planowania zadań podrzędnych — umożliwiają określenie sposobu obsługi zadań podrzędnych uruchomionych aktualnie w węźle podczas wykonywania akcji. Na przykład po wyłączeniu planowania zadań podrzędnych w węźle za pomocą biblioteki klienta platformy .NET w usłudze Batch można określić wartość wyliczeniową [DisableComputeNodeSchedulingOption][net_offline_option], aby wskazać, czy należy **przerwać** uruchomione zadania podrzędne, **umieścić je ponownie w kolejce** do planowania w innych węzłach lub zezwolić na ukończenie uruchomionych zadań przed wykonaniem akcji (**TaskCompletion**).
>
>

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](quick-run-dotnet.md) lub języka [Python](quick-run-python.md). Te przewodniki Szybki start zawierają omówienie przykładowej aplikacji, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych i stosuje usługę Azure Storage do tymczasowego przechowywania i pobierania pliku obciążenia.
* Pobierz i zainstaluj narzędzie [Batch Explorer][batch_labs], aby używać go podczas opracowywania rozwiązań usługi Batch. Użyj narzędzia Batch Explorer do tworzenia, debugowania i monitorowania aplikacji usługi Azure Batch. 
* Zobacz zasoby społeczności: forum [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), [repozytorium społeczności usługi Batch](https://github.com/Azure/Batch) oraz [forum usługi Azure Batch][batch_forum] w witrynie MSDN. 

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[batch_labs]: https://azure.github.io/BatchExplorer/
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: https://docs.microsoft.com/azure/python/

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
