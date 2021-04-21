---
title: Nawiązywanie połączenia SQL Server, Azure SQL Database lub Azure SQL Managed Instance
description: Automatyzowanie zadań dla baz danych SQL w środowisku lokalnym lub w chmurze przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
tags: connectors
ms.openlocfilehash: 2e06616914f1e78a71a540fbd64021c0e1bfcbab
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785977"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatyzowanie przepływów pracy dla bazy danych SQL przy użyciu Azure Logic Apps

W tym artykule pokazano, jak można uzyskać dostęp do danych w bazie danych SQL z wewnątrz aplikacji logiki za pomocą SQL Server łącznika. Dzięki temu można zautomatyzować zadania, procesy lub przepływy pracy, które zarządzają danymi i zasobami SQL, tworząc aplikacje logiki. Łącznik SQL Server działa [dla](/sql/sql-server/sql-server-technical-documentation) SQL Server, a także Azure SQL Database [i](../azure-sql/database/sql-database-paas-overview.md) [Azure SQL Managed Instance.](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)

Możesz tworzyć aplikacje logiki uruchamiane po wyzwoleniu przez zdarzenia w bazie danych SQL lub w innych systemach, takich jak Dynamics CRM Online. Aplikacje logiki mogą również pobrać, wstawić i usunąć dane oraz uruchamiać zapytania SQL i procedury składowane. Możesz na przykład utworzyć aplikację logiki, która automatycznie sprawdza, czy w u usługach Dynamics CRM Online są nowe rekordy, dodaje elementy do bazy danych SQL dla nowych rekordów, a następnie wysyła alerty e-mail dotyczące dodanych elementów.

