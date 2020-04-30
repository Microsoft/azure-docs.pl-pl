---
title: Tworzenie pojedynczej bazy danych
description: Utwórz Azure SQL Database pojedynczej bazy danych przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Wykonaj zapytanie do bazy danych za pomocą edytora zapytań w Azure Portal.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81685139"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Szybki Start: Tworzenie Azure SQL Database pojedynczej bazy danych

W tym przewodniku szybki start użyjesz Azure Portal, skryptu programu PowerShell lub skryptu interfejsu wiersza polecenia platformy Azure do utworzenia Azure SQL Database pojedynczej bazy danych. Następnie należy wykonać zapytanie do bazy danych za pomocą **edytora zapytań** w Azure Portal. 

[Pojedyncza baza danych](sql-database-single-database.md) to najszybsza i najprostsza opcja wdrażania dla Azure SQL Database. Zarządzasz pojedynczą bazą danych na [serwerze SQL Database](sql-database-servers.md), która znajduje się w [grupie zasobów platformy Azure](../azure-resource-manager/management/overview.md) w określonym regionie świadczenia usługi Azure. W tym przewodniku szybki start utworzysz nową grupę zasobów i program SQL Server dla nowej bazy danych.

Można utworzyć pojedynczą bazę danych w warstwie *obliczeniowej lub* *bezserwerowej* . Zainicjowana baza danych jest wstępnie przydzieloną ustaloną ilością zasobów obliczeniowych, w tym procesora CPU i pamięci, i używa jednego z dwóch [modeli zakupu](sql-database-purchase-models.md). Ten przewodnik Szybki Start tworzy zainicjowaną bazę danych przy użyciu modelu zakupu [opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md) , ale można również wybrać model [oparty na](sql-database-service-tiers-DTU.md) jednostkach DTU. 

Warstwa obliczeń bezserwerowych jest dostępna tylko w modelu zakupu opartym na rdzeń wirtualny i ma automatycznie skalowany zakres zasobów obliczeniowych, w tym procesora CPU i pamięci. Aby utworzyć pojedynczą bazę danych w warstwie obliczeń bezserwerowych, zobacz [Tworzenie bazy danych bez serwera](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

## <a name="prerequisite"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure. Jeśli jej nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Wykonywanie zapytań względem bazy danych

Po utworzeniu bazy danych można użyć wbudowanego **edytora zapytań** w Azure Portal, aby nawiązać połączenie z bazą danych i wykonać zapytanie o dane.

1. W portalu Wyszukaj i wybierz pozycję **bazy danych SQL**, a następnie wybierz bazę danych z listy.
1. Na stronie **SQL Database** dla używanej bazy danych wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie.
1. Wprowadź dane logowania administratora serwera, a następnie wybierz **przycisk OK**.
   
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

Należy zachować grupę zasobów, serwer i pojedynczą bazę danych, aby przejść do następnych kroków i dowiedzieć się, jak nawiązać połączenie z bazą danych i wysyłać do niej zapytania przy użyciu różnych metod.

Po zakończeniu korzystania z tych zasobów można usunąć utworzoną grupę zasobów, która spowoduje również usunięcie serwera i pojedynczej bazy danych w ramach tej grupy.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć **zasób** i wszystkie jego zasoby przy użyciu Azure Portal:

1. W portalu Wyszukaj i wybierz pozycję **grupy zasobów**, a następnie **Wybierz z listy pozycję Lista zasobów.**
1. Na stronie Grupa zasobów wybierz pozycję **Usuń grupę zasobów**.
1. W obszarze **wpisz nazwę grupy zasobów** *, wprowadź,* a następnie wybierz pozycję **Usuń**.

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

[Łączenie i wykonywanie zapytań](sql-database-connect-query.md) dotyczących bazy danych przy użyciu różnych narzędzi i języków:
> [!div class="nextstepaction"]
> [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
> 
> [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
