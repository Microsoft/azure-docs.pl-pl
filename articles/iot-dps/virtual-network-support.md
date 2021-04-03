---
title: Obsługa usługi Azure IoT Device Provisioning Service (DPS) dla sieci wirtualnych
description: Jak korzystać ze wzorca łączności z sieciami wirtualnymi w usłudze Azure IoT Device Provisioning Service (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94959785"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Obsługa usługi Azure IoT Hub Device Provisioning Service (DPS) dla sieci wirtualnych

W tym artykule wprowadzono wzorzec łączności sieci wirtualnej (VNET) na potrzeby aprowizacji urządzeń IoT za pomocą centrów IoT przy użyciu usługi DPS. Ten wzorzec zapewnia prywatną łączność między urządzeniami, usługą DPS i centrum IoT w ramach sieci wirtualnej platformy Azure należącej do klienta. 

W większości przypadków, w których usługa DPS jest konfigurowana z siecią wirtualną, IoT Hub również zostanie skonfigurowana w tej samej sieci wirtualnej. Aby uzyskać bardziej szczegółowe informacje na temat obsługi sieci wirtualnej i konfiguracji dla centrów IoT, zobacz [IoT Hub obsłudze usługi Virtual Network](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Wprowadzenie

Domyślnie nazwy hostów DPS są mapowane na publiczny punkt końcowy z publicznie rutowanym adresem IP przez Internet. Ten publiczny punkt końcowy jest widoczny dla wszystkich klientów. Dostęp do publicznego punktu końcowego może być podejmowany przez urządzenia IoT za pośrednictwem sieci rozległych, a także sieci lokalnych.

Z kilku powodów klienci mogą chcieć ograniczyć łączność z zasobami platformy Azure, takimi jak DPS. Oto następujące przyczyny:

* Zapobiegaj zagrożeniom połączenia przez publiczny Internet. Ekspozycja można zmniejszyć, wprowadzając dodatkowe warstwy zabezpieczeń za pośrednictwem izolacji poziomu sieci dla zasobów Centrum IoT i usługi DPS

* Umożliwienie łączności prywatnej z zasobów sieci lokalnej dzięki zapewnieniu, że dane i ruch są przekazywane bezpośrednio do sieci szkieletowej platformy Azure.

* Zapobieganie atakom eksfiltracji z poufnych sieci lokalnych. 

* Następujące wzorce łączności dla całej platformy Azure korzystające z [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md).

Typowe podejścia do ograniczania łączności obejmują [reguły filtru IP usługi DPS](./iot-dps-ip-filtering.md) i sieci wirtualne (VNET) z [prywatnymi punktami końcowymi](../private-link/private-endpoint-overview.md). Celem tego artykułu jest opisanie podejścia sieci wirtualnej dla usługi DPS przy użyciu prywatnych punktów końcowych. 

Urządzenia, które działają w sieciach lokalnych, mogą używać [wirtualnej sieci prywatnej (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub prywatnej komunikacji równorzędnej [ExpressRoute](https://azure.microsoft.com/services/expressroute/) do nawiązywania połączenia z siecią wirtualną na platformie Azure i uzyskiwać dostęp do zasobów usługi DPS za pośrednictwem prywatnych punktów końcowych. 

Prywatny punkt końcowy jest prywatnym adresem IP przydzielonym wewnątrz sieci wirtualnej należącej do klienta, za pomocą której jest dostępny zasób platformy Azure. Mając prywatny punkt końcowy dla zasobu usługi DPS, można zezwolić urządzeniom działającym w sieci wirtualnej na żądanie aprowizacji przez zasób usługi DPS bez zezwalania na ruch do publicznego punktu końcowego.


## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że spełniono następujące wymagania wstępne:

* Zasób DPS został już utworzony i połączony z centrami IoT. Aby uzyskać wskazówki dotyczące konfigurowania nowego zasobu usługi DPS, zobacz, [konfigurowanie IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md)

* Masz zainicjowaną sieć wirtualną platformy Azure z podsiecią, w której zostanie utworzony prywatny punkt końcowy. Aby uzyskać więcej informacji, zobacz temat [Tworzenie sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-network/quick-create-cli.md).

* W przypadku urządzeń, które działają w sieciach lokalnych, skonfiguruj [wirtualną sieć prywatną (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub prywatną komunikację równorzędną [EXPRESSROUTE](https://azure.microsoft.com/services/expressroute/) w sieci wirtualnej platformy Azure.

## <a name="private-endpoint-limitations"></a>Ograniczenia prywatnego punktu końcowego

Należy zwrócić uwagę na następujące bieżące ograniczenia dotyczące usługi DPS podczas korzystania z prywatnych punktów końcowych:

* Prywatne punkty końcowe nie będą działały z usługą DPS, gdy zasób DPS i połączone centrum znajdują się w różnych chmurach. Na przykład [Azure Government i globalny platformę Azure](../azure-government/documentation-government-welcome.md).

* Obecnie [niestandardowe zasady alokacji z Azure Functionsem](how-to-use-custom-allocation-policies.md) usługi DPS nie będą działać, gdy funkcja platformy Azure zostanie zablokowana w sieci wirtualnej i prywatnych punktach końcowych. 

* Bieżąca obsługa sieci wirtualnych usługi DPS dotyczy tylko danych przychodzących do usługi DPS. Dane wychodzące, które są ruchem z usługi DPS do IoT Hub, korzystają z wewnętrznego mechanizmu usługa-usługa, a nie dedykowanej sieci wirtualnej. Obsługa pełnej, opartej na sieci wirtualnej blokady ruchu wychodzącego między działem DPS a IoT Hub nie jest obecnie dostępna.

* Zasady alokacji najniższych opóźnień są używane do przypisywania urządzenia do centrum IoT Hub z najniższym opóźnieniem. Te zasady alokacji nie są niezawodne w środowisku sieci wirtualnej. 

## <a name="set-up-a-private-endpoint"></a>Konfigurowanie prywatnego punktu końcowego

Aby skonfigurować prywatny punkt końcowy, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com/)Otwórz zasób DPS i kliknij kartę **Sieć** . Kliknij pozycję **połączenia prywatne punktów końcowych** i **+ prywatny punkt końcowy**.

    ![Dodawanie nowego prywatnego punktu końcowego dla usługi DPS](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. Na stronie _Tworzenie podstawy prywatnego punktu końcowego_ wprowadź informacje wymienione w poniższej tabeli.

    ![Podstawowe informacje o tworzeniu prywatnych punktów końcowych](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Pole | Wartość |
    | :---- | :-----|
    | **Subskrypcja** | Wybierz żądaną subskrypcję platformy Azure, która będzie zawierać prywatny punkt końcowy.  |
    | **Grupa zasobów** | Wybierz lub Utwórz grupę zasobów zawierającą prywatny punkt końcowy |
    | **Nazwa**       | Wprowadź nazwę prywatnego punktu końcowego |
    | **Region**     | Wybrany region musi być taki sam jak region, w którym znajduje się sieć wirtualna, ale nie musi być taki sam jak zasób DPS. |

    Kliknij przycisk **Dalej: zasób** , aby skonfigurować zasób, do którego będzie wskazywał prywatny punkt końcowy.

3. Na stronie _Utwórz zasób prywatnego punktu końcowego_ wprowadź informacje wymienione w poniższej tabeli.

    ![Utwórz zasób prywatnego punktu końcowego](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Pole | Wartość |
    | :---- | :-----|
    | **Subskrypcja**        | Wybierz subskrypcję platformy Azure zawierającą zasób usługi DPS, do którego będzie wskazywał prywatny punkt końcowy.  |
    | **Typ zasobu**       | Wybierz pozycję **Microsoft. Devices/ProvisioningServices**. |
    | **Zasób**            | Wybierz zasób DPS, na który będzie mapowany prywatny punkt końcowy. |
    | **Docelowy zasób podrzędny** | Wybierz pozycję **iotDps**. |

    > [!TIP]
    > Informacje dotyczące ustawienia **Połącz z zasobem platformy Azure według identyfikatora zasobu lub aliasu** znajdują się w sekcji [Żądaj prywatnego punktu końcowego](#request-a-private-endpoint) w tym artykule.


    Kliknij przycisk **Dalej: Konfiguracja** , aby skonfigurować sieć wirtualną dla prywatnego punktu końcowego.

4. Na stronie _Tworzenie konfiguracji prywatnego punktu końcowego_ wybierz sieć wirtualną i podsieć, w której ma zostać utworzony prywatny punkt końcowy.
 
    Kliknij przycisk **Dalej: Tagi** i opcjonalnie Podaj wszystkie znaczniki dla zasobu.

    ![Skonfiguruj prywatny punkt końcowy](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz** , aby utworzyć prywatny zasób punktu końcowego.


## <a name="request-a-private-endpoint"></a>Żądaj prywatnego punktu końcowego

Możesz zażądać prywatnego punktu końcowego do zasobu DPS według identyfikatora zasobu. Aby to żądanie było wymagane, wymagany jest właściciel zasobu. 

1. Identyfikator zasobu znajduje się na karcie Właściwości zasobu DPS, jak pokazano poniżej.

    ![Karta właściwości DPS](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Należy pamiętać, że identyfikator zasobu zawiera identyfikator subskrypcji. 

2. Po utworzeniu identyfikatora zasobu wykonaj kroki opisane powyżej w temacie [Konfigurowanie prywatnego punktu końcowego](#set-up-a-private-endpoint) do kroku 3 na stronie _Tworzenie zasobu prywatnego punktu końcowego_ . Kliknij pozycję **Połącz z zasobem platformy Azure według identyfikatora zasobu lub aliasu** , a następnie wprowadź informacje w poniższej tabeli. 

    | Pole | Wartość |
    | :---- | :-----|
    | **Identyfikator zasobu lub alias** | Wprowadź identyfikator zasobu dla zasobu DPS. |
    | **Docelowy zasób podrzędny** | Wprowadź **iotDps** |
    | **Komunikat żądania** | Wprowadź komunikat żądania dla właściciela zasobu usługi DPS.<br>Na przykład <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Kliknij przycisk **Dalej: Konfiguracja** , aby skonfigurować sieć wirtualną dla prywatnego punktu końcowego.

3. Na stronie _Tworzenie konfiguracji prywatnego punktu końcowego_ wybierz sieć wirtualną i podsieć, w której ma zostać utworzony prywatny punkt końcowy.
 
    Kliknij przycisk **Dalej: Tagi** i opcjonalnie Podaj wszystkie znaczniki dla zasobu.

4. Kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz** , aby utworzyć prywatne żądanie punktu końcowego.

5. Właściciel DPS będzie widział żądanie prywatnego punktu końcowego na liście **połączeń prywatnych punktów końcowych** na karcie Sieć DPS. Na tej stronie właściciel może **zatwierdzić** lub **odrzucić** prywatne żądanie punktu końcowego, jak pokazano poniżej.

    ![Zatwierdzenie DPS](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Cennik — punkty końcowe

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o funkcjach zabezpieczeń DPS:

* [Bezpieczeństwo](./concepts-service.md#attestation-mechanism)
* [Obsługa protokołu TLS 1,2](tls-support.md)