---
title: Anuluj operacje zarządzania
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się, jak anulować operacje zarządzania wystąpieniem zarządzanym usługi Azure SQL.
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
ms.openlocfilehash: 092981f9d74a3f9f18c491ca6cee539a29e73c83
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782505"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Anulowanie operacji zarządzania wystąpieniami zarządzanymi przez usługę Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL umożliwia anulowanie niektórych [operacji zarządzania](management-operations-overview.md), takich jak podczas wdrażania nowego wystąpienia zarządzanego lub aktualizacji właściwości wystąpienia. 

## <a name="overview"></a>Omówienie

 Wszystkie operacje zarządzania można klasyfikować w następujący sposób:

- Wdrożenie wystąpienia (nowe wystąpienie).
- Aktualizacja wystąpienia (zmiana właściwości wystąpienia, takich jak rdzeni wirtualnych lub magazyn zarezerwowany).
- Usuwanie wystąpienia.

W razie potrzeby można [monitorować postęp i stan operacji zarządzania](management-operations-monitor.md) i anulować niektóre z nich. 

Poniższa tabela zawiera podsumowanie operacji zarządzania, bez względu na to, czy można je anulować, i ich typowy całkowity czas trwania:

Kategoria  |Operacja  |Można anulować  |Szacowany czas trwania anulowania  |
|---------|---------|---------|---------|
|wdrażania |Tworzenie wystąpienia |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Aktualizacja |Skalowanie magazynu wystąpień w górę/w dół (Ogólnego przeznaczenia) |Nie |  |
|Aktualizacja |Skalowanie magazynu wystąpień w górę/w dół (Krytyczne dla działania firmy) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Aktualizacja |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Ogólnego przeznaczenia) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Aktualizacja |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Krytyczne dla działania firmy) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Aktualizacja |Zmiana warstwy usługi wystąpienia (Ogólnego przeznaczenia do Krytyczne dla działania firmy i na odwrót) |Tak |90% operacji zakończonych w ciągu 5 minut. |
|Usuń |Usunięcie wystąpienia |Nie |  |
|Usuń |Usuwanie klastra wirtualnego (jako operacja zainicjowana przez użytkownika) |Nie |  |

## <a name="cancel-management-operation"></a>Anuluj operację zarządzania

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby anulować operacje zarządzania przy użyciu Azure Portal, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com)
1. Przejdź do bloku **Przegląd** wystąpienia zarządzanego SQL. 
1. Zaznacz pole **powiadomień** obok trwającej operacji, aby otworzyć stronę **trwającej operacji** . 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Wybierz bieżące okno operacji, aby otworzyć stronę trwające operacje.":::

1. Wybierz pozycję **Anuluj operację** w dolnej części strony. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Wybierz bieżące okno operacji, aby otworzyć stronę trwające operacje.":::

1. Potwierdź, że chcesz anulować operację. 


Jeśli żądanie anulowania zakończy się pomyślnie, operacja zarządzania zostanie anulowana i spowoduje to niepowodzenie. Otrzymasz powiadomienie o powodzeniu lub niepowodzeniu anulowania.

![Anulowanie wyniku operacji](./media/management-operations-cancel/canceling-operation-result.png)


Jeśli żądanie anulowania zakończy się niepowodzeniem lub przycisk Anuluj nie jest aktywny, oznacza to, że operacja zarządzania została wprowadzona jako niebędąca w stanie niemożliwego do anulowania i wkrótce zakończy się.  Operacja zarządzania będzie kontynuować wykonywanie, dopóki nie zostanie ukończona.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Jeśli nie masz jeszcze zainstalowanego Azure PowerShell, zobacz [Install the Azure PowerShell module](/powershell/azure/install-az-ps).

Aby anulować operację zarządzania, należy określić nazwę operacji zarządzania. W związku z tym należy najpierw użyć polecenia Get, aby pobrać listę operacji, a następnie anulować konkretną operację.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Aby zapoznać się ze szczegółowymi poleceniami, zobacz [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) i [stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby anulować operację zarządzania, należy określić nazwę operacji zarządzania. W związku z tym należy najpierw użyć polecenia Get, aby pobrać listę operacji, a następnie anulować konkretną operację.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Aby zapoznać się ze szczegółowymi poleceniami, zobacz [AZ SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Anulowane żądanie wdrożenia

W przypadku użycia interfejsu API w wersji 2020-02-02, gdy tylko żądanie utworzenia wystąpienia zostanie zaakceptowane, wystąpienie rozpoczyna się jako zasób, niezależnie od postępu procesu wdrażania (stan wystąpienia zarządzanego jest **aprowizacji** ). Jeśli anulujesz żądanie wdrożenia wystąpienia (nowe wystąpienie), wystąpienie zarządzane przejdzie ze stanu **aprowizacji** do **FailedToCreate** .

Wystąpienia, które nie zostały utworzone, są nadal obecne jako zasób i: 

- Nie są naliczone
- Nie uwzględniaj limitów zasobów (limit przydziału podsieci lub rdzeń wirtualny)
- Zachowaj zastrzeżoną nazwę wystąpienia — aby wdrożyć wystąpienie o tej samej nazwie, Usuń wystąpienie zakończone niepowodzeniem i zwolnij nazwę


> [!NOTE]
> Aby zminimalizować szum na liście zasobów lub wystąpień zarządzanych, usuń wystąpienia, dla których nie powiodło się wdrożenie lub wystąpienia z anulowanymi wdrożeniami. 


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](instance-create-quickstart.md).
- Aby zapoznać się z funkcjami i listą porównawczą, zobacz [Common SQL Features](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym korzystania z Azure Database Migration Service migracji, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).