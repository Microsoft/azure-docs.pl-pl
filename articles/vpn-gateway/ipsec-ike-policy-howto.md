---
title: 'Zasady protokołu IPsec/IKE dla sieci VPN S2S & połączeń typu sieć wirtualna-sieć wirtualna: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Skonfiguruj zasady protokołu IPsec/IKE dla połączeń S2S lub VNet-to-VNet z bramami sieci VPN platformy Azure przy użyciu Azure Resource Manager i Azure Portal.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: eda920640667abc6620c5c90ee7d04a44789353e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996757"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>Skonfiguruj zasady protokołu IPsec/IKE dla połączeń sieci VPN S2S lub komunikacji wirtualnej z siecią wirtualną: Azure Portal

W tym artykule omówiono procedurę konfigurowania zasad protokołu IPsec/IKE dla VPN Gateway połączeń sieci VPN typu lokacja-lokacja lub połączenia między siećmi wirtualnymi przy użyciu Azure Portal. Poniższe sekcje ułatwiają tworzenie i Konfigurowanie zasad protokołu IPsec/IKE oraz zastosowanie zasad do nowego lub istniejącego połączenia.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Informacje o parametrach zasad protokołu IPsec i IKE

Standard protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Zapoznaj się z [informacjami o wymaganiach kryptograficznych i bramami sieci VPN platformy Azure](vpn-gateway-about-compliance-crypto.md) , aby dowiedzieć się, jak to może pomóc w zapewnieniu łączności między różnymi lokalizacjami i siecią wirtualną w celu spełnienia wymagań dotyczących zgodności lub zabezpieczeń.

Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania zasad protokołu IPsec/IKE oraz stosowania ich do nowego lub istniejącego połączenia VPN Gateway.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Zasady protokołu IPsec/IKE działają tylko w następujących jednostkach SKU bramy:
  * ***VpnGw1 ~ 5 i VpnGw1AZ ~ 5AZ***
  * ***Standard*** i ***HighPerformance***
* Można określić tylko ***jedną*** kombinację zasad dla danego połączenia.
* Należy określić wszystkie algorytmy i parametry dla IKE (tryb główny) i IPsec (tryb szybki). Określenie zasad częściowych nie jest dozwolone.
* Zapoznaj się z wymaganiami dostawcy urządzeń sieci VPN, aby upewnić się, że zasady są obsługiwane na lokalnych urządzeniach sieci VPN. Połączenia S2S lub VNet-Sieć wirtualna nie mogą ustalić, czy zasady są niezgodne.

## <a name="workflow"></a><a name ="workflow"></a>Przepływ pracy

Ta sekcja zawiera opis przepływu pracy w celu utworzenia i zaktualizowania zasad protokołu IPsec/IKE w połączeniu sieci VPN S2S lub połączenia między siecią wirtualną:

1. Tworzenie sieci wirtualnej i bramy sieci VPN.
2. Utwórz bramę sieci lokalnej dla połączenia między lokacjami lub inną sieć wirtualną i bramę na potrzeby połączenia między sieciami wirtualnymi.
3. Utwórz połączenie (IPsec lub VNet2VNet).
4. Skonfiguruj/zaktualizuj/Usuń zasady protokołu IPsec/IKE dla zasobów połączenia.

Instrukcje zawarte w tym artykule ułatwiają konfigurowanie i Konfigurowanie zasad protokołu IPsec/IKE, jak pokazano na diagramie:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagram zasad protokołu IPsec/IKE" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Obsługiwane algorytmy kryptograficzne & mocnych kluczy

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algorytmy i klucze

W poniższej tabeli wymieniono obsługiwane algorytmy kryptograficzne i siły klucza konfigurowalne przez klientów:

