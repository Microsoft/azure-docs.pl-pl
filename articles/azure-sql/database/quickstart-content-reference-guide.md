---
title: Dokumentacja szybkiego startu dla pojedynczej bazy danych
description: Znajdź odwołanie do zawartości wszystkich przewodników Szybki Start, które pomogą Ci szybko rozpocząć pracę z pojedynczymi bazami danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184420"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Wprowadzenie do pojedynczych baz danych w usłudze Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Pojedyncza baza danych](../index.yml) jest w pełni zarządzaną platformą jako usługą (PaaS) bazą danych (DbaaS), która jest idealnym aparatem magazynu dla nowoczesnych aplikacji w chmurze. W tej sekcji dowiesz się, jak szybko skonfigurować i utworzyć pojedynczą bazę danych w Azure SQL Database.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

W tej sekcji zobaczysz Omówienie dostępnych artykułów, które mogą pomóc szybko rozpocząć pracę z pojedynczymi bazami danych. Poniższe przewodniki szybki start umożliwiają szybkie tworzenie pojedynczej bazy danych, skonfigurowanie reguły zapory na poziomie serwera, a następnie zaimportowanie bazy danych do nowej pojedynczej bazy danych przy użyciu `.bacpac` pliku:

- [Utwórz pojedynczą bazę danych przy użyciu Azure Portal](single-database-create-quickstart.md).
- Po utworzeniu bazy danych należy [ją zabezpieczyć, konfigurując reguły zapory](firewall-create-server-level-portal-quickstart.md).
- Jeśli masz istniejącą bazę danych na SQL Server, która ma zostać zmigrowana do Azure SQL Database, należy zainstalować [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , która będzie analizować bazy danych na SQL Server i znaleźć każdy problem, który może blokować migrację. Jeśli nie znajdziesz żadnego problemu, możesz wyeksportować bazę danych jako plik `.bacpac` i [zaimportować ją za pomocą witryny Azure Portal lub programu SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Automatyzowanie operacji zarządzania

Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby tworzyć, konfigurować i skalować bazę danych.

- [Tworzenie i Konfigurowanie pojedynczej bazy danych przy użyciu programu PowerShell](scripts/create-and-configure-database-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](scripts/create-and-configure-database-cli.md)
- [Aktualizowanie pojedynczej bazy danych i skalowanie zasobów przy użyciu programu PowerShell](scripts/monitor-and-scale-database-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrowanie do pojedynczej bazy danych z minimalnym przestojem

Informacje w tych przewodnikach Szybki start umożliwiają szybkie utworzenie lub zaimportowanie bazy danych na platformę Azure za pomocą pliku `.bacpac`. `.bacpac` `.dacpac` Pliki i są jednak przeznaczone do szybkiego przenoszenia baz danych między różnymi wersjami SQL Server i w ramach usługi Azure SQL albo do wdrożenia ciągłej integracji w potoku DevOps. Jednak ta metoda nie jest przeznaczona do migracji produkcyjnych baz danych z minimalnym przestojem, ponieważ należy zaprzestać dodawać nowe dane, poczekać na wyeksportowanie źródłowej bazy danych do pliku `.bacpac`, a następnie poczekać na zakończenie importu do bazy danych Azure SQL Database. Wszystkie te czynniki powodują przestój aplikacji, szczególnie w przypadku dużych baz danych. Aby przenieść produkcyjną bazę danych, potrzebujesz lepszego sposobu migracji, który gwarantuje minimalny przestój. W tym celu należy przeprowadzić migrację bazy danych przy użyciu [usługi Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json) przy minimalnym przestoju. Usługa DMS realizuje to przez przyrostowe wypychanie zmian wprowadzanych w źródłowej bazie danych do przywracanej pojedynczej baza danych. Dzięki niej można szybko przełączyć aplikację ze źródłowej do docelowej bazy danych z możliwie najkrótszym przestojem.

## <a name="hands-on-learning-modules"></a>Moduły uczenia się

Poniższe moduły Microsoft Learn mogą pomóc Ci bezpłatnie poznać Azure SQL Database.

- [Inicjowanie obsługi administracyjnej bazy danych w SQL Database do przechowywania danych aplikacji](/learn/modules/provision-azure-sql-db/)
- [Opracowywanie i Konfigurowanie aplikacji ASP.NET, która wysyła zapytanie do bazy danych w Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)
- [Zabezpiecz swoją bazę danych w Azure SQL Database](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [ogólną listę funkcji obsługiwanych w usłudze Azure SQL Database](features-comparison.md).
- Dowiedz się, jak [lepiej zabezpieczyć bazę danych](secure-database-tutorial.md).
- Więcej instrukcji zaawansowanych można znaleźć w temacie dotyczącym [sposobu używania pojedynczej bazy danych w usłudze Azure SQL Database](how-to-content-reference-guide.md).
- Znajdź więcej przykładowych skryptów pisanych w programie [PowerShell](powershell-script-content-guide.md) i [interfejsie wiersza polecenia platformy Azure](az-cli-script-samples-content-guide.md).
- Dowiedz się więcej na temat [interfejsu API zarządzania](single-database-manage.md) , którego można użyć do skonfigurowania baz danych.
- [Zidentyfikuj odpowiednie Azure SQL Database lub jednostkę SKU wystąpienia zarządzanego usługi Azure SQL dla lokalnej bazy danych](/sql/dma/dma-sku-recommend-sql-db/).