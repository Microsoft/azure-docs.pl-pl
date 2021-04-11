---
title: Rozszerzenie maszyny wirtualnej z systemem Linux dla Azure Backup
description: Utwórz kopię zapasową spójną na poziomie aplikacji maszyny wirtualnej z Azure Backup przy użyciu rozszerzenia migawki maszyny wirtualnej Linux.
services: backup, virtual-machines
documentationcenter: ''
author: trinadhkotturu
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: ea984fdc4abeb08f4b080e913a0c34b99c59d93c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561113"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Rozszerzenie maszyny wirtualnej z systemem Linux dla Azure Backup



Azure Backup zapewnia obsługę tworzenia kopii zapasowych obciążeń z zasobów lokalnych do chmury i wykonywania kopii zapasowych w magazynie Recovery Services. Azure Backup używa rozszerzenia migawki maszyny wirtualnej w celu utworzenia kopii zapasowej maszyny wirtualnej platformy Azure spójnej na poziomie aplikacji bez konieczności zamykania maszyny wirtualnej. Rozszerzenie systemu Linux migawki maszyny wirtualnej jest publikowane i obsługiwane przez firmę Microsoft w ramach usługi Azure Backup. Azure Backup zainstaluje rozszerzenie jako część pierwszej zaplanowanej kopii zapasowej wyzwalane po włączeniu kopii zapasowej. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia migawki maszyny wirtualnej.

Rozszerzenie VMSnapshot pojawia się w Azure Portal tylko dla niezarządzanych maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny
Aby uzyskać listę obsługiwanych systemów operacyjnych, zapoznaj się z [systemami operacyjnymi obsługiwanymi przez program Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON pokazuje schemat rozszerzenia migawki maszyny wirtualnej. Rozszerzenie wymaga identyfikatora zadania — identyfikuje zadanie tworzenia kopii zapasowej, które wyzwoliło migawkę na maszynie wirtualnej, identyfikator URI obiektu BLOB, a stan operacji migawki jest zapisywana, zaplanowana godzina rozpoczęcia migawki, rejestruje identyfikator URI obiektu BLOB i zapisuje dzienniki odpowiadające zadaniu migawki, objstr reprezentację dysków maszyn wirtualnych i metadanych.  Ponieważ te ustawienia powinny być traktowane jako dane poufne, powinny być przechowywane w konfiguracji chronionego ustawienia. Dane ustawienia chronionego rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że te ustawienia są zalecane do przekazywania z usługi Azure Backup tylko jako część zadania tworzenia kopii zapasowej.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | ciąg |
| commandStartTimeUTCTicks | 6.36458 e + 17 | ciąg |
| locale | en-us | ciąg |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee \/ vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | ciąg |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg |



## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Jednak zalecanym sposobem dodawania rozszerzenia migawki maszyny wirtualnej jest włączenie funkcji tworzenia kopii zapasowej na maszynie wirtualnej. Można to osiągnąć za pomocą szablonu Menedżer zasobów.  Przykładowy szablon Menedżer zasobów, który umożliwia wykonywanie kopii zapasowych na maszynie wirtualnej, można znaleźć w [galerii szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie kopii zapasowych na maszynie wirtualnej. Po włączeniu kopii zapasowej, pierwsze zaplanowane zadanie tworzenia kopii zapasowej zainstaluje rozszerzenie migawki maszyny wirtualnej na maszynie wirtualnej.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku rozwiązywania problemów z maszyną wirtualną platformy Azure](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
