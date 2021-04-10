---
title: Uzyskaj dostęp do Azure SQL Database lub usługi Azure Synapse Analytics przy użyciu tożsamości zarządzanych — Azure Stream Analytics
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania Azure Stream Analytics do Azure SQL Database lub danych wyjściowych usługi Azure Synapse Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487920"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Korzystanie z tożsamości zarządzanych do uzyskiwania dostępu do Azure SQL Database lub analizy Synapse Azure z poziomu zadania Azure Stream Analytics (wersja zapoznawcza)

Azure Stream Analytics obsługuje [uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla Azure SQL Database i ujścia danych wyjściowych usługi Azure Synapse Analytics. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartych na użytkownikach, takich jak potrzeba ponownego uwierzytelnienia ze względu na zmiany hasła lub wygaśnięcia tokenów użytkowników, które wystąpiły co 90 dni. Po usunięciu potrzeby ręcznego uwierzytelniania Stream Analytics wdrożenia mogą być w pełni zautomatyzowane.

Zarządzana tożsamość to zarządzana aplikacja zarejestrowana w Azure Active Directory, która reprezentuje daną Stream Analytics zadanie. Aplikacja zarządzana służy do uwierzytelniania w zasobach przeznaczonych do użycia. W tym artykule pokazano, jak włączyć zarządzaną tożsamość dla Azure SQL Database lub danych wyjściowych analizy usługi Azure Synapse dla Stream Analytics zadania za pomocą Azure Portal.

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono kroki niezbędne do połączenia zadania Stream Analytics z Azure SQL Database lub pulą SQL Synapse Analytics przy użyciu trybu uwierzytelniania tożsamości zarządzanej. 

- Najpierw utworzysz tożsamość zarządzaną przypisaną przez system dla zadania Stream Analytics. To jest tożsamość zadania w Azure Active Directory.  

- Dodaj administratora Active Directory do programu SQL Server lub obszaru roboczego Synapse, który umożliwia uwierzytelnianie usługi Azure AD (tożsamość zarządzana) dla tego zasobu.

- Następnie utwórz zawartego użytkownika reprezentujący tożsamość zadania Stream Analytics w bazie danych. Za każdym razem, gdy zadanie Stream Analytics współdziała z usługą SQL DB lub Synapse zasobem bazy danych SQL, jest to tożsamość, do której odwołuje się, aby sprawdzić, jakie są uprawnienia Stream Analytics zadania.

- Udziel uprawnień do zadania Stream Analytics, aby uzyskać dostęp do pul SQL SQL Database lub Synapse.

- Na koniec Dodaj Azure SQL Database/Synapse Analytics jako dane wyjściowe w zadaniu Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Do korzystania z tej funkcji wymagane są następujące elementy:

- Zadanie Azure Stream Analytics.

- Zasób Azure SQL Database.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Do korzystania z tej funkcji wymagane są następujące elementy:

- Zadanie Azure Stream Analytics.

- Pula SQL usługi Azure Synapse Analytics.

- Konto usługi Azure Storage, które jest [skonfigurowane na potrzeby zadania Stream Analytics](azure-synapse-analytics-output.md).

- Uwaga: Stream Analytics plik MSI magazynu kont zintegrowany z programem Synapse SQL MSI jest obecnie niedostępny.

---

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

1. Przejdź do Azure SQL Database lub zasobu puli SQL usługi Azure Synapse Analytics, a następnie wybierz obszar roboczy SQL Server lub Synapse, w którym znajduje się zasób. Łącze do tych informacji można znaleźć na stronie Przegląd zasobów obok *nazwy serwera* lub *obszaru roboczego*.

1. W obszarze **Ustawienia** w obszarze roboczym SQL Server i Synapse wybierz **Active Directory** pozycję Administrator lub **administrator Active Directory SQL** . Następnie wybierz pozycję **Ustaw administratora**.

   ![Strona administracyjna Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Na stronie Administrator Active Directory Wyszukaj użytkownika lub grupę, aby być administratorem SQL Server i kliknij przycisk **Wybierz**. Będzie to użytkownik, który będzie mógł utworzyć **użytkownika zawartej bazy danych** w następnej sekcji.

   ![Dodaj administratora Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

   Na stronie Administrator Active Directory są wyświetlane wszystkie elementy członkowskie i grupy Active Directory. Nie można wybrać szarych użytkowników lub grup, ponieważ nie są one obsługiwane jako administratorzy Azure Active Directory. Zapoznaj się z listą obsługiwanych administratorów w sekcji **Azure Active Directory funkcje i ograniczenia**   dotyczące [używania Azure Active Directory uwierzytelniania na potrzeby uwierzytelniania w usłudze SQL Database lub Azure Synapse](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. Na stronie **administrator Active Directory** wybierz pozycję **Zapisz** . Proces zmieniania administratora trwa kilka minut.

## <a name="create-a-contained-database-user"></a>Tworzenie użytkownika zawartej bazy danych

Następnie utworzysz użytkownika zawartej bazy danych w bazie danych Azure SQL lub Azure Synapse, która jest mapowana na tożsamość Azure Active Directory. Użytkownik zawartej bazy danych nie ma nazwy logowania podstawowej bazy danych, ale mapuje ją na tożsamość w katalogu, który jest skojarzony z bazą danych. Tożsamość Azure Active Directory może być pojedynczym kontem użytkownika lub grupą. W takim przypadku należy utworzyć użytkownika zawartej bazy danych dla zadania Stream Analytics. 

Aby uzyskać więcej informacji, zapoznaj się z następującym artykułem dotyczącym usługi Azure AD Integration: [uwierzytelnianie uniwersalne przy użyciu SQL Database i usługi Azure Synapse Analytics (Obsługa programu SSMS)](../azure-sql/database/authentication-mfa-ssms-overview.md)

1. Nawiąż połączenie z bazą danych Azure SQL lub Azure Synapse Database przy użyciu SQL Server Management Studio. **Nazwa użytkownika** to Azure Active Directory użytkownika z uprawnieniami **Zmiana dowolnego użytkownika** . Przykładem jest administrator ustawiony na SQL Server. Użyj **Azure Active Directory — uniwersalne z** uwierzytelnianiem MFA. 

   ![Ustanawianie połączenia z programem SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Nazwa serwera `<SQL Server name>.database.windows.net` może się różnić w różnych regionach. Na przykład region Chin powinien używać `<SQL Server name>.database.chinacloudapi.cn` .
 
   Możesz określić konkretną bazę danych SQL Azure lub usługę Azure Synapse, przechodząc do **opcji opcje > właściwości połączenia > Połącz z bazą danych**.  

   ![Właściwości połączenia SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Po nawiązaniu połączenia po raz pierwszy może wystąpić następujące okno:

   ![Okno nowej reguły zapory](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. W takim przypadku przejdź do zasobu SQL Server/Synapse obszaru roboczego na Azure Portal. W sekcji **zabezpieczenia** Otwórz stronę **zapory i Sieć wirtualna/zapory** . 
   1. Dodaj nową regułę z dowolną nazwą reguły.
   1. Użyj adresu *IP z okna* **Nowa reguła zapory** dla *początkowego adresu IP*.
   1. Użyj adresu *IP w oknie* **Nowa reguła zapory** dla *końcowego adresu IP*. 
   1. Wybierz pozycję **Zapisz** i spróbuj ponownie nawiązać połączenie z SQL Server Management Studio. 

1. Po nawiązaniu połączenia Utwórz użytkownika zawartej bazy danych. Następujące polecenie SQL tworzy użytkownika zawartej bazy danych, który ma taką samą nazwę jak zadanie Stream Analytics. Pamiętaj o uwzględnieniu nawiasów wokół *ASA_JOB_NAME*. Użyj następującej składni języka T-SQL i uruchom zapytanie. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    Aby sprawdzić, czy pomyślnie dodano użytkownika zawartej bazy danych, uruchom następujące polecenie w programie SSMS w obszarze odnoszące się do bazy danych i sprawdź, czy *ASA_JOB_NAME* znajduje się w kolumnie "nazwa".

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. Aby uzyskać Azure Active Directory firmy Microsoft w celu sprawdzenia, czy zadanie Stream Analytics ma dostęp do SQL Database, musimy udzielić Azure Active Directory uprawnienia do komunikacji z bazą danych. W tym celu przejdź do strony "zapory i Sieć wirtualna"/"zapory w Azure Portal ponownie, a następnie włącz opcję" Zezwalaj na dostęp do tego serwera/obszaru roboczego usług platformy Azure i zasobów ".

   ![Zapora i Sieć wirtualna](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Udziel uprawnień Stream Analytics zadania

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Po utworzeniu użytkownika zawartej bazy danych i uzyskaniu dostępu do usług platformy Azure w portalu zgodnie z opisem w poprzedniej sekcji zadanie Stream Analytics ma uprawnienia od tożsamości zarządzanej do **nawiązywania połączenia** z zasobem usługi Azure SQL Database za pośrednictwem tożsamości zarządzanej. Zaleca się przyznanie uprawnień **SELECT** i **INSERT** do zadania Stream Analytics, ponieważ będą one potrzebne później w przepływie pracy Stream Analytics. Uprawnienie **Wybierz** umożliwia przetestowanie połączenia z tabelą w usłudze Azure SQL Database. Uprawnienie **Wstawianie** umożliwia testowanie kompleksowych zapytań Stream Analytics po skonfigurowaniu danych wejściowych i danych wyjściowych usługi Azure SQL Database.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Po utworzeniu użytkownika zawartej bazy danych i uzyskaniu dostępu do usług platformy Azure w portalu zgodnie z opisem w poprzedniej sekcji zadanie Stream Analytics ma uprawnienia od tożsamości zarządzanej do **łączenia** się z zasobem usługi Azure Synapse Database za pośrednictwem tożsamości zarządzanej. Zalecamy, aby dodatkowo przyznać uprawnienia do **operacji zbiorczych** **SELECT**, **INSERT** i Administruj do zadania Stream Analytics, ponieważ będą one potrzebne później w przepływie pracy Stream Analytics. Uprawnienie **Wybierz** umożliwia przetestowanie połączenia z tabelą w bazie danych usługi Azure Synapse. Uprawnienia  do **operacji zbiorczych wstawiania i administrowania bazami danych** umożliwiają testowanie kompleksowych zapytań Stream Analytics po skonfigurowaniu danych wejściowych i danych wyjściowych usługi Azure Synapse Database.

Aby udzielić uprawnienia do **zarządzania operacjami zbiorczymi dla bazy danych** , należy udzielić wszystkim uprawnieniam, które są oznaczone jako **kontrolki** w ramach [domyślnych uprawnień bazy danych](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) do zadania Stream Analytics. To uprawnienie jest wymagane, ponieważ zadanie Stream Analytics wykonuje instrukcję **copy** , która wymaga [zarządzania operacjami zbiorczymi bazy danych i wstawiania](/sql/t-sql/statements/copy-into-transact-sql).

---

Można przyznać te uprawnienia do zadania Stream Analytics przy użyciu SQL Server Management Studio. Aby uzyskać więcej informacji, zobacz temat GRANT (Transact-SQL) Reference.

Aby udzielić uprawnienia tylko do określonej tabeli lub obiektu w bazie danych, należy użyć następującej składni języka T-SQL i uruchomić zapytanie. 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Alternatywnie możesz kliknąć prawym przyciskiem myszy bazę danych Azure SQL lub Azure Synapse Database w SQL Server Management Studio i wybrać pozycję **właściwości > uprawnienia**. Z menu uprawnienia można zobaczyć wcześniej dodane zadanie Stream Analytics i można ręcznie udzielić lub odmówić uprawnień.

Aby zapoznać się ze wszystkimi uprawnieniami dodanymi do użytkownika *ASA_JOB_NAME* , uruchom następujące polecenie w programie SSMS w ramach powiązanej bazy danych: 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Utwórz Azure SQL Database lub dane wyjściowe usługi Azure Synapse

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Teraz, gdy zarządzana tożsamość została skonfigurowana, możesz dodać Azure SQL Database lub dane wyjściowe usługi Azure Synapse do zadania Stream Analytics.

Upewnij się, że utworzono tabelę w SQL Database z odpowiednim schematem wyjściowym. Nazwa tej tabeli jest jedną z wymaganych właściwości, które muszą zostać wypełnione podczas dodawania SQL Database danych wyjściowych do zadania Stream Analytics. Upewnij się również, że zadanie ma uprawnienia **SELECT** i **INSERT** służące do testowania połączenia i uruchamiania zapytań Stream Analytics. Jeśli jeszcze tego nie zrobiono, zapoznaj się z sekcją [udziel Stream Analytics uprawnienia zadania](#grant-stream-analytics-job-permissions) . 

1. Wróć do zadania Stream Analytics i przejdź do strony dane **wyjściowe** w obszarze **topologia zadania**. 

1. Wybierz pozycję **dodaj > SQL Database**. W oknie właściwości danych wyjściowych ujścia danych wyjściowych SQL Database wybierz pozycję **zarządzana tożsamość** z listy rozwijanej tryb uwierzytelniania.

1. Wypełnij pozostałe właściwości. Aby dowiedzieć się więcej na temat tworzenia SQL Database danych wyjściowych, zobacz [tworzenie SQL Database danych wyjściowych przy użyciu Stream Analytics](sql-database-output.md). Gdy skończysz, wybierz pozycję **Zapisz**.

1. Po kliknięciu przycisku **Zapisz** test połączenia do zasobu powinien zostać wyzwolony automatycznie. Po pomyślnym zakończeniu zadania Stream Analytics zostało pomyślnie skonfigurowane w celu nawiązania połączenia Azure SQL Database lub Synapse SQL Database przy użyciu trybu uwierzytelniania tożsamości zarządzanej. 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Teraz, gdy zarządzana tożsamość i konto magazynu są skonfigurowane, możesz dodać Azure SQL Database lub dane wyjściowe usługi Azure Synapse do zadania Stream Analytics.

Upewnij się, że utworzono tabelę w bazie danych Azure Synapse z odpowiednim schematem wyjściowym. Nazwa tej tabeli jest jedną z wymaganych właściwości, które muszą zostać wypełnione po dodaniu danych wyjściowych usługi Azure Synapse do zadania Stream Analytics. Upewnij się również, że zadanie ma uprawnienia **SELECT** i **INSERT** służące do testowania połączenia i uruchamiania zapytań Stream Analytics. Jeśli jeszcze tego nie zrobiono, zapoznaj się z sekcją [udziel Stream Analytics uprawnienia zadania](#grant-stream-analytics-job-permissions) .

1. Wróć do zadania Stream Analytics i przejdź do strony dane **wyjściowe** w obszarze **topologia zadania**.

1. Wybierz pozycję **dodaj > Azure Synapse Analytics**. W oknie właściwości danych wyjściowych ujścia danych wyjściowych SQL Database wybierz pozycję **zarządzana tożsamość** z listy rozwijanej tryb uwierzytelniania.

1. Wypełnij pozostałe właściwości. Aby dowiedzieć się więcej na temat tworzenia danych wyjściowych usługi Azure Synapse, zobacz [Azure Synapse Analytics Output from Azure Stream Analytics](azure-synapse-analytics-output.md). Gdy skończysz, wybierz pozycję **Zapisz**.

1. Po kliknięciu przycisku **Zapisz** test połączenia do zasobu powinien zostać wyzwolony automatycznie. Po pomyślnym zakończeniu pracy możesz teraz przystąpić do dalszego korzystania z tożsamości zarządzanej dla zasobu usługi Azure Synapse Analytics przy użyciu Stream Analytics. 

---

## <a name="remove-managed-identity"></a>Usuwanie tożsamości zarządzanej

Tożsamość zarządzana utworzona dla zadania Stream Analytics jest usuwana tylko po usunięciu zadania. Nie można usunąć tożsamości zarządzanej bez usuwania zadania. Jeśli nie chcesz już używać tożsamości zarządzanej, możesz zmienić metodę uwierzytelniania dla danych wyjściowych. Tożsamość zarządzana będzie nadal istnieć do momentu usunięcia zadania i zostanie użyta, jeśli zdecydujesz się ponownie użyć uwierzytelniania tożsamości zarządzanej.

## <a name="next-steps"></a>Następne kroki

* [Poznanie danych wyjściowych z Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics dane wyjściowe do Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Dane wyjściowe usługi Azure Synapse Analytics z Azure Stream Analytics](azure-synapse-analytics-output.md)
