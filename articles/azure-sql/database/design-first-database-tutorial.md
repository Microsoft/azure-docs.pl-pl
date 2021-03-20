---
title: 'Samouczek: projektowanie pierwszej relacyjnej bazy danych przy użyciu programu SSMS'
description: Dowiedz się, jak zaprojektować pierwszą relacyjną bazę danych w Azure SQL Database przy użyciu SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674878"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Samouczek: projektowanie relacyjnej bazy danych w Azure SQL Database przy użyciu programu SSMS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Usługa Azure SQL Database to relacyjna baza danych oferowana jako usługa (DBaaS, database-as-a service) na platformie Microsoft Cloud (Azure). Z tego samouczka dowiesz się, jak przy użyciu witryny Azure Portal i programu [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) wykonać następujące czynności:

> [!div class="checklist"]
>
> - Tworzenie bazy danych przy użyciu Azure Portal *
> - Konfigurowanie reguły zapory bazującej na adresach IP na poziomie serwera za pomocą witryny Azure Portal
> - Nawiązywanie połączenia z bazą danych za pomocą programu SSMS
> - Tworzenie tabel za pomocą programu SSMS
> - Ładowanie zbiorcze danych za pomocą narzędzia BCP
> - Tworzenie zapytań dotyczących danych za pomocą programu SSMS

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

> [!TIP]
> Poniższy moduł Microsoft Learn umożliwia zapoznanie się z bezpłatnymi sposobami tworzenia [i konfigurowania aplikacji ASP.NET, która wysyła zapytanie do Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/), w tym tworzenia prostej bazy danych.
> [!NOTE]
> Na potrzeby tego samouczka używamy Azure SQL Database. Można również użyć puli baz danych w puli elastycznej lub wystąpieniu zarządzanym SQL. Aby uzyskać łączność z wystąpieniem zarządzanym SQL, zobacz te Przewodniki Szybki Start: [Przewodnik Szybki Start dotyczący konfigurowania maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym usługi Azure SQL](../managed-instance/connect-vm-instance-configure.md) i [Szybki Start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym Azure SQL z lokalnego](../managed-instance/point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że zainstalowano następujące elementy:

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (najnowsza wersja)
- [BCP i SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (najnowsza wersja)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-database-in-azure-sql-database"></a>Utwórz pustą bazę danych w Azure SQL Database

Baza danych w Azure SQL Database jest tworzona ze zdefiniowanym zestawem zasobów obliczeniowych i magazynu. Baza danych jest tworzona w [grupie zasobów platformy Azure](../../active-directory-b2c/overview.md) i zarządzana przy użyciu [serwera logicznego SQL](logical-servers.md).

Wykonaj następujące kroki, aby utworzyć pustą bazę danych.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
2. Na stronie **Nowy** wybierz pozycję **Bazy danych** w sekcji Azure Marketplace, a następnie kliknij pozycję **Baza danych SQL** w sekcji **Polecane**.

   ![tworzenie pustej bazy danych](./media/design-first-database-tutorial/create-empty-database.png)

3. Wypełnij formularz **SQL Database** przy użyciu następujących informacji, jak pokazano na poprzedniej ilustracji:

    | Ustawienie       | Sugerowana wartość | Opis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nazwa bazy danych** | *yourDatabase* | Prawidłowe nazwy baz danych opisano w artykule [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych). |
    | **Subskrypcja** | *yourSubscription*  | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
    | **Grupa zasobów** | *yourResourceGroup* | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa). |
    | **Wybierz źródło** | Pusta baza danych | Określa, że ma zostać utworzona pusta baza danych. |

4. Kliknij pozycję **serwer** , aby użyć istniejącego serwera lub utworzyć i skonfigurować nowy serwer. Wybierz istniejący serwer lub kliknij pozycję **Utwórz nowy serwer** i wypełnij formularz **Nowy serwer** przy użyciu następujących informacji:

    | Ustawienie       | Sugerowana wartość | Opis |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nazwa serwera** | Dowolna nazwa unikatowa w skali globalnej | Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa). |
    | **Identyfikator logowania administratora serwera** | Dowolna prawidłowa nazwa | Prawidłowe nazwy identyfikatorów logowania opisano w artykule [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identyfikatory baz danych). |
    | **Password** (Hasło) | Dowolne prawidłowe hasło | Hasło musi mieć co najmniej osiem znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
    | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/). |

    ![tworzenie serwera bazy danych](./media/design-first-database-tutorial/create-database-server.png)

