---
title: Samouczek SaaS z wieloma dzierżawcami
description: Inicjowanie obsługi administracyjnej i katalogowanie nowych dzierżawców przy użyciu wzorca aplikacji autonomicznej
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: efee261478cdc8b9b5349ef4c69ab5fc250315c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619462"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Inicjowanie obsługi administracyjnej i katalogowanie nowych dzierżawców przy użyciu wzorca aplikacji dla dzierżawców SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano Inicjowanie obsługi administracyjnej i katalogowanie nowych dzierżawców przy użyciu aplikacji autonomicznej dla każdej dzierżawy SaaS.
Ten artykuł ma dwie główne części:
* Koncepcyjna dyskusja o aprowizacji i katalogach nowych dzierżawców
* Samouczek przedstawiający przykładowy kod programu PowerShell, który wykonuje aprowizacji i katalogi
    * Samouczek korzysta z przykładowej aplikacji SaaS biletów Wingtip, która została przystosowana do wzorca aplikacji autonomicznej dla każdego dzierżawcy.

## <a name="standalone-application-per-tenant-pattern"></a>Aplikacja autonomiczna na wzorzec dzierżawy

Aplikacja autonomiczna na wzorzec dzierżawy jest jednym z kilku wzorców dla wielodostępnych aplikacji SaaS.  W tym wzorcu aplikacja autonomiczna jest obsługiwana dla każdej dzierżawy. Aplikacja składa się ze składników poziomu aplikacji i Azure SQL Database.  Każdą aplikację dzierżawców można wdrożyć w ramach subskrypcji dostawcy.  Platforma Azure oferuje [program zarządzanych aplikacji](https://docs.microsoft.com/azure/managed-applications/overview) , w którym można wdrożyć aplikację w ramach subskrypcji dzierżawcy i zarządzana przez dostawcę w imieniu dzierżawcy.

   ![Wzorzec aplikacji dla dzierżawy](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Podczas wdrażania aplikacji dla dzierżawy, aplikacja i baza danych są obsługiwane w nowej grupie zasobów utworzonej dla dzierżawcy.  Użycie osobnych grup zasobów izoluje zasoby aplikacji poszczególnych dzierżawców i umożliwia ich niezależne zarządzanie. W ramach każdej grupy zasobów każde wystąpienie aplikacji jest skonfigurowane do bezpośredniego dostępu do odpowiedniej bazy danych.  Ten model połączenia różni się od innych wzorców, które używają wykazu do brokera połączeń między aplikacją a bazą danych.  Bez udostępniania zasobów każda baza danych dzierżawy musi być obsługiwana z wystarczającą ilością zasobów, aby obsłużyć obciążenie szczytowe. Ten wzorzec jest używany dla aplikacji SaaS z mniejszą liczbą dzierżawców, w przypadku których istnieje silny nacisk na izolację dzierżawy i mniej nacisk na koszty zasobów.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Używanie wykazu dzierżawy z aplikacją na każdy wzorzec dzierżawy

Chociaż każda aplikacja i baza danych dzierżawy są w pełni izolowane, różne scenariusze zarządzania i analizy mogą działać między dzierżawcami.  Na przykład zastosowanie zmiany schematu dla nowej wersji aplikacji wymaga wprowadzenia zmian w schemacie każdej bazy danych dzierżawy. Scenariusze raportowania i analizy mogą również wymagać dostępu do wszystkich baz danych dzierżawy niezależnie od tego, gdzie są wdrożone.

   ![Wzorzec aplikacji dla dzierżawy](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Katalog dzierżawców zawiera mapowanie między identyfikatorem dzierżawy i bazą danych dzierżawy, co pozwala na rozpoznanie identyfikatora na serwerze i o nazwie bazy danych.  W aplikacji Wingtip SaaS identyfikator dzierżawy jest obliczany jako skrót nazwy dzierżawy, chociaż można użyć innych schematów.  Gdy aplikacje autonomiczne nie potrzebują wykazu do zarządzania połączeniami, można użyć wykazu do określania zakresu innych akcji do zestawu baz danych dzierżaw. Na przykład elastyczne zapytanie może korzystać z wykazu w celu określenia zestawu baz danych, dla których są dystrybuowane zapytania na potrzeby raportowania między dzierżawcami.

## <a name="elastic-database-client-library"></a>Elastic Database Biblioteka kliencka

W przykładowej aplikacji Wingtip katalog jest implementowany przez funkcje zarządzania fragmentu w [bibliotece klienta Elastic Database](elastic-database-client-library.md) (Biblioteka edcl).  Biblioteka umożliwia aplikacji Tworzenie i używanie mapy fragmentu przechowywanej w bazie danych oraz zarządzanie nią. W przykładzie biletów Wingtip katalog jest przechowywany w bazie danych *katalogu dzierżawcy* .  Fragmentu mapuje klucz dzierżawy do fragmentu (bazy danych), w którym są przechowywane dane tej dzierżawy.  Funkcje Biblioteka EDCL zarządzają *globalną mapą fragmentu* przechowywaną w tabelach w bazie danych *wykazu dzierżawców* i *lokalną mapę fragmentu* przechowywaną w każdym fragmentu.

Funkcje Biblioteka EDCL można wywoływać z aplikacji lub skryptów programu PowerShell w celu tworzenia wpisów na mapie fragmentu i zarządzania nimi. Inne funkcje Biblioteka EDCL umożliwiają pobranie zestawu fragmentów lub nawiązanie połączenia z poprawną bazą danych dla danego klucza dzierżawy.

> [!IMPORTANT]
> Nie należy edytować danych w bazie danych wykazu ani lokalnej mapie fragmentu w bazach danych dzierżawy. Bezpośrednie aktualizacje nie są obsługiwane z powodu wysokiego ryzyka uszkodzenia danych. Zamiast tego należy edytować dane mapowania przy użyciu tylko interfejsów API Biblioteka EDCL.

## <a name="tenant-provisioning"></a>Inicjowanie obsługi dzierżawy

Każda dzierżawa wymaga nowej grupy zasobów platformy Azure, która musi zostać utworzona przed zainicjowaniem obsługi zasobów w ramach tej usługi. Gdy grupa zasobów istnieje, można użyć szablonu zarządzania zasobami platformy Azure do wdrożenia składników aplikacji i bazy danych, a następnie skonfigurowania połączenia z bazą danych. Aby zainicjować schemat bazy danych, szablon może importować plik BACPAC.  Alternatywnie można utworzyć bazę danych jako kopię bazy danych "template".  Baza danych jest następnie aktualizowana przy użyciu początkowych danych o miejscu i zarejestrowanych w wykazie.

## <a name="tutorial"></a>Samouczek

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Inicjowanie obsługi administracyjnej katalogu
* Rejestrowanie przykładowych baz danych dzierżaw, które zostały wdrożone wcześniej w wykazie
* Zapewnij dodatkową dzierżawę i zarejestruj ją w wykazie

Szablon Azure Resource Manager służy do wdrażania i konfigurowania aplikacji, tworzenia bazy danych dzierżawy, a następnie do zaimportowania pliku BACPAC. Żądanie importu może być dodane do kolejki przez kilka minut przed podjęciem działania.

Na końcu tego samouczka masz zestaw autonomicznych aplikacji dzierżawców z każdą bazą danych zarejestrowaną w wykazie.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Zostaną wdrożone trzy przykładowe aplikacje dzierżawy. Aby wdrożyć te aplikacje w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie wzorca aplikacji autonomicznej Wingtip biletów SaaS](../../sql-database/saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Inicjowanie obsługi administracyjnej katalogu

W tym zadaniu dowiesz się, jak udostępnić katalog używany do rejestracji wszystkich baz danych dzierżaw. Wykonasz następujące zadania:

* **Zainicjuj obsługę administracyjną bazy danych katalogu** przy użyciu szablonu zarządzania zasobami platformy Azure. Baza danych została zainicjowana przez zaimportowanie pliku BACPAC.
* **Zarejestruj przykładowe wdrożone wcześniej aplikacje dzierżawców** .  Każda dzierżawa jest zarejestrowana przy użyciu klucza złożonego ze skrótu nazwy dzierżawy.  Nazwa dzierżawy jest również przechowywana w tabeli rozszerzeń w wykazie.

1. W programie PowerShell ISE Otwórz *..\Learning Modules\UserConfig.PSM* i zaktualizuj **\<user\>** wartość do wartości użytej podczas wdrażania trzech przykładowych aplikacji.  **Zapisz plik**.
1. W programie PowerShell ISE Otwórz *..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 1**. Wdróż katalog dzierżawy i zarejestruj wstępnie zdefiniowane dzierżawy.

1. Dodaj punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu, `& $PSScriptRoot\New-Catalog.ps1` a następnie naciśnij klawisz **F9**.

    ![Ustawianie punktu przerwania na potrzeby śledzenia](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Uruchom skrypt, naciskając klawisz **F5**.
1.  Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij klawisz **F11** , aby przejść do skryptu New-Catalog.ps1.
1.  Śledź wykonywanie skryptu przy użyciu opcji menu Debuguj, F10 i F11, aby przekroczyć lub użyć funkcji o nazwie.
    *   Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z skryptami programu PowerShell i ich debugowania](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po zakończeniu działania skryptu katalog będzie istnieć i wszystkie przykładowe dzierżawy zostaną zarejestrowane.

Teraz przyjrzyj się utworzonym zasobom.

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejrzyj grupy zasobów.  Otwórz grupę zasobów **Wingtip-sa-Catalog \<user\> ** i zanotuj serwer wykazu i bazę danych.
1. Otwórz bazę danych w portalu i wybierz pozycję *Eksplorator danych* z menu po lewej stronie.  Kliknij polecenie Zaloguj, a następnie wprowadź hasło = **P \@ ssword1**.


1. Eksploruj schemat bazy danych *tenantcatalog* .
   * Wszystkie obiekty w `__ShardManagement` schemacie są udostępniane przez Elastic Databaseą bibliotekę kliencką.
   * `Tenants`Tabela i `TenantsExtended` Widok są rozszerzeniami dodawanymi w przykładzie, który pokazuje, jak można rozszerzać katalog w celu zapewnienia dodatkowej wartości.
1. Uruchom zapytanie, `SELECT * FROM dbo.TenantsExtended` .

   ![Eksplorator danych](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Alternatywą dla korzystania z Eksplorator danych można nawiązać połączenie z bazą danych z SQL Server Management Studio. W tym celu należy nawiązać połączenie z serwerem Wingtip-


    Należy pamiętać, że nie należy edytować danych bezpośrednio w wykazie — zawsze używaj interfejsów API zarządzania fragmentu.

## <a name="provision-a-new-tenant-application"></a>Inicjowanie obsługi administracyjnej nowej aplikacji dzierżawy

W tym zadaniu dowiesz się, jak zainicjować obsługę administracyjną pojedynczej aplikacji dzierżawy. Wykonasz następujące zadania:

* **Utwórz nową grupę zasobów** dla dzierżawy.
* **Zainicjuj obsługę administracyjną aplikacji i bazy danych** w nowej grupie zasobów za pomocą szablonu usługi Azure Resource Management.  Ta akcja obejmuje zainicjowanie bazy danych ze wspólnym schematem i danymi referencyjnymi przez zaimportowanie pliku BACPAC.
* **Zainicjuj bazę danych z podstawowymi informacjami o dzierżawie**. Ta akcja obejmuje określenie typu miejsca, który określa fotografię używaną jako tło w witrynie sieci Web zdarzeń.
* **Zarejestruj bazę danych w bazie danych wykazu**.

1. W programie PowerShell ISE Otwórz polecenie *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 2**. Wdróż katalog dzierżawy i zarejestruj wstępnie zdefiniowane dzierżawy

1. Dodaj punkt przerwania w skrypcie, umieszczając kursor w dowolnym miejscu w wierszu 49, `& $PSScriptRoot\New-TenantApp.ps1` a następnie naciśnij klawisz **F9**.
1. Uruchom skrypt, naciskając klawisz **F5**.
1.  Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij klawisz **F11** , aby przejść do skryptu New-Catalog.ps1.
1.  Śledź wykonywanie skryptu przy użyciu opcji menu Debuguj, F10 i F11, aby przekroczyć lub użyć funkcji o nazwie.

Po zainicjowaniu obsługi dzierżawy zostanie otwarta witryna sieci Web zdarzenia nowej dzierżawy.

   ![wyścig z czerwonym klonem](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Następnie można sprawdzić nowe zasoby utworzone w Azure Portal.

   ![zasoby wyścigów z czerwonym klonem](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Aby zatrzymać rozliczanie, Usuń grupy zasobów

Po zakończeniu eksplorowania przykładu usuń wszystkie utworzone przez siebie grupy zasobów, aby zatrzymać powiązane rozliczenia.

## <a name="additional-resources"></a>Zasoby dodatkowe

- Aby dowiedzieć się więcej o wielodostępnych aplikacjach baz danych SaaS, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikację autonomiczną SaaS biletów Wingtip.
> * Informacje o serwerach i bazach danych, które tworzą aplikację.
> * Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Można dowiedzieć się, w jaki sposób wykaz jest używany do obsługi różnych scenariuszy obejmujących wiele dzierżawców przy użyciu wersji bazy danych na dzierżawcę [aplikacji Wingtip biletów SaaS](../../sql-database/saas-dbpertenant-wingtip-app-overview.md).