| **Protokół IPsec/IKE**    | **Opcje**    |
| ---              | ---            |
| Szyfrowanie IKE   | AES256, AES192, AES128, DES3, DES                  |
| Integralność IKE    | SHA384, SHA256, SHA1, MD5                          |
| Grupa DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, brak |
| Szyfrowanie IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Brak    |
| Integralność IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupa PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Brak   |
| Okres istnienia skojarzeń zabezpieczeń QM   | (**Opcjonalnie**: wartości domyślne są używane, jeśli nie zostaną określone)<br>Sekundy (liczba całkowita; **min. 300**/wartość domyślna 27 000 sekund)<br>KB (liczba całkowita; **min. 1024**/wartość domyślna to 102 400 000 KB)    |
| Selektor ruchu | UsePolicyBasedTrafficSelectors * * ($True/$False; **Opcjonalna**, domyślna $false, jeśli nie zostanie określona)    |
| Limit czasu DPD      | Sekundy (liczba całkowita: min. 9/maks. 3600; domyślne 45 s) |
|  |  |

#### <a name="important-requirements"></a>Ważne wymagania

* Konfiguracja lokalnego urządzenia sieci VPN musi być zgodna z następującymi algorytmami (lub je zawierać) oraz z następującymi parametrami określonymi w zasadach protokołu IPsec/IKE platformy Azure (lub je zawierać):
  * Algorytm szyfrowania IKE (tryb główny/faza 1)
  * Algorytm integralności IKE (tryb główny/faza 1)
  * Grupa DH (tryb główny/faza 1)
  * Algorytm szyfrowania IPsec (tryb szybki/faza 2)
  * Algorytm integralności protokołu IPsec (tryb szybki/faza 2)
  * Grupa PFS (tryb szybki/faza 2) > * wybór ruchu (jeśli jest używany UsePolicyBasedTrafficSelectors)
  * Okresy istnienia skojarzenia zabezpieczeń są tylko specyfikacjami lokalnymi i nie muszą być zgodne.

* Jeśli GCMAES jest używany jako algorytm szyfrowania IPsec, należy wybrać ten sam algorytm GCMAES i długość klucza dla integralności protokołu IPsec. na przykład przy użyciu GCMAES128 dla obu.

