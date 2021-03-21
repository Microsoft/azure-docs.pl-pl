---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4fc14083b0912cf18f98c229adcb297b89bc8971
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95563621"
---
Utwórz połączenie pulpitu zdalnego, aby nawiązać połączenie z maszyną wirtualną wdrożoną w sieci wirtualnej. Najlepszym sposobem na zweryfikowanie, czy można połączyć się z maszyną wirtualną, jest połączenie się z nią za pomocą jej prywatnego adresu IP, a nie nazwy komputera. W ten sposób można przetestować możliwość połączenia się, a nie poprawność skonfigurowania rozpoznawania nazw. 

1. Zlokalizuj prywatny adres IP dla swojej maszyny wirtualnej. Aby znaleźć prywatny adres IP maszyny wirtualnej, wyświetl właściwości maszyny wirtualnej w witrynie Azure Portal lub użyj programu PowerShell.
2. Sprawdź, czy masz połączenie z siecią wirtualną przez połączenie sieci VPN punkt-lokacja. 
3. Aby otworzyć program Podłączanie pulpitu zdalnego, wpisz *RDP* lub *Podłączanie pulpitu zdalnego* w polu wyszukiwania na pasku zada, a następnie wybierz pozycję **Podłączanie pulpitu zdalnego**. Możesz go również otworzyć za pomocą polecenia **mstsc** w programie PowerShell. 
3. W programie **Podłączanie pulpitu zdalnego** wprowadź prywatny adres IP maszyny wirtualnej. Jeśli to konieczne, kliknij pozycję **Pokaż opcje**, aby dostosować dodatkowe ustawienia, a następnie nawiąż połączenie.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Jak rozwiązywać problemy z połączeniem RDP z maszyną wirtualną

Jeśli masz problemy z łączeniem się z maszyną wirtualną za pośrednictwem połączenia sieci VPN, istnieje kilka rzeczy, które możesz sprawdzić. 

- Sprawdź, czy połączenie sieci VPN zostało pomyślnie nawiązane.
- Sprawdź, czy łączysz się z prywatnym adresem IP maszyny wirtualnej.
- Wprowadź polecenie **ipconfig**, aby sprawdzić adres IPv4 przypisany do karty Ethernet na komputerze, z którego jest nawiązywane połączenie. Nakładająca się przestrzeń adresowa występuje, gdy adres IP znajduje się w zakresie adresów sieci wirtualnej, z którą jest nawiązywane połączenie, lub w zakresie adresów puli VPNClientAddressPool. Kiedy przestrzeń adresowa nakłada się w ten sposób, ruch sieciowy nie dociera do platformy Azure, tylko pozostaje w sieci lokalnej.
- Jeśli możesz połączyć się z maszyną wirtualną za pomocą prywatnego adresu IP, ale nie za pomocą nazwy komputera, sprawdź, czy usługa DNS została prawidłowo skonfigurowana. Aby uzyskać więcej informacji na temat tego, jak działa rozpoznawanie nazw dla maszyn wirtualnych, zobacz [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) (Rozpoznawanie nazw dla maszyn wirtualnych).
- Sprawdź, czy pakiet konfiguracji klienta sieci VPN jest generowany po określeniu adresów IP serwera DNS dla sieci wirtualnej. Jeśli zaktualizujesz adresy IP serwera DNS, wygeneruj i zainstaluj nowy pakiet konfiguracji klienta sieci VPN.

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną](../articles/virtual-machines/troubleshooting/troubleshoot-rdp-connection.md).