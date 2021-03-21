---
title: Zarejestruj się w rozszerzeniu programu SQL IaaS Agent
description: Zarejestruj maszynę wirtualną SQL Server platformy Azure przy użyciu rozszerzenia programu SQL IaaS Agent, aby włączyć funkcje dla SQL Server maszyn wirtualnych wdrożonych poza portalem Azure Marketplace, a także zapewnić zgodność i zwiększyć możliwości zarządzania.
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
ms.openlocfilehash: 983cdab0c5f5b856537c661c7427a83099f30ed4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181435"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Zarejestruj SQL Server maszynę wirtualną przy użyciu rozszerzenia programu SQL IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Zarejestrowanie SQL Server maszyny wirtualnej za pomocą [rozszerzenia programu SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) w celu odblokowania bogactwa zalet funkcji dla SQL Server na maszynie wirtualnej platformy Azure. 

W tym artykule opisano, jak zarejestrować pojedynczą maszynę wirtualną SQL Server przy użyciu rozszerzenia programu SQL IaaS Agent. Alternatywnie można rejestrować wszystkie SQL Server maszyny wirtualne [automatycznie](sql-agent-extension-automatic-registration-all-vms.md) lub [wiele maszyn wirtualnych w skrypcie zbiorczym](sql-agent-extension-manually-register-vms-bulk.md).


## <a name="overview"></a>Omówienie

Zarejestrowanie przy użyciu [rozszerzenia Agent SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) powoduje utworzenie _zasobu_ **maszyny wirtualnej SQL** w ramach subskrypcji, czyli _oddzielnego_ zasobu z zasobu maszyny wirtualnej. Wyrejestrowanie SQL Serverej maszyny wirtualnej z rozszerzenia spowoduje usunięcie _zasobu_ usługi **SQL Virtual** Machine, ale nie spowoduje porzucenia rzeczywistej maszyny wirtualnej.

Wdrożenie SQL Server maszyny wirtualnej w portalu Azure Marketplace za pomocą Azure Portal automatycznie rejestruje maszynę wirtualną SQL Server z rozszerzeniem. Jeśli jednak zdecydujesz się na samoinstalowanie SQL Server na maszynie wirtualnej platformy Azure lub zainicjowanie obsługi administracyjnej maszyny wirtualnej platformy Azure na podstawie niestandardowego wirtualnego dysku twardego, należy zarejestrować maszynę wirtualną SQL Server przy użyciu rozszerzenia programu SQL IaaS Agent w celu odblokowania pełnych korzyści z funkcji i możliwości zarządzania. 

