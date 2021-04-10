---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3f689f24c80bcb49909bd28f077dc12520b4f47e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105834"
---
Jeśli masz problemy z łączeniem się z maszyną wirtualną za pośrednictwem połączenia sieci VPN, sprawdź następujące rzeczy:

- Sprawdź, czy połączenie sieci VPN zostało pomyślnie nawiązane.
- Sprawdź, czy łączysz się z prywatnym adresem IP maszyny wirtualnej.
- Jeśli możesz połączyć się z maszyną wirtualną za pomocą prywatnego adresu IP, ale nie za pomocą nazwy komputera, sprawdź, czy usługa DNS została prawidłowo skonfigurowana. Aby uzyskać więcej informacji na temat tego, jak działa rozpoznawanie nazw dla maszyn wirtualnych, zobacz [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) (Rozpoznawanie nazw dla maszyn wirtualnych).

Jeśli łączysz się przy użyciu polecenia punkt-lokacja, sprawdź jeszcze te dodatkowe rzeczy:

- Użyj narzędzia „ipconfig”, aby sprawdzić adres IPv4 przypisany do karty Ethernet na komputerze, z którego jest nawiązywane połączenie. Jeśli adres IP znajduje się w zakresie adresów sieci wirtualnej, z którą jest nawiązywane połączenie, lub w zakresie adresów puli VPNClientAddressPool, jest to określane jako nakładająca się przestrzeń adresowa. Kiedy przestrzeń adresowa nakłada się w ten sposób, ruch sieciowy nie dociera do platformy Azure, tylko pozostaje w sieci lokalnej.
- Sprawdź, czy pakiet konfiguracji klienta sieci VPN został wygenerowany po określeniu adresów IP serwera DNS dla sieci wirtualnej. Jeśli adresy IP serwera DNS zostały zaktualizowane, wygeneruj i zainstaluj nowy pakiet konfiguracji klienta sieci VPN.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z połączeniem pulpitu zdalnego, zobacz [Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).