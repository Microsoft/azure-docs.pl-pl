---
title: Monitor wydajności w Network Performance Monitor
description: Funkcja monitora wydajności w Network Performance Monitor ułatwia monitorowanie łączności sieciowej w różnych punktach w sieci. Możesz monitorować wdrożenia w chmurze i lokalne lokalizacje, wiele centrów danych i oddziałów oraz wielowarstwowe aplikacje lub mikrousługi.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 505584abbe573c94056888b07406437650b09611
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728625"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Rozwiązanie Network Performance Monitor: monitorowanie wydajności

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodawać nowych testów w istniejącym obszarze roboczym ani włączać nowego obszaru roboczego w Network Performance Monitor. Można nadal korzystać z testów utworzonych przed 1 lipca 2021. Aby zminimalizować zakłócenia działania usługi dla bieżących obciążeń, należy [migrować testy z Network Performance Monitor do nowego monitora połączeń](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) na platformie Azure Network Watcher przed 29 lutego 2024.

Funkcja monitora wydajności w [Network Performance Monitor](network-performance-monitor.md) ułatwia monitorowanie łączności sieciowej w różnych punktach w sieci. Możesz monitorować wdrożenia w chmurze i lokalne lokalizacje, wiele centrów danych i oddziałów oraz wielowarstwowe aplikacje lub mikrousługi. Korzystając z monitora wydajności, można wykrywać problemy z siecią przed zaskargą użytkowników. Kluczowe zalety: 

- Monitorowanie strat i opóźnień w różnych podsieciach oraz Ustawianie alertów.
- Monitoruj wszystkie ścieżki (w tym nadmiarowe ścieżki) w sieci.
- Rozwiązywanie problemów z siecią przejściową i punktem w czasie, które są trudne do replikacji.
- Określ konkretny segment w sieci, który jest odpowiedzialny za obniżenie wydajności.
- Monitoruj kondycję sieci bez konieczności stosowania protokołu SNMP.


