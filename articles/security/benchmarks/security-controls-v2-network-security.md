---
title: Azure Security test — wersja 2 — zabezpieczenia sieciowe
description: Zabezpieczenia usługi Azure Security test w wersji 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 38ab16fbadee245810a4486170dd54922c0c38f9
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558916"
---
# <a name="security-control-v2-network-security"></a>Kontrola zabezpieczeń v2: zabezpieczenia sieci

Zabezpieczenia sieci obejmują kontrolki zabezpieczające i chroniące sieci platformy Azure. Obejmuje to Zabezpieczanie sieci wirtualnych, nawiązywanie połączeń prywatnych, zapobieganie i łagodzenie ataków zewnętrznych oraz Zabezpieczanie systemu DNS.

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| NS-1 | 9,2, 9,4, 14,1, 14,2, 14,3 | AC-4, CA-3, SC-7 |

Upewnij się, że wszystkie sieci wirtualne platformy Azure są zgodne z zasadami segmentacji przedsiębiorstwa, które są dostosowane do ryzyka biznesowego. Każdy system, który może ponieść wyższy poziom ryzyka dla organizacji, powinien być odizolowany w ramach własnej sieci wirtualnej i dostatecznie zabezpieczony za pomocą sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i/lub zapory platformy Azure. 

Na podstawie aplikacji i strategii segmentacji przedsiębiorstwa należy ograniczyć lub zezwolić na ruch między zasobami wewnętrznymi na podstawie reguł sieciowej grupy zabezpieczeń. W przypadku określonych dobrze zdefiniowanych aplikacji (np. aplikacji 3-warstwowej) może to być wysoce bezpieczne "odmowa domyślnie" Odmów przez wyjątek ". To może nie skalować się prawidłowo, jeśli masz wiele aplikacji i punktów końcowych, które współdziałają ze sobą. Możesz również użyć zapory platformy Azure w sytuacjach, gdy centralne zarządzanie jest wymagane dla dużej liczby segmentów przedsiębiorstwa lub szprych (w topologii gwiazdy). 

Aby zalecać konfiguracje sieciowych grup zabezpieczeń, które ograniczają liczbę portów i źródłowych adresów IP na podstawie odwołań do zewnętrznych reguł ruchu sieciowego, należy użyć Azure Security Center.

Za pomocą wskaźnikowego platformy Azure można wykrywać użycie starszych, niezabezpieczonych protokołów, takich jak SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, niepodpisane powiązania LDAP oraz słabe szyfry w protokole Kerberos.

- [Jak utworzyć sieciową grupę zabezpieczeń z regułami zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Adaptacyjne Zabezpieczanie sieci w Azure Security Center](../../security-center/security-center-adaptive-network-hardening.md)

- [Skoroszyt niezabezpieczonych protokołów na platformie Azure](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)     

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| NS-2 | Nie dotyczy | CA-3, AC-17, MA-4 |

Użyj usługi Azure ExpressRoute lub wirtualnej sieci prywatnej (VPN) platformy Azure do tworzenia prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną w środowisku z różnymi lokalizacjami. Połączenia ExpressRoute nie przechodzą przez publiczny Internet i oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. W przypadku połączeń sieci VPN typu punkt-lokacja i sieci VPN typu lokacja-lokacja można połączyć lokalne urządzenia lub sieci z siecią wirtualną przy użyciu dowolnej kombinacji tych opcji sieci VPN i usługi Azure ExpressRoute.

Aby połączyć co najmniej dwie sieci wirtualne na platformie Azure, użyj komunikacji równorzędnej sieci wirtualnej lub łącza prywatnego. Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny i jest przechowywany w sieci szkieletowej platformy Azure. 

- [Co to są modele łączności ExpressRoute](../../expressroute/expressroute-connectivity-models.md) 

- [Omówienie usługi Azure VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md)

