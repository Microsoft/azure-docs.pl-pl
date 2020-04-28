---
title: Zarządzanie bazami danych za pomocą Azure Automation
description: Dowiedz się, w jaki sposób usługa Azure Automation może być używana do zarządzania bazami danych Azure SQL Database w odpowiedniej skali.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822802"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Zarządzanie bazami danych Azure SQL Database przy użyciu Azure Automation

W tym przewodniku przedstawiono usługę Azure Automation i sposób jej użycia do uproszczenia zarządzania bazami danych Azure SQL Database.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure, która upraszcza zarządzanie chmurą poprzez automatyzację procesów. Korzystanie z Azure Automation, długotrwałych, ręcznych, podatnych na błędy i często powtarzanych zadań może być zautomatyzowane, aby zwiększyć niezawodność, wydajność i czas do osiągnięcia korzyści dla organizacji.

Azure Automation udostępnia aparat wykonywania przepływu pracy z wysoką niezawodnością i wysoką dostępnością, który skaluje się do potrzeb użytkownika w miarę rozwoju organizacji. W Azure Automation procesy mogą być uruchamiane ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania będą wykonywane dokładnie w razie potrzeby.

Obniż koszty operacyjne i zwolnij personel IT/DevOps, aby skoncentrować się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Jak można Azure Automation pomóc w zarządzaniu bazami danych Azure SQL Database?

Azure SQL Database można zarządzać w Azure Automation za pomocą [Azure SQL Database poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) , które są dostępne w [narzędziach Azure PowerShell](/powershell/azure/overview). Azure Automation są dostępne następujące Azure SQL Database poleceń cmdlet programu PowerShell, dzięki czemu można wykonać wszystkie zadania zarządzania w usłudze SQL DB w ramach usługi. Możesz również sparować te polecenia cmdlet w Azure Automation z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w ramach usług platformy Azure i systemów innych firm.

Azure Automation ma także możliwość bezpośredniego komunikowania się z serwerami SQL przez wydawanie poleceń SQL przy użyciu programu PowerShell.

[Galeria Azure Automation Runbook](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) zawiera różne elementy Runbook zespołu produktów i społeczności, które umożliwiają automatyzację zarządzania bazami danych Azure SQL Database, innymi usługami platformy Azure i systemami innych firm. Elementy Runbook galerii obejmują:

- [Uruchamianie zapytań SQL dotyczących bazy danych SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Skalowanie w pionie (w górę lub w dół) Azure SQL Database według harmonogramu](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Obetnij tabelę SQL, gdy jej baza danych zbliża się do maksymalnego rozmiaru](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indeksowanie tabel w Azure SQL Database, jeśli są wysoce pofragmentowane](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Azure Automation i ich używania do zarządzania bazami danych Azure SQL Database, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o Azure Automation.

- [Omówienie usługi Azure Automation](../automation/automation-intro.md)
- [Mój pierwszy element Runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: Twój Agent SQL w chmurze](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 