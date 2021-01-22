---
title: Używanie prywatnych punktów końcowych
titleSuffix: Azure SignalR Service
description: Przegląd prywatnych punktów końcowych w celu bezpiecznego dostępu do usługi Azure Signal Service z sieci wirtualnych.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682634"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Używanie prywatnych punktów końcowych dla usługi Azure Signal Service

Możesz użyć [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) dla usługi Azure Signal Service, aby umożliwić klientom w sieci wirtualnej (VNET) bezpieczne uzyskiwanie dostępu do danych za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi Azure Sygnalizacyjnyej. Ruch sieciowy między klientami w sieci wirtualnej i usłudze Azure Signal Service przechodzi przez prywatny link do internetowej firmy Microsoft.

Używanie prywatnych punktów końcowych usługi Azure Signal Service pozwala:

- Zabezpiecz swoją usługę Azure Signal przy użyciu kontroli dostępu do sieci, aby blokować wszystkie połączenia w publicznym punkcie końcowym usługi Azure Signal Service.
- Zwiększ bezpieczeństwo sieci wirtualnej (VNet), umożliwiając zablokowanie eksfiltracji danych w wirtualnej.
- Bezpiecznie łącz się z usługami Azure sygnalizacyjnymi z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

## <a name="conceptual-overview"></a>Omówienie pojęć

![Przegląd prywatnych punktów końcowych usługi Azure Signal Service](media/howto-private-endpoints/private-endpoint-overview.png)

Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla usługi platformy Azure w [Virtual Network](../virtual-network/virtual-networks-overview.md) (VNET). Po utworzeniu prywatnego punktu końcowego usługi Azure Signal Agent zapewnia bezpieczną łączność między klientami w sieci wirtualnej a usługą. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym i usługą Azure Signal Service używa bezpiecznego linku prywatnego.

Aplikacje w sieci wirtualnej mogą bezproblemowo łączyć się z usługą Azure sygnalizacyjnej za pośrednictwem prywatnego punktu końcowego, **używając tych samych parametrów połączenia i mechanizmów autoryzacji, które byłyby w innym przypadku używane**. Prywatnych punktów końcowych można używać ze wszystkimi protokołami obsługiwanymi przez usługę Azure Signal, włącznie z interfejsem API REST.

Po utworzeniu prywatnego punktu końcowego w usłudze Azure Signal Service w sieci wirtualnej jest wysyłane żądanie zgody dla właściciela usługi Azure sygnalizującego. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem usługi Azure Signal, to żądanie zgody jest automatycznie zatwierdzane.

