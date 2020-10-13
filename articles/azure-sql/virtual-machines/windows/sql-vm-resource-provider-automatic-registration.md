---
title: Automatyczna rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL
description: Dowiedz się, jak włączyć funkcję automatycznej rejestracji, aby automatycznie rejestrować wszystkie przeszłe i przyszłe SQL Server maszyny wirtualne za pomocą dostawcy zasobów maszyny wirtualnej SQL przy użyciu Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996883"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatyczna rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Włącz funkcję automatycznej rejestracji w Azure Portal, aby automatycznie rejestrować wszystkie bieżące i przyszłe SQL Server na maszynach wirtualnych platformy Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym.

W tym artykule nauczysz się włączyć funkcję automatycznej rejestracji. Alternatywnie możesz [zarejestrować pojedynczą maszynę wirtualną](sql-vm-resource-provider-register.md)lub [zarejestrowanie maszyn wirtualnych](sql-vm-resource-provider-bulk-register.md) razem z dostawcą zasobów maszyny wirtualnej SQL. 

## <a name="overview"></a>Omówienie

[Dostawca zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md#overview) umożliwia zarządzanie SQL Server maszyną wirtualną z Azure Portal. Dodatkowo dostawca zasobów umożliwia niezawodny zestaw funkcji, w tym [Automatyczne stosowanie poprawek](automated-patching.md), [Automatyczne tworzenie kopii zapasowych](automated-backup.md), a także monitorowanie i możliwości zarządzania. Umożliwia również odblokowanie [licencjonowania](licensing-model-azure-hybrid-benefit-ahb-change.md) i [wersji](change-sql-server-edition.md) . Wcześniej te funkcje były dostępne tylko w przypadku SQL Server obrazów maszyn wirtualnych wdrożonych w portalu Azure Marketplace. 

Funkcja automatycznej rejestracji pozwala klientom automatycznie rejestrować wszystkie bieżące i przyszłe SQL Server maszyny wirtualne w ramach subskrypcji platformy Azure za pomocą dostawcy zasobów maszyny wirtualnej SQL. Różni się to od ręcznej rejestracji, która koncentruje się na bieżących SQL Server maszynach wirtualnych. 

Automatyczna rejestracja rejestruje SQL Server maszyny wirtualne w trybie uproszczonym. Nadal musisz [ręcznie uaktualnić do trybu pełnego zarządzania](sql-vm-resource-provider-register.md#upgrade-to-full) , aby skorzystać z pełnego zestawu funkcji. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu dostawcy zasobów, musisz: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Model zasobów platformy Azure dla [maszyny wirtualnej z systemem Windows](../../../virtual-machines/windows/quick-create-portal.md) [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) wdrożony w chmurze publicznej lub Azure Government. 


## <a name="enable"></a>Włącz

Aby włączyć automatyczną rejestrację maszyn wirtualnych SQL Server w Azure Portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do strony zasobów [**maszyn wirtualnych SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Wybierz pozycję **automatyczne SQL Server Rejestracja maszyny wirtualnej** , aby otworzyć stronę **rejestracji automatycznej** . 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Wybierz automatyczną rejestrację maszyny wirtualnej SQL Server, aby otworzyć stronę rejestracji automatycznej":::

1. Wybierz swoją subskrypcję z listy rozwijanej. 
1. Przeczytaj warunki i jeśli zgadzasz się, zaznacz opcję **Akceptuję**. 
1. Wybierz pozycję **zarejestruj** , aby włączyć funkcję i automatycznie zarejestrować wszystkie bieżące i przyszłe maszyny wirtualne SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL. Nie spowoduje to ponownego uruchomienia usługi SQL Server na żadnej z maszyn wirtualnych. 

## <a name="disable"></a>Wyłącz

Użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/install-az-ps) , aby wyłączyć funkcję automatycznej rejestracji. Gdy funkcja automatycznej rejestracji jest wyłączona, SQL Server maszyny wirtualne dodane do subskrypcji muszą zostać ręcznie zarejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL. Nie spowoduje to wyrejestrowania istniejących maszyn wirtualnych SQL Server, które zostały już zarejestrowane.



# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyłączyć automatyczną rejestrację przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenie: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyłączyć automatyczną rejestrację przy użyciu Azure PowerShell, uruchom następujące polecenie: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Włącz dla wielu subskrypcji

Funkcję automatycznej rejestracji można włączyć dla wielu subskrypcji platformy Azure za pomocą programu PowerShell. 

W tym celu wykonaj następujące czynności:

1. Zapisz [ten skrypt](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) do `.ps1` pliku, na przykład `EnableBySubscription.ps1` . 
1. Przejdź do lokalizacji, w której zapisano skrypt przy użyciu wiersza polecenia administracyjnego lub okna programu PowerShell. 
1. Połącz z platformą Azure ( `az login` ).
1. Wykonaj skrypt, przekazując w SubscriptionIds jako parametry, takie jak   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Na przykład: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Błędy rejestracji zakończonych niepowodzeniem są przechowywane w `RegistrationErrors.csv` lokalizacji znajdującej się w tym samym katalogu, w którym zapisano i wykonano `.ps1` skrypt. 

## <a name="next-steps"></a>Następne kroki

Uaktualnij tryb zarządzania do [pełnego](sql-vm-resource-provider-register.md#upgrade-to-full) , aby skorzystać z pełnego zestawu funkcji dostarczonego przez dostawcę zasobów maszyny wirtualnej SQL. 
