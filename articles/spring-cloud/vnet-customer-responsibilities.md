---
title: Obowiązki klienta działające w chmurze Azure wiosennej w sieci wirtualnej
description: W tym artykule opisano obowiązki klientów korzystających z chmury Azure wiosennej w sieci wirtualnej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0c73d0394486472c2c3c92450aab6a1a0d329cf7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878686"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Obowiązki klientów do uruchamiania chmury Azure wiosny w sieci wirtualnej
Ten dokument zawiera specyfikacje dotyczące korzystania z chmury Azure wiosennej w sieci wirtualnej.

W przypadku wdrożenia chmury Azure wiosny w sieci wirtualnej ma ona zależności wychodzące od usług spoza sieci wirtualnej. W celach zarządzania i operacyjnych chmura Wiosenna Azure musi uzyskać dostęp do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te punkty końcowe są wymagane do komunikowania się z płaszczyzną zarządzania chmurą Azure wiosną oraz do pobierania i instalowania podstawowych składników klastra Kubernetes i aktualizacji zabezpieczeń.

Domyślnie chmura sprężynowa platformy Azure ma nieograniczony dostęp do Internetu (ruch wychodzący). Ten poziom dostępu do sieci umożliwia aplikacjom, które są uruchamiane w celu uzyskania dostępu do zasobów zewnętrznych, w razie potrzeby. Jeśli chcesz ograniczyć ruch wychodzący, do zadań konserwacyjnych musi być dostępna ograniczona liczba portów i adresów. Najprostszym rozwiązaniem do zabezpieczenia adresów wychodzących jest użycie urządzenia zapory, które może kontrolować ruch wychodzący na podstawie nazw domen. Zapora platformy Azure może na przykład ograniczyć wychodzący ruch HTTP i HTTPS na podstawie nazwy FQDN lokalizacji docelowej. Możesz również skonfigurować preferowane reguły zapory i zabezpieczeń, aby umożliwić korzystanie z wymaganych portów i adresów.

## <a name="azure-spring-cloud-resource-requirements"></a>Wymagania dotyczące zasobów chmury wiosennej platformy Azure 

Poniżej znajduje się lista wymagań dotyczących zasobów dla usług Azure wiosny Cloud Services. Zgodnie z ogólnym wymaganiem nie należy modyfikować grup zasobów utworzonych przez chmurę sieciową platformy Azure i bazowe zasoby sieciowe.
- Nie należy modyfikować grup zasobów utworzonych i należących do chmury Azure wiosennej.
  - Domyślnie te grupy zasobów są nazywane jako AP-SVC-rt_ [SERVICE-INSTANCE-NAME]_[region] * i AP_[Service-instance-Name] _ [region] *.
- Nie należy modyfikować podsieci używanych przez chmurę wiosenną platformy Azure.
- Nie należy tworzyć więcej niż jednego wystąpienia usługi w chmurze Azure wiosny w tej samej podsieci.
- W przypadku sterowania ruchem przez zaporę *nie należy blokować* poniższego ruchu wychodzącego do składników chmurowych platformy Azure, które działają, utrzymują i obsługują wystąpienie usługi.

## <a name="azure-spring-cloud-network-requirements"></a>Wymagania dotyczące sieci w chmurze ze sprężyną na platformie Azure

  | Docelowy punkt końcowy | Port | Zastosowanie | Uwaga |
  |------|------|------|
  | *: 1194 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 1194 | UDP: 1194 | Podstawowe Zarządzanie klastrem Kubernetes. | |
  | *: 443 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 443 | TCP: 443 | Zarządzanie usługą w chmurze Azure wiosną. | Informacje o wystąpieniu usługi "requiredTraffics" mogą być znane w ładunku zasobów w sekcji "networkProfile". |
  | *: 9000 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 9000 | TCP: 9000 | Podstawowe Zarządzanie klastrem Kubernetes. |
  | *: 123 *lub* NTP.Ubuntu.com:123 | UDP: 123 | Synchronizacja czasu NTP w węzłach systemu Linux. | |
  | *. azure.io:443 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry. | Można je zastąpić, włączając *Azure Container Registry* [punkt końcowy usługi w sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *. core.windows.net:443 i *. core.windows.net:445 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Storage: 443 i Storage: 445 | TCP: 443, TCP: 445 | Azure File Storage | Można je zastąpić, włączając  [punkt końcowy usługi Azure Storage w sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *. servicebus.windows.net:443 *lub* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -EventHub: 443 | TCP: 443 | Centrum zdarzeń platformy Azure. | Można je zastąpić, włączając  [punkt końcowy usługi Azure Event Hubs w sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Wymagania/reguły aplikacji w chmurze ze sprężyną Azure

Zapora systemu Azure udostępnia w pełni kwalifikowaną nazwę domeny (FQDN) **AzureKubernetesService** , aby uprościć następujące konfiguracje.

  | Docelowa nazwa FQDN | Port | Zastosowanie |
  |------|------|------|
  | *. azmk8s.io | HTTPS: 443 | Podstawowe Zarządzanie klastrem Kubernetes. |
  | <i>mcr.microsoft.com</i> | HTTPS: 443 | Microsoft Container Registry (MCR). |
  | *. cdn.mscr.io | HTTPS: 443 | MCR magazynu przez Azure CDN. |
  | *. data.mcr.microsoft.com | HTTPS: 443 | MCR magazynu przez Azure CDN. |
  | <i>management.azure.com</i> | HTTPS: 443 | Podstawowe Zarządzanie klastrem Kubernetes. |
  | <i>login.microsoftonline.com</i> | HTTPS: 443 | Uwierzytelnianie Azure Active Directory. |
  |<i>packages.microsoft.com</i>    | HTTPS: 443 | Repozytorium pakietów firmy Microsoft. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS: 443 | Repozytorium jest wymagane do instalacji wymaganych plików binarnych, takich jak korzystającą wtyczki kubenet i Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS: 80 | Wymagane ścieżki łańcucha certyfikatów firmy Microsoft. |
  | *crl.microsoft.com* | HTTPS: 80 | Wymagane ścieżki łańcucha certyfikatów firmy Microsoft. |
  | *crl3.digicert.com* | HTTPS: 80 | Ścieżki łańcucha certyfikatów SSL innej firmy. |

## <a name="see-also"></a>Zobacz też
* [Uzyskiwanie dostępu do aplikacji w sieci prywatnej](spring-cloud-access-app-virtual-network.md)
* [Uwidacznianie aplikacji przy użyciu Application Gateway i zapory platformy Azure](spring-cloud-expose-apps-gateway-azure-firewall.md)