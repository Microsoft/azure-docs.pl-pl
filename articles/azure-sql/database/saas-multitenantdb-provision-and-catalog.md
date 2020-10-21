---
title: Inicjowanie obsługi administracyjnej w ramach usługi SaaS z wieloma dzierżawcami
description: Dowiedz się, jak udostępniać i katalogować nowe dzierżawy w Azure SQL Database aplikacji SaaS z wieloma dzierżawcami
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
ms.openlocfilehash: 0b381e2dbdbfd30d10f37637b30bcdfbab8ed99a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331944"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Udostępnianie i katalogowanie nowych dzierżawców w aplikacji SaaS przy użyciu wielodostępnego podzielonej na fragmenty Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano aprowizacji i katalogowanie nowych dzierżawców, w modelu lub wzorcu *bazy danych podzielonej na fragmenty z wieloma dzierżawcami* .

Ten artykuł ma dwie główne części:

- [Koncepcyjna dyskusja](#goto_2_conceptual) o aprowizacji i katalogach nowych dzierżawców.

- [Samouczek](#goto_1_tutorial) , w którym wyróżniono kod skryptu programu PowerShell, który wykonuje aprowizacji i katalogi.
  - W tym samouczku jest stosowana aplikacja Wingtip bilety SaaS, dostosowana do wzorca bazy danych podzielonej na fragmenty z wieloma dzierżawcami.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Wzorzec bazy danych

W tej sekcji i kilku dalszych krokach omówiono koncepcje wzorca bazy danych podzielonej na fragmenty z wieloma dzierżawcami.

W tym modelu podzielonej na fragmenty z wieloma dzierżawcami schematy tabel wewnątrz każdej bazy danych obejmują klucz dzierżawy w kluczu podstawowym tabel, które przechowują dane dzierżawy. Klucz dzierżawy umożliwia każdej osobie pojedynczej bazie danych przechowywanie wartości 0, 1 lub wielu dzierżawców. Korzystanie z baz danych podzielonej na fragmenty ułatwia systemowi aplikacji obsługę bardzo dużej liczby dzierżawców. Wszystkie dane dla jednej dzierżawy są przechowywane w jednej bazie danych. Duża liczba dzierżawców jest dystrybuowana w wielu bazach danych podzielonej na fragmenty. Baza danych wykazu przechowuje mapowanie poszczególnych dzierżawców do swojej bazy danych.

#### <a name="isolation-versus-lower-cost"></a>Izolacja w porównaniu z niższym kosztem

Dzierżawa, która ma bazę danych wszystkie same same zalety izolacji. Dzierżawca może przywrócić bazę danych do wcześniejszej daty bez ograniczenia wpływu na innych dzierżawców. Wydajność bazy danych można dostrajać do optymalizacji dla jednej dzierżawy, ponownie bez konieczności naruszania innych dzierżawców. Problem polega na tym, że izolacja jest tańsza niż koszt udostępniania bazy danych innym dzierżawcom.

Gdy jest inicjowana Nowa dzierżawa, może ona współużytkować bazę danych z innymi dzierżawcami lub może być umieszczona w własnej nowej bazie danych. Później możesz zmienić zdanie i przenieść bazę danych do innej sytuacji.

Bazy danych z wieloma dzierżawcami i pojedynczymi dzierżawcami są mieszane w tej samej aplikacji SaaS, aby zoptymalizować koszty lub izolację dla każdej dzierżawy.

   ![Podzielonej na fragmenty wielodostępną aplikację bazy danych z katalogiem dzierżawy](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Wzorzec katalogu dzierżawy

W przypadku dwóch lub więcej baz danych, które zawierają co najmniej jedną dzierżawę, aplikacja musi mieć możliwość wykrycia, która baza danych przechowuje dzierżawę bieżącego zainteresowania. Baza danych wykazu przechowuje to mapowanie.

#### <a name="tenant-key"></a>Klucz dzierżawy

Dla każdej dzierżawy aplikacja WingTip może utworzyć unikatowy klucz, który jest kluczem dzierżawy. Aplikacja wyodrębnia nazwę dzierżawy z adresu URL strony sieci Web. Skrót do nazwy aplikacji w celu uzyskania klucza. Aplikacja używa klucza w celu uzyskania dostępu do wykazu. Katalog zawiera informacje o odsyłaczu do bazy danych, w której jest przechowywana dzierżawa. Aplikacja używa informacji o bazie danych, aby nawiązać połączenie. Można również użyć innych schematów kluczy dzierżawy.

Użycie wykazu umożliwia zmianę nazwy lub lokalizacji bazy danych dzierżawy po zainicjowaniu obsługi bez zakłócania działania aplikacji. W modelu bazy danych z wieloma dzierżawcami katalog umożliwia przeniesienie dzierżawy między bazami danych.

#### <a name="tenant-metadata-beyond-location"></a>Metadane dzierżawy poza lokalizacją

Wykaz może również wskazywać, czy dzierżawca jest w trybie offline w celu konserwacji lub wykonywania innych czynności. Katalog można rozszerzyć, aby przechowywać dodatkowe metadane dzierżawy lub bazy danych, na przykład następujące elementy:
- Warstwa usług lub wersja bazy danych.
- Wersja schematu bazy danych.
- Nazwa dzierżawy i jej umowa SLA (Umowa dotycząca poziomu usług).
- Informacje umożliwiające zarządzanie aplikacjami, obsługę klienta lub procesy DevOps.

Wykaz może również służyć do włączania raportów między dzierżawcami, zarządzania schematami i wyodrębniania danych na potrzeby analizy.

### <a name="elastic-database-client-library"></a>Elastic Database Biblioteka kliencka

W programie Wingtip katalog jest implementowany w bazie danych *tenantcatalog* . *Tenantcatalog* jest tworzony przy użyciu funkcji zarządzania fragmentu w [bibliotece klienta Elastic Database (Biblioteka edcl)](elastic-database-client-library.md). Biblioteka umożliwia aplikacji Tworzenie i używanie *mapy fragmentu* przechowywanej w bazie danych oraz zarządzanie nią. Mapa fragmentu krzyżowo odwołuje się do klucza dzierżawy z fragmentu, co oznacza jego bazę danych podzielonej na fragmenty.

Podczas aprowizacji dzierżawców funkcje Biblioteka EDCL mogą być używane z poziomu aplikacji lub skryptów programu PowerShell do tworzenia wpisów na mapie fragmentu. Później funkcje Biblioteka EDCL mogą służyć do nawiązywania połączenia z poprawną bazą danych. Biblioteka EDCL buforuje informacje o połączeniu, aby zminimalizować ruch w bazie danych wykazu i przyspieszyć proces łączenia.

> [!IMPORTANT]
> *Nie* należy edytować danych w bazie danych wykazu za pośrednictwem dostępu bezpośredniego. Bezpośrednie aktualizacje nie są obsługiwane z powodu wysokiego ryzyka uszkodzenia danych. Zamiast tego należy edytować dane mapowania przy użyciu tylko interfejsów API Biblioteka EDCL.

## <a name="tenant-provisioning-pattern"></a>Wzorzec aprowizacji dzierżawców

#### <a name="checklist"></a>Lista kontrolna

Jeśli chcesz udostępnić nową dzierżawę do istniejącej udostępnionej bazy danych, musisz zadać następujące pytania:
- Czy jest wystarczająco dużo miejsca dla nowej dzierżawy?
- Czy istnieją tabele z wymaganymi danymi referencyjnymi dla nowej dzierżawy lub można dodać dane?
- Czy ma odpowiednią odmianę schematu bazowego dla nowej dzierżawy?
- Czy znajduje się w odpowiedniej lokalizacji geograficznej znajdującej się w pobliżu nowej dzierżawy?
- Czy jest ona w odpowiedniej warstwie usług dla nowej dzierżawy?

Jeśli chcesz, aby nowa dzierżawa była izolowana we własnej bazie danych, możesz ją utworzyć, aby spełniała wymagania dla dzierżawy.

Po zakończeniu aprowizacji należy zarejestrować dzierżawę w wykazie. Na koniec można dodać mapowanie dzierżawy w celu odwołującego się do odpowiedniej fragmentu.

#### <a name="template-database"></a>Baza danych szablonów

Inicjowanie obsługi administracyjnej bazy danych przez wykonywanie skryptów SQL, wdrażanie BACPAC lub kopiowanie bazy danych szablonów. Aplikacje Wingtip kopiują bazę danych szablonów w celu tworzenia nowych baz danych dzierżawy.

Podobnie jak w przypadku dowolnej aplikacji, Wingtip będzie się rozwijać z upływem czasu. Czasami Wingtip będzie wymagała zmian w bazie danych. Zmiany mogą obejmować następujące elementy:
- Nowy lub zmieniony schemat.
- Nowe lub zmienione dane referencyjne.
- Rutynowe zadania konserwacji bazy danych w celu zapewnienia optymalnej wydajności aplikacji.

W przypadku aplikacji SaaS zmiany te muszą zostać wprowadzone w sposób skoordynowany — potencjalnie w bardzo wielu bazach danych dzierżaw. Aby te zmiany znajdowały się w przyszłych bazach danych dzierżaw, należy je włączyć w procesie aprowizacji. To wyzwanie jest szczegółowo opisane w [samouczku zarządzania schematami](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Skrypty

Skrypty aprowizacji dzierżawców w tym samouczku obsługują oba z następujących scenariuszy:
- Inicjowanie obsługi dzierżawy w istniejącej bazie danych udostępnionej innym dzierżawcom.
- Inicjowanie obsługi dzierżawy w oddzielnym bazie danych.

Dane dzierżawy są następnie inicjowane i zarejestrowane na mapie fragmentu wykazu. W przykładowej aplikacji bazy danych, które zawierają wiele dzierżawców, mają nazwę generyczną, taką jak *tenants1* lub *tenants2*. Do baz danych, które zawierają pojedynczą dzierżawę, nadano nazwę dzierżawy. Określone konwencje nazewnictwa w przykładzie nie stanowią krytycznej części wzorca, ponieważ użycie wykazu umożliwia przypisanie każdej nazwy do bazy danych.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Początek samouczka

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udostępnianie dzierżawy w bazie danych z wieloma dzierżawcami
> * Udostępnianie dzierżawy w bazie danych z jedną dzierżawą
> * Inicjowanie obsługi partii dzierżawców w bazach danych z wieloma dzierżawcami i jedną dzierżawą
> * Rejestrowanie bazy danych i mapowania dzierżawy w wykazie

#### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- Wdrożono Wingtip biletów SaaS aplikację bazy danych z wieloma dzierżawcami. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji bazy danych z obsługą wielu dzierżawców Wingtip SaaS](../../sql-database/saas-multitenantdb-get-started-deploy.md)

- Pobierz skrypty Wingtip i kod źródłowy:
    - Wingtip bilety SaaS wielodostępnych skryptów bazy danych i kodu źródłowego aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) .
    - Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania skryptów Wingtip.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Udostępnianie dzierżawcy do bazy danych *udostępnionej* innym dzierżawcom

W tej sekcji zostanie wyświetlona lista najważniejszych akcji związanych z obsługą administracyjną podejmowanych przez skrypty programu PowerShell. Następnie za pomocą debugera ISE programu PowerShell możesz przechodzić do kolejnych skryptów, aby zobaczyć akcje w kodzie.

#### <a name="major-actions-of-provisioning"></a>Główne akcje aprowizacji

Poniżej przedstawiono najważniejsze elementy przepływu pracy aprowizacji, które można krokowo wykonać:

- **Oblicz nowy klucz dzierżawy**: funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: wykaz jest sprawdzany w celu upewnienia się, że klucz nie został jeszcze zarejestrowany.
- **Zainicjuj dzierżawcę w domyślnej bazie danych dzierżawy**: baza danych dzierżawy została zaktualizowana w celu dodania informacji o nowej dzierżawie.
- **Rejestrowanie dzierżawy w wykazie**: mapowanie między nowym kluczem dzierżawy a istniejącą bazą danych tenants1 jest dodawane do wykazu.
- **Dodaj nazwę dzierżawy do tabeli rozszerzeń wykazu**: nazwa miejsca jest dodawana do tabeli dzierżawców w katalogu.  To dodanie pokazuje, w jaki sposób baza danych wykazu może zostać rozszerzona w celu obsługi dodatkowych danych specyficznych dla aplikacji.
- **Otwórz stronę zdarzeń dla nowej dzierżawy**: Strona zdarzeń *Bushwillow Blues* zostanie otwarta w przeglądarce.

   ![Zrzut ekranu przedstawiający stronę zdarzeń dla nowej dzierżawy.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Kroki debugera

Aby zrozumieć, w jaki sposób aplikacja Wingtip implementuje nowe udostępnianie dzierżawy w udostępnionej bazie danych, Dodaj punkt przerwania i przejdź przez przepływ pracy:

1. W *ISE programu PowerShell*Otwórz pozycję... \\ Moduły edukacyjne \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* i ustawiają następujące parametry:
   - **$TenantName**  =  **Bushwillow Blues**, nazwa nowego miejsca.
   - **$VenueType**  =  **Blues**, jeden ze wstępnie zdefiniowanych typów miejsc: blues, ClassicalMusic, odpowiedzialna, Jazz, judo, motorracing, Multipurpose, Opera, ROCKMUSIC, piłka nożna (małe litery, bez spacji).
   - **$DemoScenario**  =  **1**, aby udostępnić dzierżawcę w udostępnionej bazie danych innym dzierżawcom.

2. Dodaj punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu 38, wiersz o następującej treści: *New-dzierżawca*, a następnie naciśnij klawisz **F9**.

   ![Zrzut ekranu, który podświetla linię obejmującą nową dzierżawę.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Uruchom skrypt, naciskając klawisz **F5**.

4. Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij klawisz **F11** , aby wkroczyć do kodu.

   ![Zrzut ekranu pokazuje Windows PowerShell ISE z menu Debuguj otwarty i Wkrocz do zaznaczonego.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Śledź wykonywanie skryptu przy użyciu opcji menu **Debuguj** , **F10** i **F11**, aby przekroczyć lub użyć funkcji o nazwie.

Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z skryptami programu PowerShell i ich debugowania](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Udostępnianie dzierżawcy w *własnej* bazie danych

#### <a name="major-actions-of-provisioning"></a>Główne akcje aprowizacji

Poniżej przedstawiono najważniejsze elementy przepływu pracy, które należy wykonać podczas śledzenia skryptu:

- **Oblicz nowy klucz dzierżawy**: funkcja skrótu służy do tworzenia klucza dzierżawy na podstawie nazwy dzierżawy.
- **Sprawdź, czy klucz dzierżawy już istnieje**: wykaz jest sprawdzany w celu upewnienia się, że klucz nie został jeszcze zarejestrowany.
- **Tworzenie nowej bazy danych dzierżawy**: baza danych jest tworzona przez skopiowanie bazy danych *basetenantdb* przy użyciu szablonu Menedżer zasobów.  Nazwa nowej bazy danych jest określana na podstawie nazwy dzierżawy.
- **Dodaj bazę danych do wykazu**: Nowa baza danych dzierżawy jest zarejestrowana jako fragmentu w wykazie.
- **Zainicjuj dzierżawcę w domyślnej bazie danych dzierżawy**: baza danych dzierżawy została zaktualizowana w celu dodania informacji o nowej dzierżawie.
- **Rejestrowanie dzierżawy w wykazie**: mapowanie między nowym kluczem dzierżawy i bazą danych *sequoiasoccer* jest dodawane do wykazu.
- **Nazwa dzierżawy została dodana do wykazu**: nazwa miejsca zostanie dodana do tabeli rozszerzenia dzierżawców w wykazie.
- **Otwórz stronę zdarzeń dla nowej dzierżawy**: Strona zdarzenia *Sequoia piłkarskie* jest otwarta w przeglądarce.

   ![zdarzenia](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Kroki debugera

Teraz przechodzenie przez proces skryptu podczas tworzenia dzierżawy we własnej bazie danych:

1. Nadal w... \\ Moduły uczenia \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* ustawiać następujące parametry:
   - **$TenantName**  =  **Sequoia piłka nożna**, nazwisko nowego miejsca.
   - **$VenueType**  =  **piłka nożna**, jeden ze wstępnie zdefiniowanych typów miejsc: blues, ClassicalMusic, odpowiedzialna, Jazz, judo, motorracing, Multipurpose, Opera, ROCKMUSIC, piłka nożna (małe litery, bez spacji).
   - **$DemoScenario**  =  **2**, aby udostępnić dzierżawcom swoją własną bazę danych.

2. Dodaj nowy punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu 57, wiersz " * & &nbsp; $PSScriptRoot \new-tenantanddatabase"*, a następnie naciśnij klawisz **F9**.

   ![punkt przerwania](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Uruchom skrypt, naciskając klawisz **F5**.

4. Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij klawisz **F11** , aby wkroczyć do kodu.  Użyj klawisza **F10** i **F11** , aby przekroczyć i przejść do funkcji śledzenia wykonania.

## <a name="provision-a-batch-of-tenants"></a>Inicjowanie obsługi partii dzierżawców

W tym ćwiczeniu zainicjujemy partię 17 dzierżawców. Zaleca się zainicjowanie tej partii dzierżawców przed rozpoczęciem innych samouczków Wingtip, aby umożliwić pracę z innymi bazami danych.

1. W *ISE programu PowerShell*Otwórz pozycję... \\ Moduły uczenia \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* i zmienić parametr *$DemoScenario* na 4:
   - **$DemoScenario**  =  **4**, aby zainicjować obsługę partii dzierżawców w udostępnionej bazie danych.

2. Naciśnij klawisz **F5** i uruchom skrypt.

### <a name="verify-the-deployed-set-of-tenants"></a>Weryfikowanie wdrożonego zestawu dzierżawców

Na tym etapie masz połączenie dzierżawców wdrożonych w udostępnionej bazie danych i dzierżawcach wdrożonych w ich własnych bazach danych. Azure Portal można użyć do sprawdzenia utworzonych baz danych. W [Azure Portal](https://portal.azure.com)Otwórz program **tenants1-MT- \<USER\> ** Server, przechodząc do listy serwerów SQL.  Lista **baz danych SQL** powinna zawierać udostępnioną bazę danych **tenants1** oraz bazy danych dla dzierżawców, które znajdują się w własnej bazie danych:

   ![lista baz danych](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

Gdy Azure Portal zawiera bazy danych dzierżawy, nie można zobaczyć dzierżawców w udostępnionej *inside* bazie danych. Pełną listę dzierżawców można zobaczyć w witrynie sieci Web **centrum zdarzeń** Wingtip i przeglądając katalog.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Korzystanie ze strony centrum zdarzeń biletów Wingtip

Otwórz stronę centrum zdarzeń w przeglądarce (http: Events. Wingtip-Mt. \<USER\> . trafficmanager.net)

#### <a name="using-catalog-database"></a>Korzystanie z bazy danych wykazu

Pełna lista dzierżawców i odpowiednia baza danych dla każdej z nich są dostępne w wykazie. Zostanie udostępniony Widok SQL, który przyłącza nazwę dzierżawy do nazwy bazy danych. Widok dobrze demonstruje wartość rozszerzania metadanych, które są przechowywane w katalogu.
- Widok SQL jest dostępny w bazie danych tenantcatalog.
- Nazwa dzierżawy jest przechowywana w tabeli dzierżawców.
- Nazwa bazy danych jest przechowywana w tabelach zarządzania fragmentu.

1. W SQL Server Management Studio (SSMS) Połącz się z serwerem dzierżawców w **katalogu-Mt. \<USER\> database.windows.net**, z identyfikatorem logowania = **Developer**i hasłem = **P \@ ssword1**

    ![Okno dialogowe połączenia programu SSMS](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. W Eksplorator obiektów SSMS przejdź do widoku w bazie danych *tenantcatalog* .

3. Kliknij prawym przyciskiem myszy widok *TenantsExtended* i wybierz **pozycję Wybierz pierwsze 1000 wierszy**. Zanotuj mapowanie między nazwą dzierżawy i bazą danych dla różnych dzierżawców.

    ![Widok ExtendedTenants w programie SSMS](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Inne wzorce aprowizacji

W tej sekcji omówiono inne interesujące wzorce udostępniania.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Wstępne Inicjowanie obsługi administracyjnej baz danych w pulach elastycznych

Wzorzec wstępnej aprowizacji wykorzystuje fakt, że w przypadku korzystania z pul elastycznych rozliczenia są przeznaczone dla puli, a nie bazy danych. W ten sposób można dodać bazy danych do elastycznej puli, zanim będą one potrzebne bez ponoszenia dodatkowych kosztów. To wstępne udostępnienie znacznie skraca czas potrzebny na udostępnienie dzierżawy w bazie danych. Liczba wstępnie zainicjowanych baz danych można dostosować w miarę potrzeb, aby zachować bufor odpowiedni dla przewidywanego tempa aprowizacji.

#### <a name="auto-provisioning"></a>Automatyczne aprowizowanie

We wzorcu automatycznej aprowizacji dedykowana usługa aprowizacji jest używana do automatycznego udostępniania serwerów, pul i baz danych, zgodnie z potrzebami. Ta Automatyzacja obejmuje wstępne Inicjowanie obsługi baz danych w pulach elastycznych. Po zlikwidowaniu i usunięciu baz danych luki w puli elastycznej mogą być wypełniane przez usługę aprowizacji zgodnie z potrzebami.

Ten typ zautomatyzowanej usługi może być prosty lub skomplikowany. Na przykład Automatyzacja może obsłużyć obsługę w wielu lokalizacje geograficzneach i skonfigurować replikację geograficzną na potrzeby odzyskiwania po awarii. Ze wzorcem autoaprowizacji aplikacja kliencka lub skrypt wyśle żądanie aprowizacji do kolejki w celu przetworzenia przez usługę aprowizacji. Skrypt będzie następnie sondowany, aby wykryć zakończenie. Jeśli jest używane wstępne Inicjowanie obsługi, żądania byłyby obsługiwane szybko, podczas gdy usługa w tle zarządza zastępowaniem zastępujący bazy danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteka kliencka Elastic Database](elastic-database-client-library.md)
- [Jak debugować skrypty w Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udostępnianie jednej nowej dzierżawy do udostępnionej bazy danych z wieloma dzierżawcami i jej własnej bazy danych
> * Aprowizowanie partii dodatkowych dzierżaw
> * Przechodzenie między szczegółowymi informacjami o aprowizacji dzierżawców i rejestrowanie ich w wykazie

Wypróbuj [Samouczek dotyczący monitorowania wydajności](../../sql-database/saas-multitenantdb-performance-monitoring.md).

