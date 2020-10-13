---
title: Inicjowanie obsługi administracyjnej nowych dzierżawców w aplikacji wielodostępnej
description: Dowiedz się, jak udostępniać i katalogować nowe dzierżawy w Azure SQL Database wielodostępnej aplikacji SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: bc649551986190f944e3225ff0914d091acd3f88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619699"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Dowiedz się, jak zainicjować obsługę nowych dzierżawców i zarejestrować je w wykazie
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku dowiesz się, jak udostępniać i wykazać wzorce SaaS. Dowiesz się również, jak są one zaimplementowane w Wingtip bilety SaaS bazy danych dla dzierżawców. Utwórz i zainicjuj nowe bazy danych dzierżawy i zarejestruj je w katalogu dzierżawy aplikacji. Katalog jest bazą danych, która zachowuje mapowanie między wieloma dzierżawcami i danymi aplikacji SaaS. Katalog odgrywa ważną rolę w kierowaniu żądań aplikacji i zarządzania do prawidłowej bazy danych.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Inicjowanie obsługi administracyjnej jednej nowej dzierżawy.
> * Zainicjuj obsługę partii dodatkowych dzierżawców.


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację Wingtip bilety SaaS bazy danych dla dzierżawców. Aby wdrożyć ją w czasie krótszym niż pięć minut, zobacz [wdrażanie i eksplorowanie Wingtip biletów SaaS bazy danych dla dzierżawców](../../sql-database/saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Wprowadzenie do wzorca wykazu SaaS

W przypadku aplikacji SaaS wielodostępnej w bazie danych należy wiedzieć, gdzie są przechowywane informacje dotyczące poszczególnych dzierżawców. W przypadku wzorca wykazu SaaS baza danych wykazu jest używana do przechowywania mapowania między poszczególnymi dzierżawcami i bazą danych, w której są przechowywane dane. Ten wzorzec ma zastosowanie, gdy dane dzierżawy są dystrybuowane między wieloma bazami danych.

Każda dzierżawa jest identyfikowana przez klucz w wykazie, który jest mapowany do lokalizacji bazy danych. W aplikacji bilety Wingtip klucz jest tworzony na podstawie skrótu nazwy dzierżawcy. Ten schemat umożliwia aplikacji utworzenie klucza z nazwy dzierżawy zawartej w adresie URL aplikacji. Można używać innych schematów kluczy dzierżawy.

Wykaz umożliwia zmianę nazwy lub lokalizacji bazy danych przy minimalnym wpływie na aplikację. W wielodostępnym modelu bazy danych ta funkcja umożliwia również przechodzenie dzierżawy między bazami danych. Wykazu można także użyć, aby wskazać, czy dzierżawca lub baza danych jest w trybie offline w celu konserwacji lub wykonywania innych czynności. Ta funkcja została zbadana w [samouczku przywracanie pojedynczej dzierżawy](saas-dbpertenant-restore-single-tenant.md).

W wykazie mogą być również przechowywane dodatkowe metadane dzierżawy lub bazy danych, takie jak wersja schematu, plan usługi lub umowy SLA udostępniane dzierżawcom. W wykazie mogą być przechowywane inne informacje, które umożliwiają zarządzanie aplikacjami, obsługę klienta lub DevOps.

Po przekroczeniu aplikacji SaaS katalog może włączać narzędzia bazy danych. W przypadku Wingtip biletów SaaS bazy danych na dzierżawcę katalog jest używany do włączenia zapytania między dzierżawcami, które jest omówione w [samouczku sprawozdawczości ad hoc](saas-tenancy-cross-tenant-reporting.md). Zarządzanie zadaniami między bazami danych jest dostępne w samouczkach dotyczących [zarządzania schematami](saas-tenancy-schema-management.md) i [analizy dzierżawców](saas-tenancy-tenant-analytics.md) .

W przykładowych Wingtip bilety SaaS wykaz jest implementowany przy użyciu funkcji zarządzania fragmentu w [bibliotece klienta Elastic Database (Biblioteka edcl)](elastic-database-client-library.md). Biblioteka EDCL jest dostępna w języku Java i .NET Framework. Biblioteka EDCL umożliwia aplikacji Tworzenie i używanie mapy fragmentu z kopią zapasową bazy danych oraz zarządzanie nią.

Mapa fragmentu zawiera listę fragmentów (baz danych) i mapowanie między kluczami (dzierżawcami) i fragmentów. Funkcje Biblioteka EDCL są używane podczas aprowizacji dzierżawy do tworzenia wpisów na mapie fragmentu. Są one używane w czasie wykonywania przez aplikacje do nawiązywania połączenia z poprawną bazą danych. Biblioteka EDCL buforuje informacje o połączeniu, aby zminimalizować ruch do bazy danych wykazu i przyspieszyć aplikację.

> [!IMPORTANT]
> Dane mapowania są dostępne w bazie danych wykazu, ale *nie można ich edytować*. Edytuj dane mapowania, używając tylko Elastic Database interfejsów API biblioteki klienta. Bezpośrednie manipulowanie ryzykiem związanym z danymi mapowania uszkodzonym katalog i nie jest obsługiwane.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Wprowadzenie do wzorca aprowizacji SaaS

Po dodaniu nowej dzierżawy w aplikacji SaaS, która używa modelu bazy danych o pojedynczej dzierżawie, musisz zainicjować obsługę administracyjną nowej bazy danych dzierżawy. Baza danych musi zostać utworzona w odpowiedniej lokalizacji i warstwie usług. Należy ją również zainicjować z odpowiednimi schematami i danymi referencyjnymi. I musi być zarejestrowane w wykazie przy użyciu odpowiedniego klucza dzierżawy.

Można użyć różnych metod inicjowania obsługi bazy danych. Można wykonywać skrypty SQL, wdrażać BACPAC lub kopiować bazę danych szablonów.

Inicjowanie obsługi bazy danych musi być częścią strategii zarządzania schematem. Należy upewnić się, że są obsługiwane nowe bazy danych z najnowszym schematem. Ten wymóg jest omawiany w [samouczku zarządzania schematami](saas-tenancy-schema-management.md).

Aplikacja do obsługi biletów Wingtip w bazie danych dla dzierżawców udostępnia nowe dzierżawy, kopiując bazę danych szablonów o nazwie _basetenantdb_, która jest wdrożona na serwerze wykazu. Inicjowanie obsługi można zintegrować z aplikacją w ramach środowiska rejestracji. Może być również obsługiwane w trybie offline za pomocą skryptów. Ten samouczek eksploruje Inicjowanie obsługi przy użyciu programu PowerShell.

Skrypty aprowizacji kopiują bazę danych _basetenantdb_ , aby utworzyć nową bazę danych dzierżawcy w puli elastycznej. Baza danych dzierżawy jest tworzona na serwerze dzierżawy zmapowanym na alias DNS _newtenant_ . Ten alias zachowuje odwołanie do serwera służącego do aprowizacji nowych dzierżawców i jest aktualizowane w celu wskazywania na serwerze dzierżawy odzyskiwania w samouczkach odzyskiwania po awarii ([Dr przy użyciu funkcji georestore](../../sql-database/saas-dbpertenant-dr-geo-restore.md)przy [użyciu funkcji georeplikacji, dr](../../sql-database/saas-dbpertenant-dr-geo-replication.md)). Skrypty zainicjują bazę danych z informacjami specyficznymi dla dzierżawy i rejestrują je na mapie fragmentu wykazu. Bazy danych dzierżawy są nadawane nazw na podstawie nazwy dzierżawy. Ten schemat nazewnictwa nie jest istotną częścią wzorca. Katalog mapuje klucz dzierżawy na nazwę bazy danych, więc można użyć dowolnej konwencji nazewnictwa.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz bilety Wingtip SaaS dla skryptów aplikacji dla dzierżawców

Wingtip bilety SaaS skrypty i kod źródłowy aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Szczegółowy przewodnik po aprowizacji i wykazie

Aby zrozumieć, w jaki sposób aplikacja biletów Wingtip implementuje nową obsługę dzierżawy, Dodaj punkt przerwania i postępuj zgodnie z przepływem pracy podczas aprowizacji dzierżawy.

1. W ISE programu PowerShell Otwórz pozycję... \\ Moduły edukacyjne \\ ProvisionAndCatalog \\ _Demo-ProvisionAndCatalog.ps1_ i ustawiają następujące parametry:

   * **$TenantName** = nazwa nowego miejsca (na przykład *Bushwillow Blues*).
   * **$VenueType** = jeden ze wstępnie zdefiniowanych typów miejsc: _Blues, ClassicalMusic, odpowiedzialna, Jazz, judo, wyścigi mechaniczne, Multipurpose, Opera, ROCKMUSIC, piłka nożna_.
   * **$DemoScenario**  =  **1**, *Zainicjuj obsługę pojedynczej dzierżawy*.

2. Aby dodać punkt przerwania, umieść kursor w dowolnym miejscu w wierszu informującym o *nowym dzierżawie*. Następnie naciśnij klawisz F9.

   ![Zrzut ekranu przedstawia skrypt z New-Tenant wyróżniony do dodawania punktu przerwania.](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Aby uruchomić skrypt, naciśnij klawisz F5.

4. Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij klawisz F11, aby wkroczyć do kodu.

   ![Debugowanie](./media/saas-dbpertenant-provision-and-catalog/debug.png)



Śledź wykonywanie skryptu przy użyciu opcji menu **Debuguj** . Naciśnij klawisz F10 i F11, aby przekroczyć lub do wywołanych funkcji. Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z skryptami programu PowerShell i ich debugowania](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Nie musisz jawnie obserwować tego przepływu pracy. Wyjaśniono, jak debugować skrypt.

* **Zaimportuj moduł CatalogAndDatabaseManagement. PSM1.** Zapewnia wykaz i abstrakcję poziomu dzierżawy za pośrednictwem funkcji [zarządzania fragmentu](elastic-scale-shard-map-management.md) . Ten moduł hermetyzuje wiele wzorców wykazu i jest cenny.
* **Zaimportuj moduł SubscriptionManagement. PSM1.** Zawiera funkcje logowania do platformy Azure i wybierania subskrypcji platformy Azure, z którą chcesz korzystać.
* **Pobierz szczegóły konfiguracji.** Wkrocz do Get-Configuration za pomocą klawisza F11 i zobacz, jak określono konfigurację aplikacji. Nazwy zasobów i inne wartości specyficzne dla aplikacji są definiowane w tym miejscu. Nie zmieniaj tych wartości, dopóki nie znasz skryptów.
* **Pobierz obiekt katalogu.** Wkrocz do Get-Catalog, który składa się z obiektu katalogu, który jest używany w skrypcie wyższego poziomu. Ta funkcja korzysta z funkcji zarządzania fragmentu, które są importowane z **AzureShardManagement. PSM1**. Obiekt wykazu składa się z następujących elementów:

   * $catalogServerFullyQualifiedName jest konstruowany przy użyciu standardowego trzonu i nazwy użytkownika: _Catalog- \<user\> . Database. Windows .NET_.
   * Obiekt $catalogDatabaseName jest pobierany z konfiguracji: *tenantcatalog*.
   * Obiekt $shardMapManager jest inicjowany z bazy danych wykazu.
   * Obiekt $shardMap jest inicjowany z mapy fragmentów _tenantcatalog_ w bazie danych wykazu. Obiekt wykazu jest tworzony i zwracany. Jest on używany w skrypcie wyższego poziomu.
* **Oblicz nowy klucz dzierżawy.** Funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
* **Sprawdź, czy klucz dzierżawy już istnieje.** Wykaz zostanie sprawdzony w celu upewnienia się, że klucz jest dostępny.
* **Aprowizacja bazy danych jest wykonywana przy użyciu obiektu New-TenantDatabase.** Użyj klawisza F11, aby przejść do procedury obsługi bazy danych przy użyciu [szablonu Azure Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    Nazwa bazy danych jest tworzona na podstawie nazwy dzierżawy, aby było jasne, który fragment należy do której dzierżawy. Można również użyć innych konwencji nazewnictwa baz danych. Szablon Menedżer zasobów tworzy bazę danych dzierżawy przez skopiowanie bazy danych szablonów (_baseTenantDB_) na serwerze wykazu. Alternatywnie można utworzyć bazę danych i zainicjować ją przez zaimportowanie BACPAC. Lub można wykonać skrypt inicjujący ze dobrze znanej lokalizacji.

    Szablon Menedżer zasobów znajduje się w folderze. ..\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **Baza danych dzierżawy jest już zainicjowana.** Dodawane są nazwy miejsca (dzierżawy) i typu miejsca. Możesz również wykonać inne inicjalizacje tutaj.

* **Baza danych dzierżawy jest zarejestrowana w wykazie.** Jest ona zarejestrowana za pomocą polecenia *Add-TenantDatabaseToCatalog* przy użyciu klucza dzierżawy. Użyj klawisza F11, aby wejść do funkcji:

    * Baza danych wykazu jest dodawana do mapy fragmentów (listy znanych baz danych).
    * Tworzone jest mapowanie łączące wartość klucza z fragmentem.
    * Dodatkowe metadane dotyczące dzierżawy (nazwa miejsca) są dodawane do tabeli dzierżawców w katalogu. Tabela dzierżawców nie jest częścią schematu fragmentu Management i nie jest instalowana przez Biblioteka EDCL. W tej tabeli przedstawiono sposób rozszerzania bazy danych wykazu w celu obsługi dodatkowych danych specyficznych dla aplikacji.


Po zakończeniu aprowizacji, wykonanie powraca do oryginalnego skryptu *demonstracyjnego ProvisionAndCatalog* . Zostanie otwarta strona **zdarzenia** dla nowej dzierżawy w przeglądarce.

   ![Strona zdarzeń](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Inicjowanie obsługi partii dzierżawców

W tym ćwiczeniu zainicjujemy partię 17 dzierżawców. Zalecamy udostępnienie tej partii dzierżawców przed rozpoczęciem innych biletów Wingtip SaaSych samouczków bazy danych dla dzierżawców. Istnieje więcej niż kilka baz danych, z którymi można współpracować.

1. W ISE programu PowerShell Otwórz pozycję... \\ Moduły uczenia \\ ProvisionAndCatalog \\ *Demo-ProvisionAndCatalog.ps1*. Zmień parametr *$DemoScenario* na 3:

   * **$DemoScenario**  =  **3**, *Zainicjuj obsługę partii dzierżawców*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt wdroży partię dodatkowych dzierżaw. Używa [szablonu Azure Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) , który kontroluje zadanie wsadowe i deleguje Inicjowanie obsługi każdej bazy danych w połączonym szablonie. Ten sposób użycia szablonów umożliwia aplikacji Azure Resource Manager pełnienie roli brokera w procesie aprowizacji przy użyciu skryptu. Szablony udostępniają bazy danych równolegle i obsługują ponowne próby w razie konieczności. Skrypt jest idempotentne, więc jeśli nie powiedzie się lub zostanie zatrzymany z dowolnego powodu, uruchom go ponownie.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Sprawdź partię pomyślnie wdrożonych dzierżawców

* W [Azure Portal](https://portal.azure.com)przejdź do listy serwerów i Otwórz serwer *tenants1* . Wybierz pozycję **bazy danych SQL**i sprawdź, czy na liście znajduje się partia dodatkowych baz danych.

   ![Lista baz danych](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

Inne wzorce aprowizacji, które nie zostały uwzględnione w tym samouczku:

**Wstępne Inicjowanie obsługi administracyjnej baz danych**: wzorzec wstępnej aprowizacji wykorzystuje fakt, że bazy danych w puli elastycznej nie dodawaj dodatkowych kosztów. Opłaty są naliczane za pulę elastyczną, a nie bazy danych. Bezczynne bazy danych nie zużywają żadnych zasobów. Przed zainicjowaniem obsługi administracyjnej baz danych w puli i przydzieleniu ich w razie potrzeby można skrócić czas dodawania dzierżawców. Liczba wstępnie zainicjowanych baz danych można dostosować w miarę potrzeb, aby zachować bufor odpowiedni dla przewidywanego tempa aprowizacji.

**Automatyczne Inicjowanie obsługi**: w przypadku automatycznego inicjowania obsługi administracyjnej usługa aprowizacji automatycznie inicjuje obsługę administracyjną serwerów, pul i baz danych. Jeśli chcesz, możesz dołączyć bazy danych wstępnej aprowizacji do pul elastycznych. W przypadku zlikwidowania i usunięcia baz danych luki w pulach elastycznych mogą być wypełniane przez usługę aprowizacji. Takie usługi mogą być proste lub złożone, takie jak obsługa aprowizacji wielu lokalizacje geograficzne i Konfigurowanie replikacji geograficznej na potrzeby odzyskiwania po awarii.

Ze wzorcem autoaprowizacji aplikacja kliencka lub skrypt przesyła żądanie aprowizacji do kolejki w celu przetworzenia przez usługę aprowizacji. Następnie sonduje usługę, aby określić zakończenie. Jeśli jest używane wstępne Inicjowanie obsługi, żądania są obsługiwane szybko. Usługa inicjuje zastępowanie zastępczej bazy danych w tle.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Inicjowanie obsługi administracyjnej jednej nowej dzierżawy.
> * Zainicjuj obsługę partii dodatkowych dzierżawców.
> * Przejdź do szczegółów dotyczących aprowizacji i zarejestrowania się w wykazie.

Wypróbuj [Samouczek dotyczący monitorowania wydajności](../../sql-database/saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczki, które kompilują w Wingtip bilety SaaS bazy danych dla dzierżawców](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteka kliencka Elastic Database](elastic-database-client-library.md)
* [Debugowanie skryptów w Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
