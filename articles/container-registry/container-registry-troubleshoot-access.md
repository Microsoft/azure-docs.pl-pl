---
title: Rozwiązywanie problemów z siecią przy użyciu rejestru
description: Objawy, przyczyny i rozwiązywanie typowych problemów podczas uzyskiwania dostępu do usługi Azure Container Registry w sieci wirtualnej lub za zaporą
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 75c94d40663a7058dab7ed691183dd578964edcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699610"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Rozwiązywanie problemów z siecią przy użyciu rejestru

Ten artykuł pomaga rozwiązywać problemy, które mogą wystąpić podczas uzyskiwania dostępu do usługi Azure Container Registry w sieci wirtualnej lub za zaporą. 

## <a name="symptoms"></a>Objawy

Może zawierać co najmniej jedną z następujących czynności:

* Nie można wypchnąć ani ściągnąć obrazów i pojawia się błąd `dial tcp: lookup myregistry.azurecr.io`
* Nie można wypchnąć ani ściągnąć obrazów i uzyskać błędu interfejsu wiersza polecenia platformy Azure `Could not connect to the registry login server`
* Nie można ściągnąć obrazów z rejestru do usługi Azure Kubernetes lub innej usługi platformy Azure
* Nie można uzyskać dostępu do rejestru za pośrednictwem serwera proxy HTTPS i pojawia się błąd `Error response from daemon: login attempt failed with status: 403 Forbidden`
* Nie można skonfigurować ustawień sieci wirtualnej i pojawia się błąd `Failed to save firewall and virtual network settings for container registry`
* Nie można uzyskać dostępu do ustawień rejestru w Azure Portal lub zarządzać rejestrem przy użyciu interfejsu wiersza polecenia platformy Azure
* Nie można dodać lub zmodyfikować ustawień sieci wirtualnej lub reguł dostępu publicznego
* Zadania ACR nie mogą wypchnąć ani ściągnąć obrazów
* Azure Security Center nie może skanować obrazów w rejestrze lub wyniki skanowania nie są wyświetlane w Azure Security Center

## <a name="causes"></a>Przyczyny

