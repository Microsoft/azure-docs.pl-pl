---
title: Zarządzanie bazami danych za pomocą Azure Automation
description: Dowiedz się, w jaki sposób usługa Azure Automation może być używana do zarządzania Azure SQL Database na dużą skalę.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3d785edb82e62363dd96d9fd67447f1be7ffe8f5
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982613"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Zarządzanie bazami danych w Azure SQL Database przy użyciu Azure Automation

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym przewodniku przedstawiono usługę Azure Automation i sposób jej użycia w celu uproszczenia zarządzania bazami danych w programie Azure SQL Database.

## <a name="about-azure-automation"></a>Informacje o Azure Automation

[Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure, która upraszcza zarządzanie chmurą poprzez automatyzację procesów. Korzystanie z Azure Automation, długotrwałych, ręcznych, podatnych na błędy i często powtarzanych zadań może być zautomatyzowane, aby zwiększyć niezawodność, wydajność i czas do osiągnięcia korzyści dla organizacji. Aby uzyskać informacje na temat rozpoczynania pracy, zobacz [Azure Automation wprowadzenie](../../automation/automation-intro.md)

Azure Automation udostępnia aparat wykonywania przepływu pracy z wysoką niezawodnością i wysoką dostępnością, który skaluje się do potrzeb użytkownika w miarę rozwoju organizacji. W Azure Automation procesy mogą być uruchamiane ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania będą wykonywane dokładnie w razie potrzeby.

Obniż koszty operacyjne i zwolnij personel IT/DevOps, aby skoncentrować się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez Azure Automation.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Jak Azure Automation może pomóc w zarządzaniu bazami danych

Za pomocą Azure Automation można zarządzać bazami danych w Azure SQL Database za pomocą [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) , które są dostępne w [narzędziach Azure PowerShell](/powershell/azure/overview). Azure Automation są dostępne następujące Azure SQL Database poleceń cmdlet programu PowerShell, dzięki czemu można wykonywać wszystkie SQL Database zadania zarządzania w ramach usługi. Możesz również sparować te polecenia cmdlet w Azure Automation z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w ramach usług platformy Azure i systemów innych firm.

Azure Automation ma także możliwość bezpośredniego komunikowania się z serwerami SQL przez wydawanie poleceń SQL przy użyciu programu PowerShell.

Galerie elementów Runbook i modułów dla [Azure Automation](../../automation/automation-runbook-gallery.md) oferują różne elementy Runbook firmy Microsoft i społeczność, które można zaimportować do Azure Automation. Aby użyć jednego z nich, pobierz element Runbook z galerii lub bezpośrednio Importuj elementy Runbook z galerii lub z konta usługi Automation w Azure Portal.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Azure Automation i sposobu ich używania do zarządzania Azure SQL Database, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o Azure Automation.

- [Omówienie usługi Azure Automation](../../automation/automation-intro.md)
- [Mój pierwszy element Runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
