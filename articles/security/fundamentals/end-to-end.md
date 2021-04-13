---
title: Kompleksowe zabezpieczenia na platformie Azure | Microsoft Docs
description: Artykuł zawiera mapę usług platformy Azure, które ułatwiają Zabezpieczanie i ochronę zasobów w chmurze oraz wykrywanie i badanie zagrożeń.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310346"
---
# <a name="end-to-end-security-in-azure"></a>Kompleksowe zabezpieczenia na platformie Azure
Jednym z najlepszych powodów używania platformy Azure dla aplikacji i usług jest skorzystanie z zalet szerokiej gamy narzędzi i możliwości zabezpieczeń. Te narzędzia i funkcje ułatwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. Microsoft Azure zapewnia poufność, integralność i dostępność danych klientów, a także umożliwia przejrzyste odpowiedzialności.

Poniższy diagram i dokumentacja przedstawiają usługi zabezpieczeń na platformie Azure. Te usługi zabezpieczeń pomagają sprostać wymaganiom związanym z bezpieczeństwem firmy oraz chronić użytkowników, urządzenia, zasoby, dane i aplikacje w chmurze.

## <a name="microsoft-security-services-map"></a>Mapa usług zabezpieczeń firmy Microsoft

Mapa usług zabezpieczeń organizuje usługi według chronionych przez nich zasobów (kolumny). Diagram grupuje również usługi do następujących kategorii (wiersz):

- Zabezpieczanie i Ochrona — usługi, które umożliwiają wdrożenie warstwowych i bardziej szczegółowych strategii ochrony między tożsamościami, hostami, sieciami i danymi. Dzięki tej kolekcji usług i możliwości zabezpieczeń można zrozumieć i ulepszyć stan zabezpieczeń w środowisku platformy Azure.
- Wykrywaj zagrożenia — usługi identyfikujące podejrzane działania i ułatwiają łagodzenie zagrożeń.
- Zbadaj i udziel odpowiedzi — usługi, które pobierają dane rejestrowania, aby można było ocenić podejrzane działanie i reagować na nie.

Diagram zawiera program Azure Security test, który jest zbiorem zaleceń dotyczących zabezpieczeń mających duże znaczenie, których można użyć w celu zabezpieczenia usług używanych na platformie Azure.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagram przedstawiający kompleksowe usługi zabezpieczeń na platformie Azure." border="false":::

## <a name="security-controls-and-baselines"></a>Kontrolki zabezpieczeń i linie bazowe
Program [testów wydajności Azure](../benchmarks/introduction.md) zawiera zbiór zaleceń dotyczących zabezpieczeń o dużym wpływie, których można użyć w celu zabezpieczenia usług używanych na platformie Azure:

- Kontrola zabezpieczeń — te zalecenia są zwykle stosowane w ramach dzierżawy platformy Azure i usług platformy Azure. Każde zalecenie określa listę uczestników, którzy zwykle są zaangażowani w planowanie, zatwierdzanie lub implementację testu porównawczego.
- Linie bazowe usługi — te elementy są stosowane do poszczególnych usług platformy Azure w celu zapewnienia zaleceń dotyczących konfiguracji zabezpieczeń tej usługi.

## <a name="secure-and-protect"></a>Zabezpieczanie i ochrona

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagram przedstawiający usługi platformy Azure, które ułatwiają Zabezpieczanie zasobów w chmurze i ich ochronę." border="false":::

