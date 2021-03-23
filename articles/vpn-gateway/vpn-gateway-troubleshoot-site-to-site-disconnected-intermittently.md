---
title: Rozwiązywanie problemów z siecią VPN typu lokacja-lokacja na platformie Azure sporadycznie
description: Dowiedz się, jak rozwiązać problem polegający na tym, że połączenie sieci VPN typu lokacja-lokacja jest regularnie rozłączone.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: 38846bbe717912092ccfe2b236b717770b79302f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867257"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Troubleshooting: połączenie lokacja-lokacja w sieci VPN na platformie Azure jest sporadycznie rozłączane

Może wystąpić problem polegający na tym, że nowe lub istniejące Microsoft Azure połączenie sieci VPN typu lokacja-lokacja nie jest stabilne lub rozłączane regularnie. W tym artykule opisano kroki rozwiązywania problemów, które ułatwiają zidentyfikowanie i rozwiązanie przyczyny problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="prerequisite-step"></a>Etap wymagań wstępnych

Sprawdź typ bramy sieci wirtualnej platformy Azure:

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Sprawdź stronę **Przegląd** bramy sieci wirtualnej, aby uzyskać informacje o typie.
    
    ![Omówienie bramy](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1 Sprawdź, czy lokalne urządzenie sieci VPN zostało zweryfikowane

1. Należy sprawdzić, czy używane jest [zweryfikowane urządzenie sieci VPN i wersja systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Jeśli urządzenie sieci VPN nie jest zweryfikowane, może być konieczne skontaktowanie się z producentem urządzenia, aby sprawdzić, czy wystąpił problem ze zgodnością.
2. Upewnij się, że urządzenie sieci VPN jest prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [Edytowanie przykładów konfiguracji urządzeń](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2 Sprawdź ustawienia skojarzeń zabezpieczeń (w przypadku bram sieci wirtualnej platformy Azure opartych na zasadach)

1. Upewnij się, że sieć wirtualna, podsieci i zakresy w definicji **bramy sieci lokalnej** w Microsoft Azure są takie same jak konfiguracja na lokalnym urządzeniu sieci VPN.
2. Sprawdź, czy ustawienia skojarzeń zabezpieczeń są zgodne.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3. Sprawdzanie User-Defined tras lub sieciowych grup zabezpieczeń w podsieci bramy

Trasa zdefiniowana przez użytkownika w podsieci bramy może ograniczać ruch i zezwalać na ruch. Sprawia to, że połączenie sieci VPN jest zawodne dla pewnego ruchu i dobre dla innych. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4. sprawdzenie ustawienia "jeden tunel VPN na parę podsieci" (w przypadku bram sieci wirtualnej opartych na zasadach)

Upewnij się, że lokalne urządzenie sieci VPN jest ustawione tak, aby miało **jeden tunel VPN na parę podsieci** dla bram sieci wirtualnych opartych na zasadach.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Krok 5 sprawdzanie ograniczenia skojarzenia zabezpieczeń (w przypadku bram sieci wirtualnej opartych na zasadach)

Brama sieci wirtualnej oparta na zasadach ma limit 200 par skojarzeń zabezpieczeń podsieci. Jeśli liczba podsieci sieci wirtualnej platformy Azure pomnożona przez liczbę podsieci lokalnych jest większa niż 200, widoczne są sporadyczne podsieci.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6 Sprawdź adres interfejsu zewnętrznego lokalnego urządzenia sieci VPN

Jeśli adres IP połączony z Internetem urządzenia sieci VPN zostanie uwzględniony w definicji **bramy sieci lokalnej** na platformie Azure, mogą wystąpić sporadyczne rozłączenia.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7 Sprawdź, czy lokalne urządzenie sieci VPN ma włączone doskonałe utajnienie przekazywania

Funkcja **doskonałe utajnienie przekazywania dalej** może spowodować problemy z połączeniem. Jeśli urządzenie sieci VPN ma włączone **doskonałe utajnienie przekazywania** , wyłącz tę funkcję. Następnie [zaktualizuj zasady protokołu IPSec bramy sieci wirtualnej](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie połączenia typu lokacja-lokacja z siecią wirtualną](./tutorial-site-to-site-portal.md)
- [Konfigurowanie zasad protokołu IPsec/IKE dla połączeń sieci VPN typu lokacja-lokacja](vpn-gateway-ipsecikepolicy-rm-powershell.md)