![Monitor wydajności sieci](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurowanie
Aby otworzyć konfigurację Network Performance Monitor, Otwórz [rozwiązanie Network Performance Monitor](network-performance-monitor.md)i wybierz pozycję **Konfiguruj**.

![Konfigurowanie rozwiązania Network Performance Monitor](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Utwórz nowe sieci

Sieć w Network Performance Monitor jest kontenerem logicznym dla podsieci. Ułatwia ona organizowanie monitorowania infrastruktury sieciowej zgodnie z potrzebami. Można utworzyć sieć z przyjazną nazwą i dodać do niej podsieci zgodnie z logiką biznesową. Można na przykład utworzyć sieć o nazwie Londyn i dodać wszystkie podsieci w centrum danych w Londynie. Możesz też utworzyć sieć o nazwie *ContosoFrontEnd* i dodać ją do tej sieci, aby wszystkie podsieci o nazwie contoso obsługiwały fronton aplikacji. Rozwiązanie automatycznie tworzy sieć domyślną, która zawiera wszystkie podsieci wykryte w danym środowisku. 

Po każdym utworzeniu sieci należy dodać do niej podsieć. Następnie ta podsieć zostanie usunięta z domyślnej sieci. W przypadku usunięcia sieci wszystkie jej podsieci są automatycznie zwracane do sieci domyślnej. Sieć domyślna działa jako kontener dla wszystkich podsieci, które nie znajdują się w żadnej sieci zdefiniowanej przez użytkownika. Nie można edytować ani usunąć sieci domyślnej. Zawsze pozostaje w systemie. W razie potrzeby można utworzyć dowolną liczbę sieci niestandardowych. W większości przypadków podsieci w organizacji są rozmieszczone w więcej niż jednej sieci. Utwórz co najmniej jedną sieć do grupowania podsieci dla logiki biznesowej.

Aby utworzyć nową sieć:


1. Wybierz kartę **sieci** .
1. Wybierz pozycję **Dodaj sieć**, a następnie wprowadź nazwę i opis sieci. 
2. Wybierz co najmniej jedną podsieć, a następnie wybierz pozycję **Dodaj**. 
3. Wybierz pozycję **Zapisz** , aby zapisać konfigurację. 


### <a name="create-monitoring-rules"></a>Utwórz reguły monitorowania 

Monitor wydajności generuje zdarzenia dotyczące kondycji, gdy próg wydajności połączeń sieciowych między dwiema podsieciami lub między dwiema sieciami został naruszony. System może poznawać te progi automatycznie. Można również podać progi niestandardowe. System automatycznie tworzy regułę domyślną, która generuje zdarzenie kondycji zawsze, gdy utrata lub opóźnienie między dowolnymi łączami sieci lub podsieci narusza próg wyznaczony przez system. Ten proces ułatwia monitorowanie infrastruktury sieciowej przez rozwiązanie, dopóki nie zostaną jawnie utworzone reguły monitorowania. Jeśli domyślna reguła jest włączona, wszystkie węzły wysyłają transakcje syntetyczne do wszystkich innych węzłów włączonych do monitorowania. Reguła domyślna jest przydatna w przypadku małych sieci. Przykładem jest scenariusz, w którym masz niewielką liczbę serwerów z mikrousługą i chcesz upewnić się, że wszystkie serwery mają łączność ze sobą.

>[!NOTE]
> Zalecamy wyłączenie reguły domyślnej i utworzenie niestandardowych reguł monitorowania, szczególnie w przypadku dużych sieci, w których jest używana duża liczba węzłów do monitorowania. Niestandardowe reguły monitorowania mogą zmniejszyć ruch generowany przez rozwiązanie i pomóc w organizowaniu monitorowania sieci.

Utwórz reguły monitorowania zgodnie z logiką biznesową. Przykładem może być monitorowanie wydajności łączności sieciowej dwóch lokacji biurowych do centrali. Grupuj wszystkie podsieci w pakiecie Office site1 w sieci O1. Następnie pogrupuj wszystkie podsieci w pakiecie Office site2 w obszarze Network O2. Na koniec Grupuj wszystkie podsieci w centrali w sieci H. Utwórz dwie reguły monitorowania — jeden między O1 i H, a drugi między O2 a H. 

Aby utworzyć niestandardowe reguły monitorowania:

1. Wybierz pozycję **Dodaj regułę** na karcie **monitor** , a następnie wprowadź nazwę i opis reguły.
2. Wybierz parę łączy sieciowych lub podsieci do monitorowania z list. 
3. Z listy rozwijanej sieć wybierz sieć zawierającą odpowiednie podsieci. Następnie wybierz podsieci z listy rozwijanej odpowiadająca podsieć. Jeśli chcesz monitorować wszystkie podsieci w łączu sieciowym, zaznacz opcję **wszystkie podsieci**. Analogicznie, wybierz inne podsieci, których chcesz użyć. Aby wykluczyć monitorowanie określonych linków podsieci z wybranych opcji, wybierz pozycję **Dodaj wyjątek**. 
4. Wybór protokołów ICMP i TCP do wykonania transakcji syntetycznych. 
5. Jeśli nie chcesz tworzyć zdarzeń dotyczących kondycji dla wybranych elementów, wyczyść pole wyboru **Włącz monitorowanie kondycji dla linków objętych tą regułą**. 
6. Wybierz pozycję warunki monitorowania. Aby ustawić progi niestandardowe dla generowania zdarzeń kondycji, wprowadź wartości progowe. Zawsze, gdy wartość warunku przekroczy wybrany próg dla wybranej pary sieciowej lub podsieci, zostanie wygenerowane zdarzenie kondycji. 
7. Wybierz pozycję **Zapisz** , aby zapisać konfigurację. 

Po zapisaniu reguły monitorowania można zintegrować tę regułę z Alert Management, wybierając pozycję **Utwórz alert**. Reguła alertu jest tworzona automatycznie przy użyciu zapytania wyszukiwania. Inne wymagane parametry są wypełniane automatycznie. Korzystając z reguły alertów, można odbierać alerty na podstawie poczty e-mail, a także istniejące alerty w ramach Network Performance Monitor. Alerty mogą również wyzwalać akcje zaradcze w elementach Runbook lub integrować je z istniejącymi rozwiązaniami do zarządzania usługami za pomocą elementów webhook. Wybierz pozycję **Zarządzaj alertami** , aby edytować ustawienia alertu. 

Możesz teraz utworzyć więcej reguł monitora wydajności lub przejść do pulpitu nawigacyjnego rozwiązania, aby korzystać z tej funkcji.

### <a name="choose-the-protocol"></a>Wybierz protokół

Network Performance Monitor używa transakcji syntetycznych w celu obliczenia metryk wydajności sieci, takich jak utrata pakietów i opóźnienie łącza. Aby lepiej zrozumieć te koncepcje, należy wziąć pod uwagę Network Performance Monitor agenta połączonego z jednym końcem łącza sieciowego. Ten Network Performance Monitor Agent wysyła pakiety sondy do drugiego agenta Network Performance Monitor połączonego z innym końcem sieci. Drugi Agent odpowiedzi z pakietami odpowiedzi. Ten proces jest powtarzany kilka razy. Mierząc liczbę odpowiedzi i czas potrzebny do odebrania każdej odpowiedzi, pierwszy Agent Network Performance Monitor ocenia opóźnienia linków i porzucania pakietów. 

Format, rozmiar i sekwencję tych pakietów są określane przez protokół wybierany podczas tworzenia reguł monitorowania. W zależności od protokołu pakietów pośredniczące urządzenia sieciowe, takie jak routery i przełączniki, mogą przetwarzać te pakiety inaczej. W związku z tym wybór protokołu ma wpływ na dokładność wyników. Wybór protokołu określa również, czy przed wdrożeniem rozwiązania Network Performance Monitor należy wykonać wszelkie czynności ręczne. 

Network Performance Monitor oferuje wybór między protokołami ICMP i TCP na potrzeby wykonywania transakcji syntetycznych. W przypadku wybrania protokołu ICMP podczas tworzenia reguły transakcji syntetycznej agenci Network Performance Monitor używają komunikatów ICMP ECHO do obliczenia opóźnienia sieci i utraty pakietów. ECHO ICMP używa tego samego komunikatu, który jest wysyłany przez konwencjonalne Narzędzie ping. W przypadku korzystania z protokołu TCP jako protokół Network Performance Monitor agenci wysyłają pakiety TCP SYN przez sieć. W tym kroku następuje zakończenie uzgadniania protokołu TCP, a połączenie jest usuwane przy użyciu pakietów RST. 

Przed wybraniem protokołu należy wziąć pod uwagę następujące informacje: 

* **Odnajdywanie wielu tras sieciowych.** Protokół TCP jest dokładniejszy podczas odnajdywania wielu tras i potrzebuje mniejszej liczby agentów w każdej podsieci. Na przykład jeden lub dwa Agenci używający protokołu TCP mogą odnajdywać wszystkie nadmiarowe ścieżki między podsieciami. Potrzebujesz kilku agentów, którzy używają protokołu ICMP, aby osiągnąć podobne wyniki. Przy użyciu protokołu ICMP, jeśli masz wiele tras między dwiema podsieciami, potrzebujesz więcej niż 5N agentów w podsieci źródłowej lub docelowej.

* **Dokładność wyników.** Routery i przełączniki umożliwiają przypisanie niższych priorytetów do pakietów echa protokołu ICMP w porównaniu z pakietami TCP. W niektórych sytuacjach, gdy urządzenia sieciowe są mocno załadowane, dane uzyskane przez TCP lepiej odzwierciedlają utratę i opóźnienia w przypadku aplikacji. Dzieje się tak, ponieważ większość przepływów ruchu aplikacji za pośrednictwem protokołu TCP. W takich przypadkach protokół ICMP zapewnia mniej dokładne wyniki w porównaniu z protokołem TCP. 

* **Konfiguracja zapory.** Protokół TCP wymaga, aby pakiety TCP były wysyłane do portu docelowego. Domyślnym portem używanym przez agentów Network Performance Monitor jest 8084. Port można zmienić podczas konfigurowania agentów. Upewnij się, że zapory sieciowe lub reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) (na platformie Azure) zezwalają na ruch na porcie. Należy również upewnić się, że Zapora lokalna na komputerach, na których są zainstalowane agenci, jest skonfigurowana tak, aby zezwalać na ruch na tym porcie. Skrypty programu PowerShell umożliwiają konfigurowanie reguł zapory na komputerach z systemem Windows, ale należy ręcznie skonfigurować zaporę sieciową. Z kolei protokół ICMP nie działa przy użyciu portu. W większości scenariuszy przedsiębiorstwa ruch ICMP jest dozwolony przez zapory, aby umożliwić korzystanie z narzędzi diagnostyki sieci, takich jak narzędzie ping. Jeśli można wysłać polecenie ping do jednego komputera z innego, można użyć protokołu ICMP bez konieczności ręcznego konfigurowania zapór.

>[!NOTE] 
> Niektóre zapory mogą blokować protokół ICMP, co może prowadzić do ponownej transmisji, co spowoduje powstanie dużej liczby zdarzeń w systemie informacji zabezpieczających i zarządzania zdarzeniami. Upewnij się, że wybrany protokół nie jest blokowany przez zaporę sieciową lub sieciowej grupy zabezpieczeń. W przeciwnym razie Network Performance Monitor nie może monitorować segmentu sieci. Zalecamy używanie protokołu TCP do monitorowania. Protokołu ICMP należy używać w scenariuszach, w których nie można używać protokołu TCP, np.: 
>
> - Używasz węzłów opartych na kliencie systemu Windows, ponieważ nieprzetworzone gniazda TCP nie są dozwolone na klientach z systemem Windows.
> - Zapora sieciowa lub sieciowej grupy zabezpieczeń blokuje protokół TCP.
> - Nie wiesz, jak przełączyć protokół.

Jeśli podczas wdrażania wybrano opcję użycia protokołu ICMP, można w dowolnym momencie przełączyć się do protokołu TCP, edytując domyślną regułę monitorowania.

1. Przejdź kolejno do pozycji Monitor **wydajności sieci**   >  ****   >  **Konfiguracja**   >  **monitora**. Następnie wybierz pozycję **domyślna reguła**. 
2. Przewiń do sekcji **Protokół** i wybierz protokół, który ma być używany. 
3. Wybierz pozycję **Zapisz** , aby zastosować ustawienie. 

Nawet jeśli reguła domyślna używa określonego protokołu, można utworzyć nowe reguły z innym protokołem. Można nawet utworzyć kombinację reguł, w których niektóre reguły używają protokołu ICMP, a inne korzystają z protokołu TCP. 

## <a name="walkthrough"></a>Przewodnik 

Teraz Obejrzyj proste badanie dotyczące głównej przyczyny zdarzenia dotyczącego kondycji.

Na pulpicie nawigacyjnym rozwiązania zdarzenie kondycji pokazuje, że łącze sieciowe jest w złej kondycji. Aby zbadać ten problem, wybierz kafelek **monitorowane linki sieciowe** .

Strona przechodzenia do szczegółów pokazuje, że łącze sieciowe **DMZ2-DMZ1** jest w złej kondycji. Wybierz pozycję **Wyświetl linki podsieci** dla tego linku sieciowego. 


Na stronie przechodzenie do szczegółów są wyświetlane wszystkie linki podsieci w łączu sieciowym **DMZ2-DMZ1** . W przypadku łączy podsieci czas oczekiwania przekracza próg, co sprawia, że łącze sieciowe jest w złej kondycji. Widoczne są również trendy opóźnienia obu łączy podsieci. Użyj kontrolki wybór czasu na wykresie, aby skoncentrować się na wymaganym zakresie czasu. Możesz zobaczyć pora dnia, gdy opóźnienie osiągnie swój szczyt. Przeszukaj dzienniki później dla tego okresu, aby sprawdzić problem. Wybierz pozycję **Wyświetl linki węzłów** , aby przejść do szczegółów. 
 
 ![Strona linków podsieci](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Podobnie jak w przypadku poprzedniej strony, Strona przechodzenia do szczegółów dla danego linku podsieci zawiera listę linków węzła składnika. Podobne akcje można wykonać w tym miejscu, tak jak w poprzednim kroku. Wybierz pozycję **Wyświetl topologię** , aby wyświetlić topologię między dwoma węzłami. 
 
 ![Strona linków węzłów](media/network-performance-monitor-performance-monitor/node-links.png) 

Wszystkie ścieżki między dwoma wybranymi węzłami są kreślone na mapie topologii. Można wizualizować topologię przeskoków między dwoma węzłami na mapie topologii. Zawiera jasne zdjęcie, ile tras istnieje między dwoma węzłami i jakie ścieżki pobiera pakiet danych. Wąskie gardła wydajności sieci są wyświetlane na czerwono. Aby zlokalizować błędne połączenie sieciowe lub wadliwe urządzenie sieciowe, poszukaj czerwonych elementów na mapie topologii. 

 ![Pulpit nawigacyjny topologii z mapą topologii](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

W okienku **Akcja** można sprawdzić utratę, opóźnienie i liczbę przeskoków w każdej ścieżce. Użyj paska przewijania, aby wyświetlić szczegóły ścieżek w złej kondycji. Użyj filtrów, aby wybrać ścieżki z przeskokiem w złej kondycji w celu wykreślenia topologii tylko dla wybranych ścieżek. Aby powiększyć lub pomniejszyć mapę topologii, użyj kółka myszy. 

Na poniższej ilustracji główne przyczyny problemów z obszarami problemu w danej sekcji sieci są wyświetlane w czerwonej ścieżce i przeskokach. Wybierz węzeł na mapie topologii, aby wyświetlić właściwości węzła, w tym nazwę FQDN i adres IP. Wybranie skoku pokazuje adres IP przeskoku. 
 
![Mapa topologii z wybranymi właściwościami węzła](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Następne kroki
[Wyszukaj dzienniki](../logs/log-query-overview.md) , aby wyświetlić szczegółowe rekordy danych wydajności sieci.