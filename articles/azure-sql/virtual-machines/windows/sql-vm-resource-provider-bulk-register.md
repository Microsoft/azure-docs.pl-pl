---
title: Zbiorcze rejestrowanie maszyn wirtualnych SQL na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL | Microsoft Docs
description: Rejestruj zbiorczo SQL Server maszyny wirtualne z dostawcą zasobów maszyny wirtualnej SQL, aby zwiększyć możliwości zarządzania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b83a44db98907f505c7bf0d8302470cf3031a967
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761264"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Rejestrowanie wielu maszyn wirtualnych SQL na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób samoSQL Serverowego rejestrowania maszyn wirtualnych na platformie Azure za pomocą dostawcy zasobów maszyny wirtualnej SQL przy użyciu `Register-SqlVMs` polecenia cmdlet programu PowerShell.

W tym artykule przedstawiono zbiorczo rejestrację SQL Server maszyn wirtualnych. Alternatywnie można rejestrować [wszystkie SQL Server maszyny wirtualne automatycznie](sql-vm-resource-provider-automatic-registration.md) lub [SQL Server maszyn wirtualnych](sql-vm-resource-provider-register.md). 

## <a name="overview"></a>Omówienie

`Register-SqlVMs`Polecenia cmdlet można użyć do zarejestrowania wszystkich maszyn wirtualnych na danej liście subskrypcji, grup zasobów lub listy określonych maszyn wirtualnych. Polecenie cmdlet spowoduje zarejestrowanie maszyn wirtualnych w trybie _uproszczonego_ zarządzania, a następnie wygenerowanie zarówno [raportu, jak i pliku dziennika](#output-description). 

Proces rejestracji nie wiąże się z ryzykiem, nie ma żadnego przestoju i nie uruchomi ponownie SQL Server ani maszyny wirtualnej. 

Aby uzyskać więcej informacji na temat dostawcy zasobów, zobacz [dostawca zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu dostawcy zasobów, potrzebne są następujące elementy: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/) , która została [zarejestrowana w dostawcy zasobów](sql-vm-resource-provider-register.md#register-subscription-with-rp) i zawiera niezarejestrowane SQL Server maszyn wirtualnych. 
- Poświadczenia klienta używane do rejestrowania maszyn wirtualnych istnieją w żadnej z następujących ról platformy Azure: współautor, **współautor**lub **właściciel** **maszyny wirtualnej**. 
- Najnowsza wersja polecenia [AZ PowerShell](/powershell/azure/new-azureps-module-az). 
- Najnowsza wersja [AZ. SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="get-started"></a>Wprowadzenie

Przed kontynuowaniem należy najpierw utworzyć kopię lokalną skryptu, zaimportować ją jako moduł programu PowerShell i nawiązać połączenie z platformą Azure. 

### <a name="create-the-script"></a>Utwórz skrypt

Aby utworzyć skrypt, skopiuj [pełny skrypt](#full-script) z końca tego artykułu i Zapisz go lokalnie jako `RegisterSqlVMs.psm1` . 

### <a name="import-the-script"></a>Zaimportuj skrypt

Po utworzeniu skryptu można go zaimportować jako moduł w terminalu programu PowerShell. 

Otwórz Terminal programu PowerShell w środowisku administracyjnym i przejdź do lokalizacji, w której zapisano `RegisterSqlVMs.psm1` plik. Następnie uruchom następujące polecenie cmdlet programu PowerShell w celu zaimportowania skryptu jako modułu: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby nawiązać połączenie z platformą Azure, użyj następującego polecenia cmdlet programu PowerShell:

```powershell-interactive
Connect-AzAccount
```


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Zarejestruj wszystkie maszyny wirtualne na liście subskrypcji 

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie SQL Server maszyny wirtualne na liście subskrypcji:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Przykładowe dane wyjściowe: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-all-vms-in-a-single-subscription"></a>Zarejestruj wszystkie maszyny wirtualne w jednej subskrypcji

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie SQL Server maszyny wirtualne w ramach jednej subskrypcji: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-all-vms-in-multiple-resource-groups"></a>Zarejestruj wszystkie maszyny wirtualne w wielu grupach zasobów

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie SQL Server maszyny wirtualne w wielu grupach zasobów w ramach jednej subskrypcji:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-all-vms-in-a-resource-group"></a>Rejestrowanie wszystkich maszyn wirtualnych w grupie zasobów

Użyj następującego polecenia cmdlet, aby zarejestrować wszystkie SQL Server maszyny wirtualne w pojedynczej grupie zasobów: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-specific-vms-in-a-single-resource-group"></a>Rejestrowanie określonych maszyn wirtualnych w pojedynczej grupie zasobów

Użyj następującego polecenia cmdlet, aby zarejestrować określone SQL Server maszyny wirtualne w ramach jednej grupy zasobów:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="register-a-specific-vm"></a>Zarejestruj konkretną maszynę wirtualną

Aby zarejestrować określoną SQL Server maszynę wirtualną, użyj następującego polecenia cmdlet: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Przykładowe dane wyjściowe:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Opis danych wyjściowych

Raport i plik dziennika są generowane za każdym razem, gdy `Register-SqlVMs` polecenie cmdlet jest używane. 

### <a name="report"></a>Raport

Raport jest generowany jako `.txt` plik o nazwie `RegisterSqlVMScriptReport<Timestamp>.txt` , gdzie sygnatura czasowa jest uruchamiana, gdy polecenie cmdlet zostało uruchomione. Raport zawiera następujące szczegóły:

| **Wartość wyjściowa** | **Opis** |
| :--------------  | :-------------- | 
| Liczba rejestracji subskrypcji nie powiodła się, ponieważ nie masz dostępu lub poświadczenia są nieprawidłowe | Zapewnia to liczbę i listę subskrypcji, dla których wystąpiły problemy z podanym uwierzytelnianiem. Szczegółowy błąd można znaleźć w dzienniku, wyszukując Identyfikator subskrypcji. | 
| Liczba subskrypcji, których nie można było próbować, ponieważ nie są one zarejestrowane w RP | Ta sekcja zawiera liczbę i listę subskrypcji, które nie zostały zarejestrowane dla dostawcy zasobów maszyny wirtualnej SQL. |
| Łączna liczba znalezionych maszyn wirtualnych | Liczba maszyn wirtualnych, które zostały znalezione w zakresie parametrów przesyłanych do polecenia cmdlet. | 
| Maszyny wirtualne zostały już zarejestrowane | Liczba maszyn wirtualnych, które zostały pominięte, ponieważ zostały już zarejestrowane w ramach dostawcy zasobów. |
| Liczba pomyślnie zarejestrowanych maszyn wirtualnych | Liczba maszyn wirtualnych, które zostały pomyślnie zarejestrowane po uruchomieniu polecenia cmdlet. Wyświetla listę zarejestrowanych maszyn wirtualnych w formacie `SubscriptionID, Resource Group, Virtual Machine` . | 
| Nie można zarejestrować liczby maszyn wirtualnych z powodu błędu | Liczba maszyn wirtualnych, których zarejestrowanie nie powiodło się z powodu błędu. Szczegóły błędu można znaleźć w pliku dziennika. | 
| Liczba pominiętych maszyn wirtualnych, ponieważ maszyna wirtualna lub Agent porywach na maszynie wirtualnej nie jest uruchomiony | Liczba i lista maszyn wirtualnych, których nie można zarejestrować jako maszyny wirtualnej lub agenta gościa na maszynie wirtualnej, nie zostały uruchomione. Można ponowić próbę po uruchomieniu maszyny wirtualnej lub agenta gościa. Szczegóły można znaleźć w pliku dziennika. |
| Liczba pominiętych maszyn wirtualnych, ponieważ nie są one uruchomione SQL Server w systemie Windows | Liczba maszyn wirtualnych, które zostały pominięte, ponieważ nie działają SQL Server lub nie są maszynami wirtualnymi z systemem Windows. Maszyny wirtualne są wyświetlane w formacie `SubscriptionID, Resource Group, Virtual Machine` . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

Błędy są rejestrowane w pliku dziennika o nazwie `VMsNotRegisteredDueToError<Timestamp>.log` , gdzie sygnatura czasowa jest momentu uruchomienia skryptu. Jeśli błąd znajduje się na poziomie subskrypcji, dziennik zawiera identyfikator subskrypcji rozdzielany przecinkami i komunikat o błędzie. Jeśli błąd dotyczy rejestracji maszyny wirtualnej, dziennik zawiera identyfikator subskrypcji, nazwę grupy zasobów, nazwę maszyny wirtualnej, kod błędu i komunikat rozdzielony przecinkami. 

## <a name="remarks"></a>Uwagi

Podczas rejestrowania SQL Server maszyn wirtualnych z dostawcą zasobów przy użyciu dostarczonego skryptu należy wziąć pod uwagę następujące kwestie:

- Rejestracja w ramach dostawcy zasobów wymaga agenta gościa działającego na maszynie wirtualnej SQL Server. Obrazy systemu Windows Server 2008 nie mają agenta gościa, dlatego te maszyny wirtualne będą kończyć się niepowodzeniem i muszą zostać zarejestrowane ręcznie przy użyciu [trybu zarządzania bez agenta](sql-vm-resource-provider-register.md#management-modes).
- W celu przezwyciężenia przezroczystych błędów logika ponawiania jest wbudowana. Jeśli maszyna wirtualna została pomyślnie zarejestrowana, jest to szybka operacja. Jeśli jednak rejestracja nie powiedzie się, każda maszyna wirtualna zostanie ponowiona.  W związku z tym należy zapewnić znaczny czas na ukończenie procesu rejestracji — chociaż rzeczywiste wymaganie czasu zależy od typu i liczby błędów. 

## <a name="full-script"></a>Pełny skrypt

Pełny skrypt w serwisie GitHub można znaleźć w temacie Bulk Register VM VMS [with AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Skopiuj pełny skrypt i Zapisz go jako `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](../../database/doc-changes-updates-release-notes.md)
