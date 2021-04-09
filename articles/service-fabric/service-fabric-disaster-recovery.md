---
title: Odzyskiwanie po awarii Service Fabric platformy Azure
description: Usługa Azure Service Fabric oferuje funkcje do rozwiązywania awarii. W tym artykule opisano typy awarii, które mogą wystąpić, oraz sposób postępowania z nimi.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791159"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Odzyskiwanie po awarii na platformie Azure Service Fabric
Krytyczna część dostarczania wysokiej dostępności zapewnia, że usługi mogą przetrwać wszystkie różne typy awarii. Jest to szczególnie ważne w przypadku nieplanowanych awarii i poza formantem. 

W tym artykule opisano niektóre typowe tryby awarii, które mogą być awarie, jeśli nie są poprawnie modelowane i zarządzane. Omówiono w nim również środki zaradcze i działania podejmowane w przypadku wystąpienia awarii. Celem jest ograniczenie lub wyeliminowanie ryzyka przestoju lub utraty danych w przypadku wystąpienia awarii, planowanego lub w inny sposób.

## <a name="avoiding-disaster"></a>Uniknięcie awarii
Głównym celem usługi Azure Service Fabric jest ułatwienie modelowania środowiska i usług w taki sposób, że typowe typy awarii nie są awariami. 

Ogólnie rzecz biorąc, istnieją dwa typy scenariuszy awarii/awarii:
- Błędy sprzętu i oprogramowania
- Błędy operacyjne

### <a name="hardware-and-software-faults"></a>Błędy sprzętu i oprogramowania
Błędy sprzętu i oprogramowania są nieprzewidywalne. Najprostszym sposobem przeżycia błędów jest uruchomienie większej liczby kopii usługi na granicach sprzętu lub oprogramowania. 

Na przykład, jeśli usługa jest uruchomiona tylko na jednym komputerze, awaria tej usługi jest niezależna od tego, czy jest ona niezależna. Prostym sposobem na uniknięcie tego awarii jest upewnienie się, że usługa jest uruchomiona na wielu komputerach. Testowanie jest również konieczne, aby upewnić się, że awaria jednego komputera nie zakłóca działania uruchomionej usługi. Planowanie pojemności zapewnia, że wystąpienie zastępcze może zostać utworzone w innym miejscu i zmniejszenie wydajności nie powoduje przeciążenia pozostałych usług. 

Ten sam wzorzec działa niezależnie od tego, co próbujesz uniknąć awarii. Na przykład Jeśli obawiasz się awarii sieci SAN, możesz uruchomić wiele sieci SAN. Jeśli chodzi o utratę stojaka serwerów, możesz uruchamiać wiele stojaków. W przypadku martwisz się o utracie centrów danych usługa powinna działać w wielu regionach platformy Azure, w wielu Strefy dostępności platformy Azureach lub we własnych centrach danych. 

Gdy usługa jest objęta wieloma wystąpieniami fizycznymi (maszynami, stojakami, centrami danych, regionami), nadal podlegają pewne typy jednoczesnych awarii. Jednak pojedyncza i nawet wiele błędów określonego typu (na przykład awaria pojedynczej maszyny wirtualnej lub łącza sieciowego) są automatycznie obsługiwane i nie są już "katastrofami". 

Service Fabric udostępnia mechanizmy rozszerzania klastra i obsługują przywracanie uszkodzonych węzłów i usług. Service Fabric również umożliwia uruchamianie wielu wystąpień usług, aby zapobiec występowaniu nieplanowanych awarii.

Może się zdarzyć, że wdrożenie wystarczająco duże, aby przetworzyć awarie, nie jest możliwe. Na przykład może to potrwać więcej zasobów sprzętowych, niż jest to konieczne, w odniesieniu do szansy niepowodzeń. W przypadku korzystania z aplikacji rozproszonych dodatkowe przeskoki komunikacyjne lub koszty replikacji stanu na odległość geograficzną mogą spowodować nieakceptowalne opóźnienia. Miejsce, w którym ten wiersz jest rysowany, różni się w zależności od aplikacji. 

