---
title: Problemy z korzystaniem z rozszerzeń maszyn wirtualnych w systemie Python 3 z obsługą systemu Linux na platformie Azure Virtual Machines
description: Dowiedz się więcej o korzystaniu z rozszerzeń maszyny wirtualnej w systemach Linux z obsługą języka Python 3
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 944abc62f25473ea52836af7dc1fdcd1e16d9269
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120784"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problemy z korzystaniem z rozszerzeń maszyn wirtualnych w systemie Python 3 z obsługą systemu Linux na platformie Azure Virtual Machines

> [!NOTE]
> Firma Microsoft zachęca użytkowników do przyjęcia języka **Python 3. x** w swoich systemach, chyba że obciążenie wymaga obsługi języka **Python 2. x** . Przykłady tego wymagania mogą obejmować starsze skrypty administracyjne lub rozszerzenia, takie jak **Azure Disk Encryption** i **Azure monitor**.
>
> Przed zainstalowaniem języka **Python 2. x** w środowisku produkcyjnym należy rozważyć pytanie długoterminowej obsługi języka Python 2. x, szczególnie ich możliwość otrzymywania aktualizacji zabezpieczeń. Jako produkty, w tym niektóre z wymienionych rozszerzeń, Aktualizuj z obsługą języka **python 3,8** , należy przerwać korzystanie z języka Python 2. x.

Niektóre dystrybucje systemu Linux przechodzą do języka Python 3,8 i całkowicie usunęły starszy `/usr/bin/python` punkt wejścia dla środowiska Python. To przejście ma wpływ na wbudowane, zautomatyzowane wdrażanie niektórych rozszerzeń maszyn wirtualnych (VM) z następującymi warunkami:

- Rozszerzenia, które są nadal przenoszone do obsługi języka Python 3. x
- Rozszerzenia korzystające ze starszego `/usr/bin/python` punktu wejścia

Użytkownicy dystrybucji systemu Linux, którzy przechodzą do języka **Python 3. x** , muszą `/usr/bin/python` upewnić się, że starszy punkt wejścia istnieje przed podjęciem próby wdrożenia tych rozszerzeń na maszynach wirtualnych. W przeciwnym razie wdrożenie rozszerzenia może zakończyć się niepowodzeniem. 

- Do zatwierdzonych dystrybucji systemu Linux, których dotyczy ten system, należą **Ubuntu Server 20,04 LTS** i **Ubuntu Pro 20,04 LTS**.

- Objęte rozszerzenia maszyny wirtualnej obejmują **Azure Disk Encryption**, **log Analytics**, **dostęp do maszyny wirtualnej** (używany do resetowania hasła) i **diagnostyki gościa** (używane w przypadku dodatkowych liczników wydajności).

Uaktualnienia w miejscu, takie jak uaktualnienie z **Ubuntu 18,04 LTS** do **Ubuntu 20,04 LTS**, powinny zachować `/usr/bin/python` link symboliczny i pozostać bez zmian.

## <a name="resolution"></a>Rozwiązanie

Przed wdrożeniem rozszerzeń w znanych scenariuszach opisanych wcześniej w podsumowaniu należy wziąć pod uwagę następujące ogólne zalecenia:

1.  Przed wdrożeniem rozszerzenia Przywróć `/usr/bin/python` link symboliczny przy użyciu metody dostarczonej przez dostawcę dystrybucji systemu Linux.

    - Na przykład w przypadku języka **Python 2,7**należy użyć:`sudo apt update && sudo apt install python-is-python2`

2.  Jeśli już wdrożono wystąpienie, które wykazuje ten problem, użyj funkcji **Run polecenia** w **bloku VM** , aby uruchomić polecenia wymienione powyżej. Przechodzenie do języka Python 3,8 nie wpłynie na samo rozszerzenie polecenia uruchamiania.

3.  Jeśli wdrażasz nowe wystąpienie i musisz ustawić rozszerzenie w czasie aprowizacji, użyj danych użytkownika z usługi **Cloud-init** , aby zainstalować wymienione powyżej pakiety.

    Na przykład dla języka Python 2,7:

    ```
    # create cloud-init config
    cat > cloudinitConfig.json <<EOF
    #cloud-config
    package_update: true
    
    runcmd:
    - sudo apt update
    - sudo apt install python-is-python2 
    EOF
    
    # create VM
    az vm create \
        --resource-group <resourceGroupName> \
        --name <vmName> \
        --image <Ubuntu 20.04 Image URN> \
        --admin-username azadmin \
        --ssh-key-value "<sshPubKey>" \
        --custom-data ./cloudinitConfig.json
    ```

4.  Jeśli administratorzy zasad organizacji określają, że rozszerzenia nie należy wdrażać na maszynach wirtualnych, można wyłączyć obsługę rozszerzenia w czasie aprowizacji:

    - Interfejs API REST

      Aby wyłączyć i włączyć rozszerzenia, gdy można wdrożyć maszynę wirtualną z tą właściwością:

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [innymi zmianami systemu podstawowego od wersji 18,04 LTS-Python 3 domyślnie,](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) Aby uzyskać dodatkowe informacje.
