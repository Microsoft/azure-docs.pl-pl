---
title: Zarządzanie Azure Analysis Services przy użyciu programu PowerShell | Microsoft Docs
description: Opisuje Azure Analysis Services poleceń cmdlet programu PowerShell dla typowych zadań administracyjnych, takich jak tworzenie serwerów, wstrzymywanie operacji lub zmiana poziomu usługi.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28f414c5eaaea7b987f2c3694cb8fc73b70838e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018767"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Zarządzanie usługami Azure Analysis Services przy użyciu programu PowerShell

W tym artykule opisano polecenia cmdlet programu PowerShell służące do wykonywania zadań związanych z zarządzaniem Azure Analysis Services serwera i bazy danych. 

Zadania zarządzania zasobami serwera, takie jak tworzenie lub usuwanie serwera, wstrzymywanie lub wznawianie operacji serwera lub zmiana poziomu usługi (warstwy) używają Azure Analysis Services poleceń cmdlet. Inne zadania związane z zarządzaniem bazami danych, takimi jak dodawanie lub usuwanie elementów członkowskich roli, przetwarzania lub partycjonowania, używają poleceń cmdlet zawartych w tym samym module SqlServer co SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Uprawnienia

Większość zadań programu PowerShell wymaga uprawnień administratora na zarządzanym serwerze Analysis Services. Zaplanowane zadania programu PowerShell są operacjami nienadzorowanymi. Konto lub jednostka usługi z uruchomionym harmonogramem musi mieć uprawnienia administratora na serwerze Analysis Services. 

W przypadku operacji serwera przy użyciu Azure PowerShell poleceń cmdlet konto użytkownika lub konto z uruchomionym harmonogramem musi również należeć do roli właściciela zasobu w [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operacje zasobów i serwera 

Zainstaluj moduł- [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentacja- [AZ. AnalysisServices Reference](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operacje bazy danych

Operacje bazy danych Azure Analysis Services używają tego samego modułu SqlServer co SQL Server Analysis Services. Nie wszystkie polecenia cmdlet są jednak obsługiwane dla Azure Analysis Services. 

Moduł SqlServer udostępnia specyficzne dla zadania polecenia cmdlet zarządzania bazami danych, a także polecenie cmdlet Invoke-ASCmd ogólnego przeznaczenia, które akceptuje zapytanie lub skrypt języka skryptów tabelarycznych (TMSL). Następujące polecenia cmdlet w module SqlServer są obsługiwane dla Azure Analysis Services.

Moduł instalacji — [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentacja — [odwołanie do SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Obsługiwane polecenia cmdlet

|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Dodaj członka do roli bazy danych.| 
|[Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase)|Utwórz kopię zapasową bazy danych Analysis Services.|  
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Usuń członka z roli bazy danych.|   
|[Invoke-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Wykonaj skrypt TMSL.|
|[Invoke-ProcessASDatabase](/powershell/module/sqlserver/invoke-processasdatabase)|Przetwarzaj bazę danych.|  
|[Invoke-ProcessPartition](/powershell/module/sqlserver/invoke-processpartition)|Przetwórz partycję.| 
|[Invoke-Processing](/powershell/module/sqlserver/invoke-processtable)|Przetwórz tabelę.|  
|[Scalanie partycji](/powershell/module/sqlserver/merge-partition)|Scal partycję.|  
|[Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase)|Przywróć bazę danych Analysis Services.| 
  

## <a name="related-information"></a>Informacje pokrewne

* [SQL Server PowerShell](/sql/powershell/sql-server-powershell)      
* [Pobierz moduł SQL Server PowerShell](/sql/ssms/download-sql-server-ps-module)   
* [Pobierz narzędzie SSMS](/sql/ssms/download-sql-server-management-studio-ssms)   
* [Moduł SqlServer w Galeria programu PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programowanie modeli tabelarycznych dla poziomu zgodności 1200 i wyższych](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)