Aby można było korzystać z rozszerzenia programu SQL IaaS Agent, należy najpierw [zarejestrować swoją subskrypcję u dostawcy **Microsoft. SqlVirtualMachine**](#register-subscription-with-rp), co zapewnia rozszerzeniu SQL IaaS możliwość tworzenia zasobów w ramach tej konkretnej subskrypcji.

> [!IMPORTANT]
> Rozszerzenie SQL IaaS Agent zbiera dane w celu udzielenia klientom opcjonalnych korzyści w przypadku korzystania z SQL Server w ramach Virtual Machines platformy Azure. Firma Microsoft nie będzie używać tych danych do inspekcji licencjonowania bez wcześniejszej zgody klienta. Aby uzyskać więcej informacji, zobacz [dodatek SQL Server privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu rozszerzenia, będziesz potrzebować: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Model zasobów platformy Azure z [systemem Windows Server 2008 (lub nowszym)](../../../virtual-machines/windows/quick-create-portal.md) z [SQL serverem 2008 (lub nowszym)](https://www.microsoft.com/sql-server/sql-server-downloads) wdrożonym w chmurze publicznej lub Azure Government. 
- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell (minimum 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Zarejestruj subskrypcję za pomocą RP

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu rozszerzenia agenta SQL IaaS, musisz najpierw zarejestrować subskrypcję w dostawcy **Microsoft. SqlVirtualMachine** . Dzięki temu rozszerzenie agenta SQL IaaS umożliwia tworzenie zasobów w ramach subskrypcji.  Można to zrobić za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Otwórz Azure Portal i przejdź do pozycji **wszystkie usługi**. 
1. Przejdź do pozycji **subskrypcje** i wybierz swoją subskrypcję.  
1. Na stronie **subskrypcje** przejdź do pozycji **rozszerzenia**. 
1. Wprowadź **SQL** w filtrze, aby wyświetlić rozszerzenia związane z programem SQL. 
1. Wybierz pozycję **zarejestruj**, **zarejestruj ponownie** lub **Wyrejestruj** dostawcę  **Microsoft. SqlVirtualMachine** , w zależności od żądanej akcji. 

   ![Modyfikowanie dostawcy](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Wiersz polecenia

Zarejestruj swoją subskrypcję platformy Azure za pomocą dostawcy **Microsoft. SqlVirtualMachine** przy użyciu interfejsu wiersza polecenia platformy azure lub Azure PowerShell. 

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

## <a name="register-with-extension"></a>Zarejestruj z rozszerzeniem

Istnieją trzy tryby zarządzania [rozszerzeniami SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Zarejestrowanie rozszerzenia w trybie pełnego zarządzania powoduje ponowne uruchomienie usługi SQL Server, więc zaleca się zarejestrowanie rozszerzenia w trybie uproszczonym, a następnie [przeprowadzenie uaktualnienia do pełnej](#upgrade-to-full) wersji w oknie obsługi. 

### <a name="lightweight-management-mode"></a>Uproszczony tryb zarządzania

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby zarejestrować maszynę wirtualną SQL Server z rozszerzeniem w trybie uproszczonym. Nie spowoduje to ponownego uruchomienia usługi SQL Server. Następnie można przeprowadzić uaktualnienie do trybu pełnego w dowolnym momencie, ale spowoduje to ponowne uruchomienie usługi SQL Server, dlatego zaleca się zaczekanie do czasu zaplanowanego okna obsługi. 

Podaj typ licencji SQL Server jako płatność zgodnie z rzeczywistym `PAYG` użyciem (), aby uregulować opłaty za użycie, korzyść użycia hybrydowego platformy Azure () w celu `AHUB` korzystania z własnej licencji lub odzyskiwania po awarii ( `DR` ) w celu aktywowania [bezpłatnej licencji na program Dr Replica](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Wystąpienia klastra trybu failover i wdrożenia z obsługą wiele wystąpień mogą być rejestrowane tylko przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonym. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Rejestrowanie maszyny wirtualnej SQL Server w trybie uproszczonym przy użyciu interfejsu wiersza polecenia platformy Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Zarejestruj maszynę wirtualną SQL Server w trybie uproszczonym z użyciem Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Tryb pełnego zarządzania

Zarejestrowanie maszyny wirtualnej SQL Server w trybie pełnym spowoduje ponowne uruchomienie usługi SQL Server. Należy zachować ostrożność. 

Aby zarejestrować SQL Server maszynę wirtualną bezpośrednio w trybie pełnym (a także ponownie uruchomić usługę SQL Server), użyj następującego polecenia Azure PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Tryb zarządzania bez agenta 

SQL Server 2008 i 2008 R2 zainstalowane w systemie Windows Server 2008 (_nie R2_) można zarejestrować przy użyciu rozszerzenia agenta SQL IaaS w [trybie noagent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Ta opcja zapewnia zgodność i umożliwia monitorowanie SQL Server maszyny wirtualnej w Azure Portal z ograniczoną funkcjonalnością.


W polu **Typ licencji** Określ wartość: `AHUB` , `PAYG` lub `DR` . W polu **Oferta obrazu** Określ wartość `SQL2008-WS2008` lub. `SQL2008R2-WS2008`

Aby zarejestrować SQL Server 2008 ( `SQL2008-WS2008` ) lub 2008 R2 ( `SQL2008R2-WS2008` ) w wystąpieniu systemu Windows Server 2008, użyj następującego interfejsu wiersza polecenia platformy Azure lub fragmentu kodu Azure PowerShell: 


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Zarejestruj SQL Server maszynę wirtualną w trybie noagent przy użyciu interfejsu wiersza polecenia platformy Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Zarejestruj SQL Server maszynę wirtualną w trybie noagent, używając Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Tryb weryfikacji

Bieżący tryb SQL Server agenta IaaS można wyświetlić za pomocą Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Uaktualnianie do pełnej wersji  

SQL Server maszyny wirtualne, które zarejestrowały rozszerzenie w trybie *uproszczonym* , można uaktualnić do _pełnej_ wersji przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. SQL Server maszyny wirtualne w trybie _noagent_ można uaktualnić do wersji _pełnej_ po uaktualnieniu systemu operacyjnego do wersji Windows 2008 R2 lub nowszej. Nie jest możliwe przeprowadzenie obniżenia — w tym celu należy [wyrejestrować](#unregister-from-extension) SQL Server maszynę wirtualną z rozszerzenia agenta SQL IaaS. Spowoduje to usunięcie _zasobu_ **maszyny wirtualnej SQL** , ale nie spowoduje usunięcia rzeczywistej maszyny wirtualnej. 

> [!NOTE]
> Po uaktualnieniu trybu zarządzania rozszerzenia SQL IaaS do pełnej zostanie ponownie uruchomiona usługa SQL Server. W niektórych przypadkach ponowne uruchomienie może spowodować, że nazwy główne usługi (SPN) skojarzone z usługą SQL Server nie zmienią na nieprawidłowe konto użytkownika. Jeśli masz problemy z łącznością po uaktualnieniu trybu zarządzania do pełnej, [Wyrejestruj i zarejestruj ponownie nazwy SPN](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections).


### <a name="azure-portal"></a>Azure Portal

Aby uaktualnić rozszerzenie do trybu pełnego przy użyciu Azure Portal, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do zasobu [maszyny wirtualnej SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) . 
1. Wybierz maszynę wirtualną SQL Server i wybierz pozycję **Przegląd**. 
1. W przypadku SQL Server maszyn wirtualnych z trybem noagent lub Lightweight IaaS wybierz opcję **jedyne typy licencji i aktualizacje wersji są dostępne w komunikacie rozszerzenia SQL IaaS** .

   ![Wybory dotyczące zmiany trybu z portalu](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Zaznacz pole wyboru **Zgadzam się na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej** , a następnie wybierz pozycję **Potwierdź** , aby uaktualnić tryb IaaS do pełnego. 

    ![Pole wyboru, aby wyrazić zgodę na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Wiersz polecenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Aby uaktualnić rozszerzenie do trybu pełnego, uruchom następujący fragment kodu interfejsu wiersza polecenia platformy Azure:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Aby uaktualnić rozszerzenie do trybu pełnego, uruchom następujący fragment kodu Azure PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Sprawdź stan rejestracji
Możesz sprawdzić, czy maszyna wirtualna SQL Server została już zarejestrowana w rozszerzeniu programu SQL IaaS Agent przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

Aby sprawdzić stan rejestracji przy użyciu Azure Portal, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Przejdź do [maszyn wirtualnych SQL Server](manage-sql-vm-portal.md).
1. Z listy wybierz maszynę wirtualną z SQL Server. Jeśli maszyna wirtualna w SQL Server nie jest wymieniona w tym miejscu, prawdopodobnie nie została zarejestrowana przy użyciu rozszerzenia programu SQL IaaS Agent. 
1. Wyświetl wartość w obszarze **stan**. Jeśli **stan** ma wartość **powodzenie**, maszyna wirtualna SQL Server została pomyślnie zarejestrowana w rozszerzeniu agenta SQL IaaS. 

   ![Weryfikowanie stanu przy użyciu rejestracji jednostki UZALEŻNIONej SQL](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Wiersz polecenia

Sprawdź bieżące SQL Server stanu rejestracji maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. `ProvisioningState` będzie wyświetlana, `Succeeded` Jeśli rejestracja zakończyła się pomyślnie. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/bash)

Aby sprawdzić stan rejestracji za pomocą interfejsu wiersza polecenia platformy Azure, uruchom następujący fragment kodu:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Aby sprawdzić stan rejestracji za pomocą Azure PowerShell, uruchom następujący fragment kodu:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Błąd oznacza, że maszyna wirtualna SQL Server nie została zarejestrowana przy użyciu rozszerzenia. 


## <a name="unregister-from-extension"></a>Wyrejestruj z rozszerzenia

Aby wyrejestrować maszynę wirtualną SQL Server za pomocą rozszerzenia agenta SQL IaaS, Usuń *zasób* maszyny wirtualnej SQL przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure. Usunięcie *zasobu* maszyny wirtualnej SQL nie powoduje usunięcia maszyny wirtualnej SQL Server. Należy jednak ostrożnie i postępować zgodnie z krokami, ponieważ można przypadkowo usunąć maszynę wirtualną podczas próby usunięcia *zasobu*. 

Wyrejestrowanie maszyny wirtualnej SQL przy użyciu rozszerzenia programu SQL IaaS Agent jest niezbędne do obniżenia poziomu trybu zarządzania od pełnego. 

### <a name="azure-portal"></a>Azure Portal

Aby wyrejestrować SQL Server maszynę wirtualną z rozszerzenia przy użyciu Azure Portal, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Przejdź do zasobu maszyny wirtualnej SQL. 
  
   ![Zasób maszyn wirtualnych SQL](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Wybierz pozycję **Usuń**. 

   ![Wybierz pozycję Usuń w górnym obszarze nawigacji](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Wpisz nazwę maszyny wirtualnej SQL i **Usuń zaznaczenie pola wyboru obok maszyny wirtualnej**.

   ![Usuń zaznaczenie maszyny wirtualnej, aby zapobiec usunięciu rzeczywistej maszyny wirtualnej, a następnie wybierz pozycję Usuń, aby kontynuować usuwanie zasobu maszyny wirtualnej SQL](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nie można wyczyścić pola wyboru obok nazwy maszyny wirtualnej. całkowicie *usunie* maszynę wirtualną. Usuń zaznaczenie pola wyboru, aby wyrejestrować SQL Server MASZYNę wirtualną z rozszerzenia, ale *nie usuwaj rzeczywistej maszyny wirtualnej*. 

1. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie *zasobu* maszyny wirtualnej SQL, a nie maszyny wirtualnej SQL Server. 

### <a name="command-line"></a>Wiersz polecenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyrejestrować SQL Server maszynę wirtualną z rozszerzenia przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ SQL VM Delete](/cli/azure/sql/vm#az-sql-vm-delete) . Spowoduje to usunięcie SQL Server *zasobów* maszyn wirtualnych, ale nie spowoduje usunięcia maszyny wirtualnej. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyrejestrować SQL Server maszynę wirtualną z rozszerzenia z Azure PowerShell, użyj polecenia [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Spowoduje to usunięcie SQL Server *zasobów* maszyn wirtualnych, ale nie spowoduje usunięcia maszyny wirtualnej. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)  
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](../../database/doc-changes-updates-release-notes.md)
