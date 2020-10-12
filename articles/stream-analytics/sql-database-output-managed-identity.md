---
title: Dostęp do Azure SQL Database-Azure Stream Analytics za pomocą tożsamości zarządzanych
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania Azure Stream Analytics w danych wyjściowych usługi Azure SQL DB.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 26644d42e0e51d59c6c28daaba5447a65a43b6a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460645"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Uzyskiwanie dostępu do Azure SQL Database z zadania Azure Stream Analytics za pomocą tożsamości zarządzanych (wersja zapoznawcza)

Azure Stream Analytics obsługuje [uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla Azure SQL Database ujścia danych wyjściowych. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartych na użytkownikach, takich jak potrzeba ponownego uwierzytelnienia ze względu na zmiany hasła lub wygaśnięcia tokenów użytkowników, które wystąpiły co 90 dni. Po usunięciu potrzeby ręcznego uwierzytelniania Stream Analytics wdrożenia mogą być w pełni zautomatyzowane.

Zarządzana tożsamość to zarządzana aplikacja zarejestrowana w Azure Active Directory, która reprezentuje daną Stream Analytics zadanie. Aplikacja zarządzana służy do uwierzytelniania w zasobach przeznaczonych do użycia. W tym artykule pokazano, jak włączyć zarządzaną tożsamość dla Azure SQL Database danych wyjściowych zadania Stream Analytics za pomocą Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do tej funkcji wymagane są następujące elementy:

- Zadanie Azure Stream Analytics.

- Zasób Azure SQL Database.

## <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Najpierw utwórz zarządzaną tożsamość dla zadania Azure Stream Analytics.

1. W [Azure Portal](https://portal.azure.com)otwórz zadanie Azure Stream Analytics.

1. W menu nawigacji po lewej stronie wybierz pozycję **zarządzana tożsamość** znajdującą się w obszarze **Konfiguruj**. Następnie zaznacz pole wyboru obok **opcji Użyj tożsamości zarządzanej przypisanej do systemu** i wybierz pozycję **Zapisz**.

   ![Wybierz tożsamość zarządzaną przypisaną przez system](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Nazwa główna usługi dla tożsamości zadania Stream Analytics jest tworzona w Azure Active Directory. Cykl życia nowo utworzonej tożsamości jest zarządzany przez platformę Azure. Po usunięciu zadania Stream Analytics skojarzona tożsamość (czyli nazwa główna usługi) zostanie automatycznie usunięta przez platformę Azure. 

1. Podczas zapisywania konfiguracji identyfikator obiektu (OID) jednostki usługi jest wymieniony jako identyfikator podmiotu zabezpieczeń, jak pokazano poniżej: 

   ![Identyfikator obiektu pokazywany jako identyfikator podmiotu zabezpieczeń](./media/sql-db-output-managed-identity/principal-id.png)

   Nazwa główna usługi ma taką samą nazwę jak zadanie Stream Analytics. Na przykład jeśli nazwa zadania to *MyASAJob*, nazwa główna usługi również jest *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Wybierz administratora Active Directory

Po utworzeniu tożsamości zarządzanej wybierz administratora Active Directory.

1. Przejdź do zasobu Azure SQL Database i wybierz SQL Server, w którym znajduje się baza danych. Nazwę SQL Server obok pozycji *Nazwa serwera* można znaleźć na stronie Przegląd zasobów. 

1. W obszarze **Ustawienia**wybierz pozycję **administrator Active Directory** . Następnie wybierz pozycję **Ustaw administratora**. 

   ![Strona administracyjna Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Na stronie Administrator Active Directory Wyszukaj użytkownika lub grupę, aby być administratorem SQL Server i kliknij przycisk **Wybierz**.

   ![Dodaj administratora Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

   Na stronie Administrator Active Directory są wyświetlane wszystkie elementy członkowskie i grupy Active Directory. Nie można wybrać użytkowników lub grup, które są wyszarzone, ponieważ nie są one obsługiwane jako administratorzy Azure Active Directory. Zapoznaj się z listą obsługiwanych administratorów w sekcji **Azure Active Directory funkcje i ograniczenia**   dotyczące [używania Azure Active Directory uwierzytelniania na potrzeby uwierzytelniania w usłudze SQL Database lub Azure Synapse](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do portalu i nie jest stosowana na serwerze SQL. Wybrany użytkownik lub grupa jest również użytkownikiem, który będzie mógł utworzyć **użytkownika zawartej bazy danych** w następnej sekcji.

1. Na stronie **administrator Active Directory** wybierz pozycję **Zapisz** . Proces zmieniania administratora trwa kilka minut.

   Po skonfigurowaniu administratora Azure Active Directory Nowa nazwa administratora (użytkownik lub Grupa) nie może być obecna w wirtualnej podstawowej bazie danych jako użytkownik uwierzytelniania SQL Server. Jeśli jest obecny, konfiguracja administratora Azure Active Directory nie powiedzie się i wycofa jego tworzenie, wskazując, że administrator (nazwa) już istnieje. Ponieważ użytkownik uwierzytelniania SQL Server nie jest częścią Azure Active Directory, wszelkie wysiłki związane z nawiązywaniem połączenia z serwerem za pomocą uwierzytelniania Azure Active Directory nie powiedzie się. 

## <a name="create-a-contained-database-user"></a>Tworzenie użytkownika zawartej bazy danych

Następnie utworzysz użytkownika zawartej bazy danych w SQL Database, który jest mapowany do Azure Active Directory tożsamości. Użytkownik zawartej bazy danych nie ma nazwy logowania podstawowej bazy danych, ale mapuje ją na tożsamość w katalogu, który jest skojarzony z bazą danych. Tożsamość Azure Active Directory może być pojedynczym kontem użytkownika lub grupą. W takim przypadku należy utworzyć użytkownika zawartej bazy danych dla zadania Stream Analytics. 

1. Połącz się z SQL Database przy użyciu SQL Server Management Studio. **Nazwa użytkownika** to Azure Active Directory użytkownika z uprawnieniami **Zmiana dowolnego użytkownika** . Przykładem jest administrator ustawiony na SQL Server. Użyj **Azure Active Directory — uniwersalne z** uwierzytelnianiem MFA. 

   ![Ustanawianie połączenia z programem SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Nazwa serwera `<SQL Server name>.database.windows.net` może się różnić w różnych regionach. Na przykład region Chin powinien używać `<SQL Server name>.database.chinacloudapi.cn` .
 
   Możesz określić konkretny SQL Database, przechodząc do **opcji opcje > właściwości połączenia > nawiązać połączenie z bazą danych**.  

   ![Właściwości połączenia SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Po nawiązaniu połączenia po raz pierwszy może wystąpić następujące okno:

   ![Okno nowej reguły zapory](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Jeśli tak, przejdź do zasobu SQL Server na Azure Portal. W sekcji **zabezpieczenia** Otwórz stronę **zapory i Sieć wirtualna** . 
   1. Dodaj nową regułę z dowolną nazwą reguły.
   1. Użyj adresu *IP z okna* **Nowa reguła zapory** dla *początkowego adresu IP*.
   1. Użyj adresu *IP w oknie* **Nowa reguła zapory** dla *końcowego adresu IP*. 
   1. Wybierz pozycję **Zapisz** i spróbuj ponownie nawiązać połączenie z SQL Server Management Studio. 

1. Po nawiązaniu połączenia Utwórz użytkownika zawartej bazy danych. Następujące polecenie SQL tworzy użytkownika zawartej bazy danych, który ma taką samą nazwę jak zadanie Stream Analytics. Pamiętaj o uwzględnieniu nawiasów wokół *ASA_JOB_NAME*. Użyj następującej składni języka T-SQL i uruchom zapytanie. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. Aby uzyskać Azure Active Directory firmy Microsoft w celu sprawdzenia, czy zadanie Stream Analytics ma dostęp do SQL Database, musimy udzielić Azure Active Directory uprawnienia do komunikacji z bazą danych. W tym celu przejdź do strony "zapory i Sieć wirtualna" w Azure Portal ponownie i Włącz opcję "Zezwalaj na dostęp do tego serwera usługom i zasobom platformy Azure". 

   ![Zapora i Sieć wirtualna](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Udziel uprawnień Stream Analytics zadania

Po utworzeniu użytkownika zawartej bazy danych i uzyskaniu dostępu do usług platformy Azure w portalu zgodnie z opisem w poprzedniej sekcji zadanie Stream Analytics ma uprawnienia od tożsamości zarządzanej do **nawiązywania połączenia** z zasobem SQL Database za pośrednictwem tożsamości zarządzanej. Zaleca się przyznanie uprawnień SELECT i INSERT do zadania Stream Analytics, ponieważ będą one potrzebne później w przepływie pracy Stream Analytics. Uprawnienie **Wybierz** umożliwia przetestowanie połączenia z tabelą w SQL Database. Uprawnienie **INSERT** umożliwia testowanie kompleksowych zapytań Stream Analytics po skonfigurowaniu danych wejściowych i SQL Database danych wyjściowych. Można przyznać te uprawnienia do zadania Stream Analytics przy użyciu SQL Server Management Studio. Aby uzyskać więcej informacji, zobacz temat GRANT (Transact-SQL) Reference.

Aby udzielić uprawnienia tylko do określonej tabeli lub obiektu w bazie danych, należy użyć następującej składni języka T-SQL i uruchomić zapytanie. 

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME; 
```

Alternatywnie możesz kliknąć prawym przyciskiem myszy bazę danych SQL w SQL Server Management Studio i wybrać pozycję **właściwości > uprawnienia**. Z menu uprawnienia można zobaczyć wcześniej dodane zadanie Stream Analytics i można ręcznie udzielić lub odmówić uprawnień.

## <a name="create-an-azure-sql-database-output"></a>Tworzenie danych wyjściowych Azure SQL Database

Teraz, gdy zarządzana tożsamość została skonfigurowana, możesz dodać Azure SQL Database jako dane wyjściowe do zadania Stream Analytics.

Upewnij się, że utworzono tabelę w SQL Database z odpowiednim schematem wyjściowym. Nazwa tej tabeli jest jedną z wymaganych właściwości, które muszą zostać wypełnione podczas dodawania SQL Database danych wyjściowych do zadania Stream Analytics. Upewnij się również, że zadanie ma uprawnienia **SELECT** i **INSERT** służące do testowania połączenia i uruchamiania zapytań Stream Analytics. Jeśli jeszcze tego nie zrobiono, zapoznaj się z sekcją [udziel Stream Analytics uprawnienia zadania](#grant-stream-analytics-job-permissions) . 

1. Wróć do zadania Stream Analytics i przejdź do strony dane **wyjściowe** w obszarze **topologia zadania**. 

1. Wybierz pozycję **dodaj > SQL Database**. W oknie właściwości danych wyjściowych ujścia danych wyjściowych SQL Database wybierz pozycję **zarządzana tożsamość** z listy rozwijanej tryb uwierzytelniania.

1. Wypełnij pozostałe właściwości. Aby dowiedzieć się więcej na temat tworzenia SQL Database danych wyjściowych, zobacz [tworzenie SQL Database danych wyjściowych przy użyciu Stream Analytics](sql-database-output.md). Gdy skończysz, wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki

* [Poznanie danych wyjściowych z Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics dane wyjściowe do Azure SQL Database](stream-analytics-sql-output-perf.md)
