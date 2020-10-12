---
title: Przykłady Azure PowerShell dla Azure Data Factory
description: Przykłady Azure PowerShell — skrypty ułatwiające tworzenie fabryk danych i zarządzanie nimi.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 6df400e7ce4b66a1589a0c44089148b33ab88638
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439065"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Przykłady Azure PowerShell dla Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Poniższa tabela zawiera linki do przykładowych skryptów Azure PowerShell dla Azure Data Factory.

| Skrypt | Opis  |
|---|---|
|**Kopiowanie danych**||
|[Kopiowanie obiektów blob z folderu do innego folderu w usłudze Azure Blob Storage](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell kopiuje obiekty blob z folderu na platformie Azure Blob Storage do innego folderu w tej samej Blob Storage. |
|[Kopiowanie danych z SQL Server na platformę Azure Blob Storage](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell kopiuje dane z bazy danych SQL Server do magazynu obiektów blob platformy Azure. |
|[Kopia Zbiorcza](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten przykładowy skrypt programu PowerShell kopiuje dane z wielu tabel w bazie danych w Azure SQL Database do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse). |
|[Kopia przyrostowa](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten przykładowy skrypt programu PowerShell ładuje tylko nowe lub zaktualizowane rekordy z magazynu danych źródłowych do magazynu danych ujścia po początkowej pełnej kopii danych ze źródła do ujścia. |
|**Przekształcanie danych**||
|[Przekształcanie danych przy użyciu klastra Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell przekształca dane przez uruchomienie programu w klastrze Spark. |
|**Podnieś i Przenieś pakiety SSIS na platformę Azure**||
|[Tworzenie środowiska Azure SSIS Integration Runtime](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell inicjuje środowisko Azure-SSIS Integration Runtime, które uruchamia pakiety SQL Server Integration Services (SSIS) na platformie Azure. |



