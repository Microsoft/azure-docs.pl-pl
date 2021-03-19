---
title: Adresy IP w Azure Functions
description: Dowiedz się, jak znaleźć przychodzące i wychodzące adresy IP dla aplikacji funkcji i co powoduje ich zmianę.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 2c248756899459e17082bcab863a4e857b594909
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608235"
---
# <a name="ip-addresses-in-azure-functions"></a>Adresy IP w Azure Functions

W tym artykule opisano następujące pojęcia dotyczące adresów IP aplikacji funkcji:

* Lokalizowanie adresów IP, które są obecnie używane przez aplikację funkcji.
* Warunki, które powodują zmianę adresów IP aplikacji funkcji.
* Ograniczenie adresów IP, które mogą uzyskiwać dostęp do aplikacji funkcji.
* Definiowanie dedykowanych adresów IP dla aplikacji funkcji.

Adresy IP są skojarzone z aplikacjami funkcji, a nie z poszczególnymi funkcjami. Przychodzące żądania HTTP nie mogą używać przychodzącego adresu IP do wywoływania pojedynczych funkcji; muszą używać domyślnej nazwy domeny (functionappname.azurewebsites.net) lub niestandardowej nazwy domeny.

## <a name="function-app-inbound-ip-address"></a>Adres IP ruchu przychodzącego aplikacji funkcji

Każda aplikacja funkcji ma jeden adres IP ruchu przychodzącego. Aby znaleźć ten adres IP:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do aplikacji funkcji.
3. W obszarze **Ustawienia** wybierz pozycję **Właściwości**. Adres IP ruchu przychodzącego jest wyświetlany w obszarze **wirtualny adres IP**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Wychodzące adresy IP aplikacji funkcji

Każda aplikacja funkcji ma zestaw dostępnych wychodzących adresów IP. Każde połączenie wychodzące z funkcji, na przykład do bazy danych zaplecza, używa jednego z dostępnych wychodzących adresów IP jako źródłowego adresu IP. Nie można wcześniej wiedzieć, który adres IP będzie używany przez określone połączenie. Z tego powodu usługa zaplecza musi otworzyć Zaporę we wszystkich wychodzących adresach IP aplikacji funkcji.

Aby znaleźć wychodzące adresy IP dostępne dla aplikacji funkcji:

