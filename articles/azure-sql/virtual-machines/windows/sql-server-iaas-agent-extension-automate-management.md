---
title: Automatyzowanie zadań zarządzania przy użyciu rozszerzenia agenta IaaS
description: W tym artykule opisano sposób zarządzania rozszerzeniem SQL Server agenta IaaS, które automatyzuje określone SQL Server zadania administracyjne. Obejmują one automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integrację Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 00dfcad351348ed4ca4f08289e76e85a089c5d86
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272280"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatyzowanie zadań zarządzania na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia SQL Server IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Rozszerzenie agenta IaaS w programie SQL Server (SqlIaasExtension) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten artykuł zawiera omówienie usług obsługiwanych przez rozszerzenie. Ten artykuł zawiera również instrukcje dotyczące instalacji, stanu i usuwania rozszerzenia.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Aby wyświetlić klasyczną wersję tego artykułu, zobacz [SQL Server rozszerzenia agenta IaaS dla SQL Server maszyn wirtualnych (klasyczny)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta SQL Server IaaS obsługuje następujące zadania administracyjne:

| Funkcja administrowania | Opis |
| --- | --- |
| **SQL Server automatyczne kopie zapasowe** |Automatyzuje Planowanie kopii zapasowych dla wszystkich baz danych dla wystąpienia domyślnego lub [poprawnie zainstalowane](frequently-asked-questions-faq.md#administration) nazwane wystąpienie SQL Server na maszynie wirtualnej. Aby uzyskać więcej informacji, zobacz [zautomatyzowane tworzenie kopii zapasowych SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](automated-backup-sql-2014.md). |
| **SQL Server zautomatyzowane stosowanie poprawek** |Konfiguruje okno obsługi, w którym mogą być wykonywane ważne aktualizacje systemu Windows dla maszyny wirtualnej, dzięki czemu można uniknąć aktualizacji w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](automated-patching.md). |
| **Integracja Azure Key Vault** |Umożliwia automatyczne instalowanie i Konfigurowanie Azure Key Vault na maszynie wirtualnej SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji Azure Key Vault dla SQL Server na platformie Azure Virtual Machines (Menedżer zasobów)](azure-key-vault-integration-configure.md). |

Po zainstalowaniu i uruchomieniu rozszerzenia SQL Server IaaS Agent udostępnia funkcje administracyjne:

* W panelu SQL Server maszyny wirtualnej w Azure Portal i za pomocą Azure PowerShell dla obrazów SQL Server w witrynie Azure Marketplace.
* Za pomocą Azure PowerShell ręcznej instalacji rozszerzenia. 

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania dotyczące używania SQL Server rozszerzenia agenta IaaS na maszynie wirtualnej:

**System operacyjny**:

* Windows Server 2008 z dodatkiem R2
* Windows Server 2012
* Windows Server 2012 z dodatkiem R2
* Windows Server 2016
* Windows Server 2019 

**Wersja SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Pobieranie i Konfigurowanie najnowszych poleceń Azure PowerShell](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalacja
SQL Server rozszerzenie IaaS jest instalowane po zarejestrowaniu maszyny wirtualnej SQL Server z [dostawcą zasobów maszyny wirtualnej SQL Server](sql-vm-resource-provider-register.md). W razie potrzeby można zainstalować agenta SQL Server IaaS ręcznie przy użyciu poniższego polecenia programu PowerShell: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> Zainstalowanie rozszerzenia powoduje ponowne uruchomienie usługi SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalowanie na maszynie wirtualnej z jednym wystąpieniem o nazwie SQL Server
SQL Server rozszerzenie IaaS będzie współdziałać z nazwanym wystąpieniem na SQL Server, jeśli wystąpienie domyślne zostanie odinstalowane i zostanie zainstalowane ponownie rozszerzenie IaaS.

Aby użyć nazwanego wystąpienia SQL Server, wykonaj następujące kroki:
   1. Wdróż maszynę wirtualną SQL Server z poziomu portalu Azure Marketplace. 
   1. Odinstaluj rozszerzenie IaaS z [Azure Portal](https://portal.azure.com).
   1. Odinstaluj SQL Server całkowicie w SQL Server maszynie wirtualnej.
   1. Zainstaluj SQL Server z nazwanym wystąpieniem w ramach maszyny wirtualnej SQL Server. 
   1. Zainstaluj rozszerzenie IaaS z Azure Portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Pobierz stan rozszerzenia IaaS SQL Server
Jednym ze sposobów sprawdzenia, czy rozszerzenie jest zainstalowane, jest wyświetlenie stanu agenta w Azure Portal. Wybierz pozycję **wszystkie ustawienia** w oknie maszyna wirtualna, a następnie wybierz pozycję **rozszerzenia**. Powinna zostać wyświetlona lista rozszerzeń **SqlIaasExtension** .

![Stan rozszerzenia agenta SQL Server IaaS w Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

Można również użyć polecenia cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Poprzednie polecenie potwierdza, że Agent jest zainstalowany i zawiera ogólne informacje o stanie. Informacje o stanie automatycznej kopii zapasowej i poprawek można uzyskać, korzystając z następujących poleceń:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Praw
W Azure Portal można odinstalować rozszerzenie, wybierając wielokropek w oknie **rozszerzenia** we właściwościach maszyny wirtualnej. Następnie wybierz pozycję **Usuń**.

![Odinstalowywanie rozszerzenia SQL Server Agent IaaS w Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

Można również użyć polecenia cmdlet **Remove-AzVMSqlServerExtension** programu PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Następne kroki
Zacznij korzystać z jednej z usług obsługiwanych przez rozszerzenie. Aby uzyskać więcej informacji, zobacz artykuły, do których odwołuje się sekcja [obsługiwane usługi](#supported-services) w tym artykule.

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na platformie Azure Virtual Machines, zobacz temat [co to jest SQL Server na platformie azure Virtual Machines?](sql-server-on-azure-vm-iaas-what-is-overview.md).