* Zapora klienta lub serwer proxy uniemożliwiają [rozwiązanie](#configure-client-firewall-access) dostępu
* Reguły dostępu do sieci publicznej w rejestrze uniemożliwiają dostęp do [rozwiązania](#configure-public-access-to-registry)
* Konfiguracja sieci wirtualnej uniemożliwia dostęp do [rozwiązania](#configure-vnet-access)
* Podjęto próbę zintegrowania Azure Security Center lub niektórych innych usług platformy Azure z rejestrem, który ma prywatny punkt końcowy, punkt końcowy usługi lub reguły dostępu do publicznego adresu IP — [rozwiązanie](#configure-service-access)

## <a name="further-diagnosis"></a>Przeprowadzenia dalszej diagnostyki 

Uruchom polecenie [AZ ACR Check-Health](/cli/azure/acr#az-acr-check-health) , aby uzyskać więcej informacji na temat kondycji środowiska rejestru i opcjonalnego dostępu do rejestru docelowego. Na przykład diagnozowanie niektórych problemów z łącznością sieciową lub konfiguracją. 

Przykłady poleceń można znaleźć [w temacie Sprawdzanie kondycji usługi Azure Container Registry](container-registry-check-health.md) . W przypadku zgłoszenia błędów Przejrzyj [Informacje o błędzie](container-registry-health-error-reference.md) i poniższe sekcje dotyczące zalecanych rozwiązań.

Jeśli występują problemy przy użyciu rejestru wih usługę Azure Kubernetes, uruchom polecenie [AZ AKS Check-ACR](/cli/azure/aks#az_aks_check_acr) , aby sprawdzić, czy rejestr jest dostępny z klastra AKS.

> [!NOTE]
> Niektóre objawy łączności sieciowej mogą również wystąpić w przypadku problemów z uwierzytelnianiem lub autoryzacją rejestru. Zobacz [Rozwiązywanie problemów z logowaniem do rejestru](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Potencjalne rozwiązania

### <a name="configure-client-firewall-access"></a>Konfigurowanie dostępu do zapory klienta

Aby uzyskać dostęp do rejestru za pośrednictwem zapory klienta lub serwera proxy, należy skonfigurować reguły zapory w celu uzyskania dostępu do publicznego punktu końcowego REST i danych rejestru. W przypadku włączenia [dedykowanych punktów końcowych danych](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) wymagane są reguły dostępu do programu:

* Punkt końcowy REST: `<registryname>.azurecr.io`
* Punkty końcowe danych: `<registry-name>.<region>.data.azurecr.io`

W przypadku rejestru z replikacją geograficzną Skonfiguruj dostęp do punktu końcowego danych dla każdej repliki regionalnej.

Za pośrednictwem serwera proxy HTTPS upewnij się, że zarówno klient platformy Docker, jak i demon platformy Docker są skonfigurowane pod kątem zachowania serwera proxy.

Dzienniki zasobów rejestru w tabeli ContainerRegistryLoginEvents mogą pomóc zdiagnozować próbne połączenie, które zostało zablokowane.

Powiązane linki:

* [Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą](container-registry-firewall-access-rules.md)
* [Konfiguracja serwera proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Replikacja geograficzna Azure Container Registry](container-registry-geo-replication.md)
* [Dzienniki Azure Container Registry na potrzeby oceny i inspekcji diagnostyki](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Konfigurowanie publicznego dostępu do rejestru

W przypadku uzyskiwania dostępu do rejestru za pośrednictwem Internetu upewnij się, że rejestr zezwala na dostęp do sieci publicznej z klienta programu. Domyślnie usługa Azure Container Registry umożliwia dostęp do punktów końcowych rejestru publicznego ze wszystkich sieci. Rejestr może ograniczyć dostęp do wybranych sieci lub wybranych adresów IP. 

Jeśli rejestr jest skonfigurowany dla sieci wirtualnej z punktem końcowym usługi, wyłączenie dostępu do sieci publicznej wyłącza również dostęp za pośrednictwem punktu końcowego usługi. Jeśli rejestr jest skonfigurowany dla sieci wirtualnej z linkiem prywatnym, reguły sieci IP nie mają zastosowania do prywatnych punktów końcowych rejestru. 

Powiązane linki:

* [Konfigurowanie reguł sieci publicznych adresów IP](container-registry-access-selected-networks.md)
* [Połącz się prywatnie z usługą Azure Container Registry przy użyciu prywatnego linku platformy Azure](container-registry-private-link.md)
* [Ograniczanie dostępu do rejestru kontenerów przy użyciu punktu końcowego usługi w sieci wirtualnej platformy Azure](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Konfigurowanie dostępu do sieci wirtualnej

Upewnij się, że sieć wirtualna jest skonfigurowana za pomocą prywatnego punktu końcowego dla linku prywatnego lub punktu końcowego usługi (wersja zapoznawcza). Obecnie punkt końcowy usługi Azure bastionu nie jest obsługiwany.

Przejrzyj reguły sieciowej grupy zabezpieczeń i Tagi usług używane do ograniczania ruchu z innych zasobów w sieci do rejestru. 

Jeśli skonfigurowano punkt końcowy usługi do rejestru, potwierdź, że reguła sieci jest dodawana do rejestru, który zezwala na dostęp z tej podsieci sieciowej. Punkt końcowy usługi obsługuje tylko dostęp z maszyn wirtualnych i klastrów AKS w sieci.

Jeśli chcesz ograniczyć dostęp do rejestru przy użyciu sieci wirtualnej w innej subskrypcji platformy Azure, upewnij się, że `Microsoft.ContainerRegistry` dostawca zasobów został zarejestrowany w tej subskrypcji. [Zarejestruj dostawcę zasobów](../azure-resource-manager/management/resource-providers-and-types.md) dla Azure Container Registry przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure.

Jeśli w sieci jest skonfigurowana Zapora platformy Azure lub podobne rozwiązanie, należy sprawdzić, czy ruch wychodzący z innych zasobów, takich jak klaster AKS, jest włączony w celu uzyskania dostępu do punktów końcowych rejestru.

W przypadku skonfigurowania prywatnego punktu końcowego upewnij się, że system DNS rozwiązuje publiczną nazwę FQDN rejestru, taką jak *myregistry.azurecr.IO* , do prywatnego adresu IP rejestru. Użyj narzędzia sieciowego, takiego jak `dig` lub `nslookup` do wyszukiwania DNS.

Powiązane linki:

* [Połącz się prywatnie z usługą Azure Container Registry przy użyciu prywatnego linku platformy Azure](container-registry-private-link.md)
* [Ograniczanie dostępu do rejestru kontenerów przy użyciu punktu końcowego usługi w sieci wirtualnej platformy Azure](container-registry-vnet.md)
* [Wymagane reguły sieci wychodzącej i nazwy FQDN dla klastrów AKS](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: debugowanie rozpoznawania nazw DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Tagi usługi sieci wirtualnej](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Konfigurowanie dostępu do usługi

Obecnie dostęp do rejestru kontenerów z ograniczeniami sieci nie jest dozwolony z kilku usług platformy Azure:

* Azure Security Center nie może przeprowadzić [skanowania w poszukiwaniu luk w zabezpieczeniach](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) w rejestrze, który ogranicza dostęp do prywatnych punktów końcowych, wybranych podsieci lub adresów IP. 
* Zasoby niektórych usług platformy Azure nie mogą uzyskać dostępu do rejestru kontenerów z ograniczeniami sieci, w tym Azure App Service i Azure Container Instances.

Jeśli wymagany jest dostęp lub integracja tych usług platformy Azure z rejestrem kontenerów, Usuń ograniczenie sieci. Na przykład Usuń prywatne punkty końcowe rejestru lub Usuń lub zmodyfikuj reguły dostępu publicznego rejestru.

Od stycznia 2021 można skonfigurować rejestr z ograniczeniami sieci, aby [zezwolić na dostęp](allow-access-trusted-services.md) z wybranych usług zaufanych.

Powiązane linki:

* [Azure Container Registry skanowania obrazów przez Security Center](../security-center/defender-for-container-registries-introduction.md)
* Prześlij [opinię](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Zezwalaj zaufanym usługom na bezpieczny dostęp do rejestru kontenerów z ograniczeniami w sieci](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Jeśli [Kolekcja dzienników zasobów](container-registry-diagnostics-audit-logs.md) jest włączona w rejestrze, przejrzyj dziennik ContainterRegistryLoginEvents. Ten dziennik przechowuje zdarzenia i stan uwierzytelniania, w tym przychodzącą tożsamość i adres IP. Zbadaj dziennik pod kątem [błędów uwierzytelniania rejestru](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Powiązane linki:

* [Dzienniki dotyczące oceny i inspekcji diagnostyki](container-registry-diagnostics-audit-logs.md)
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)
* [Podstawa zabezpieczeń platformy Azure dla Azure Container Registry](security-baseline.md)
* [Najlepsze rozwiązania dla usługi Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Następne kroki

Jeśli w tym miejscu nie rozwiążesz problemu, zapoznaj się z następującymi opcjami.

* Inne tematy dotyczące rozwiązywania problemów z rejestrem obejmują:
  * [Rozwiązywanie problemów z logowaniem do rejestru](container-registry-troubleshoot-login.md) 
  * [Rozwiązywanie problemów z wydajnością rejestru](container-registry-troubleshoot-performance.md)
* Opcje [pomocy technicznej społeczności](https://azure.microsoft.com/support/community/)
* [Pytania i odpowiedzi Microsoft](/answers/products/)
* [Otwieranie biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/)