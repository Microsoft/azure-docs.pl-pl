---
title: Korzystanie z usługi Azure API Management z wewnętrznymi sieciami wirtualnymi
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować i skonfigurować usługę Azure API Management w wewnętrznej sieci wirtualnej
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 10154f496d76ce6b9eb19d610fdff8d7a4023c2d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565958"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Korzystanie z usługi Azure API Management z wewnętrzną siecią wirtualną
Usługa API Management Azure Virtual Networks umożliwia Zarządzanie interfejsami API, które nie są dostępne w Internecie. Do nawiązania połączenia są dostępne różne technologie sieci VPN. API Management można wdrożyć w dwóch głównych trybach wewnątrz sieci wirtualnej:
* Zewnętrzna
* Wewnętrzny

W przypadku API Management wdrożenia w trybie wewnętrznej sieci wirtualnej wszystkie punkty końcowe usługi (Brama serwera proxy, Portal deweloperów, zarządzanie bezpośrednie i Git) są widoczne tylko w sieci wirtualnej, do której kontrolujesz dostęp. Żaden z punktów końcowych usługi nie jest zarejestrowany na publicznym serwerze DNS.

> [!NOTE]
> Ponieważ nie ma żadnych wpisów DNS dla punktów końcowych usługi, te punkty końcowe nie będą dostępne do czasu [skonfigurowania usługi DNS](#apim-dns-configuration) dla sieci wirtualnej.

Korzystając z API Management w trybie wewnętrznym, można osiągnąć następujące scenariusze:

* Interfejsy API hostowane w Twoim prywatnym centrum danych są bezpiecznie dostępne dla osób trzecich poza nią za pomocą połączeń sieci VPN typu lokacja-lokacja lub Azure ExpressRoute.
* Scenariusze chmury hybrydowej umożliwiają udostępnianie opartych na chmurze interfejsów API i lokalnych interfejsów API za pośrednictwem wspólnej bramy.
* Zarządzanie interfejsami API hostowanymi w wielu lokalizacjach geograficznych za pomocą pojedynczego punktu końcowego bramy.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować:

+ **Aktywna subskrypcja platformy Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Wystąpienie usługi Azure API Management**. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Po wdrożeniu usługi API Management w sieci wirtualnej jest używana [Lista portów](./api-management-using-with-vnet.md#required-ports) i należy ją otworzyć. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Tworzenie API Management w wewnętrznej sieci wirtualnej
Usługa API Management w wewnętrznej sieci wirtualnej jest hostowana za [wewnętrznym modułem równoważenia obciążenia (klasycznym)](/previous-versions/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Jest to jedyna dostępna opcja i nie można jej zmienić.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Włączanie połączenia sieci wirtualnej przy użyciu Azure Portal

1. Przejdź do wystąpienia usługi Azure API Management w [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Sieć wirtualna**.
3. Skonfiguruj wystąpienie API Management, które ma zostać wdrożone w sieci wirtualnej.

    ![Menu do konfigurowania API Management platformy Azure w wewnętrznej sieci wirtualnej][api-management-using-internal-vnet-menu]

4. Wybierz pozycję **Zapisz**.

Po pomyślnym wdrożeniu należy zobaczyć **prywatny** wirtualny adres IP i **publiczny** wirtualny adres IP usługi API Management w bloku przegląd. **Prywatny** wirtualny adres IP to adres IP ze zrównoważonym obciążeniem z API Management delegowanej podsieci, w `gateway` której `portal` `management` `scm` można uzyskać dostęp do punktów końcowych. **Publiczny** wirtualny adres IP jest używany **tylko** dla ruchu płaszczyzny kontroli do `management` punktu końcowego przez port 3443 i można go zablokować do [ApiManagement][ServiceTags] servicetag.

![API Management pulpit nawigacyjny z skonfigurowaną wewnętrzną siecią wirtualną][api-management-internal-vnet-dashboard]

> [!NOTE]
> Konsola testowa dostępna w witrynie Azure Portal nie będzie działała w przypadku **wewnętrznej** usługi wdrożonej sieci wirtualnej, ponieważ adres URL bramy nie jest zarejestrowany w publicznym systemie DNS. Zamiast tego należy użyć konsoli testowej dostępnej w **portalu dla deweloperów**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Wdróż API Management w Virtual Network

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Połączenie sieci wirtualnej można również włączyć za pomocą poleceń cmdlet programu PowerShell.

* Utwórz usługę API Management w sieci wirtualnej: Użyj polecenia cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) , aby utworzyć usługę Azure API Management w sieci wirtualnej, i skonfiguruj ją tak, aby korzystała z wewnętrznego typu sieci wirtualnej.

* Aktualizowanie istniejącego wdrożenia usługi API Management w sieci wirtualnej: Użyj polecenia cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) , aby przenieść istniejącą usługę API Management do sieci wirtualnej i skonfiguruj ją tak, aby korzystała z wewnętrznego typu sieci wirtualnej.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Konfiguracja usługi DNS
Gdy API Management jest w trybie zewnętrznej sieci wirtualnej, system DNS jest zarządzany przez platformę Azure. W przypadku wewnętrznego trybu sieci wirtualnej należy zarządzać własnym systemem DNS. Zalecanym rozwiązaniem jest skonfigurowanie strefy prywatnej Azure DNS i połączenie jej z usługą API Management sieci wirtualnej.  Kliknij [tutaj](../dns/private-dns-getstarted-portal.md) , aby dowiedzieć się, jak skonfigurować strefę prywatną w Azure DNS.

> [!NOTE]
> Usługa API Management nie nasłuchuje żądań przychodzących z adresów IP. Odpowiada tylko na żądania do nazwy hosta skonfigurowanej w punktach końcowych usługi. Te punkty końcowe obejmują bramę, Azure Portal i Portal deweloperów, punkt końcowy zarządzania bezpośredniego i git.

### <a name="access-on-default-host-names"></a>Dostęp do domyślnych nazw hostów
Podczas tworzenia usługi API Management o nazwie "contosointernalvnet" na przykład następujące punkty końcowe usługi są konfigurowane domyślnie:

   * Brama lub serwer proxy: contosointernalvnet.azure-api.net

   * Portal dla deweloperów: contosointernalvnet.portal.azure-api.net

   * Nowy portal dla deweloperów: contosointernalvnet.developer.azure-api.net

   * Punkt końcowy zarządzania bezpośredniego: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Aby uzyskać dostęp do tych punktów końcowych usługi API Management, można utworzyć maszynę wirtualną w podsieci połączonej z siecią wirtualną, w której API Management jest wdrożony. Zakładając, że wewnętrzny wirtualny adres IP usługi to 10.1.0.5, można zmapować plik hosts%SystemDrive%\drivers\etc\hosts w następujący sposób:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Następnie można uzyskać dostęp do wszystkich punktów końcowych usługi z utworzonej maszyny wirtualnej.
Jeśli używasz niestandardowego serwera DNS w sieci wirtualnej, możesz również utworzyć rekordy DNS i uzyskać dostęp do tych punktów końcowych z dowolnego miejsca w sieci wirtualnej.

### <a name="access-on-custom-domain-names"></a>Dostęp do niestandardowych nazw domen

1. Jeśli nie chcesz uzyskiwać dostępu do usługi API Management z domyślnymi nazwami hostów, możesz skonfigurować niestandardowe nazwy domen dla wszystkich punktów końcowych usługi, jak pokazano na poniższej ilustracji:

   ![Konfigurowanie domeny niestandardowej dla API Management][api-management-custom-domain-name]

2. Następnie można utworzyć rekordy na serwerze DNS, aby uzyskać dostęp do punktów końcowych, które są dostępne tylko w sieci wirtualnej.

## <a name="routing"></a><a name="routing"></a> Routing

* *Prywatny* wirtualny adres IP ze zrównoważonym obciążeniem z zakresu podsieci zostanie zarezerwowany i będzie używany do uzyskiwania dostępu do punktów końcowych usługi API Management z poziomu sieci wirtualnej. Ten *prywatny* adres IP można znaleźć w bloku przegląd dla usługi w Azure Portal. Ten adres musi być zarejestrowany na serwerach DNS używanych przez sieć wirtualną.
* *Publiczny* adres IP ze zrównoważonym obciążeniem (VIP) zostanie również zarezerwowany w celu zapewnienia dostępu do punktu końcowego usługi zarządzania przez port 3443. Ten *publiczny* adres IP można znaleźć w bloku przegląd dla usługi w Azure Portal. *Publiczny* adres IP jest używany tylko dla ruchu płaszczyzny kontroli do `management` punktu końcowego przez port 3443 i można go zablokować do [ApiManagement][ServiceTags] servicetag.
* Adresy IP z zakresu adresów IP podsieci (DIP) będą przypisywane do każdej maszyny wirtualnej w usłudze i będą używane do uzyskiwania dostępu do zasobów w sieci wirtualnej. Publiczny adres IP (VIP) będzie używany do uzyskiwania dostępu do zasobów spoza sieci wirtualnej. Jeśli listy ograniczeń adresów IP są używane do zabezpieczania zasobów w sieci wirtualnej, należy określić cały zakres dla podsieci, w której wdrożono usługę API Management, aby przyznać lub ograniczyć dostęp z usługi.
* Publiczne i prywatne adresy IP ze zrównoważonym obciążeniem można znaleźć w bloku przegląd w Azure Portal.
* Adresy IP przypisane do publicznego i prywatnego dostępu mogą ulec zmianie, jeśli usługa zostanie usunięta z, a następnie dodana do sieci wirtualnej. W takim przypadku może być konieczne zaktualizowanie rejestracji DNS, reguł routingu i list ograniczeń adresów IP w ramach sieci wirtualnej.

## <a name="related-content"></a><a name="related-content"> </a>Powiązana zawartość
Więcej informacji można znaleźć w następujących artykułach:
* [Typowe problemy z konfiguracją sieci podczas konfigurowania usługi Azure API Management w sieci wirtualnej][Common network configuration problems]
* [Często zadawane pytania dotyczące sieci wirtualnej](../virtual-network/virtual-networks-faq.md)
* [Tworzenie rekordu w systemie DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
