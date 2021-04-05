---
title: Zmiana modelu licencji dla maszyny wirtualnej SQL na platformie Azure
description: Dowiedz się, jak przełączać licencję na SQL Server maszynę wirtualną na platformie Azure, korzystając z opcji płatność zgodnie z rzeczywistym użyciem, aby przystąpić do posiadania licencji przy użyciu Korzyść użycia hybrydowego platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5813331d5eafd953d776dd19d9cc885ff71b8be0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361557"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Zmienianie modelu licencjonowania maszyny wirtualnej z programem SQL na platformie Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


W tym artykule opisano, jak zmienić model licencji dla SQL Server maszyny wirtualnej na platformie Azure przy użyciu [rozszerzenia agenta SQL IaaS](./sql-server-iaas-agent-extension-automate-management.md).

## <a name="overview"></a>Omówienie

Istnieją trzy modele licencji dla maszyny wirtualnej platformy Azure, która obsługuje SQL Server: płatność zgodnie z rzeczywistym użyciem, Korzyść użycia hybrydowego platformy Azure (AHB) i wysoka dostępność/odzyskiwanie po awarii (HA/DR). Model licencji maszyny wirtualnej SQL Server można zmodyfikować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

- Model **płatność zgodnie z rzeczywistym** użyciem oznacza, że koszt korzystania z maszyny wirtualnej platformy Azure na sekundę jest uwzględniany w koszcie SQL Server licencji.
- [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) umożliwia korzystanie z własnej licencji SQL Server z maszyną wirtualną, na której działa SQL Server. 
- Typ licencji **ha/Dr** jest używany w przypadku [bezpłatnej repliki ha/Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) na platformie Azure. 

Korzyść użycia hybrydowego platformy Azure umożliwia korzystanie z licencji SQL Server z programem Software Assurance ("kwalifikowana Licencja") na maszynach wirtualnych platformy Azure. W przypadku Korzyść użycia hybrydowego platformy Azure klienci nie są obciążani opłatami za korzystanie z licencji SQL Server na maszynie wirtualnej. Jednak nadal muszą płacić za koszt zasobów obliczeniowych w chmurze (czyli stawka podstawowa), magazyn i kopie zapasowe. Muszą również uiścić opłaty za we/wy powiązane z użyciem usług (stosownie do obowiązujących).

Zgodnie z [postanowieniami produktu](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)firmy Microsoft: "klienci muszą wskazywać, że używają Azure SQL Database (wystąpienie zarządzane, Pula elastyczna i pojedyncza baza danych), Azure Data Factory, SQL Server Integration Services lub SQL Server Virtual Machines w Korzyść użycia hybrydowego platformy Azure dla SQL Server podczas konfigurowania obciążeń na platformie Azure".

Aby wskazać użycie Korzyść użycia hybrydowego platformy Azure dla SQL Server na maszynie wirtualnej platformy Azure i być zgodne, masz trzy opcje:

- Zainicjuj obsługę administracyjną maszyny wirtualnej, korzystając z obrazu "Przenieś własne SQL Server licencji" z witryny Azure Marketplace. Ta opcja jest dostępna tylko dla klientów, którzy mają Enterprise Agreement.
- Zainicjuj obsługę administracyjną maszyny wirtualnej przy użyciu obrazu z opcją płatność zgodnie z SQL Server rzeczywistym użyciem z witryny Azure Marketplace i aktywuj Korzyść użycia hybrydowego platformy Azure.
- Samoinstalując SQL Server na maszynie wirtualnej platformy Azure, [zarejestruj się ręcznie przy użyciu rozszerzenia agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md)i aktywuj korzyść użycia hybrydowego platformy Azure.

Typ licencji SQL Server można skonfigurować, gdy maszyna wirtualna jest obsługiwana lub w dowolnym momencie później. Przełączanie między modelami licencji nie powoduje przestoju, nie powoduje ponownego uruchomienia maszyny wirtualnej ani usługi SQL Server, nie powoduje dodania dodatkowych kosztów i obowiązuje od razu. W rzeczywistości aktywowanie Korzyść użycia hybrydowego platformy Azure *obniża* koszty.

## <a name="prerequisites"></a>Wymagania wstępne

Zmiana modelu licencjonowania maszyny wirtualnej SQL Server ma następujące wymagania: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- [Maszyna wirtualna SQL Server](./create-sql-vm-portal.md) zarejestrowana w [rozszerzeniu programu SQL IaaS Agent](./sql-server-iaas-agent-extension-automate-management.md).
- [Program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) jest wymagana do korzystania z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Zmiana modelu licencji

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Możesz zmodyfikować model licencji bezpośrednio z portalu: 

