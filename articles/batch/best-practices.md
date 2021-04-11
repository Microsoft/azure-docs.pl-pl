---
title: Najlepsze rozwiązania
description: Poznaj najlepsze rozwiązania i przydatne porady dotyczące tworzenia rozwiązań Azure Batch.
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: 7ef94b07a5131726c42a94088fd3ee1f413dbec7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802356"
---
# <a name="azure-batch-best-practices"></a>Azure Batch najlepszych praktyk

W tym artykule omówiono zbiór najlepszych rozwiązań i przydatne porady dotyczące efektywnego korzystania z usługi Azure Batch w oparciu o środowiska życiowe w usłudze Batch. Te porady mogą pomóc zwiększyć wydajność i uniknąć pułapek projektu w rozwiązaniach Azure Batch.

> [!TIP]
> Aby uzyskać wskazówki dotyczące zabezpieczeń w Azure Batch, zobacz [najlepsze rozwiązania w zakresie zabezpieczeń i zgodności usługi Batch](security-best-practices.md).

## <a name="pools"></a>Pule

[Pule](nodes-and-pools.md#pools) to zasoby obliczeniowe służące do wykonywania zadań w usłudze Batch. W poniższych sekcjach przedstawiono zalecenia dotyczące pracy z pulami usługi Batch.

### <a name="pool-configuration-and-naming"></a>Konfiguracja puli i nazewnictwo

- **Tryb alokacji puli:** Podczas tworzenia konta usługi Batch można wybrać jeden z dwóch trybów alokacji puli: **Usługa Batch** lub **subskrypcja użytkownika**. W większości przypadków należy użyć domyślnego trybu usługi Batch, w którym pule są przyliczane w tle w ramach subskrypcji zarządzanych przez usługę Batch. W alternatywnym trybie subskrypcji użytkownika maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Konta subskrypcji użytkowników są głównie używane do włączania ważnych, ale małych podzestawów scenariuszy. Więcej informacji na temat trybu subskrypcji użytkownika można znaleźć w [dodatkowej konfiguracji trybu subskrypcji użytkownika](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **"virtualMachineConfiguration" lub "cloudServiceConfiguration":** Obecnie można tworzyć pule przy użyciu jednej z konfiguracji, nowe pule należy konfigurować przy użyciu "virtualMachineConfiguration", a nie "cloudServiceConfiguration". Wszystkie bieżące i nowe funkcje wsadowe będą obsługiwane przez pule konfiguracji maszyny wirtualnej. Pule konfiguracji Cloud Services nie obsługują wszystkich funkcji i nie są planowane żadne nowe możliwości. Nie będzie można tworzyć nowych pul "cloudServiceConfiguration" ani dodawać nowych węzłów do istniejących pul [po 29 lutego 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Aby uzyskać więcej informacji, zobacz [Migrowanie konfiguracji puli partii z Cloud Services do maszyny wirtualnej](batch-pool-cloud-service-to-virtual-machine-configuration.md).

- **Podczas określania zadania do mapowania puli należy wziąć pod uwagę czas wykonywania zadania i zadania:** Jeśli zadania składają się głównie z zadań wykonywanych przed chwilą, a oczekiwana całkowita liczba zadań jest mała, w związku z czym ogólna oczekiwany czas wykonywania zadania nie jest długa, nie należy przydzielać nowej puli dla każdego zadania. Czas wykonywania tego zadania będzie zmniejszać czas ich alokacji.

- **Pule powinny mieć więcej niż jeden węzeł obliczeniowy:** Nie ma gwarancji, że poszczególne węzły są zawsze dostępne. Nietypowe błędy sprzętowe, aktualizacje systemu operacyjnego i hosta innych problemów mogą spowodować, że poszczególne węzły przestaną być w trybie offline. Jeśli obciążenie wsadowe wymaga deterministycznego, gwarantowanego postępu, należy przydzielić pule z wieloma węzłami.

- **Nie należy używać obrazów z nieoczekiwanymi datami zakończenia cyklu życia (EOL).**
    Zdecydowanie zaleca się uniknięcie obrazów z nieoczekiwaną datą zakończenia okresu istnienia (EOL). Te daty można odnaleźć za pośrednictwem [ `ListSupportedImages` interfejsu API](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages), [programu PowerShell](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)lub interfejsu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/batch/pool/supported-images). Jest odpowiedzialny za okresowe odświeżanie widoku dat EOL związanych z pulami oraz Migrowanie obciążeń przed datą EOL. Jeśli używany jest obraz niestandardowy z określonym agentem węzła, należy się upewnić, że w przypadku obrazu, dla którego obraz niestandardowy jest wyprowadzany lub wyrównany, należy wykonać następujące czynności.

- **Nie należy ponownie używać nazw zasobów.**
    Zasoby wsadowe (zadania, pule itp.) często zaczynają się i są dostępne w czasie. Można na przykład utworzyć pulę w poniedziałek, usunąć ją we wtorek, a następnie utworzyć kolejną pulę w czwartek. Każdy nowy tworzony zasób powinien mieć unikatową nazwę, która nie została wcześniej użyta. Można to zrobić przy użyciu identyfikatora GUID (jako całej nazwy zasobu lub jego części) lub osadzania czasu utworzenia zasobu w nazwie zasobu. Funkcja Batch obsługuje [Właściwość DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname), która może być używana do nadawania zasobowi czytelnej nazwy, nawet jeśli rzeczywisty identyfikator zasobu to coś, co nie jest przyjazne dla człowieka. Używanie unikatowych nazw ułatwia odróżnienie określonego zasobu w dziennikach i metrykach. Powoduje również usunięcie niejednoznaczności, jeśli kiedykolwiek trzeba było korzystać z pomocy technicznej dla zasobu.


- **Ciągłość podczas konserwacji i niepowodzenia puli:** Najlepszym rozwiązaniem jest dynamiczne korzystanie z pul przez zadania. Jeśli zadania korzystają z tej samej puli dla wszystkich elementów, istnieje możliwość, że zadania nie będą działać, jeśli coś się nie dzieje z pulą. Jest to szczególnie ważne w przypadku obciążeń zależnych od czasu. Aby rozwiązać ten problem, wybierz lub Utwórz pulę dynamicznie przy zaplanowaniu każdego zadania lub aby zastąpić nazwę puli, aby można było ominąć pulę w złej kondycji.

- **Ciągłość działania podczas konserwacji i niepowodzenia puli:** Istnieje wiele powodów, dla których Pula może nie zwiększyć się do żądanego rozmiaru, takich jak błędy wewnętrzne, ograniczenia pojemności itd. Z tego powodu należy być gotowy do przekierowania zadań w innej puli (prawdopodobnie z innym rozmiarem maszyny wirtualnej — partia ta obsługuje tę funkcję za pośrednictwem [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)), w razie potrzeby. Należy unikać używania identyfikatora puli statycznej z oczekiwaniami, który nigdy nie zostanie usunięty, i nigdy nie ulegnie zmianie.

### <a name="pool-lifetime-and-billing"></a>Okres istnienia puli i rozliczenia

Okres istnienia puli może się różnić w zależności od metody alokacji i opcji stosowanych do konfiguracji puli. Pule mogą mieć dowolny okres istnienia i różną liczbę węzłów obliczeniowych w puli w dowolnym momencie. Odpowiedzialność za zarządzanie węzłami obliczeniowymi w puli w sposób jawny lub za pomocą funkcji udostępnianych przez usługę ([Automatyczne skalowanie](nodes-and-pools.md#automatic-scaling-policy) lub [autopule](nodes-and-pools.md#autopools)).

- **Przechowuj pule jako świeże:** Zmień rozmiar pul na zero co kilka miesięcy, aby upewnić się, że są używane [najnowsze aktualizacje agenta węzła i poprawki błędów](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md). W puli nie będą odbierane aktualizacje agentów węzła, chyba że zostanie on ponownie utworzony lub zmieniono rozmiar do 0 węzłów obliczeniowych. Przed ponownym utworzeniem lub zmianą puli zaleca się pobranie wszelkich dzienników agentów węzłów na potrzeby debugowania, zgodnie z opisem w sekcji [węzły](#nodes) .

- **Ponowne tworzenie puli:** Na podobnej uwadze nie zaleca się usuwania i ponownego tworzenia pul. Zamiast tego Utwórz nową pulę, zaktualizuj istniejące zadania, aby wskazywały nową pulę. Po przeniesieniu wszystkich zadań do nowej puli Usuń starą pulę.

- **Wydajność i rozliczenia puli:** Sama partia nie wiąże się z dodatkowymi opłatami, ale opłaty są naliczane za użyte zasoby obliczeniowe. Opłaty są naliczane za każdy węzeł obliczeniowy w puli, bez względu na stan, w którym się znajduje. Obejmuje to opłaty wymagane do uruchomienia węzła, takie jak koszty magazynu i sieci. Aby dowiedzieć się więcej o najlepszych rozwiązaniach, zobacz [Analiza kosztów i budżety dla Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Błędy alokacji puli

Błędy alokacji puli mogą wystąpić w dowolnym momencie podczas pierwszego przydziału lub po zmianie rozmiaru. Może to być spowodowane tymczasowym wyczerpaniem wydajności w regionie lub błędami w innych usługach platformy Azure, na których bazuje usługa Batch. Limit przydziału rdzenia nie jest gwarancją, ale jest raczej ograniczeniem.

### <a name="unplanned-downtime"></a>Nieplanowany przestój

Istnieje możliwość, że pule usługi Batch mogą mieć zdarzenia przestoju na platformie Azure. Należy pamiętać o tym, gdy planujesz i opracowujesz swój scenariusz lub przepływ pracy dla usługi Batch.

W przypadku awarii węzła funkcja Batch automatycznie próbuje odzyskać te węzły obliczeniowe w Twoim imieniu. Może to spowodować ponowne zaplanowanie uruchomionego zadania w węźle, który został odzyskany. Zobacz temat [projektowanie pod kątem ponownych prób](#design-for-retries-and-re-execution) , aby dowiedzieć się więcej o przerwanych zadaniach.

### <a name="custom-image-pools"></a>Pule obrazów niestandardowych

Podczas tworzenia puli Azure Batch przy użyciu konfiguracji maszyny wirtualnej należy określić obraz maszyny wirtualnej, który dostarcza system operacyjny dla każdego węzła obliczeniowego w puli. Pulę można utworzyć przy użyciu obsługiwanego obrazu witryny Azure Marketplace lub [utworzyć obraz niestandardowy z obrazem udostępnionej galerii obrazów](batch-sig-images.md). Chociaż można również utworzyć niestandardową pulę obrazów przy użyciu [zarządzanego obrazu](batch-custom-images.md) , zalecamy utworzenie niestandardowych obrazów przy użyciu udostępnionej galerii obrazów wszędzie tam, gdzie to możliwe. Używanie galerii obrazów udostępnionych ułatwia Inicjowanie obsługi pul, skalowanie większych ilości maszyn wirtualnych i zwiększanie niezawodności podczas aprowizacji maszyn wirtualnych.

### <a name="third-party-images"></a>Obrazy innych firm

Pule można tworzyć przy użyciu obrazów innych firm opublikowanych w portalu Azure Marketplace. Przy użyciu kont usługi Batch w trybie subskrypcji użytkownika może zostać wyświetlony komunikat o błędzie "Alokacja nie powiodła się z powodu sprawdzania uprawnień do zakupu w witrynie Marketplace" podczas tworzenia puli z niektórymi obrazami innych firm. Aby rozwiązać ten problem, zaakceptuj warunki określone przez wydawcę obrazu. Można to zrobić za pomocą [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/image/terms).

### <a name="azure-region-dependency"></a>Zależność regionu platformy Azure

Nie należy polegać na pojedynczym regionie świadczenia usługi Azure, jeśli jest to obciążenie czasochłonne lub produkcyjne. Rzadko występują problemy, które mogą mieć wpływ na cały region. Na przykład jeśli przetwarzanie musi rozpocząć się o określonym czasie, rozważ skalowanie w górę puli w regionie podstawowym *przed upływem czasu rozpoczęcia*. W przypadku niepowodzenia skalowania puli można wrócić do skalowania w górę puli w regionie (lub regionach) kopii zapasowej. Pule na wielu kontach w różnych regionach zapewniają gotową, łatwą do udostępnienia kopię zapasową, jeśli coś się nie udaje z inną pulą. Aby uzyskać więcej informacji, zobacz [projektowanie aplikacji pod kątem wysokiej dostępności](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Stanowiska

[Zadanie](jobs-and-tasks.md#jobs) jest kontenerem zaprojektowanym do przechowywania setek, tysięcy lub nawet milionów zadań. Postępuj zgodnie z poniższymi wskazówkami podczas tworzenia zadań.

### <a name="fewer-jobs-more-tasks"></a>Mniej zadań, więcej zadań

Korzystanie z zadania do uruchamiania pojedynczego zadania jest niewydajne. Na przykład wydajniejszym rozwiązaniem jest użycie jednego zadania zawierającego 1000 zadań zamiast tworzenia zadań 100, które zawierają 10 zadań. Uruchamianie zadań 1000, każdy z jednym zadaniem, będzie najmniej wydajnym, najwolniejszym i najtańszym podejściem do wykonania.

W związku z tym upewnij się, że nie zaprojektowano rozwiązania usługi Batch, które wymaga tysięcy jednocześnie aktywnych zadań. Nie ma przydziału dla zadań, dlatego wykonywanie wielu zadań w ramach możliwie najmniejszej liczby zadań korzysta z [przydziałów zadań i harmonogramów zadań](batch-quota-limit.md#resource-quotas).

### <a name="job-lifetime"></a>Okres istnienia zadania

Zadanie usługi Batch ma czas nieokreślony, dopóki nie zostanie usunięty z systemu. Jego stan określa, czy może akceptować więcej zadań związanych z planowaniem, czy nie.

Zadanie nie przejdzie automatycznie do stanu ukończenia, chyba że zostanie jawnie zakończone. Może to być automatycznie wyzwalane za pomocą właściwości [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) lub [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints).

Istnieje domyślny [limit przydziału aktywnego zadania i harmonogramu zadań](batch-quota-limit.md#resource-quotas). Zadania i harmonogramy zadań w stanie ukończone nie są wliczane do tego przydziału.

## <a name="tasks"></a>Zadania

[Zadania](jobs-and-tasks.md#tasks) są pojedynczymi jednostkami pracy, które składają się na zadanie. Zadania są przesyłane przez użytkownika i zaplanowane przez zadanie wsadowe na węzłach obliczeniowych. Istnieje kilka zagadnień projektowych, które należy wykonać podczas tworzenia i wykonywania zadań. W poniższych sekcjach objaśniono typowe scenariusze i sposób projektowania zadań do obsługi problemów i wydajnego działania.

### <a name="save-task-data"></a>Zapisz dane zadania

Węzły obliczeniowe są według ich charakterów tymczasowych. Istnieje wiele funkcji usługi Batch, takich jak [autopula](nodes-and-pools.md#autopools) i [Automatyczne skalowanie](nodes-and-pools.md#automatic-scaling-policy) , które mogą ułatwić usuwanie węzłów. Gdy węzły opuszczają pulę (z powodu zmiany rozmiaru lub usunięcia puli), wszystkie pliki w tych węzłach również zostaną usunięte. W związku z tym zadanie powinno przenieść wyjście z węzła, w którym jest uruchomiony, i do magazynu trwałego przed jego ukończeniem. Podobnie, jeśli zadanie nie powiedzie się, należy przenieść dzienniki wymagane do zdiagnozowania niepowodzenia do magazynu trwałego.

Usługa Batch została zintegrowana z obsługą usługi Azure Storage w celu przekazywania danych za pośrednictwem programu [OutputFiles](batch-task-output-files.md), a także wielu udostępnionych systemów plików lub można wykonać przekazywanie samodzielnie w swoich zadaniach.

### <a name="manage-task-lifetime"></a>Zarządzanie okresem istnienia zadania

Usuń zadania, gdy nie są już potrzebne, lub ustaw ograniczenie zadania [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) . Jeśli `retentionTime` jest ustawiona, program Batch automatycznie czyści miejsce na dysku używane przez zadanie po `retentionTime` wygaśnięciu.

Usuwanie zadań ma dwie rzeczy. Gwarantuje to, że nie masz kompilacji zadań w zadaniu, co może utrudnić wykonanie zapytania/znalezienie żądanego zadania (ponieważ należy przefiltrować zakończone zadania). Czyści również odpowiednie dane zadania w węźle (dostarczony `retentionTime` nie został jeszcze trafiony). Pozwala to zagwarantować, że węzły nie wypełniają danych zadania i nie zabraknie miejsca na dysku.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Przesyłanie dużej liczby zadań w kolekcji

Zadania mogą być przesyłane pojedynczo lub w kolekcjach. Przesyłaj zadania w [kolekcjach](/rest/api/batchservice/task/addcollection) nawet do 100 w czasie wykonywania zbiorczego przesyłania zadań, aby zmniejszyć obciążenie i czas przesyłania.

### <a name="set-max-tasks-per-node-appropriately"></a>Odpowiednio ustaw maksymalną liczbę zadań na węzeł

Zadanie wsadowe obsługuje zadania związane z subskrypcją w węzłach (wykonywanie większej liczby zadań niż węzeł ma rdzenie). Jest to konieczne, aby upewnić się, że zadania "pasują" do węzłów w puli. Można na przykład mieć obniżoną wydajność, Jeśli podjęto próbę zaplanowania ośmiu zadań, za pomocą których każde zużywa 25% użycia procesora CPU w jednym węźle (w puli z `taskSlotsPerNode = 8` ).

### <a name="design-for-retries-and-re-execution"></a>Projektowanie na potrzeby ponownych prób i ponownego wykonywania

Zadania mogą być automatycznie ponawiane przez partię. Istnieją dwa typy ponownych prób: sterowane przez użytkownika i wewnętrzne. Ponowne próby sterowane przez użytkownika są określane przez [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)zadania. Gdy program określony w zadaniu zostanie zakończony z kodem zakończenia innym niż zero, zadanie zostanie ponowione w celu uzyskania wartości `maxTaskRetryCount` .

Chociaż rzadko, zadanie może być ponawiane wewnętrznie ze względu na błędy w węźle obliczeniowym, na przykład nie można zaktualizować stanu wewnętrznego lub awarii w węźle, gdy zadanie jest uruchomione. Zadanie zostanie ponowione w tym samym węźle obliczeniowym, jeśli jest to możliwe, do wewnętrznego limitu przed zapisaniem zadania i oddanie zadania do ponownego zaplanowania przez partię, potencjalnie w innym węźle obliczeniowym.

Podczas wykonywania zadań w węzłach dedykowanych lub o niskim priorytecie nie ma żadnych różnic projektowych. Niezależnie od tego, czy zadanie jest zastępują uruchomione w węźle niskiego priorytetu, czy przerywane z powodu błędu w dedykowanym węźle, obie sytuacje są korygowane przez zaprojektowanie zadania w celu wytrzymania błędu.

### <a name="build-durable-tasks"></a>Tworzenie trwałych zadań

Zadania powinny zostać zaprojektowane w celu wytrzymania awarii i ponowienia próby. Jest to szczególnie ważne w przypadku długotrwałych zadań. W tym celu należy się upewnić, że zadania generują ten sam, pojedynczy wynik nawet wtedy, gdy są uruchamiane więcej niż raz. Jednym ze sposobów osiągnięcia tego celu jest przeszukanie zadań ". Innym sposobem jest upewnienie się, że zadania są idempotentne (zadania będą miały ten sam wynik niezależnie od tego, ile razy są uruchamiane).

Typowym przykładem jest zadanie kopiowania plików do węzła obliczeniowego. Proste podejście to zadanie, które kopiuje wszystkie określone pliki przy każdym uruchomieniu, co jest niewydajne i nie jest skompilowane w celu wytrzymania awarii. Zamiast tego należy utworzyć zadanie, aby upewnić się, że pliki znajdują się w węźle obliczeniowym. zadanie, które nie kopiuje już wcześniej plików. W ten sposób zadanie zostanie odebrane w tym miejscu, w którym zostało przerwane.

### <a name="avoid-short-execution-time"></a>Unikaj krótkiego czasu wykonywania

Zadania, które są uruchamiane tylko przez jeden do dwóch sekund, nie są idealnym rozwiązaniem. Spróbuj wykonać znaczną ilość pracy w pojedynczym zadaniu (10 sekund minimum, przechodząc do kilku godzin lub dni). Jeśli każde zadanie jest wykonywane przez jedną minutę (lub więcej), obciążenie planowania jako ułamek całkowitego czasu obliczeniowego jest małe.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Używanie zakresu puli dla krótkich zadań w węzłach systemu Windows

Planując zadanie w węzłach wsadowych, można wybrać, czy ma być uruchamiane z zakresem zadania czy zakresem puli. Jeśli zadanie zostanie uruchomione tylko przez krótki czas, zakres zadania może być nieefektywny ze względu na zasoby, które są konieczne do utworzenia konta użytkownika dla tego zadania. Aby zwiększyć wydajność, należy rozważyć ustawienie tych zadań na zakres puli. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadania jako użytkownika z zakresem puli](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Węzły

[Węzeł obliczeniowy](nodes-and-pools.md#nodes) jest maszyną wirtualną platformy Azure lub maszyną wirtualną usługi w chmurze, która jest przeznaczona do przetwarzania części obciążenia aplikacji. Postępuj zgodnie z poniższymi wskazówkami podczas pracy z węzłami.

### <a name="idempotent-start-tasks"></a>Zadania uruchamiania idempotentne

Podobnie jak w przypadku innych zadań, [zadanie uruchomieniowe](jobs-and-tasks.md#start-task) węzła powinno być idempotentne, ponieważ zostanie uruchomione ponownie za każdym razem, gdy węzeł zostanie uruchomiony. Zadanie idempotentne jest po prostu jednym, które generuje spójny wynik w przypadku wielokrotnego uruchomienia.

### <a name="isolated-nodes"></a>Izolowane węzły

Należy rozważyć użycie wyizolowanych rozmiarów maszyn wirtualnych w przypadku obciążeń z wymaganiami zgodności lub przepisami prawnymi. Obsługiwane rozmiary izolowane w trybie konfiguracji maszyny wirtualnej obejmują `Standard_E80ids_v4` ,,,, `Standard_M128ms` `Standard_F72s_v2` `Standard_G5` `Standard_GS5` , i `Standard_E64i_v3` . Aby uzyskać więcej informacji na temat wyizolowanych rozmiarów maszyn wirtualnych, zobacz [izolacja maszyny wirtualnej na platformie Azure](../virtual-machines/isolation.md).

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>Zarządzanie długotrwałymi usługami za pośrednictwem interfejsu usług systemu operacyjnego

Czasami istnieje potrzeba uruchomienia innego agenta obok agenta partii w węźle. Na przykład możesz chcieć zebrać dane z węzła i zgłosić je. Zalecamy wdrożenie tych agentów jako usług systemu operacyjnego, takich jak usługa systemu Windows lub `systemd` Usługa Linux.

W przypadku uruchamiania tych usług nie wolno zablokować plików na żadnych plikach w katalogach zarządzanych przez usługę Batch w węźle, ponieważ w przeciwnym razie usługa Batch nie będzie mogła usunąć tych katalogów ze względu na blokady plików. Jeśli na przykład zainstalujesz usługę systemu Windows w zadaniu uruchamiania, zamiast uruchamiania usługi bezpośrednio z katalogu roboczego zadania uruchamiania, skopiuj pliki w innym miejscu (lub jeśli pliki już istnieją, Pomiń kopię). Następnie zainstaluj usługę z tej lokalizacji. Gdy wsadowe ponownie uruchamia zadanie podrzędne, usunie katalog roboczy zadania uruchamiania i ponownie go utworzy. Dzieje się tak, ponieważ usługa ma blokady plików w innym katalogu, a nie w katalogu roboczym zadania uruchomieniowego.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Unikaj tworzenia połączeń katalogów w systemie Windows

Rozgałęzienia katalogów, czasami nazywane twardymi łączami katalogów, są trudne do rozproszenia podczas zadań i czyszczenia zadań. Użyj linków symbolicznych (linki nietrwałe) zamiast twardych linków.

### <a name="collect-the-batch-agent-logs"></a>Zbierz dzienniki agentów wsadowych

Jeśli zauważysz problem związany z zachowaniem węzła lub zadań uruchomionych w węźle, Zbierz dzienniki agentów wsadowych przed cofnięciem przydziału danego węzła. Dzienniki agenta usługi Batch można zbierać przy użyciu interfejsu API przekazywania dzienników usług Batch. Te dzienniki mogą być dostarczane do firmy Microsoft jako część biletu pomocy technicznej i mogą pomóc w rozwiązywaniu problemów z rozwiązaniem.

### <a name="manage-os-upgrades"></a>Zarządzanie uaktualnieniami systemu operacyjnego

W przypadku kont usługi Batch w trybie subskrypcji użytkownika zautomatyzowane uaktualnienia systemu operacyjnego mogą przerwać postęp zadania, zwłaszcza jeśli zadania są długotrwałe. [Kompilowanie zadań idempotentne](#build-durable-tasks) może pomóc w zmniejszeniu błędów spowodowanych przez te przerwy. Zalecamy również [Planowanie uaktualnień obrazów systemu operacyjnego przez czas, w którym zadania nie są oczekiwane](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

W przypadku pul systemu Windows program `enableAutomaticUpdates` jest domyślnie ustawiony na wartość `true` . Zezwalanie na aktualizacje automatyczne jest zalecane, ale możesz ustawić tę wartość na, `false` Jeśli chcesz mieć pewność, że aktualizacja systemu operacyjnego nie wystąpi nieoczekiwanie.

## <a name="isolation-security"></a>Zabezpieczenia izolacji

W celu przeprowadzenia izolacji, jeśli scenariusz wymaga izolowania zadań od siebie, należy to zrobić, umieszczając je w osobnych pulach. Pula to granica izolacji zabezpieczeń w usłudze Batch, a domyślnie dwie pule nie są widoczne ani nie mogą komunikować się ze sobą. Należy unikać używania odrębnych kont usługi Batch jako metody izolacji.

## <a name="moving-batch-accounts-across-regions"></a>Przeniesienie kont wsadowych między regionami

Istnieją scenariusze, w których może być pomocne przeniesienie istniejącego konta usługi Batch z jednego regionu do innego. Na przykład możesz chcieć przejść do innego regionu w ramach planowania odzyskiwania po awarii.

Kont Azure Batch nie można przenosić bezpośrednio z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager, aby wyeksportować istniejącą konfigurację konta w usłudze Batch. Następnie można przemieścić zasób w innym regionie, eksportując konto wsadowe do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrażając szablon w nowym regionie.

Po przekazaniu szablonu do nowego regionu konieczne będzie ponowne utworzenie certyfikatów, harmonogramów zadań i pakietów aplikacji. Aby zatwierdzić zmiany i zakończyć Przenoszenie konta w usłudze Batch, pamiętaj o usunięciu oryginalnego konta partii lub grupy zasobów.

Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Łączność

Zapoznaj się z poniższymi wskazówkami dotyczącymi łączności w rozwiązaniach usługi Batch.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i trasy zdefiniowane przez użytkownika (UDR)

Podczas aprowizacji pul usługi [Batch w sieci wirtualnej](batch-virtual-network.md)należy się upewnić, że są one zgodne z wytycznymi dotyczącymi korzystania z `BatchNodeManagement` tagu usług, portów, protokołów i kierunku reguły. Użycie znacznika usługi jest zdecydowanie zalecane zamiast korzystania z źródłowych adresów IP usługi Batch. Wynika to z faktu, że adresy IP mogą ulec zmianie z upływem czasu. Korzystanie z adresów IP usługi Batch bezpośrednio może spowodować niestabilność, przerwy lub awarię pul usługi Batch.

W przypadku tras zdefiniowanych przez użytkownika (UDR) Upewnij się, że masz proces w miejscu, aby okresowo aktualizować adresy IP usługi Batch w tabeli tras, ponieważ te adresy zmieniają się z upływem czasu. Aby dowiedzieć się, jak uzyskać listę adresów IP usługi Batch, zobacz [lokalne znaczniki usług](../virtual-network/service-tags-overview.md). Adresy IP usługi Batch zostaną skojarzone z `BatchNodeManagement` tagiem usługi (lub odmianą regionalną zgodną z regionem konta usługi Batch).

### <a name="honoring-dns"></a>Przestrzeganie systemu DNS

Upewnij się, że Twoje systemy są uznawane za czas wygaśnięcia (TTL) DNS dla adresu URL usługi Batch account. Ponadto upewnij się, że klienci usługi Batch i inne mechanizmy łączności z usługą Batch nie korzystają z adresów IP (lub [Utwórz pulę ze statycznymi publicznymi adresami IP](create-pool-public-ip.md) , jak opisano poniżej).

Jeśli żądania odbierają odpowiedzi HTTP na poziomie 5xx i istnieje nagłówek "Connection: Close" w odpowiedzi, klient usługi Batch powinien przestrzegać zalecenia, zamykając istniejące połączenie, ponownie rozwiązując system DNS dla adresu URL usługi Batch Account i próbuje wykonać następujące żądania dotyczące nowego połączenia.

### <a name="retry-requests-automatically"></a>Automatycznie ponów żądania

Upewnij się, że klienci usługi Batch mają odpowiednie zasady ponawiania prób w celu automatycznego ponowienia żądań, nawet w trakcie normalnej operacji, a nie wyłącznie w okresach czasu konserwacji usługi. Te zasady ponawiania powinny obejmować interwał wynoszący co najmniej 5 minut. Funkcje automatycznego ponawiania prób są dostarczane z różnymi zestawami SDK partii, takimi jak [Klasa .NET RetryPolicyProvider](/dotnet/api/microsoft.azure.batch.retrypolicyprovider).

### <a name="static-public-ip-addresses"></a>Statyczne publiczne adresy IP

Zwykle maszyny wirtualne w puli wsadowej są dostępne za pośrednictwem publicznych adresów IP, które mogą ulec zmianie w okresie istnienia puli. Może to utrudnić współpracę z bazą danych lub inną usługą zewnętrzną, która ogranicza dostęp do określonych adresów IP. Aby upewnić się, że publiczne adresy IP w puli nie zmieniają się nieoczekiwanie, można utworzyć pulę przy użyciu zestawu statycznych publicznych adresów IP, które kontrolujesz. Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch z określonymi publicznymi adresami IP](create-pool-public-ip.md).

### <a name="testing-connectivity-with-cloud-services-configuration"></a>Testowanie łączności z konfiguracją Cloud Services

Nie można użyć normalnego protokołu "ping"/ICMP z usługami w chmurze, ponieważ protokół ICMP nie jest dozwolony za pomocą modułu równoważenia obciążenia platformy Azure. Aby uzyskać więcej informacji, zobacz [łączność i obsługa sieci dla platformy Azure Cloud Services](../cloud-services/cloud-services-connectivity-and-networking-faq.md#can-i-ping-a-cloud-service).

## <a name="batch-node-underlying-dependencies"></a>Podstawowe zależności węzła wsadowego

Podczas projektowania rozwiązań wsadowych należy wziąć pod uwagę następujące zależności i ograniczenia.

### <a name="system-created-resources"></a>Zasoby utworzone przez system

Azure Batch tworzy i zarządza zestawem użytkowników i grup na maszynie wirtualnej, które nie powinny być modyfikowane. Są one następujące:

W systemie Windows:

- Użytkownik o nazwie **PoolNonAdmin**
- Grupa użytkowników o nazwie **WATaskCommon**

W systemie Linux:

- Użytkownik o nazwie **_azbatch**

### <a name="file-cleanup"></a>Czyszczenie pliku

Zadanie wsadowe aktywnie próbuje oczyścić katalog roboczy, w którym są uruchamiane zadania, po upływie okresu przechowywania. Wszystkie pliki zapisywane poza tym katalogiem są [odpowiedzialne za wyczyszczenie](#manage-task-lifetime) w celu uniknięcia wypełniania miejsca na dysku.

Automatyczne czyszczenie katalogu roboczego zostanie zablokowane, jeśli uruchomisz usługę w systemie Windows z katalogu roboczego startTask, ponieważ folder nadal jest używany. Spowoduje to spadek wydajności. Aby rozwiązać ten problem, Zmień katalog dla tej usługi na oddzielny katalog, który nie jest zarządzany przez usługę Batch.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Dowiedz się więcej o [domyślnych przydziałach, limitach i ograniczeniach Azure Batch oraz o sposobie zwiększania limitu przydziału](batch-quota-limit.md).
- Dowiedz się [, jak wykrywać i unikać błędów w operacjach w puli i węzłach w tle ](batch-pool-node-error-checking.md).
