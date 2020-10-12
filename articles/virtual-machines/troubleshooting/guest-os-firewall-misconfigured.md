---
title: Zapora systemu operacyjnego gościa maszyny wirtualnej platformy Azure jest nieprawidłowo skonfigurowana | Microsoft Docs
description: Dowiedz się, jak za pomocą konsoli szeregowej lub metody w trybie offline zdiagnozować i naprawić niepoprawnie skonfigurowaną zaporę systemu operacyjnego gościa na zdalnej maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422531"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Zapora systemu operacyjnego gościa maszyny wirtualnej platformy Azure jest nieprawidłowo skonfigurowana

W tym artykule opisano sposób naprawy nieprawidłowo skonfigurowanej zapory systemu operacyjnego gościa na maszynie wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

1.  Ekran powitalny maszyny wirtualnej pokazuje, że maszyna wirtualna jest w pełni załadowana.

2.  W zależności od sposobu skonfigurowania systemu operacyjnego gościa może być jakiś ruch sieciowy lub nie docierał do maszyny wirtualnej.

## <a name="cause"></a>Przyczyna

Nieprawidłowej konfiguracji zapory systemu gościa może blokować niektóre lub wszelkiego rodzaju ruch sieciowy do maszyny wirtualnej.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy wykonać migawkę dysku systemowego, której dotyczy dana maszyna wirtualna, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj konsoli szeregowej lub [napraw maszynę wirtualną w trybie offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) , dołączając dysk systemowy maszyny wirtualnej do maszyny wirtualnej odzyskiwania.

## <a name="online-mitigations"></a>Środki zaradcze online

Połącz się z [konsolą szeregową, a następnie otwórz wystąpienie programu PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, przejdź do sekcji "Naprawa maszyny wirtualnej offline" w następującym artykule platformy Azure:

 [An internal error occurs when you try to connect to an Azure VM through Remote Desktop (Podczas próby połączenia z maszyną wirtualną platformy Azure za pośrednictwem pulpitu zdalnego występuje błąd wewnętrzny)](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Poniższe reguły można edytować w celu zapewnienia dostępu do maszyny wirtualnej (za pomocą protokołu RDP) lub ułatwienia rozwiązywania problemów:

*   Pulpit zdalny (ruch przychodzący TCP): jest to standardowa reguła, która zapewnia podstawowy dostęp do maszyny wirtualnej przez umożliwienie protokołu RDP na platformie Azure.

*   Windows Remote Management (ruch przychodzący HTTP): Ta reguła umożliwia nawiązanie połączenia z maszyną wirtualną za pomocą programu PowerShell. na platformie Azure ten rodzaj dostępu umożliwia korzystanie z aspektów skryptów zdalnych i rozwiązywania problemów.

*   Udostępnianie plików i drukarek (ruch przychodzący SMB): Ta reguła umożliwia dostęp do udziału sieciowego jako opcję rozwiązywania problemów.

*   Udostępnianie plików i drukarek (żądanie echa — ICMPv4-in): Ta reguła umożliwia wysyłanie poleceń ping do maszyny wirtualnej.

W wystąpieniu dostępu do konsoli szeregowej można zbadać bieżący stan reguły zapory.

*   Zapytanie przy użyciu nazwy wyświetlanej jako parametru:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Zapytanie przy użyciu portu lokalnego używanego przez aplikację:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Zapytanie przy użyciu lokalnego adresu IP, z którego korzysta aplikacja:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Jeśli zobaczysz, że reguła jest wyłączona, możesz ją włączyć, uruchamiając następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   W celu rozwiązywania problemów można wyłączyć profile zapory:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    W przypadku poprawnego ustawienia zapory należy ponownie włączyć zaporę po zakończeniu rozwiązywania problemów.

    > [!Note]
    > Nie musisz ponownie uruchamiać maszyny wirtualnej, aby zastosować tę zmianę.

*   Spróbuj ponownie nawiązać połączenie z maszyną wirtualną za pomocą protokołu RDP.

### <a name="offline-mitigations"></a>Środki zaradcze w trybie offline

1.  Aby włączyć lub wyłączyć reguły zapory, zapoznaj się z tematem [Włączanie lub wyłączanie reguły zapory w systemie operacyjnym gościa maszyny wirtualnej platformy Azure](enable-disable-firewall-rule-guest-os.md).

2.  Sprawdź, czy jesteś w [scenariuszu blokowania ruchu przychodzącego w zaporze systemu operacyjnego gościa](guest-os-firewall-blocking-inbound-traffic.md).

3.  Jeśli nadal masz wątpliwości, czy Zapora blokuje dostęp, zapoznaj się z tematem [wyłączanie Zapory systemu operacyjnego gościa na maszynie wirtualnej platformy Azure](disable-guest-os-firewall-windows.md), a następnie ponownie Włącz Zaporę systemu gościa przy użyciu odpowiednich reguł.

