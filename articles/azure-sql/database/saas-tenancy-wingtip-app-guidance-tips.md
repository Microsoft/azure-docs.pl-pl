---
title: Przykład aplikacji z wieloma dzierżawcami — Wingtip SaaS
description: Zawiera instrukcje i wskazówki dotyczące instalowania i uruchamiania przykładowej aplikacji wielodostępnej, która używa Azure SQL Database, bilety Wingtip SaaS.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 4c3a141525f650d6e254f79ce9e3396f2c35a95f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84051885"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Ogólne wskazówki dotyczące pracy z Wingtip bilety przykładowe aplikacje SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera ogólne wskazówki dotyczące uruchamiania Wingtip biletów przykładowych aplikacji SaaS, które używają Azure SQL Database.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Pobieranie i odblokowywanie Wingtip biletów SaaS

Zawartość wykonywalna (skrypty, biblioteki dll) może być blokowana przez system Windows, gdy pliki zip są pobierane ze źródła zewnętrznego i wyodrębniane. Podczas wyodrębniania skryptów z pliku zip **wykonaj poniższe kroki, aby odblokować plik. zip przed wyodrębnieniem**. Zapewnia to możliwość uruchamiania skryptów.

1. Przejdź do repozytorium Wingtip SaaS w witrynie GitHub, aby uzyskać wzorzec dzierżawy bazy danych, który chcesz eksplorować:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kliknij pozycję **Klonuj lub Pobierz**.
3. Kliknij pozycję **Pobierz kod pocztowy** i Zapisz plik.
4. Kliknij prawym przyciskiem myszy plik zip, a następnie wybierz polecenie **Właściwości**. Nazwa pliku zip będzie odpowiadać nazwie repozytorium. uprzedni. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na karcie **Ogólne** wybierz opcję **Odblokuj**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty znajdują się w *... \\ Folder modułów szkoleniowych* .


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Praca ze skryptami programu PowerShell dla biletów Wingtip

