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
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b09d808201d58b571b2fe5ceb2e228d4e1c21d11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316957"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Zmienianie modelu licencjonowania maszyny wirtualnej z programem SQL na platformie Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


W tym artykule opisano, jak zmienić model licencji dla SQL Server maszyny wirtualnej na platformie Azure przy użyciu nowego SQL Server dostawcy zasobów maszyny wirtualnej, **Microsoft. SqlVirtualMachine**.

Istnieją trzy modele licencji dla maszyny wirtualnej, która obsługuje SQL Server: płatność zgodnie z rzeczywistym użyciem, Korzyść użycia hybrydowego platformy Azure (AHB) i odzyskiwanie po awarii (DR). Model licencji maszyny wirtualnej SQL Server można zmodyfikować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

- Model **płatność zgodnie z rzeczywistym** użyciem oznacza, że koszt korzystania z maszyny wirtualnej platformy Azure na sekundę jest uwzględniany w koszcie SQL Server licencji.
- [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) umożliwia korzystanie z własnej licencji SQL Server z maszyną wirtualną, na której działa SQL Server. 
- Typ licencji **odzyskiwania po awarii** jest używany dla [bezpłatnej repliki Dr](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) na platformie Azure. 

Korzyść użycia hybrydowego platformy Azure umożliwia korzystanie z licencji SQL Server z programem Software Assurance ("kwalifikowana Licencja") na maszynach wirtualnych platformy Azure. W przypadku Korzyść użycia hybrydowego platformy Azure klienci nie są obciążani opłatami za korzystanie z licencji SQL Server na maszynie wirtualnej. Jednak nadal muszą płacić za koszt zasobów obliczeniowych w chmurze (czyli stawka podstawowa), magazyn i kopie zapasowe. Muszą również uiścić opłaty za we/wy powiązane z użyciem usług (stosownie do obowiązujących).

Zgodnie z postanowieniami produktu firmy Microsoft: "klienci muszą wskazywać, że używają Azure SQL Database (wystąpienie zarządzane, Pula elastyczna i pojedyncza baza danych), Azure Data Factory, SQL Server Integration Services lub SQL Server Virtual Machines w korzyść użycia hybrydowego platformy Azure dla SQL Server podczas konfigurowania obciążeń na platformie Azure".

Aby wskazać użycie Korzyść użycia hybrydowego platformy Azure dla SQL Server na maszynie wirtualnej platformy Azure i być zgodne, masz trzy opcje:

- Zainicjuj obsługę administracyjną maszyny wirtualnej, korzystając z obrazu "Przenieś własne SQL Server licencji" z witryny Azure Marketplace. Ta opcja jest dostępna tylko dla klientów, którzy mają Umowa Enterprise.
- Zainicjuj obsługę administracyjną maszyny wirtualnej przy użyciu obrazu z opcją płatność zgodnie z SQL Server rzeczywistym użyciem z witryny Azure Marketplace i aktywuj Korzyść użycia hybrydowego platformy Azure.
- Samoinstalując SQL Server na maszynie wirtualnej platformy Azure, [zarejestruj się ręcznie przy użyciu dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md)i aktywuj korzyść użycia hybrydowego platformy Azure.

Typ licencji SQL Server można skonfigurować, gdy maszyna wirtualna jest obsługiwana lub w dowolnym momencie później. Przełączanie między modelami licencji nie powoduje przestoju, nie powoduje ponownego uruchomienia maszyny wirtualnej ani usługi SQL Server, nie powoduje dodania dodatkowych kosztów i obowiązuje od razu. W rzeczywistości aktywowanie Korzyść użycia hybrydowego platformy Azure *obniża* koszty.

## <a name="prerequisites"></a>Wymagania wstępne

