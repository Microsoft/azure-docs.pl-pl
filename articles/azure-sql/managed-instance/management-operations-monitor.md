---
title: Monitorowanie operacji zarządzania
titleSuffix: Azure SQL Managed Instance
description: Poznaj różne sposoby monitorowania operacji zarządzania wystąpieniami zarządzanymi przez usługę Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: bdb021bc0247972fa29975c62bc9214e3b474e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996841"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Monitorowanie operacji zarządzania wystąpieniami zarządzanymi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL udostępnia monitorowanie [operacji zarządzania](management-operations-overview.md) , które są używane do wdrażania nowych wystąpień zarządzanych, aktualizowania właściwości wystąpienia lub usuwania wystąpień, gdy nie są już potrzebne. 

## <a name="overview"></a>Omówienie

Wszystkie operacje zarządzania można klasyfikować w następujący sposób:

- Wdrożenie wystąpienia (nowe wystąpienie).
- Aktualizacja wystąpienia (zmiana właściwości wystąpienia, takich jak rdzeni wirtualnych lub magazyn zarezerwowany).
- Usuwanie wystąpienia.

Większość operacji zarządzania to [długotrwałe operacje](management-operations-overview.md#duration). W związku z tym należy monitorować stan lub postępować zgodnie z postępem kroków operacji. 

Istnieje kilka sposobów monitorowania operacji zarządzania wystąpieniami zarządzanymi:

- [Wdrożenia grupy zasobów](../../azure-resource-manager/templates/deployment-history.md)
- [Dziennik aktywności](../../azure-monitor/platform/activity-log.md)
- [Interfejs API operacji wystąpienia zarządzanego](#managed-instance-operations-api)


Poniższa tabela zawiera porównanie opcji monitorowania operacji zarządzania: 

| Opcja | Przechowywanie | Obsługuje anulowanie | Utwórz | Aktualizacja | Usuwanie | Anuluj | Kroki |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Wdrożenia grupy zasobów | Nieskończoność<sup>1</sup> | Nr<sup>2</sup> | Widoczne | Widoczne | Niewidoczne | Widoczne | Niewidoczne |
| Dziennik aktywności | 90 dni | Nie | Widoczne | Widoczne | Widoczne | Widoczne |  Niewidoczne |
| Interfejs API operacji wystąpienia zarządzanego | 24 godziny | [Tak](management-operations-cancel.md) | Widoczne | Widoczne | Widoczne | Widoczne | Widoczne |
|  |  |  |  |  |  |  | |

<sup>1</sup> historia wdrożenia grupy zasobów jest ograniczona do 800 wdrożeń.

<sup>2</sup> wdrożenia grupy zasobów obsługują operację anulowania. Jednak ze względu na logikę anulowania tylko operacja zaplanowana do wdrożenia po akcji anulowania zostanie anulowana. Bieżące wdrożenie nie jest anulowane po anulowaniu wdrożenia grupy zasobów. Ponieważ wdrożenie wystąpienia zarządzanego składa się z jednego działającego kroku (z perspektywy usługi Azure Resource Manager), anulowanie wdrożenia grupy zasobów nie spowoduje anulowania wdrożenia wystąpienia zarządzanego, a operacja zostanie ukończona. 

## <a name="managed-instance-operations-api"></a>Interfejs API operacji wystąpienia zarządzanego

Interfejsy API operacji zarządzania są specjalnie zaprojektowane do monitorowania operacji. Monitorowanie operacji wystąpienia zarządzanego może zapewnić wgląd w parametry operacji i kroki operacji, a także [anulować określone operacje](management-operations-cancel.md). Oprócz szczegółów operacji i polecenia Cancel ten interfejs API może być używany w skryptach automatyzacji z wdrożeniami z obsługą kilku zasobów — na podstawie kroku postępu można uruchomić niektóre zależne wdrożenia zasobów.

Są to interfejsy API: 

| Polecenie | Opis |
| --- | --- |
|[Operacje wystąpienia zarządzanego — Pobierz](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|Pobiera operację zarządzania na wystąpieniu zarządzanym.|
|[Operacje wystąpienia zarządzanego — anulowanie](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|Anuluje operację asynchroniczną na zarządzanym wystąpieniu.|
|[Operacje wystąpienia zarządzanego — lista według wystąpienia zarządzanego](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Pobiera listę operacji wykonanych na zarządzanym wystąpieniu.|

> [!NOTE]
> Użyj interfejsu API w wersji 2020-02-02, aby wyświetlić operację tworzenia wystąpienia zarządzanego na liście operacji. Jest to domyślna wersja używana w Azure Portal i najnowszych pakietach interfejsu wiersza polecenia platformy Azure.

## <a name="monitor-operations"></a>Monitorowanie operacji

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal Użyj strony **omówienia** wystąpienia zarządzanego, aby monitorować operacje wystąpienia zarządzanego. 

Na przykład **operacja tworzenia** jest widoczna na początku procesu tworzenia na stronie **Przegląd** : 

![Postęp tworzenia wystąpienia zarządzanego](./media/management-operations-monitor/monitoring-create-operation.png)

Wybierz **bieżącą operację** , aby otworzyć **stronę trwające operacje i** wyświetlić operacje **tworzenia** lub **aktualizowania** . Możesz również [anulować](management-operations-cancel.md) operacje na tej stronie.  

![Szczegóły operacji wystąpienia zarządzanego](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> [Można anulować](management-operations-cancel.md)operacje tworzenia za pośrednictwem Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub innych narzędzi przy użyciu interfejsu API REST w wersji 2020-02-02. Wersje interfejsu API REST starsze niż 2020-02-02 użyte do przesłania operacji tworzenia spowodują rozpoczęcie wdrożenia wystąpienia, ale wdrożenie nie będzie wyświetlane w interfejsie API operacji i nie można go anulować.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenie cmdlet Get-AzSqlInstanceOperation pobiera informacje o operacjach w wystąpieniu zarządzanym. Można wyświetlić wszystkie operacje na zarządzanym wystąpieniu lub wyświetlić określoną operację, podając nazwę operacji.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Aby zapoznać się ze szczegółowymi poleceniami, zobacz [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Lista AZ SQL mi op pobiera listę operacji wykonanych na zarządzanym wystąpieniu. Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Aby zapoznać się ze szczegółowymi poleceniami, zobacz [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](instance-create-quickstart.md).
- Aby zapoznać się z funkcjami i listą porównawczą, zobacz [Common SQL Features](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym korzystania z Azure Database Migration Service migracji, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