Właściciele usługi Azure sygnalizujący mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi, korzystając z karty "*prywatne punkty końcowe*" dla usługi Azure signal w [Azure Portal](https://portal.azure.com).

> [!TIP]
> Jeśli chcesz ograniczyć dostęp do usługi Azure sygnalizacyjnej tylko za pomocą prywatnego punktu końcowego, [skonfiguruj Access Control sieci](howto-network-access-control.md#managing-network-access-control) , aby odmówić lub kontrolować dostęp za pomocą publicznego punktu końcowego.

### <a name="connecting-to-private-endpoints"></a>Nawiązywanie połączenia z prywatnymi punktami końcowymi

Klienci w sieci wirtualnej korzystającej z prywatnego punktu końcowego powinni używać tych samych parametrów połączenia dla usługi Azure Signal Service, ponieważ klienci nawiązują połączenie z publicznym punktem końcowym. Firma Microsoft korzysta z rozpoznawania nazw DNS, aby automatycznie kierować połączenia z sieci wirtualnej do usługi Azure Signal Service za pośrednictwem prywatnego linku.

> [!IMPORTANT]
> Użyj tych samych parametrów połączenia, aby nawiązać połączenie z usługą Azure sygnalizująca przy użyciu prywatnych punktów końcowych, jak w przeciwnym razie. Nie łącz się z usługą Azure Signal Service przy użyciu `privatelink` adresu URL jego domeny podrzędnej.

Utworzymy [prywatną strefę DNS](../dns/private-dns-overview.md) dołączoną do sieci wirtualnej z domyślnymi aktualizacjami dla prywatnych punktów końcowych. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS. W sekcji dotyczącej [zmian w systemie DNS](#dns-changes-for-private-endpoints) poniżej opisano aktualizacje wymagane dla prywatnych punktów końcowych.

## <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych

Podczas tworzenia prywatnego punktu końcowego rekord zasobu CNAME DNS dla usługi Azure Signaler zostanie zaktualizowany do aliasu w poddomenie z prefiksem `privatelink` . Domyślnie tworzymy również [prywatną strefę DNS](../dns/private-dns-overview.md)odpowiadającą `privatelink` poddomeną, z rekordem zasobów DNS a dla prywatnych punktów końcowych.

Po rozwiązaniu nazwy domeny usługi Azure sygnalizującej spoza sieci wirtualnej z prywatnym punktem końcowym jest on rozpoznawany jako publiczny punkt końcowy usługi Azure Signal Service. Po rozwiązaniu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, nazwa domeny jest rozpoznawana jako adres IP prywatnego punktu końcowego.

W przykładzie przedstawionym powyżej rekordy zasobów DNS dla usługi Azure Signal Service "Foobar", po rozwiązaniu spoza sieci wirtualnej obsługującej prywatny punkt końcowy, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

Jak wspomniano wcześniej, można odmówić lub kontrolować dostęp dla klientów spoza sieci wirtualnej za pośrednictwem publicznego punktu końcowego za pomocą kontroli dostępu do sieci.

Rekordy zasobów DNS dla "Foobar", po rozwiązaniu przez klienta w sieci wirtualnej hostującym prywatny punkt końcowy, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | klienta 10.1.1.5                                              |

Takie podejście umożliwia dostęp do usługi Azure Signal Service **przy użyciu tych samych parametrów połączenia** dla klientów w sieci wirtualnej, w których znajdują się prywatne punkty końcowe, a także klientów spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci muszą mieć możliwość rozpoznania nazwy FQDN dla punktu końcowego usługi Azure Signal Service do prywatnego adresu IP punktu końcowego. Należy skonfigurować serwer DNS w celu delegowania poddomeny prywatnego linku do prywatnej strefy DNS dla sieci wirtualnej lub skonfigurować rekordy A dla `foobar.privatelink.service.signalr.net` za pomocą prywatnego adresu IP punktu końcowego.

> [!TIP]
> W przypadku korzystania z niestandardowego lub lokalnego serwera DNS należy skonfigurować serwer DNS w celu rozpoznania nazwy usługi Azure Signal w `privatelink` poddomenie na adres IP prywatnego punktu końcowego. Można to zrobić przez delegowanie `privatelink` poddomeny do prywatnej strefy DNS sieci wirtualnej lub skonfigurowanie strefy DNS na serwerze DNS i dodanie rekordu A DNS a.

Zalecaną nazwą strefy DNS dla prywatnych punktów końcowych usługi Azure Signal to: `privatelink.service.signalr.net` .

Aby uzyskać więcej informacji na temat konfigurowania własnego serwera DNS do obsługi prywatnych punktów końcowych, zapoznaj się z następującymi artykułami:

- [Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Konfiguracja DNS dla prywatnych punktów końcowych](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Utwórz prywatny punkt końcowy wraz z nową usługą Azure Signal w Azure Portal

1. Podczas tworzenia nowej usługi Azure sygnalizującej wybierz pozycję Karta **sieciowa** . Wybierz **prywatny punkt końcowy** jako metodę łączności.

    ![Tworzenie usługi Azure Signal Service — Karta Sieć](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Kliknij pozycję **Dodaj**. Wypełnij pola subskrypcja, Grupa zasobów, lokalizacja i nazwa nowego prywatnego punktu końcowego. Wybierz sieć wirtualną i podsieć.

    ![Tworzenie usługi Azure Signal Service — Dodawanie prywatnego punktu końcowego](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Kliknij pozycję **Przejrzyj i utwórz**.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Utwórz prywatny punkt końcowy dla istniejącej usługi Azure Signal w Azure Portal

1. Przejdź do usługi Azure Signal Service.

1. Kliknij menu Ustawienia o nazwie **połączenia prywatnego punktu końcowego**.

1. Kliknij przycisk **+ prywatny punkt końcowy** w górnej części strony.

    ![Blok połączeń prywatnych punktów końcowych](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Wypełnij pola subskrypcja, Grupa zasobów, nazwa zasobu i region dla nowego prywatnego punktu końcowego.
    
    ![Tworzenie prywatnego punktu końcowego — podstawy](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Wybierz docelowy zasób usługi sygnałów platformy Azure.

    ![Tworzenie prywatnego punktu końcowego — zasób](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Wybierz docelową sieć wirtualną

    ![Tworzenie prywatnego punktu końcowego — konfiguracja](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Kliknij pozycję **Przejrzyj i utwórz**.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure
    ```azurecli
    az login
    ```
1. Wybierz swoją subskrypcję platformy Azure
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Tworzenie nowej grupy zasobów
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Zarejestruj Microsoft. SignalRService jako dostawcę
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. Tworzenie nowej usługi Azure sygnalizującej
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Tworzenie sieci wirtualnej
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Dodawanie podsieci
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Wyłącz zasady Virtual Network
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Dodawanie strefy Prywatna strefa DNS
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Połącz strefę Prywatna strefa DNS z Virtual Network
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Tworzenie prywatnego punktu końcowego (automatyczne zatwierdzanie)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Tworzenie prywatnego punktu końcowego (żądanie zatwierdzenia ręcznie)
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Pokaż stan połączenia
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Znane problemy

Należy wziąć pod uwagę następujące znane problemy dotyczące prywatnych punktów końcowych usługi Azure Signal Service

### <a name="free-tier"></a>Warstwa Bezpłatna

Nie można utworzyć żadnych prywatnych punktów końcowych dla usługi Azure sygnalizującej dla warstwy Bezpłatna.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Ograniczenia dostępu dla klientów w sieci wirtualnych z prywatnymi punktami końcowymi

Klienci w programie sieci wirtualnych z istniejącymi prywatnymi punktami końcowymi ograniczeniami podczas uzyskiwania dostępu do innych wystąpień usługi Azure sygnalizujących, które mają prywatne punkty końcowe. Na przykład załóżmy, że sieć wirtualna N1 ma prywatny punkt końcowy wystąpienia usługi Azure Signal instance. Jeśli usługa sygnału Azure S2 ma prywatny punkt końcowy w sieci wirtualnej N2, wówczas klienci w sieci wirtualnej N1 muszą również uzyskać dostęp do usługi Azure Signal Service S2 przy użyciu prywatnego punktu końcowego. Jeśli usługa sygnałów platformy Azure S2 nie ma żadnych prywatnych punktów końcowych, wówczas klienci w sieci wirtualnej N1 mogą uzyskać dostęp do usługi Azure sygnalizującej na tym koncie bez prywatnego punktu końcowego.

To ograniczenie jest wynikiem zmian wprowadzonych w systemie DNS, gdy usługa Azure Signaling Service w sieci S2 tworzy prywatny punkt końcowy.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Reguły sieciowej grupy zabezpieczeń dla podsieci z prywatnymi punktami końcowymi

Obecnie nie można skonfigurować zasad [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md) (sieciowej grupy zabezpieczeń) i tras zdefiniowanych przez użytkownika dla prywatnych punktów końcowych. Reguły sieciowej grupy zabezpieczeń stosowane do podsieci obsługującej prywatny punkt końcowy są stosowane do prywatnego punktu końcowego. Ograniczone obejście tego problemu polega na implementacji reguł dostępu dla prywatnych punktów końcowych w podsieciach źródłowych, chociaż takie podejście może wymagać wyższego obciążenia zarządzania.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie Access Control sieci](howto-network-access-control.md)