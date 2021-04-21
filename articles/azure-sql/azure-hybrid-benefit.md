---
title: Korzyść użycia hybrydowego platformy Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Użyj istniejących SQL Server licencji na Azure SQL Database i SQL Managed Instance rabaty.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: b5f85e0dcb8ca70d5773b8f1c3b53e0b449ef013
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779205"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Korzyść użycia hybrydowego platformy Azure — Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

W aprowizowanych warstwach obliczeniowych modelu zakupów opartych na rdzeniu wirtualnych można wymienić istniejące licencje na obniżone stawki na usługi Azure SQL Database i Azure SQL Managed Instance przy użyciu Korzyść użycia hybrydowego platformy Azure [.](https://azure.microsoft.com/pricing/hybrid-benefit/) Ta korzyść platformy Azure pozwala zaoszczędzić do 30% lub nawet więcej na SQL Database & SQL Managed Instance dzięki użyciu licencji SQL Server z pakiet Software Assurance. Strona [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) zawiera kalkulator, który pomaga określić oszczędności.  Należy pamiętać, że Korzyść użycia hybrydowego platformy Azure nie ma zastosowania do Azure SQL Database bez serwera.

> [!NOTE]
> Zmiana na Korzyść użycia hybrydowego platformy Azure nie wymaga żadnych przestojów.

![struktura cen rdzeni wirtualnych](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Wybieranie modelu licencji

Korzystając z usługi Korzyść użycia hybrydowego platformy Azure, możesz płacić tylko za podstawową infrastrukturę platformy Azure przy użyciu istniejącej licencji usługi SQL Server dla samego aparatu bazy danych usługi SQL Server (cennik bazowych zasobów obliczeniowych) lub płacić zarówno za podstawową infrastrukturę, jak i licencję platformy SQL Server (cennik z licencją).

Model licencjonowania można wybrać lub zmienić w Azure Portal: 
- W przypadku nowych baz danych podczas  tworzenia wybierz pozycję **Konfiguruj** bazę danych na karcie Podstawowe i wybierz opcję, aby zaoszczędzić pieniądze.
- W przypadku istniejących baz danych wybierz **pozycję Konfiguruj** w menu **Ustawienia** i wybierz opcję, aby zaoszczędzić pieniądze.

Możesz również skonfigurować nową lub istniejącą bazę danych przy użyciu jednego z następujących interfejsów API:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby ustawić lub zaktualizować typ licencji przy użyciu programu PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu wiersza polecenia platformy Azure:

- [az sql db create](/cli/azure/sql/db#az_sql_db_create)
- [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)
- [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)

# <a name="rest-api"></a>[Interfejs API REST](#tab/rest)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu API REST:

- [Bazy danych — tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate)
- [Bazy danych — aktualizacja](/rest/api/sql/databases/update)
- [Wystąpienia zarządzane — tworzenie lub aktualizowanie](/rest/api/sql/managedinstances/createorupdate)
- [Wystąpienia zarządzane — aktualizacja](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Korzyść użycia hybrydowego platformy Azure pytania

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Czy istnieją prawa do podwójnego użycia z Korzyść użycia hybrydowego platformy Azure dla SQL Server?

Masz 180 dni praw podwójnego użycia licencji, aby upewnić się, że migracje są bezproblemowo uruchomione. Po upływie tego 180-dniowego okresu możesz używać licencji SQL Server w chmurze tylko w SQL Database. Nie masz już praw podwójnego użycia w środowisku lokalnym i w chmurze.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Czym różni Korzyść użycia hybrydowego platformy Azure od SQL Server licencji?

Oferujemy korzyści związane z mobilnością licencji SQL Server klientom pakiet Software Assurance. Umożliwia to ponowne przypisanie licencji do serwerów udostępnionych partnera. Możesz użyć tej korzyści w usługach Azure IaaS i AWS EC2.

Korzyść użycia hybrydowego platformy Azure dla SQL Server się od mobilności licencji w dwóch kluczowych obszarach:

- Zapewnia korzyści ekonomiczne związane z przenoszeniem wysoce zwirtualizowanych obciążeń na platformę Azure. SQL Server Enterprise Edition mogą uzyskać cztery rdzenie na platformie Azure w ramach Ogólnego przeznaczenia SKU dla każdego rdzenia, który są ich właścicielami lokalnie w przypadku wysoce zwirtualizowanych aplikacji. Mobilność licencji nie zezwala na żadne korzyści związane ze specjalnymi kosztami przenoszenia zwirtualizowanych obciążeń do chmury.
- Zapewnia miejsce docelowe PaaS na platformie Azure (SQL Managed Instance), które jest w wysokim stopniu zgodne z SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jakie są określone prawa Korzyść użycia hybrydowego platformy Azure dla SQL Server?

SQL Database i SQL Managed Instance mają następujące prawa skojarzone z Korzyść użycia hybrydowego platformy Azure dla SQL Server:

|Zużycie licencji|Co Korzyść użycia hybrydowego platformy Azure dla SQL Server cię?|
|---|---|
|klienci SQL Server Enterprise Edition Core z skojazeniami zabezpieczeń|<li>Może płacić stawkę bazową na podstawie hiperskali, Ogólnego przeznaczenia lub Krytyczne dla działania firmy SKU</li><br><li>1 rdzeń w środowisku lokalnym = 4 rdzenie w przypadku sku w hiperskali</li><br><li>1 rdzeń w środowisku lokalnym = 4 rdzenie w Ogólnego przeznaczenia SKU</li><br><li>1 rdzeń w środowisku lokalnym = 1 rdzeń w Krytyczne dla działania firmy SKU</li>|
|klienci SQL Server Standard Edition Core z skojazeniami zabezpieczeń|<li>Może płacić stawkę bazową na podstawie hiperskali, Ogólnego przeznaczenia lub Krytyczne dla działania firmy SKU</li><br><li>1 rdzeń w środowisku lokalnym = 1 rdzeń w przypadku sku w hiperskali</li><br><li>1 rdzeń w środowisku lokalnym = 1 rdzeń w Ogólnego przeznaczenia SKU</li><br><li>4 rdzenie w środowisku lokalnym = 1 rdzeń w Krytyczne dla działania firmy SKU</li>|
|||


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać pomoc przy wyborze Azure SQL wdrożenia, zobacz [Wybieranie odpowiedniej opcji wdrażania w](azure-sql-iaas-vs-paas-what-is-overview.md)Azure SQL .
- Aby uzyskać porównanie funkcji SQL Database i SQL Managed Instance, [zobacz SQL Database & SQL Managed Instance funkcji](database/features-comparison.md).