W przypadku błędów oprogramowania w odniesieniu do błędu może być używana usługa, którą próbujesz skalować. W takim przypadku więcej kopii nie zapobiega awarii, ponieważ warunek błędu jest skorelowany we wszystkich wystąpieniach.

### <a name="operational-faults"></a>Błędy operacyjne
Nawet jeśli Twoja usługa jest położona na całym świecie z wieloma nadmiarowośćmi, nadal mogą wystąpić zdarzenia katastrofalne. Na przykład ktoś może przypadkowo zmienić nazwę DNS usługi lub usunąć ją z prawej strony. 

Załóżmy na przykład, że masz usługę stanową Service Fabric, a ktoś przypadkowo usunął tę usługę. O ile nie istnieje jakieś inne środki zaradcze, ta usługa i wszystkie Stany, które zostały już usunięte. Te typy katastrof operacyjnych ("Niestety") wymagają różnych środków zaradczych i czynności związanych z odzyskiwaniem niż regularne nieplanowane błędy. 

Najlepszym sposobem na uniknięcie tego typu błędów operacyjnych jest:
- Ogranicz dostęp operacyjny do środowiska.
- Ścisła Inspekcja niebezpiecznych operacji.
- Narzucaj automatyzację, Zapobiegaj ręcznym lub poza pasmem, a także Weryfikuj konkretne zmiany w środowisku przed ich przekazaniem.
- Upewnij się, że operacje niszczące są "miękkie". Operacje nietrwałe nie zaczynają obowiązywać natychmiast lub mogą być cofnięte w przedziale czasu.