Zmiana modelu licencjonowania maszyny wirtualnej SQL Server ma następujące wymagania: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- [Maszyna wirtualna SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zarejestrowana w [dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md).
- [Program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) jest wymagana do korzystania z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Maszyny wirtualne już zarejestrowane z dostawcą zasobów 

# <a name="the-azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Możesz zmodyfikować model licencji bezpośrednio z portalu: 

1. Otwórz [Azure Portal](https://portal.azure.com) i Otwórz zasób usługi [SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) dla maszyny wirtualnej SQL Server. 
1. W obszarze **Ustawienia**wybierz pozycję **Konfiguruj** . 
1. Wybierz opcję **korzyść użycia hybrydowego platformy Azure** , a następnie zaznacz pole wyboru, aby potwierdzić, że masz licencję na SQL Server z programem Software Assurance. 
1. Wybierz pozycję **Zastosuj** w dolnej części strony **Konfigurowanie** . 

![Korzyść użycia hybrydowego platformy Azure w portalu](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zmienić model licencji, możesz użyć interfejsu wiersza polecenia platformy Azure.  


**Korzyść użycia hybrydowego platformy Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Płatność zgodnie z rzeczywistym**użyciem: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Odzyskiwanie awaryjne (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby zmienić model licencji, możesz użyć programu PowerShell.

**Korzyść użycia hybrydowego platformy Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Płatność zgodnie z rzeczywistym użyciem**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Odzyskiwanie po awarii** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Maszyny wirtualne nie są zarejestrowane dla dostawcy zasobów

Jeśli zainicjowano SQL Server maszynę wirtualną z obrazów systemu Azure Marketplace z opcją płatność zgodnie z rzeczywistym użyciem, typ licencji SQL Server będzie miał płatność zgodnie z rzeczywistym użyciem. Jeśli zainicjowano SQL Server maszynę wirtualną przy użyciu obrazu "Przenieś własną licencję" z portalu Azure Marketplace, typ licencji zostanie — AHUB. Wszystkie SQL Server maszyny wirtualne, które zostały udostępnione z domyślnych (płatność zgodnie z rzeczywistym użyciem) lub na podstawie własnych licencji, są automatycznie rejestrowane przy użyciu dostawcy zasobów maszyny wirtualnej SQL, dzięki czemu mogą zmienić [Typ licencji](#vms-already-registered-with-the-resource-provider).

Możesz zakwalifikować się tylko do samoinstalowania SQL Server na maszynie wirtualnej platformy Azure za pośrednictwem Korzyść użycia hybrydowego platformy Azure. Należy [zarejestrować te maszyny wirtualne przy użyciu dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md) , ustawiając SQL Server licencję jako korzyść użycia hybrydowego platformy Azure, aby wskazać korzyść użycia hybrydowego platformy Azure użycie zgodnie z postanowieniami produktu firmy Microsoft.

Typ licencji maszyny wirtualnej SQL Server można zmienić jako płatność zgodnie z rzeczywistym użyciem lub Korzyść użycia hybrydowego platformy Azure tylko wtedy, gdy maszyna wirtualna SQL Server jest zarejestrowana u dostawcy zasobów maszyny wirtualnej SQL.

## <a name="remarks"></a>Uwagi

- Klienci korzystający z usługi Azure Cloud Solution Provider (CSP) mogą używać Korzyść użycia hybrydowego platformy Azure, najpierw wdrażając maszynę wirtualną z płatność zgodnie z rzeczywistym użyciem, a następnie konwertując ją na własną licencję, jeśli mają aktywny program Software Assurance.
- W przypadku porzucenia SQL Server zasobów maszyny wirtualnej powrócisz do ustawienia ustalonej licencji dla obrazu. 
- Możliwość zmiany modelu licencji to funkcja dostawcy zasobów maszyny wirtualnej SQL. Wdrożenie obrazu portalu Azure Marketplace za pomocą Azure Portal powoduje automatyczne zarejestrowanie SQL Serverj maszyny wirtualnej przy użyciu dostawcy zasobów. Jednak klienci, którzy samodzielnie instalują SQL Server, będą musieli ręcznie [zarejestrować swoją SQL Serverą maszynę wirtualną](sql-vm-resource-provider-register.md). 
- Dodanie maszyny wirtualnej SQL Server do zestawu dostępności wymaga ponownego utworzenia maszyny wirtualnej. W związku z tym wszystkie maszyny wirtualne dodane do zestawu dostępności zostaną przywrócone do domyślnego typu licencji płatność zgodnie z rzeczywistym użyciem. Korzyść użycia hybrydowego platformy Azure należy ponownie włączyć. 


## <a name="limitations"></a>Ograniczenia

Zmiana modelu licencji to:
   - Dostępne tylko dla klientów z programem [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Obsługiwane tylko w wersjach Standard i Enterprise SQL Server. Zmiany licencji dla ekspresowych, sieci Web i dewelopera nie są obsługiwane. 
   - Obsługiwane tylko w przypadku maszyn wirtualnych wdrożonych za pomocą modelu Azure Resource Manager. Maszyny wirtualne wdrożone za pomocą modelu klasycznego nie są obsługiwane. 
   - Dostępne tylko dla chmur publicznych lub Azure Government. 
   - Obsługiwane tylko na maszynach wirtualnych, które mają pojedynczy interfejs sieciowy (NIC). 


## <a name="known-errors"></a>Znane błędy

Przejrzyj często znane błędy i ich rozwiązania. 

**Nie znaleziono zasobu "Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> " w grupie zasobów " \<resource-group> ".**

Ten błąd występuje podczas próby zmiany modelu licencji na SQL Server maszynie wirtualnej, która nie została zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Musisz zarejestrować swoją subskrypcję u dostawcy zasobów, a następnie [zarejestrować SQL Serverą maszynę wirtualną przy użyciu dostawcy zasobów](sql-vm-resource-provider-register.md). 


**Maszyna wirtualna " \<vmname\> " ma skojarzoną więcej niż jedną kartę sieciową**

Ten błąd występuje na maszynach wirtualnych, które mają więcej niż jedną kartę sieciową. Przed zmianą modelu licencjonowania Usuń jedną z kart sieciowych. Mimo że można dodać kartę sieciową z powrotem do maszyny wirtualnej po zmianie modelu licencji, operacje w Azure Portal, takie jak automatyczne tworzenie kopii zapasowych i stosowanie poprawek, nie będą już obsługiwane. 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](../../database/doc-changes-updates-release-notes.md)


