---
title: Nawiązywanie połączenia z usługą SQL Server, Azure SQL Database lub wystąpieniem zarządzanym usługi Azure SQL
description: Automatyzowanie zadań związanych z bazami danych SQL w środowisku lokalnym lub w chmurze przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/16/2020
tags: connectors
ms.openlocfilehash: 534b9fedc6649d3174ea65caf51b28004de7bda2
ms.sourcegitcommit: a75ca63da5c0cc2aff5fb131308853b9edb41552
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169391"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatyzowanie przepływów pracy dla bazy danych SQL przy użyciu Azure Logic Apps

W tym artykule pokazano, jak uzyskać dostęp do danych w bazie danych SQL z poziomu aplikacji logiki za pomocą łącznika SQL Server. Dzięki temu możesz zautomatyzować zadania, procesy lub przepływy pracy, które zarządzają danymi i zasobami SQL, tworząc Aplikacje logiki. Łącznik SQL Server działa dla [SQL Server](/sql/sql-server/sql-server-technical-documentation) , a także [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) i [wystąpienia zarządzanego Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

Możesz tworzyć aplikacje logiki, które są uruchamiane, gdy wyzwalane przez zdarzenia w bazie danych SQL lub w innych systemach, takich jak Dynamics CRM Online. Aplikacje logiki mogą również pobierać, wstawiać i usuwać dane oraz uruchamiać zapytania SQL i procedury składowane. Można na przykład utworzyć aplikację logiki, która automatycznie sprawdza dostępność nowych rekordów w usłudze Dynamics CRM Online, dodaje do bazy danych SQL elementy dla nowych rekordów, a następnie wysyła do nich alerty e-mail dotyczące dodanych elementów.

Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Informacje techniczne, ograniczenia i znane problemy dotyczące konkretnego łącznika znajdują się na stronie informacje o [łączniku SQL Server](/connectors/sql/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [SQL Server bazy danych](/sql/relational-databases/databases/create-a-database), [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)lub [wystąpienia zarządzanego Azure SQL](../azure-sql/managed-instance/instance-create-quickstart.md).

  Tabele muszą zawierać dane, aby aplikacja logiki mogła zwracać wyniki podczas wywoływania operacji. Jeśli używasz Azure SQL Database, możesz użyć przykładowych baz danych, które są dołączone.

* Nazwa programu SQL Server, nazwa bazy danych, nazwa użytkownika i hasło. Te poświadczenia są potrzebne, aby można było autoryzować logikę w celu uzyskania dostępu do programu SQL Server.

  * W przypadku SQL Server lokalnych można znaleźć te szczegóły w parametrach połączenia:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * W przypadku Azure SQL Database te szczegóły można znaleźć w parametrach połączenia.
  
    Na przykład aby znaleźć ten ciąg w Azure Portal, Otwórz bazę danych. W menu Baza danych wybierz opcję **Parametry połączenia** lub **Właściwości**:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* W oparciu o to, czy aplikacje logiki mają być uruchamiane w globalnym, wielodostępnym środowisku Azure, czy na platformie [usług integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), poniżej przedstawiono inne wymagania dotyczące łączenia się z lokalnymi SQL Server:

  * W przypadku aplikacji logiki na globalnym, wielodostępnej platformie Azure, która nawiązuje połączenie z lokalnym SQL Server, należy zainstalować lokalną [bramę danych](../logic-apps/logic-apps-gateway-install.md) na komputerze lokalnym oraz [zasób bramy danych, który został już utworzony na platformie Azure](../logic-apps/logic-apps-gateway-connection.md).

  * W przypadku aplikacji logiki w ISE, które łączą się z lokalnym SQL Server i używają uwierzytelniania systemu Windows, łącznik SQL Server z mi wersjami nie obsługuje uwierzytelniania systemu Windows. W związku z tym nadal musisz używać bramy Data Gateway i ISE SQL Server Connector. W przypadku innych typów uwierzytelniania nie trzeba używać bramy Data Gateway i można używać łącznika z ISE.

* Aplikacja logiki, do której jest potrzebny dostęp do bazy danych SQL. Aby uruchomić aplikację logiki przy użyciu wyzwalacza SQL, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Teraz wykonaj następujące kroki:

* [Łączenie z usługą Azure SQL Database opartą na chmurze lub wystąpieniem zarządzanym](#connect-azure-sql-db)
* [Nawiązywanie połączenia z lokalnym SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Nawiązywanie połączenia z Azure SQL Database lub wystąpieniem zarządzanym

Aby uzyskać dostęp do wystąpienia zarządzanego usługi Azure SQL bez korzystania z lokalnej bramy danych lub środowiska usługi integracji, należy [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym usługi Azure SQL](../azure-sql/managed-instance/public-endpoint-configure.md). Publiczny punkt końcowy używa portu 3342, dlatego należy określić ten numer portu podczas tworzenia połączenia z poziomu aplikacji logiki.


Gdy po raz pierwszy dodasz [wyzwalacz SQL](#add-sql-trigger) lub [akcję SQL](#add-sql-action)i nie utworzono wcześniej połączenia z bazą danych, zostanie wyświetlony monit o wykonanie następujących czynności:

1. W polu **Typ uwierzytelniania**wybierz Uwierzytelnianie, które jest wymagane i włączone dla bazy danych w Azure SQL Database lub wystąpieniu zarządzanym Azure SQL:

   | Authentication | Opis |
   |----------------|-------------|
   | [**Integracja z usługą Azure AD**](../azure-sql/database/authentication-aad-overview.md) | -Obsługuje łącznik SQL Server ISE i ISE. <p><p>-Wymaga prawidłowej tożsamości w usłudze Azure Active Directory (Azure AD), która ma dostęp do bazy danych. <p>Więcej informacji można znaleźć w następujących tematach: <p>- [Omówienie zabezpieczeń usługi Azure SQL — uwierzytelnianie](../azure-sql/database/security-overview.md#authentication) <br>- [Autoryzowanie dostępu do bazy danych do usługi Azure SQL — uwierzytelnianie i autoryzacja](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL — uwierzytelnianie zintegrowane z usługą Azure AD](../azure-sql/database/authentication-aad-overview.md) |
   | [**Uwierzytelnianie SQL Server**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Obsługuje łącznik SQL Server ISE i ISE. <p><p>-Wymaga prawidłowej nazwy użytkownika i silnego hasła, które są tworzone i przechowywane w bazie danych. <p>Więcej informacji można znaleźć w następujących tematach: <p>- [Omówienie zabezpieczeń usługi Azure SQL — uwierzytelnianie](../azure-sql/database/security-overview.md#authentication) <br>- [Autoryzowanie dostępu do bazy danych do usługi Azure SQL — uwierzytelnianie i autoryzacja](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Ten przykład jest kontynuowany z **integracją z usługą Azure AD**:

   ![Zrzut ekranu przedstawiający okno połączenia "SQL Server" z otwartą listą "typ uwierzytelniania" i wybraną opcją "Azure AD Integrated".](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Po wybraniu opcji **integracja z usługą Azure AD**wybierz pozycję **Zaloguj**. W zależności od tego, czy używasz Azure SQL Database, czy z wystąpienia zarządzanego Azure SQL, wybierz poświadczenia użytkownika na potrzeby uwierzytelniania.

1. Wybierz następujące wartości dla bazy danych:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa serwera** | Yes | Adres serwera SQL, na przykład `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nazwa bazy danych** | Yes | Nazwa bazy danych SQL, na przykład `Fabrikam-Azure-SQL-DB` |
   | **Nazwa tabeli** | Yes | Tabela, której chcesz użyć, na przykład `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Te informacje można znaleźć w parametrach połączenia bazy danych. Na przykład w Azure Portal Znajdź i Otwórz bazę danych. W menu Baza danych wybierz opcję **Parametry połączenia** lub **Właściwości** , w których można znaleźć ten ciąg:
   >
   > `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

   Ten przykład pokazuje, jak te wartości mogą wyglądać następująco:

   ![Utwórz połączenie z bazą danych SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Teraz wykonaj kroki, które nie zostały jeszcze wykonane w obszarze [Dodaj wyzwalacz SQL](#add-sql-trigger) lub [Dodaj akcję SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Nawiązywanie połączenia z lokalnym SQL Server

Gdy po raz pierwszy dodasz [wyzwalacz SQL](#add-sql-trigger) lub [akcję SQL](#add-sql-action)i nie utworzono wcześniej połączenia z bazą danych, zostanie wyświetlony monit o wykonanie następujących czynności:

1. W przypadku połączeń z lokalnym programem SQL Server, który wymaga lokalnej bramy danych, upewnij się, że zostały [spełnione te wymagania wstępne](#multi-tenant-or-ise).

   W przeciwnym razie zasób bramy danych nie będzie wyświetlany na liście **bramy połączeń** podczas tworzenia połączenia.

1. W **polu Typ uwierzytelniania**wybierz Uwierzytelnianie, które jest wymagane i włączone na SQL Server:

   | Authentication | Opis |
   |----------------|-------------|
   | [**Uwierzytelnianie systemu Windows**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | -Obsługuje tylko ISE łącznik SQL Server, który wymaga zasobu bramy danych, który został wcześniej utworzony na platformie Azure dla połączenia, niezależnie od tego, czy używasz wielodostępnej platformy Azure, czy ISE. <p><p>-Wymaga prawidłowej nazwy użytkownika i hasła systemu Windows w celu potwierdzenia tożsamości za pomocą konta systemu Windows. <p>Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**Uwierzytelnianie SQL Server**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Obsługuje łącznik SQL Server ISE i ISE. <p><p>-Wymaga prawidłowej nazwy użytkownika i silnego hasła, które są tworzone i przechowywane w SQL Server. <p>Aby uzyskać więcej informacji, zobacz [SQL Server Authentication](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Ten przykład jest kontynuowany z **uwierzytelnianiem systemu Windows**:

   ![Wybierz typ uwierzytelniania do użycia](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Wybierz lub podaj następujące wartości dla bazy danych SQL:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa serwera SQL** | Yes | Adres serwera SQL, na przykład `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL database name** (nazwa bazy danych SQL) | Yes | Nazwa bazy danych SQL Server, na przykład `Fabrikam-Azure-SQL-DB` |
   | **Nazwa użytkownika** | Yes | Nazwa użytkownika serwera SQL i bazy danych |
   | **Password** (Hasło) | Yes | Hasło do programu SQL Server i bazy danych |
   | **Subskrypcja** |  Tak, na potrzeby uwierzytelniania systemu Windows | Subskrypcja platformy Azure dla zasobu bramy danych, który został wcześniej utworzony na platformie Azure |
   | **Brama połączenia** | Tak, na potrzeby uwierzytelniania systemu Windows | Nazwa zasobu bramy danych, który został wcześniej utworzony na platformie Azure <p><p>**Porada**: Jeśli Brama nie pojawia się na liście, sprawdź poprawność [konfiguracji bramy](../logic-apps/logic-apps-gateway-connection.md). |
   |||

   > [!TIP]
   > Te informacje można znaleźć w parametrach połączenia bazy danych:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Ten przykład pokazuje, jak te wartości mogą wyglądać następująco:

   ![Ukończono tworzenie połączenia SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Teraz wykonaj kroki, które nie zostały jeszcze wykonane w obszarze [Dodaj wyzwalacz SQL](#add-sql-trigger) lub [Dodaj akcję SQL](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Dodawanie wyzwalacza SQL

1. W [Azure Portal](https://portal.azure.com) lub w programie Visual Studio Utwórz pustą aplikację logiki, która spowoduje otwarcie projektanta aplikacji logiki. Ten przykład kontynuuje działanie z Azure Portal.

1. W projektancie w polu wyszukiwania wpisz `sql server` . Z listy Wyzwalacze wybierz wyzwalacz SQL, którego chcesz użyć. Ten przykład używa wyzwalacza **podczas tworzenia elementu** .

   ![Wybierz wyzwalacz "po utworzeniu elementu"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Jeśli łączysz się z usługą SQL Database po raz pierwszy, zostanie wyświetlony monit o [utworzenie połączenia z usługą SQL Database](#create-connection). Po utworzeniu tego połączenia możesz przejść do następnego kroku.

1. W wyzwalaczu określ interwał i częstotliwość, z jaką wyzwalacz sprawdza tabelę.

1. Aby dodać inne właściwości dostępne dla tego wyzwalacza, Otwórz listę **Dodaj nowy parametr** .

   Ten wyzwalacz zwraca tylko jeden wiersz z wybranej tabeli i nic nie jest inne. Aby wykonać inne zadania, Kontynuuj przez dodanie [akcji łącznika SQL](#add-sql-action) lub [innej akcji](../connectors/apis-list.md) , która wykonuje następne zadanie w przepływie pracy aplikacji logiki.
   
   Na przykład, aby wyświetlić dane w tym wierszu, można dodać inne akcje, które tworzą plik, który zawiera pola z zwróconego wiersza, a następnie wysyła alerty e-mail. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego łącznika, zobacz [stronę odwołania łącznika](/connectors/sql/).

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Mimo że ten krok automatycznie włącza i publikuje aplikację logiki na żywo na platformie Azure, jedyną akcją, którą zajmuje bieżąca aplikacja logiki, jest sprawdzenie bazy danych na podstawie określonego interwału i częstotliwości.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Dodaj akcję SQL

W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacza cykl](../connectors/connectors-native-recurrence.md)i wywołuje akcję, która pobiera wiersz z bazy danych SQL.

1. W [Azure Portal](https://portal.azure.com) lub w programie Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. Ten przykład kontynuuje Azure Portal.

1. W obszarze wyzwalacza lub akcji, w której chcesz dodać akcję SQL, wybierz pozycję **nowy krok**.

   ![Dodawanie akcji do aplikacji logiki](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Lub, aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `sql server` . Z listy Akcje wybierz żądaną akcję SQL. W tym przykładzie zostanie użyta akcja **Pobierz wiersz** , która pobiera jeden rekord.

   ![Wybieranie akcji "Pobierz wiersz" SQL](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Jeśli łączysz się z usługą SQL Database po raz pierwszy, zostanie wyświetlony monit o [utworzenie połączenia z usługą SQL Database](#create-connection). Po utworzeniu tego połączenia możesz przejść do następnego kroku.

1. Wybierz **nazwę tabeli**, która jest `SalesLT.Customer` w tym przykładzie. Wprowadź **Identyfikator wiersza** dla żądanego rekordu.

   ![Wybierz nazwę tabeli i określ identyfikator wiersza](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Ta akcja zwraca tylko jeden wiersz z wybranej tabeli, nic innego. Aby wyświetlić dane w tym wierszu, możesz dodać inne akcje, które tworzą plik, który zawiera pola z zwróconego wiersza, i zapisać ten plik na koncie magazynu w chmurze. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego łącznika, zobacz [stronę odwołania łącznika](/connectors/sql/).

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok powoduje automatyczne włączenie i opublikowanie aplikacji logiki na żywo na platformie Azure.

## <a name="handle-bulk-data"></a>Obsługa danych zbiorczych

Czasami musisz współpracować z zestawami wyników tak długo, że łącznik nie zwróci wszystkich wyników w tym samym czasie lub chcesz mieć lepszą kontrolę nad rozmiarem i strukturą zestawów wyników. Oto kilka sposobów obsługi takich dużych zestawów wyników:

* Aby ułatwić zarządzanie wynikami w postaci mniejszych zestawów, Włącz *podział na strony*. Aby uzyskać więcej informacji, zobacz [pobieranie danych zbiorczych, rekordów i elementów za pomocą dzielenia na strony](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Utwórz procedurę przechowywaną, która organizuje wyniki w odpowiedni sposób.

  Podczas pobierania lub wstawiania wielu wierszy aplikacja logiki może wykonać iterację tych wierszy przy użyciu [*pętli until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) w ramach tych [limitów](../logic-apps/logic-apps-limits-and-config.md). Jeśli jednak aplikacja logiki musi współpracować z zestawami rekordów tak duże, na przykład tysięcy lub milionów wierszy, można zminimalizować koszty związane z wywołaniami bazy danych.

  Aby zorganizować wyniki w odpowiedni sposób, można utworzyć [*procedurę składowaną*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , która jest uruchamiana w wystąpieniu SQL, i używa instrukcji **SELECT-order by** . To rozwiązanie zapewnia większą kontrolę nad rozmiarem i strukturą wyników. Aplikacja logiki wywołuje procedurę składowaną za pomocą akcji **procedury składowanej wykonaj** SQL Server łącznika.

  Aby uzyskać więcej informacji o rozwiązaniach, zobacz następujące artykuły:

  * [Podział na strony SQL na potrzeby transferu danych zbiorczych za pomocą Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY — klauzula](/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Obsługa dynamicznych danych zbiorczych

Po wywołaniu procedury składowanej przy użyciu łącznika SQL Server, zwracane dane wyjściowe są czasami dynamiczne. W tym scenariuszu wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Wyświetl format danych wyjściowych, wykonując przebieg testu. Skopiuj i Zapisz przykładowe dane wyjściowe.

1. W projektancie w obszarze działania, w którym wywołujesz procedurę składowaną, wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz akcję**Znajdź i wybierz akcję [**Przeanalizuj dane JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) .

1. W akcji **Przeanalizuj dane JSON** wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

1. W polu **wprowadź lub wklej przykładowy ładunek JSON** wklej przykładowe dane wyjściowe, a następnie wybierz pozycję **gotowe**.

   > [!NOTE]
   > Jeśli zostanie wyświetlony komunikat o błędzie, który Logic Apps nie może wygenerować schematu, sprawdź, czy składnia przykładowych danych wyjściowych jest prawidłowo sformatowana. Jeśli nadal nie można wygenerować schematu, w polu **schemat** ręcznie wprowadź schemat.

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

1. Aby odwołać się do właściwości zawartości JSON, kliknij wewnątrz pól edycji, w których chcesz odwołać się do tych właściwości, aby wyświetlić listę zawartości dynamicznej. Na liście pod nagłówkiem [**JSON analizy**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) wybierz tokeny danych dla właściwości zawartości JSON, które chcesz.

## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

Problemy z połączeniem mogą być często wykonywane, dlatego w celu rozwiązywania problemów i rozwiązywania tych problemów sprawdź [Błędy łączności, aby SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Oto kilka przykładów:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać informacje techniczne na temat wyzwalaczy, akcji i limitów tego łącznika, zobacz [stronę referencyjną łącznika](/connectors/sql/), która jest generowana na podstawie opisu struktury Swagger.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników dla Azure Logic Apps](../connectors/apis-list.md)
