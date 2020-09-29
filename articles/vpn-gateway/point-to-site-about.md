---
title: Informacje o połączeniach sieci VPN typu punkt-lokacja na platformie Azure | VPN Gateway
description: Ten artykuł ułatwia zrozumienie połączeń punkt-lokacja i pomaga określić, który typ uwierzytelniania bramy sieci VPN P2S ma być używany.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 68d2ed0d6702a42f44e502f2f7d3a91c0221ff6a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440947"
---
# <a name="about-point-to-site-vpn"></a>Informacje o sieci VPN typu punkt-lokacja

Połączenie bramy VPN Gateway typu punkt-lokacja pozwala utworzyć bezpieczne połączenie z siecią wirtualną z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne dla osób pracujących zdalnie, które chcą łączyć się z sieciami wirtualnymi platformy Azure z lokalizacji zdalnej, na przykład z domu lub sali konferencyjnej. Połączenie sieci VPN typu punkt-lokacja jest również przydatne zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Ten artykuł ma zastosowanie w modelu wdrażania usługi Resource Manager.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jakiego protokołu używa P2S?

Sieć VPN typu punkt-lokacja może korzystać z jednego z następujących protokołów:

* **OpenVPN® protokół**sieci VPN oparty na protokole SSL/TLS. Rozwiązanie sieci VPN TLS może przeniknąć zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, który jest używany przez protokół TLS. OpenVPN można użyć do nawiązania połączenia z urządzeń z systemami Android, iOS (wersja 11,0 i nowsze), Windows, Linux i Mac (OSX wersje 10,13 i nowsze).

* Secure Socket Tunneling Protocol (SSTP), własnościowy protokół sieci VPN oparty na protokole TLS. Rozwiązanie sieci VPN TLS może przeniknąć zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, który jest używany przez protokół TLS. Protokół SSTP jest obsługiwany tylko na urządzeniach z systemem Windows. Platforma Azure obsługuje wszystkie wersje systemu Windows, które mają protokół SSTP (system Windows 7 i nowsze).

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).


>[!NOTE]
>Protokoły IKEv2 i OpenVPN dla P2S są dostępne tylko dla modelu wdrażania Menedżer zasobów. Nie są one dostępne dla klasycznego modelu wdrażania.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Jak są uwierzytelniani klienci sieci VPN P2S?

Przed zaakceptowaniem przez platformę Azure P2S połączenia sieci VPN należy najpierw uwierzytelnić użytkownika. Istnieją dwa mechanizmy, które usługa Azure oferuje do uwierzytelniania użytkownika nawiązującego połączenie.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Uwierzytelnianie przy użyciu natywnego uwierzytelniania certyfikatu platformy Azure

W przypadku korzystania z natywnego uwierzytelniania certyfikatu platformy Azure certyfikat klienta, który jest obecny na urządzeniu, jest używany do uwierzytelniania użytkownika nawiązującego połączenie. Certyfikaty klienta są generowane na podstawie zaufanego certyfikatu głównego, a następnie instalowane na każdym komputerze klienckim. Można użyć certyfikatu głównego wygenerowanego przy użyciu rozwiązania Enterprise lub można wygenerować certyfikat z podpisem własnym.

Sprawdzanie poprawności certyfikatu klienta jest wykonywane przez bramę sieci VPN i występuje podczas ustanawiania połączenia sieci VPN P2S. Certyfikat główny jest wymagany do weryfikacji i musi zostać przekazany do platformy Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Uwierzytelnianie przy użyciu natywnego uwierzytelniania Azure Active Directory

