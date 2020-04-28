---
title: Integruj Key Vault z SQL Server na maszynach wirtualnych z systemem Windows na platformie Azure (Menedżer zasobów) | Microsoft Docs
description: Dowiedz się, jak zautomatyzować konfigurację szyfrowania SQL Server do użycia z Azure Key Vault. W tym temacie wyjaśniono, jak używać integracji Azure Key Vault z SQL Server maszynami wirtualnymi utworzonymi przy użyciu Menedżer zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70102149"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurowanie integracji z usługą Azure Key Vault dla programu SQL Server w usłudze Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Omówienie
Istnieje wiele funkcji szyfrowania SQL Server, takich jak [przezroczyste szyfrowanie danych (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [szyfrowanie na poziomie kolumny (cle)](https://msdn.microsoft.com/library/ms173744.aspx)i [szyfrowanie kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx). Te formy szyfrowania wymagają zarządzania kluczami kryptograficznymi używanymi do szyfrowania i ich przechowywania. Usługa Azure Key Vault (AKV) została zaprojektowana w celu poprawy bezpieczeństwa i zarządzania tymi kluczami w bezpiecznej i wysoce dostępnej lokalizacji. [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) umożliwia SQL Server Korzystanie z tych kluczy z Azure Key Vault.

W przypadku uruchamiania SQL Server z maszynami lokalnymi istnieją [kroki, które można wykonać, aby uzyskać dostęp do Azure Key Vault z lokalnej maszyny SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Jednak w przypadku SQL Server na maszynach wirtualnych platformy Azure można zaoszczędzić czas, korzystając z funkcji *integracji Azure Key Vault* .

Po włączeniu tej funkcji program automatycznie zainstaluje SQL Server Connector, skonfiguruje dostawcę EKM w celu uzyskania dostępu Azure Key Vault i utworzy poświadczenie, aby umożliwić dostęp do magazynu. Jeśli zostały przedstawione kroki opisane wcześniej w dokumentacji lokalnej, można zobaczyć, że ta funkcja automatyzuje kroki 2 i 3. Jedyną czynnością, którą należy wykonać ręcznie, jest utworzenie magazynu kluczy i kluczy. Z tego miejsca cała konfiguracja maszyny wirtualnej SQL jest zautomatyzowana. Po ukończeniu tej funkcji można wykonać instrukcje języka T-SQL, aby rozpocząć szyfrowanie baz danych lub kopii zapasowych w zwykły sposób.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM Provider wersja 1.0.4.0 jest zainstalowana na maszynie wirtualnej SQL Server za pomocą [rozszerzenia SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Uaktualnienie rozszerzenia IaaS SQL nie spowoduje zaktualizowania wersji dostawcy. Rozważ ręczne uaktualnienie wersji dostawcy EKM, jeśli jest to konieczne (na przykład podczas migracji do wystąpienia zarządzanego SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Włączanie i Konfigurowanie integracji AKV
Integrację AKV można włączyć podczas aprowizacji lub skonfigurować dla istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
W przypadku aprowizacji nowej SQL Serverej maszyny wirtualnej z Menedżer zasobów, Azure Portal zapewnia sposób włączenia integracji Azure Key Vault. Funkcja Azure Key Vault jest dostępna tylko w wersjach Enterprise, Developer i wersja ewaluacyjna SQL Server.

![Integracja magazynu kluczy Usług SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Aby uzyskać szczegółowy przewodnik po aprowizacji, zobacz temat Inicjowanie obsługi administracyjnej [SQL Server maszyny wirtualnej w Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących SQL Server maszyn wirtualnych Otwórz [zasób usługi SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) i wybierz pozycję **zabezpieczenia** w obszarze **Ustawienia**. Wybierz pozycję **Włącz** , aby włączyć integrację Azure Key Vault. 

![Integracja z programem SQL AKV dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Po zakończeniu wybierz przycisk **Zastosuj** w dolnej części strony **zabezpieczenia** , aby zapisać zmiany.

> [!NOTE]
> Nazwa poświadczenia utworzona w tym miejscu zostanie zamapowana na nazwę logowania SQL później. Dzięki temu logowanie SQL będzie miało dostęp do magazynu kluczy. 


> [!NOTE]
> Integrację AKV można także skonfigurować przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [szablon szybkiego startu platformy Azure dla integracji Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
