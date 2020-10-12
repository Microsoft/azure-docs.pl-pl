---
title: Nie można nawiązać zdalnego połączenia z usługą Azure Virtual Machines, ponieważ karta sieciowa jest wyłączona | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z błędem protokołu RDP, ponieważ karta sieciowa jest wyłączona na maszynie wirtualnej platformy Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 0e79efc9de43fc0a3044e9ae1e3959f63bb6e69f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090270"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nie można uzyskać pulpitu zdalnego z maszyną wirtualną, ponieważ interfejs sieciowy jest wyłączony

W tym artykule wyjaśniono, jak rozwiązać problem polegający na tym, że nie można nawiązać połączenia Pulpit zdalny z platformą Azure Windows Virtual Machines (maszyny wirtualne), jeśli interfejs sieciowy jest wyłączony.


## <a name="symptoms"></a>Objawy

Nie można nawiązać połączenia RDP ani innego typu połączenia z innymi portami z maszyną wirtualną na platformie Azure, ponieważ interfejs sieciowy w maszynie wirtualnej jest wyłączony.

![Zrzut ekranu pokazujący maszynę wirtualną, w której interfejs sieciowy jest odłączony.](./media/troubleshoot-rdp-nic-disabled/disconnected.png)

![Zrzut ekranu pokazujący maszynę wirtualną, w której interfejs sieciowy jest wyłączony.](./media/troubleshoot-rdp-nic-disabled/disabled.png)


## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy wykonać migawkę dysku systemu operacyjnego, którego dotyczy dana maszyna wirtualna, jako kopii zapasowej. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby włączyć interfejs dla maszyny wirtualnej, użyj kontrolki szeregowej lub [Zresetuj interfejs sieciowy](#reset-network-interface) dla maszyny wirtualnej.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowej

1. Połącz się z [konsolą szeregową i Otwórz wystąpienie programu Cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, zobacz [Resetowanie interfejsu sieciowego](#reset-network-interface).
2. Sprawdź stan interfejsu sieciowego:

    ```console
    netsh interface show interface
    ```

    Zanotuj nazwę wyłączonego interfejsu sieciowego.

3. Włącz interfejs sieciowy:

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    Na przykład jeśli interfejsem roboczym jest nazwa "Ethernet 2", uruchom następujące polecenie:

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  Sprawdź ponownie stan interfejsu sieciowego, aby upewnić się, że interfejs sieciowy jest włączony.

    ```console
    netsh interface show interface
    ```

    Nie musisz ponownie uruchamiać maszyny wirtualnej w tym momencie. Maszyna wirtualna będzie ponownie dostępna.

5.  Nawiąż połączenie z maszyną wirtualną i sprawdź, czy problem został rozwiązany.

## <a name="reset-network-interface"></a>Zresetuj interfejs sieciowy

Aby zresetować interfejs sieciowy, należy zmienić adres IP na inny adres IP, który jest dostępny w podsieci. W tym celu należy użyć Azure Portal lub Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Resetowanie interfejsu sieciowego](reset-network-interface.md).
