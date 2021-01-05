---
title: Nie można uzyskać pulpitu zdalnego na platformie Azure Virtual Machines ze względu na statyczny adres IP | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z protokołem RDP, który jest spowodowany przez statyczny adres IP w Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: bf19a6f77a87f2424f9e7b889e48119d57d1e2e5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820986"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Nie można uzyskać pulpitu zdalnego na platformie Azure Virtual Machines ze względu na statyczny adres IP

W tym artykule opisano problem polegający na tym, że nie można uzyskać zdalnego pulpitu do usługi Azure Windows Virtual Machines (maszyny wirtualne) po skonfigurowaniu statycznego adresu IP na maszynie wirtualnej.


## <a name="symptoms"></a>Objawy

Po nawiązaniu połączenia RDP z maszyną wirtualną na platformie Azure zostanie wyświetlony następujący komunikat o błędzie:

**Pulpit zdalny nie może nawiązać połączenia z komputerem zdalnym z jednego z następujących powodów:**

1. **Dostęp zdalny do serwera nie jest włączony**

2. **Komputer zdalny jest wyłączony**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, że komputer zdalny jest włączony i podłączony do sieci, a dostęp zdalny jest włączony.**

Po sprawdzeniu zrzutu ekranu w [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) w Azure Portal zobaczysz, że maszyna wirtualna jest uruchamiana normalnie i czeka na poświadczenia na ekranie logowania.

## <a name="cause"></a>Przyczyna

Maszyna wirtualna ma statyczny adres IP, który jest zdefiniowany w interfejsie sieciowym w systemie Windows. Ten adres IP różni się od adresu zdefiniowanego w Azure Portal.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy wykonać migawkę dysku systemu operacyjnego, którego dotyczy dana maszyna wirtualna, jako kopii zapasowej. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć kontrolki serial do włączenia protokołu DHCP lub [zresetowania interfejsu sieciowego](reset-network-interface.md) dla maszyny wirtualnej.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowej

1. Połącz się z [konsolą szeregową i Otwórz wystąpienie programu Cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, zobacz [Resetowanie interfejsu sieciowego](reset-network-interface.md).
2. Sprawdź, czy usługa DHCP jest wyłączona w interfejsie sieciowym:

    ```console
    netsh interface ip show config
    ```

3. Jeśli protokół DHCP jest wyłączony, Przywróć konfigurację interfejsu sieciowego, aby korzystać z protokołu DHCP:

    ```console
    netsh interface ip set address name="<NIC Name>" source=dhcp
    ```

    Na przykład, jeśli nazwa interfejsu współpracującego "Ethernet 2", uruchom następujące polecenie:

    ```console
    netsh interface ip set address name="Ethernet 2" source=dhcp
    ```

4. Wykonaj ponownie zapytanie dotyczące konfiguracji protokołu IP, aby upewnić się, że interfejs sieciowy jest teraz prawidłowo skonfigurowany. Nowy adres IP powinien być zgodny z tym, który jest dostarczany przez platformę Azure.

    ```console
    netsh interface ip show config
    ```

    Nie musisz ponownie uruchamiać maszyny wirtualnej w tym momencie. Maszyna wirtualna będzie ponownie dostępna.

Po tym, jeśli chcesz skonfigurować statyczny adres IP dla maszyny wirtualnej, zobacz [Konfigurowanie statycznych adresów IP dla maszyny wirtualnej](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).
