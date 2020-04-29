---
title: Użyj Azure Functions, aby wykonać zadanie oczyszczania bazy danych
description: Użyj Azure Functions, aby zaplanować zadanie, które łączy się z Azure SQL Database, aby okresowo czyścić wiersze.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79366817"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Użyj Azure Functions, aby nawiązać połączenie z Azure SQL Database

W tym artykule pokazano, jak za pomocą Azure Functions utworzyć zaplanowane zadanie, które nawiązuje połączenie z Azure SQL Database lub wystąpieniem zarządzanym usługi Azure SQL. Kod funkcji czyści wiersze w tabeli w bazie danych. Nowa funkcja języka C# jest tworzona na podstawie wstępnie zdefiniowanego szablonu wyzwalacza czasomierza w programie Visual Studio 2019. Aby obsłużyć ten scenariusz, należy również ustawić parametry połączenia bazy danych jako ustawienia aplikacji w aplikacji funkcji. W przypadku wystąpienia zarządzanego Azure SQL należy [włączyć publiczny punkt końcowy](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) , aby można było nawiązać połączenie z Azure Functions. W tym scenariuszu jest stosowana operacja zbiorcza względem bazy danych. 

Jeśli jest to pierwsze środowisko pracy z funkcjami języka C#, należy przeczytać [informacje dotyczące deweloperów Azure Functions C#](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj kroki opisane w artykule [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](functions-create-your-first-function-visual-studio.md) , aby utworzyć lokalną aplikację funkcji, która jest przeznaczona dla wersji 2. x lub nowszej wersji środowiska uruchomieniowego. Musisz również opublikować projekt w aplikacji funkcji na platformie Azure.

+ W tym artykule przedstawiono polecenie języka Transact-SQL, które wykonuje operację oczyszczania zbiorczego w tabeli **SalesOrderHeader** w przykładowej bazie danych AdventureWorksLT. Aby utworzyć przykładową bazę danych AdventureWorksLT, wykonaj kroki opisane w artykule [Tworzenie bazy danych Azure SQL Database w Azure Portal](../sql-database/sql-database-get-started-portal.md).