1. Zaloguj się do [Azure Resource Explorer](https://resources.azure.com).
2. Wybierz pozycję **subskrypcje > {Twoja subskrypcja} > dostawcami > witryny Microsoft. Web >**.
3. W panelu JSON Znajdź witrynę z `id` właściwością kończącą się nazwą aplikacji funkcji.
4. Zobacz `outboundIpAddresses` i `possibleOutboundIpAddresses` . 

Zestaw `outboundIpAddresses` jest obecnie dostępny dla aplikacji funkcji. Zestaw `possibleOutboundIpAddresses` zawiera adresy IP, które będą dostępne tylko wtedy, gdy aplikacja funkcji jest [skalowana w inne warstwy cenowe](#outbound-ip-address-changes).

Alternatywnym sposobem znalezienia dostępnych wychodzących adresów IP jest użycie [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> W przypadku skalowania aplikacji funkcji uruchamianej zgodnie z [planem zużycia](consumption-plan.md) lub [planu Premium](functions-premium-plan.md) można przypisać nowy zakres wychodzących adresów IP. W przypadku korzystania z jednego z tych planów może być konieczne dodanie całego centrum danych do listy dozwolonych.

## <a name="data-center-outbound-ip-addresses"></a>Wychodzące adresy IP centrum danych

Jeśli musisz dodać wychodzące adresy IP używane przez aplikacje funkcji do dozwolonych, kolejną opcją jest dodanie centrum danych aplikacji funkcji (region platformy Azure) do dozwolonych. Można [pobrać plik JSON zawierający listę adresów IP dla wszystkich centrów danych platformy Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Następnie Znajdź fragment JSON dotyczący regionu, w którym działa aplikacja funkcji.

Na przykład poniższy fragment kodu JSON może wyglądać następująco:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Aby uzyskać informacje o tym, kiedy ten plik jest aktualizowany i kiedy zmieniają się adresy IP, rozwiń sekcję **szczegóły** na [stronie Centrum pobierania](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Zmiany adresów IP dla ruchu przychodzącego

Adres IP ruchu przychodzącego **może** ulec zmianie, gdy:

- Usuń aplikację funkcji i utwórz ją ponownie w innej grupie zasobów.
- Usuń ostatnią aplikację funkcji w kombinacji grupy zasobów i regionu i utwórz ją ponownie.
- Usuń powiązanie TLS, takie jak podczas [odnawiania certyfikatu](../app-service/configure-ssl-certificate.md#renew-certificate).

Gdy aplikacja funkcji jest uruchamiana w [planie zużycia](consumption-plan.md) lub w [planie Premium](functions-premium-plan.md), adres IP ruchu przychodzącego może ulec zmianie nawet wtedy, gdy nie wykonano żadnych akcji, takich jak [wymienione powyżej](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Zmiany wychodzącego adresu IP

Zestaw dostępnych wychodzących adresów IP dla aplikacji funkcji może ulec zmianie, gdy:

* Wykonaj wszelkie akcje, które mogą zmienić przychodzące adresy IP.
* Zmień warstwę cenową planu App Service. Lista wszystkich możliwych wychodzących adresów IP, które mogą być używane przez aplikację dla wszystkich warstw cenowych, znajduje się we `possibleOutboundIPAddresses` właściwości. Zobacz [Znajdź wychodzące adresy IP](#find-outbound-ip-addresses).

Gdy aplikacja funkcji jest uruchamiana w [planie zużycia](consumption-plan.md) lub w [planie Premium](functions-premium-plan.md), wychodzący adres IP może ulec zmianie nawet wtedy, gdy nie wykonano żadnych akcji, takich jak [wymienione powyżej](#inbound-ip-address-changes).

Aby zaświadomie wymusić zmianę wychodzącego adresu IP, należy wykonać poniższą procedurę:

1. Skaluj plan App Service w górę lub w dół między warstwami cenowymi Standard i Premium w wersji 2.

2. Odczekaj 10 minut.

3. Skaluj z powrotem do miejsca, w którym rozpoczęto pracę.

## <a name="ip-address-restrictions"></a>ograniczenia adresów IP

Można skonfigurować listę adresów IP, dla których chcesz zezwolić na dostęp lub odmówić dostępu do aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [Azure App Service ograniczeń statycznych adresów IP](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedykowane adresy IP

Istnieje kilka strategii, które należy zbadać, gdy aplikacja funkcji wymaga statycznych, dedykowanych adresów IP. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>Brama NAT sieci wirtualnej dla wychodzącego statycznego adresu IP

Można kontrolować adres IP ruchu wychodzącego z funkcji przy użyciu bramy translatora adresów sieciowych sieci wirtualnej do kierowania ruchem za pośrednictwem statycznego publicznego adresu IP. Tej topologii można używać w [planie Premium](functions-premium-plan.md). Aby dowiedzieć się więcej, zobacz [Samouczek: kontrola Azure Functions wychodzący adres IP z bramą NAT sieci wirtualnej platformy Azure](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>Środowiska usługi App Service

Aby uzyskać pełną kontrolę nad adresami IP, zarówno przychodzącymi, jak i wychodzącymi, zalecamy [App Service środowiska](../app-service/environment/intro.md) ( [izolowana warstwa](https://azure.microsoft.com/pricing/details/app-service/) App Service planów). Aby uzyskać więcej informacji, zobacz [App Service Environment adresy IP](../app-service/environment/network-info.md#ase-ip-addresses) i [sterowanie ruchem przychodzącym do App Service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Aby dowiedzieć się, czy aplikacja funkcji działa w App Service Environment:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do aplikacji funkcji.
3. Wybierz kartę **Omówienie**.
4. Warstwa planu App Service zostanie wyświetlona w obszarze **App Service planu/warstwy cenowej**. Warstwa cenowa App Service Environment jest **izolowana**.
 
Alternatywnie można użyć [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service Environment `sku` to `Isolated` .

## <a name="next-steps"></a>Następne kroki

Częstą przyczyną zmian adresów IP są zmiany skalowania aplikacji. [Dowiedz się więcej o skalowaniu aplikacji funkcji](functions-scale.md).