Jeśli jesteś nowym użytkownikom aplikacji logiki, zapoznaj się z Azure Logic Apps [i](../logic-apps/logic-apps-overview.md) [Szybki start: tworzenie pierwszej aplikacji logiki.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Aby uzyskać informacje techniczne dotyczące łącznika, ograniczenia i znane problemy, zobacz stronę SQL Server [łącznika.](/connectors/sql/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Baza [SQL Server ,](/sql/relational-databases/databases/create-a-database) [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)lub [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  Tabele muszą zawierać dane, aby aplikacja logiki może zwracać wyniki podczas wywoływania operacji. Jeśli używasz Azure SQL Database, możesz użyć przykładowych baz danych, które są dołączone.

* Nazwa serwera SQL, nazwa bazy danych, nazwa użytkownika i hasło. Te poświadczenia są potrzebne, aby można było autoryzować logikę w celu uzyskania dostępu do serwera SQL.

  * W przypadku połączeń lokalnych SQL Server te szczegóły można znaleźć w parametrów połączenia:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Te Azure SQL Database można znaleźć w parametrów połączenia.
  
    Aby na przykład znaleźć ten ciąg w Azure Portal, otwórz bazę danych. W menu bazy danych wybierz pozycję **Parametry połączenia** lub **Właściwości:**

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* W zależności od tego, czy aplikacje logiki będą działać na globalnej, wielodostępnej platformie Azure, czy w środowisku usługi integracji [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)poniżej znajdują się inne wymagania dotyczące nawiązywania połączenia z lokalnymi SQL Server:

  * W przypadku aplikacji logiki na globalnej, wielodostępnych platformie Azure, które [](../logic-apps/logic-apps-gateway-install.md) łączą się z lokalną usługą SQL Server, musisz mieć zainstalowaną lokalną bramę danych na komputerze lokalnym i zasób bramy danych, który został już utworzony na [platformie Azure.](../logic-apps/logic-apps-gateway-connection.md)

  * W przypadku aplikacji logiki w środowisku ISE, które łączą się z lokalnymi usługami SQL Server i korzystają z uwierzytelniania systemu Windows, łącznik usługi SQL Server ise nie obsługuje uwierzytelniania systemu Windows. Dlatego nadal musisz używać bramy danych i łącznika interfejsu SQL Server ISE. W przypadku innych typów uwierzytelniania nie trzeba używać bramy danych i można używać łącznika z wersją ise.

* Aplikacja logiki, w której potrzebujesz dostępu do bazy danych SQL. Aby uruchomić aplikację logiki za pomocą wyzwalacza SQL, potrzebujesz [pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Teraz przejdź do tych kroków:

* [Nawiązywanie połączenia z chmurową Azure SQL Database wystąpieniem zarządzanym](#connect-azure-sql-db)
* [Nawiązywanie połączenia z lokalnymi SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Nawiązywanie połączenia z Azure SQL Database lub wystąpieniem zarządzanym

Aby uzyskać dostęp do Azure SQL Managed Instance bez użycia lokalnej bramy danych lub środowiska usługi integracji, należy skonfigurować publiczny punkt końcowy na Azure SQL Managed Instance [.](../azure-sql/managed-instance/public-endpoint-configure.md) Publiczny punkt końcowy używa portu 3342, dlatego należy określić ten numer portu podczas tworzenia połączenia z aplikacji logiki.


Przy pierwszym dodaniu wyzwalacza [SQL](#add-sql-trigger) lub akcji [SQL](#add-sql-action)i nie utworzono wcześniej połączenia z bazą danych, zostanie wyświetlony monit o ukończenie tych kroków:

1. W **przypadku opcji Typ** uwierzytelniania wybierz wymagane i włączone uwierzytelnianie w bazie danych w Azure SQL Database lub Azure SQL Managed Instance:

   | Authentication | Opis |
   |----------------|-------------|
   | [**Zintegrowana z usługą Azure AD**](../azure-sql/database/authentication-aad-overview.md) | - Obsługuje łączniki inne niż ISE i ISE SQL Server ise. <p><p>— Wymaga prawidłowej tożsamości w usłudze Azure Active Directory (Azure AD), która ma dostęp do bazy danych. <p>Więcej informacji można znaleźć w następujących tematach: <p>- [Azure SQL Przegląd zabezpieczeń — uwierzytelnianie](../azure-sql/database/security-overview.md#authentication) <br>- [Autoryzowanie dostępu do bazy danych Azure SQL — uwierzytelnianie i autoryzacja](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL — zintegrowane uwierzytelnianie usługi Azure AD](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server uwierzytelniania**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Obsługuje łącznik łącznika dla obu SQL Server ise i ISE. <p><p>- Wymaga prawidłowej nazwy użytkownika i silnego hasła, które są tworzone i przechowywane w bazie danych. <p>Więcej informacji można znaleźć w następujących tematach: <p>- [Azure SQL Przegląd zabezpieczeń — uwierzytelnianie](../azure-sql/database/security-overview.md#authentication) <br>- [Autoryzowanie dostępu do bazy danych Azure SQL — uwierzytelnianie i autoryzacja](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   W tym przykładzie jest kontynuowana **integracja z usługą Azure AD:**

   ![Zrzut ekranu przedstawiający okno połączenia "SQL Server" z otwartą listą "Typ uwierzytelniania" i wybranymi opcjami "Azure AD Integrated".](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Po wybraniu opcji **Azure AD Integrated (Zintegrowane z usługą Azure AD)** **wybierz pozycję Sign In (Zaloguj się).** W zależności od tego, Azure SQL Database czy Azure SQL Managed Instance, wybierz poświadczenia użytkownika na użytek uwierzytelniania.

1. Wybierz następujące wartości dla bazy danych:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa serwera** | Yes | Adres serwera SQL, na przykład `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nazwa bazy danych** | Tak | Nazwa bazy danych SQL, na przykład `Fabrikam-Azure-SQL-DB` |
   | **Nazwa tabeli** | Tak | Tabela, której chcesz użyć, na przykład `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Aby podać informacje o bazie danych i tabeli, dostępne są następujące opcje:
   > 
   > * Znajdź te informacje w parametrów połączenia bazy danych. Na przykład w Azure Portal znajdź i otwórz bazę danych. W menu bazy danych wybierz pozycję **Parametry połączenia** lub **Właściwości,** gdzie możesz znaleźć ten ciąg:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Domyślnie tabele w systemowych bazach danych są filtrowane, więc mogą nie być automatycznie wyświetlane po wybraniu systemowej bazy danych. Alternatywnie możesz ręcznie wprowadzić nazwę tabeli po wybraniu opcji Wprowadź wartość **niestandardową** z listy bazy danych.
   >

   W tym przykładzie pokazano, jak mogą wyglądać następujące wartości:

   ![Tworzenie połączenia z bazą danych SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Teraz kontynuuj kroki, które nie zostały jeszcze wykonane w akcję Dodaj wyzwalacz [SQL](#add-sql-trigger) lub [Dodaj akcję SQL.](#add-sql-action)

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Nawiązywanie połączenia z usługą lokalną SQL Server

Przy pierwszym dodaniu wyzwalacza [SQL](#add-sql-trigger) lub akcji [SQL](#add-sql-action)i nie utworzono wcześniej połączenia z bazą danych, zostanie wyświetlony monit o ukończenie tych kroków:

1. W przypadku połączeń z lokalnym serwerem SQL, które wymagają lokalnej bramy danych, upewnij się, że zostały spełnione [te wymagania wstępne.](#multi-tenant-or-ise)

   W przeciwnym razie zasób bramy danych nie będzie wyświetlany na liście **Brama** połączeń podczas tworzenia połączenia.

1. W **przypadku opcji Typ** uwierzytelniania wybierz wymagane i włączone uwierzytelnianie na SQL Server:

   | Authentication | Opis |
   |----------------|-------------|
   | [**Uwierzytelnianie systemu Windows**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | — Obsługuje tylko łącznik programu SQL Server innym niż ISE, który wymaga zasobu bramy danych, który został wcześniej utworzony na platformie Azure, dla połączenia, niezależnie od tego, czy używasz wielodostępnych platform Azure, czy ise. <p><p>- Wymaga prawidłowej nazwy użytkownika i hasła systemu Windows w celu potwierdzenia tożsamości za pośrednictwem konta systemu Windows. <p>Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie systemu Windows](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server uwierzytelniania**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Obsługuje łącznik łącznika dla obu SQL Server ise i ISE. <p><p>- Wymaga prawidłowej nazwy użytkownika i silnego hasła, które są tworzone i przechowywane w SQL Server. <p>Aby uzyskać więcej informacji, zobacz [SQL Server Authentication (Uwierzytelnianie).](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) |
   |||

   W tym przykładzie uwierzytelnianie **systemu Windows jest kontynuowane:**

   ![Wybieranie typu uwierzytelniania do użycia](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Wybierz lub podaj następujące wartości dla bazy danych SQL:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa serwera SQL** | Tak | Adres serwera SQL, na przykład `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL database name** (nazwa bazy danych SQL) | Tak | Nazwa bazy danych SQL Server, na przykład `Fabrikam-Azure-SQL-DB` |
   | **Nazwa użytkownika** | Tak | Nazwa użytkownika serwera SQL i bazy danych |
   | **Password** (Hasło) | Tak | Hasło serwera SQL i bazy danych |
   | **Subskrypcja** |  Tak, w przypadku uwierzytelniania systemu Windows | Subskrypcja platformy Azure dla wcześniej utworzonego zasobu bramy danych na platformie Azure |
   | **Brama połączeń** | Tak, w przypadku uwierzytelniania systemu Windows | Nazwa wcześniej utworzonego zasobu bramy danych na platformie Azure <p><p>**Porada:** Jeśli brama nie jest wyświetlana na liście, sprawdź, czy [brama jest poprawnie ustawiona.](../logic-apps/logic-apps-gateway-connection.md) |
   |||

   > [!TIP]
   > Te informacje można znaleźć w parametrów połączenia bazy danych:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   W tym przykładzie pokazano, jak mogą wyglądać następujące wartości:

   ![Tworzenie SQL Server połączenia](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Teraz przejdź do kroków, które nie zostały jeszcze wykonane w akcję Dodaj wyzwalacz [SQL](#add-sql-trigger) lub [Dodaj akcję SQL.](#add-sql-action)

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Dodawanie wyzwalacza SQL

1. W [Azure Portal](https://portal.azure.com) lub w Visual Studio utwórz pustą aplikację logiki, co spowoduje otwarcie Projektanta aplikacji logiki. Ten przykład jest kontynuacją Azure Portal.

1. W projektancie w polu wyszukiwania wprowadź `sql server` . Z listy wyzwalaczy wybierz wyzwalacz SQL, który chcesz. W tym przykładzie **użyto wyzwalacza Po utworzeniu** elementu.

   ![Wybieranie wyzwalacza "Po utworzeniu elementu"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Jeśli łączysz się z bazą danych SQL po raz pierwszy, zostanie wyświetlony monit o utworzenie połączenia [z bazą danych SQL.](#create-connection) Po utworzeniu tego połączenia możesz przejść do następnego kroku.

1. W wyzwalaczu określ interwał i częstotliwość sprawdzania tabeli przez wyzwalacz.

1. Aby dodać inne dostępne właściwości dla tego wyzwalacza, otwórz **listę Dodaj nowy parametr.**

   Ten wyzwalacz zwraca tylko jeden wiersz z wybranej tabeli i nic innego. Aby wykonać inne zadania, kontynuuj, dodając [](../connectors/apis-list.md) akcję łącznika [SQL](#add-sql-action) lub inną akcję, która wykonuje następne zadanie w przepływie pracy aplikacji logiki.

   Aby na przykład wyświetlić dane w tym wierszu, możesz dodać inne akcje, które tworzą plik, który zawiera pola ze zwróconego wiersza, a następnie wysyłać alerty e-mail. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego łącznika, zobacz [stronę referencyjną łącznika](/connectors/sql/).

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz.**

   Mimo że ten krok automatycznie włącza i publikuje aplikację logiki na żywo na platformie Azure, jedyną akcją, która obecnie podejmuje aplikacja logiki, jest sprawdzenie bazy danych na podstawie określonego interwału i częstotliwości.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Wyzwalanie przesunięcia i dryfu cyklu

Wyzwalacze oparte na połączeniach, w przypadku których należy najpierw utworzyć połączenie, takie jak wyzwalacz SQL, różnią się od wbudowanych wyzwalaczy uruchamianych natywnie w programie Azure Logic Apps, takich jak wyzwalacz [Cykl.](../connectors/connectors-native-recurrence.md) W wyzwalaczach opartych na połączeniach cyklicznych harmonogram cyklu nie jest jedynym sterownikem sterującym wykonywaniem, a strefa czasowa określa tylko początkowy czas rozpoczęcia. Kolejne przebiegi zależą od harmonogramu cyklu,  ostatniego wykonania wyzwalacza i innych czynników, które mogą powodować dryfowanie czasu wykonywania lub spowodować nieoczekiwane zachowanie, na przykład nieotrzymanie określonego harmonogramu podczas uruchamiania i zakończenia czasu letniego (DST). Aby upewnić się, że czas cyklu nie zmienia się, gdy czas DST zostanie uruchomiony, ręcznie dostosuj cykl, aby aplikacja logiki kontynuowała działanie w oczekiwanym czasie. W przeciwnym razie czas rozpoczęcia przesuwa się o godzinę do przodu, gdy rozpoczyna się czas DST, i o godzinę do tyłu po zakończeniu czasu DST. Aby uzyskać więcej informacji, zobacz [Cykl dla wyzwalaczy opartych na połączeniu.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Dodawanie akcji SQL

W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacza Cyklicznie](../connectors/connectors-native-recurrence.md)i wywołuje akcję, która pobiera wiersz z bazy danych SQL.

1. W [Azure Portal](https://portal.azure.com) lub w Visual Studio otwórz aplikację logiki w Projektancie aplikacji logiki. Ten przykład kontynuuje Azure Portal.

1. W obszarze wyzwalacza lub akcji, w której chcesz dodać akcję SQL, wybierz **pozycję Nowy krok**.

   ![Dodawanie akcji do aplikacji logiki](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę połączenia. Wybierz wyświetlony znak plus **+** (), a następnie wybierz **pozycję Dodaj akcję**.

1. W **obszarze Wybierz akcję** w polu wyszukiwania wprowadź `sql server` . Z listy akcji wybierz akcję SQL, która ma zostać wyświetlona. W tym przykładzie użyto **akcji Pobierz wiersz,** która pobiera pojedynczy rekord.

   ![Wybieranie akcji "Pobierz wiersz" SQL](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Jeśli łączysz się z bazą danych SQL po raz pierwszy, zostanie wyświetlony monit o utworzenie połączenia [z bazą danych SQL.](#create-connection) Po utworzeniu tego połączenia możesz kontynuować pracę z następnym krokiem.

1. Wybierz nazwę **tabeli**, która znajduje `SalesLT.Customer` się w tym przykładzie. Wprowadź **identyfikator wiersza** dla rekordu, którego potrzebujesz.

   ![Wybieranie nazwy tabeli i określanie identyfikatora wiersza](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Ta akcja zwraca tylko jeden wiersz z wybranej tabeli, nic więcej. Aby wyświetlić dane w tym wierszu, możesz dodać inne akcje, które tworzą plik, który zawiera pola ze zwróconego wiersza, i przechowują ten plik na koncie magazynu w chmurze. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego łącznika, zobacz [stronę referencyjną łącznika](/connectors/sql/).

1. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta wybierz pozycję **Zapisz.**

   Ten krok automatycznie włącza i publikuje aplikację logiki na żywo na platformie Azure.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Obsługa danych zbiorczych

Czasami trzeba pracować z zestawami wyników tak dużymi, aby łącznik nie zwracał wszystkich wyników w tym samym czasie lub aby lepiej kontrolować rozmiar i strukturę zestawów wyników. Oto kilka sposobów obsługi tak dużych zestawów wyników:

* Aby ułatwić zarządzanie wynikami jako mniejszymi zestawami, włącz *stronicowanie*. Aby uzyskać więcej informacji, zobacz [Get bulk data, records, and items by using pagination](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)(Uzyskiwanie danych, rekordów i elementów zbiorczych przy użyciu stronicowania). Aby uzyskać więcej informacji, zobacz [Stronicowanie SQL](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)na temat zbiorczego transferu danych za pomocą Logic Apps .

* Utwórz procedurę [*składowaną,*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) która organizuje wyniki w sposób, w jaki chcesz. Łącznik SQL udostępnia wiele funkcji zaplecza, do których można uzyskać dostęp za pomocą usługi Azure Logic Apps, dzięki czemu można łatwiej zautomatyzować zadania biznesowe, które działają z tabelami bazy danych SQL.

  Podczas uzyskiwania lub wstawiania wielu wierszy aplikacja logiki może iterować te wiersze przy użyciu pętli [*until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) w ramach tych [limitów](../logic-apps/logic-apps-limits-and-config.md). Jednak gdy aplikacja logiki musi pracować z zestawami rekordów tak dużymi, na przykład tysiącami lub milionami wierszy, że chcesz zminimalizować koszty wynikające z wywołań do bazy danych.

  Aby uporządkować wyniki w taki sposób, w jaki chcesz, możesz utworzyć procedurę składowaną uruchamianą w wystąpieniu SQL i korzystającą z instrukcji **SELECT - ORDER BY.** To rozwiązanie zapewnia większą kontrolę nad rozmiarem i strukturą wyników. Aplikacja logiki wywołuje procedurę składowaną przy użyciu SQL Server akcji Wykonaj procedurę **składowaną łącznika.** Aby uzyskać więcej informacji, zobacz [klauzulę SELECT - ORDER BY.](/sql/t-sql/queries/select-order-by-clause-transact-sql)

  > [!NOTE]
  > Łącznik SQL ma limit czasu procedury składowanej, który jest [krótszy niż 2 minuty.](/connectors/sql/#known-issues-and-limitations) Niektóre procedury składowane mogą trwać dłużej niż ten limit, co powoduje `504 Timeout` błąd. Ten problem można rozwiązać przy użyciu wyzwalacza uzupełniania SQL, natywnego zapytania przebiegu SQL, tabeli stanu i zadań po stronie serwera.
  > 
  > W tym zadaniu możesz użyć agenta zadań elastycznych [platformy Azure](../azure-sql/database/elastic-jobs-overview.md) dla [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). W [przypadku SQL Server lokalnych](/sql/sql-server/sql-server-technical-documentation) i [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)można użyć agenta [SQL Server .](/sql/ssms/agent/sql-server-agent) Aby dowiedzieć się więcej, zobacz [Handle long-running stored procedure timeouts in the SQL connector for Azure Logic Apps](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Obsługa dynamicznych danych zbiorczych

W przypadku wywołania procedury składowanej przy użyciu łącznika SQL Server zwracane dane wyjściowe są czasami dynamiczne. W tym scenariuszu wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Wyświetl format danych wyjściowych, wykonując przebieg testowy. Skopiuj i zapisz przykładowe dane wyjściowe.

1. W projektancie w obszarze akcji, w której jest wywołana procedura składowana, wybierz **pozycję Nowy krok**.

1. W **obszarze Wybierz akcję** znajdź i wybierz akcję [**Prze analizuj JSON.**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)

1. W akcji **Prze analizuj dane JSON** wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

1. W polu **Wprowadź lub wklej przykładowy ładunek JSON** wklej przykładowe dane wyjściowe i wybierz pozycję **Gotowe.**

   > [!NOTE]
   > Jeśli wystąpi błąd, który Logic Apps nie może wygenerować schematu, sprawdź, czy składnia przykładowych danych wyjściowych jest poprawnie sformatowana. Jeśli nadal nie można wygenerować schematu, w polu **Schemat** ręcznie wprowadź schemat.

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz.**

1. Aby odwołać się do właściwości zawartości JSON, kliknij wewnątrz pól edycji, w których chcesz odwoływać się do tych właściwości, aby wyświetlić listę zawartości dynamicznej. Na liście w obszarze [**Nagłówka Prze analizuj dane JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) wybierz tokeny danych dla właściwości zawartości JSON, które mają.

## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Problemy z połączeniem

Często mogą wystąpić problemy z połączeniem, dlatego aby rozwiązać tego rodzaju problemy, zapoznaj się z tematem Rozwiązywanie błędów [łączności](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)w celu SQL Server . Oto kilka przykładów:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać informacje techniczne na temat wyzwalaczy, akcji i limitów tego łącznika, zobacz stronę referencyjną łącznika [,](/connectors/sql/)która jest generowana na podstawie opisu programu Swagger.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [innych łącznikach dla Azure Logic Apps](../connectors/apis-list.md)
