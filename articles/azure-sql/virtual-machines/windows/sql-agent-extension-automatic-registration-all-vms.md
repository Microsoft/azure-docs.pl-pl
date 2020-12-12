---
title: Automatyczna rejestracja przy użyciu rozszerzenia programu SQL IaaS Agent
description: Dowiedz się, jak włączyć funkcję automatycznej rejestracji, aby automatycznie rejestrować wszystkie przeszłe i przyszłe SQL Server maszyny wirtualne za pomocą rozszerzenia programu SQL IaaS Agent przy użyciu Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 1ef7943586123a1870ed9a2d0c21aa8b5fd38c1c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360003"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Automatyczna rejestracja przy użyciu rozszerzenia programu SQL IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Włącz funkcję automatycznej rejestracji w Azure Portal, aby automatycznie rejestrować wszystkie bieżące i przyszłe SQL Server na platformie Azure Virtual Machines (maszyny wirtualne) przy użyciu [rozszerzenia programu SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) w trybie uproszczonym. 

W tym artykule nauczysz się włączyć funkcję automatycznej rejestracji. Alternatywnie możesz [zarejestrować pojedynczą maszynę wirtualną](sql-agent-extension-manually-register-single-vm.md)lub ponownie [zarejestrować maszyny wirtualne](sql-agent-extension-manually-register-vms-bulk.md) za pomocą rozszerzenia programu SQL IaaS Agent. 

## <a name="overview"></a>Omówienie

Zarejestrowanie maszyny wirtualnej SQL Server przy użyciu [rozszerzenia programu SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) w celu odblokowania pełnego zestawu funkcji korzyści. 

Po włączeniu automatycznej rejestracji zadanie jest uruchamiane codziennie w celu wykrycia, czy SQL Server jest zainstalowana na wszystkich niezarejestrowanych maszynach wirtualnych w ramach subskrypcji. W tym celu należy skopiować pliki binarne rozszerzenia agenta SQL IaaS do maszyny wirtualnej, a następnie uruchomić narzędzie jednorazowe, które sprawdza gałąź rejestru SQL Server. Jeśli zostanie wykryta SQL Server Hive, maszyna wirtualna zostanie zarejestrowana z rozszerzeniem w trybie uproszczonym. Jeśli w rejestrze nie istnieje gałąź SQL Server, pliki binarne zostaną usunięte.

Po włączeniu automatycznej rejestracji w ramach subskrypcji wszystkie bieżące i przyszłe maszyny wirtualne, na których zainstalowano SQL Server, zostaną zarejestrowane przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonym. Nadal musisz [ręcznie uaktualnić do trybu pełnego zarządzania](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) , aby skorzystać z pełnego zestawu funkcji. 

> [!IMPORTANT]
> Rozszerzenie SQL IaaS Agent zbiera dane w celu udzielenia klientom opcjonalnych korzyści w przypadku korzystania z SQL Server w ramach Virtual Machines platformy Azure. Firma Microsoft nie będzie używać tych danych do inspekcji licencjonowania bez wcześniejszej zgody klienta. Aby uzyskać więcej informacji, zobacz [dodatek SQL Server privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu rozszerzenia, będziesz potrzebować: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/) i co najmniej uprawnienia [roli współautor](../../../role-based-access-control/built-in-roles.md#all) .
- Model zasobów platformy Azure [systemu Windows Server 2008 R2 (lub nowszego)](../../../virtual-machines/windows/quick-create-portal.md) z [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) wdrożony w chmurze publicznej lub Azure Government. System Windows Server 2008 nie jest obsługiwany. 


## <a name="enable"></a>Włącz

Aby włączyć automatyczną rejestrację maszyn wirtualnych SQL Server w Azure Portal, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do strony zasobów [**maszyn wirtualnych SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Wybierz pozycję **automatyczne SQL Server Rejestracja maszyny wirtualnej** , aby otworzyć stronę **rejestracji automatycznej** . 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="Wybierz automatyczną rejestrację maszyny wirtualnej SQL Server, aby otworzyć stronę rejestracji automatycznej":::

1. Wybierz swoją subskrypcję z listy rozwijanej. 
1. Przeczytaj warunki i jeśli zgadzasz się, zaznacz opcję **Akceptuję**. 
1. Wybierz pozycję **zarejestruj** , aby włączyć funkcję i automatycznie zarejestrować wszystkie bieżące i przyszłe maszyny wirtualne SQL Server przy użyciu rozszerzenia agenta SQL IaaS. Nie spowoduje to ponownego uruchomienia usługi SQL Server na żadnej z maszyn wirtualnych. 

## <a name="disable"></a>Wyłącz

Użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/install-az-ps) , aby wyłączyć funkcję automatycznej rejestracji. Gdy funkcja automatycznej rejestracji jest wyłączona, SQL Server maszyny wirtualne dodane do subskrypcji muszą zostać ręcznie zarejestrowane przy użyciu rozszerzenia agenta SQL IaaS. Nie spowoduje to wyrejestrowania istniejących maszyn wirtualnych SQL Server, które zostały już zarejestrowane.



# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyłączyć automatyczną rejestrację przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenie: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

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

Uaktualnij tryb zarządzania do [pełnego](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) , aby skorzystać z pełnego zestawu funkcji dostarczonego przez rozszerzenie programu SQL IaaS Agent. 
