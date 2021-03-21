---
title: Kopiowanie danych ze środowiska lokalnego na platformę Azure przy użyciu programu PowerShell
description: Ten skrypt programu PowerShell kopiuje dane z bazy danych SQL Server do innej Blob Storage platformy Azure.
ms.service: data-factory
ms.topic: article
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 34c5497a8b059260dfe60e8015e62c0f1511f021
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373389"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-sql-server-to-azure"></a>Tworzenie potoku usługi Data Factory w celu kopiowania danych z SQL Server na platformę Azure przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy potok w Azure Data Factory, który kopiuje dane z bazy danych SQL Server do Blob Storage platformy Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- **SQL Server**. W tym przykładzie używasz bazy danych SQL Server jako **źródła** magazynu danych.
- **Konto usługi Azure Storage**. W tym przykładzie używasz magazynu obiektów blob platformy Azure jako **miejsca docelowego/ujścia** danych. Jeśli nie masz konta usługi Azure Storage, zapoznaj się z artykułem [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) , aby dowiedzieć się, jak go utworzyć.
- **Własne środowisko Integration Runtime**. Pobierz plik MSI z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) i uruchom go, aby zainstalować własne środowisko Integration Runtime na komputerze.  

### <a name="create-sample-database-in-sql-server"></a>Tworzenie przykładowej bazy danych w SQL Server
1. W bazie danych SQL Server Utwórz tabelę o nazwie **EMP** przy użyciu następującego skryptu SQL:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Wstaw przykładowe dane do tabeli:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki JSON, które definiują Data Factory jednostki (połączone usługi, zestawy danych i potok) na dysku twardym w c:\ system32\drivers\etc.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po uruchomieniu przykładowego skryptu można użyć następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Aby usunąć fabrykę danych z grupy zasobów, uruchom następujące polecenie:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Tworzenie fabryki danych. |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Szyfruje poświadczenia w połączonej usłudze i generuje nową definicję połączonej usługi z zaszyfrowanym poświadczeniem.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Tworzy połączoną usługę w fabryce danych. Połączona usługa łączy magazyn danych lub dane obliczeniowe z fabryką danych. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Tworzy zestaw danych w fabryce danych. Zestaw danych reprezentuje dane wejściowe/wyjściowe dla działania w potoku. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera jedną lub więcej działań, które wykonują określoną operację. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w Blob Storage platformy Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Tworzy przebieg dla potoku. Innymi słowy, uruchamia potok. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Pobiera szczegółowe informacje o przebiegu działania (przebieg działania) w potoku.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Dodatkowe przykłady skryptów programu Azure Data Factory PowerShell można znaleźć w [przykładach Azure Data Factory programu PowerShell](../samples-powershell.md).