5. Kliknij pozycję **Wybierz**.
6. Kliknij pozycję **Warstwa cenowa**, aby określić warstwę usługi, liczbę jednostek DTU lub rdzeni wirtualnych i ilość miejsca do magazynowania. Możesz przejrzeć opcje liczby jednostek DTU/rdzeni wirtualnych i miejsca do magazynowania dostępne dla poszczególnych warstw usług.

    Po wybraniu warstwy usługi, liczby jednostek DTU lub rdzeni wirtualnych i ilości miejsca do magazynowania kliknij pozycję **Zastosuj**.

7. Wprowadź **sortowanie** dla pustej bazy danych (na potrzeby tego samouczka użyj wartości domyślnej). Aby uzyskać więcej informacji na temat sortowań, zobacz [Sortowania](/sql/t-sql/statements/collations)

8. Teraz, po uzupełnieniu formularza usługi **SQL Database**, kliknij przycisk **Utwórz**, aby aprowizować bazę danych. Może to potrwać kilka minut.

9. Na pasku narzędzi kliknij pozycję **Powiadomienia**, aby monitorować proces wdrażania.

   ![Zrzut ekranu przedstawia menu powiadomienia z wdrożeniem w toku.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Tworzenie reguły zapory bazującej na adresach IP na poziomie serwera

Azure SQL Database tworzy zaporę IP na poziomie serwera. Ta zapora uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze, chyba że reguła zapory zezwala na przechodzenie ruchu z ich adresów IP przez zaporę. Aby włączyć łączność zewnętrzną z bazą danych, należy najpierw dodać regułę zapory IP dla adresu IP (lub zakresu adresów IP). Wykonaj następujące kroki, aby utworzyć [regułę zapory adresów IP na poziomie serwera](firewall-configure.md).

> [!IMPORTANT]
> Usługa Azure SQL Database komunikuje się przez port 1433. Jeśli próbujesz nawiązać połączenie z tą usługą z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z bazą danych, chyba że administrator otworzy port 1433.

1. Po zakończeniu wdrożenia wybierz opcję **bazy danych SQL** z menu Azure Portal lub Wyszukaj i wybierz pozycję *bazy danych SQL* z dowolnej strony.  

1. Wybierz pozycję *yourDatabase* na stronie **bazy danych SQL** . Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną **nazwę serwera** (na przykład `contosodatabaseserver01.database.windows.net` ) i opcje dalszej konfiguracji.

   ![nazwa serwera](./media/design-first-database-tutorial/server-name.png)

1. Skopiuj tę w pełni kwalifikowaną nazwę serwera w celu nawiązania połączenia z serwerem i bazami danych w programie SQL Server Management Studio.

1. Kliknij pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera.

   ![reguła zapory bazująca na adresach IP na poziomie serwera](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Kliknij pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory bazującej na adresach IP. Reguła zapory bazująca na adresach IP może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

1. Kliknij pozycję **Zapisz**. Reguła zapory adresów IP na poziomie serwera jest tworzona dla bieżącego adresu IP otwierającego port 1433 na serwerze.

1. Kliknij przycisk **OK**, a następnie zamknij stronę **Ustawienia zapory**.

Adres IP może teraz być przekazywany przez zaporę IP. Teraz możesz nawiązać połączenie z bazą danych przy użyciu SQL Server Management Studio lub innego wybranego narzędzia. Używaj wcześniej utworzonego konta administratora serwera.

> [!IMPORTANT]
> Domyślnie dostęp przez zaporę IP usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Kliknij przycisk **WYŁ.** na tej stronie, aby wyłączyć tę opcję dla wszystkich usług platformy Azure.

## <a name="connect-to-the-database"></a>Łączenie z bazą danych

Użyj [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) , aby nawiązać połączenie z bazą danych.

1. Otwórz program SQL Server Management Studio.
2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Typ serwera** | Aparat bazy danych | Ta wartość jest wymagana. |
   | **Nazwa serwera** | W pełni kwalifikowana nazwa serwera | Przykład: *yourserver.database.windows.net*. |
   | **Authentication** | Uwierzytelnianie programu SQL Server | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | **Zaloguj się** | Konto administratora serwera | Konto określone podczas tworzenia serwera. |
   | **Password** (Hasło) | Hasło konta administratora serwera | Hasło określone podczas tworzenia serwera. |

   ![łączenie z serwerem](./media/design-first-database-tutorial/connect.png)

3. Kliknij przycisk **Opcje** w oknie dialogowym **Połącz z serwerem**. W sekcji **Nawiązywanie połączenia z bazą danych** wprowadź ciąg *yourDatabase*, aby nawiązać połączenie z tą bazą danych.

    ![nawiązywanie połączenia z bazą danych na serwerze](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. Kliknij przycisk **Połącz**. W programie SSMS zostanie otwarte okno **Eksplorator obiektów**.

5. W **Eksploratorze obiektów** rozwiń pozycję **Bazy danych**, a następnie rozwiń pozycję *yourDatabase*, aby wyświetlić obiekty w przykładowej bazie danych.

   ![obiekty bazy danych](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Tworzenie tabel w bazie danych

Utwórz schemat bazy danych z czterema tabelami, które modelują system zarządzania studentami dla uczelni wyższych, korzystając z języka [Transact-SQL](/sql/t-sql/language-reference):

- Person (Osoba)
- Kurs
- Uczeń
- Środki

Na poniższym diagramie przedstawiono, jak te tabele są ze sobą powiązane. Niektóre z tych tabel odwołują się do kolumn w innych tabelach. Na przykład tabela *Student* odwołuje się do kolumny *PersonId* w tabeli *Person*. Zapoznaj się z tym diagramem, aby zrozumieć, jak tabele w tym samouczku są ze sobą powiązane. Szczegółowe omówienie tworzenia efektywnych tabel bazy danych znajduje się w temacie [Tworzenie efektywnych tabel bazy danych](/previous-versions/tn-archive/cc505842(v=technet.10)). Aby uzyskać informacje dotyczące wybierania typów danych, zobacz [Typy danych](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> W celu tworzenia i projektowania tabel można również użyć [projektanta tabel w programie SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools).

![Relacje między tabelami](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy pozycję *yourDatabase* i wybierz pozycję **Nowe zapytanie**. Zostanie otwarte puste okno zapytania, które jest połączone z Twoją bazą danych.

2. W oknie zapytania wykonaj następujące zapytanie, aby utworzyć cztery tabele w bazie danych:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Tworzenie tabel](./media/design-first-database-tutorial/create-tables.png)

3. Rozwiń węzeł **Tabele** w obszarze bazy danych *yourDatabase* w **Eksploratorze obiektów**, aby wyświetlić utworzone tabele.

   ![Utworzone tabele w programie SSMS](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Ładowanie danych do tabel

1. W folderze Pobrane utwórz folder o nazwie *sampleData* do przechowywania przykładowych danych bazy danych.

2. Kliknij prawym przyciskiem myszy poniższe linki i zapisz je w folderze *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Otwórz okno wiersza polecenia i przejdź do folderu *sampleData*.

4. Wykonaj następujące polecenia, aby wstawić przykładowe dane do tabel, zastępując wartości *server* (serwer), *database* (baza danych), *user* (użytkownik) i *password* (hasło) wartościami odpowiednimi dla Twojego środowiska.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Przykładowe dane zostaną załadowane do utworzonych wcześniej tabel.

## <a name="query-data"></a>Zapytania o dane

Wykonaj następujące zapytania, aby pobrać informacje z tabel bazy danych. Zobacz [Pisanie zapytań SQL](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)), aby dowiedzieć się więcej na temat pisania zapytań SQL. Pierwsze zapytanie sprzęga wszystkie cztery tabele, aby znaleźć wszystkich studentów nauczanych przez wykładowcę „Dominick Pope”, którzy mają ocenę wyższą niż 75%. Drugie zapytanie sprzęga wszystkie cztery tabele i znajduje zajęcia, na które kiedykolwiek zapisał się student „Noe Coleman”.

1. W oknie zapytań programu SQL Server Management Studio wykonaj następujące zapytanie:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. W oknie zapytania wykonaj następujące zapytanie:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono wiele podstawowych zadań bazy danych. W tym samouczku omówiono:

> [!div class="checklist"]
>
> - Tworzenie bazy danych przy użyciu Azure Portal *
> - Konfigurowanie reguły zapory bazującej na adresach IP na poziomie serwera za pomocą witryny Azure Portal
> - Nawiązywanie połączenia z bazą danych za pomocą programu SSMS
> - Tworzenie tabel za pomocą programu SSMS
> - Ładowanie zbiorcze danych za pomocą narzędzia BCP
> - Tworzenie zapytań dotyczących danych za pomocą programu SSMS

Przejdź do następnego samouczka, aby dowiedzieć się, jak projektować bazy danych przy użyciu programu Visual Studio i języka C#.

> [!div class="nextstepaction"]
> [Projektowanie relacyjnej bazy danych w Azure SQL Database C# i ADO.NET](design-first-database-csharp-tutorial.md)