+ Należy dodać [regułę zapory na poziomie serwera](../sql-database/sql-database-get-started-portal-firewall.md) dla publicznego adresu IP komputera, który jest używany w tym przewodniku Szybki Start. Ta reguła jest wymagana, aby można było uzyskać dostęp do wystąpienia bazy danych SQL z komputera lokalnego.  

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Należy uzyskać parametry połączenia dla bazy danych utworzonej po zakończeniu [tworzenia bazy danych Azure SQL Database w Azure Portal](../sql-database/sql-database-get-started-portal.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **bazy danych SQL** w menu po lewej stronie, a następnie wybierz bazę danych ze strony bazy **danych SQL** .

1. Wybierz **Parametry połączenia** w obszarze **Ustawienia** i skopiuj kompletne parametry połączenia **ADO.NET** . W przypadku wystąpienia zarządzanego usługi Azure SQL Kopiuj parametry połączenia dla publicznego punktu końcowego.

    ![Skopiuj parametry połączenia ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ustawianie parametrów połączenia

Aplikacja funkcji obsługuje wykonywanie funkcji na platformie Azure. Najlepszym rozwiązaniem w zakresie zabezpieczeń jest przechowywanie parametrów połączenia i innych wpisów tajnych w ustawieniach aplikacji funkcji. Używanie ustawień aplikacji zapobiega przypadkowemu poufności parametrów połączenia z kodem. Dostęp do ustawień aplikacji dla aplikacji funkcji można uzyskać bezpośrednio z programu Visual Studio.

Aplikacja musi być wcześniej opublikowana na platformie Azure. Jeśli jeszcze tego nie zrobiono, [Opublikuj swoją aplikację funkcji na platformie Azure](functions-develop-vs.md#publish-to-azure).

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji i wybierz polecenie **Publikuj** > **Edytuj ustawienia Azure App Service**. Wybierz pozycję **Dodaj ustawienie**, w polu **Nazwa ustawienia nowej aplikacji**wpisz `sqldb_connection`nazwę, a następnie wybierz **przycisk OK**.

    ![Ustawienia aplikacji dla aplikacji funkcji.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. W nowym **sqldb_connection** , wklej parametry połączenia skopiowane w poprzedniej sekcji do pola **lokalnego** i Zamień `{your_username}` i `{your_password}` symbole zastępcze na wartości rzeczywiste. Wybierz pozycję **Wstaw wartość z lokalizacji lokalnej** , aby skopiować zaktualizowaną wartość do pola **zdalnego** , a następnie wybierz przycisk **OK**.

    ![Dodaj ustawienie parametrów połączenia SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Parametry połączenia są przechowywane w postaci zaszyfrowanej na platformie Azure (**zdalna**). Aby zapobiec przeciekom kluczy tajnych, plik projektu Local. Settings. JSON (**Local**) powinien być wykluczony z kontroli źródła, na przykład przy użyciu pliku. gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Dodaj pakiet SqlClient do projektu

Należy dodać pakiet NuGet zawierający bibliotekę SqlClient. Ta biblioteka dostępu do danych jest niezbędna do nawiązania połączenia z bazą danych SQL.

1. Otwórz projekt aplikacji funkcji lokalnych w programie Visual Studio 2019.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Na karcie **Przeglądaj** wyszukaj pozycję ```System.Data.SqlClient``` i po znalezieniu wybierz ją.

1. Na stronie **System. Data. SqlClient** wybierz pozycję wersja `4.5.1` , a następnie kliknij pozycję **Zainstaluj**.

1. Po zakończeniu instalacji przejrzyj zmiany, a następnie kliknij przycisk **OK**, aby zamknąć okno **Podgląd**.

1. W przypadku wyświetlenia okna **Akceptacja licencji** kliknij przycisk **Akceptuję**.

Teraz można dodać kod funkcji w języku C#, który łączy się z SQL Database.

## <a name="add-a-timer-triggered-function"></a>Dodawanie funkcji wyzwalanej czasomierzem

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji funkcji i wybierz polecenie **Dodaj** > **nową funkcję platformy Azure**.

1. Po wybraniu szablonu **Azure Functions** Nazwij nowy element, `DatabaseCleanup.cs` a następnie wybierz pozycję **Dodaj**.

1. W oknie dialogowym **Nowa funkcja platformy Azure** wybierz **wyzwalacz czasomierza** , a następnie kliknij przycisk **OK**. To okno dialogowe tworzy plik kodu dla funkcji wyzwalanej przez czasomierz.

1. Otwórz nowy plik kodu i Dodaj następujące instrukcje using na początku pliku:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Zastąp istniejącą `Run` funkcję następującym kodem:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Ta funkcja jest uruchamiana co 15 sekund, `Status` aby zaktualizować kolumnę na podstawie daty wysyłki. Aby dowiedzieć się więcej na temat wyzwalacza czasomierza, zobacz [wyzwalacz czasomierza dla Azure Functions](functions-bindings-timer.md).

1. Naciśnij klawisz **F5** , aby uruchomić aplikację funkcji. Zostanie otwarte okno wykonywania [Azure Functions Core Tools](functions-develop-local.md) za Visual Studio.

1. O 15 sekund po uruchomieniu funkcja zostanie uruchomiona. Obejrzyj dane wyjściowe i zanotuj liczbę wierszy zaktualizowanych w tabeli **SalesOrderHeader** .

    ![Wyświetl dzienniki funkcji.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Po pierwszym wykonaniu należy zaktualizować 32 wierszy danych. Następujące uruchomienia aktualizują Brak wierszy danych, chyba że wprowadzisz zmiany w danych tabeli SalesOrderHeader, tak aby więcej wierszy zostało wybranych `UPDATE` przez instrukcję.

Jeśli planujesz [opublikowanie tej funkcji](functions-develop-vs.md#publish-to-azure), pamiętaj o zmianie `TimerTrigger` atrybutu na bardziej uzasadniony harmonogram firmy [cronus](functions-bindings-timer.md#ncrontab-expressions) niż co 15 sekund.

## <a name="next-steps"></a>Następne kroki

Następnie Dowiedz się, jak korzystać z programu. Funkcje z Logic Apps do integracji z innymi usługami.

> [!div class="nextstepaction"]
> [Tworzenie funkcji, która integruje się z Logic Apps](functions-twitter-email.md)

Aby uzyskać więcej informacji na temat funkcji, zobacz następujące artykuły:

+ [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dokumentacja dla programistów dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
+ [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.  