Aby maksymalnie wykorzystać próbkę, którą należy szczegółowe do dostarczonych skryptów. Używaj punktów przerwania i przechodzenia przez skrypty podczas ich wykonywania i Badaj, jak są implementowane różne wzorce SaaS. Aby łatwo zapoznać się z udostępnionymi skryptami i modułami, zalecamy korzystanie z programu [POWERSHELL ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualizowanie pliku konfiguracji dla danego wdrożenia

Edytuj plik **userconfig. PSM1** przy użyciu grupy zasobów i wartości użytkownika ustawionej podczas wdrażania:

1. Otwórz *ISE programu PowerShell* i Załaduj... \\ Moduły uczenia \\ *userconfig. PSM1*
2. Zaktualizuj *ResourceGroupName* i *nadaj nazwę* określonym wartościom dla danego wdrożenia (tylko wiersze 10 i 11).
3. Zapisz zmiany!

Ustawienie tych wartości w tym miejscu po prostu pozwala uniknąć konieczności aktualizowania tych wartości specyficznych dla danego wdrożenia w każdym skrypcie.

### <a name="execute-the-scripts-by-pressing-f5"></a>Wykonaj skrypty, naciskając klawisz F5

Kilka skryptów używa *$PSScriptRoot* do nawigowania po folderach, a *$PSScriptRoot* jest obliczana tylko wtedy, gdy skrypty są wykonywane przez naciśnięcie klawisza **F5**.Wyróżnianie i uruchamianie zaznaczenia (**F8**) może powodować błędy, dlatego należy nacisnąć klawisz **F5** w przypadku uruchamiania skryptów.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Przechodzenie przez skrypty w celu zapoznania się z implementacją

Najlepszym sposobem na zrozumienie skryptów jest przechodzenie przez siebie, aby zobaczyć, co robią. Zapoznaj się z dołączonymi skryptami **demonstracyjnymi** , które zawierają łatwy do obserwowania przepływ pracy wysokiego poziomu. W skryptach **demonstracyjnych** wyświetlane są kroki wymagane do wykonania każdego zadania, dlatego należy ustawić punkty przerwania i przegłębić się w poszczególne wywołania, aby zobaczyć szczegóły implementacji różnych wzorców SaaS.

Porady dotyczące eksplorowania i przechodzenia za pośrednictwem skryptów programu PowerShell:

- Otwórz **Pokaz —** skrypty w programie PowerShell ISE.
- Wykonaj lub Kontynuuj z **F5** (użycie **klawisza F8** nie jest zalecane, ponieważ *$PSScriptRoot* nie jest oceniane podczas uruchamiania wybranych skryptów).
- Umieść punkty przerwania, klikając lub zaznaczając wiersz i naciskając klawisz **F9**.
- Pomiń wywołanie funkcji lub skryptu, używając klawisza **F10**.
- Wejdź do wywoływanej funkcji lub skryptu, używając klawisza **F11**.
- Wyjdź z bieżącego wywołania funkcji lub skryptu, używając kombinacji klawiszy **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Badanie schematu bazy danych i wykonywanie zapytań SQL za pomocą aplikacji SSMS

Użyj [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) , aby nawiązać połączenie i przeglądać serwery i bazy danych aplikacji.

Wdrożenie początkowo ma dzierżawy i serwery wykazu, z którymi można nawiązać połączenie. Nazwy serwerów są zależne od wzorca dzierżawy bazy danych (patrz poniżej).

   - **Aplikacja autonomiczna:** serwery dla każdej dzierżawy (np. *contosoconcerthall — &lt; Serwer &gt; użytkownika* ) i *wykaz — &lt; użytkownik &gt; *
   - **Baza danych na dzierżawcę:** *tenants1-DPT- &lt; User &gt; * i *Catalog-DPT- &lt; User &gt; * Servers
   - **Baza danych z wieloma dzierżawcami:** *tenants1-MT- &lt; User &gt; * i *Catalog-MT--------- &lt; Users &gt; *

Aby zapewnić pomyślne połączenie demonstracyjne, wszystkie serwery mają [regułę zapory](firewall-configure.md) zezwalającą na wszystkie adresy IP w programie.


1. Otwórz narzędzie *SSMS* i Połącz się z dzierżawcami. Nazwa serwera zależy od wybranego wzorca dzierżawy bazy danych (patrz poniżej w przypadku określonych):
    - **Aplikacja autonomiczna:** serwery indywidualnych dzierżawców (np. *contosoconcerthall — &lt; User &gt; . Database.Windows.NET*)
    - **Baza danych na dzierżawcę:** *tenants1-DPT- &lt; User &gt; . Database.Windows.NET*
    - **Baza danych z wieloma dzierżawcami:** *tenants1-MT- &lt; User &gt; . Database.Windows.NET*
2. Kliknij pozycję **Połącz**  >  **aparat bazy danych..**.:

   ![serwer wykazu](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Poświadczenia demonstracyjne: login = *Developer*, Password = *P \@ ssword1*

    Na poniższej ilustracji przedstawiono dane logowania dla wzorca *dzierżawy* .
    ![połączenia](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Powtórz kroki 2-3 i nawiąż połączenie z serwerem wykazu (zobacz poniżej, aby poznać określone nazwy serwera na podstawie wybranego wzorca dzierżawy bazy danych)
    - **Aplikacja autonomiczna:** *Catalog-sa- &lt; User &gt; . Database.Windows.NET*
    - **Baza danych na dzierżawcę:** *Catalog-DPT- &lt; User &gt; . Database.Windows.NET*
    - **Baza danych z wieloma dzierżawcami:** *Catalog-MT- &lt; User &gt; . Database.Windows.NET*


Po pomyślnym nawiązaniu połączenia powinny być widoczne wszystkie serwery. Lista baz danych może się różnić, w zależności od dzierżawców, których zainicjowano.

Na poniższej ilustracji przedstawiono logowanie do *bazy danych na wzorzec dzierżawy* .

![eksplorator obiektów](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Następne kroki
- [Wdróż aplikację autonomiczną Wingtip biletów SaaS](../../sql-database/saas-standaloneapp-get-started-deploy.md)
- [Wdróż bazę danych SaaS biletów Wingtip na aplikację dzierżawców](../../sql-database/saas-dbpertenant-get-started-deploy.md)
- [Wdrażanie Wingtip biletów SaaS aplikacji bazy danych z wieloma dzierżawcami](../../sql-database/saas-multitenantdb-get-started-deploy.md)

