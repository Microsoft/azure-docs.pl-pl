---
title: 'Rozwiązywanie problemów z połączeniem sieci VPN typu lokacja-lokacja platformy Azure, które nie może nawiązać połączenia z titleSuffix: Azure VPN Gateway'
description: Dowiedz się, jak rozwiązywać problemy z połączeniem sieci VPN typu lokacja-lokacja, które nagle przestanie działać i nie można go ponownie połączyć.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: cebb05b35379573fc9797e89dee3c0c2bf3de6e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867291"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Rozwiązywanie problemów: połączenie sieci VPN typu lokacja-lokacja platformy Azure nie może nawiązać połączenia i przestanie działać

Po skonfigurowaniu połączenia sieci VPN typu lokacja-lokacja między siecią lokalną a siecią wirtualną platformy Azure połączenie sieci VPN nagle przestanie działać i nie można go ponownie połączyć. W tym artykule opisano kroki rozwiązywania problemów, które ułatwiają rozwiązanie tego problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby rozwiązać ten problem, najpierw spróbuj [zresetować bramę sieci VPN platformy Azure](./reset-gateway.md) i zresetować tunel z lokalnego urządzenia sieci VPN. Jeśli problem będzie się powtarzał, wykonaj następujące kroki, aby zidentyfikować przyczynę problemu.

### <a name="prerequisite-step"></a>Etap wymagań wstępnych

Sprawdź typ bramy sieci VPN platformy Azure.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Aby uzyskać informacje o typie, zapoznaj się ze stroną z **omówieniem** bramy sieci VPN.
    
    ![Przegląd bramy](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Sprawdź, czy lokalne urządzenie sieci VPN zostało zweryfikowane

1. Należy sprawdzić, czy używane jest [zweryfikowane urządzenie sieci VPN i wersja systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Jeśli urządzenie nie jest zweryfikowanym urządzeniem sieci VPN, może być konieczne skontaktowanie się z producentem urządzenia, aby sprawdzić, czy wystąpił problem ze zgodnością.

2. Upewnij się, że urządzenie sieci VPN jest prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [Edytowanie przykładów konfiguracji urządzeń](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Weryfikowanie klucza współużytkowanego

Porównaj klucz współużytkowany dla lokalnego urządzenia sieci VPN z usługą Azure Virtual Network VPN, aby upewnić się, że klucze są zgodne. 

Aby wyświetlić klucz współużytkowany dla połączenia sieci VPN platformy Azure, użyj jednej z następujących metod:

**Witryna Azure Portal**

1. Przejdź do utworzonego połączenia bramy sieci VPN typu lokacja-lokacja.

2. W sekcji **Ustawienia** kliknij pozycję **klucz współużytkowany**.
    
    ![Klucz wspólny](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W przypadku modelu wdrażania Azure Resource Manager:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

W przypadku klasycznego modelu wdrażania:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Weryfikowanie adresów IP elementów równorzędnych sieci VPN

-   Definicja adresu IP w obiekcie **bramy sieci lokalnej** na platformie Azure powinna być zgodna z adresem IP urządzenia lokalnego.
-   Definicja adresu IP bramy platformy Azure ustawiona na urządzeniu lokalnym powinna być zgodna z adresem IP bramy platformy Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4. Sprawdź UDR i sieciowych grup zabezpieczeń w podsieci bramy

Sprawdź i Usuń zdefiniowane przez użytkownika Routing (UDR) lub sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) w podsieci bramy, a następnie przetestuj wynik. Jeśli problem zostanie rozwiązany, sprawdź ustawienia, które zastosowały UDR lub sieciowej grupy zabezpieczeń.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Sprawdź adres interfejsu zewnętrznego lokalnego urządzenia sieci VPN

Jeśli internetowy adres IP urządzenia sieci VPN zostanie uwzględniony w definicji **sieci lokalnej** na platformie Azure, mogą wystąpić sporadyczne rozłączenia.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Sprawdź, czy podsieci są zgodne dokładnie (bramy oparte na zasadach platformy Azure)

-   Sprawdź, czy przestrzenie adresów sieci wirtualnej dokładnie pasują do sieci wirtualnej platformy Azure i lokalnych definicji.
-   Należy sprawdzić, czy podsieci są zgodne dokładnie między **bramą sieci lokalnej** i lokalnymi definicjami sieci lokalnych.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Weryfikowanie sondy kondycji bramy platformy Azure

1. Otwórz sondę kondycji, przechodząc do następującego adresu URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Kliknij ostrzeżenie o certyfikacie.
3. Jeśli otrzymasz odpowiedź, Brama sieci VPN jest traktowana jako dobra kondycja. Jeśli nie otrzymasz odpowiedzi, Brama może nie być w dobrej kondycji lub sieciowej grupy zabezpieczeń w podsieci bramy powoduje problem. Następujący tekst jest odpowiedzią przykładową:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Sprawdź, czy lokalne urządzenie sieci VPN ma włączoną funkcję doskonałego utajnienia przekazywania

Funkcja doskonałe utajnienie przekazywania dalej może spowodować problemy z połączeniem. Jeśli urządzenie sieci VPN ma włączone doskonałe utajnienie przekazywania, wyłącz tę funkcję. Następnie zaktualizuj zasady protokołu IPsec bramy sieci VPN.

## <a name="next-steps"></a>Następne kroki

-   [Konfigurowanie połączenia typu lokacja-lokacja z siecią wirtualną](./tutorial-site-to-site-portal.md)
-   [Konfigurowanie zasad protokołu IPsec/IKE dla połączeń sieci VPN typu lokacja-lokacja](vpn-gateway-ipsecikepolicy-rm-powershell.md)