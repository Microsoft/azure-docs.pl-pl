---
title: Monitorowanie wydajności bazy danych podzielonej na fragmenty z wieloma dzierżawami
description: Monitorowanie i zarządzanie wydajnością podzielonej na fragmenty wielodostępnego Azure SQL Database w aplikacji SaaS z wieloma dzierżawcami
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
ms.openlocfilehash: 3307e31935377f55f792e640934e59017c1980c7
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619625"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorowanie i zarządzanie wydajnością podzielonej na fragmenty wielodostępnego Azure SQL Database w aplikacji SaaS z wieloma dzierżawcami
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku przedstawiono kilka kluczowych scenariuszy zarządzania wydajnością używanych w aplikacjach SaaS. Przy użyciu generatora obciążenia w celu symulowania aktywności baz danych z wieloma dzierżawcami podzielonej na fragmenty, wbudowane funkcje monitorowania i alertów Azure SQL Database są przedstawiane.

Wingtip bilety SaaS wielodostępnej aplikacji bazy danych korzystają z modelu danych wielodostępnego podzielonej na fragmenty, gdzie dane o miejscu (dzierżawcy) są dystrybuowane według identyfikatora dzierżawy w przypadku potencjalnie wielu baz danych. Podobnie jak w przypadku wielu innych aplikacji SaaS, oczekiwany wzorzec obciążenia dzierżawy charakteryzuje się nieprzewidywalnością i sporadycznością występowania. Innymi słowy, sprzedaż biletów może nastąpić w dowolnej chwili. Aby skorzystać z tego typowego wzorca użycia bazy danych, bazy danych można skalować w górę i w dół w celu zoptymalizowania kosztów rozwiązania. W przypadku tego typu wzorca ważne jest monitorowanie użycia zasobów bazy danych w celu zapewnienia, że obciążenia są rozsądnie zrównoważone dla potencjalnie wielu baz danych. Należy również upewnić się, że poszczególne bazy danych mają odpowiednie zasoby i nie będą przekraczać limitów [jednostek DTU](purchasing-models.md#dtu-based-purchasing-model) . W tym samouczku przedstawiono sposoby monitorowania baz danych i zarządzania nimi oraz podejmowanie działań naprawczych w odpowiedzi na różnice w obciążeniu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Symulowanie użycia dla wielodostępnej bazy danych podzielonej na fragmenty przez uruchomienie dostarczonego generatora obciążenia
> * Monitoruj bazę danych w miarę reagowania na wzrost obciążenia
> * Skalowanie bazy danych w górę w odpowiedzi na zwiększone obciążenie bazy danych
> * Udostępnianie dzierżawy w bazie danych z jedną dzierżawą

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono Wingtip biletów SaaS aplikację bazy danych z wieloma dzierżawcami. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji bazy danych z obsługą wielu dzierżawców Wingtip SaaS](../../sql-database/saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Wprowadzenie do wzorców zarządzania wydajnością SaaS

Zarządzanie wydajnością bazy danych polega na zbieraniu danych dotyczących wydajności i analizowaniu ich, a następnie reagowaniu na te dane przez dostosowanie parametrów w celu zachowania akceptowalnego czasu odpowiedzi aplikacji. 

### <a name="performance-management-strategies"></a>Strategie zarządzania wydajnością

* Aby uniknąć konieczności ręcznego monitorowania wydajności, najprawdopodobniej **ustawisz alerty wyzwalane w przypadku nienormalnego przedziału baz danych**.
* Aby odpowiedzieć na krótkoterminowe fluktuacje w rozmiarze obliczeniowym bazy danych, **poziom jednostek DTU można skalować w górę lub w dół**. Jeśli ta fluktuacja występuje regularnie lub przewidywalna, **skalowanie bazy danych może odbywać się automatycznie**. Na przykład skalowanie w dół może nastąpić, kiedy przewidywane jest niskie obciążenie — w nocy lub podczas weekendów.
* Aby odpowiedzieć na dłuższe wahania lub zmiany w dzierżawach, **poszczególne dzierżawy można przenieść do innej bazy danych**.
* Aby odpowiedzieć na krótkoterminowe wzrosty obciążenia *poszczególnych* dzierżawców, **poszczególne dzierżawy mogą być wyłączane z bazy danych i mieć przypisany indywidualny rozmiar obliczeniowy**. Po zmniejszeniu obciążenia dzierżawa może zostać zwrócona do bazy danych z wieloma dzierżawcami. Gdy jest to znane z wyprzedzeniem, dzierżawy mogą być przenoszone zapobiegawczo, aby zapewnić, że baza danych zawsze ma potrzebne zasoby, i nie ma wpływu na inne dzierżawców w bazie danych z wieloma dzierżawcami. Jeśli takie wymaganie jest przewidywalne, na przykład w przypadku oczekiwania na wzmożone zakupy biletów na popularną imprezę, wówczas takie działanie funkcji zarządzania można uwzględnić w aplikacji.

Witryna [Azure Portal](https://portal.azure.com) udostępnia wbudowane funkcje monitorowania i alertów dla większości zasobów. W przypadku SQL Database, monitorowanie i alerty są dostępne w bazach danych. Ta wbudowana funkcja monitorowania i generowania alertów jest zależna od zasobów, dlatego jest wygodnie używana w przypadku małych liczb zasobów, ale nie jest wygodna podczas pracy z wieloma zasobami.

W przypadku scenariuszy o dużej ilości, w których pracujesz z wieloma zasobami, można użyć [dzienników Azure monitor](https://azure.microsoft.com/services/log-analytics/) . Jest to oddzielna usługa platformy Azure, która zapewnia analizę za pośrednictwem emitowanych dzienników zebranych w obszarze roboczym Log Analytics. Dzienniki Azure Monitor mogą zbierać dane telemetryczne z wielu usług i używać ich do wykonywania zapytań i ustawiania alertów.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz Wingtip bilety SaaS kodu źródłowego i skryptów aplikacji bazy danych z wieloma dzierżawcami

Wingtip bilety SaaS wielodostępnych skryptów bazy danych i kodu źródłowego aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) . Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.

## <a name="provision-additional-tenants"></a>Aprowizacja dodatkowych dzierżaw

Aby zrozumieć, jak monitorowanie wydajności i zarządzanie działa w odpowiedniej skali, ten samouczek wymaga posiadania wielu dzierżawców w bazie danych z wieloma dzierżawcami.

Jeśli masz już zainicjowaną partię dzierżawców w poprzednim samouczku, przejdź do sekcji [symulowanie użycia we wszystkich bazach danych dzierżaw](#simulate-usage-on-all-tenant-databases) .

1. W **ISE programu PowerShell**Otwórz pozycję... \\ \\Demo-PerformanceMonitoringAndManagement.ps1monitorowania wydajności modułów szkoleniowych i zarządzania nimi \\ * *. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustawianie **$DemoScenario**  =  **1**, _Inicjowanie obsługi partii dzierżawców_
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt wdraża 17 dzierżawców w bazie danych z wieloma dzierżawcami w ciągu kilku minut. 

Skrypt *New-TenantBatch* tworzy nowe dzierżawy z unikatowymi kluczami dzierżaw w bazie danych podzielonej na fragmenty z wieloma dzierżawami i inicjuje je przy użyciu nazwy dzierżawy i typu miejsca. Jest to zgodne ze sposobem, w jaki aplikacja inicjuje nową dzierżawę. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Symulowanie użycia we wszystkich baz danych dzierżaw

Zostanie udostępniony skrypt *Demo-PerformanceMonitoringAndManagement.ps1* , który symuluje obciążenie uruchomione względem bazy danych z wieloma dzierżawcami. Obciążenie jest generowane przy użyciu jednego z dostępnych scenariuszy ładowania:

| Demonstracja | Scenariusz |
|:--|:--|
| 2 | Generuj normalne obciążenie natężenia (około 30 jednostek DTU) |
| 3 | Generuj obciążenie z dłuższymi seriami na dzierżawcę|
| 4 | Generuj obciążenie większymi liczbami jednostek DTU na dzierżawcę (około 70 jednostek DTU)|
| 5 | Generuj wysokie natężenie (około 90 jednostek DTU) dla jednej dzierżawy oraz normalne obciążenie dla wszystkich innych dzierżawców |

Generator obciążenia stosuje obciążenie *syntetyczne* wyłącznie do procesorów dla każdej bazy danych dzierżawy. Generator uruchamia zadanie dla każdej bazy danych dzierżawy, co powoduje cykliczne wywołanie procedury składowanej, która generuje obciążenie. Poziomy obciążenia (w DTU), czas trwania i interwały są zróżnicowane dla wszystkich baz danych, co symuluje nieprzewidywalne działanie dzierżawy.

1. W **ISE programu PowerShell**Otwórz pozycję... \\ \\Demo-PerformanceMonitoringAndManagement.ps1monitorowania wydajności modułów szkoleniowych i zarządzania nimi \\ * *. Nie zamykaj tego skryptu, gdyż w ramach tego samouczka będzie konieczne uruchomienie kilku scenariuszy.
1. Ustawianie **$DemoScenario**  =  **2**, _generowanie normalnego obciążenia o natężeniu_
1. Naciśnij klawisz **F5** , aby zastosować obciążenie do wszystkich dzierżawców.

Bilety Wingtip SaaS wielodostępnej bazy danych to aplikacja SaaS, a rzeczywiste obciążenie aplikacji SaaS jest zwykle sporadyczne i nieprzewidywalne. Aby to zasymulować, generator w sposób losowy obciąża wszystkie dzierżawy. Do nawiązania wzorca obciążenia potrzeba kilku minut, więc uruchom generator obciążenia przez 3-5 minut przed próbą monitorowania obciążenia w poniższych sekcjach.

> [!IMPORTANT]
> Generator obciążenia działa jako seria zadań w nowym oknie programu PowerShell. W przypadku zamknięcia sesji Generator obciążenia zostaje zatrzymany. Generator obciążenia pozostaje w stanie *zadania* , w którym generuje obciążenie dla nowych dzierżawców, które są inicjowane po uruchomieniu generatora. Użyj *klawiszy CTRL-C* , aby zatrzymać wywoływanie nowych zadań i zakończyć działanie skryptu. Generator obciążenia będzie nadal działać, ale tylko dla istniejących dzierżawców.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorowanie użycia zasobów przy użyciu Azure Portal

Aby monitorować użycie zasobów wynikające z zastosowanego obciążenia, Otwórz Portal do bazy danych z wieloma dzierżawcami, **tenants1**zawierający dzierżawców:

1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do serwera *tenants1-MT- &lt; &gt; User*.
1. Przewiń w dół i Znajdź bazy danych, a następnie kliknij pozycję **tenants1**. Ta podzielonej na fragmenty baza danych z wieloma dzierżawcami zawiera wszystkie utworzone dzierżawy.

![Wykres bazy danych](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Obserwuj wykres **DTU** .

## <a name="set-performance-alerts-on-the-database"></a>Ustawianie alertów wydajności dla bazy danych

Ustaw Alert dla bazy danych, która wyzwala na \> 75% użycie w następujący sposób:

1. Otwórz bazę danych *tenants1* (na serwerze *tenants1-MT- &lt; User &gt; * ) w [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **Reguły alertów**, a następnie kliknij przycisk **+ Dodaj alert**:

   ![dodawanie alertu](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Podaj nazwę, taką jak **Wysoki poziom jednostek DTU**,
1. Ustaw następujące wartości:
   * **Metric = procent jednostek DTU**
   * **Warunek = większe niż**
   * **Próg = 75**.
   * **Okres = w ciągu ostatnich 30 minut**
1. Dodaj adres e-mail do pola *dodatkowe adresy e-mail administratora* , a następnie kliknij przycisk **OK**.

   ![ustawianie alertu](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skalowanie w górę zajętej bazy danych

Jeśli poziom obciążenia zostanie zwiększony w bazie danych do punktu, w którym maxes bazę danych, i osiągnie użycie jednostek DTU 100%, wydajność bazy danych może spowodować spowolnienie czasów odpowiedzi na zapytanie.

**Krótkoterminowe**, rozważ skalowanie bazy danych w górę w celu zapewnienia dodatkowych zasobów lub usunięcie dzierżawców z wielodostępnej bazy danych (przeniesienie ich z bazy danych z wieloma dzierżawcami do autonomicznej bazy danych).

**Dłużej**należy rozważyć optymalizację zapytań lub użycie indeksów w celu poprawy wydajności bazy danych. W zależności od czułości aplikacji do problemów z wydajnością najlepszym rozwiązaniem jest skalowanie bazy danych przed osiągnie 100% jednostek DTU. Użyj alertów do wczesnego ostrzegania.

Można symulować zajętą bazę danych przez zwiększenie obciążenia wytwarzanego przez generator. Wydłużenie liczby dzierżawców i wydłużenie obciążenia wielodostępnej bazy danych bez konieczności zmiany wymagań poszczególnych dzierżawców. Skalowanie w górę bazy danych jest łatwo wykonywane w portalu lub w programie PowerShell. W tym ćwiczeniu użyto portalu.

1. Ustaw *$DemoScenario*  =  **3**, _Generuj obciążenia z dłuższą i bardziej częstą liczbą serii na bazę danych_ , aby zwiększyć intensywność obciążenia zagregowanego w bazie danych bez zmiany szczytowego obciążenia wymaganego przez poszczególne dzierżawy.
1. Naciśnij klawisz **F5**, aby zastosować obciążenie do wszystkich baz danych dzierżaw.
1. Przejdź do bazy danych **tenants1** w Azure Portal.

Monitoruj zwiększone użycie jednostek DTU bazy danych na górnym wykresie. Nowe wyższe obciążenie może zostać rozpoczęte przez kilka minut, ale należy szybko sprawdzić, czy baza danych ma trafiać z maksymalnym użyciem, a w miarę stałego ładowania do nowego wzorca.

1. Aby skalować bazę danych w górę, kliknij pozycję **warstwa cenowa (DTU skalowania)** w bloku ustawienia.
1. Dostosuj ustawienie **jednostek DTU** do **100**. 
1. Kliknij przycisk **Zastosuj** , aby przesłać żądanie do skalowania bazy danych.

Wróć do omówienia **tenants1**  >  **Overview** , aby wyświetlić wykresy monitorowania. Monitoruj wpływ bazy danych na więcej zasobów (choć z kilkoma dzierżawcami i losowo Załaduj, które nie zawsze są łatwo widoczne do momentu uruchomienia przez jakiś czas). Podczas przeglądania wykresów należy pamiętać, że 100% na górnym wykresie teraz reprezentuje 100 DTU, natomiast na niższym wykresie 100% jest nadal 50 DTU.

Bazy danych pozostają w trybie online i są w pełni dostępne podczas całego procesu. Kod aplikacji powinien zawsze być zapisany, aby ponowić próbę porzucenia połączeń i dlatego ponownie nawiąże połączenie z bazą danych.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Inicjowanie obsługi administracyjnej nowej dzierżawy we własnej bazie danych 

Model z wieloma dzierżawcami podzielonej na fragmenty umożliwia wybranie, czy ma zostać zainicjowana Nowa dzierżawa w wielodostępnej bazie danych, obok innych dzierżawców, czy też do samodzielnego udostępniania dzierżawy w bazie danych. Inicjowanie obsługi dzierżawy we własnej bazie danych wiąże się z izolacją w oddzielnej bazie danych, dzięki czemu można zarządzać wydajnością tej dzierżawy niezależnie od innych, przywracać tę dzierżawę niezależnie od innych itd. Możesz na przykład wybrać opcję korzystania z bezpłatnych wersji próbnych i zwykłych klientów w bazie danych z wieloma dzierżawami oraz klientów premium w poszczególnych bazach danych.  W przypadku tworzenia izolowanych baz danych z jedną dzierżawą nadal można zarządzać nimi zbiorczo w puli elastycznej w celu optymalizowania kosztów zasobów.

Jeśli została już zainicjowana Nowa dzierżawa w swojej bazie danych, Pomiń kilka następnych kroków.

1. W **ISE programu PowerShell**Otwórz pozycję... \\ Moduły uczenia \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1*. 
1. Modyfikuj **$TenantName = "Salix Salsa"** i **$VenueType = "odpowiedzialna"**
1. Ustawianie **$Scenario**  =  **2**, _Inicjowanie obsługi dzierżawy w nowej bazie danych z jedną dzierżawą_
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt zainicjuje tę dzierżawę w oddzielnej bazie danych, zarejestruje bazę danych i dzierżawcę przy użyciu wykazu, a następnie otworzy stronę zdarzenia dzierżawy w przeglądarce. Odśwież stronę centrum zdarzeń, aby zobaczyć, że jako miejsce została dodana wartość "Salix Salsa".

## <a name="manage-performance-of-an-individual-database"></a>Zarządzanie wydajnością pojedynczej bazy danych

Jeśli pojedyncza dzierżawa w ramach wielodostępnej bazy danych korzysta z dużego obciążenia, może zależeć od zasobów bazy danych i mieć wpływ na innych dzierżawców w tej samej bazie danych. Jeśli działanie może być kontynuowane przez jakiś czas, dzierżawa może zostać tymczasowo przeniesiona z bazy danych i do własnej bazy danych z jedną dzierżawą. Dzięki temu Dzierżawca może mieć dodatkowe zasoby, których potrzebuje, i w pełni izoluje go od innych dzierżawców.

To ćwiczenie symuluje efekt Salix Salsa, gdy bilety przechodzą do sprzedaży dla popularnego zdarzenia.

1. Otwórz... \\ Skrypt *Demo-PerformanceMonitoringAndManagement.ps1* .
1. Ustaw **$DemoScenario = 5**, _Wygeneruj normalne obciążenie i wysokie obciążenie dla pojedynczej dzierżawy (około 90 jednostek DTU)._
1. Ustaw **$SingleTenantName = Salix Salsa**
1. Wykonaj skrypt, używając klawisza **F5**.

Przejdź do portalu i przejdź do **salixsalsa**  >  **omówienia** salixsalsa, aby wyświetlić wykresy monitorowania. 

## <a name="other-performance-management-patterns"></a>Inne wzorce zarządzania wydajnością

**Skalowanie samoobsługowe dzierżawców**

Ze względu na to, że skalowanie to zadanie łatwo wywoływane za pośrednictwem interfejsu API zarządzania, można łatwo utworzyć możliwość skalowania baz danych dzierżawy do aplikacji dla dzierżawy i zaoferować ją jako funkcję usługi SaaS. Na przykład, można umożliwić dzierżawcom samodzielne administrowanie skalowaniem w górę i w dół, być może bezpośrednio powiązane z ich rozliczeniami!

**Skalowanie bazy danych w górę i w dół zgodnie z harmonogramem w celu dopasowania wzorców użycia**

W przypadku agregowania użycia dzierżawy poniżej przewidywalnych wzorców użycia można użyć Azure Automation do skalowania bazy danych w górę i w dół zgodnie z harmonogramem. Na przykład przeskalować bazę danych w dół po 18:00 i ponownie przed 6:00 w dniach tygodnia, gdy wiadomo, że zaistnieją pewne wymagania dotyczące zasobów.

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Symulowanie użycia dla wielodostępnej bazy danych podzielonej na fragmenty przez uruchomienie dostarczonego generatora obciążenia
> * Monitoruj bazę danych w miarę reagowania na wzrost obciążenia
> * Skalowanie bazy danych w górę w odpowiedzi na zwiększone obciążenie bazy danych
> * Udostępnianie dzierżawy w bazie danych z jedną dzierżawą

## <a name="additional-resources"></a>Dodatkowe zasoby

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Usługa Azure Automation](../../automation/automation-intro.md)