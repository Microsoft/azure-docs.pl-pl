---
title: Używanie usługi Azure API Management z wewnętrznymi sieciami wirtualnymi
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować usługę Azure API Management w wewnętrznej sieci wirtualnej
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 35d813b6dfedbd7f76a88713757ce83c2644ff95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813150"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Korzystanie z usługi Azure API Management z wewnętrzną siecią wirtualną
Dzięki sieciom wirtualnym platformy Azure API Management zarządzać interfejsami API niedostępnymi w Internecie. Do nawiązaniu połączenia jest dostępnych wiele technologii sieci VPN. API Management można wdrożyć w dwóch głównych trybach w sieci wirtualnej:
* Zewnętrzna
* Wewnętrzny

Podczas API Management w trybie wewnętrznej sieci wirtualnej wszystkie punkty końcowe usługi (brama serwera proxy, portal deweloperów, bezpośrednie zarządzanie i usługa Git) są widoczne tylko w sieci wirtualnej, do których kontrolujesz dostęp. Żaden z punktów końcowych usługi nie jest zarejestrowany na publicznym serwerze DNS.

> [!NOTE]
> Ponieważ nie ma żadnych wpisów DNS dla punktów końcowych usługi, te punkty końcowe nie będą dostępne, dopóki [system DNS](#apim-dns-configuration) nie zostanie skonfigurowany dla sieci wirtualnej.

Korzystając API Management w trybie wewnętrznym, można osiągnąć następujące scenariusze:

* Zapewnianie bezpiecznego dostępu do interfejsów API hostowanych w prywatnym centrum danych przez osoby trzecie poza nim przy użyciu połączeń typu lokacja-lokacja lub Azure ExpressRoute sieci VPN.
* Włącz scenariusze chmury hybrydowej, ujawniając interfejsy API oparte na chmurze i lokalne interfejsy API za pośrednictwem wspólnej bramy.
* Zarządzanie interfejsami API hostowanych w wielu lokalizacjach geograficznych przy użyciu pojedynczego punktu końcowego bramy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ **Aktywna subskrypcja platformy Azure.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Wystąpienie usługi Azure API Management .** Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management .](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Gdy usługa API Management jest wdrożona w sieci wirtualnej, jest używana lista portów i należy je otworzyć. [](./api-management-using-with-vnet.md#required-ports) 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Tworzenie API Management w wewnętrznej sieci wirtualnej
Usługa API Management w wewnętrznej sieci wirtualnej jest hostowana za podstawową wersją SKU wewnętrznego równoważenia obciążenia, jeśli usługa jest tworzona przy użyciu interfejsu API klienta w wersji 2020-12-01. W przypadku usługi utworzonej z klientami z interfejsem API w wersji 2021-01-01-preview i z publicznym adresem IP z subskrypcji klienta jest ona hostowana za wewnętrzną standardową wersją SKU usługi równoważenia obciążenia. Aby uzyskać więcej informacji, [zobacz Azure Load Balancer SKU](../load-balancer/skus.md).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Włączanie połączenia sieci wirtualnej przy użyciu Azure Portal

1. Przejdź do wystąpienia usługi Azure API Management w [Azure Portal](https://portal.azure.com/).
1. Wybierz **pozycję Sieć wirtualna.**
1. Skonfiguruj typ **dostępu** Wewnętrzne. Aby uzyskać szczegółowe instrukcje, zobacz [Włączanie łączności z siecią wirtualną przy użyciu Azure Portal](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![Menu do konfigurowania usługi Azure API Management w wewnętrznej sieci wirtualnej][api-management-using-internal-vnet-menu]

4. Wybierz pozycję **Zapisz**.

Po zakończeniu wdrażania prywatny  wirtualny adres  IP i publiczny wirtualny adres IP usługi API Management powinny być dostępne w bloku przeglądu. Prywatny **wirtualny** adres IP jest adresem IP o zrównoważonym obciążeniu z poziomu API Management podsieci delegowane, z której można uzyskiwać dostęp do punktów końcowych , `gateway` i `portal` `management` `scm` . Publiczny **wirtualny** adres  IP jest używany tylko dla ruchu płaszczyzny sterowania do punktu końcowego przez port 3443 i może być zablokowany do tagu usługi `management` [ApiManagement.][ServiceTags]

![API Management pulpitu nawigacyjnego ze skonfigurowaną wewnętrzną siecią wirtualną][api-management-internal-vnet-dashboard]

> [!NOTE]
> Konsola Test dostępna w witrynie Azure  Portal nie będzie działać w przypadku usługi wdrożonej w wewnętrznej sieci wirtualnej, ponieważ adres URL bramy nie jest zarejestrowany w publicznym systemie DNS. Zamiast tego należy użyć konsoli testowej dostępnej w **portalu dla deweloperów.**

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Wdrażanie API Management w Virtual Network

Możesz również włączyć łączność z siecią wirtualną przy użyciu następujących metod.


### <a name="api-version-2020-12-01"></a>Interfejs API w wersji 2020-12-01

* Azure Resource Manager [szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-internal-vnet)

     [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet — [tworzenie](/powershell/module/az.apimanagement/new-azapimanagement) lub [aktualizowanie](/powershell/module/az.apimanagement/update-azapimanagementregion) wystąpienia API Management w sieci wirtualnej

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Konfiguracja dns
Gdy API Management jest w trybie zewnętrznej sieci wirtualnej, usługa DNS jest zarządzana przez platformę Azure. W przypadku trybu wewnętrznej sieci wirtualnej musisz zarządzać własną usługą DNS. Zalecaną opcją Azure DNS skonfigurowanie strefy prywatnej i połączenie jej z siecią API Management wirtualnej. Dowiedz się, [jak skonfigurować strefę prywatną w Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> API Management usługa nie nasłuchuje żądań pochodzących z adresów IP. Odpowiada tylko na żądania na nazwę hosta skonfigurowaną w punktach końcowych usługi. Te punkty końcowe obejmują bramę, Azure Portal portal deweloperów, punkt końcowy zarządzania bezpośredniego i usługę Git.

### <a name="access-on-default-host-names"></a>Dostęp do domyślnych nazw hostów
Podczas tworzenia usługi API Management o nazwie "contosointernalvnet" domyślnie konfigurowane są następujące punkty końcowe usługi:

   * Brama lub serwer proxy: contosointernalvnet.azure-api.net

   * Portal deweloperów: contosointernalvnet.portal.azure-api.net

   * Nowy portal deweloperów: contosointernalvnet.developer.azure-api.net

   * Punkt końcowy zarządzania bezpośredniego: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Aby uzyskać dostęp do tych API Management końcowych usługi, możesz utworzyć maszynę wirtualną w podsieci połączonej z siecią wirtualną, w której API Management jest wdrażana. Przy założeniu, że wewnętrzny wirtualny adres IP usługi to 10.1.0.5, można mapować plik hosts %SystemDrive%\drivers\etc\hosts w następujący sposób:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Następnie możesz uzyskać dostęp do wszystkich punktów końcowych usługi z utworzonej maszyny wirtualnej.
Jeśli używasz niestandardowego serwera DNS w sieci wirtualnej, możesz również utworzyć rekordy A DNS i uzyskać dostęp do tych punktów końcowych z dowolnego miejsca w sieci wirtualnej.

### <a name="access-on-custom-domain-names"></a>Dostęp do niestandardowych nazw domen

1. Jeśli nie chcesz uzyskać dostępu do usługi API Management przy użyciu domyślnych nazw hostów, możesz skonfigurować niestandardowe nazwy domen dla wszystkich punktów końcowych usługi, jak pokazano na poniższej ilustracji:

   ![Konfigurowanie domeny niestandardowej dla API Management][api-management-custom-domain-name]

2. Następnie możesz utworzyć rekordy na serwerze DNS, aby uzyskać dostęp do punktów końcowych, które są dostępne tylko z poziomu sieci wirtualnej.

## <a name="routing"></a><a name="routing"></a> Routing

* Prywatny *wirtualny* adres IP ze zrównoważonym obciążeniem z zakresu podsieci zostanie zarezerwowany i będzie używany do uzyskiwania dostępu do API Management końcowych usługi z poziomu sieci wirtualnej. Ten *prywatny* adres IP można znaleźć w bloku Przegląd usługi w Azure Portal. Ten adres musi być zarejestrowany na serwerach DNS używanych przez sieć wirtualną.
* Publiczny adres *IP* (VIP) ze zrównoważonym obciążeniem również zostanie zarezerwowany w celu zapewnienia dostępu do punktu końcowego usługi zarządzania za pośrednictwem portu 3443. Ten *publiczny* adres IP można znaleźć w bloku Przegląd usługi w Azure Portal. Publiczny *adres* IP jest używany tylko dla ruchu płaszczyzny sterowania do punktu końcowego przez port 3443 i może być zablokowany do tagu usługi `management` [ApiManagement.][ServiceTags]
* Adresy IP z zakresu adresów IP podsieci (DIP) zostaną przypisane do każdej maszyny wirtualnej w usłudze i będą używane do uzyskiwania dostępu do zasobów w sieci wirtualnej. Publiczny adres IP (VIP) będzie używany do uzyskiwania dostępu do zasobów poza siecią wirtualną. Jeśli listy ograniczeń adresów IP są używane do zabezpieczania zasobów w sieci wirtualnej, należy określić cały zakres podsieci, w której wdrożono usługę API Management, aby udzielić lub ograniczyć dostęp z usługi.
* Publiczny i prywatny adres IP ze zrównoważonym obciążeniem można znaleźć w bloku Przegląd w Azure Portal.
* Adresy IP przypisane do dostępu publicznego i prywatnego mogą ulec zmianie, jeśli usługa zostanie usunięta z sieci wirtualnej, a następnie dodana z powrotem do sieci wirtualnej. W takim przypadku może być konieczne zaktualizowanie rejestracji DNS, reguł routingu i list ograniczeń adresów IP w sieci wirtualnej.

## <a name="related-content"></a><a name="related-content"> </a>Powiązana zawartość
Więcej informacji można znaleźć w następujących artykułach:
* [Typowe problemy z konfiguracją sieci podczas konfigurowania usługi Azure API Management w sieci wirtualnej][Common network configuration problems]
* [Sieć wirtualna często zadawane pytania](../virtual-network/virtual-networks-faq.md)
* [Tworzenie rekordu w systemie DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
