---
title: Uaktualnij maszynę wirtualną platformy Azure przy użyciu SUSE Linux Enterprise Server do SUSE 15 SP1 | Microsoft Docs
description: W tym artykule opisano ogólne kroki dotyczące uaktualniania systemu SUSE Linux Enterprise Server do wersji SUSE 15 SP1 dla maszyny wirtualnej platformy Azure w ramach programu.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359515"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Uaktualnij maszynę wirtualną platformy Azure z SLES 12 do SLES 15 SP1

Ten artykuł zawiera ogólne kroki dotyczące uaktualniania systemu SUSE Linux Enterprise Server (SLES) 12 do SLES 15 SP1 dla maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z systemu do migracji dystrybucji SuSE](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) i [Podręcznik uaktualnienia SUSE Linux Enterprise Server 15 SP1](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Obsługiwane ścieżki uaktualnienia
Bieżąca wersja SLES musi mieć wartość SLES 12 SP4 lub 12 SP5, aby można było wykonać operację SLES 15 SP1.

![Zrzut ekranu przedstawiający obsługiwaną ścieżkę uaktualnienia](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Zaplanuj działanie migracji zgodnie z zatwierdzoną oknem przestoju. Dzieje się tak dlatego, że maszyna wirtualna jest uruchamiana ponownie podczas migracji.
- Przed uruchomieniem działania migracji należy wykonać pełną kopię zapasową maszyny wirtualnej.
- Jeśli nie skonfigurowano kopii zapasowej, należy wykonać kopię zapasową migawki dysku systemu operacyjnego.
- [Sprawdź, czy maszyna wirtualna jest w wersji 1 lub 2 generacji v2](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Uaktualnianie z wersji SUSE 12 SP4 lub SP5 do wersji SUSE 15 SP1

1. Zainstaluj najnowszy pakiet dla maszyny wirtualnej:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. Po zakończeniu instalacji uruchom ponownie maszynę wirtualną.

3. Sprawdź wersję jądra i systemu operacyjnego. Upewnij się, że wersja to SUSE 12 SP4 lub SUSE 12 SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Zainstaluj program **SUSE-Migration-sle15-Activation**. Gdy pakiet **SUSE-Migration-sle15-Activation** zostanie zainstalowany, inny pakiet **SLES15 — migracja** zostanie automatycznie zainstalowana jako pakiet zależności. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. Po zakończeniu instalacji uruchom `reboot` polecenie, aby ponownie uruchomić maszynę wirtualną.

6. Przejdź do [Azure Portal](https://portal.azure.com), wybierz maszynę wirtualną, a następnie wybierz pozycję **konsola szeregowa**. Zobaczysz, że system zatrzyma się o "ponownym uruchomieniu systemu". Ten proces powinien trwać około 15-45 minut. W przypadku maszyny wirtualnej 2. generacji może być ona zablokowana na ekranie "ponowne uruchamianie systemu". W takim przypadku poczekaj na 45 minut. Jeśli nadal nie będzie postępować dalej, przejdź do strony **przeglądu** maszyny wirtualnej w Azure Portal, Zatrzymaj maszynę wirtualną, a następnie uruchom ją ponownie.

     ![Zrzut ekranu dotyczący komunikatów w konsoli szeregowej.](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. Po ponownym uruchomieniu systemu przy użyciu nowego jądra zostanie wyświetlony następujący komunikat.

     ![Zrzut ekranu dotyczący komunikatów w konsoli szeregowej po ponownym uruchomieniu systemu przy użyciu nowego jądra.](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Sprawdź wersję jądra i systemu operacyjnego, aby sprawdzić, czy system został pomyślnie uaktualniony.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>Sprawdź wersję generacji dla maszyny wirtualnej

Aby sprawdzić wersję generacji, można użyć jednej z następujących metod:

- W terminalu SLES Uruchom polecenie `dmidecode | grep -i hyper` . Jeśli jest to maszyna wirtualna z systemem 1. generacji, nie są zwracane żadne dane wyjściowe. W przypadku maszyn wirtualnych generacji v2 zostaną wyświetlone następujące dane wyjściowe:

     ![Zrzut ekranu przedstawiający dane wyjściowe dla maszyny wirtualnej generacji 2](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- W [Azure Portal](https://portal.azure.com)przejdź do **Właściwości**  maszyny wirtualnej, a następnie sprawdź pole **generacji maszyny wirtualnej** .
