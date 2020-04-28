---
title: Automatyczne kopie zapasowe Virtual Machines SQL Server (klasyczne) | Microsoft Docs
description: 'W tym artykule wyjaśniono funkcję automatycznego tworzenia kopii zapasowych SQL Server uruchamianej na platformie Azure Virtual Machines przy użyciu Menedżer zasobów. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ff230d4769a23c9007b3da29858d2105366f9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978095"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatyczne tworzenie kopii zapasowych SQL Server na platformie Azure Virtual Machines (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-backup.md)
> 
> 

Automatyczna kopia zapasowa automatycznie konfiguruje [zarządzaną kopię zapasową do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) dla wszystkich istniejących i nowych baz danych na maszynie wirtualnej platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. Dzięki temu można konfigurować regularne kopie zapasowe bazy danych korzystające z trwałego magazynu obiektów blob platformy Azure. Automatyczne tworzenie kopii zapasowej zależy od [rozszerzenia agenta SQL Server IaaS](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję Menedżer zasobów tego artykułu, zobacz [Automatyczne tworzenie kopii zapasowych dla SQL Server na platformie Azure Virtual Machines Menedżer zasobów](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z zautomatyzowanej kopii zapasowej, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja SQL Server/Edition**:

* Standard SQL Server 2014
* SQL Server 2014 Enterprise

> [!NOTE]
> Zautomatyzowana kopia zapasowa dla SQL Server 2016 jest obsługiwana w przypadku Menedżer zasobów maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [zautomatyzowane kopie zapasowe v2 dla SQL Server 2016 Virtual Machines Azure (Menedżer zasobów)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Konfiguracja bazy danych**:

* Docelowe bazy danych muszą używać modelu odzyskiwania pełnego.

**Azure PowerShell**:

* [Zainstaluj najnowsze polecenia Azure PowerShell](/powershell/azure/overview).

**SQL Server rozszerzenie IaaS**:

* [Zainstaluj rozszerzenie SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego tworzenia kopii zapasowych. W przypadku klasycznych maszyn wirtualnych należy skonfigurować te ustawienia przy użyciu programu PowerShell.

| Ustawienie | Zakres (wartość domyślna) | Opis |
| --- | --- | --- |
| **Automatyczna kopia zapasowa** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza automatyczne tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** |1-30 dni (30 dni) |Liczba dni przechowywania kopii zapasowej. |
| **Konto magazynu** |Konto usługi Azure Storage (konto magazynu utworzone dla określonej maszyny wirtualnej) |Konto usługi Azure Storage służące do przechowywania plików automatycznego tworzenia kopii zapasowych w usłudze BLOB Storage. W tej lokalizacji jest tworzony kontener służący do przechowywania wszystkich plików kopii zapasowej. Konwencja nazewnictwa plików kopii zapasowej obejmuje datę, godzinę i nazwę maszyny. |
| **Szyfrowanie** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza szyfrowanie. Po włączeniu szyfrowania certyfikaty używane do przywracania kopii zapasowej znajdują się na określonym koncie magazynu w tym samym kontenerze automaticbackup przy użyciu tej samej konwencji nazewnictwa. Jeśli hasło zostanie zmienione, zostanie wygenerowany nowy certyfikat z tym hasłem, ale stary certyfikat pozostaje przywrócony do przywrócenia poprzednich kopii zapasowych. |
| **Hasło** |Tekst hasła (brak) |Hasło dla kluczy szyfrowania. Jest to wymagane tylko wtedy, gdy szyfrowanie jest włączone. Aby można było przywrócić zaszyfrowaną kopię zapasową, należy dysponować prawidłowym hasłem i powiązanym certyfikatem użytym w czasie wykonywania kopii zapasowej. |
| **Bazy danych systemu tworzenia kopii zapasowych** | Włącz/Wyłącz (wyłączone) | Twórz pełne kopie zapasowe bazy Master, model i MSDB |
| **Konfigurowanie harmonogramu tworzenia kopii zapasowych** | Ręczne/automatyczne (zautomatyzowane) | Wybierz opcję **zautomatyzowany** , aby automatycznie przyjmować pełne kopie zapasowe i rejestrować je na podstawie przyrostu dziennika. Wybierz pozycję **ręcznie** , aby określić harmonogram pełnych i dzienników kopii zapasowych. |

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
W poniższym przykładzie programu PowerShell jest konfigurowana automatyczna kopia zapasowa dla istniejącej maszyny wirtualnej SQL Server 2014. Polecenie **New-AzureVMSqlServerAutoBackupConfig** służy do konfigurowania ustawień automatycznego tworzenia kopii zapasowych w celu przechowywania kopii zapasowych na koncie usługi Azure Storage określonym przez zmienną $storageaccount. Te kopie zapasowe będą przechowywane przez 10 dni. Polecenie **Set-AzureVMSqlServerExtension** AKTUALIZUJE określoną maszynę wirtualną platformy Azure przy użyciu tych ustawień.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Zainstalowanie i skonfigurowanie agenta SQL Server IaaS może potrwać kilka minut.

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt, aby przekazać parametr EnableEncryption wraz z hasłem (bezpieczny ciąg) dla parametru CertificatePassword. Poniższy skrypt włącza ustawienia zautomatyzowanej kopii zapasowej w poprzednim przykładzie i dodaje szyfrowanie.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Aby wyłączyć automatyczne tworzenie kopii zapasowej, Uruchom ten sam skrypt bez parametru **-enable** dla **New-AzureVMSqlServerAutoBackupConfig**. Tak jak w przypadku instalacji, wyłączenie automatycznej kopii zapasowej może potrwać kilka minut.

> [!NOTE]
> Wyłączenie i odinstalowanie agenta SQL Server IaaS nie powoduje usunięcia wcześniej skonfigurowanych ustawień zarządzanej kopii zapasowej. Przed wyłączeniem lub odinstalowaniem SQL Server agenta IaaS należy wyłączyć automatyczne tworzenie kopii zapasowej.
> 
> 

## <a name="next-steps"></a>Następne kroki
Automatyczna kopia zapasowa konfiguruje zarządzaną kopię zapasową na maszynach wirtualnych Dlatego ważne jest zapoznanie się [z dokumentacją dotyczącą zarządzanej kopii zapasowej](https://msdn.microsoft.com/library/dn449496.aspx) w celu zrozumienia zachowania i konsekwencji.

Dodatkowe wskazówki dotyczące tworzenia kopii zapasowych i przywracania dla SQL Server na maszynach wirtualnych platformy Azure można znaleźć w następującym temacie: [kopia zapasowa i przywracanie SQL Server na platformie azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [SQL Server rozszerzenia agenta IaaS](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

