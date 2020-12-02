---
title: Kontrola zabezpieczeń Azure — Zabezpieczenia sieci
description: Zabezpieczenia sieci dotyczące kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3a232f8e8c35e265a8243ac79e465c03f6b9650e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487868"
---
# <a name="security-control-network-security"></a>Kontrola zabezpieczeń: zabezpieczenia sieci

Zalecenia dotyczące zabezpieczeń sieci koncentrują się na określaniu, które protokoły sieciowe, porty TCP/UDP i usługi połączone z siecią są dozwolone lub odrzucane dostępu do usług platformy Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1,1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Klient |

Upewnij się, że wszystkie wdrożenia podsieci Virtual Network mają grupę zabezpieczeń sieci stosowaną z kontrolami dostępu do sieci, które są specyficzne dla zaufanych portów i źródeł aplikacji. Jeśli to możliwe, użyj prywatnych punktów końcowych z prywatnym linkiem, aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, rozszerzając tożsamość VNet do usługi. Gdy prywatne punkty końcowe i link prywatny są niedostępne, użyj punktów końcowych usługi. Wymagania dotyczące konkretnych usług zapoznaj się z zaleceniami dotyczącymi zabezpieczeń tej konkretnej usługi. 

Alternatywnie, jeśli masz określony przypadek użycia, wymagania mogą być spełnione przez zaimplementowanie zapory platformy Azure.

- [Informacje o punktach końcowych usługi Virtual Network](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Opis linku prywatnego platformy Azure](../../private-link/private-link-overview.md)

- [Jak utworzyć Virtual Network](../../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md)

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1,2 | 9,3, 12,2, 12,8 | Klient |

Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych na platformie Azure. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1.3 | 9,5 | Klient |

Wdróż zaporę aplikacji sieci Web platformy Azure (WAF) przed krytycznymi aplikacjami sieci Web, aby uzyskać dodatkową inspekcję ruchu przychodzącego. Włącz ustawienie diagnostyczne dla WAF i pobierania dzienników do konta magazynu, centrum zdarzeń lub Log Analytics obszaru roboczego.

- [Jak wdrożyć usługę Azure WAF](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1.4 | 12,3 | Klient |

Włącz ochronę standardową DDoS w sieciach wirtualnych platformy Azure, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi adresami IP.

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji z włączoną analizą zagrożeń i skonfigurowaną jako "Alert i Odmów" w celu uzyskania złośliwego ruchu sieciowego.

Użyj Azure Security Center dostęp do sieci w czasie w celu skonfigurowania sieciowych grup zabezpieczeń w celu ograniczenia narażenia punktów końcowych na zatwierdzone adresy IP przez ograniczony okres.

Aby zalecać sieciowej grupy zabezpieczeń konfiguracje, które ograniczają porty i źródłowe adresy IP w oparciu o rzeczywisty ruch i analizę zagrożeń, należy użyć Azure Security Center.

- [Jak skonfigurować ochronę DDoS](../../ddos-protection/manage-ddos-protection.md)

- [Jak wdrożyć zaporę platformy Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](../../security-center/azure-defender.md)

- [Informacje o Azure Security Center adaptacyjnej ograniczania przepustowości sieci](../../security-center/security-center-adaptive-network-hardening.md)

- [Informacje o Azure Security Center Access Control sieci w czasie](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1.5 | 12,5 | Klient |

Włącz przechwytywanie pakietów Network Watcher, aby zbadać nietypowe działania.

- [Jak włączyć Network Watcher](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1.6 | 12,6, 12,7 | Klient |

Wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.  Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wdrażaj wybrane rozwiązanie zapory w każdej z granic sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć zaporę platformy Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1,7 | 12,9, 12,10 | Klient |

Wdróż Application Gateway platformy Azure dla aplikacji sieci Web z włączonym protokołem HTTPS/TLS dla zaufanych certyfikatów.

- [Jak wdrożyć Application Gateway](../../application-gateway/quick-create-portal.md)

- [Jak skonfigurować Application Gateway do korzystania z protokołu HTTPS](../../application-gateway/create-ssl-portal.md)

- [Omówienie równoważenia obciążenia warstwy 7 za pomocą bram aplikacji sieci Web platformy Azure](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1.8 | 1.5 | Klient |

Użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Aby uprościć konfigurację zabezpieczeń, można również użyć grup zabezpieczeń aplikacji. Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.

- [Zrozumienie i używanie tagów usługi](../../virtual-network/service-tags-overview.md)

- [Zrozumienie i używanie grup zabezpieczeń aplikacji](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1,9 | 11,1 | Klient |

Zdefiniuj i Implementuj standardowe konfiguracje zabezpieczeń dla zasobów sieciowych za pomocą Azure Policy.

Możesz również użyć planów platformy Azure, aby uprościć duże wdrożenia platformy Azure przez pakowanie kluczowych artefaktów, takich jak szablony usługi Azure Resources, formanty RBAC platformy Azure i zasady, w ramach jednej definicji planu. Możesz zastosować plan do nowych subskrypcji i dostosować kontrolę i zarządzanie przy użyciu wersji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Przykłady Azure Policy dla sieci](../../governance/policy/samples/built-in-policies.md#network)

- [Jak utworzyć Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1.10 | 11,2 | Klient |

Używaj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../../azure-resource-manager/management/tag-resources.md)

- [Jak utworzyć Virtual Network](../../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 1,11 | 11,3 | Klient |

Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian w zasobach platformy Azure. Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w zasobach krytycznych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [Rejestrowanie i monitorowanie](security-control-logging-monitoring.md)