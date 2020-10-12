---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183690"
---
Jeśli występują problemy z urządzeniem, możesz utworzyć pakiet pomocy technicznej z dzienników systemu. Pomoc techniczna firmy Microsoft używa tego pakietu do rozwiązywania problemów. Wykonaj następujące kroki, aby utworzyć pakiet pomocy technicznej:

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](#connect-to-the-powershell-interface).
2. Użyj `Get-HcsNodeSupportPackage` polecenia, aby utworzyć pakiet dla pomocy technicznej. Użycie polecenia cmdlet jest następujące:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Polecenie cmdlet zbiera dzienniki z urządzenia i kopiuje je do określonej sieci lub udziału lokalnego.

    Używane parametry są następujące:

    - `-Path` -Określ sieć lub ścieżkę lokalną, do której ma zostać skopiowana pakiet obsługi. potrzeb
    - `-Credential` -Określ poświadczenia, aby uzyskać dostęp do chronionej ścieżki.
    - `-Zip` -Określ, aby wygenerować plik zip.
    - `-Include` -Określ, aby uwzględnić składniki do uwzględnienia w pakiecie pomocy technicznej. Jeśli nie zostanie określony, `Default` przyjmowana jest wartość.
    - `-IncludeArchived` -Określ, aby uwzględnić archiwizowane dzienniki w pakiecie obsługi.
    - `-IncludePeriodicStats` -Określ, aby uwzględnić okresowe dzienniki statnia w pakiecie pomocy technicznej.

    
