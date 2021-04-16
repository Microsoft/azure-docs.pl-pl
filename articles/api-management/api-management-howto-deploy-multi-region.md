---
title: Wdrażanie usług Azure API Management w wielu regionach świadczenia usługi Azure
titleSuffix: Azure API Management
description: Dowiedz się, jak wdrożyć wystąpienie usługi Azure API Management Service w wielu regionach platformy Azure.
author: mikebudzynski
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 9546813173e72b1f264c3668ee889bbeea07ce7f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534090"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure

Usługa Azure API Management obsługuje wdrażanie w wielu regionach, co umożliwia wydawcom interfejsów API dystrybucję pojedynczej usługi Azure API Management w dowolnej liczbie obsługiwanych regionów świadczenia usługi Azure. Funkcja obsługi wielu regionów pomaga zmniejszyć opóźnienie żądań odbierane przez geograficznie rozproszonych odbiorców interfejsu API i zwiększa dostępność usługi, jeśli jeden region przejdzie w tryb offline.

Nowa usługa Azure API Management początkowo zawiera tylko jedną jednostkę [w][unit] jednym regionie świadczenia usługi Azure , regionie podstawowym. Dodatkowe jednostki można dodać do regionów podstawowych lub pomocniczych. Składnik API Management jest wdrażany w każdym wybranym regionie podstawowym i pomocniczym. Przychodzące żądania interfejsu API są automatycznie kierowane do najbliższego regionu. Jeśli region przejdzie w tryb offline, żądania interfejsu API będą automatycznie kierowane wokół regionu, który zakończył się niepowodzeniem, do następnej najbliższej bramy.

> [!NOTE]
> Tylko składnik bramy usługi API Management wdrożony we wszystkich regionach. Składnik zarządzania usługami i portal dla deweloperów są hostowane tylko w regionie podstawowym. W związku z tym w przypadku wyłączenie regionu podstawowego dostęp do portalu dla deweloperów i możliwość zmiany konfiguracji (np. dodania interfejsów API, zastosowania zasad) będzie ograniczona do momentu powrotu regionu podstawowego do trybu online. Gdy region podstawowy jest w trybie offline, dostępne regiony pomocnicze będą nadal obsługiwać ruch interfejsu API przy użyciu najnowszej dostępnej konfiguracji. Opcjonalnie włącz [redundacy](zone-redundancy.md) strefy, aby zwiększyć dostępność i odporność regionów podstawowych lub pomocniczych.

>[!IMPORTANT]
> Funkcja, która umożliwia przechowywanie danych klientów w jednym regionie, jest obecnie dostępna tylko w regionie Azja Południowo-Wschodnia (Singapur) Azja i Pacyfik Geograficznego. W przypadku wszystkich innych regionów dane klientów są przechowywane w lokalizacji geograficznej.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli jeszcze nie utworzono wystąpienia usługi API Management, zobacz [Tworzenie API Management usługi](get-started-create-service-instance.md). Wybierz warstwę usługi Premium.
* Jeśli twoje API Management jest wdrożone w sieci [wirtualnej,](api-management-using-with-vnet.md)upewnij się, że sieć wirtualna, podsieć i publiczny adres IP zostały ustawione w lokalizacji, która ma zostać do dodania.

## <a name="deploy-api-management-service-to-an-additional-location"></a><a name="add-region"> </a>Wdrażanie API Management usługi w dodatkowej lokalizacji

1. W Azure Portal przejdź do usługi API Management i wybierz **pozycję Lokalizacje** w menu.
1. Wybierz **pozycję + Dodaj** na górnym pasku.
1. Wybierz lokalizację z listy rozwijanej.
1. Wybierz liczbę jednostek **[skalowania](upgrade-and-scale.md)** w lokalizacji.
1. Opcjonalnie włącz [**strefy dostępności.**](zone-redundancy.md)
1. Jeśli wystąpienie API Management jest wdrożone w sieci [wirtualnej,](api-management-using-with-vnet.md)skonfiguruj ustawienia sieci wirtualnej w tej lokalizacji. Wybierz istniejącą sieć wirtualną, podsieć i publiczny adres IP, które są dostępne w lokalizacji.
1. Wybierz **pozycję Dodaj,** aby potwierdzić.
1. Powtarzaj ten proces do momentu skonfigurowania wszystkich lokalizacji.
1. Wybierz **pozycję** Zapisz na górnym pasku, aby rozpocząć proces wdrażania.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Usuwanie API Management usługi

