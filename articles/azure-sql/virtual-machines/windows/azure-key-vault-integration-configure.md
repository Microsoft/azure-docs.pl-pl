---
title: Integruj Key Vault z SQL Server na maszynach wirtualnych z systemem Windows na platformie Azure (Menedżer zasobów) | Microsoft Docs
description: Dowiedz się, jak zautomatyzować konfigurację szyfrowania SQL Server do użycia z Azure Key Vault. W tym temacie wyjaśniono, jak używać integracji Azure Key Vault z maszynami wirtualnymi SQL utworzonymi przy użyciu Menedżer zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7fab8db1fcc02e26d1b19d3889414565ff56351b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293574"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Konfigurowanie Azure Key Vault integracji SQL Server na maszynach wirtualnych platformy Azure (Menedżer zasobów)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Istnieje wiele funkcji szyfrowania SQL Server, takich jak [przezroczyste szyfrowanie danych (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [szyfrowanie na poziomie kolumny (cle)](https://msdn.microsoft.com/library/ms173744.aspx)i [szyfrowanie kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx). Te formy szyfrowania wymagają zarządzania kluczami kryptograficznymi używanymi do szyfrowania i ich przechowywania. Usługa Azure Key Vault została zaprojektowana w celu poprawy bezpieczeństwa i zarządzania tymi kluczami w bezpiecznej i wysokiej dostępności lokalizacji. [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) umożliwia SQL Server Korzystanie z tych kluczy z Azure Key Vault.

Jeśli używasz SQL Server lokalnie, istnieją kroki, które można wykonać, aby [uzyskać dostęp do Azure Key Vault z lokalnego wystąpienia SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Jednak w przypadku SQL Server na maszynach wirtualnych platformy Azure można zaoszczędzić czas, korzystając z funkcji *integracji Azure Key Vault* .

Po włączeniu tej funkcji program automatycznie zainstaluje SQL Server Connector, skonfiguruje dostawcę EKM w celu uzyskania dostępu Azure Key Vault i utworzy poświadczenie, aby umożliwić dostęp do magazynu. Jeśli zostały przedstawione kroki opisane wcześniej w dokumentacji lokalnej, można zobaczyć, że ta funkcja automatyzuje kroki 2 i 3. Jedyną czynnością, którą należy wykonać ręcznie, jest utworzenie magazynu kluczy i kluczy. W tym miejscu jest zautomatyzowana cała konfiguracja maszyny wirtualnej SQL Server. Po zakończeniu tej instalacji można wykonać instrukcje języka Transact-SQL (T-SQL), aby rozpocząć szyfrowanie baz danych lub kopii zapasowych w zwykły sposób.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Program Extensible Key Management (EKM) w wersji 1.0.4.0 jest zainstalowany na maszynie wirtualnej SQL Server za pomocą [rozszerzenia infrastruktury SQL jako usługi (IaaS)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Uaktualnienie rozszerzenia IaaS SQL nie spowoduje zaktualizowania wersji dostawcy. Rozważ ręczne uaktualnienie wersji dostawcy EKM, jeśli jest to konieczne (na przykład podczas migracji do wystąpienia zarządzanego SQL).


## <a name="enabling-and-configuring-key-vault-integration"></a>Włączanie i Konfigurowanie integracji Key Vault
Możesz włączyć integrację Key Vault podczas aprowizacji lub skonfigurować ją dla istniejących maszyn wirtualnych.

### <a name="new-vms"></a>Nowe maszyny wirtualne
W przypadku aprowizacji nowej maszyny wirtualnej SQL przy użyciu Menedżer zasobów Azure Portal zapewnia sposób włączenia integracji Azure Key Vault. Funkcja Azure Key Vault jest dostępna tylko w wersjach Enterprise, Developer i wersja ewaluacyjna SQL Server.

![Integracja magazynu kluczy Usług SQL Azure](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Aby uzyskać szczegółowy przewodnik po aprowizacji, zobacz temat [udostępnianie maszyny wirtualnej SQL w Azure Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych SQL Otwórz [zasób usługi SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) i wybierz pozycję **zabezpieczenia** w obszarze **Ustawienia**. Wybierz pozycję **Włącz** , aby włączyć integrację Azure Key Vault. 

![Integracja z programem SQL Key Vault dla istniejących maszyn wirtualnych](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Po zakończeniu wybierz przycisk **Zastosuj** u dołu strony **zabezpieczenia** , aby zapisać zmiany.

> [!NOTE]
> Nazwa poświadczenia utworzona w tym miejscu zostanie zamapowana na nazwę logowania SQL później. Dzięki temu logowanie SQL będzie miało dostęp do magazynu kluczy. 


> [!NOTE]
> Integrację Key Vault można również skonfigurować przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [szablon szybkiego startu platformy Azure dla integracji Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
