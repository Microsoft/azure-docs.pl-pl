---
title: Diagnostyka rozruchu dla maszyn wirtualnych na platformie Azure | Microsoft doc
description: Przegląd dwóch funkcji debugowania dla maszyn wirtualnych na platformie Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 9030adb9904095ac9b909e650ec6f11dcdf85ed3
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475526"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Jak rozwiązywać problemy z maszynami wirtualnymi na platformie Azure przy użyciu diagnostyki rozruchu

Może istnieć wiele powodów, w których maszyna wirtualna przechodzi w stan rozruchowy. Aby rozwiązać problemy z maszynami wirtualnymi utworzonymi za pomocą Menedżer zasobów model wdrażania, można użyć następujących funkcji debugowania: obsługa danych wyjściowych konsoli i zrzutu ekranu dla maszyn wirtualnych platformy Azure. 

W przypadku maszyn wirtualnych z systemem Linux można wyświetlić dane wyjściowe dziennika konsoli z portalu. W przypadku maszyn wirtualnych z systemami Windows i Linux platforma Azure umożliwia wyświetlenie zrzutu ekranu maszyny wirtualnej z funkcji hypervisor. Obie funkcje są obsługiwane w przypadku maszyn wirtualnych platformy Azure we wszystkich regionach. Należy pamiętać, że zrzuty ekranu i dane wyjściowe mogą pojawić się na koncie magazynu dopiero po 10 minutach.

Możesz wybrać opcję **Diagnostyka rozruchu** , aby wyświetlić dziennik i zrzut ekranu.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Typowe błędy rozruchu

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Nie znaleziono systemu operacyjnego](https://support.microsoft.com/help/4010142)
- [Niepowodzenie rozruchu lub błąd INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Włączanie diagnostyki na maszynie wirtualnej utworzonej przy użyciu witryny Azure Portal

Poniższa procedura dotyczy maszyny wirtualnej utworzonej przy użyciu modelu wdrażania Menedżer zasobów.

Na karcie **Zarządzanie** w sekcji **monitorowanie** upewnij się, że **Diagnostyka rozruchu** jest włączona. Z listy rozwijanej **konto magazynu diagnostyki** wybierz konto magazynu, w którym mają zostać umieszczone pliki diagnostyczne.
 
![Tworzenie maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funkcja diagnostyki rozruchu nie obsługuje kont magazynu w warstwie Premium ani nadmiarowych typów kont magazynu. Jeśli używasz konta usługi Premium Storage na potrzeby diagnostyki rozruchu, podczas uruchamiania maszyny wirtualnej może zostać wyświetlony błąd StorageAccountTypeNotSupported. 
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Wdrażanie przy użyciu szablonu Azure Resource Manager

W przypadku wdrażania z szablonu Azure Resource Manager przejdź do zasobu maszyny wirtualnej i Dołącz sekcję Profil diagnostyki. Ustaw nagłówek wersja interfejsu API na wartość "2015-06-15" lub nowszą. Najnowsza wersja to "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Profil diagnostyki umożliwia wybranie konta magazynu, na którym chcesz umieścić te dzienniki.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Aby uzyskać więcej informacji na temat wdrażania zasobów przy użyciu szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Włącz diagnostykę rozruchu na istniejącej maszynie wirtualnej 

Aby włączyć diagnostykę rozruchu na istniejącej maszynie wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie wybierz maszynę wirtualną.
2. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **Diagnostyka rozruchu**, a następnie wybierz kartę **Ustawienia** .
3. W obszarze Ustawienia **diagnostyki rozruchu** Zmień stan na **włączone**, a na liście rozwijanej **konto magazynu** wybierz konto magazynu. 
4. Zapisz zmianę.

    ![Aktualizowanie istniejącej maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Włączanie diagnostyki rozruchu przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można włączyć diagnostykę rozruchu na istniejącej maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [AZ VM Boot-Diagnostics](/cli/azure/vm/boot-diagnostics).
