---
title: Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z łącznością, które mogą wystąpić między maszynami wirtualnymi platformy Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 6ece3e639e0ef3516696e2a0bad7deeb833433a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219202"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure

Mogą wystąpić problemy z łącznością między maszynami wirtualnymi platformy Azure. W tym artykule opisano kroki rozwiązywania problemów, które ułatwiają rozwiązanie tego problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Objaw

Jedna maszyna wirtualna platformy Azure nie może nawiązać połączenia z inną maszyną wirtualną platformy Azure.

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdź, czy karta sieciowa jest nieprawidłowo skonfigurowana](#step-1-check-whether-nic-is-misconfigured)
2. [Sprawdź, czy ruch sieciowy jest blokowany przez sieciowej grupy zabezpieczeń czy UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Sprawdź, czy ruch sieciowy jest blokowany przez zaporę maszyny wirtualnej](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Sprawdź, czy aplikacja lub usługa maszyny wirtualnej nasłuchuje na porcie](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Sprawdź, czy problem jest spowodowany przez](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Sprawdź, czy ruch jest blokowany przez listy ACL dla klasycznej maszyny wirtualnej](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Sprawdź, czy punkt końcowy został utworzony dla klasycznej maszyny wirtualnej](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Próba nawiązania połączenia z udziałem sieciowym maszyny wirtualnej](#step-8-try-to-connect-to-a-vm-network-share)
9. [Sprawdź łączność Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Wykonaj następujące kroki, aby rozwiązać problem. Po ukończeniu każdego kroku Sprawdź, czy problem został rozwiązany. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1. sprawdzenie, czy karta sieciowa jest nieprawidłowo skonfigurowana

Wykonaj kroki opisane w temacie [jak zresetować interfejs sieciowy dla maszyny wirtualnej platformy Azure z systemem Windows](../virtual-machines/troubleshooting/reset-network-interface.md). 

Jeśli problem występuje po zmodyfikowaniu interfejsu sieciowego (NIC), wykonaj następujące kroki:

**Maszyny wirtualne z obsługą wiele kart sieciowych**

1. Dodaj kartę sieciową.
2. Usuń problemy z niewłaściwej karty sieciowej lub wyjmij nieprawidłową kartę sieciową.  Następnie ponownie Dodaj kartę sieciową.

Aby uzyskać więcej informacji, zobacz [Dodawanie interfejsów sieciowych do lub usuwanie z maszyn wirtualnych](virtual-network-network-interface-vm.md).

**Maszyna wirtualna z jedną kartą sieciową** 

- [Ponowne wdrażanie maszyny wirtualnej z systemem Windows](../virtual-machines/troubleshooting/redeploy-to-new-node-windows.md)
- [Ponowne wdrażanie maszyny wirtualnej z systemem Linux](../virtual-machines/troubleshooting/redeploy-to-new-node-linux.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2. Sprawdzanie, czy ruch sieciowy jest blokowany przez sieciowej grupy zabezpieczeń czy UDR

Użyj [Network Watcher sprawdzenia przepływu IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [rejestrowania przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-overview.md) , aby określić, czy istnieje sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) czy User-Defined Route (UDR), która zakłóca przepływ ruchu.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3. sprawdzenie, czy ruch sieciowy jest blokowany przez zaporę maszyny wirtualnej

Wyłącz zaporę, a następnie przetestuj wynik. Jeśli problem został rozwiązany, sprawdź ustawienia zapory, a następnie ponownie Włącz zaporę.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4. Sprawdzanie, czy aplikacja lub usługa maszyny wirtualnej nasłuchuje na porcie

Możesz użyć jednej z następujących metod, aby sprawdzić, czy aplikacja lub usługa maszyny wirtualnej nasłuchuje na porcie.

- Uruchom następujące polecenia, aby sprawdzić, czy serwer nasłuchuje na tym porcie.

**Maszyna wirtualna z systemem Windows**

```console
netstat –ano
```

**Maszyna wirtualna z systemem Linux**

```console
netstat -l
```

- Uruchom polecenie **Telnet** na maszynie wirtualnej, aby przetestować port. Jeśli test zakończy się niepowodzeniem, aplikacja lub usługa nie jest skonfigurowana do nasłuchiwania na tym porcie.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5. Sprawdź, czy problem jest spowodowany przez przyłączeniem do niego

W niektórych scenariuszach maszyna wirtualna jest umieszczona za rozwiązaniem równoważenia obciążenia, które ma zależność od zasobów poza platformą Azure. W tych scenariuszach, jeśli wystąpią sporadyczne problemy z połączeniem, problem może być spowodowany [wyczerpaniem portów](../load-balancer/load-balancer-outbound-connections.md). Aby rozwiązać ten problem, Utwórz adres VIP (lub ILPIP dla klasycznej) dla każdej maszyny wirtualnej, która znajduje się za modułem równoważenia obciążenia, i Zabezpiecz z sieciowej grupy zabezpieczeń lub listą ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6. Sprawdzanie, czy ruch jest blokowany przez listy ACL dla klasycznej maszyny wirtualnej

Lista kontroli dostępu (ACL) zapewnia możliwość selektywnego zezwalania na ruch lub odmawiania go dla punktu końcowego maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zarządzanie listą ACL w punkcie końcowym](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7. Sprawdzanie, czy punkt końcowy został utworzony dla klasycznej maszyny wirtualnej

Wszystkie maszyny wirtualne utworzone na platformie Azure przy użyciu klasycznego modelu wdrażania mogą automatycznie komunikować się za pośrednictwem kanału sieci prywatnej z innymi maszynami wirtualnymi w tej samej usłudze w chmurze lub sieci wirtualnej. Jednak komputery w innych sieciach wirtualnych wymagają punktów końcowych, aby skierować ruch sieciowy przychodzący do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak skonfigurować punkty końcowe](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8. próba nawiązania połączenia z udziałem sieciowym maszyny wirtualnej

Jeśli nie można nawiązać połączenia z udziałem sieciowym maszyn wirtualnych, problem może być spowodowany przez niedostępne karty sieciowe w maszynie wirtualnej. Aby usunąć niedostępne karty sieciowe, zobacz [Jak usunąć niedostępne karty sieciowe](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9. Sprawdzanie łączności Inter-Vnet

Użyj [Network Watcher sprawdzenia przepływu IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [rejestrowania przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-overview.md) , aby określić, czy istnieje sieciowej grupy zabezpieczeń lub UDR, które zakłócają przepływ ruchu. W [tym miejscu](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)możesz również sprawdzić konfigurację Inter-Vnet.

### <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.