* W [tabeli algorytmy i klucze](#table1) powyżej:
  * Protokół IKE odnosi się do trybu głównego lub fazy 1
  * Protokół IPsec odnosi się do trybu szybkiego lub fazy 2
  * Grupa DH określa grupę Diffie-Hellmen używaną w trybie głównym lub w fazie 1
  * Grupa PFS określiła grupę Diffie-Hellmen używaną w trybie szybkim lub fazie 2

* Okres istnienia skojarzenia zabezpieczeń trybu głównego usługi IKE został ustalony o 28 800 sekund na bramach sieci VPN platformy Azure.

* Jeśli ustawisz $True **UsePolicyBasedTrafficSelectors** na połączenie, skonfigurujesz bramę sieci VPN platformy Azure do łączenia się z ZAPORĄ sieci VPN opartą na zasadach lokalnie. W przypadku włączenia PolicyBasedTrafficSelectors należy upewnić się, że urządzenie sieci VPN ma pasujące selektory ruchu zdefiniowane ze wszystkimi kombinacjami sieci lokalnych (Brama sieci lokalnej) prefiksami do/z prefiksów sieci wirtualnej platformy Azure, a nie z dowolnego miejsca. Na przykład jeśli prefiksy sieci lokalnej to 10.1.0.0/16 i 10.2.0.0/16, a prefiksy sieci wirtualnej to 192.168.0.0/16 i 172.16.0.0/16, trzeba określić następujące selektory ruchu:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   Aby uzyskać więcej informacji na temat selektorów ruchu opartych na zasadach, zobacz [łączenie wielu lokalnych urządzeń sieci VPN opartych na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md).

* DPD timeout — wartość domyślna to 45 sekund na bramach sieci VPN platformy Azure. Ustawienie limitu czasu na krótsze okresy spowoduje, że Usługa IKE zostanie podświetlona w sposób agresywny, co powoduje, że połączenie zostanie rozłączone w niektórych wystąpieniach. Może to nie być pożądane, jeśli lokalizacje lokalne znajdują się poza regionem świadczenia usługi Azure, w którym znajduje się Brama sieci VPN, lub warunek łącza fizycznego może spowodować utratę pakietów. Ogólnym zaleceniem jest ustawienie limitu czasu z zakresu od **30 do 45** sekund.

### <a name="diffie-hellman-groups"></a>Grupy Diffie-Hellman

Poniższa tabela zawiera listę odpowiednich grup Diffie-Hellman obsługiwanych przez zasady niestandardowe:

| **Grupa Diffie’ego-Hellmana**  | **DHGroup**              | **PFSGroup** | **Długość klucza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP, 768-bitowy   |
| 2                         | DHGroup2                 | PFS2         | MODP, 1024-bitowy  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP, 2048-bitowy  |
| 19                        | ECP256                   | ECP256       | ECP, 256-bitowy    |
| 20                        | ECP384                   | ECP384       | ECP, 384-bitowy    |
| 24                        | DHGroup24                | PFS24        | MODP, 2048-bitowy  |

Więcej informacji można znaleźć w artykułach [RFC3526](https://tools.ietf.org/html/rfc3526) i [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>Sieci VPN S2S z zasadami protokołu IPsec/IKE

W tej sekcji omówiono procedurę tworzenia połączenia sieci VPN typu lokacja-lokacja za pomocą zasad protokołu IPsec/IKE. Poniższe kroki tworzą połączenie, jak pokazano na poniższym diagramie:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Diagram zasad protokołu IPsec/IKE" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Krok 1. Tworzenie sieci wirtualnej, bramy sieci VPN i bramy sieci lokalnej

Utwórz następujące zasoby, jak pokazano na poniższym zrzucie ekranu. Aby uzyskać instrukcje, zobacz [Tworzenie połączenia sieci VPN typu lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

* **Sieć wirtualna:**  Sieci testvnet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

* **Brama sieci VPN:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

* **Brama sieci lokalnej:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

* **Połączenie:** VNet1 do Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>Krok 2. Konfigurowanie zasad protokołu IPsec/IKE dla połączenia sieci VPN S2S

W tej sekcji Skonfiguruj zasady protokołu IPsec/IKE przy użyciu następujących algorytmów i parametrów:

* IKE: AES256, SHA384, DHGroup24, DPD timeout 45 s
* IPsec: AES256, SHA256, PFS None, okres istnienia SA 30000 sekund i 102400000KB

1. Przejdź do zasobu połączenia, **VNet1toSite6**, w Azure Portal. Wybierz pozycję **Konfiguracja** , a następnie wybierz pozycję **niestandardowe** zasady protokołu IPSec/IKE, aby wyświetlić wszystkie opcje konfiguracji. Na poniższym zrzucie ekranu przedstawiono konfigurację zgodną z listą:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

1. Jeśli używasz GCMAES dla protokołu IPsec, należy użyć tego samego algorytmu GCMAES i długości klucza dla szyfrowania i integralności protokołu IPsec. Na przykład poniższy zrzut ekranu określa GCMAES128 dla szyfrowania IPsec i integralności protokołu IPsec:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

1. Opcjonalnie można wybrać opcję **Włącz** dla opcji **Użyj selektorów ruchu opartych na zasadach** , aby umożliwić bramie sieci VPN platformy Azure Łączenie się z urządzeniami sieci VPN opartymi na zasadach, zgodnie z powyższym opisem.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

1. Po wybraniu wszystkich opcji wybierz pozycję **Zapisz** , aby zatwierdzić zmiany w zasobie połączenia. Zasady zostaną wymuszone w ciągu około minuty.

> [!IMPORTANT]
>
> * Po określeniu zasad protokołu IPsec/IKE w ramach połączenia Brama sieci VPN platformy Azure będzie wysyłać i akceptować tylko propozycje protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i silnymi kluczami w tym konkretnym połączeniu. Upewnij się, że lokalne urządzenie sieci VPN do połączenia używa lub akceptuje dokładną kombinację zasad, w przeciwnym razie tunel VPN S2S nie zostanie nawiązane.
>
> * Opcje **wyboru ruchu opartego na zasadach** i **DPD limitu czasu** można określić przy użyciu zasad **domyślnych** , bez niestandardowych zasad protokołu IPSec/IKE, jak pokazano na poniższym zrzucie ekranu.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Sieć wirtualna-sieć wirtualna z zasadami protokołu IPsec/IKE

Kroki tworzenia połączenia między sieciami wirtualnymi za pomocą zasad protokołu IPsec/IKE są podobne do połączeń sieci VPN S2S.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="Diagram zasad protokołu IPsec/IKE" border="false":::

1. Aby utworzyć połączenie między sieciami wirtualnymi, wykonaj kroki opisane w artykule [Tworzenie połączenia Sieć wirtualna-sieć](vpn-gateway-vnet-vnet-rm-ps.md) wirtualna.

2. Po wykonaniu tych kroków zobaczysz dwie połączenia między sieciami wirtualnymi, jak pokazano na poniższym zrzucie ekranu z zasobu VNet2GW:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

3. Przejdź do zasobu połączenia i przejdź do strony **konfiguracji** w portalu. Wybierz pozycję **niestandardowe** w **zasadach protokołu IPSec/IKE** , aby wyświetlić opcje zasad niestandardowych. Wybierz algorytmy kryptograficzne o odpowiednich długościach kluczy.

   Zrzut ekranu przedstawia różne zasady protokołu IPsec/IKE z następującymi algorytmami i parametrami:
   * IKE: AES128, SHA1, DHGroup14, DPD timeout 45 s
   * IPsec: GCMAES128, GCMAES128, PFS14, okres istnienia SA 14400 sekund & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

4. Wybierz pozycję **Zapisz** , aby zastosować zmiany zasad dla zasobu połączenia.

5. Zastosuj te same zasady do innego zasobu połączenia, VNet2toVNet1. W przeciwnym razie tunel VPN protokołu IPsec/IKE nie zostanie połączony ze względu na niezgodność zasad.

   > [!IMPORTANT]
   > Po określeniu zasad protokołu IPsec/IKE w ramach połączenia Brama sieci VPN platformy Azure będzie wysyłać i akceptować tylko propozycje protokołu IPsec/IKE z określonymi algorytmami kryptograficznymi i silnymi kluczami w tym konkretnym połączeniu. Upewnij się, że zasady protokołu IPsec dla obu połączeń są takie same, w przeciwnym razie połączenie między sieciami wirtualnymi nie zostanie nawiązane.

6. Po wykonaniu tych kroków połączenie zostanie nawiązane w ciągu kilku minut i będzie dostępna następująca topologia sieci:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagram zasad protokołu IPsec/IKE" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Aby usunąć niestandardowe zasady protokołu IPsec/IKE z połączenia

1. Aby usunąć zasady niestandardowe z połączenia, przejdź do zasobu połączenia i przejdź do strony **Konfiguracja** , aby wyświetlić bieżące zasady.

2. Wybierz pozycję **domyślne** w opcji **zasady protokołu IPSec/IKE** . Spowoduje to usunięcie wszystkich zasad niestandardowych określonych wcześniej w połączeniu i przywrócenie domyślnych ustawień protokołu IPsec/IKE dla tego połączenia:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Diagram zasad protokołu IPsec/IKE":::

3. Wybierz pozycję **Zapisz** , aby usunąć zasady niestandardowe i przywrócić domyślne ustawienia protokołu IPSec/IKE w połączeniu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat selektorów ruchu opartych na zasadach, zobacz [łączenie wielu lokalnych urządzeń sieci VPN opartych na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md) .
