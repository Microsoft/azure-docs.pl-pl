---
title: Obowiązki klienta uruchomione w Azure Spring Cloud wirtualnej
description: W tym artykule opisano obowiązki klienta Azure Spring Cloud w sieci wirtualnej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java
ms.openlocfilehash: a6b444092ec4e3588564a3f902b49c4ed3dc5fe5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376787"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Obowiązki klienta dotyczące uruchamiania Azure Spring Cloud sieci wirtualnej
Ten dokument zawiera specyfikacje dotyczące używania Azure Spring Cloud w sieci wirtualnej.

Po Azure Spring Cloud sieci wirtualnej ma ona zależności ruchu wychodzącego od usług spoza sieci wirtualnej. W celach zarządzania i Azure Spring Cloud musi mieć dostęp do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te punkty końcowe są wymagane do komunikowania się z płaszczyzną zarządzania Azure Spring Cloud oraz do pobierania i instalowania podstawowych składników klastra Kubernetes i aktualizacji zabezpieczeń.

Domyślnie program Azure Spring Cloud nieograniczony dostęp wychodzący do Internetu. Ten poziom dostępu do sieci umożliwia uruchamianym aplikacjom dostęp do zasobów zewnętrznych zgodnie z potrzebami. Jeśli chcesz ograniczyć ruch wychodzący, ograniczona liczba portów i adresów musi być dostępna dla zadań konserwacji. Najprostszym rozwiązaniem zabezpieczania adresów wychodzących jest użycie urządzenia zapory, które może kontrolować ruch wychodzący na podstawie nazw domen. Azure Firewall może na przykład ograniczyć wychodzący ruch HTTP i HTTPS na podstawie WQDN miejsca docelowego. Możesz również skonfigurować preferowaną zaporę i reguły zabezpieczeń, aby zezwolić na te wymagane porty i adresy.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud zasobów 

Poniżej znajduje się lista wymagań dotyczących zasobów dla Azure Spring Cloud usług. Zgodnie z ogólnym wymaganiem nie należy modyfikować grup zasobów utworzonych przez Azure Spring Cloud i bazowych zasobów sieciowych.
- Nie należy modyfikować grup zasobów utworzonych i należących do Azure Spring Cloud.
  - Domyślnie te grupy zasobów są nazwane jako ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]* i ap_[SERVICE-INSTANCE-NAME]_[REGION]*.
- Nie należy modyfikować podsieci używanych przez Azure Spring Cloud.
- Nie należy tworzyć więcej niż jednego Azure Spring Cloud usługi w tej samej podsieci.
- W przypadku używania zapory do sterowania ruchem *nie* należy blokować następującego ruchu wychodzącego do Azure Spring Cloud składników, które działają, obsługują i obsługują wystąpienie usługi.

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud sieciowe

  | Docelowy punkt końcowy | Port | Zastosowanie | Uwaga |
  |------|------|------|------|
  | *:1194 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) — AzureCloud:1194 | UDP:1194 | Podstawowe zarządzanie klastrami Kubernetes. | |
  | *:443 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) — AzureCloud:443 | TCP:443 | Azure Spring Cloud Service Management. | Informacje o wystąpieniu usługi "requiredTraffics" mogą być znane w ładunku zasobu w sekcji "networkProfile". |
  | *:9000 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) — AzureCloud:9000 | TCP:9000 | Podstawowe zarządzanie klastrami Kubernetes. |
  | *:123 *lub ntp.ubuntu.com:123* | UDP:123 | Synchronizacja czasu NTP w węzłach systemu Linux. | |
  | *.azure.io:443 lub  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) — AzureContainerRegistry:443 | TCP:443 | Azure Container Registry. | Można zastąpić przez włączenie punktu *końcowego Azure Container Registry* [usługi w sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.core.windows.net:443 i *.core.windows.net:445 *Lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) — Storage:443 and Storage:445 | TCP:443, TCP:445 | Azure File Storage | Można go zastąpić, włączając punkt *końcowy usługi Azure Storage* w sieci [wirtualnej.](../virtual-network/virtual-network-service-endpoints-overview.md) |
  | *.servicebus.windows.net:443 lub  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) — EventHub:443 | TCP:443 | Azure Event Hub. | Można zastąpić przez włączenie punktu *końcowego Azure Event Hubs* [usługi w sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud/reguły aplikacji w sieci FQDN

Azure Firewall udostępnia tag FQDN **AzureKubernetesService,** aby uprościć następujące konfiguracje:

  | Docelowa fQDN | Port | Zastosowanie |
  |------|------|------|
  | *.azmk8s.io | HTTPS:443 | Podstawowe zarządzanie klastrami Kubernetes. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft Container Registry (MCR). |
  | *.cdn.mscr.io | HTTPS:443 | Magazyn MCR, który jest Azure CDN. |
  | *.data.mcr.microsoft.com | HTTPS:443 | Magazyn MCR z Azure CDN. |
  | <i>management.azure.com</i> | HTTPS:443 | Podstawowe zarządzanie klastrami Kubernetes. |
  | <i>*login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory uwierzytelniania. |
  | <i>*login.microsoft.com</i> | HTTPS:443 | Azure Active Directory uwierzytelniania. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Repozytorium pakietów firmy Microsoft. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Repozytorium wymagane do zainstalowania wymaganych plików binarnych, takich jak kubenet i Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS:80 | Wymagane ścieżki łańcucha certyfikatów firmy Microsoft. |
  | *crl.microsoft.com* | HTTPS:80 | Wymagane ścieżki łańcucha certyfikatów firmy Microsoft. |
  | *crl3.digicert.com* | HTTPS:80 | Ścieżki łańcucha certyfikatów SSL innych firm. |
  
## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Azure Spring Cloud FQDN do zarządzania wydajnością aplikacji innych firm

Azure Firewall udostępnia tag FQDN **AzureKubernetesService,** aby uprościć następujące konfiguracje:

  | Docelowa fQDN | Port | Zastosowanie                                                          |
  | ---------------- | ---- | ------------------------------------------------------------ |
  | moduł zbierający*.newrelic.com | TCP:443/80 | Wymagane sieci agentów New Relic APM z regionu Stanów Zjednoczonych, zobacz również [Sieci agentów APM](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
  | moduł zbierający*.eu01.nr-data.net | TCP:443/80 | Wymagane sieci agentów New Relic APM z regionu UE, zobacz również [Sieci agentów APM](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |

## <a name="see-also"></a>Zobacz też
* [Uzyskiwanie dostępu do aplikacji w sieci prywatnej](access-app-virtual-network.md)
* [Uwidocznij aplikacje przy użyciu Application Gateway i Azure Firewall](expose-apps-gateway-azure-firewall.md)
