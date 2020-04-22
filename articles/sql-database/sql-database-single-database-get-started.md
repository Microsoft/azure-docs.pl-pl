---
title: Tworzenie pojedynczej bazy danych
description: Utwórz pojedynczą bazę danych usługi Azure SQL Database przy użyciu portalu Azure, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Kwerenda bazy danych z Edytorem zapytań w witrynie Azure portal.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: b06c255f94b4d539c7c85e318238b10629cbb907
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685139"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Szybki start: tworzenie pojedynczej bazy danych usługi Azure SQL Database

W tym przewodniku Szybki start do utworzenia pojedynczej bazy danych usługi Azure SQL Database użyjesz portalu Azure portal, skryptu programu PowerShell lub skryptu interfejsu wiersza polecenia platformy Azure. Następnie kwerendy bazy danych za pomocą **edytora zapytań** w witrynie Azure portal. 

[Pojedyncza baza danych](sql-database-single-database.md) jest najszybszą i najprostszą opcją wdrażania dla usługi Azure SQL Database. Zarządzasz pojedynczą bazą danych w [ramach serwera bazy danych SQL](sql-database-servers.md), który znajduje się wewnątrz grupy zasobów platformy [Azure](../azure-resource-manager/management/overview.md) w określonym regionie platformy Azure. W tym przewodniku Szybki start utworzysz nową grupę zasobów i serwer SQL dla nowej bazy danych.

Można utworzyć pojedynczą bazę danych w warstwie obliczeniowej *aprowizowana* lub *bezserwerowa.* Aprowizowana baza danych jest wstępnie przydzielona określoną ilość zasobów obliczeniowych, w tym procesora i pamięci, i używa jednego z dwóch [modeli zakupu](sql-database-purchase-models.md). Ten przewodnik Szybki start tworzy aprowizowana bazę danych przy użyciu modelu zakupów [opartego na](sql-database-service-tiers-vcore.md) języka, ale można również wybrać model [oparty na UTU.](sql-database-service-tiers-DTU.md) 

Warstwa obliczeniowa bezserwerowa jest dostępna tylko w modelu zakupów opartym na rdzeniach wirtualnych i ma automatycznie skalowany zakres zasobów obliczeniowych, w tym procesora CPU i pamięci. Aby utworzyć pojedynczą bazę danych w warstwie obliczeniowej bezserwerowej, zobacz [Tworzenie bazy danych bezserwerowej](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

## <a name="prerequisite"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure. Jeśli jej nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Wykonywanie zapytań względem bazy danych

Po utworzeniu bazy danych można użyć wbudowanego **edytora zapytań** w witrynie Azure portal, aby połączyć się z bazą danych i zbadać dane.

1. W portalu wyszukaj i wybierz **bazy danych SQL**, a następnie wybierz bazę danych z listy.
1. Na stronie **SQL Database** dla używanej bazy danych wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie.
1. Wprowadź informacje logowania administratora serwera i wybierz **przycisk OK**.
   
   ![Logowanie w Edytorze zapytań](./media/sql-database-single-database-get-started/query-editor-login.png)

1. Wprowadź następujące zapytanie w okienku **Edytora zapytań**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Wybierz opcję **Uruchom**, a następnie przejrzyj wyniki zapytania w okienku **Wyniki**.

   ![Wyniki Edytora zapytań](./media/sql-database-single-database-get-started/query-editor-results.png)

1. Zamknij stronę **Edytor zapytań**, a następnie kliknij przycisk **OK** po wyświetleniu monitu o odrzucenie niezapisanych zmian.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zachowaj grupę zasobów, serwer i pojedynczą bazę danych, aby przejść do następnych kroków, i dowiedz się, jak połączyć się i zbadać bazę danych za pomocą różnych metod.

Po zakończeniu korzystania z tych zasobów można usunąć utworzoną grupę zasobów, która spowoduje również usunięcie serwera i pojedynczej bazy danych w nim.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć **myResourceGroup** i wszystkie jego zasoby za pomocą witryny Azure portal:

1. W portalu wyszukaj i wybierz **grupę zasobów**, a następnie wybierz **myResourceGroup** z listy.
1. Na stronie grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
1. W obszarze **Wpisz nazwę grupy zasobów**wprowadź *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, używając nazwy grupy zasobów:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom następujące polecenie cmdlet programu PowerShell, używając nazwy grupy zasobów:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Następne kroki

[Łączenie i wykonywanie zapytań](sql-database-connect-query.md) z bazą danych przy użyciu różnych narzędzi i języków:
> [!div class="nextstepaction"]
> [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
> 
> [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
