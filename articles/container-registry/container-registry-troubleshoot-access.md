---
title: Rozwiązywanie problemów z siecią za pomocą rejestru
description: Objawy, przyczyny i rozwiązania typowych problemów występujących podczas uzyskiwania dostępu do rejestru kontenerów platformy Azure w sieci wirtualnej lub za zaporą
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 0fdedf109703eb443904989d2c0b2d75a6ba5bb1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481229"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Rozwiązywanie problemów z siecią za pomocą rejestru

Ten artykuł ułatwia rozwiązywanie problemów, które mogą wystąpić podczas uzyskiwania dostępu do rejestru kontenerów platformy Azure w sieci wirtualnej lub za zaporą bądź serwerem proxy. 

## <a name="symptoms"></a>Objawy

Może zawierać co najmniej jedną z następujących czynności:

* Nie można wypchnąć lub ściągnąć obrazów i występuje błąd `dial tcp: lookup myregistry.azurecr.io`
* Nie można wypchnąć lub ściągnąć obrazów i występuje błąd `Client.Timeout exceeded while awaiting headers`
* Nie można wypchnąć lub ściągnąć obrazów i zostanie wyświetlony błąd interfejsu wiersza polecenia platformy Azure `Could not connect to the registry login server`
* Nie można ściągnąć obrazów z rejestru do Azure Kubernetes Service lub innej usługi platformy Azure
* Nie można uzyskać dostępu do rejestru za serwerem proxy HTTPS i występuje błąd `Error response from daemon: login attempt failed with status: 403 Forbidden` lub `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Nie można skonfigurować ustawień sieci wirtualnej i jest wyświetlany błąd `Failed to save firewall and virtual network settings for container registry`
* Nie można uzyskać dostępu do ustawień rejestru w usłudze Azure Portal lub zarządzać rejestrem przy użyciu interfejsu wiersza polecenia platformy Azure
* Nie można dodać lub zmodyfikować ustawień sieci wirtualnej lub reguł dostępu publicznego
* zadania usługi ACR nie może wypychać ani ściągać obrazów
* Azure Security Center nie można skanować obrazów w rejestrze lub wyniki skanowania nie są wyświetlane w Azure Security Center
* Podczas próby uzyskania dostępu do rejestru skonfigurowanego za pomocą prywatnego punktu końcowego występuje `host is not reachable` błąd.

## <a name="causes"></a>Przyczyny

* Zapora klienta lub serwer proxy uniemożliwia dostęp — [rozwiązanie](#configure-client-firewall-access)
* Reguły dostępu do sieci publicznej w rejestrze uniemożliwiają dostęp — [rozwiązanie](#configure-public-access-to-registry)
* Konfiguracja sieci wirtualnej uniemożliwia dostęp — [rozwiązanie](#configure-vnet-access)
* Próbujesz zintegrować usługę Azure Security Center lub niektóre inne usługi platformy Azure z rejestrem, który ma prywatny punkt końcowy, punkt końcowy usługi lub reguły dostępu do publicznego adresu IP — [rozwiązanie](#configure-service-access)

## <a name="further-diagnosis"></a>Dalsza diagnostyka 

Uruchom [polecenie az acr check-health,](/cli/azure/acr#az-acr-check-health) aby uzyskać więcej informacji o kondycji środowiska rejestru i opcjonalnie uzyskać dostęp do rejestru docelowego. Na przykład zdiagnozuj niektóre problemy z łącznością sieciową lub konfiguracją. 

Zobacz [Sprawdzanie kondycji rejestru kontenerów platformy Azure, aby uzyskać](container-registry-check-health.md) przykłady poleceń. Jeśli zostaną zgłoszone błędy, zapoznaj się z [odwołaniami do błędów](container-registry-health-error-reference.md) i następującymi sekcjami, aby zapoznać się z zalecanymi rozwiązaniami.

Jeśli występują problemy z używaniem usługi Azure Kubernetes Service ze zintegrowanym rejestrem, uruchom polecenie [az aks check-acr,](/cli/azure/aks#az_aks_check_acr) aby sprawdzić, czy klaster usługi AKS może uzyskać dostęp do rejestru.

> [!NOTE]
> Niektóre objawy łączności sieciowej mogą również wystąpić, gdy występują problemy z uwierzytelnianiem lub autoryzacją rejestru. Zobacz [Rozwiązywanie problemów z logowaniem do rejestru.](container-registry-troubleshoot-login.md)

## <a name="potential-solutions"></a>Potencjalne rozwiązania

### <a name="configure-client-firewall-access"></a>Konfigurowanie dostępu zapory klienta

Aby uzyskać dostęp do rejestru zza zapory klienta lub serwera proxy, skonfiguruj reguły zapory w celu uzyskania dostępu do publicznego serwera REST i punktów końcowych danych rejestru. Jeśli [dedykowane punkty końcowe danych](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) są włączone, potrzebne są reguły dostępu:

* Punkt końcowy REST: `<registryname>.azurecr.io`
* Punkty końcowe danych: `<registry-name>.<region>.data.azurecr.io`

W przypadku rejestru z replikacją geograficzną skonfiguruj dostęp do punktu końcowego danych dla każdej repliki regionalnej.

Za serwerem proxy HTTPS upewnij się, że zarówno klient platformy Docker, jak i demon platformy Docker są skonfigurowane do zachowania serwera proxy. Jeśli zmienisz ustawienia serwera proxy dla demona platformy Docker, pamiętaj o ponownym uruchomieniu demona. 

Dzienniki zasobów rejestru w tabeli ContainerRegistryLoginEvents mogą pomóc zdiagnozować próbę połączenia, które zostało zablokowane.

Powiązane linki:

* [Konfigurowanie reguł dostępu do rejestru kontenerów platformy Azure za zaporą](container-registry-firewall-access-rules.md)
* [Konfiguracja serwera proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Replikacja geograficznaw Azure Container Registry](container-registry-geo-replication.md)
* [Azure Container Registry do oceny diagnostycznej i inspekcji](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Konfigurowanie publicznego dostępu do rejestru

Jeśli uzyskujesz dostęp do rejestru za pośrednictwem Internetu, upewnij się, że rejestr zezwala na dostęp do sieci publicznej z klienta. Domyślnie rejestr kontenerów platformy Azure umożliwia dostęp do punktów końcowych rejestru publicznego ze wszystkich sieci. Rejestr może ograniczyć dostęp do wybranych sieci lub wybranych adresów IP. 

Jeśli rejestr jest skonfigurowany dla sieci wirtualnej z punktem końcowym usługi, wyłączenie dostępu do sieci publicznej spowoduje również wyłączenie dostępu za pośrednictwem punktu końcowego usługi. Jeśli rejestr jest skonfigurowany dla sieci wirtualnej z usługą Private Link, reguły sieci IP nie mają zastosowania do prywatnych punktów końcowych rejestru. 

Powiązane linki:

* [Konfigurowanie reguł sieci publicznych adresów IP](container-registry-access-selected-networks.md)
* [Nawiązywanie prywatnego połączenia z rejestrem kontenerów platformy Azure przy użyciu Azure Private Link](container-registry-private-link.md)
* [Ograniczanie dostępu do rejestru kontenerów przy użyciu punktu końcowego usługi w sieci wirtualnej platformy Azure](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Konfigurowanie dostępu do sieci wirtualnej

Upewnij się, że sieć wirtualna jest skonfigurowana przy użyciu prywatnego punktu końcowego dla usługi Private Link lub punktu końcowego usługi (wersja zapoznawcza). Obecnie punkt Azure Bastion nie jest obsługiwany.

Jeśli skonfigurowano prywatny punkt końcowy, upewnij się, że system DNS rozpozna publiczną nazwę FQDN rejestru, na przykład *myregistry.azurecr.io* jako prywatny adres IP rejestru. Użyj narzędzia sieciowego, takiego `dig` jak lub , do wyszukiwania `nslookup` DNS. Upewnij [się, że rekordy DNS są skonfigurowane](container-registry-private-link.md#dns-configuration-options) dla nazwy FQDN rejestru i dla każdej nazwy FQDN punktu końcowego danych.

Przejrzyj reguły sieciowej organizacji sieciowej i tagi usługi używane do ograniczania ruchu z innych zasobów w sieci do rejestru. 

Jeśli skonfigurowano punkt końcowy usługi dla rejestru, upewnij się, że do rejestru dodano regułę sieci, która zezwala na dostęp z tej podsieci sieciowej. Punkt końcowy usługi obsługuje dostęp tylko z maszyn wirtualnych i klastrów usługi AKS w sieci.

Jeśli chcesz ograniczyć dostęp do rejestru przy użyciu sieci wirtualnej w innej subskrypcji platformy Azure, upewnij się, że dostawca zasobów został zarejestrowany `Microsoft.ContainerRegistry` w tej subskrypcji. [Zarejestruj dostawcę zasobów dla usługi](../azure-resource-manager/management/resource-providers-and-types.md) Azure Container Registry użyciu interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure.

Jeśli Azure Firewall lub podobne rozwiązanie jest skonfigurowane w sieci, sprawdź, czy ruch wychodzący z innych zasobów, takich jak klaster usługi AKS, jest włączony w celu dotarcia do punktów końcowych rejestru.

Powiązane linki:

* [Nawiązywanie prywatnego połączenia z rejestrem kontenerów platformy Azure przy użyciu Azure Private Link](container-registry-private-link.md)
* [Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Ograniczanie dostępu do rejestru kontenerów przy użyciu punktu końcowego usługi w sieci wirtualnej platformy Azure](container-registry-vnet.md)
* [Wymagane reguły sieciowego ruchu wychodzącego i sieci FQDN dla klastrów usługi AKS](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Debugowanie rozpoznawania nazw DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Tagi usługi dla sieci wirtualnej](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Konfigurowanie dostępu do usługi

Obecnie dostęp do rejestru kontenerów z ograniczeniami sieci nie jest dozwolony z kilku usług platformy Azure:

* Azure Security Center nie może wykonać [](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) skanowania luk w zabezpieczeniach obrazu w rejestrze, który ogranicza dostęp do prywatnych punktów końcowych, wybranych podsieci lub adresów IP. 
* Zasoby niektórych usług platformy Azure nie mogą uzyskać dostępu do rejestru kontenerów z ograniczeniami sieci, w tym Azure App Service i Azure Container Instances.

Jeśli wymagany jest dostęp do tych usług platformy Azure lub ich integracja z rejestrem kontenerów, usuń ograniczenie sieci. Na przykład usuń prywatne punkty końcowe rejestru lub usuń lub zmodyfikuj reguły dostępu publicznego rejestru.

Od stycznia 2021 r. można skonfigurować rejestr z ograniczeniami sieci, aby zezwolić na dostęp [z](allow-access-trusted-services.md) wybranych zaufanych usług.

Powiązane linki:

* [Azure Container Registry skanowania obrazów przez Security Center](../security-center/defender-for-container-registries-introduction.md)
* Przekazać [opinię](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Zezwalaj zaufanym usługom na bezpieczny dostęp do rejestru kontenerów z ograniczeniami sieci](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Jeśli [zbieranie dzienników zasobów](container-registry-diagnostics-audit-logs.md) jest włączone w rejestrze, przejrzyj dziennik ContainterRegistryLoginEvents. Ten dziennik przechowuje zdarzenia i stan uwierzytelniania, w tym tożsamość przychodzącą i adres IP. Przeszukaj w [dzienniku zapytanie o błędy uwierzytelniania rejestru.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Powiązane linki:

* [Dzienniki dotyczące oceny diagnostycznej i inspekcji](container-registry-diagnostics-audit-logs.md)
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)
* [Punkt odniesienia zabezpieczeń platformy Azure dla Azure Container Registry](security-baseline.md)
* [Najlepsze rozwiązania dla usługi Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Następne kroki

Jeśli nie rozwiążemy problemu w tym miejscu, zobacz następujące opcje.

* Inne tematy dotyczące rozwiązywania problemów z rejestrem obejmują:
  * [Rozwiązywanie problemów z logowaniem do rejestru](container-registry-troubleshoot-login.md) 
  * [Rozwiązywanie problemów z wydajnością rejestru](container-registry-troubleshoot-performance.md)
* [Opcje pomocy technicznej społeczności](https://azure.microsoft.com/support/community/)
* [Pytania i odpowiedzi Microsoft](/answers/products/)
* [Otwieranie biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/)