- [Link prywatny platformy Azure](../../private-link/private-link-service-overview.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Ustanów dostęp w sieci prywatnej do usług platformy Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| NS-3 | 14,1 | AC-4, CA-3, SC-7 |

Użyj prywatnego linku platformy Azure, aby umożliwić prywatny dostęp do usług platformy Azure z sieci wirtualnych bez przekraczania Internetu. W sytuacjach, w których łącze prywatne platformy Azure nie jest jeszcze dostępne, użyj punktów końcowych usługi Azure Virtual Network.  Punkty końcowe usługi Azure Virtual Network zapewniają bezpieczny dostęp do usług za pośrednictwem zoptymalizowanej trasy za pośrednictwem sieci szkieletowej platformy Azure.  

Dostęp prywatny to dodatkowa ochrona w miarę jak funkcja uwierzytelniania i zabezpieczeń ruchu oferowana przez usługi platformy Azure. 

- [Opis linku prywatnego platformy Azure](../../private-link/private-link-overview.md)

- [Informacje o punktach końcowych usługi Virtual Network](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| NS-4 | 9,5, 12,3, 12,9 | SC-5, SC-7 |

Ochrona zasobów platformy Azure przed atakami z sieci zewnętrznych, w tym atakami rozproszonymi typu "odmowa usługi" (DDoS), atakami specyficznymi dla aplikacji oraz nieproszonym i potencjalnie złośliwym ruchem internetowym. Platforma Azure oferuje natywne możliwości:
-   Używanie zapory platformy Azure do ochrony aplikacji i usług przed potencjalnie złośliwym ruchem z Internetu i innych lokalizacji zewnętrznych. 

-   Korzystanie z funkcji zapory aplikacji sieci Web (WAF) na platformie Azure Application Gateway, na platformie Azure z przodu i na platformie Azure Content Delivery Network (CDN) w celu ochrony aplikacji, usług i interfejsów API przed atakami z warstwy aplikacji. 

-   Chroń zasoby przed atakami DDoS, włączając ochronę standardową DDoS w sieciach wirtualnych platformy Azure. 
-   Użyj Azure Security Center do wykrywania niepowodowanych problemów z konfiguracją, związanych z powyższym. 

- [Dokumentacja zapory platformy Azure](../../firewall/index.yml)

- [Jak wdrożyć usługę Azure WAF](../../web-application-firewall/overview.md)

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](../../ddos-protection/manage-ddos-protection.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| NS-5 | 12,6, 12,7 | SI-4 |

Filtrowanie oparte na analizie zagrożeń zapory platformy Azure umożliwia wysyłanie alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. Gdy jest wymagana inspekcja ładunku, można wdrożyć system (identyfikatory/adresy IP) w witrynie Azure Marketplace, korzystając z możliwości inspekcji ładunku. Alternatywnie można używać identyfikatorów/adresów IP opartych na hoście lub rozwiązania do wykrywania punktów końcowych (EDR) opartych na hoście, w połączeniu z lub zamiast identyfikatorów/adresów IP opartych na sieci.  

Uwaga: Jeśli masz przepisy prawne lub inne wymagania dotyczące używania identyfikatorów/adresów IP, upewnij się, że jest zawsze dostrojona, aby zapewnić wysoką jakość alertów w rozwiązaniu SIEM. 

- [Jak wdrożyć zaporę platformy Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Portal Azure Marketplace obejmuje możliwości identyfikatorów innych firm](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Funkcja Microsoft Defender ATP EDR](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

Uprość reguły zabezpieczeń sieci, wykorzystując Tagi usług i grupy zabezpieczeń aplikacji (grupy zabezpieczeń aplikacji). 

Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi w polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Można również użyć grup zabezpieczeń aplikacji do uproszczenia złożonej konfiguracji zabezpieczeń. Zamiast definiować zasady oparte na jawnych adresach IP w sieciowych grupach zabezpieczeń, grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

- [Zrozumienie i używanie tagów usługi](../../virtual-network/service-tags-overview.md)

- [Zrozumienie i używanie grup zabezpieczeń aplikacji](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: bezpieczna usługa nazw domen (DNS)

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| NS-7 | Nie dotyczy | SC-20, SC-21 |

Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń systemu DNS, aby wyeliminować typowe ataki, takie jak zawieszonego DNS, serwer DNS wzmianuje ataki i zatrucie DNS itd. 

Gdy Azure DNS jest używany jako autorytatywna usługa DNS, upewnij się, że strefy i rekordy DNS są chronione przed przypadkową lub złośliwą modyfikacją przy użyciu funkcji RBAC i blokad zasobów platformy Azure. 

- [Przegląd Azure DNS](../../dns/dns-overview.md)

- [Przewodnik wdrażania bezpiecznego systemu nazw domen (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [Zapobiegaj zawieszonego wpisów DNS i unikaj przejęcia domen podrzędnych](../fundamentals/subdomain-takeover.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
