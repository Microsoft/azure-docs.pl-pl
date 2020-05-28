---
title: Połącz z SQL Server lub Azure SQL Database
description: Automatyzowanie zadań związanych z bazami danych SQL w środowisku lokalnym lub w chmurze przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: 5090d772be4f68c696e8dd4882a78ba6954b6058
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020731"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatyzowanie przepływów pracy dla SQL Server lub Azure SQL Database przy użyciu Azure Logic Apps

W tym artykule pokazano, jak uzyskać dostęp do danych w bazie danych SQL z poziomu aplikacji logiki za pomocą łącznika SQL Server. Dzięki temu możesz zautomatyzować zadania, procesy lub przepływy pracy, które zarządzają danymi i zasobami SQL, tworząc Aplikacje logiki. Łącznik SQL Server działa zarówno dla [SQL Server lokalnych](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) , jak i dla [Azure SQL Database opartych na chmurze](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Możesz tworzyć aplikacje logiki, które są uruchamiane, gdy wyzwalane przez zdarzenia w bazie danych SQL lub w innych systemach, takich jak Dynamics CRM Online. Aplikacje logiki mogą również pobierać, wstawiać i usuwać dane oraz uruchamiać zapytania SQL i procedury składowane. Można na przykład utworzyć aplikację logiki, która automatycznie sprawdza dostępność nowych rekordów w usłudze Dynamics CRM Online, dodaje do bazy danych SQL elementy dla nowych rekordów, a następnie wysyła do nich alerty e-mail dotyczące dodanych elementów.

Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Informacje techniczne, ograniczenia i znane problemy dotyczące konkretnego łącznika znajdują się na stronie informacje o [łączniku SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Baza danych SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) lub baza [danych Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)

  Tabele muszą zawierać dane, aby aplikacja logiki mogła zwracać wyniki podczas wywoływania operacji. W przypadku tworzenia Azure SQL Database można użyć przykładowych baz danych, które są dołączone.

* Nazwa programu SQL Server, nazwa bazy danych, nazwa użytkownika i hasło. Te poświadczenia są potrzebne, aby można było autoryzować logikę w celu uzyskania dostępu do programu SQL Server.

  * W przypadku SQL Server te szczegóły można znaleźć w parametrach połączenia:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Aby uzyskać Azure SQL Database, można znaleźć te szczegóły w parametrach połączenia lub Azure Portal w obszarze właściwości SQL Database:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Lokalna [brama danych](../logic-apps/logic-apps-gateway-install.md) zainstalowana na komputerze lokalnym i [zasób usługi Azure Data gateway utworzony w Azure Portal](../logic-apps/logic-apps-gateway-connection.md) dla następujących scenariuszy:

  * Aplikacje logiki nie są uruchamiane w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * *Aplikacje logiki działają w* środowisku usługi integracji, ale musisz użyć uwierzytelniania systemu Windows dla połączenia SQL Server. W tym scenariuszu należy użyć wersji ISE łącznika SQL Server wraz z bramą danych, ponieważ wersja ISE nie obsługuje uwierzytelniania systemu Windows.

* Aplikacja logiki, do której jest potrzebny dostęp do bazy danych SQL. Aby uruchomić aplikację logiki przy użyciu wyzwalacza SQL, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Dodawanie wyzwalacza SQL

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji logiki.

1. W Azure Portal lub Visual Studio Utwórz pustą aplikację logiki, która otwiera Logic Apps projektanta. Ten przykład używa Azure Portal.

1. W projektancie w polu wyszukiwania wprowadź ciąg "SQL Server" jako filtr. Z listy Wyzwalacze wybierz wyzwalacz SQL, którego chcesz użyć.

   Ten przykład używa wyzwalacza **podczas tworzenia elementu** .

   ![Wybierz wyzwalacz "po utworzeniu elementu"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Jeśli zostanie wyświetlony monit o utworzenie połączenia, [Utwórz połączenie SQL teraz](#create-connection). Jeśli połączenie istnieje, wybierz **nazwę tabeli**.

   ![Wybierz żądaną tabelę](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Ustaw właściwości **interwału** i **częstotliwości** , które określają, jak często aplikacja logiki ma sprawdzać tabelę.

   Ten wyzwalacz zwraca tylko jeden wiersz z wybranej tabeli, nic nie jest inne. Aby wykonać inne zadania, Dodaj inne akcje, które wykonują żądane zadania. Na przykład, aby wyświetlić dane w tym wierszu, można dodać inne akcje, które tworzą plik, który zawiera pola z zwróconego wiersza, a następnie wysyła alerty e-mail. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/sql/).

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok powoduje automatyczne włączenie i opublikowanie aplikacji logiki na żywo na platformie Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Dodaj akcję SQL

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacza cykl](../connectors/connectors-native-recurrence.md)i wywołuje akcję, która pobiera wiersz z bazy danych SQL.

1. W Azure Portal lub w programie Visual Studio Otwórz aplikację logiki w programie Logic Apps Designer. Ten przykład używa Azure Portal.

1. W obszarze wyzwalacza lub akcji, w której chcesz dodać akcję SQL, wybierz pozycję **nowy krok**.

   ![Dodawanie nowego kroku do aplikacji logiki](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź ciąg "SQL Server" jako filtr. Z listy Akcje wybierz żądaną akcję SQL.

   W tym przykładzie zostanie użyta akcja **Pobierz wiersz** , która pobiera jeden rekord.

   ![Znajdź i wybierz akcję SQL "Pobierz wiersz"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Ta akcja zwraca tylko jeden wiersz z wybranej tabeli, nic innego. Aby wyświetlić dane w tym wierszu, możesz dodać inne akcje, które tworzą plik, który zawiera pola z zwróconego wiersza, i Zapisz ten plik na koncie magazynu w chmurze. Aby dowiedzieć się więcej o innych dostępnych akcjach dla tego łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/sql/).

1. Jeśli zostanie wyświetlony monit o utworzenie połączenia, [Utwórz połączenie SQL teraz](#create-connection). Jeśli połączenie istnieje, wybierz **nazwę tabeli**i wprowadź **Identyfikator wiersza** dla żądanego rekordu.

   ![Wprowadź nazwę tabeli i Identyfikator wiersza](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok powoduje automatyczne włączenie i opublikowanie aplikacji logiki na żywo na platformie Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Obsługa danych zbiorczych

Czasami musisz współpracować z zestawami wyników tak długo, że łącznik nie zwróci wszystkich wyników w tym samym czasie lub chcesz mieć lepszą kontrolę nad rozmiarem i strukturą zestawów wyników. Oto kilka sposobów obsługi takich dużych zestawów wyników:

* Aby ułatwić zarządzanie wynikami w postaci mniejszych zestawów, Włącz *podział na strony*. Aby uzyskać więcej informacji, zobacz [pobieranie danych zbiorczych, rekordów i elementów za pomocą dzielenia na strony](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Utwórz procedurę przechowywaną, która organizuje wyniki w odpowiedni sposób.

  Podczas pobierania lub wstawiania wielu wierszy aplikacja logiki może wykonać iterację tych wierszy przy użyciu [*pętli until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) w ramach tych [limitów](../logic-apps/logic-apps-limits-and-config.md). Jeśli jednak aplikacja logiki musi współpracować z zestawami rekordów tak duże, na przykład tysięcy lub milionów wierszy, można zminimalizować koszty związane z wywołaniami bazy danych.

  Aby zorganizować wyniki w odpowiedni sposób, można utworzyć [*procedurę składowaną*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , która jest uruchamiana w wystąpieniu SQL, i używa instrukcji **SELECT-order by** . To rozwiązanie zapewnia większą kontrolę nad rozmiarem i strukturą wyników. Aplikacja logiki wywołuje procedurę składowaną za pomocą akcji **procedury składowanej wykonaj** SQL Server łącznika.

  Aby uzyskać więcej informacji o rozwiązaniach, zobacz następujące artykuły:

  * [Podział na strony SQL na potrzeby transferu danych zbiorczych za pomocą Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY — klauzula](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Obsługa dynamicznych danych zbiorczych

Czasami w przypadku wywołania procedury składowanej w łączniku SQL Server zwrócone dane wyjściowe są dynamiczne. W tym scenariuszu wykonaj następujące kroki:

1. Otwórz **projektanta Logic Apps**.
1. Wykonaj przebieg testowy aplikacji logiki, aby wyświetlić format danych wyjściowych. Skopiuj przykładowe dane wyjściowe.
1. W projektancie w obszarze działania, w którym wywołujesz procedurę składowaną, wybierz pozycję **nowy krok**.
1. W obszarze **Wybierz akcję**Wyszukaj i wybierz akcję [**Przeanalizuj dane JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) .
1. W akcji **Przeanalizuj dane JSON** wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.
1. W oknie **wprowadzanie lub wklejanie przykładowego ładunku JSON** wklej przykładowe dane wyjściowe, a następnie wybierz pozycję **gotowe**.
1. Jeśli zostanie wyświetlony komunikat o błędzie, który Logic Apps nie może wygenerować schematu, sprawdź, czy składnia przykładowych danych wyjściowych jest prawidłowo sformatowana. Jeśli nadal nie można wygenerować schematu, wprowadź go ręcznie w polu **schemat** .
1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.
1. Aby uzyskać dostęp do właściwości zawartości JSON, użyj tokenów danych, które są wyświetlane na liście zawartości dynamicznej w ramach [akcji **Analizuj dane JSON** ](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać informacje techniczne na temat wyzwalaczy, akcji i limitów tego łącznika, zobacz [stronę referencyjną łącznika](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników dla Azure Logic Apps](../connectors/apis-list.md)
