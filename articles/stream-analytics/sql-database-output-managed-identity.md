---
title: Dostęp do Azure SQL Database-Azure Stream Analytics za pomocą tożsamości zarządzanych
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania Azure Stream Analytics w danych wyjściowych usługi Azure SQL DB.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598241"
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

1. Na stronie **administrator Active Directory** wybierz pozycję **Zapisz** . Proces zmieniania administratora trwa kilka minut.  

## <a name="create-a-database-user"></a>Tworzenie użytkownika bazy danych

Następnie utworzysz użytkownika zawartej bazy danych w SQL Database, który jest mapowany do Azure Active Directory tożsamości. Użytkownik zawartej bazy danych nie ma nazwy logowania dla bazy danych Master, ale mapuje ją na tożsamość w katalogu, który jest skojarzony z bazą danych. Tożsamość Azure Active Directory może być pojedynczym kontem użytkownika lub grupą. W takim przypadku należy utworzyć użytkownika zawartej bazy danych dla zadania Stream Analytics. 

1. Nawiąż połączenie z bazą danych SQL przy użyciu SQL Server Management Studio. **Nazwa użytkownika** to Azure Active Directory użytkownika z uprawnieniami **Zmiana dowolnego użytkownika** . Przykładem jest administrator ustawiony na SQL Server. Użyj **Azure Active Directory — uniwersalne z** uwierzytelnianiem MFA. 

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

## <a name="grant-stream-analytics-job-permissions"></a>Udziel uprawnień Stream Analytics zadania

Zadanie Stream Analytics ma uprawnienia od tożsamości zarządzanej do **łączenia** się z zasobem SQL Database. Najbardziej prawdopodobną przyczyną jest umożliwienie Stream Analytics zadania uruchamiania poleceń, takich jak **SELECT**. Można przyznać te uprawnienia do zadania Stream Analytics przy użyciu SQL Server Management Studio. Aby uzyskać więcej informacji, zobacz temat [Grant (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) Reference.

Alternatywnie możesz kliknąć prawym przyciskiem myszy bazę danych SQL w SQL Server Management Studio i wybrać pozycję **właściwości > uprawnienia**. Z menu uprawnienia można zobaczyć wcześniej dodane zadanie Stream Analytics i można ręcznie udzielić lub odmówić uprawnień.

## <a name="create-an-azure-sql-database-output"></a>Tworzenie danych wyjściowych Azure SQL Database

Teraz, gdy zarządzana tożsamość została skonfigurowana, możesz dodać Azure SQL Database jako dane wyjściowe do zadania Stream Analytics.

1. Wróć do zadania Stream Analytics i przejdź do strony dane **wyjściowe** w obszarze **topologia zadania**. 

1. Wybierz pozycję **dodaj > SQL Database**. W oknie właściwości danych wyjściowych ujścia danych wyjściowych SQL Database wybierz pozycję **zarządzana tożsamość** z listy rozwijanej tryb uwierzytelniania.

1. Wypełnij pozostałe właściwości. Aby dowiedzieć się więcej na temat tworzenia SQL Database danych wyjściowych, zobacz [tworzenie SQL Database danych wyjściowych przy użyciu Stream Analytics](stream-analytics-define-outputs.md#sql-database). Gdy skończysz, wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki

* [Poznanie danych wyjściowych z Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics dane wyjściowe do Azure SQL Database](stream-analytics-sql-output-perf.md)
