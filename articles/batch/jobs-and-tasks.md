---
title: Zadania i zadania w Azure Batch
description: Dowiedz się więcej o zadaniach i zadaniach oraz sposobie ich użycia w przepływie pracy Azure Batch z punktu widzenia rozwoju.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95808588"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Zadania i zadania w Azure Batch

W Azure Batch *zadanie* reprezentuje jednostkę obliczeniową. *Zadanie* to zbiór tych zadań. Poniżej opisano więcej informacji o zadaniach i zadaniach oraz sposobie ich użycia w przepływie pracy Azure Batch.

## <a name="jobs"></a>Stanowiska

Zadanie to kolekcja zadań podrzędnych. Umożliwia ono zarządzanie sposobem wykonywania obliczeń przez zadania podrzędne w węzłach obliczeniowych puli.

Zadanie określa [pulę](nodes-and-pools.md#pools) , w której ma być uruchamiana praca. Możesz utworzyć nową pulę dla każdego zadania lub używać jednej puli dla wielu zadań. Można utworzyć pulę dla każdego zadania skojarzonego z [harmonogramem zadań](#scheduled-jobs)lub jednej puli dla wszystkich zadań skojarzonych z harmonogramem zadań.

### <a name="job-priority"></a>Priorytet zadań

Do utworzonych zadań można przypisać opcjonalny priorytet zadania. Usługa Batch używa wartości priorytetu zadania w celu określenia kolejności planowania (dla wszystkich zadań w ramach zadania) wtihin każdej puli.

Aby zaktualizować priorytet zadania, należy wywołać [aktualizację właściwości operacji zadania](/rest/api/batchservice/job/update) (Batch REST) lub zmodyfikować [CloudJob. Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (Batch .NET). Wartości priorytetów mieszczą się w zakresie od-1000 (najniższy priorytet) do 1000 (najwyższy priorytet).

W ramach tej samej puli zadania o wyższym priorytecie mają pierwszeństwo planowania nad zadaniami o niższym priorytecie. Zadania w zadaniach o niższym priorytecie, które są już uruchomione, nie zostaną przeniesiona przez zadania w ramach zadania o wyższym priorytecie. Zadania o takim samym poziomie priorytetu mają równą możliwość zaplanowania, a porządkowanie wykonywania zadań nie jest zdefiniowane.

Zadanie z wartością o wysokim priorytecie działającą w jednej puli nie będzie miało wpływu na planowanie zadań uruchomionych w osobnej puli lub na innym koncie wsadowym. Priorytet zadania nie ma zastosowania do [pul autopool](nodes-and-pools.md#autopools), które są tworzone podczas przesyłania zadania.

### <a name="job-constraints"></a>Ograniczenia zadania

Ograniczenia zadania umożliwiają określenie pewnych limitów dla zadań:

- Możesz ustawić **maksymalny czas zegarowy**, dzięki czemu zadanie działające dłużej niż wybrany maksymalny czas zegarowy oraz jego zadania podrzędne zostaną przerwane.
- Można określić **maksymalną liczbę ponownych prób wykonania zadania** jako ograniczenie, w tym to, czy zadanie jest zawsze ponawiane, czy nigdy nie ponawianie próby. Ponowienie próby wykonania zadania oznacza, że jeśli zadanie nie powiedzie się, zostanie ono ponownie uruchomione w kolejce w celu ponownego uruchomienia.

### <a name="job-manager-tasks-and-automatic-termination"></a>Zadania Menedżera zadań i automatyczne zakończenie

Aplikacja kliencka może dodawać zadania podrzędne do zadania. Można również wybrać [zadanie podrzędne Menedżera zadań](#job-manager-task). Zadanie podrzędne Menedżera zadań zawiera informacje niezbędne do utworzenia wymaganych zadań podrzędnych danego zadania. Jest ono uruchamiane na jednym z węzłów obliczeniowych w puli. Zadanie Menedżera zadań jest obsługiwane w odróżnieniu od partii; jest on umieszczany zaraz po utworzeniu zadania i zostanie ponownie uruchomiony, jeśli zakończy się niepowodzeniem. Zadanie Menedżera zadań jest wymagane w przypadku zadań tworzonych w ramach [harmonogramu zadań](#scheduled-jobs), ponieważ jest jedynym sposobem definiowania zadań przed wystąpieniem zadania.

Domyślnie zadania pozostają aktywne do momentu ukończenia zdań podrzędnych odpowiadających danemu zadaniu. To zachowanie można zmienić tak, aby zadanie było automatycznie przerywane po ukończeniu wszystkich powiązanych z nim zadań podrzędnych. Ustaw właściwość **onAllTasksComplete** zadania ([OnAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) w usłudze Batch .NET) na wartość `terminatejob` *, aby automatycznie kończyć zadanie, gdy wszystkie jego zadania są w stanie ukończone.

Usługa Batch traktuje zadanie *bez* zadań do wykonania wszystkich zadań. W związku z tym ta opcja jest najczęściej używana w przypadku [zadania podrzędnego Menedżera zadań](#job-manager-task). Jeśli chcesz użyć automatycznego kończenia zadania bez Menedżera zadań, należy wstępnie ustawić właściwość **onAllTasksComplete** nowego zadania na `noaction` , a następnie ustawić ją na `terminatejob` * ' dopiero po zakończeniu dodawania zadań do zadania.

### <a name="scheduled-jobs"></a>Zaplanowane zadania

[Harmonogramy zadań](/rest/api/batchservice/jobschedule) umożliwiają tworzenie zadań cyklicznych w ramach usługi Batch. Harmonogram zadań określa, kiedy uruchamiać zadania i zawiera specyfikacje zadań do uruchomienia. Możesz określić czas trwania harmonogramu (jak długo i kiedy harmonogram obowiązuje) oraz częstotliwość tworzenia zadań w zaplanowanym okresie.

## <a name="tasks"></a>Zadania

Zadanie podrzędne to jednostka obliczeniowa skojarzona z zadaniem. Jest ono uruchamiane w węźle. Zadania są przypisywane do węzła w celu wykonania lub są umieszczane w kolejce, dopóki węzeł nie zostanie zwolniony. Mówiąc prosto, zadanie podrzędne służy do uruchamiania co najmniej jednego programu lub skryptu w węźle obliczeniowym w celu wykonania założonej pracy.

Podczas tworzenia zadania podrzędnego można określić:

- **Wiersz polecenia** zadania podrzędnego. Jest to wiersz polecenia, który powoduje uruchomienie aplikacji lub skryptu w węźle obliczeniowym.

    Należy pamiętać, że wiersz polecenia nie jest uruchamiany w ramach powłoki. W związku z tym nie może natywnie korzystać z zalet funkcji powłoki, takich jak rozszerzenie [zmiennej środowiskowej](#environment-settings-for-tasks) (w tym `PATH`). Aby skorzystać z takich funkcji, należy wywołać powłokę w wierszu polecenia, na przykład uruchamiając `cmd.exe` w węzłach systemu Windows lub `/bin/sh` w systemie Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Jeśli w ramach zadań podrzędnych należy uruchomić aplikację lub skrypt, który nie należy do elementu `PATH` lub zmiennych środowiskowych odwołania, wywołaj powłokę jawnie w wierszu polecenia zadania podrzędnego.
- **Pliki zasobów** zawierające dane do przetworzenia. Te pliki są automatycznie kopiowane do węzła z usługi Blob Storage na koncie usługi Azure Storage przed wykonaniem wiersza polecenia zadania podrzędnego. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadań](#start-task) i [plików i katalogów](files-and-directories.md).
- **Zmienne środowiskowe** wymagane w aplikacji. Aby uzyskać więcej informacji, zobacz [Ustawienia środowiska dla zadań](#environment-settings-for-tasks).
- **Ograniczenia**, zgodnie z którymi powinno działać zadanie podrzędne. Na przykład ograniczenia obejmują maksymalny czas działania zadania podrzędnego, maksymalna liczba prób ponownego wykonania zadania podrzędnego zakończonego niepowodzeniem i maksymalny czas przechowywania plików w katalogu roboczym zadania podrzędnego.
- **Pakiety aplikacji** do wdrożenia w obrębie węzła obliczeniowego, w których zgodnie z harmonogramem będzie uruchamiane zadanie podrzędne. [Pakiety aplikacji](batch-application-packages.md) umożliwiają uproszczone wdrażanie aplikacji uruchamianych w ramach zadań podrzędnych oraz zarządzanie ich wersjami. Pakiety aplikacji na poziomie zadania podrzędnego są szczególnie użyteczne w środowiskach z udostępnioną pulą, w których różne zadania są uruchamiane w jednej puli, a pula nie jest usuwana po ukończeniu zadania. Jeśli liczba zadań podrzędnych w zadaniu jest mniejsza niż liczba węzłów w puli, pakiety aplikacji zadania podrzędnego mogą minimalizować ilość transferowanych danych, ponieważ aplikacja jest wdrażana tylko dla węzłów, w których odbywa się uruchamianie zadań podrzędnych.
- Odwołanie do **obrazu kontenera** w usłudze Docker Hub lub prywatny rejestr i dodatkowe ustawienia do tworzenia kontenera platformy Docker, w którym zadanie jest uruchamiane w węźle. Te informacje musisz podać tylko wtedy, gdy pula została skonfigurowana za pomocą konfiguracji kontenera.

> [!NOTE]
> Maksymalny okres istnienia zadania podrzędnego od momentu dodania go do zadania do jego ukończenia wynosi 180 dni. Ukończone zadania podrzędne są utrwalone przez 7 dni. Dane dla zadań podrzędnych nieukończonych w ciągu maksymalnego okresu istnienia nie są dostępne.

Oprócz zadań zdefiniowanych przez użytkownika do wykonywania obliczeń w węźle, usługa Batch udostępnia także kilka specjalnych zadań:

- [Zadanie podrzędne uruchamiania](#start-task)
- [Zadanie podrzędne Menedżera zadań](#job-manager-task)
- [Zadania podrzędne przygotowania i zwolnienia zadań](#job-preparation-and-release-tasks)
- [Zadania obejmujące wiele wystąpień](#multi-instance-task)
- [Zależności zadań](#task-dependencies)

### <a name="start-task"></a>Zadanie uruchamiania

Kojarząc zadanie podrzędne uruchamiania z pulą, można przygotować środowisko operacyjne dla jego węzłów. Na przykład można wykonać akcje, takie jak instalowanie aplikacji, które są uruchamiane w ramach zadań podrzędnych, i uruchamianie procesów w tle. Zadanie uruchamiania jest uruchamiane za każdym razem, gdy węzeł zostanie uruchomiony, tak długo, jak pozostanie w puli. Obejmuje to, kiedy węzeł jest po raz pierwszy dodany do puli i po ponownym uruchomieniu lub odłączeniu obrazu.

Główną korzyścią płynącą z zadania podrzędnego uruchamiania jest to, że może ono zawierać wszystkie informacje niezbędne do konfiguracji węzła obliczeniowego oraz instalacji aplikacji potrzebnych do wykonania zadania podrzędnego. Dlatego zwiększenie liczby węzłów w puli jest proste jak określenie nowej liczby węzłów docelowych. Zadanie podrzędne uruchamiania udostępnia informacje potrzebne do skonfigurowania nowych węzłów przez usługę Batch i przygotowanie ich do akceptowania zadań.

Podobnie jak w przypadku dowolnego zadania podrzędnego usługi Azure Batch, można określić listę plików zasobów w usłudze [Azure Storage](../storage/index.yml) (oprócz wiersza polecenia) do wykonania. Usługa Batch najpierw kopiuje pliki zasobów do węzła z usługi Azure Storage, a następnie uruchamia wiersz polecenia. W przypadku zadania podrzędnego uruchamiania w puli lista plików zawiera zazwyczaj aplikacje zadania podrzędnego i jego zależności.

Jednak zadanie podrzędne uruchamiania może również uwzględnić dane odwołania do użycia przez wszystkie zadania podrzędne, które są uruchamiane w węźle obliczeniowym. Na przykład wiersz polecenia zadania uruchamiania może wykonać `robocopy` operację kopiowania plików aplikacji (określonych jako pliki zasobów i pobranych do węzła) z [katalogu roboczego](files-and-directories.md) zadania podrzędnego uruchamiania do folderu **udostępnionego** , a następnie uruchomić plik MSI lub `setup.exe` .

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

### <a name="job-manager-task"></a>Zadanie Menedżera zadań

Zwykle zadanie Menedżera zadań służy do kontrolowania i/lub monitorowania wykonywania zadań. Na przykład zadania Menedżera zadań są często używane do tworzenia i przesyłania zadań dla zadania, określania dodatkowych zadań do uruchomienia i określania, kiedy praca została ukończona.

Zadanie podrzędne Menedżera zadań nie jest jednak ograniczone do tych działań. Jest to zadanie dopracowane, które może wykonywać wszystkie akcje wymagane dla danego zadania. Na przykład zadanie podrzędne Menedżera zadań może pobrać plik określony jako parametr, przeanalizować zawartość tego pliku i przesłać dodatkowe zadania podrzędne na podstawie tej zawartości.

Zadanie podrzędne Menedżera zadań jest uruchamiane przed innymi zadaniami podrzędnymi. Oferuje ono następujące funkcje:

- Zostaje automatycznie przesyłane jako zadanie przez usługę Batch po utworzeniu zadania.
- Zostaje zaplanowane do wykonania przed innymi zadaniami w ramach zadania.
- Jego skojarzony węzeł jest ostatnim do usunięcia z puli w przypadku zmniejszania puli.
- Jego zakończenie może mieć związek z zakończeniem wszystkich zadań podrzędnych w zadaniu.
- Zadanie podrzędne Menedżera zadań otrzymuje najwyższy priorytet, jeśli musi zostać ponownie uruchomione. Jeśli węzeł bezczynny jest niedostępny, usługa Batch może przerwać jedno z innych uruchomionych zadań podrzędnych w puli, aby zwolnić miejsce na uruchomienie zadania podrzędnego Menedżera zadań.
- Zadanie podrzędne Menedżera zadań w ramach jednego zadania nie ma pierwszeństwa przed zadaniami podrzędnymi innych zadań. W zadaniach przestrzegane są tylko priorytety na poziomie zadań.

### <a name="job-preparation-and-release-tasks"></a>Zadania przygotowania i zwolnienia zadań

Zadanie wsadowe zawiera zadania przygotowania zadań dla konfiguracji wykonywania przed zadaniami oraz zadania wydania zadań do konserwacji lub czyszczenia zadań.

Zadanie podrzędne przygotowania zadania jest uruchamiane we wszystkich węzłach obliczeniowych zaplanowanych do uruchamiania zadań podrzędnych, zanim zostaną wykonane inne zadania podrzędne zadania. Można na przykład użyć zadania przygotowania zadania w celu skopiowania danych, które są współużytkowane przez wszystkie zadania, ale są unikatowe dla danego zadania.

Po zakończeniu zadania zadanie podrzędne zwolnienia zadania jest uruchamiane w każdym węźle w puli, który wykonał przynajmniej jedno zadanie podrzędne. Na przykład zadanie zwolnienia zadania może usunąć dane, które zostały skopiowane przez zadanie przygotowania zadania, lub skompresować i przekazać dane dziennika diagnostycznego.

Zadania podrzędne przygotowania i zwolnienia zadania pozwalają na wybranie wiersza polecenia do uruchomienia po wywołaniu zadania podrzędnego. Oferują one takie funkcje jak pobieranie plików, wykonywanie z podwyższonym poziomem uprawnień, niestandardowe zmienne środowiskowe, maksymalny czas trwania wykonywania, liczba ponownych prób i czas przechowywania pliku.

Więcej informacji na temat zadań przygotowania i zwolnienia zadań znajduje się w temacie [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Uruchamianie zadań przygotowania i ukończenia zadań w węzłach obliczeniowych w usłudze Azure Batch).

### <a name="multi-instance-task"></a>Zadanie podrzędne obejmujące wiele wystąpień

[Zadanie obejmujące wiele wystąpień](batch-mpi.md) jest zadaniem, które jest konfigurowane do uruchamiania w więcej niż jednym węźle obliczeniowym równocześnie. Zadania obejmujące wiele wystąpień umożliwiają włączenie scenariuszy obliczeniowych o wysokiej wydajności, które wymagają grupy węzłów obliczeniowych, które są przypisywane razem do przetwarzania pojedynczego obciążenia, takiego jak interfejs przekazywania komunikatów (MPI).

Szczegółowe omówienie dotyczące uruchamiania zadań MPI w usłudze Batch przy użyciu biblioteki usługi Batch dla platformy .NET znajdują się w temacie [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md) (Używanie zadań podrzędnych obejmujących wiele wystąpień do uruchamiania aplikacji MPI w usłudze Azure Batch).

### <a name="task-dependencies"></a>Zależności zadań

[Zależności zadań podrzędnych](batch-task-dependencies.md), jak sama nazwa wskazuje, pozwalają na określenie, że wykonanie zadania podrzędnego zależy od ukończenia innych zadań tego typu. Ta funkcja zapewnia obsługę w sytuacjach, w których zadanie „podrzędne” pobiera dane wyjściowe zadania „nadrzędnego” lub gdy zadanie nadrzędne wykonuje inicjowanie wymagane przez zadanie podrzędne.

Aby użyć tej funkcji, należy najpierw [włączyć zależności zadań](batch-task-dependencies.md#enable-task-dependencies
) w ramach zadania wsadowego. Następnie dla każdego zadania, które jest zależne od innego (lub wielu innych) określ zadania, od których zadanie zależy.

Zależności zadań podrzędnych umożliwiają konfigurację takich scenariuszy jak poniższe:

- *zadanie_podrzędne_B* zależy od *zadania_podrzędnego_A* (wykonywanie *zadania_podrzędnego_B* nie rozpocznie się, dopóki nie zostanie ukończone *zadanie_podrzędne_A*).
- *zadanie_podrzędne_C* zależy od *zadania_podrzędnego_A* i *zadania_podrzędnego_B*.
- *zadanie_podrzędne_D* zależy od wcześniejszego wykonania zakresu zadań podrzędnych, np. zadań od *1* do *10*.

Aby uzyskać więcej informacji, zobacz [zależności zadań w Azure Batch](batch-task-dependencies.md) i przykład kodu [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) w repozytorium GitHub [Azure-Batch-Samples](https://github.com/Azure-Samples/azure-batch-samples) .

### <a name="environment-settings-for-tasks"></a>Ustawienia środowiska dla zadań

Każde zadanie podrzędne wykonywane przez usługę Batch ma dostęp do zmiennych środowiskowych ustawionych w węzłach obliczeniowych. Obejmuje to zmienne środowiskowe zdefiniowane przez usługę Batch ([zdefiniowane przez usługę](./batch-compute-node-environment-variables.md) i niestandardowe zmienne środowiskowe, które można zdefiniować dla zadań podrzędnych). Aplikacje i skrypty wykonywane przez zadania podrzędne mają dostęp do tych zmiennych środowiskowych podczas wykonywania.

Można ustawić niestandardowe zmienne środowiskowe na poziomie zadania podrzędnego lub zadania, podając informacje o właściwości *ustawień środowiska* dla tych jednostek. Aby uzyskać więcej informacji, zobacz [CloudTask. EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) i [CloudJob. CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) w [usłudze](/rest/api/batchservice/task/add?)Batch .NET, aby uzyskać więcej szczegółów.

Usługa lub aplikacja kliencka może pobrać zmienne środowiskowe zadania podrzędnego, zdefiniowane przez usługę i niestandardowe, za pomocą operacji [Uzyskaj informacje o zadaniu podrzędnym](/rest/api/batchservice/task/get) (interfejs API REST usługi Batch) lub uzyskując dostęp do właściwości [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (platforma .NET usługi Batch). Procesy wykonywane w węźle obliczeniowym mają również dostęp do wszystkich zmiennych środowiskowych, np. przy użyciu znanej składni `%VARIABLE_NAME%` (Windows) lub `$VARIABLE_NAME` (Linux).

Pełna lista wszystkich zmiennych środowiskowych zdefiniowanych przez usługę jest dostępna w artykule dotyczącym [zmiennych środowiskowych węzłów obliczeniowych](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [plikach i katalogach](files-and-directories.md).
