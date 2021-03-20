---
title: 'Azure VPN Gateway: wymagania kryptograficzne'
description: Dowiedz się, jak skonfigurować bramy sieci VPN platformy Azure, aby spełniały wymagania kryptograficzne zarówno dla tuneli VPN S2S, jak i połączeń między sieciami wirtualnymi platformy Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 47d14c5ee7f6c4816bf15351e9cb28a2aaa72b4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546849"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Wymagania kryptograficzne i bramy sieci VPN platformy Azure — informacje

W tym artykule omówiono, jak można skonfigurować bramy sieci VPN platformy Azure, aby spełniały wymagania kryptograficzne zarówno dla tuneli VPN S2S, jak i połączeń między sieciami wirtualnymi w ramach platformy Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Informacje o protokołach IKEv1 i IKEv2 dla połączeń sieci VPN platformy Azure

Tradycyjnie dozwolone są połączenia protokołu IKEv1 tylko dla podstawowych jednostek SKU i dozwolone są połączenia IKEv2 dla wszystkich jednostek SKU bramy sieci VPN innych niż podstawowe jednostki SKU. Podstawowe jednostki SKU zezwalają tylko na 1 połączenie i wraz z innymi ograniczeniami, takimi jak wydajność, klienci korzystający ze starszych urządzeń, które obsługują tylko protokoły IKEv1, mają ograniczoną obsługę. Aby zwiększyć możliwości klientów korzystających z protokołów IKEv1, teraz zezwalamy na połączenia protokołu IKEv1 dla wszystkich jednostek SKU bramy sieci VPN, z wyjątkiem podstawowej jednostki SKU. Aby uzyskać więcej informacji, zobacz [VPN Gateway jednostek SKU](./vpn-gateway-about-vpn-gateway-settings.md#gwsku).

![Azure VPN Gateway połączenia IKEv1 i IKEv2](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Gdy połączenia protokołu IKEv1 i IKEv2 są stosowane do tej samej bramy sieci VPN, podczas przesyłania między tymi dwoma połączeniami jest włączana funkcja włączona.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Informacje o parametrach zasad protokołu IPsec i IKE dla bram sieci VPN platformy Azure

Standard protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Jeśli nie zażądasz konkretnej kombinacji algorytmów kryptograficznych i parametrów, bramy sieci VPN platformy Azure używają zestawu domyślnych propozycji. Domyślne zestawy zasad zostały wybrane do zmaksymalizowania współdziałania z szeroką gamą urządzeń sieci VPN innych firm w konfiguracji domyślnej. W związku z tym zasady i liczba propozycji nie mogą obejmować wszystkich możliwych kombinacji dostępnych algorytmów kryptograficznych i siły klucza.

### <a name="default-policy"></a>Zasady domyślne

Domyślny zestaw zasad dla usługi Azure VPN Gateway jest wymieniony w artykule: [Informacje o urządzeniach sieci VPN i parametrach protokołu IPSec/IKE dla połączeń między](vpn-gateway-about-vpn-devices.md)lokacjami VPN Gateway.

## <a name="cryptographic-requirements"></a>Wymagania kryptograficzne

W przypadku komunikacji wymagającej określonych algorytmów kryptograficznych lub parametrów, zazwyczaj ze względu na wymagania dotyczące zgodności lub zabezpieczeń, można teraz skonfigurować bramy sieci VPN platformy Azure tak, aby korzystały z niestandardowych zasad protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i silnymi zasadami, a nie z domyślnymi zestawami zasad platformy Azure.

Na przykład zasady trybu głównego protokołu IKEv2 dla bram sieci VPN platformy Azure używają tylko Diffie-Hellman grupy 2 (1024 bitów), podczas gdy może być konieczne określenie silniejszych grup do użycia w usłudze IKE, takich jak grupa 14 (2048-bitowa), Grupa 24 (2048-bitowa Grupa MODP, bitowy) lub ECP (grupy krzywej eliptycznej) 256 lub 384 (odpowiednio grupy 19 i grupy 20). Podobne wymagania dotyczą również zasad trybu szybkiego protokołu IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Niestandardowe zasady protokołu IPsec/IKE z bramami sieci VPN platformy Azure

Bramy sieci VPN platformy Azure obsługują teraz dla połączeń, niestandardowe zasady protokołu IPsec/IKE. W przypadku połączenia typu lokacja-lokacja lub Sieć wirtualna-sieć wirtualna można wybrać konkretną kombinację algorytmów kryptograficznych dla protokołów IPsec i IKE z odpowiednią siłą klucza, jak pokazano w następującym przykładzie:

![IPSec — zasady IKE](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Można utworzyć zasady protokołu IPsec/IKE i zastosować je do nowego lub istniejącego połączenia.

### <a name="workflow"></a>Przepływ pracy

1. Utwórz sieci wirtualne, bramy sieci VPN lub bramy sieci lokalnej dla topologii łączności, zgodnie z opisem w innych dokumentach
2. Tworzenie zasad protokołu IPsec/IKE
3. Zasady można zastosować podczas tworzenia połączenia S2S lub połączeń między sieciami wirtualnymi
4. Jeśli połączenie zostało już utworzone, możesz zastosować lub zaktualizować zasady do istniejącego połączenia

## <a name="ipsecike-policy-faq"></a>Zasady protokołu IPsec/IKE — często zadawane pytania

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Następne kroki

Instrukcje krok po kroku dotyczące konfigurowania niestandardowych zasad protokołu IPsec/IKE można znaleźć w temacie [Konfigurowanie zasad protokołu IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) .

Zobacz również [łączenie wielu urządzeń sieci VPN opartych na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md) , aby dowiedzieć się więcej o opcji UsePolicyBasedTrafficSelectors.
