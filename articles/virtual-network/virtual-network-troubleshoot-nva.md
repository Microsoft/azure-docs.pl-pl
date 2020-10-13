---
title: Rozwiązywanie problemów z urządzeniami wirtualnymi w sieci na platformie Azure | Microsoft Docs
description: Rozwiązywanie problemów z urządzeniami wirtualnymi (urządzenie WUS) na platformie Azure i weryfikowanie podstawowych wymagań platformy Azure dla konfiguracji urządzenie WUS.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 7046062b1c2e42f47d650df6d616d6fb73c8d1ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033068"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemy z sieciowym urządzeniem wirtualnym na platformie Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Problemy z łącznością maszyn wirtualnych lub sieci VPN mogą wystąpić w przypadku korzystania z zewnętrznego urządzenia sieciowego (urządzenie WUS) innej firmy w Microsoft Azure. W tym artykule przedstawiono podstawowe kroki, które ułatwiają Weryfikowanie podstawowych wymagań platformy Azure dla konfiguracji urządzenie WUS.

Pomoc techniczna dla urządzeń WUS innych firm oraz integracja z platformą Azure jest świadczona przez dostawcę urządzenie WUS.

> [!NOTE]
> Jeśli występuje problem z łącznością lub routingiem, który obejmuje urządzenie WUS, należy [skontaktować się bezpośrednio z dostawcą urządzenie WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista kontrolna dotycząca rozwiązywania problemów z dostawcą urządzenie WUS

- Aktualizacje oprogramowania dla maszyny wirtualnej urządzenie WUS
- Konfiguracja i funkcjonalność konta usługi
- Trasy zdefiniowane przez użytkownika (UDR) w podsieciach sieci wirtualnej, które kierują ruch do urządzenie WUS
- UDR w podsieciach sieci wirtualnej, które kierują ruch z urządzenie WUS
- Tabele i reguły routingu w ramach urządzenie WUS (na przykład z NIC1 do NIC2)
- Śledzenie na kartach sieciowych urządzenie WUS, aby zweryfikować odbieranie i wysyłanie ruchu sieciowego
- Korzystając ze standardowej jednostki SKU i publicznych adresów IP, musi istnieć sieciowej grupy zabezpieczeń i jawna reguła zezwalająca na kierowanie ruchu do urządzenie WUS.

## <a name="basic-troubleshooting-steps"></a>Podstawowe kroki rozwiązywania problemów

- Sprawdź konfigurację podstawową
- Sprawdź wydajność urządzenie WUS
- Zaawansowane rozwiązywanie problemów z siecią

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Sprawdź wymagania dotyczące minimalnej konfiguracji programu urządzeń WUS na platformie Azure

Każdy urządzenie WUS ma podstawowe wymagania dotyczące konfiguracji, które działają poprawnie na platformie Azure. W poniższej sekcji przedstawiono kroki, które należy wykonać, aby sprawdzić te podstawowe konfiguracje. Aby uzyskać więcej informacji, [skontaktuj się z dostawcą urządzenie WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Sprawdź, czy funkcja przekazywania adresów IP jest włączona w urządzenie WUS**

Korzystanie z witryny Azure Portal

1. Znajdź zasób urządzenie WUS w [Azure Portal](https://portal.azure.com)wybierz pozycję Sieć, a następnie wybierz interfejs sieciowy.
2. Na stronie interfejs sieciowy wybierz pozycję Konfiguracja protokołu IP.
3. Upewnij się, że włączono przekazywanie adresów IP.

Korzystanie z programu PowerShell

1. Otwórz program PowerShell, a następnie zaloguj się do swojego konta platformy Azure.
2. Uruchom następujące polecenie (Zastąp wartości w nawiasach klamrowymi informacjami):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Sprawdź Właściwość **EnableIPForwarding** .
4. Jeśli przekazywanie adresów IP nie jest włączone, uruchom następujące polecenia, aby je włączyć:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Wyszukaj sieciowej grupy zabezpieczeń w przypadku korzystania ze standardowego SKU PUBILC IP** W przypadku korzystania ze standardowej jednostki SKU i publicznych adresów IP, musi istnieć sieciowej grupy zabezpieczeń i jawna reguła zezwalająca na ruch do urządzenie WUS.

**Sprawdź, czy ruch może być kierowany do urządzenie WUS**

1. Na [Azure Portal](https://portal.azure.com)Otwórz **Network Watcher**, wybierz **Następny przeskok**.
2. Określ maszynę wirtualną skonfigurowaną do przekierowywania ruchu do urządzenie WUS i docelowy adres IP, pod którym ma być wyświetlany następny przeskok. 
3. Jeśli urządzenie WUS nie jest wymieniony w **następnym przeskoku**, sprawdź i zaktualizuj tabele tras platformy Azure.

**Sprawdź, czy ruch może dotrzeć do urządzenie WUS**

1. W [Azure Portal](https://portal.azure.com)Otwórz **Network Watcher**, a następnie wybierz pozycję **Weryfikuj przepływ IP**. 
2. Określ maszynę wirtualną i adres IP urządzenie WUS, a następnie sprawdź, czy ruch jest blokowany przez wszystkie sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń).
3. Jeśli istnieje reguła sieciowej grupy zabezpieczeń, która blokuje ruch, zlokalizuj sieciowej grupy zabezpieczeń w **skutecznych** regułach zabezpieczeń, a następnie zaktualizuj je, aby zezwolić na przekazywanie ruchu. Następnie ponownie uruchom **weryfikację przepływu IP** i Użyj **rozwiązywania problemów z połączeniami** , aby przetestować komunikację TCP z maszyny wirtualnej na wewnętrzny lub zewnętrzny adres IP.

**Sprawdź, czy urządzenie WUS i maszyny wirtualne nasłuchują oczekiwanego ruchu**

1. Połącz się z usługą urządzenie WUS przy użyciu protokołu RDP lub SSH, a następnie uruchom następujące polecenie:

    W przypadku systemu Windows:

    ```console
   netstat -an
    ```

    Dla systemu Linux:

    ```console
   netstat -an | grep -i listen
    ```
2. Jeśli nie widzisz portu TCP, który jest używany przez oprogramowanie urządzenie WUS wymienione w wynikach, musisz skonfigurować aplikację na urządzenie WUS i maszynie wirtualnej, aby nasłuchiwać i odpowiadać na ruch, który dociera do tych portów. [Skontaktuj się z dostawcą urządzenie WUS, aby uzyskać pomoc w razie potrzeby](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Sprawdź wydajność urządzenie WUS

### <a name="validate-vm-cpu"></a>Weryfikuj procesor maszyny wirtualnej

Jeśli użycie procesora CPU zostanie zbliżone do 100%, mogą wystąpić problemy, które mają wpływ na porzucanie pakietów sieciowych. Maszyna wirtualna raportuje średni procesor CPU dla określonego przedziału czasu w Azure Portal. Podczas przechodzenia przez procesor CPU Sprawdź, który proces na maszynie wirtualnej gościa powoduje duże wykorzystanie procesora CPU, i Ogranicz go, jeśli to możliwe. Może być również konieczne zmianę rozmiaru maszyny wirtualnej na większy rozmiar jednostki SKU lub, w przypadku zestawu skalowania maszyn wirtualnych, zwiększenia liczby wystąpień lub ustawienia skalowania automatycznego przy użyciu procesora. W razie potrzeby należy [skontaktować się z dostawcą urządzenie WUS w celu uzyskania pomocy](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Weryfikowanie statystyk sieci maszyn wirtualnych

Jeśli w sieci maszyny wirtualnej używane są skoki lub przedstawiono okresy wysokiego użycia, może być również konieczne zwiększenie rozmiaru jednostki SKU maszyny wirtualnej w celu uzyskania większej przepływności. Możesz również ponownie wdrożyć maszynę wirtualną, korzystając z włączonej przyspieszonej sieci. Aby sprawdzić, czy urządzenie WUS obsługuje funkcję przyspieszonej sieci, należy [skontaktować się z dostawcą urządzenie WUS w celu uzyskania pomocy](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="advanced-network-administrator-troubleshooting"></a>Zaawansowane rozwiązywanie problemów z administratorem sieci

### <a name="capture-network-trace"></a>Przechwyć śledzenie sieci
Przechwyć jednoczesne śledzenie sieci na źródłowej maszynie wirtualnej, urządzenie WUS i docelowej maszynie wirtualnej podczas uruchamiania **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** lub **Nmap**, a następnie Zatrzymaj śledzenie.

1. Aby przechwycić jednoczesne śledzenie sieci, uruchom następujące polecenie:

   **Dla systemu Windows**

   polecenie netsh Trace Rozpocznij przechwytywanie = Yes TraceFile = c:\ server_IP. etl scenariusz = NetConnection

   **W przypadku systemu Linux**

   sudo tcpdump-S0-i eth0-X-w vmtrace. Cap

2. Użyj **PsPing** lub **Nmap** ze źródłowej maszyny wirtualnej na docelowej maszynie wirtualnej (na przykład: `PsPing 10.0.0.4:80` lub `Nmap -p 80 10.0.0.4` ).
3. Otwórz ślad sieci z docelowej maszyny wirtualnej przy użyciu [Monitor sieci](https://cnet-downloads.com/network-monitor) lub tcpdump. Zastosuj filtr wyświetlania dla adresu IP źródłowej maszyny wirtualnej, z której uruchomiono **PsPing** lub **Nmap** , na przykład `IPv4.address==10.0.0.4 (Windows netmon)` lub `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analizowanie śladów

Jeśli pakiety przychodzące do śladu maszyny wirtualnej wewnętrznej bazy danych nie są widoczne, prawdopodobnie sieciowej grupy zabezpieczeń lub UDR się z nieprawidłowymi tabelami routingu urządzenie WUS.

Jeśli pakiety przychodzące są widoczne, ale brak jest odpowiedzi, być może musisz rozwiązać problem z zaporą lub aplikacją maszyny wirtualnej. W przypadku każdego z tych problemów należy [skontaktować się z dostawcą urządzenie WUS, aby uzyskać pomoc w razie potrzeby](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).