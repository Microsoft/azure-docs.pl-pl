---
title: Adresy IP w Azure Functions
description: Dowiedz się, jak znaleźć przychodzące i wychodzące adresy IP dla aplikacji funkcji i co powoduje ich zmianę.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 1d2cf34ee4712705eaa1c0da5ad63712f9e649fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652469"
---
# <a name="ip-addresses-in-azure-functions"></a>Adresy IP w Azure Functions

W tym artykule opisano następujące tematy dotyczące adresów IP aplikacji funkcji:

* Jak znaleźć adresy IP aktualnie używane przez aplikację funkcji.
* Co powoduje zmianę adresów IP aplikacji funkcji.
* Jak ograniczyć adresy IP, które mogą uzyskiwać dostęp do aplikacji funkcji.
* Jak uzyskać dedykowane adresy IP dla aplikacji funkcji.

Adresy IP są skojarzone z aplikacjami funkcji, a nie z poszczególnymi funkcjami. Przychodzące żądania HTTP nie mogą używać przychodzącego adresu IP do wywoływania pojedynczych funkcji; muszą używać domyślnej nazwy domeny (functionappname.azurewebsites.net) lub niestandardowej nazwy domeny.

## <a name="function-app-inbound-ip-address"></a>Adres IP ruchu przychodzącego aplikacji funkcji

Każda aplikacja funkcji ma jeden adres IP ruchu przychodzącego. Aby znaleźć ten adres IP:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do aplikacji funkcji.
3. Wybierz pozycję **Funkcje platformy**.
4. Wybierz **Właściwości**, a adres IP ruchu przychodzącego jest wyświetlany w obszarze **wirtualny adres IP**.

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
> W przypadku skalowania aplikacji funkcji uruchamianej zgodnie z [planem zużycia](functions-scale.md#consumption-plan) lub [planu Premium](functions-scale.md#premium-plan) można przypisać nowy zakres wychodzących adresów IP. W przypadku korzystania z jednego z tych planów może być konieczne dodanie całego centrum danych do listy dozwolonych.

## <a name="data-center-outbound-ip-addresses"></a>Wychodzące adresy IP centrum danych

Jeśli musisz dodać wychodzące adresy IP używane przez aplikacje funkcji do listy dozwolonych, inna opcja polega na dodaniu centrum danych aplikacji funkcji (regionu platformy Azure) do listy dozwolonych. Można [pobrać plik JSON zawierający listę adresów IP dla wszystkich centrów danych platformy Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Następnie Znajdź fragment JSON dotyczący regionu, w którym działa aplikacja funkcji.

Na przykład jest to fragment kodu JSON Europa Zachodnia, który może wyglądać następująco:

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

Gdy aplikacja funkcji jest uruchamiana w [planie zużycia](functions-scale.md#consumption-plan) lub w [planie Premium](functions-scale.md#premium-plan), adres IP ruchu przychodzącego może ulec zmianie nawet wtedy, gdy nie wykonano żadnych akcji, takich jak [wymienione powyżej](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Zmiany wychodzącego adresu IP

Zestaw dostępnych wychodzących adresów IP dla aplikacji funkcji może ulec zmianie, gdy:

* Wykonaj wszelkie akcje, które mogą zmienić przychodzące adresy IP.
* Zmień warstwę cenową planu App Service. Lista wszystkich możliwych wychodzących adresów IP, które mogą być używane przez aplikację dla wszystkich warstw cenowych, znajduje się we `possibleOutboundIPAddresses` właściwości. Zobacz [Znajdź wychodzące adresy IP](#find-outbound-ip-addresses).

Gdy aplikacja funkcji jest uruchamiana w [planie zużycia](functions-scale.md#consumption-plan) lub w [planie Premium](functions-scale.md#premium-plan), wychodzący adres IP może ulec zmianie nawet wtedy, gdy nie wykonano żadnych akcji, takich jak [wymienione powyżej](#inbound-ip-address-changes).

Aby celowo wymusić zmianę wychodzącego adresu IP:

1. Skaluj plan App Service w górę lub w dół między warstwami cenowymi Standard i Premium w wersji 2.
2. Odczekaj 10 minut.
3. Skaluj z powrotem do miejsca, w którym rozpoczęto pracę.

## <a name="ip-address-restrictions"></a>ograniczenia adresów IP

Można skonfigurować listę adresów IP, dla których chcesz zezwolić na dostęp lub odmówić dostępu do aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [Azure App Service ograniczeń statycznych adresów IP](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedykowane adresy IP

Jeśli potrzebujesz statycznych, dedykowanych adresów IP, zalecamy [App Service środowiska](../app-service/environment/intro.md) ( [izolowana warstwa](https://azure.microsoft.com/pricing/details/app-service/) planów App Service). Aby uzyskać więcej informacji, zobacz [App Service Environment adresy IP](../app-service/environment/network-info.md#ase-ip-addresses) i [sterowanie ruchem przychodzącym do App Service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

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
