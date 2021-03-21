---
title: 'Aplikacja SaaS: monitorowanie wydajności wielu baz danych'
description: Monitorowanie i zarządzanie wydajnością Azure SQL Database w wielodostępnej aplikacji SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 21c0a7a3fe6d5be9d99ea53dbfa74cf72e163272
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780669"
---
# <a name="monitor-and-manage-performance-of-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie wydajnością Azure SQL Database w wielodostępnej aplikacji SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku przedstawiono kilka kluczowych scenariuszy zarządzania wydajnością używanych w aplikacjach SaaS. W celu symulowania aktywności wszystkich baz danych dzierżaw przy użyciu generatora obciążenia są zademonstrowane wbudowane funkcje monitorowania i alertów SQL Database i pul elastycznych.

Wingtip bilety SaaS baza danych dla dzierżawców korzysta z modelu danych o pojedynczej dzierżawie, gdzie każdy z nich ma własną bazę danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby skorzystać z tego typowego wzorca użycia bazy danych, bazy danych dzierżaw są wdrażane do pul elastycznych. Elastyczne pule umożliwiają optymalizację kosztu rozwiązania dzięki udostępnieniu zasobów pomiędzy wieloma bazami danych. W przypadku tego typu wzorca ważne jest, aby monitorować użycie zasobów bazy danych i puli, co ma na celu rozsądne równoważenie obciążenia między pulami. Należy także upewnić się, że pojedyncze bazy danych posiadają odpowiednie zasoby, i że pule nie zbliżają się do swoich limitów liczby jednostek [eDTU](purchasing-models.md#dtu-based-purchasing-model). W tym samouczku przedstawiono metody monitorowania baz danych i pul oraz zarządzania nimi, a także wykonywanie akcji naprawczych w odpowiedzi na wahania obciążenia.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono Wingtip bilety bazy danych SaaS dla dzierżawy. Aby wdrożyć program w ciągu mniej niż pięciu minut, zobacz [wdrażanie i eksplorowanie bazy danych Wingtip biletów SaaS na aplikację dzierżawców](./saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców zarządzania wydajnością SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. W przypadku hostowania wielu dzierżawców pule elastyczne to ekonomiczny sposób udostępniania zasobów i zarządzania nimi dla grupy baz danych z nieprzewidywalnymi obciążeniami. Przy pewnych wzorcach obciążenia korzyści mogą się pojawić przy zarządzaniu w puli zaledwie dwoma bazami danych S3.

![Diagram aplikacji](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pule i bazy danych w pulach powinny być monitorowane w celu zapewnienia, że pozostają w akceptowalnych zakresach wydajności. Dostosuj konfigurację puli, aby spełniała potrzeby zagregowanego obciążenia wszystkich baz danych, co zapewnia, że jednostek eDTU puli są odpowiednie dla całego obciążenia. Dostosuj minimalne i maksymalne wartości eDTU dla bazy danych do wartości odpowiadających wymaganiom konkretnych aplikacji.

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorowania wydajności, najbardziej efektywne jest **ustawienie alertów wyzwalanych w przypadku, gdy bazy danych lub pule są nienormalne dla normalnych zakresów**.
* Aby odpowiedzieć na krótkoterminowe fluktuacje zagregowanego rozmiaru obliczeń puli, **poziom eDTU puli można skalować w górę lub w dół**. Jeśli takie wahania występują regularnie lub są przewidywalne, **można zaplanować automatyczne skalowanie puli**. Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Odpowiedzią na wahania długoterminowe lub zmiany liczby baz danych jest **przeniesienie pojedynczych baz danych do innych pul**.
* Aby odpowiedzieć na krótkoterminowe wzrosty *pojedynczych* **baz danych, można wypróbować każdą z pul, a następnie przypisać indywidualny rozmiar**. Po zmniejszeniu obciążenia można zwrócić bazę danych do puli. Gdy jest to znane z wyprzedzeniem, można przenieść bazy danych zapobiegawczo, aby zapewnić, że baza danych zawsze ma potrzebne zasoby, i aby uniknąć wpływu na inne bazy danych w puli. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. Monitorowanie i alerty są dostępne w bazach danych i pulach. Ta wbudowana funkcja monitorowania i generowania alertów jest zależna od zasobów, dlatego jest wygodnie używana w przypadku małych liczb zasobów, ale nie jest bardzo wygodna podczas pracy z wieloma zasobami.

W przypadku scenariuszy o dużej ilości, w których pracujesz z wieloma zasobami, można użyć [dzienników Azure monitor](./saas-dbpertenant-log-analytics.md) . Jest to oddzielna usługa platformy Azure, która zapewnia analizę za pośrednictwem emitowanych dzienników zebranych w obszarze roboczym Log Analytics. Dzienniki Azure Monitor mogą zbierać dane telemetryczne z wielu usług i używać ich do wykonywania zapytań i ustawiania alertów.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz Wingtip bilety bazy danych SaaS na skrypty aplikacji dzierżawców

Wingtip bilety SaaS wielodostępnych skryptów bazy danych i kodu źródłowego aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Pule stają się opłacalne nawet w przypadku zaledwie dwóch baz danych nas poziomie S3, a związane z nimi oszczędności wzrastają wraz ze zwiększeniem liczby baz danych w puli, co jest spowodowane efektem uśredniania. Aby dobrze zrozumieć działanie monitorowania wydajności i zarządzania nią w dużej skali, w tym samouczku zalecamy wdrożenie co najmniej 20 baz danych.

Jeśli masz już zainicjowaną partię dzierżawców w poprzednim samouczku, przejdź do sekcji [symulowanie użycia we wszystkich bazach danych dzierżaw](#simulate-usage-on-all-tenant-databases) .

1. W **ISE programu PowerShell** Otwórz pozycję... \\ \\Demo-PerformanceMonitoringAndManagement.ps1monitorowania wydajności modułów szkoleniowych i zarządzania nimi \\ **. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustawianie **$DemoScenario**  =  **1**, **Inicjowanie obsługi partii dzierżawców**
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Ten skrypt wdroży 17 dzierżaw w czasie krótszym niż pięć minut.

Skrypt *New-TenantBatch* używa zagnieżdżonego lub połączonego zestawu [Menedżer zasobów](../../azure-resource-manager/index.yml) szablonów, które tworzą partię dzierżawców, które domyślnie kopiuje bazę danych **basetenantdb** na serwerze wykazu w celu utworzenia nowych baz danych dzierżawy, a następnie rejestruje je w wykazie i inicjuje je przy użyciu nazwy dzierżawy i typu miejsca. Jest to zgodne ze sposobem, w jaki aplikacja inicjuje nową dzierżawę. Wszelkie zmiany wprowadzone do *basetenantdb* są stosowane do wszystkich nowych dzierżawców, które zostały udostępnione w późniejszym czasie. Zobacz [samouczek zarządzania schematami](saas-tenancy-schema-management.md) , aby dowiedzieć się, jak wprowadzać zmiany schematu do *istniejących* baz danych dzierżaw (w tym bazy danych *basetenantdb* ).

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

Zostanie udostępniony skrypt *Demo-PerformanceMonitoringAndManagement.ps1* , który symuluje obciążenie uruchomione dla wszystkich baz danych dzierżawy. Obciążenie jest generowane przy użyciu jednego z dostępnych scenariuszy ładowania:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generuj normalne obciążenie natężenia (około 40 jednostek DTU) |
| 3 | Generowanie obciążenia z dłuższymi i częstszymi skokami wartości dla każdej bazy danych|
| 4 | Generuj obciążenie o większej serii jednostek DTU na bazę danych (około 80 jednostek DTU)|
| 5 | Generowanie normalnego obciążenia oraz wysokiego obciążenia dla jednej dzierżawy (około 95 jednostek DTU)|
| 6 | Generowanie niezrównoważonego obciążenia dla wielu pul|

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (mierzone w jednostkach eDTU), czas trwania i interwały są zróżnicowane dla wszystkich baz danych, co symuluje nieprzewidywalną aktywność dzierżawy.

1. W **ISE programu PowerShell** Otwórz pozycję... \\ \\Demo-PerformanceMonitoringAndManagement.ps1monitorowania wydajności modułów szkoleniowych i zarządzania nimi \\ **. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustaw **$DemoScenario**  =  **2**, *generując normalne obciążenie intensywnie*.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

Bilety Wingtip SaaS Database na dzierżawcę to aplikacja SaaS, a rzeczywiste obciążenie aplikacji SaaS jest zwykle sporadyczne i nieprzewidywalne. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Do nawiązania wzorca obciążenia potrzeba kilku minut, więc uruchom generator obciążenia przez 3-5 minut przed próbą monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako seria zadań w lokalnej sesji programu PowerShell. Karta *Demo-PerformanceMonitoringAndManagement.ps1* musi pozostać otwarta! Zamknięcie karty lub wstrzymanie pracy komputera spowoduje zatrzymanie generatora obciążenia. Generator obciążenia pozostaje w stanie *zadania* , w którym generuje obciążenie dla nowych dzierżawców, które są inicjowane po uruchomieniu generatora. Użyj *klawiszy CTRL-C* , aby zatrzymać wywoływanie nowych zadań i zakończyć działanie skryptu. Generator obciążenia będzie nadal działać, ale tylko dla istniejących dzierżawców.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie użycia zasobów przy użyciu Azure Portal

Aby monitorować użycie zasobów wynikające z zastosowanego obciążenia, Otwórz Portal w puli zawierającej bazy danych dzierżawcy:

1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do serwera *tenants1-DPT- &lt; User &gt;* .
1. Przewiń w dół, zlokalizuj elastyczne pule i kliknij pozycję **Pool1**. Ta pula zawiera wszystkie bazy danych dzierżaw utworzone dotychczas.

Obserwuj wykresy monitorowania **elastycznej puli** i monitorowania **Elastic Database** .

Wykorzystanie zasobów puli to zagregowane użycie bazy danych dla wszystkich baz danych w puli. Wykres bazy danych przedstawia pięć baz danych okienko:

![Wykres bazy danych](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Ponieważ w puli znajdują się dodatkowe bazy danych powyżej pięciu pierwszych, użycie puli pokazuje aktywność, która nie jest uwzględniona na wykresie z górnym pięcioma bazami danych. Aby uzyskać więcej informacji, kliknij pozycję **wykorzystanie zasobów bazy danych**:

![wykorzystanie zasobów bazy danych](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ustawianie alertów wydajności dla puli

Ustaw Alert w puli, który wyzwala na \> 75% użycie w następujący sposób:

1. Otwórz *Pool1* (na *tenants1-DPT \<user\> -* Server) w [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](./media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metryka = Procent eDTU**
   * **Warunek = większe niż**
   * **Próg = 75**
   * **Okres = w ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do pola *dodatkowe adresy e-mail administratora* , a następnie kliknij przycisk **OK**.

   ![ustawianie alertu](./media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skalowanie mocno obciążonej puli w górę

Jeśli poziom zagregowanego obciążenia puli zwiększy się do poziomu maksymalnych możliwości puli i osiągnie 100 % wykorzystania jednostek eDTU, będzie to miało wpływ na wydajność poszczególnych baz danych, co potencjalnie może wydłużyć czasy odpowiedzi na zapytania dla wszystkich baz danych w puli.

**Krótkoterminowe**, rozważ skalowanie w górę puli w celu zapewnienia dodatkowych zasobów lub usuwania baz danych z puli (przenoszenie ich do innych pul lub z puli do autonomicznej warstwy usług).

**Dłużej** należy rozważyć optymalizację zapytań lub użycie indeksów w celu poprawy wydajności bazy danych. W zależności od wrażliwości aplikacji na problemy wydajnościowe najlepszym rozwiązaniem jest skalowanie puli w górę przed osiągnięciem przez nią wykorzystania jednostek eDTU równego 100%. Użyj alertów do wczesnego ostrzegania.

Zajętość puli można zasymulować, zwiększając obciążenia wytwarzane przez generator. Wydłużenie wielu baz danych i wydłużenie zagregowanego obciążenia puli bez zmiany wymagań poszczególnych baz danych. Skalowanie puli w górę można łatwo przeprowadzić w portalu lub z programu PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw *$DemoScenario*  =  **3**, _Generuj obciążenie z dłuższą i bardziej częstą liczbą serii na bazę danych_ , aby zwiększyć intensywność obciążenia zagregowanego w puli bez zmiany szczytowego obciążenia wymaganego przez każdą bazę danych.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.

1. Przejdź do **Pool1** w Azure Portal.

Monitoruj zwiększone użycie jednostek eDTU puli na górnym wykresie. Nowe wyższe obciążenie może zostać rozpoczęte przez kilka minut, ale należy szybko sprawdzić, czy pula zaczyna trafiać maksymalne użycie, a gdy obciążenie jest stałe w nowym wzorcu, szybko przeciąża pulę.

1. Aby skalować pulę w górę, kliknij pozycję **Konfiguruj pulę** w górnej części strony **Pool1** .
1. Dostosuj ustawienie liczby **jednostek EDTU puli** do **100**. Zmiana liczby jednostek eDTU puli nie zmienia ustawienia poszczególnych baz danych (które nadal ma maksymalną wartość 50 jednostek eDTU na bazę danych). Ustawienia poszczególnych baz danych są widoczne po prawej stronie strony **Konfigurowanie puli** .
1. Kliknij przycisk **Zapisz** , aby przesłać żądanie skalowania puli.

Wróć do omówienia **Pool1**  >   , aby wyświetlić wykresy monitorowania. Monitoruj wpływ puli o więcej zasobów (choć z nielicznymi bazami danych i losowo załadowanego obciążenia nie zawsze łatwo sprawdzaj się, dopóki nie zostanie uruchomiony przez jakiś czas). Patrząc na wykresy należy sobie zdawać sprawę, że 100% na górnym wykresie odpowiada teraz 100 jednostkom eDTU, podczas gdy na dolnym wykresie 100% nadal odpowiada 50 jednostkom eDTU, gdyż maksymalna wartość dla pojedynczej bazy danych nadal wynosi 50 jednostek eDTU.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. W ostatniej chwili przed włączeniem dla każdej bazy danych nowej wartości eDTU dla puli wszystkie aktywne połączenia zostają przerwane. Kod aplikacji powinien być napisany w taki sposób, aby zawsze próbować ponownie nawiązać przerwane połączenie, by móc ponownie połączyć się z bazą danych w puli przeskalowanej w górę.

## <a name="load-balance-between-pools"></a>Równoważenie obciążenia między pulami

Alternatywnym rozwiązaniem w stosunku do skalowania puli w górę jest utworzenie drugiej puli i przeniesienie do niej baz danych w celu zrównoważenia obciążenia między dwoma pulami. Aby to rozwiązanie zadziałało, nowa pula musi zostać utworzona na tym samym serwerze co pierwsza.

1. W [Azure Portal](https://portal.azure.com)Otwórz serwer **tenants1-DPT- &lt; &gt; użytkownika** .
1. Kliknij pozycję **+ Nowa pula** , aby utworzyć pulę na bieżącym serwerze.
1. W szablonie **elastycznej puli** :

   1. Ustaw **nazwę** na *Pool2*.
   1. Pozostaw warstwę cenową **Pula Standardowa**.
   1. Kliknij pozycję **Konfiguruj pulę**,
   1. Ustaw wartość **eDTU puli** na *50 jednostek eDTU*.
   1. Kliknij pozycję **Dodaj bazy danych** , aby wyświetlić listę baz danych na serwerze, które mogą zostać dodane do *Pool2*.
   1. Wybierz dowolną 10 baz danych, aby przenieść je do nowej puli, a następnie kliknij przycisk **Wybierz**. Jeśli używasz generatora obciążenia, usługa już wie, że profil wydajności wymaga większej puli niż domyślny 50 jednostek eDTU i zaleca się rozpoczęcie od ustawienia jednostki eDTU 100.

      ![zaleca](./media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Na potrzeby tego samouczka pozostaw wartość domyślną na 50 jednostek eDTU, a następnie kliknij pozycję **Wybierz** ponownie.
   1. Wybierz **przycisk OK** , aby utworzyć nową pulę i przenieść do niej wybrane bazy danych.

Utworzenie puli i przeniesienie baz danych trwa kilka minut. Po przeniesieniu bazy danych są one w trybie online i są w pełni dostępne do momentu, w którym wszystkie otwarte połączenia są zamknięte. Tak długo, jak masz kilka logiki ponawiania prób, klienci będą łączyć się z bazą danych w nowej puli.

Przejdź do **Pool2** (na *tenants1-DPT- \<user\>* Server), aby otworzyć pulę i monitorować jej wydajność. Jeśli go nie widzisz, poczekaj na ukończenie aprowizacji nowej puli.

Zobaczysz teraz, że użycie zasobów na *Pool1* zostało przerwane i że *Pool2* jest teraz podobnie załadowane.

## <a name="manage-performance-of-an-individual-database"></a>Zarządzanie wydajnością pojedynczej bazy danych

Jeśli poszczególna baza danych w puli ma duże obciążenie, w zależności od konfiguracji puli może zamiarować zasoby w puli i wpływać na inne bazy danych. Jeśli działanie może być kontynuowane przez jakiś czas, baza danych może zostać tymczasowo przeniesiona z puli. Dzięki temu baza danych ma dodatkowe zasoby, których potrzebuje, i izoluje ją od innych baz danych.

W tym ćwiczeniu zostanie zasymulowane zwiększone obciążenie dotyczące miejsca Contoso Concert Hall wywołane wzmożonym pobytem na bilety na popularny koncert.

1. W **ISE programu PowerShell** Otwórz pozycję... \\ Skrypt *Demo-PerformanceMonitoringAndManagement.ps1* .
1. Ustaw **$DemoScenario = 5, wygeneruj normalne obciążenie i wysokie obciążenie dla pojedynczej dzierżawy (około 95 jednostek DTU).**
1. Ustaw wartość zmiennej **$SingleTenantDatabaseName = contosoconcerthall**
1. Wykonaj skrypt, używając klawisza **F5**.


1. W [Azure Portal](https://portal.azure.com)przejdź do listy baz danych na serwerze *tenants1-DPT- \<user\>* Server. 
1. Kliknij bazę danych **contosoconcerthall** .
1. Kliknij pulę, w której znajduje się **contosoconcerthall** . Znajdź pulę w sekcji **Pula elastyczna** .

1. Sprawdź wykres **monitorowania puli elastycznej** i poszukaj zwiększonych obciążeń jednostek eDTU puli. Po minucie lub dwóch powinno być widoczne wyższe obciążenie, które wkrótce osiągnie poziom 100% wykorzystania puli.
2. Zbadaj wyświetlacz **monitorowania Elastic Database** , który pokazuje bazy danych okienko w ciągu ostatniej godziny. Baza danych *contosoconcerthall* powinna wkrótce pojawić się jako jedna z pięciu baz danych okienko.
3. **Kliknij wykres monitorowania Elastic Database**  , aby otworzyć stronę **użycie zasobów bazy danych** , na której można monitorować dowolną bazę danych. Umożliwia to wyizolowanie ekranu dla bazy danych *contosoconcerthall* .
4. Z listy baz danych kliknij pozycję **contosoconcerthall**.
5. Kliknij pozycję **warstwa cenowa (DTU skalowania)** , aby otworzyć stronę **Konfigurowanie wydajności** , na której można ustawić autonomiczny rozmiar obliczeń dla bazy danych.
6. Kliknij kartę **Standardowa**, aby otworzyć opcje skalowania w warstwie Standardowa.
7. Przesuń **suwak DTU** w prawo, aby wybrać **100** DTU. Należy pamiętać, że odnosi się to do celu usługi ( **S3**).
8. Kliknij przycisk **Zastosuj** , aby przenieść bazę danych z puli i uczynić ją *standardową* bazą danych S3.
9. Po zakończeniu skalowania należy monitorować wpływ bazy danych contosoconcerthall i Pool1 na bloki elastycznej puli i bazy danych.

Po nawiązaniu wysokiego obciążenia bazy danych contosoconcerthall należy natychmiast zwrócić ją do puli, aby zmniejszyć jej koszt. Jeśli jest to niejasne, gdy zostanie wykonane, można ustawić alert dla bazy danych, która zostanie wyzwolona, gdy użycie jednostek DTU spadnie poniżej wartości maksymalnej dla bazy danych w puli. Przenoszenie bazy danych do puli opisano w ćwiczeniu 5.

## <a name="other-performance-management-patterns"></a>Inne wzorce zarządzania wydajnością

**Skalowanie przed prewencyjne** W tym ćwiczeniu, w którym opisano sposób skalowania izolowanej bazy danych, wiesz, która baza danych ma być wyszukiwana. Jeśli zarządzanie korytarzem w firmie Contoso zostało poinformowane Wingtips o zbliżającej się sprzedaży biletów, baza danych mogła zostać przeniesiona z puli zapobiegawczo. W przeciwnym razie konieczne byłoby skonfigurowanie alertów dla puli lub bazy danych w celu informowania o zmieniającej się sytuacji. Byłoby wysoce nieprzyjemne, gdyby wiadomość o zdarzeniu pochodziła od innych dzierżawców w puli, skarżących się na pogorszenie wydajności. Jeśli jednak dzierżawca może przewidzieć, jak długo będą mu potrzebne dodatkowe zasoby, możesz tak skonfigurować element Runbook usługi Azure Automation, aby przenieść bazę danych z puli, a następnie z powrotem do puli zgodnie z ustalonym harmonogramem.

**Samoobsługowe skalowanie przez dzierżawcę** Ponieważ skalowanie jest zadaniem łatwo wywoływanym za pośrednictwem interfejsu API zarządzania, można w prosty sposób wbudować możliwość skalowania baz danych dzierżawy w aplikację używaną przez dzierżawcę i zaoferować ją jako funkcję usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie puli w górę i w dół zgodnie z harmonogramem odpowiadającym wzorcom użycia**

W przypadku, gdy zagregowane wykorzystanie dzierżaw jest zgodne z przewidywalnymi wzorcami użycia można użyć usługi Azure Automation do skalowania puli w górę i w dół zgodnie z harmonogramem. Na przykład pulę można skalować w dół po godzinie 18:00 i ponownie w górę przed godziną 6:00 w dni robocze, jeśli wiadomo, że pomiędzy tymi godzinami występuje spadek wymagań dotyczących zasobów.



## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie korzystania z baz danych dzierżaw przy użyciu dostarczonego generatora obciążenia
> * Monitorowanie reakcji baz danych dzierżaw na wzrost obciążenia
> * Skalowanie w górę elastycznej puli w reakcji na zwiększone obciążenie bazy danych
> * Aprowizacja drugiej elastycznej puli w celu równoważenia aktywności baz danych

[Samouczek przywracania pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Dodatkowe zasoby

* Dodatkowe [samouczki, które kompilują się na Wingtip bilety SaaS bazy danych na wdrożenie aplikacji dzierżawców](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Pule elastyczne SQL](elastic-pool-overview.md)
* [Usługa Azure Automation](../../automation/automation-intro.md)
* [Dzienniki Azure monitor](./saas-dbpertenant-log-analytics.md) — Konfigurowanie i korzystanie z przewodnika po dziennikach Azure monitor