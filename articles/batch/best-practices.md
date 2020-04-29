---
title: Najlepsze rozwiązania
description: Poznaj najlepsze rozwiązania i przydatne porady dotyczące tworzenia rozwiązań Azure Batch.
ms.date: 04/03/2020
ms.topic: article
ms.openlocfilehash: 43a0020953ea44593cf38298a78547194751fc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117509"
---
# <a name="azure-batch-best-practices"></a>Azure Batch najlepszych praktyk

W tym artykule omówiono zbiór najlepszych rozwiązań dotyczących efektywnego i efektywnego korzystania z usługi Azure Batch. Te najlepsze rozwiązania wynikają z naszych rozwiązań związanych z usługą Batch i klientami usługi Batch. Ważne jest zapoznanie się z tym artykułem, aby uniknąć pułapek projektu, potencjalnych problemów z wydajnością i antywzorców podczas opracowywania usługi Batch i korzystania z niej.

W tym artykule omówiono następujące informacje:

> [!div class="checklist"]
> - Jakie są najlepsze rozwiązania
> - Dlaczego należy stosować najlepsze rozwiązania
> - Co się stanie, jeśli nie można zastosować najlepszych rozwiązań
> - Jak postępować zgodnie z najlepszymi rozwiązaniami

## <a name="pools"></a>Pule

Pule usługi Batch to zasoby obliczeniowe służące do wykonywania zadań w usłudze Batch. W poniższych sekcjach przedstawiono wskazówki dotyczące najważniejszych najlepszych rozwiązań, które należy wykonać podczas pracy z pulami wsadowymi.

### <a name="pool-configuration-and-naming"></a>Konfiguracja puli i nazewnictwo

