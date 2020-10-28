---
title: 'Projektowanie pierwszej relacyjnej bazy danych C #'
description: Dowiedz się, jak zaprojektować pierwszą relacyjną bazę danych w Azure SQL Database za pomocą języka C# przy użyciu ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 4b3235f457f1c6475c18045886c49d3dd2ca2242
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671172"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>Samouczek: projektowanie relacyjnej bazy danych w Azure SQL Database C&#x23; i ADO.NET
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Usługa Azure SQL Database to relacyjna baza danych oferowana jako usługa (DBaaS, database-as-a service) na platformie Microsoft Cloud (Azure). Z tego samouczka dowiesz się, jak przy użyciu witryny Azure Portal i narzędzia ADO.NET w programie Visual Studio wykonać następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie bazy danych przy użyciu Azure Portal
> * Konfigurowanie reguły zapory bazującej na adresach IP na poziomie serwera za pomocą witryny Azure Portal
> * Nawiązywanie połączenia z bazą danych przy użyciu narzędzia ADO.NET i programu Visual Studio
> * Tworzenie tabel za pomocą narzędzia ADO.NET
> * Wstawianie, aktualizowanie i usuwanie danych za pomocą narzędzia ADO.NET
> * Wykonywanie zapytań względem danych za pomocą narzędzia ADO.NET

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

> [!TIP]
> Poniższy moduł Microsoft Learn umożliwia zapoznanie się z bezpłatnymi sposobami tworzenia [i konfigurowania aplikacji ASP.NET, która wysyła zapytanie do Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/), w tym tworzenia prostej bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

Instalacja programu [Visual Studio 2019](https://www.visualstudio.com/downloads/) lub nowszego.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Utwórz pustą bazę danych w Azure SQL Database

Baza danych w Azure SQL Database jest tworzona ze zdefiniowanym zestawem zasobów obliczeniowych i magazynu. Baza danych jest tworzona w [grupie zasobów platformy Azure](../../active-directory-b2c/overview.md) i zarządzana przy użyciu [serwera logicznego SQL](logical-servers.md).

Wykonaj następujące kroki, aby utworzyć pustą bazę danych.

1. Kliknij pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
2. Na stronie **Nowy** wybierz pozycję **Bazy danych** w sekcji Azure Marketplace, a następnie kliknij pozycję **Baza danych SQL** w sekcji **Polecane** .

   ![tworzenie pustej bazy danych](./media/design-first-database-csharp-tutorial/create-empty-database.png)

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
    | **Hasło** | Dowolne prawidłowe hasło | Hasło musi mieć co najmniej osiem znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
    | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/). |

    ![tworzenie serwera bazy danych](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. Kliknij pozycję **Wybierz** .
6. Kliknij pozycję **Warstwa cenowa** , aby określić warstwę usługi, liczbę jednostek DTU lub rdzeni wirtualnych i ilość miejsca do magazynowania. Możesz przejrzeć opcje liczby jednostek DTU/rdzeni wirtualnych i miejsca do magazynowania dostępne dla poszczególnych warstw usług.

    Po wybraniu warstwy usługi, liczby jednostek DTU lub rdzeni wirtualnych i ilości miejsca do magazynowania kliknij pozycję **Zastosuj** .

7. Wprowadź **sortowanie** dla pustej bazy danych (na potrzeby tego samouczka użyj wartości domyślnej). Aby uzyskać więcej informacji na temat sortowań, zobacz [Sortowania](/sql/t-sql/statements/collations)

8. Teraz, po uzupełnieniu formularza usługi **SQL Database** , kliknij przycisk **Utwórz** , aby aprowizować bazę danych. Może to potrwać kilka minut.

9. Na pasku narzędzi kliknij pozycję **Powiadomienia** , aby monitorować proces wdrażania.

   ![Zrzut ekranu przedstawia powiadomienia w Azure Portal z wdrożeniem w toku.](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Tworzenie reguły zapory bazującej na adresach IP na poziomie serwera

SQL Database tworzy zaporę IP na poziomie serwera. Ta zapora uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze, chyba że reguła zapory zezwala na przechodzenie ruchu z ich adresów IP przez zaporę. Aby włączyć łączność zewnętrzną z bazą danych, należy najpierw dodać regułę zapory IP dla adresu IP (lub zakresu adresów IP). Wykonaj następujące kroki, aby utworzyć [regułę zapory adresów IP na poziomie serwera](firewall-configure.md).

> [!IMPORTANT]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z tą usługą z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z bazą danych, chyba że administrator otworzy port 1433.

1. Po zakończeniu wdrażania kliknij pozycję **bazy danych SQL** w menu po lewej stronie, a następnie kliknij pozycję *yourDatabase* na stronach **bazy danych SQL** . Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną **nazwę serwera** (na przykład *yourserver.database.windows.net* ) i opcje dalszej konfiguracji.

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera w celu nawiązania połączenia z serwerem i bazami danych w programie SQL Server Management Studio.

   ![nazwa serwera](./media/design-first-database-csharp-tutorial/server-name.png)

3. Kliknij pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera.

   ![reguła zapory bazująca na adresach IP na poziomie serwera](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. Kliknij pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory bazującej na adresach IP. Reguła zapory bazująca na adresach IP może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

5. Kliknij pozycję **Zapisz** . Reguła zapory adresów IP na poziomie serwera jest tworzona dla bieżącego adresu IP otwierającego port 1433 na serwerze.

6. Kliknij przycisk **OK** , a następnie zamknij stronę **Ustawienia zapory** .

Adres IP może teraz być przekazywany przez zaporę IP. Teraz możesz nawiązać połączenie z bazą danych przy użyciu SQL Server Management Studio lub innego wybranego narzędzia. Używaj wcześniej utworzonego konta administratora serwera.

> [!IMPORTANT]
> Domyślnie dostęp przez zaporę IP usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Kliknij przycisk **Wyłącz** na tej stronie, aby wyłączyć dostęp dla wszystkich usług platformy Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono podstawowe zadania dotyczące baz danych, takie jak tworzenie bazy danych i tabel, łączenie się z bazą danych, ładowanie danych i uruchamianie zapytań. W tym samouczku omówiono:

> [!div class="checklist"]
>
> * Tworzenie bazy danych przy użyciu Azure Portal
> * Konfigurowanie reguły zapory bazującej na adresach IP na poziomie serwera za pomocą witryny Azure Portal
> * Nawiązywanie połączenia z bazą danych przy użyciu narzędzia ADO.NET i programu Visual Studio
> * Tworzenie tabel za pomocą narzędzia ADO.NET
> * Wstawianie, aktualizowanie i usuwanie danych za pomocą narzędzia ADO.NET
> * Wykonywanie zapytań względem danych za pomocą narzędzia ADO.NET

Przejdź do następnego samouczka, aby dowiedzieć się więcej o migracji danych.

> [!div class="nextstepaction"]
> [Przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie offline przy użyciu usługi DMS](../../dms/tutorial-sql-server-to-azure-sql.md)