---
title: Korzyść użycia hybrydowego platformy Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Użyj istniejących licencji SQL Server dla rabatów wystąpienia zarządzanego Azure SQL Database i SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4, devx-track-azurecli
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: 872b36eae9142473885885d5bf5ad3fa10ae5ccb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841601"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Korzyść użycia hybrydowego platformy Azure-Azure SQL Database & wystąpienie zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

W warstwie obliczeniowej z zainicjowaną obsługą modelu zakupu opartego na rdzeń wirtualny można wymienić istniejące licencje dla obniżonych stawek na Azure SQL Database i wystąpienie zarządzane usługi Azure SQL przy użyciu [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Ta korzyść na korzystanie z platformy Azure pozwala zaoszczędzić do 30 procent lub nawet w przypadku SQL Database & wystąpienia zarządzanego SQL przy użyciu licencji SQL Server z programem Software Assurance. Na stronie [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) znajduje się Kalkulator ułatwiający określenie oszczędności.  Należy pamiętać, że Korzyść użycia hybrydowego platformy Azure nie ma zastosowania do Azure SQL Database bezserwerowe.

> [!NOTE]
> Zmiana na Korzyść użycia hybrydowego platformy Azure nie wymaga żadnych przestojów.

![Rdzeń wirtualny — struktura cenowa](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Wybierz model licencji

Za pomocą Korzyść użycia hybrydowego platformy Azure Możesz wybrać opcję płacenia wyłącznie za podstawową infrastrukturę platformy Azure, korzystając z istniejącej licencji SQL Server dla samego aparatu bazy danych SQL Server (podstawowa cena obliczeniowa) lub płacisz za podstawową infrastrukturę i licencję SQL Server (Cennik uwzględniony w licencji).

Możesz wybrać lub zmienić model licencjonowania w Azure Portal: 
- W przypadku nowych baz danych podczas tworzenia wybierz pozycję **Konfiguruj bazę danych** na karcie **podstawowe** i wybierz opcję, aby zaoszczędzić pieniądze.
- W przypadku istniejących baz danych wybierz pozycję **Konfiguruj** w menu **Ustawienia** , a następnie wybierz opcję oszczędności pieniędzy.

Istnieje również możliwość skonfigurowania nowej lub istniejącej bazy danych przy użyciu jednego z następujących interfejsów API:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby ustawić lub zaktualizować typ licencji przy użyciu programu PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu wiersza polecenia platformy Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [AZ SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[Interfejs API REST](#tab/rest)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu API REST:

- [Bazy danych — Utwórz lub zaktualizuj](/rest/api/sql/databases/createorupdate)
- [Bazy danych — aktualizacja](/rest/api/sql/databases/update)
- [Wystąpienia zarządzane — Utwórz lub zaktualizuj](/rest/api/sql/managedinstances/createorupdate)
- [Wystąpienia zarządzane — aktualizacja](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Pytania Korzyść użycia hybrydowego platformy Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Czy istnieją podwójne prawa do korzystania z Korzyść użycia hybrydowego platformy Azure dla SQL Server?

Masz 180 dni od podwójnego prawa do używania licencji, aby zapewnić bezproblemowe działanie migracji. Po upływie tego 180-dniowego okresu można korzystać tylko z licencji SQL Server w chmurze w SQL Database. Nie masz już praw do podwójnego użytkowania lokalnie i w chmurze.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak Korzyść użycia hybrydowego platformy Azure dla SQL Server różni się od przenoszenia licencji?

Firma Microsoft oferuje korzyści z zakresu mobilności licencji do SQL Server klientom z programem Software Assurance. Pozwala to na ponowne przypisanie licencji do współużytkowanych serwerów partnerskich. Możesz użyć tej korzyści w przypadku usług Azure IaaS i AWS EC2.

Korzyść użycia hybrydowego platformy Azure SQL Server różni się od przenoszenia licencji w dwóch kluczowych obszarach:

- Zapewnia ekonomiczne korzyści związane z przenoszenium wysoce zwirtualizowanych obciążeń na platformę Azure. Klienci wersji SQL Server Enterprise Edition mogą uzyskać cztery rdzenie na platformie Azure w jednostce SKU Ogólnego przeznaczenia dla każdego rdzenia, które są lokalnie przeznaczone dla wysoce zwirtualizowanych aplikacji. Mobilność licencji nie pozwala na przenoszenie zwirtualizowanych obciążeń do chmury z uwzględnieniem specjalnych kosztów.
- Zapewnia miejsce docelowe PaaS na platformie Azure (wystąpienie zarządzane SQL), które jest wysoce zgodne z SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jakie są określone prawa Korzyść użycia hybrydowego platformy Azure dla SQL Server?

SQL Database klienci mają następujące prawa skojarzone z Korzyść użycia hybrydowego platformy Azure dla SQL Server:

|Wpływ na licencję|Co Korzyść użycia hybrydowego platformy Azure na SQL Server uzyskać użytkownika?|
|---|---|
|Klienci z wersjami SQL Server Enterprise Core z pakietem SA|<li>Może być płacona stawka podstawowa dla Ogólnego przeznaczenia lub Krytyczne dla działania firmy jednostki SKU</li><br><li>1 rdzeń lokalnego = 4 rdzenie w jednostce SKU Ogólnego przeznaczenia</li><br><li>1 rdzeń lokalnego = 1 rdzeń w jednostce SKU Krytyczne dla działania firmy</li>|
|Klienci z wersjami SQL Server Standard Core z pakietem SA|<li>Można uiścić stawkę bazową tylko dla Ogólnego przeznaczenia jednostki SKU</li><br><li>1 rdzeń lokalnego = 1 rdzeń w jednostce SKU Ogólnego przeznaczenia</li>|
|||


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać pomoc dotyczącą wybierania opcji wdrożenia usługi Azure SQL, zobacz [Wybieranie odpowiedniej opcji wdrażania w usłudze Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- Porównanie funkcji wystąpień zarządzanych SQL Database i SQL można znaleźć w temacie [SQL Database & funkcje wystąpienia zarządzanego SQL](database/features-comparison.md).
