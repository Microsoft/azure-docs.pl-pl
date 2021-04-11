---
title: Włącz prywatny dostęp za pomocą prywatnego linku (wersja zapoznawcza) — interfejs wiersza polecenia
titleSuffix: Azure Digital Twins
description: Zapoznaj się z tematem Włączanie prywatnego dostępu do rozwiązań Azure Digital bliźniaczych reprezentacji z linkiem prywatnym przy użyciu interfejsu wiersza polecenia platformy Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4dab08983fc1348ca49e728a65d48aa65fe19a47
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105718"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Włącz prywatny dostęp za pomocą linku prywatnego (wersja zapoznawcza): interfejs wiersza polecenia platformy Azure

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

W tym artykule opisano różne sposoby [włączania prywatnego linku do prywatnego punktu końcowego dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji](concepts-security.md#private-network-access-with-azure-private-link-preview) (obecnie dostępne w wersji zapoznawczej). Skonfigurowanie prywatnego punktu końcowego dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji umożliwia zabezpieczenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji i wyeliminowanie opinii publicznej, a także uniknięcie eksfiltracji danych z [usługi azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

W tym artykule omówiono proces przy użyciu [**interfejsu wiersza polecenia platformy Azure**](/cli/azure/what-is-azure-cli).

Poniżej przedstawiono kroki omówione w tym artykule: 
1. Włącz link prywatny i skonfiguruj prywatny punkt końcowy dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
1. Wyłącz lub Włącz flagi dostępu do sieci publicznej, aby ograniczyć dostęp API tylko do połączeń prywatnych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było skonfigurować prywatny punkt końcowy, będzie potrzebna [**usługa Azure Virtual Network (VNET)**](../virtual-network/virtual-networks-overview.md) , w której można wdrożyć punkt końcowy. Jeśli nie masz jeszcze sieci wirtualnej, możesz skorzystać z jednego z [przewodników szybki start](../virtual-network/quick-create-portal.md) dla systemu Virtual Network Azure, aby go skonfigurować.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Zarządzanie prywatnymi punktami końcowymi wystąpienia usługi Azure Digital bliźniaczych reprezentacji 

Korzystając z [interfejsu wiersza polecenia platformy Azure](/cli/azure/what-is-azure-cli), można skonfigurować prywatne punkty końcowe z prywatnym linkiem w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, które już istnieje (nie można go dodać jako części tworzenia wystąpienia). Następnie można nadal wyświetlać i zarządzać nimi przy użyciu dodatkowych poleceń interfejsu wiersza polecenia. 

>[!TIP]
> Możesz również skonfigurować prywatny punkt końcowy łącza za pomocą usługi link prywatny, a nie za pomocą swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Zapewnia to również te same opcje konfiguracji i ten sam wynik końcowy.
>
> Aby uzyskać więcej informacji na temat konfigurowania prywatnych zasobów łączy, zobacz dokumentację linku prywatnego dla [Azure Portal](../private-link/create-private-endpoint-portal.md), [interfejsu wiersza polecenia platformy Azure](../private-link/create-private-endpoint-cli.md), [szablonów ARM](../private-link/create-private-endpoint-template.md)lub [programu PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Dodaj prywatny punkt końcowy do istniejącego wystąpienia

Aby utworzyć prywatny punkt końcowy i połączyć go z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, użyj polecenia [**AZ Network Private-Endpoint Create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) . Zidentyfikuj wystąpienie usługi Azure Digital bliźniaczych reprezentacji przy użyciu jego w pełni kwalifikowanego identyfikatora w `--private-connection-resource-id` parametrze.

Oto przykład, który używa polecenia, aby utworzyć prywatny punkt końcowy z tylko wymaganymi parametrami.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Aby uzyskać pełną listę wymaganych i opcjonalnych parametrów, a także więcej przykładów tworzenia prywatnych punktów końcowych, zobacz [ **AZ Network Private-Endpoint Create** Reference dokumentacja](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Zarządzanie połączeniami prywatnego punktu końcowego na tym wystąpieniu

Po utworzeniu prywatnego punktu końcowego dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji można użyć poleceń [**AZ DT Network Private-Endpoint Connection**](/cli/azure/dt/network/private-endpoint/connection) , aby kontynuować zarządzanie **połączeniami** prywatnego punktu końcowego w odniesieniu do wystąpienia. Operacje obejmują:
* Pokaż połączenie prywatnego punktu końcowego
* Ustawianie stanu połączenia prywatnego — punkt końcowy
* Usuwanie połączenia prywatnego — punkt końcowy
* Wyświetl listę wszystkich połączeń prywatnych punktów końcowych dla wystąpienia

Więcej informacji i przykładów można znaleźć w dokumentacji usługi [ **AZ DT Network Private-Endpoint** Reference](/cli/azure/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Zarządzanie innymi informacjami o linku prywatnym w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji

Dodatkowe informacje o stanie łącza prywatnego wystąpienia można uzyskać za pomocą polecenia [**AZ DT Network Private-link**](/cli/azure/dt/network/private-link) . Operacje obejmują:
* Wyświetlanie listy prywatnych linków skojarzonych z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji
* Pokaż prywatne łącze skojarzone z wystąpieniem

Więcej informacji i przykładów można znaleźć w dokumentacji usługi [ **AZ DT Network Private-link** Reference](/cli/azure/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Wyłącz/Włącz flagi dostępu do sieci publicznej

Można skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji, aby odmówić wszystkich połączeń publicznych i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci. Ta akcja jest wykonywana z **flagą dostępu do sieci publicznej**. 

Te zasady umożliwiają ograniczenie dostępu do interfejsu API tylko do połączeń prywatnych. Gdy flaga dostępu do sieci publicznej jest ustawiona na *wyłączone*, wszystkie wywołania interfejsu API REST do płaszczyzny danych wystąpienia bliźniaczych reprezentacji cyfrowych platformy Azure z chmury publicznej zostaną zwrócone `403, Unauthorized` . Alternatywnie, jeśli zasady są ustawione na *wyłączone* , a żądanie jest nawiązywane za pomocą prywatnego punktu końcowego, wywołanie interfejsu API powiedzie się.

W tym artykule pokazano, jak zaktualizować wartość flagi sieci przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/) lub [Narzędzia ARMClient](https://github.com/projectkudu/ARMClient). Aby uzyskać instrukcje, jak to zrobić za pomocą Azure Portal, zobacz [wersję portalu](how-to-enable-private-link-portal.md) w tym artykule.

### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure można wyłączyć lub włączyć dostęp do sieci publicznej, dodając `--public-network-access` do niego parametr `az dt create` . Chociaż to polecenie może również służyć do tworzenia nowego wystąpienia, można użyć go do edycji właściwości istniejącego wystąpienia, podając nazwę wystąpienia, które już istnieje. (Aby uzyskać więcej informacji na temat tego polecenia, zobacz jego [dokumentację referencyjną](/cli/azure/dt#az_dt_create) lub [Ogólne instrukcje dotyczące konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Aby **wyłączyć** dostęp do sieci publicznej dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji, użyj `--public-network-access` parametru w następujący sposób:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Aby **włączyć** dostęp do sieci publicznej w wystąpieniu, w którym jest ono obecnie wyłączone, użyj następującego podobnego polecenia:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Korzystanie z narzędzia wiersza polecenia ARMClient 

Za pomocą [narzędzia wiersza polecenia ARMClient](https://github.com/projectkudu/ARMClient)dostęp do sieci publicznej jest włączany lub wyłączany przy użyciu poniższych poleceń. 

Aby **wyłączyć** dostęp do sieci publicznej:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Aby **włączyć** dostęp do sieci publicznej:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat prywatnego linku dla platformy Azure: 
* [*Co to jest usługa Azure Private Link Service?*](../private-link/private-link-service-overview.md)