- **Tryb alokacji puli** Podczas tworzenia konta usługi Batch można wybrać jeden z dwóch trybów alokacji puli: **Usługa Batch** lub **subskrypcja użytkownika**. W większości przypadków należy użyć domyślnego trybu usługi Batch, w którym pule są przyliczane w tle w ramach subskrypcji zarządzanych przez usługę Batch. W alternatywnym trybie subskrypcji użytkownika maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Konta subskrypcji użytkowników są głównie używane do włączania ważnych, ale małych podzestawów scenariuszy. Więcej informacji na temat trybu subskrypcji użytkownika można znaleźć w [dodatkowej konfiguracji trybu subskrypcji użytkownika](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Podczas określania zadania do mapowania puli należy wziąć pod uwagę czas wykonywania zadania i zadania.**
    Jeśli zadania składają się głównie z zadań wykonywanych przed chwilą, a oczekiwana całkowita liczba zadań jest mała, w związku z czym ogólna oczekiwany czas wykonywania zadania nie jest długa, nie należy przydzielać nowej puli dla każdego zadania. Czas wykonywania tego zadania będzie zmniejszać czas ich alokacji.

- **Pule powinny mieć więcej niż jeden węzeł obliczeniowy.**
    Nie ma gwarancji, że poszczególne węzły są zawsze dostępne. Nietypowe błędy sprzętowe, aktualizacje systemu operacyjnego i hosta innych problemów mogą spowodować, że poszczególne węzły przestaną być w trybie offline. Jeśli obciążenie wsadowe wymaga deterministycznego, gwarantowanego postępu, należy przydzielić pule z wieloma węzłami.

- **Nie należy ponownie używać nazw zasobów.**
    Zasoby wsadowe (zadania, pule itp.) często zaczynają się i są dostępne w czasie. Można na przykład utworzyć pulę w poniedziałek, usunąć ją we wtorek, a następnie utworzyć kolejną pulę w czwartek. Każdy nowy tworzony zasób powinien mieć unikatową nazwę, która nie została wcześniej użyta. Można to zrobić przy użyciu identyfikatora GUID (jako całej nazwy zasobu lub jego części) lub osadzania czasu utworzenia zasobu w nazwie zasobu. Funkcja Batch obsługuje [Właściwość DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), która może być używana do nadawania zasobowi czytelnej nazwy, nawet jeśli rzeczywisty identyfikator zasobu to coś, co nie jest przyjazne dla człowieka. Używanie unikatowych nazw ułatwia odróżnienie określonego zasobu w dziennikach i metrykach. Powoduje również usunięcie niejednoznaczności, jeśli kiedykolwiek trzeba było korzystać z pomocy technicznej dla zasobu.

- **Ciągłość podczas konserwacji i niepowodzenia puli.**
    Najlepszym rozwiązaniem jest dynamiczne korzystanie z pul przez zadania. Jeśli zadania korzystają z tej samej puli dla wszystkich elementów, istnieje możliwość, że zadania nie będą działać, jeśli coś się nie dzieje z pulą. Jest to szczególnie ważne w przypadku obciążeń zależnych od czasu. Aby rozwiązać ten problem, wybierz lub Utwórz pulę dynamicznie przy zaplanowaniu każdego zadania lub aby zastąpić nazwę puli, aby można było ominąć pulę w złej kondycji.

- **Ciągłość działania podczas konserwacji i niepowodzenia puli** Istnieje wiele możliwych przyczyn, które mogą uniemożliwić zwiększenie ilości puli do wymaganego rozmiaru, takiego jak błędy wewnętrzne, ograniczenia pojemności itd. Z tego powodu należy być gotowy do przekierowania zadań w innej puli (prawdopodobnie z innym rozmiarem maszyny wirtualnej — partia ta obsługuje tę funkcję za pośrednictwem [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)), w razie potrzeby. Należy unikać używania identyfikatora puli statycznej z oczekiwaniami, który nigdy nie zostanie usunięty, i nigdy nie ulegnie zmianie.

### <a name="pool-lifetime-and-billing"></a>Okres istnienia puli i rozliczenia

Okres istnienia puli może się różnić w zależności od metody alokacji i opcji stosowanych do konfiguracji puli. Pule mogą mieć dowolny okres istnienia i różną liczbę węzłów obliczeniowych w puli w dowolnym momencie. Odpowiedzialność za zarządzanie węzłami obliczeniowymi w puli w sposób jawny lub za pomocą funkcji udostępnianych przez usługę (automatyczne skalowanie lub autopule).

- **Przechowuj pule jako świeże.**
    Należy zmienić rozmiar pul na zero co kilka miesięcy, aby upewnić się, że są używane najnowsze aktualizacje agenta węzła i poprawki błędów. W puli nie będą odbierane aktualizacje agentów węzła, chyba że zostanie on ponownie utworzony lub zmieniono rozmiar do 0 węzłów obliczeniowych. Przed ponownym utworzeniem lub zmianą puli zaleca się pobranie wszelkich dzienników agentów węzłów na potrzeby debugowania, zgodnie z opisem w sekcji [węzły](#nodes) .

- **Ponowne tworzenie puli** Na podobnej uwadze nie zaleca się usuwania i ponownego tworzenia pul. Zamiast tego Utwórz nową pulę, zaktualizuj istniejące zadania, aby wskazywały nową pulę. Po przeniesieniu wszystkich zadań do nowej puli Usuń starą pulę.

- **Wydajność i rozliczenia puli** Sama partia nie wiąże się z dodatkowymi opłatami, ale opłaty są naliczane za użyte zasoby obliczeniowe. Opłaty są naliczane za każdy węzeł obliczeniowy w puli, bez względu na stan, w którym się znajduje. Obejmuje to opłaty wymagane do uruchomienia węzła, takie jak koszty magazynu i sieci. Aby dowiedzieć się więcej o najlepszych rozwiązaniach, zobacz [Analiza kosztów i budżety dla Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Błędy alokacji puli

Błędy alokacji puli mogą wystąpić w dowolnym momencie podczas pierwszego przydziału lub po zmianie rozmiaru. Może to być spowodowane tymczasowym wyczerpaniem wydajności w regionie lub błędami w innych usługach platformy Azure, na których bazuje usługa Batch. Limit przydziału rdzenia nie jest gwarancją, ale jest raczej ograniczeniem.

### <a name="unplanned-downtime"></a>Nieplanowany przestój

Istnieje możliwość, że pule usługi Batch mogą mieć zdarzenia przestoju na platformie Azure. Jest to ważne w przypadku planowania i opracowywania scenariusza lub przepływu pracy dla usługi Batch.

W przypadku awarii węzła funkcja Batch automatycznie próbuje odzyskać te węzły obliczeniowe w Twoim imieniu. Może to spowodować ponowne zaplanowanie uruchomionego zadania w węźle, który został odzyskany. Zobacz temat [projektowanie pod kątem ponownych prób](#designing-for-retries-and-re-execution) , aby dowiedzieć się więcej o przerwanych zadaniach.

- **Zależność regionu platformy Azure** Zaleca się, aby nie zależeć od jednego regionu świadczenia usługi Azure, jeśli jest to obciążenie czasochłonne lub produkcyjne. Rzadko występują problemy, które mogą mieć wpływ na cały region. Na przykład jeśli przetwarzanie musi rozpocząć się o określonym czasie, rozważ skalowanie w górę puli w regionie podstawowym *przed upływem czasu rozpoczęcia*. W przypadku niepowodzenia skalowania puli można wrócić do skalowania w górę puli w regionie (lub regionach) kopii zapasowej. Pule na wielu kontach w różnych regionach zapewniają gotową, łatwą do udostępnienia kopię zapasową, jeśli coś się nie udaje z inną pulą. Aby uzyskać więcej informacji, zobacz [projektowanie aplikacji pod kątem wysokiej dostępności](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Stanowiska

Zadanie jest kontenerem zaprojektowanym do przechowywania setek, tysięcy lub nawet milionów zadań.

- **Umieszczanie wielu zadań w zadaniu** Korzystanie z zadania do uruchamiania pojedynczego zadania jest niewydajne. Na przykład wydajniejszym rozwiązaniem jest użycie jednego zadania zawierającego 1000 zadań zamiast tworzenia zadań 100, które zawierają 10 zadań. Uruchamianie zadań 1000, każdy z jednym zadaniem, będzie najmniej wydajnym, najwolniejszym i najtańszym podejściem do wykonania.

    Nie należy projektować rozwiązania usługi Batch, które wymaga tysięcy jednocześnie aktywnych zadań. Nie ma przydziału dla zadań, dlatego wykonywanie w miarę jak największej liczby zadań w miarę możliwości efektywnie korzysta z [przydziałów zadań i harmonogramów zadań](batch-quota-limit.md#resource-quotas).

- **Okres istnienia zadania** Zadanie usługi Batch ma czas nieokreślony, dopóki nie zostanie usunięty z systemu. Stan zadania wyznacza, czy może akceptować więcej zadań związanych z planowaniem, czy nie. Zadanie nie przejdzie automatycznie do stanu ukończenia, chyba że zostanie jawnie zakończone. Może to być automatycznie wyzwalane za pomocą właściwości [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) lub [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Istnieje domyślny [limit przydziału aktywnego zadania i harmonogramu zadań](batch-quota-limit.md#resource-quotas). Zadania i harmonogramy zadań w stanie ukończone nie są wliczane do tego przydziału.

## <a name="tasks"></a>Zadania

Zadania są pojedynczymi jednostkami pracy, które składają się na zadanie. Zadania są przesyłane przez użytkownika i zaplanowane przez zadanie wsadowe na węzłach obliczeniowych. Istnieje kilka zagadnień projektowych, które należy wykonać podczas tworzenia i wykonywania zadań. W poniższych sekcjach objaśniono typowe scenariusze i sposób projektowania zadań do obsługi problemów i wydajnego działania.

- **Zapisz dane zadania w ramach zadania.**
    Węzły obliczeniowe są według ich charakterów tymczasowych. Istnieje wiele funkcji usługi Batch, takich jak autopula i automatyczne skalowanie, które ułatwiają węzły. Gdy węzły opuszczają pulę (z powodu zmiany rozmiaru lub usuwania puli), wszystkie pliki w tych węzłach również zostaną usunięte. Z tego powodu zaleca się, aby przed ukończeniem zadania przeniesiono jego wyjście z węzła, który jest uruchomiony w systemie i do magazynu trwałego, podobnie, jeśli zadanie nie powiedzie się, należy przenieść dzienniki wymagane do zdiagnozowania niepowodzenia do trwałego magazynu. Usługa Batch została zintegrowana z obsługą usługi Azure Storage w celu przekazywania danych za pośrednictwem programu [OutputFiles](batch-task-output-files.md), a także wielu udostępnionych systemów plików lub można wykonać przekazywanie samodzielnie w swoich zadaniach.

### <a name="task-lifetime"></a>Okres istnienia zadania

- **Usuń zadania po ich zakończeniu.**
    Usuń zadania, gdy nie są już potrzebne, lub ustaw ograniczenie zadania [retentionTime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) . Jeśli `retentionTime` jest ustawiona, program Batch automatycznie czyści miejsce na dysku używane przez zadanie po `retentionTime` wygaśnięciu.

    Usuwanie zadań ma dwie rzeczy. Gwarantuje to, że nie masz kompilacji zadań w zadaniu, co umożliwia wykonywanie zapytań i znajdowanie zadania, które interesują Cię trudniejsze (ponieważ konieczne będzie przefiltrowanie zadań zakończonych). Czyści również odpowiednie dane zadania w węźle (dostarczony `retentionTime` nie został jeszcze trafiony). Gwarantuje to, że węzły nie wypełniają danych zadania i zabraknie miejsca na dysku.

### <a name="task-submission"></a>Przesyłanie zadania

- **Przesyłanie dużej liczby zadań w kolekcji.**
    Zadania mogą być przesyłane pojedynczo lub w kolekcjach. Przesyłaj zadania w [kolekcjach](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) nawet do 100 w czasie wykonywania zbiorczego przesyłania zadań, aby zmniejszyć obciążenie i czas przesyłania.

### <a name="task-execution"></a>Wykonywanie zadania

- **Wybieranie Maksymalna liczba zadań na węzeł** Zadanie wsadowe obsługuje zadania związane z subskrypcją w węzłach (wykonywanie większej liczby zadań niż węzeł ma rdzenie). Jest to konieczne, aby upewnić się, że zadania "pasują" do węzłów w puli. Można na przykład mieć obniżoną wydajność, Jeśli podjęto próbę zaplanowania ośmiu zadań, za pomocą których każde zużywa 25% użycia procesora CPU w jednym węźle ( `maxTasksPerNode = 8`w puli z).

### <a name="designing-for-retries-and-re-execution"></a>Projektowanie na potrzeby ponownych prób i ponowne wykonywanie

Zadania mogą być automatycznie ponawiane przez partię. Istnieją dwa typy ponownych prób: sterowane przez użytkownika i wewnętrzne. Ponowne próby sterowane przez użytkownika są określane przez [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)zadania. Gdy program określony w zadaniu zostanie zakończony z kodem zakończenia innym niż zero, zadanie zostanie ponowione w celu uzyskania wartości `maxTaskRetryCount`.

Chociaż rzadko, zadanie może być ponawiane wewnętrznie ze względu na błędy w węźle obliczeniowym, na przykład nie można zaktualizować stanu wewnętrznego lub awarii w węźle, gdy zadanie jest uruchomione. Zadanie zostanie ponowione w tym samym węźle obliczeniowym, jeśli jest to możliwe, do wewnętrznego limitu przed zapisaniem zadania i oddanie zadania do ponownego zaplanowania przez partię, potencjalnie w innym węźle obliczeniowym.

- **Tworzenie trwałych zadań** Zadania powinny zostać zaprojektowane w celu wytrzymania awarii i ponowienia próby. Jest to szczególnie ważne w przypadku długotrwałych zadań. W tym celu należy się upewnić, że zadania generują ten sam, pojedynczy wynik nawet wtedy, gdy są uruchamiane więcej niż raz. Jednym ze sposobów osiągnięcia tego celu jest zadanie zadań "szukania celów". Innym sposobem jest upewnienie się, że zadania są idempotentne (zadania będą miały ten sam wynik niezależnie od tego, ile razy są uruchamiane).

    Typowym przykładem jest zadanie kopiowania plików do węzła obliczeniowego. Proste podejście to zadanie, które kopiuje wszystkie określone pliki przy każdym uruchomieniu, co jest niewydajne i nie jest skompilowane w celu wytrzymania awarii. Zamiast tego należy utworzyć zadanie, aby upewnić się, że pliki znajdują się w węźle obliczeniowym. zadanie, które nie kopiuje już wcześniej plików. W ten sposób zadanie zostanie odebrane w tym miejscu, w którym zostało przerwane.

- **Węzły o niskim priorytecie** Podczas wykonywania zadań w węzłach dedykowanych lub o niskim priorytecie nie ma żadnych różnic projektowych. Niezależnie od tego, czy zadanie jest zastępują uruchomione w węźle niskiego priorytetu, czy przerywane z powodu błędu w dedykowanym węźle, obie sytuacje są korygowane przez zaprojektowanie zadania w celu wytrzymania błędu.

- **Czas wykonywania zadania** Unikaj zadań o krótkim czasie wykonywania. Zadania, które są uruchamiane tylko przez jeden do dwóch sekund, nie są idealnym rozwiązaniem. Należy podjąć próbę wykonania znacznej nakładu pracy w pojedynczym zadaniu (10 sekund minimum, przechodząc do kilku godzin lub dni). Jeśli każde zadanie jest wykonywane przez jedną minutę (lub więcej), obciążenie planowania jako ułamek całkowitego czasu obliczeniowego jest małe.

## <a name="nodes"></a>Węzły

- **Zadania uruchamiania powinny być idempotentne** Podobnie jak w przypadku innych zadań, zadanie uruchomieniowe węzła powinno być idempotentne, ponieważ zostanie uruchomione ponownie za każdym razem, gdy węzeł zostanie uruchomiony. Zadanie idempotentne jest po prostu jednym, które generuje spójny wynik w przypadku wielokrotnego uruchomienia.

- **Zarządzanie długotrwałymi usługami za pośrednictwem interfejsu usług systemu operacyjnego.**
    Czasami istnieje potrzeba uruchomienia innego agenta obok agenta usługi Batch w węźle, np. w celu zebrania danych z węzła i zgłoszenia go. Zalecamy wdrożenie tych agentów jako usług systemu operacyjnego, takich jak usługa systemu Windows lub usługa Linux `systemd` .

    W przypadku uruchamiania tych usług nie wolno zablokować plików na żadnych plikach w katalogach zarządzanych przez usługę Batch w węźle, ponieważ w przeciwnym razie usługa Batch nie będzie mogła usunąć tych katalogów ze względu na blokady plików. Jeśli na przykład zainstalujesz usługę systemu Windows w zadaniu uruchamiania, zamiast uruchamiania usługi bezpośrednio z katalogu roboczego zadania uruchamiania, skopiuj pliki w inne miejsce (jeśli pliki już istnieją, Pomiń kopię). Zainstaluj usługę z tej lokalizacji. Gdy wsadowe ponownie uruchamia zadanie podrzędne, usunie katalog roboczy zadania uruchamiania i ponownie go utworzy. Dzieje się tak, ponieważ usługa ma blokady plików w innym katalogu, a nie w katalogu roboczym zadania uruchamiania.

- **Unikaj tworzenia połączeń katalogów w systemie Windows** Rozgałęzienia katalogów, czasami nazywane twardymi łączami katalogów, są trudne do rozproszenia podczas zadań i czyszczenia zadań. Użyj linków symbolicznych (linki nietrwałe) zamiast twardych linków.

- **Zbierz dzienniki agenta wsadowego, jeśli wystąpił problem** Jeśli zauważysz problem związany z zachowaniem węzła lub zadań uruchomionych w węźle, zaleca się zebranie dzienników agenta zadań wsadowych przed cofnięciem przydziału danego węzła. Dzienniki agenta usługi Batch można zbierać przy użyciu interfejsu API przekazywania dzienników usług Batch. Te dzienniki mogą być dostarczane do firmy Microsoft jako część biletu pomocy technicznej i mogą pomóc w rozwiązywaniu problemów z rozwiązaniem.

## <a name="security"></a>Zabezpieczenia

### <a name="security-isolation"></a>Izolacja zabezpieczeń

W celu przeprowadzenia izolacji, jeśli scenariusz wymaga izolowania zadań od siebie, należy izolować te zadania, umieszczając je w osobnych pulach. Pula to granica izolacji zabezpieczeń w usłudze Batch, a domyślnie dwie pule nie są widoczne ani nie mogą komunikować się ze sobą. Należy unikać używania odrębnych kont usługi Batch jako metody izolacji.

## <a name="moving"></a>Noszeniu

### <a name="move-batch-account-across-regions"></a>Przenoszenie konta wsadowego w różnych regionach

Istnieją różne scenariusze, w których należy przenieść istniejące konto usługi Batch z jednego regionu do innego. Na przykład możesz chcieć przejść do innego regionu w ramach planowania odzyskiwania po awarii.

Kont Azure Batch nie można przenosić z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager, aby wyeksportować istniejącą konfigurację konta w usłudze Batch.  Następnie można przemieścić zasób w innym regionie, eksportując konto wsadowe do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie. Po przekazaniu szablonu do nowego regionu konieczne będzie ponowne utworzenie certyfikatów, harmonogramów zadań i pakietów aplikacji. Aby zatwierdzić zmiany i zakończyć Przenoszenie konta w usłudze Batch, pamiętaj o usunięciu oryginalnego konta partii lub grupy zasobów.

Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="connectivity-to-the-batch-service"></a>Łączność z usługą Batch

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i trasy zdefiniowane przez użytkownika (UDR)

Podczas aprowizacji `BatchNodeManagement` pul usługi [Batch w sieci wirtualnej](batch-virtual-network.md)należy się upewnić, że są one zgodne z wytycznymi dotyczącymi korzystania z tagu usług, portów, protokołów i kierunku reguły.
Użycie znacznika usługi jest zdecydowanie zalecane, a nie adresów IP podstawowych usług Batch, ponieważ mogą one ulec zmianie w czasie. Korzystanie z adresów IP usługi Batch bezpośrednio może zaistnieć jako niestabilność, przerwy lub przestoje dla pul usługi Batch, ponieważ usługa Batch aktualizuje adresy IP używane w czasie. Jeśli obecnie używasz adresów IP usługi Batch w regułach sieciowej grupy zabezpieczeń, zaleca się przełączenie na korzystanie z tagu usługi.

W przypadku tras zdefiniowanych przez użytkownika upewnij się, że masz proces w miejscu, aby okresowo aktualizować adresy IP usługi Batch w tabeli tras w miarę zmiany w czasie. Aby dowiedzieć się, jak uzyskać listę adresów IP usługi Batch, zobacz [lokalne znaczniki usług](../virtual-network/service-tags-overview.md). Adresy IP usługi Batch zostaną skojarzone z tagiem `BatchNodeManagement` usługi (lub odmianą regionalną zgodną z regionem konta usługi Batch).

### <a name="honoring-dns"></a>Przestrzeganie systemu DNS

Upewnij się, że Twoje systemy są uznawane za czas wygaśnięcia (TTL) DNS dla adresu URL usługi Batch account. Ponadto należy się upewnić, że klienci usługi Batch i inne mechanizmy łączności z usługą Batch nie korzystają z adresów IP.

Jeśli żądania odbierają odpowiedzi HTTP na poziomie 5xx i istnieje nagłówek "Connection: Close" w odpowiedzi, klient usługi Batch powinien przestrzegać zalecenia, zamykając istniejące połączenie, ponownie rozwiązując system DNS dla adresu URL usługi Batch Account i próbuje wykonać następujące żądania dotyczące nowego połączenia.

### <a name="retrying-requests-automatically"></a>Ponawianie prób żądania automatycznie

Upewnij się, że klienci usługi Batch mają odpowiednie zasady ponawiania prób w celu automatycznego ponowienia żądań, nawet w trakcie normalnej operacji, a nie wyłącznie w okresach czasu konserwacji usługi. Te zasady ponawiania powinny obejmować interwał wynoszący co najmniej 5 minut. Funkcje automatycznego ponawiania prób są dostarczane z różnymi zestawami SDK partii, takimi jak [Klasa .NET RetryPolicyProvider](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet).

