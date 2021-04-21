---
title: Rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS
description: Zarejestruj maszynę wirtualną z programem Azure SQL Server przy użyciu rozszerzenia agenta SQL IaaS, aby włączyć funkcje dla maszyn wirtualnych programu SQL Server wdrożonych poza programem Azure Marketplace, a także zgodność i lepsze możliwości zarządzania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: e34876c76259b8274e0b0ef9059659802eb55cf1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765453"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Rejestrowanie SQL Server wirtualnej przy użyciu rozszerzenia agenta SQL IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Zarejestrowanie maszyny SQL Server wirtualnej przy użyciu rozszerzenia [agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) w celu odblokowania bogatych korzyści związanych z funkcjami dla maszyny SQL Server wirtualnej platformy Azure. 

W tym artykule nauczysz się rejestrować pojedynczą maszynę wirtualną SQL Server z rozszerzeniem agenta SQL IaaS. Alternatywnie możesz zarejestrować wszystkie maszyny SQL Server wirtualne [](sql-agent-extension-automatic-registration-all-vms.md) automatycznie lub wiele maszyn wirtualnych, których skrypty [są tworzone zbiorczo.](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="overview"></a>Omówienie

Zarejestrowanie przy [użyciu SQL Server agenta IaaS](sql-server-iaas-agent-extension-automate-management.md) powoduje utworzenie zasobu maszyny wirtualnej  **SQL**  w ramach subskrypcji, który jest zasobem oddzielnym od zasobu maszyny wirtualnej. Wyrejestrowanie maszyny SQL Server wirtualnej z rozszerzenia spowoduje usunięcie zasobu maszyny wirtualnej **SQL,**  ale nie spowoduje usunięcia rzeczywistej maszyny wirtualnej.

Wdrożenie obrazu SQL Server wirtualnej Azure Marketplace za pośrednictwem Azure Portal automatycznie rejestruje maszynę wirtualną SQL Server wirtualnej przy użyciu rozszerzenia. Jeśli jednak zdecydujesz się samodzielnie zainstalować program SQL Server na maszynie wirtualnej platformy Azure lub aprowizować maszynę wirtualną platformy Azure z niestandardowego wirtualnego dysku twardego, musisz zarejestrować maszynę wirtualną usługi SQL Server przy użyciu rozszerzenia agenta SQL IaaS, aby odblokować pełne korzyści funkcji i możliwości zarządzania. 

Aby korzystać z rozszerzenia agenta SQL IaaS, należy najpierw zarejestrować subskrypcję u dostawcy [ **Microsoft.SqlVirtualMachine,**](#register-subscription-with-resource-provider)co daje rozszerzeniu SQL IaaS możliwość tworzenia zasobów w ramach tej konkretnej subskrypcji.

> [!IMPORTANT]
> Rozszerzenie agenta SQL IaaS zbiera dane w celu nadawania klientom opcjonalnych korzyści podczas korzystania SQL Server w ramach usługi Azure Virtual Machines. Firma Microsoft nie będzie używać tych danych do przeprowadzania inspekcji licencjonowania bez uprzedniej zgody klienta. Zobacz uzupełnienie [SQL Server prywatności,](/sql/sql-server/sql-server-privacy#non-personal-data) aby uzyskać więcej informacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do zarejestrowania SQL Server wirtualnej z rozszerzeniem potrzebne są: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Maszyna wirtualna azure resource model z systemem [Windows Server 2008 (lub większym)](../../../virtual-machines/windows/quick-create-portal.md) z programem [SQL Server 2008 (lub większym)](https://www.microsoft.com/sql-server/sql-server-downloads) wdrożonym w publicznej lub Azure Government chmurze. 
- Najnowsza wersja interfejsu wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) [lub Azure PowerShell (minimum 5.0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-resource-provider"></a>Rejestrowanie subskrypcji przy użyciu dostawcy zasobów

Aby zarejestrować swoją SQL Server wirtualną z rozszerzeniem agenta SQL IaaS, musisz najpierw zarejestrować swoją subskrypcję u dostawcy zasobów **Microsoft.SqlVirtualMachine.** Dzięki temu rozszerzenie agenta SQL IaaS może tworzyć zasoby w ramach subskrypcji.  Możesz to zrobić przy użyciu interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Otwórz okno Azure Portal i przejdź do **menu Wszystkie usługi.** 
1. Przejdź do **subskrypcji i** wybierz subskrypcję, która Cię interesuje.  
1. Na **stronie Subskrypcje** przejdź do **rozszerzenia**. 
1. Wprowadź **wartość sql** w filtrze, aby wyprowadzić rozszerzenia związane z bazą danych SQL. 
1. Wybierz **pozycję** Zarejestruj, **Zarejestruj ponownie** lub **Wyrejestruj** dla dostawcy  **Microsoft.SqlVirtualMachine,** w zależności od wybranej akcji. 

   ![Modyfikowanie dostawcy](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Wiersz polecenia

Zarejestruj subskrypcję platformy Azure u **dostawcy Microsoft.SqlVirtualMachine** przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Rejestrowanie przy użyciu rozszerzenia

Istnieją trzy tryby zarządzania dla [SQL Server agenta IaaS.](sql-server-iaas-agent-extension-automate-management.md#management-modes) 

Zarejestrowanie rozszerzenia w trybie pełnego zarządzania powoduje ponowne uruchomienie usługi SQL Server, dlatego zaleca się [](#upgrade-to-full) zarejestrowanie rozszerzenia w trybie lekkim, a następnie uaktualnienie do pełnej wersji w oknie obsługi. 

### <a name="lightweight-management-mode"></a>Tryb uproszczonego zarządzania

Użyj interfejsu wiersza polecenia platformy Azure Azure PowerShell, aby zarejestrować maszynę wirtualną SQL Server z rozszerzeniem w trybie lekkim. Nie spowoduje to ponownego uruchomienia SQL Server usługi. Następnie można uaktualnić do trybu pełnego w dowolnym momencie, ale spowoduje to ponowne uruchomienie usługi SQL Server, dlatego zaleca się zaczekanie na zaplanowane okno obsługi. 

Podaj typ SQL Server jako płatność zgodnie z użyciem (), aby płacić za użycie, Korzyść użycia hybrydowego platformy Azure ( ) w celu użycia własnej licencji lub odzyskiwanie po awarii ( ), aby aktywować bezpłatną licencję repliki odzyskiwania po `PAYG` `AHUB` `DR` [awarii.](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)

Wystąpienia klastra trybu failover i wdrożenia z wieloma wystąpieniami można zarejestrować tylko przy użyciu rozszerzenia agenta SQL IaaS w trybie lekkim. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Zarejestruj maszynę wirtualną SQL Server w trybie lekkim za pomocą interfejsu wiersza polecenia platformy Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Zarejestruj maszynę SQL Server wirtualną w trybie lekkim za pomocą Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Tryb pełnego zarządzania

Zarejestrowanie maszyny SQL Server wirtualnej w trybie pełnym spowoduje ponowne uruchomienie SQL Server wirtualnej. Należy zachować ostrożność podczas wykonywania dalszych czynności. 

Aby zarejestrować maszynę SQL Server wirtualną bezpośrednio w trybie pełnym (i prawdopodobnie ponownie uruchomić usługę SQL Server), użyj następującego Azure PowerShell polecenia: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Tryb zarządzania NoAgent 

SQL Server 2008 i 2008 R2 zainstalowane w systemie Windows Server 2008 (nie _R2)_ można zarejestrować przy użyciu rozszerzenia agenta SQL IaaS w [trybie NoAgent.](sql-server-iaas-agent-extension-automate-management.md#management-modes) Ta opcja zapewnia zgodność i umożliwia monitorowanie SQL Server wirtualnej na Azure Portal z ograniczoną funkcjonalnością.


Dla typu **licencji określ:** `AHUB` , lub `PAYG` `DR` . Dla oferty **obrazu określ** wartość `SQL2008-WS2008` lub `SQL2008R2-WS2008`

Aby zarejestrować swoje wystąpienie SQL Server 2008 ( ) lub `SQL2008-WS2008` 2008 R2 ( ) w wystąpieniu systemu Windows Server 2008, użyj następującego fragmentu kodu Azure PowerShell interfejsu wiersza `SQL2008R2-WS2008` polecenia platformy Azure: 


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Zarejestruj swoją SQL Server wirtualną w trybie NoAgent przy użyciu interfejsu wiersza polecenia platformy Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Zarejestruj swoją SQL Server wirtualną w trybie NoAgent za pomocą Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Tryb weryfikacji

Bieżący tryb agenta IaaS usługi SQL Server można wyświetlić przy użyciu Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Uaktualnianie do pełnej wersji  

SQL Server maszyn wirtualnych, które zarejestrowała rozszerzenie w  trybie lekkim, można uaktualnić do pełnego przy użyciu interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.  SQL Server maszyn wirtualnych w _trybie NoAgent_  można uaktualnić do pełnej wersji po uaktualnieniu systemu operacyjnego do systemu Windows 2008 R2 lub jego wersji. Nie można obniżyć poziomu — w tym celu [](#unregister-from-extension) należy wyrejestrować maszynę wirtualną SQL Server wirtualnej z rozszerzenia agenta SQL IaaS. Spowoduje to usunięcie zasobu **maszyny wirtualnej SQL,** _ale_ nie spowoduje usunięcia rzeczywistej maszyny wirtualnej. 

> [!NOTE]
> Uaktualnienie trybu zarządzania rozszerzenia SQL IaaS do pełnego spowoduje ponowne uruchomienie SQL Server usługi. W niektórych przypadkach ponowne uruchomienie może spowodować zmianę nazw głównych usług (SPN) skojarzonych z usługą SQL Server na niewłaściwe konto użytkownika. Jeśli masz problemy z łącznością po uaktualnieniu trybu zarządzania do pełnego, wyrejestruj i ponownie [zarejestruj swoje sieci SPN.](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)


### <a name="azure-portal"></a>Azure Portal

Aby uaktualnić rozszerzenie do trybu pełnego przy użyciu Azure Portal, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do zasobu [maszyn wirtualnych SQL.](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 
1. Wybierz maszynę SQL Server wirtualną, a następnie wybierz pozycję **Przegląd.** 
1. W SQL Server wirtualnych z trybem NoAgent lub lekkim trybem IaaS wybierz opcję Tylko aktualizacje typu licencji i wersji są dostępne z komunikatem Rozszerzenie **SQL IaaS.**

   ![Opcje zmiany trybu z portalu](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Zaznacz pole **wyboru Wyrażam zgodę** na ponowne SQL Server usługi na  maszynie wirtualnej, a następnie wybierz pozycję Potwierdź, aby uaktualnić tryb IaaS do pełnego. 

    ![Pole wyboru dla wyrażania zgody na ponowne SQL Server usługi na maszynie wirtualnej](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Wiersz polecenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Aby uaktualnić rozszerzenie do trybu pełnego, uruchom następujący fragment kodu interfejsu wiersza polecenia platformy Azure:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Aby uaktualnić rozszerzenie do trybu pełnego, uruchom następujące Azure PowerShell kodu:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Weryfikowanie stanu rejestracji
Możesz sprawdzić, czy maszyna wirtualna SQL Server została już zarejestrowana w rozszerzeniu agenta SQL IaaS przy użyciu programu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

Aby sprawdzić stan rejestracji przy użyciu Azure Portal, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Przejdź do [SQL Server wirtualnych.](manage-sql-vm-portal.md)
1. Wybierz swoją SQL Server wirtualną z listy. Jeśli Twoja SQL Server wirtualna nie jest wymieniona w tym miejscu, prawdopodobnie nie została zarejestrowana przy użyciu rozszerzenia agenta SQL IaaS. 
1. Wyświetl wartość w obszarze **Stan**. Jeśli **stan** to **Succeeded**(Powodzenie), maszyna SQL Server wirtualna została pomyślnie zarejestrowana przy użyciu rozszerzenia agenta SQL IaaS. 

   ![Weryfikowanie stanu przy użyciu rejestracji jednostki żądania SQL](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Wiersz polecenia

Sprawdź bieżący stan SQL Server maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. `ProvisioningState` Jeśli rejestracja `Succeeded` powiodła się, zostanie pokazana. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Aby sprawdzić stan rejestracji przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujący fragment kodu:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Aby sprawdzić stan rejestracji przy użyciu Azure PowerShell, uruchom następujący fragment kodu:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Błąd wskazuje, że maszyna SQL Server wirtualna nie została zarejestrowana w rozszerzeniu. 


## <a name="unregister-from-extension"></a>Wyrejestruj z rozszerzenia

Aby wyrejestrować maszynę wirtualną SQL Server z rozszerzeniem agenta SQL IaaS, usuń zasób maszyny wirtualnej SQL przy użyciu interfejsu Azure Portal wiersza polecenia platformy Azure.  Usunięcie zasobu maszyny *wirtualnej* SQL nie powoduje usunięcia SQL Server wirtualnej. Należy jednak zachować ostrożność i ostrożnie postępować zgodnie z instrukcjami, ponieważ istnieje możliwość przypadkowego usunięcia maszyny wirtualnej podczas próby usunięcia *zasobu*. 

Wyrejestrowanie maszyny wirtualnej SQL z rozszerzeniem agenta SQL IaaS jest konieczne, aby obniżyć tryb zarządzania z pełnego. 

### <a name="azure-portal"></a>Azure Portal

Aby wyrejestrować maszynę wirtualną SQL Server z rozszerzenia przy użyciu Azure Portal, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Przejdź do zasobu maszyny wirtualnej SQL. 
  
   ![Zasób maszyn wirtualnych SQL](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Wybierz pozycję **Usuń**. 

   ![Wybieranie opcji Usuń w górnym panelu nawigacyjnym](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Wpisz nazwę maszyny wirtualnej SQL i wyczyść pole wyboru **obok maszyny wirtualnej**.

   ![Usuń zaznaczenie maszyny wirtualnej, aby uniemożliwić usunięcie rzeczywistej maszyny wirtualnej, a następnie wybierz pozycję Usuń, aby kontynuować usuwanie zasobu maszyny wirtualnej SQL](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Jeśli nie można wyczyścić pola wyboru obok nazwy maszyny wirtualnej, *maszyna wirtualna* zostanie całkowicie usunąć. Wyczyść pole wyboru, aby wyrejestrować maszynę wirtualną SQL Server z rozszerzenia, ale nie usunąć *rzeczywistej maszyny wirtualnej.* 

1. Wybierz **pozycję Usuń,** aby potwierdzić usunięcie zasobu maszyny wirtualnej *SQL,* a nie SQL Server wirtualnej. 

### <a name="command-line"></a>Wiersz polecenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyrejestrować maszynę wirtualną SQL Server z rozszerzenia za pomocą interfejsu wiersza polecenia platformy Azure, użyj [polecenia az sql vm delete.](/cli/azure/sql/vm#az_sql_vm_delete) Spowoduje to usunięcie zasobu SQL Server *wirtualnej,* ale nie spowoduje usunięcia maszyny wirtualnej. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyrejestrować maszynę wirtualną SQL Server z rozszerzenia za pomocą polecenia Azure PowerShell, użyj polecenia [Remove-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm) Spowoduje to usunięcie zasobu SQL Server *wirtualnej,* ale nie spowoduje usunięcia maszyny wirtualnej. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server maszyny wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)  
* [Wskazówki dotyczące cen SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
* [Informacje o wersji dla SQL Server na maszynie wirtualnej z systemem Windows](../../database/doc-changes-updates-release-notes.md)
