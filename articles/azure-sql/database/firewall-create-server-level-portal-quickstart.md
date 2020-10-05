---
title: Tworzenie reguły zapory na poziomie serwera
description: Tworzenie reguły zapory na poziomie serwera
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, sstein
ms.date: 02/11/2019
ms.openlocfilehash: d62d568b4924a2bfa67740b3e5f0cc1377519d47
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91335045"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>Szybki Start: Tworzenie reguły zapory na poziomie serwera przy użyciu Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia [reguły zapory na poziomie serwera](firewall-configure.md) w Azure SQL Database przy użyciu Azure Portal, aby umożliwić nawiązywanie połączeń z [logicznymi serwerami SQL](logical-servers.md), pojedynczymi bazami danych i pulami elastycznymi oraz ich bazami danych. Reguła zapory jest wymagana do nawiązania połączenia z innymi zasobami platformy Azure i z zasobami lokalnymi. Reguły zapory na poziomie serwera nie mają zastosowania do wystąpienia zarządzanego Azure SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Jako punktu początkowego ten przewodnik Szybki start używa zasobów utworzonych w przewodniku [Tworzenie pojedynczej bazy danych za pomocą witryny Azure Portal](single-database-create-quickstart.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Tworzenie reguły zapory bazującej na adresach IP na poziomie serwera

 SQL Database tworzy zaporę na poziomie serwera dla jednej i puli baz danych. Ta zapora uniemożliwia aplikacjom klienckim łączenie się z serwerem lub dowolnymi z jego baz danych, chyba że zostanie utworzona reguła zapory IP w celu otworzenia zapory. Na potrzeby połączenia z adresu IP spoza platformy Azure utwórz regułę zapory dla określonego adresu lub zakresu adresów IP, z którymi połączenie ma być możliwe. Aby uzyskać więcej informacji na temat reguł zapory adresów IP na poziomie serwera i na poziomie bazy danych, zobacz [reguły zapory adresów IP na poziomie serwera i na poziomie bazy danych](firewall-configure.md).

> [!NOTE]
> Usługa Azure SQL Database komunikuje się przez port 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 1433.
> [!IMPORTANT]
> Reguła zapory o wartości 0.0.0.0 umożliwia wszystkim usługom platformy Azure przekazywanie reguły zapory na poziomie serwera i próbę nawiązania połączenia z bazą danych za pomocą serwera programu.

Wykonaj następujące kroki, aby utworzyć regułę zapory adresów IP na poziomie serwera dla adresu IP klienta i włączyć zewnętrzną łączność za pośrednictwem zapory Azure SQL Database tylko dla adresu IP.

1. Po zakończeniu wdrażania [bazy danych](#prerequisites) wybierz pozycję **bazy danych SQL** w menu po lewej stronie, a następnie wybierz pozycję **MySampleDatabase** na stronach **bazy danych SQL** . Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **mynewserver-20170824.database.windows.net**) i opcje dalszej konfiguracji.

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera do użycia podczas nawiązywania połączenia z serwerem i jego bazami danych w innych przewodnikach Szybki start.

   ![nazwa serwera](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. Wybierz pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera.

   ![reguła zapory bazująca na adresach IP na poziomie serwera](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. Wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory bazującej na adresach IP na poziomie serwera. Reguła zapory bazująca na adresach IP na poziomie serwera może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

   > [!IMPORTANT]
   > Domyślnie dostęp za pomocą zapory Azure SQL Database jest wyłączony dla wszystkich usług platformy Azure. Jeśli chcesz włączyć dostęp dla wszystkich usług platformy Azure, wybierz pozycję **włączone** na tej stronie.
   >

5. Wybierz pozycję **Zapisz**. Reguła zapory adresów IP na poziomie serwera jest tworzona dla bieżącego adresu IP otwierającego port 1433 na serwerze.

6. Zamknij stronę **Ustawienia zapory**.

Za pomocą SQL Server Management Studio lub innego wybranego narzędzia możesz teraz połączyć się z serwerem i jego bazami danych z tego adresu IP przy użyciu utworzonego wcześniej konta administratora serwera.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zapisz te zasoby, jeśli chcesz przejść do sekcji [Następne kroki](#next-steps) i dowiedzieć się, jak połączyć bazę danych i wykonywać w niej zapytania przy użyciu różnych metod. Jeśli jednak chcesz usunąć zasoby utworzone w tym przewodniku Szybki start, wykonaj poniższe czynności.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz pozycję **myResourceGroup**.
2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym nazwę **myResourceGroup**, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Teraz, gdy już masz bazę danych, możesz [nawiązać z nią połączenie i uruchamiać zapytania](connect-query-content-reference-guide.md) za pomocą jednego z Twoich ulubionych narzędzi lub języków, w tym
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby dowiedzieć się, jak zaprojektować pierwszą bazę danych, tworzyć tabele i wstawiać dane, zapoznaj się z jednym z następujących samouczków:
  - [Projektowanie pierwszej pojedynczej bazy danych w usłudze Azure SQL Database przy użyciu programu SSMS](design-first-database-tutorial.md)
  - [Projektowanie pojedynczej bazy danych w usłudze Azure SQL Database i nawiązywanie połączenia za pomocą języka C# i narzędzia ADO.NET](design-first-database-csharp-tutorial.md)