Service Fabric udostępnia mechanizmy uniemożliwiające awarie operacyjne, takie jak zapewnienie kontroli dostępu [opartej na rolach](service-fabric-cluster-security-roles.md) dla operacji klastra. Większość z tych usterek operacyjnych wymaga jednak wysiłków organizacyjnych i innych systemów. Service Fabric udostępnia mechanizmy do pogłębienia błędów operacyjnych, [a mianowicie tworzenie kopii zapasowych i przywracanie dla usług stanowych](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Zarządzanie błędami
Celem Service Fabric jest automatyczne zarządzanie niepowodzeńmi. Jednak w celu obsługi niektórych typów błędów usługi muszą mieć dodatkowy kod. W przypadku innych typów awarii _nie_ należy automatycznie rozwiązywać problemów z bezpieczeństwem i ciągłością biznesową. 

### <a name="handling-single-failures"></a>Obsługa pojedynczych błędów
Pojedyncze maszyny mogą kończyć się niepowodzeniem w przypadku wszystkich różnych przyczyn. Czasami jest to przyczyną problemów sprzętowych, takich jak zasilacze i awarie sprzętu sieciowego. Inne błędy są w oprogramowaniu. Obejmują one błędy systemu operacyjnego i samej usługi. Service Fabric automatycznie wykrywa te typy błędów, w tym przypadki, w których maszyna zostanie odizolowana od innych maszyn z powodu problemów z siecią.

Niezależnie od typu usługi uruchomienie pojedynczego wystąpienia powoduje przestoje dla tej usługi, jeśli jedna kopia kodu nie powiedzie się z jakiegokolwiek powodu. 

W celu obsługi dowolnego pojedynczego błędu najprostszą czynnością jest upewnienie się, że usługi działają domyślnie w więcej niż jednym węźle. W przypadku usług bezstanowych upewnij się, że `InstanceCount` jest ona większa niż 1. W przypadku usług stanowych minimalnym zaleceniem są te, które `TargetReplicaSetSize` `MinReplicaSetSize` są ustawione na 3. Uruchomienie większej liczby kopii kodu usługi zapewnia, że usługa może obsłużyć pojedynczy błąd automatycznie. 

### <a name="handling-coordinated-failures"></a>Obsługa niepowodzeń koordynowanych
Skoordynowane awarie w klastrze mogą być spowodowane planowanymi lub nieplanowanymi awariami infrastruktury oraz zmianami lub planowanymi zmianami oprogramowania. Service Fabric modeleje strefy infrastruktury, które napotykają skoordynowane błędy jako *domeny błędów*. Obszary, które będą powodować skoordynowane zmiany oprogramowania są modelowane jako *domeny uaktualnienia*. Aby uzyskać więcej informacji na temat domen błędów, domen uaktualnienia i topologii klastra, zobacz [Opis klastra Service Fabric przy użyciu Menedżer zasobów klastrów](service-fabric-cluster-resource-manager-cluster-description.md).

Domyślnie Service Fabric traktuje domeny błędów i uaktualniania podczas planowania, gdzie usługi powinny działać. Domyślnie program Service Fabric podejmuje próbę upewnienia się, że usługi działają w kilku domenach błędów i uaktualnień, aby w przypadku wystąpienia planowanych lub nieplanowanych zmian Twoje usługi pozostaną dostępne. 

Załóżmy na przykład, że awaria źródła zasilacza powoduje, że wszystkie maszyny w stojaku kończą się niepowodzeniem jednocześnie. W przypadku wielu kopii usługi, utrata wielu maszyn w przypadku awarii domeny błędów przestanie działać na inny przykład pojedynczego błędu dla usługi. Dlatego Zarządzanie domenami błędów i uaktualniania ma kluczowe znaczenie dla zapewnienia wysokiej dostępności usług. 

Gdy uruchamiasz Service Fabric na platformie Azure, domeny błędów i domeny uaktualnień są zarządzane automatycznie. W innych środowiskach mogą one nie być. Jeśli tworzysz własne klastry lokalnie, pamiętaj o poprawnym zamapowaniu i zaplanowaniu układu domeny błędów.

Domeny uaktualnień są przydatne w przypadku obszarów modelowania, w których oprogramowanie zostanie uaktualnione w tym samym czasie. Z tego powodu domeny uaktualnienia często definiują granice, w których oprogramowanie jest wyłączane podczas planowanych uaktualnień. Uaktualnienia obu Service Fabric i usług są zgodne z tym samym modelem. Aby uzyskać więcej informacji na temat uaktualnień stopniowych, domen uaktualnienia i modelu kondycji Service Fabric, który pomaga zapobiegać niezamierzonym zmianom wpływającym na klaster i usługę, zobacz:

 - [Uaktualnienie aplikacji](service-fabric-application-upgrade.md)
 - [Samouczek dotyczący uaktualniania aplikacji](service-fabric-application-upgrade-tutorial.md)
 - [Model kondycji Service Fabric](service-fabric-health-introduction.md)

Możesz wizualizować układ klastra przy użyciu mapy klastra podanej w [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Węzły rozłożone między domenami błędów w Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelowanie obszarów awarii, uaktualnień stopniowych, uruchamianie wielu wystąpień kodu i stanu usługi, reguły umieszczania w celu upewnienia się, że usługi działają w domenach błędów i uaktualnień, a wbudowane monitorowanie kondycji to tylko *niektóre* funkcje, które Service Fabric zapewnia, aby zachować normalne problemy z działaniem i awarie przed włączeniem awarii. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Obsługa jednoczesnych awarii sprzętu lub oprogramowania
Otrzymaliśmy informacje o pojedynczych błędach. Jak widać, są one łatwe do obsłużenia zarówno w przypadku usług bezstanowych, jak i bezstanowych, co pozwala zachować większą liczbę kopii kodu (i stanu) uruchomionych w domenach błędów i uaktualnień. 

Może również wystąpić wiele jednoczesnych błędów losowych. Są one bardziej potencjalne, aby prowadzić przestoje lub rzeczywista awaria.


#### <a name="stateless-services"></a>Usługi bezstanowe
Liczba wystąpień usługi bezstanowej wskazuje żądaną liczbę wystąpień, które muszą być uruchomione. Gdy dowolne (lub wszystkie) wystąpienia zakończą się niepowodzeniem, Service Fabric reaguje, automatycznie tworząc wystąpienia zastępcze w innych węzłach. Service Fabric kontynuuje tworzenie zamian, dopóki usługa nie zostanie przywrócona do żądanej liczby wystąpień.

Załóżmy na przykład, że usługa bezstanowa ma `InstanceCount` wartość-1. Ta wartość oznacza, że jedno wystąpienie powinno być uruchomione na każdym węźle w klastrze. Jeśli niektóre z tych wystąpień zakończą się niepowodzeniem, Service Fabric wykryje, że usługa nie jest w żądanym stanie i spróbuje utworzyć wystąpienia w węzłach, w których się znajdują.

#### <a name="stateful-services"></a>Usługi stanowe
Istnieją dwa typy usług stanowych:
- Stanowy z trwałym stanem.
- Stanowy z nieutrwalonym stanem. (Stan jest przechowywany w pamięci).

Odzyskiwanie po awarii usługi stanowej zależy od typu usługi stanowej, liczby replik usługi i liczby replik zakończonych niepowodzeniem.

W usłudze stanowej dane przychodzące są replikowane między replikami (podstawowymi i wszystkimi aktywnymi elementami pomocniczymi). Jeśli większość replik odbierze dane, dane są uznawane za zatwierdzone *kworum* . (Dla pięciu replik, trzy będzie kworum). Oznacza to, że w dowolnym momencie będzie co najmniej kworum replik z najnowszymi danymi. Jeśli repliki zakończą się niepowodzeniem (powiedzmy dwa z pięciu), możemy użyć wartości kworum do obliczenia, czy można odzyskać. (Ponieważ pozostałe trzy repliki są nadal dostępne, gwarantujemy, że co najmniej jedna replika będzie mieć kompletne dane).

Gdy kworum replik nie powiedzie się, partycja jest zadeklarowana jako w stanie *utraty kworum* . Załóżmy, że partycja ma pięć replik, co oznacza, że co najmniej trzy są gwarantowane pełnych danych. Jeśli kworum (trzy z pięciu) replik kończy się niepowodzeniem, Service Fabric nie może określić, czy pozostałe repliki (dwa z pięciu) mają wystarczającą ilość danych do przywrócenia partycji. W przypadkach, w których Service Fabric wykrywa utratę kworum, jego zachowanie domyślne polega na uniemożliwieniu dodatkowych zapisów w partycji, zadeklarowaniu utraty kworum i poczekaniu na przywrócenie kworum replik.

Określenie, czy wystąpił błąd usługi stanowej, a następnie zarządzanie nią następuje po trzech etapach:

1. Określanie, czy nastąpiła utrata kworum.
   
   Utrata kworum jest zadeklarowana, gdy większość replik stanowych usług nie działa w tym samym czasie.
2. Ustalanie, czy utrata kworum jest stała, czy nie.
   
   W większości przypadków błędy są przejściowe. Procesy są ponownie uruchamiane, węzły są ponownie uruchamiane, maszyny wirtualne zostaną uruchomione, a partycje sieciowe zostały zaleczone. Czasami niepowodzenia są trwałe. Czy niepowodzenia są trwałe, czy nie są zależne od tego, czy usługa stanowa utrzymuje swój stan, czy też w pamięci: 
   
   - W przypadku usług bez trwałego uszkodzenia kworum lub większej liczby replik _natychmiast_ po utracie trwałych kworum. Gdy Service Fabric wykrywa utratę kworum w nietrwałej usłudze stanowej, natychmiast przechodzi do kroku 3 przez zadeklarowanie (potencjalne) utraty danych. Przechodzenie do utraty danych ma sens, ponieważ Service Fabric wie, że nie ma żadnego punktu oczekiwania na powrót replik. Nawet w przypadku ich odzyskania dane zostaną utracone z powodu nieutrwalonego charakteru usługi.
   - W przypadku stałych usług trwałych awaria kworum lub większej liczby replik powoduje, że Service Fabric czekać, aż repliki powróciją i przywróciją kworum. Powoduje to awarię usługi dla wszelkich _zapisów_ na partycji, której to dotyczy (lub "zestawu replik") usługi. Jednak odczyty mogą być nadal możliwe z obniżonymi gwarancjami spójności. Domyślna ilość czasu, jaką Service Fabric czeka na przywrócenie kworum, jest *nieskończona*, ponieważ jest to (potencjalne) zdarzenie utraty danych i przenosi inne zagrożenia. Oznacza to, że Service Fabric nie przejdzie do następnego kroku, chyba że administrator podejmie akcję zadeklarować utratę danych.
3. Określanie, czy dane są tracone i przywracane z kopii zapasowych.

   Jeśli utrata kworum została zadeklarowana (automatycznie lub za pomocą akcji administracyjnej), Service Fabric i usługi przechodzą w celu określenia, czy dane zostały faktycznie utracone. W tym momencie Service Fabric również wie, że inne repliki nie są wycofywane. To była decyzja podjęta po zatrzymaniu oczekiwania na naprawienie przez chwilę utraty kworum. Najlepszym sposobem działania usługi jest zazwyczaj zawieszanie się i oczekiwanie na konkretną interwencję administracyjną.
   
   Gdy Service Fabric wywołuje `OnDataLossAsync` metodę, zawsze jest z powodu _podejrzanej_ utraty danych. Service Fabric zapewnia, że to wywołanie jest dostarczane do _najlepszej_ pozostałej repliki. Jest to niezależna replika. 
   
   Powód, dla którego zawsze mówimy, że _podejrzewana_ utrata danych polega na tym, że pozostała replika ma ten sam stan, co w przypadku utraty kworum. Jednak bez tego stanu, aby porównać go z, nie istnieje dobry sposób na Service Fabric lub operatorów, do których należy wiedzieć.     
   
   Co robią typową implementację `OnDataLossAsync` metody?
   1. Dzienniki implementacji, które `OnDataLossAsync` zostały wyzwolone i uruchomiły wszelkie niezbędne Alerty administracyjne.
   1. Zazwyczaj implementacja wstrzymuje się i czeka na dalsze decyzje i akcje wykonywane ręcznie. Jest to spowodowane tym, że nawet jeśli kopie zapasowe są dostępne, może być konieczne ich przygotowanie. 
   
      Na przykład jeśli dwie różne usługi koordynują informacje, te kopie zapasowe mogą być konieczne do zmodyfikowania, aby upewnić się, że po zakończeniu przywracania informacje o tym, że te dwie usługi są zgodne. 
   1. Często istnieją inne dane telemetryczne lub dane wydechowe z usługi. Te metadane mogą być zawarte w innych usługach lub w dziennikach. Te informacje mogą być używane w razie potrzeby w celu ustalenia, czy zostały odebrane jakiekolwiek wywołania i przetworzone na podstawowym poziomie, które nie były obecne w kopii zapasowej lub zreplikowane do tej repliki. Te wywołania mogą wymagać odtworzenia lub dodania do kopii zapasowej, zanim będzie możliwe przywrócenie.  
   1. Implementacja porównuje stan pozostałej repliki z zawartą w dowolnych dostępnych kopiach zapasowych. W przypadku korzystania z Service Fabric niezawodnych kolekcji dostępne są [Narzędzia i procesy](service-fabric-reliable-services-backup-restore.md) umożliwiające wykonanie tej operacji. Celem jest sprawdzenie, czy stan w replice jest wystarczający, i zobacz, w jaki sposób kopia zapasowa może być brakująca.
   1. Po zakończeniu porównania i po zakończeniu przywracania (w razie potrzeby) kod usługi powinien zwrócić **wartość true** , jeśli wprowadzono jakiekolwiek zmiany stanu. Jeśli replika ustaliła, że była to Najlepsza dostępna kopia stanu i nie wprowadziła żadnych zmian, kod zwróci **wartość false**. 
   
      Wartość **true** wskazuje, że wszystkie _inne_ repliki pozostałe mogą teraz być niespójne z tym elementem. Zostaną one porzucone i ponownie skompilowane z tej repliki. Wartość **false** wskazuje, że nie zostały wprowadzone żadne zmiany stanu, więc inne repliki mogą zachować te informacje. 

Niezwykle ważne jest, aby autorzy usług mogli wdrożyć potencjalne działania związane z utratą danych i niepowodzeniami, zanim usługi zostaną wdrożone w środowisku produkcyjnym. Aby chronić przed utratą danych, ważne jest, aby okresowo [wykonywać kopie zapasowe](service-fabric-reliable-services-backup-restore.md) wszystkich usług stanowych do magazynu geograficznie nadmiarowego. 

Należy również upewnić się, że masz możliwość przywrócenia stanu. Ponieważ kopie zapasowe wielu różnych usług są wykonywane w różnym czasie, należy upewnić się, że po przywróceniu usługi będą mieć spójny widok. 

Rozważmy na przykład sytuację, w której jedna usługa generuje liczbę i zapisuje ją, a następnie wysyła ją do innej usługi, która również ją przechowuje. Po przywróceniu można stwierdzić, że druga usługa ma numer, ale nie jest to pierwsza, ponieważ jej kopia zapasowa nie obejmuje tej operacji.

Jeśli okaże się, że pozostałe repliki są niewystarczające do kontynuowania w scenariuszu utraty danych i nie można odtworzyć stanu usługi z telemetrii lub wydechu, częstotliwość tworzenia kopii zapasowych określa najlepszy możliwy cel punktu odzyskiwania (RPO). Service Fabric udostępnia wiele narzędzi do testowania różnych scenariuszy awarii, w tym trwałego kworum i utraty danych, które wymagają przywrócenia z kopii zapasowej. Te scenariusze są dołączone jako część narzędzi do testowania w Service Fabric zarządzane przez usługę błędów. Aby uzyskać więcej informacji na temat tych narzędzi i wzorców, zobacz [wprowadzenie do usługi błędów analizy](service-fabric-testability-overview.md). 

> [!NOTE]
> Usługi systemowe mogą również mieć negatywny wpływ na kworum. Wpływ jest specyficzny dla danej usługi. Z tego względu utrata kworum w usłudze nazw ma wpływ na rozpoznawanie nazw, podczas gdy usługa Menedżer trybu failover powoduje zablokowanie nowego tworzenia usługi i przełączenia w tryb failover. 
> 
> Usługi systemowe Service Fabric są zgodne z tym samym wzorcem, co usługi do zarządzania stanem, ale nie zalecamy przechodzenia do nich utraty kworum i utraty danych. Zamiast tego zalecamy zapoznaj się z  [pomocą techniczną](service-fabric-support.md) , aby znaleźć rozwiązanie, które jest przeznaczone do Twojej sytuacji. Zwykle zaleca się po prostu poczekanie, aż repliki w dół zostaną zwrócone.
>

#### <a name="troubleshooting-quorum-loss"></a>Rozwiązywanie problemów z utratą kworum

Repliki mogą występować sporadycznie z powodu przejściowego błędu. Poczekaj chwilę, aż Service Fabric podejmie próbę ich przełączenia. Jeśli repliki nie były dłużej niż oczekiwany czas trwania, wykonaj następujące czynności w zakresie rozwiązywania problemów:
- Repliki mogą ulec awarii. Sprawdź raporty kondycji na poziomie repliki i dzienniki aplikacji. Zbieraj Zrzuty awaryjne i podejmuj działania niezbędne do odzyskania.
- Proces repliki mógł przestać odpowiadać. Sprawdź dzienniki aplikacji, aby to sprawdzić. Zbierz zrzuty procesów, a następnie Zatrzymaj proces nieodpowiadający. Service Fabric utworzy proces zastępczy i spróbuje przywrócić replikę.
- Węzły obsługujące repliki mogą być wyłączone. Uruchom ponownie podstawową maszynę wirtualną, aby przenieść węzły w górę.

Czasami odzyskanie replik może być niemożliwe. Na przykład dyski zakończyły się niepowodzeniem lub komputery fizycznie nie odpowiadają. W takich przypadkach Service Fabric musi zostać pożądany, aby nie czekać na odzyskanie repliki.

Nie należy używać tych metod, jeśli *nie* można zaakceptować potencjalnej utraty danych w celu przełączenia usługi do trybu online. W takim przypadku należy wykonać wszystkie działania w kierunku odzyskiwania maszyn fizycznych.

Następujące akcje mogą spowodować utratę danych. Sprawdź przed wykonaniem tych czynności.
   
> [!NOTE]
> _Nie_ można bezpiecznie używać tych metod innych niż w celu dla konkretnych partycji. 
>

- Użyj `Repair-ServiceFabricPartition -PartitionId` `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` interfejsu API lub. Ten interfejs API umożliwia określenie identyfikatora partycji, która ma zostać przeniesiona z utraty kworum, oraz do utraty danych.
- Jeśli klaster napotyka częste błędy, które powodują, że usługi przechodzą w stan utraty kworum, a potencjalną _utratą danych jest akceptowalna_, określenie odpowiedniej wartości [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) może ułatwić automatyczne odzyskanie usługi. Service Fabric będzie oczekiwać na podaną `QuorumLossWaitDuration` wartość (domyślnie nieskończoną) przed wykonaniem odzyskiwania. Ta metoda *nie* jest zalecana, ponieważ może to spowodować nieoczekiwane straty danych.

## <a name="availability-of-the-service-fabric-cluster"></a>Dostępność klastra Service Fabric
Ogólnie rzecz biorąc, klaster Service Fabric jest wysoce rozproszonym środowiskiem bez pojedynczych punktów awarii. Awaria jednego z węzłów nie powoduje problemów z dostępnością lub niezawodnością klastra, głównie ponieważ usługi systemu Service Fabric są zgodne z tymi samymi wskazówkami opisanymi wcześniej. Oznacza to, że zawsze są domyślnie uruchamiane z co najmniej trzema replikami, a usługi systemowe, które są bezstanowe uruchamiane na wszystkich węzłach. 

Podstawowe warstwy Service Fabric sieci i wykrywania błędów są w pełni rozproszone. Większość usług systemowych można odbudować z metadanych w klastrze lub wiedzieć, jak ponownie synchronizować ich stan z innych miejsc. Dostępność klastra może zostać naruszona, jeśli usługi systemowe zaczną korzystać z innych sytuacji związanych z utratą kworum, takich jak opisane wcześniej. W takich przypadkach może nie być możliwe wykonanie pewnych operacji w klastrze (na przykład od momentu uaktualnienia lub wdrożenia nowych usług), ale sam klaster jest w dalszym ciągu. 

Usługi uruchomione w uruchomionym klastrze będą działać w tych warunkach, chyba że wymagają zapisu do usług systemowych, aby kontynuować działanie. Na przykład jeśli Menedżer trybu failover jest tracona kworum, wszystkie usługi będą nadal działać. Jednak wszelkie usługi, które nie powiodą się, nie będą mogły automatycznie ponownie uruchamiać, ponieważ wymaga to zaangażowania Menedżer trybu failover. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Awarie centrum danych lub regionu świadczenia usługi Azure
W rzadkich przypadkach fizyczne centrum danych może stać się tymczasowo niedostępne po utracie mocy lub łączności sieciowej. W takich przypadkach klastry Service Fabric i usługi w tym centrum danych lub regionie platformy Azure będą niedostępne. _Dane są jednak zachowywane_. 

W przypadku klastrów działających na platformie Azure można wyświetlić aktualizacje na [stronie stanu platformy Azure][azure-status-dashboard]. W bardzo mało prawdopodobnym przypadku, gdy fizyczne centrum danych jest częściowo lub w pełni niszczone, wszystkie hostowane w niej klastry Service Fabric lub usługi w nich mogły zostać utracone. Ta utrata obejmuje wszystkie Stany, których kopie zapasowe nie są tworzone poza tym centrum danych lub regionem.

Istnieją dwie różne strategie, które mogą obsłużyć trwałą lub nieprzerwaną awarię jednego centrum danych lub regionu: 

- Uruchamianie oddzielnych klastrów Service Fabric w wielu takich regionach i używanie pewnego mechanizmu przełączania do trybu failover i powrotu po awarii między tymi środowiskami. To sortowanie wielu klastrów aktywny/aktywny lub aktywny/pasywny wymaga dodatkowego kodu zarządzania i operacji. Ten model wymaga również koordynacji kopii zapasowych z usług w jednym centrum danych lub regionie, dzięki czemu są one dostępne w innych centrach danych lub regionach, gdy jeden z nich ulegnie awarii. 
- Uruchom pojedynczy klaster Service Fabric, który obejmuje wiele centrów danych lub regionów. Minimalna obsługiwana konfiguracja tej strategii to trzy centra danych lub regiony. Zalecana liczba regionów lub centrów danych wynosi pięć. 
  
  Ten model wymaga bardziej złożonej topologii klastra. Korzyść jest jednak taka, że awaria jednego centrum danych lub regionu jest konwertowana z awarii na normalną awarię. Te błędy mogą być obsługiwane przez mechanizmy, które działają w przypadku klastrów w obrębie jednego regionu. Domeny błędów, domeny uaktualnień i Service Fabric reguły umieszczania zapewniają, że obciążenia są dystrybuowane w taki sposób, aby tolerowali normalne awarie. 
  
  Aby uzyskać więcej informacji na temat zasad, które mogą pomóc w obsłudze usług w tym typie klastra, zobacz [zasady umieszczania dla Service Fabric usług](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Błędy losowe, które prowadzą do awarii klastra
Service Fabric ma koncepcję *węzłów inicjatora*. Są to węzły, które utrzymują dostępność bazowego klastra. 

Węzły inicjatora pomagają zapewnić, że klaster pozostanie przez ustanowienie dzierżaw z innymi węzłami i służy jako tiebreakers w przypadku niektórych rodzajów błędów. Jeśli błędy losowe usuwają większość węzłów inicjatora w klastrze i nie są przywracane szybko, klaster zostanie automatycznie zamknięty. Klaster kończy się niepowodzeniem. 

Na platformie Azure dostawca zasobów Service Fabric zarządza Service Fabric konfiguracjami klastra. Domyślnie dostawca zasobów dystrybuuje węzły inicjatora w domenach błędów i uaktualnień dla *typu węzła podstawowego*. Jeśli typ węzła podstawowego jest oznaczony jako trwałość Srebrna lub złota, po usunięciu węzła inicjatora (przez przeskalowanie w podstawowym typie węzła lub przez ręczne usunięcie go) klaster podejmie próbę podwyższenia poziomu innego węzła, który nie jest inicjatorem z dostępnej pojemności typu węzła podstawowego. Ta próba zakończy się niepowodzeniem, jeśli masz mniejszą ilość dostępnej pojemności niż wymaga poziomu niezawodności klastra dla typu węzła podstawowego.

W przypadku autonomicznych klastrów Service Fabric i platformy Azure typ węzła podstawowego to ten, na którym są uruchamiane nasiona. Podczas definiowania typu węzła podstawowego Service Fabric automatycznie będzie korzystać z liczby udostępnionych węzłów przez utworzenie do dziewięciu węzłów inicjatora i siedmiu replik każdej usługi systemowej. Jeśli zestaw losowych błędów pobiera większość tych replik jednocześnie, usługi systemowe spowodują utratę kworum. Jeśli większość węzłów inicjatora zostanie utracona, klaster zostanie zamknięty wkrótce.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak symulować różne błędy przy użyciu [środowiska testowania](service-fabric-testability-overview.md).
- Zapoznaj się z innymi zasobami odzyskiwania po awarii i wysokiej dostępności. Firma Microsoft opublikowała na tych tematach dużą liczbę wskazówek. Mimo że niektóre z tych zasobów odnoszą się do określonych technik korzystania z innych produktów, zawierają one wiele ogólnych najlepszych rozwiązań, które można zastosować w kontekście Service Fabric:
  - [Lista kontrolna dotycząca dostępności](/azure/architecture/checklist/resiliency-per-service)
  - [Przechodzenie do szczegółów odzyskiwania po awarii](../azure-sql/database/disaster-recovery-drills.md)
  - [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność][dr-ha-guide]
- Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