1. W Azure Portal przejdź do usługi API Management i kliknij pozycję **Lokalizacje** w menu.
2. W przypadku lokalizacji, która ma być usuwana, otwórz menu kontekstowe przy użyciu przycisku **...** na prawym końcu tabeli. Wybierz opcję **Usuń.**
3. Potwierdź usunięcie i kliknij **przycisk Zapisz,** aby zastosować zmiany.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Przekieruj wywołania interfejsu API do regionalnych usług zaplecza

Usługa Azure API Management zawiera tylko jeden adres URL usługi zaplecza. Mimo że istnieją wystąpienia usługi Azure API Management w różnych regionach, brama interfejsu API nadal będzie przesyłać żądania do tej samej usługi zaplecza, która jest wdrażana tylko w jednym regionie. W takim przypadku wzrost wydajności będzie pochodzić tylko z odpowiedzi buforowanych w usłudze Azure API Management w regionie specyficznym dla żądania, ale kontaktowanie się z zapleczami na całym świecie może nadal powodować duże opóźnienie.

Aby w pełni wykorzystać geograficzną dystrybucję systemu, usługi zaplecza powinny być wdrożone w tych samych regionach co usługi Azure API Management wystąpienia. Następnie przy użyciu zasad `@(context.Deployment.Region)` i właściwości można przekierowyć ruch do lokalnych wystąpień zaplecza.

1. Przejdź do wystąpienia usługi Azure API Management i kliknij pozycję **Interfejsy API** w menu po lewej stronie.
2. Wybierz żądany interfejs API.
3. Kliknij **pozycję Edytor kodu** na liście rozwijanej strzałki w obszarze Przychodzące **przetwarzanie**.

    ![Edytor kodu interfejsu API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Użyj instrukcji `set-backend` połączonej z `choose` zasadami warunkowym, aby utworzyć odpowiednie zasady rozsyłania w sekcji `<inbound> </inbound>` pliku.

    Na przykład poniższy plik XML będzie działać w regionach Zachodnie stany USA i Azja Wschodnia USA:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Możesz również korzystać z usług zaplecza za pomocą usługi [Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)kierować wywołania interfejsu API do interfejsu Traffic Manager i pozwolić na automatyczne rozwiązywanie problemów z routingiem.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Używanie routingu niestandardowego do API Management bram regionalnych

API Management kieruje żądania do bramy _regionalnej_ w oparciu o [najmniejsze opóźnienie](../traffic-manager/traffic-manager-routing-methods.md#performance). Chociaż nie jest możliwe zastąpienie tego ustawienia w programie API Management, można użyć własnej Traffic Manager z niestandardowymi regułami routingu.

1. Utwórz własny [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Jeśli używasz domeny [niestandardowej,](../traffic-manager/traffic-manager-point-internet-domain.md) użyj jej z Traffic Manager zamiast domeny API Management usługi.
1. [Skonfiguruj API Management regionalne punkty końcowe w Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Regionalne punkty końcowe są zgodne ze wzorcem adresu URL `https://<service-name>-<region>-01.regional.azure-api.net` , na przykład `https://contoso-westus2-01.regional.azure-api.net` .
1. [Skonfiguruj punkty końcowe API Management stan regionalny w programie Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Regionalne punkty końcowe stanu są zgodne ze wzorcem adresu URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` , na przykład `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Określ [metodę routingu](../traffic-manager/traffic-manager-routing-methods.md) Traffic Manager.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