| Usługa | Opis |
|------|--------|
| [Azure Security Center](../../security-center/security-center-introduction.md)| Ujednolicony system zarządzania zabezpieczeniami infrastruktury, który wzmacnia stan zabezpieczeń centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze — niezależnie od tego, czy znajdują się na platformie Azure, czy też w środowisku lokalnym. |
| **&nbsp; & &nbsp; Zarządzanie dostępem do tożsamości &nbsp;** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Usługa zarządzania tożsamościami i dostępem w chmurze firmy Microsoft.  |
|  | [Dostęp warunkowy](../../active-directory/conditional-access/overview.md) jest narzędziem używanym przez usługę Azure AD do przenoszenia sygnałów tożsamości, podejmowania decyzji i wymuszania zasad organizacji. |
|  | [Usługi domenowe](../../active-directory-domain-services/overview.md) są narzędziem używanym przez usługę Azure AD do udostępniania usług domeny zarządzanej, takich jak przyłączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) oraz uwierzytelnianie Kerberos/NTLM. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) to usługa w usłudze Azure AD, która umożliwia zarządzanie, kontrolowanie i monitorowanie dostępu do ważnych zasobów w organizacji. |
|  | [Uwierzytelnianie wieloskładnikowe](../../active-directory/authentication/concept-mfa-howitworks.md) to narzędzie używane przez usługę Azure AD do zabezpieczania dostępu do danych i aplikacji przez wymaganie drugiej formy uwierzytelniania. |
| [Usługa Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Narzędzie, które umożliwia organizacjom Automatyzowanie wykrywania i korygowania zagrożeń opartych na tożsamościach, badanie ryzyka przy użyciu danych w portalu oraz eksportowanie danych wykrywania ryzyka do narzędzi innych firm w celu dalszej analizy. |
| **&nbsp; & &nbsp; Sieć infrastruktury** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Brama sieci wirtualnej służąca do wysyłania zaszyfrowanego ruchu między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznego Internetu oraz do wysyłania zaszyfrowanego ruchu między sieciami wirtualnymi platformy Azure za pośrednictwem sieci firmy Microsoft. |
| [Usługa Azure DDoS Protection w warstwie Standardowa](../../ddos-protection/ddos-protection-overview.md) | Zapewnia ulepszone funkcje ograniczenia DDoS, aby chronić przed atakami DDoS. Jest on automatycznie dostosowany do ochrony określonych zasobów platformy Azure w sieci wirtualnej. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Globalny, skalowalny punkt wejścia, który używa sieci Microsoft Global Edge do tworzenia szybkich, bezpiecznych i skalowalnych aplikacji sieci Web. |
| [Azure Firewall](../../firewall/overview.md) | Zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. |
| [Usługa Azure Key Vault](../../key-vault/general/overview.md) | Bezpieczny magazyn wpisów tajnych dla tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych. Key Vault można również użyć do tworzenia i kontrolowania kluczy szyfrowania używanych do szyfrowania danych. |
| [Key Vault zarządzanego modułu HSM (wersja zapoznawcza)](../../key-vault/managed-hsm/overview.md) | W pełni zarządzana usługa w chmurze o wysokiej dostępności, która jest zgodna ze standardami, która umożliwia Zabezpieczanie kluczy kryptograficznych dla aplikacji w chmurze przy użyciu zweryfikowanych sprzętowych modułów zabezpieczeń poziomu 3 w trybie FIPS 140-2. |
| [Link prywatny platformy Azure](../../private-link/private-link-overview.md) | Umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych przez klienta usług partnerskich/partnerów platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. |
| [Usługa Azure Application Gateway](../../application-gateway/overview.md) | Zaawansowany moduł równoważenia obciążenia sieci Web, który umożliwia zarządzanie ruchem do aplikacji sieci Web. Application Gateway może podejmować decyzje dotyczące routingu na podstawie dodatkowych atrybutów żądania HTTP, na przykład ścieżki URI lub nagłówków hosta. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | W pełni zarządzany Broker komunikatów przedsiębiorstwa z kolejkami komunikatów i tematami publikowania/subskrybowania. Service Bus jest używany do oddzielania aplikacji i usług od siebie nawzajem. |
| [Web Application Firewall](../../web-application-firewall/overview.md) | Zapewnia centralną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach. WAF można wdrożyć za pomocą usługi Azure Application Gateway i platformy Azure. |
| **Aplikacja & danych** |  |
| [Azure Backup](../../backup/backup-overview.md) | Oferuje proste, bezpieczne i ekonomiczne rozwiązania umożliwiające tworzenie kopii zapasowych danych i odzyskiwanie ich z chmury Microsoft Azureej. |
| [szyfrowanie usługi Storage platformy Azure](../../storage/common/storage-service-encryption.md) | Program automatycznie szyfruje dane przed ich zapisaniem i automatycznie odszyfrowuje dane podczas ich pobierania. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | Rozwiązanie oparte na chmurze, które umożliwia organizacjom odnajdywanie, klasyfikowanie i ochrona dokumentów i wiadomości e-mail przez stosowanie etykiet do zawartości. |
| [API Management](../../api-management/api-management-key-concepts.md) | Sposób tworzenia spójnych i nowoczesnych bram interfejsu API dla istniejących usług zaplecza. |
| [Poufne przetwarzanie na platformie Azure](../../confidential-computing/overview.md) | Umożliwia izolowanie poufnych danych podczas przetwarzania w chmurze. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | Twoje projekty programistyczne korzystają z wielu warstw technologii zabezpieczeń i zarządzania, praktyk operacyjnych i zasad zgodności, które są przechowywane w usłudze Azure DevOps. |
| **Dostęp klienta** |  |
| [Tożsamości zewnętrzne usługi Azure AD](../../active-directory/external-identities/compare-with-b2c.md) | Tożsamości zewnętrzne w usłudze Azure AD umożliwiają osobom spoza organizacji dostęp do aplikacji i zasobów, jednocześnie pozwalając im na logowanie się przy użyciu dowolnej tożsamości, którą preferują. |
|  | Możesz udostępniać aplikacje i zasoby użytkownikom zewnętrznym za pośrednictwem funkcji współpracy [B2B usługi Azure AD](../../active-directory/external-identities/what-is-b2b.md) . |
|  | [Azure AD B2C](../../active-directory-b2c/overview.md) umożliwia obsługę milionów użytkowników i miliardów uwierzytelnień dziennie, monitorowanie i automatyczne obsługiwanie zagrożeń, takich jak odmowa usługi, rozpylanie hasła lub ataki w trybie bezrzeczy. |

## <a name="detect-threats"></a>Wykrywania zagrożeń

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagram przedstawiający usługi platformy Azure, które wykrywają zagrożenia." border="false":::

| Usługa | Opis |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Oferuje zaawansowane, inteligentne i zabezpieczające zasoby platformy Azure i hybrydowe oraz obciążenia. Pulpit nawigacyjny usługi Azure Defender w Security Center zapewnia widoczność i kontrolę funkcji ochrony obciążeń w chmurze dla danego środowiska. |
| [Azure Sentinel](../../sentinel/overview.md) | Skalowalne i natywne rozwiązanie do zarządzania zdarzeniami zabezpieczeń (SIEM) i usługa automatycznej reakcji aranżacji (o). Wskaźnik kontrolny oferuje inteligentne analizy zabezpieczeń i analizy zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia. |
| **&nbsp; & &nbsp; Zarządzanie dostępem do tożsamości &nbsp;** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | Ujednolicony i bezproblemowy pakiet obrony przed przedsiębiorstwami, który natywnie koordynuje wykrywanie, zapobieganie, badanie i reagowanie na punkty końcowe, tożsamości, wiadomości e-mail i aplikacje w celu zapewnienia zintegrowanej ochrony przed zaawansowanymi atakami. |
|  | [Microsoft Defender for Endpoint](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) to platforma zabezpieczeń punktu końcowego przedsiębiorstwa zaprojektowana w celu ułatwienia sieci przedsiębiorstwa zapobiegania, wykrywaniu, badaniem i reagowaniu na zaawansowane zagrożenia. |
|  | [Microsoft Defender for Identity](https://docs.microsoft.com/defender-for-identity/what-is) to oparte na chmurze rozwiązanie zabezpieczeń korzystające z lokalnych sygnałów Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, złamanych tożsamości i złośliwych działań niejawnego oprogramowania w organizacji. |
| [Usługa Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Wysyła dwa typy zautomatyzowanych wiadomości e-mail z powiadomieniami, które ułatwiają zarządzanie ryzykiem użytkownika i wykrywaniem ryzyka: Użytkownicy narażony na ryzyko wykryją wiadomości e-mail i cotygodniowe wiadomości e-mail z podsumowaniem. |
| **Infrastruktura & sieci** |  |
| [Usługa Azure Defender dla IoT](../../defender-for-iot/overview.md) | Ujednolicone rozwiązanie zabezpieczeń do identyfikowania urządzeń, luk i zagrożeń związanych z IoT. Dzięki temu można zabezpieczyć całe środowisko IoT/OT, niezależnie od tego, czy należy chronić istniejące urządzenia IoT/OT, czy też tworzyć zabezpieczenia w nowych innowacje IoT. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Oferuje narzędzia do monitorowania, diagnozowania, wyświetlania metryk oraz włączania i wyłączania dzienników dla zasobów w sieci wirtualnej platformy Azure. Network Watcher jest przeznaczony do monitorowania i naprawiania kondycji sieci produktów IaaS, w tym maszyn wirtualnych, sieci wirtualnych, bram aplikacji i modułów równoważenia obciążenia. |
| [Rejestrowanie inspekcji Azure Policy](../../governance/policy/overview.md) | Pomaga wymuszać standardy organizacyjne i oceniać zgodność na skalę. Azure Policy korzysta z dzienników aktywności, które są automatycznie włączane w taki sposób, aby obejmowały Źródło zdarzenia, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy. |
| **Aplikacja & danych** |  |
| [Usługa Azure Defender dla rejestrów kontenerów](../../security-center/defender-for-container-registries-introduction.md) | Program zawiera skaner luk w zabezpieczeniach służący do skanowania obrazów w rejestrach Azure Container Registry opartych na Azure Resource Managerach i zapewniają lepszy wgląd w luki w zabezpieczeniach obrazów. |
| [Usługa Azure Defender dla platformy Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Zapewnia ochronę przed zagrożeniami na poziomie klastra przez monitorowanie usług zarządzanych przez program AKS za pomocą dzienników pobranych przez usługę Azure Kubernetes Service (AKS). |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | Broker zabezpieczeń dostępu w chmurze (CASB), który działa na wielu chmurach. Zapewnia rozbudowany wgląd w dane, kontrolę nad transferami danych oraz zaawansowaną analizę w celu identyfikowania i zwalczania cyberzagrożeń dotyczących wszystkich Twoich usług w chmurze. |

## <a name="investigate-and-respond"></a>Badanie i reagowanie

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagram przedstawiający usługi platformy Azure, które ułatwiają badanie zagrożeń i reagowanie na nie." border="false":::

| Usługa | Opis |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Zaawansowane narzędzia do wyszukiwania i zapytań, które umożliwiają wyszukiwanie zagrożeń związanych z bezpieczeństwem w ramach źródeł danych w organizacji. |
| [&nbsp; &nbsp; Dzienniki &nbsp; i &nbsp; metryki usługi Azure monitor](../../azure-monitor/overview.md) | Oferuje kompleksowe rozwiązanie do zbierania i analizowania danych telemetrycznych w środowiskach chmurowych i lokalnych. Azure Monitor [zbiera i agreguje dane](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) z różnych źródeł do wspólnej platformy danych, gdzie można jej używać do analizowania, wizualizacji i generowania alertów. |
| **&nbsp; & &nbsp; Zarządzanie dostępem do tożsamości &nbsp;** |  |
| [&nbsp; &nbsp; Raporty &nbsp; i &nbsp; monitorowanie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Raporty usługi Azure AD](../../active-directory/reports-monitoring/overview-reports.md) zapewniają Kompleksowy wgląd w działanie w Twoim środowisku. |
|  | [Usługa Azure AD monitoring](../../active-directory/reports-monitoring/overview-monitoring.md) umożliwia kierowanie dzienników aktywności usługi Azure AD do różnych punktów końcowych.|
| [Historia inspekcji PIM usługi Azure AD](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Pokazuje wszystkie przypisania ról i aktywacje w ciągu ostatnich 30 dni dla wszystkich ról uprzywilejowanych. |
| **Aplikacja & danych** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Oferuje narzędzia do dokładniejszego poznania tego, co dzieje się w środowisku chmury. |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z Twoją [wspólną odpowiedzialnością w chmurze](shared-responsibility.md).

- Zapoznaj się z [opcjami izolacji w chmurze platformy Azure w](isolation-choices.md) odniesieniu do złośliwych i niezłośliwych użytkowników.