Uwierzytelnianie usługi Azure AD umożliwia użytkownikom nawiązywanie połączeń z platformą Azure przy użyciu poświadczeń Azure Active Directory. Natywne uwierzytelnianie w usłudze Azure AD jest obsługiwane tylko dla protokołów OpenVPN i Windows 10 i wymaga użycia [klienta sieci VPN platformy Azure](https://go.microsoft.com/fwlink/?linkid=2117554).

Przy użyciu natywnego uwierzytelniania usługi Azure AD można korzystać z dostępu warunkowego usługi Azure AD oraz funkcji Multi-Factor Authentication (MFA) dla sieci VPN.

Na wysokim poziomie należy wykonać następujące kroki, aby skonfigurować uwierzytelnianie usługi Azure AD:

1. [Konfigurowanie dzierżawy usługi Azure AD](openvpn-azure-ad-tenant.md)

2. [Włączanie uwierzytelniania usługi Azure AD na bramie](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Pobieranie i Konfigurowanie klienta sieci VPN platformy Azure](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Uwierzytelnianie przy użyciu serwera domeny Active Directory (AD)

Uwierzytelnianie domeny usługi AD umożliwia użytkownikom nawiązywanie połączeń z platformą Azure przy użyciu poświadczeń domeny organizacji. Wymaga serwera RADIUS, który integruje się z serwerem usługi AD. Organizacje mogą również korzystać z istniejącego wdrożenia usługi RADIUS.
  
Serwer RADIUS można wdrożyć lokalnie lub w sieci wirtualnej platformy Azure. Podczas uwierzytelniania VPN Gateway Azure pełni rolę przekazywania i przesyłania dalej komunikatów uwierzytelniania między serwerem RADIUS a urządzeniem łączącym. W związku z tym ważne jest, aby brama była osiągalna do serwera RADIUS. Jeśli serwer RADIUS jest obecny w środowisku lokalnym, w celu uzyskania dostępu do lokacji lokalnej wymagane jest połączenie S2S sieci VPN z platformy Azure.  
  
Serwer RADIUS można także zintegrować z usługami certyfikatów usługi AD. Pozwala to na użycie serwera RADIUS i wdrożenia certyfikatu przedsiębiorstwa do uwierzytelniania certyfikatu P2S jako alternatywę dla uwierzytelniania certyfikatu platformy Azure. Zalety polegają na tym, że nie trzeba przekazywać certyfikatów głównych i odwołać certyfikatów na platformie Azure.

Serwer RADIUS można także zintegrować z innymi zewnętrznymi systemami tożsamości. Spowoduje to otwarcie dużej liczby opcji uwierzytelniania dla sieci VPN P2S, w tym opcji wieloskładnikowych.

![Diagram przedstawiający sieć VPN typu punkt-lokacja z lokacją lokalną.](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>Jakie są wymagania dotyczące konfiguracji klienta?

>[!NOTE]
>W przypadku klientów z systemem Windows należy mieć uprawnienia administratora na urządzeniu klienckim w celu zainicjowania połączenia sieci VPN z urządzenia klienckiego do platformy Azure.
>

Użytkownicy korzystają z natywnych klientów sieci VPN na urządzeniach z systemem Windows i Mac w celu P2S. Platforma Azure udostępnia plik zip konfiguracji klienta sieci VPN, który zawiera ustawienia wymagane przez tych natywnych klientów do łączenia się z platformą Azure.

* W przypadku urządzeń z systemem Windows konfiguracja klienta sieci VPN obejmuje pakiet Instalatora instalowany przez użytkowników na ich urządzeniach.
* Urządzenia Mac obejmują plik MOBILECONFIG instalowany przez użytkowników na ich urządzeniach.

Plik zip zawiera również wartości niektórych ważnych ustawień po stronie platformy Azure, których można użyć do utworzenia własnego profilu dla tych urządzeń. Niektóre wartości obejmują adres bramy sieci VPN, skonfigurowane typy tuneli, trasy i certyfikat główny na potrzeby weryfikacji bramy.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Które jednostki SKU bramy obsługują sieć VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Zalecenia dotyczące jednostki SKU bramy znajdują się w temacie [Informacje o ustawieniach VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Podstawowa jednostka SKU nie obsługuje uwierzytelniania za pomocą protokołu IKEv2 ani usługi RADIUS.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jakie zasady IKE/IPsec są skonfigurowane na bramach sieci VPN dla usługi P2S?


**IKEv2**

|**Odrzucono** | **Integralność** | **PRF** | **Grupa DH** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**IPsec**

|**Odrzucono** | **Integralność** | **Grupa PFS** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jakie zasady protokołu TLS są skonfigurowane na bramach sieci VPN dla usługi P2S?
**Protokół**

|**Zasady** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Jak mogę skonfigurować połączenie P2S?

Konfiguracja P2S wymaga wykonania kilku konkretnych kroków. Poniższe artykuły zawierają instrukcje dotyczące konfiguracji P2S oraz linki do konfigurowania urządzeń klienckich sieci VPN:

* [Konfigurowanie połączenia P2S — uwierzytelnianie usługi RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — natywne uwierzytelnianie certyfikatu platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurowanie oprogramowania OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Aby usunąć konfigurację połączenia usługi P2S

Aby uzyskać instrukcje, zobacz [często zadawane pytania](#removeconfig)poniżej.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Często zadawane pytania dotyczące natywnego uwierzytelniania certyfikatu platformy Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Często zadawane pytania dotyczące uwierzytelniania usługi RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie połączenia P2S — uwierzytelnianie usługi RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — natywne uwierzytelnianie certyfikatu platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