1. Otwórz [Azure Portal](https://portal.azure.com) i Otwórz zasób usługi [SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) dla maszyny wirtualnej SQL Server. 
1. W obszarze **Ustawienia** wybierz pozycję **Konfiguruj** . 
1. Wybierz opcję **korzyść użycia hybrydowego platformy Azure** , a następnie zaznacz pole wyboru, aby potwierdzić, że masz licencję na SQL Server z programem Software Assurance. 
1. Wybierz pozycję **Zastosuj** w dolnej części strony **Konfigurowanie** . 

![Korzyść użycia hybrydowego platformy Azure w portalu](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zmienić model licencji, możesz użyć interfejsu wiersza polecenia platformy Azure.  

Określ następujące wartości dla **typu licencji**:
- `AHUB` dla Korzyść użycia hybrydowego platformy Azure
- `PAYG` płatność zgodnie z rzeczywistym użyciem
- `DR` Aby aktywować bezpłatną replikę HA/DR


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby zmienić model licencji, możesz użyć programu PowerShell.

Określ następujące wartości dla **typu licencji**:
- `AHUB` dla Korzyść użycia hybrydowego platformy Azure
- `PAYG` płatność zgodnie z rzeczywistym użyciem
- `DR` Aby aktywować bezpłatną replikę HA/DR


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Uwagi

- Klienci korzystający z usługi Azure Cloud Solution Provider (CSP) mogą używać Korzyść użycia hybrydowego platformy Azure, najpierw wdrażając maszynę wirtualną z płatność zgodnie z rzeczywistym użyciem, a następnie konwertując ją na własną licencję, jeśli mają aktywny program Software Assurance.
- Jeśli porzucisz zasób maszyny wirtualnej SQL, powrócisz do ustawienia ustalonej licencji dla obrazu. 
- Możliwość zmiany modelu licencji to funkcja rozszerzenia SQL IaaS Agent. Wdrożenie obrazu portalu Azure Marketplace za pomocą Azure Portal powoduje automatyczne zarejestrowanie maszyny wirtualnej SQL Server z rozszerzeniem. Jednak klienci, którzy samodzielnie instalują SQL Server, będą musieli ręcznie [zarejestrować swoją SQL Serverą maszynę wirtualną](sql-agent-extension-manually-register-single-vm.md). 
- Dodanie maszyny wirtualnej SQL Server do zestawu dostępności wymaga ponownego utworzenia maszyny wirtualnej. W związku z tym wszystkie maszyny wirtualne dodane do zestawu dostępności zostaną przywrócone do domyślnego typu licencji płatność zgodnie z rzeczywistym użyciem. Korzyść użycia hybrydowego platformy Azure należy ponownie włączyć. 


## <a name="limitations"></a>Ograniczenia

Zmiana modelu licencji to:
   - Dostępne tylko dla klientów z programem [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Obsługiwane tylko w wersjach Standard i Enterprise SQL Server. Zmiany licencji dla ekspresowych, sieci Web i dewelopera nie są obsługiwane. 
   - Obsługiwane tylko w przypadku maszyn wirtualnych wdrożonych za pomocą modelu Azure Resource Manager. Maszyny wirtualne wdrożone za pomocą modelu klasycznego nie są obsługiwane. 
   - Dostępne tylko dla chmur publicznych lub Azure Government. 

> [!Note]
> Korzyść użycia hybrydowego platformy Azure nie kwalifikują się tylko do SQL Server licencjonowania opartego na rdzeniach z programem Software Assurance lub licencją subskrypcyjną. Jeśli używasz licencjonowania serwera i licencji CAL na SQL Server i masz program Software Assurance, możesz użyć funkcji "Przenieś własną licencję" do obrazu maszyny wirtualnej platformy Azure SQL Server, aby korzystać z mobilności licencji dla tych serwerów, ale nie możesz korzystać z innych cech Korzyść użycia hybrydowego platformy Azure. 

## <a name="known-errors"></a>Znane błędy

Przejrzyj często znane błędy i ich rozwiązania. 

**Nie znaleziono zasobu "Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> " w grupie zasobów " \<resource-group> ".**

Ten błąd występuje podczas próby zmiany modelu licencji na SQL Server maszynie wirtualnej, która nie została zarejestrowana przy użyciu rozszerzenia agenta SQL Server IaaS:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Musisz zarejestrować swoją subskrypcję u dostawcy zasobów, a następnie [zarejestrować maszynę wirtualną SQL Server przy użyciu rozszerzenia programu SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](../../database/doc-changes-updates-release-notes.md)
* [Przegląd rozszerzenia usługi SQL IaaS Agent](./sql-server-iaas-agent-extension-automate-management.md)
