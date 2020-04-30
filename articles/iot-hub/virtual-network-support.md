---
title: Obsługa IoT Hub platformy Azure dla sieci wirtualnych
description: Jak używać wzorca łączności sieci wirtualnych z IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: jlian
ms.openlocfilehash: c0d01ae6507864373a79282476846d6f96adf83b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231445"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Obsługa IoT Hub sieci wirtualnych

W tym artykule wprowadzono wzorzec łączności sieci wirtualnej i opisano sposób konfigurowania środowiska łączności prywatnej w usłudze IoT Hub za pośrednictwem sieci wirtualnej platformy Azure należącej do klienta.

> [!NOTE]
> Funkcje IoT Hub opisane w tym artykule są obecnie dostępne dla centrów IoT [utworzonych za pomocą tożsamości usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity) w następujących regionach: Wschodnie stany USA, Południowo-środkowe stany USA i zachodnie stany USA 2.


## <a name="introduction"></a>Wprowadzenie

Domyślnie nazwy hostów IoT Hub są mapowane na publiczny punkt końcowy z publicznie rutowanym adresem IP przez Internet. Jak pokazano na poniższej ilustracji, ten IoT Hub publiczny punkt końcowy jest współużytkowany między centrami należącymi do różnych klientów i mogą uzyskać dostęp do urządzeń IoT za pośrednictwem sieci rozległych, a także sieci lokalnych.

Kilka funkcji IoT Hub, takich jak [routing wiadomości](./iot-hub-devguide-messages-d2c.md), [przekazywanie plików](./iot-hub-devguide-file-upload.md)oraz [zbiorcze importowanie/eksportowanie urządzeń](./iot-hub-bulk-identity-mgmt.md) , również wymagają łączności z IoT Hub do zasobu platformy Azure należącego do klienta w jego publicznym punkcie końcowym. Jak pokazano poniżej, te ścieżki łączności zbiorczo stanowią ruch wychodzący z IoT Hub do zasobów klienta.
![IoT Hub publiczny punkt końcowy](./media/virtual-network-support/public-endpoint.png)


Z kilku powodów klienci mogą chcieć ograniczyć łączność ze swoimi zasobami platformy Azure (w tym IoT Hub) za pośrednictwem sieci wirtualnej, która jest ich własnością i działa. Oto następujące przyczyny:

* Wprowadzenie dodatkowych warstw zabezpieczeń przez izolację poziomu sieci dla Centrum IoT Hub przez zapobieganie zagrożeniu łączności z centrum za pośrednictwem publicznego Internetu.

* Umożliwienie łączności prywatnej z zasobów sieci lokalnej dzięki zapewnieniu, że dane i ruch są przekazywane bezpośrednio do sieci szkieletowej platformy Azure.

* Zapobieganie atakom eksfiltracji z poufnych sieci lokalnych. 

* Następujące wzorce łączności dla całej platformy Azure korzystające z [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md).


W tym artykule opisano, jak osiągnąć te cele przy użyciu [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) dla łączności przychodzącej do IoT Hub, jak przy użyciu wyjątku usługi Azure Trusted First Database w przypadku łączności wychodzącej z IoT Hub do innych zasobów platformy Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Połączenia przychodzące do IoT Hub za pomocą prywatnych punktów końcowych

Prywatny punkt końcowy jest prywatnym adresem IP przydzielonym wewnątrz sieci wirtualnej należącej do klienta, za pomocą którego jest dostępny zasób platformy Azure. Mając prywatny punkt końcowy dla Centrum IoT, można zezwolić usługom działającym w sieci wirtualnej na dostęp do IoT Hub bez konieczności wysyłania ruchu do publicznego punktu końcowego IoT Hub. Podobnie urządzenia działające w środowisku lokalnym mogą korzystać z [wirtualnej sieci prywatnej (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) lub prywatnej komunikacji równorzędnej [ExpressRoute](https://azure.microsoft.com/services/expressroute/) w celu uzyskania łączności z siecią wirtualną na platformie Azure, a następnie do IoT Hub (za pośrednictwem prywatnego punktu końcowego). W związku z tym klienci, którzy chcą ograniczyć łączność z publicznymi punktami końcowymi usługi IoT Hub (lub ewentualnie całkowicie ją zablokować), mogą osiągnąć ten cel, używając [reguł zapory IoT Hub](./iot-hub-ip-filtering.md) podczas zachowywania łączności z koncentratorem za pomocą prywatnego punktu końcowego.

> [!NOTE]
> Głównym fokusem tego Instalatora są urządzenia znajdujące się w sieci lokalnej. Ta konfiguracja nie jest zalecana w przypadku urządzeń wdrożonych w sieci rozległej.

![IoT Hub publiczny punkt końcowy](./media/virtual-network-support/virtual-network-ingress.png)

Przed kontynuowaniem upewnij się, że spełniono następujące wymagania wstępne:

* Usługa IoT Hub musi być obsługiwana przy użyciu [tożsamości usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity).

* Centrum IoT musi być inicjowane w jednym z [obsługiwanych regionów](#regional-availability-private-endpoints).

* Masz zainicjowaną sieć wirtualną platformy Azure z podsiecią, w której zostanie utworzony prywatny punkt końcowy. Aby uzyskać więcej informacji, zobacz [Tworzenie sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-network/quick-create-cli.md) .

* W przypadku urządzeń, które działają w sieciach lokalnych, skonfiguruj [wirtualną sieć prywatną (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) lub prywatną komunikację równorzędną [EXPRESSROUTE](https://azure.microsoft.com/services/expressroute/) w sieci wirtualnej platformy Azure.


### <a name="regional-availability-private-endpoints"></a>Dostępność regionalna (prywatne punkty końcowe)

Prywatne punkty końcowe obsługiwane w IoT Hub utworzone w następujących regionach:

* Wschodnie stany USA

* Południowo-środkowe stany USA

* Zachodnie stany USA 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Skonfiguruj prywatny punkt końcowy dla IoT Hub ruchu przychodzącego

Aby skonfigurować prywatny punkt końcowy, wykonaj następujące kroki:

1. Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby ponownie zarejestrować dostawcę usługi Azure IoT Hub w ramach subskrypcji:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Przejdź do karty **połączenia prywatnego punktu końcowego** w portalu IoT Hub (Ta karta jest dostępna tylko w centrach IoT w [obsługiwanych regionach](#regional-availability-private-endpoints)), a następnie kliknij **+** znak, aby dodać nowy prywatny punkt końcowy.

3. Podaj subskrypcję, grupę zasobów, nazwę i region, aby utworzyć nowy prywatny punkt końcowy w programie (w idealnym przypadku należy utworzyć prywatny punkt końcowy w tym samym regionie co centrum). Aby uzyskać więcej informacji, zobacz [sekcję Dostępność regionalna](#regional-availability-private-endpoints) .

4. Kliknij przycisk **Dalej: zasób**i podaj subskrypcję dla zasobu IoT Hub, a następnie wybierz pozycję **"Microsoft. Devices/IotHubs"** jako typ zasobu, nazwę IoT Hub jako **zasób**, a **iotHub** jako docelowy zasób podrzędny.

5. Kliknij przycisk **Dalej: Konfiguracja** i podaj sieć wirtualną i podsieć, aby utworzyć prywatny punkt końcowy w programie. W razie potrzeby wybierz opcję integracji z prywatną strefą DNS platformy Azure.

6. Kliknij przycisk **Dalej: Tagi**i opcjonalnie Podaj wszystkie znaczniki dla zasobu.

7. Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć prywatny zasób punktu końcowego.


### <a name="pricing-private-endpoints"></a>Cennik (prywatne punkty końcowe)

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Ruch wychodzący z IoT Hub do innych zasobów platformy Azure

IoT Hub musi mieć dostęp do magazynu obiektów blob platformy Azure, centrów zdarzeń, zasobów usługi Service Bus na potrzeby [routingu komunikatów](./iot-hub-devguide-messages-d2c.md), [przekazywania plików](./iot-hub-devguide-file-upload.md)i [zbiorczego importowania/eksportowania urządzeń](./iot-hub-bulk-identity-mgmt.md), które zwykle odbywają się za pośrednictwem publicznego punktu końcowego zasobów. W przypadku powiązania konta magazynu, centrów zdarzeń lub zasobu usługi Service Bus z siecią wirtualną, zalecana konfiguracja domyślnie blokuje łączność z zasobem. W związku z tym będzie przeszkadzać IoT Hub funkcje, które wymagają dostępu do tych zasobów.

Aby wyeliminować tę sytuację, musisz włączyć łączność z poziomu zasobów IoT Hub do konta magazynu, centrów zdarzeń lub zasobów usługi Service Bus za pomocą opcji **Zaufane usługi platformy Azure w pierwszej kolejności** .

Obowiązują następujące wymagania wstępne:

* Centrum IoT musi być inicjowane w jednym z [obsługiwanych regionów](#regional-availability-trusted-microsoft-first-party-services).

* IoT Hub musi mieć przypisaną tożsamość usługi zarządzanej w czasie aprowizacji centrum. Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia centrum z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Dostępność regionalna (zaufane usługi pierwszej firmy Microsoft)

Wyjątek usługi zaufanej pierwszej firmy Azure do pomijania ograniczeń zapory do usługi Azure Storage, centrów zdarzeń i zasobów usługi Service Bus są obsługiwane tylko w przypadku centrów IoT w następujących regionach:

* Wschodnie stany USA

* Południowo-środkowe stany USA

* Zachodnie stany USA 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Cennik (zaufane usługi pierwszej firmy Microsoft)

Funkcja wyjątków zaufanych usług pierwszej firmy Microsoft jest bezpłatna w centrach IoT w [obsługiwanych regionach](#regional-availability-trusted-microsoft-first-party-services). Opłaty za zainicjowane konta magazynu, Centra zdarzeń lub zasoby usługi Service Bus są stosowane oddzielnie.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Tworzenie Centrum IoT Hub z tożsamością usługi zarządzanej

Tożsamość usługi zarządzanej można przypisać do centrum w czasie aprowizacji zasobów (Ta funkcja nie jest obecnie obsługiwana dla istniejących centrów), co wymaga, aby usługa IoT Hub używała protokołu TLS 1,2 jako wersji minimalnej. W tym celu należy użyć szablonu zasobu ARM poniżej:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Po podstawianiu `name`wartości dla zasobu `location`, `SKU.name` i `SKU.tier`można użyć interfejsu wiersza polecenia platformy Azure do wdrożenia zasobu w istniejącej grupie zasobów przy użyciu:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Po utworzeniu zasobu można pobrać tożsamość usługi zarządzanej przypisanej do centrum przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Po zainicjowaniu obsługi administracyjnej IoT Hub z tożsamością usługi zarządzanej należy postępować zgodnie z odpowiednią sekcją w celu skonfigurowania punktów końcowych routingu na [kontach magazynu](#egress-connectivity-to-storage-account-endpoints-for-routing), [centrach zdarzeń](#egress-connectivity-to-event-hubs-endpoints-for-routing)i zasobach [usługi Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing) albo w celu skonfigurowania [przekazywania plików](#egress-connectivity-to-storage-accounts-for-file-upload) i [importowania/eksportowania urządzeń zbiorczych](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport).


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Ruch wychodzący z punktów końcowych konta magazynu dla routingu

IoT Hub można skonfigurować w celu kierowania komunikatów do konta magazynu należącego do klienta. Aby umożliwić funkcji routingu dostęp do konta magazynu w trakcie ograniczeń zapory, IoT Hub musi mieć tożsamość usługi zarządzanej (patrz jak [utworzyć centrum z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity)). Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do konta magazynu.

1. W Azure Portal przejdź do karty **kontroli dostępu konta magazynu (IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz kolejno pozycje **Magazyn danych obiektów BLOB — współautor** jako **rola**, **użytkownik usługi Azure AD, Grupa lub jednostka usługi** jako **przypisanie dostępu do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** na koncie magazynu i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **routing wiadomości** .

5. Przejdź do sekcji **niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz pozycję **Storage** jako typ punktu końcowego.

6. Na wyświetlonej stronie Podaj nazwę punktu końcowego, wybierz kontener, który ma być używany w magazynie obiektów blob, podaj kodowanie i format nazwy pliku. Wybierz pozycję **system przypisany** jako **Typ uwierzytelniania** do punktu końcowego magazynu. Kliknij przycisk **Utwórz**.

Teraz niestandardowy punkt końcowy magazynu jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory. Możesz teraz używać tego punktu końcowego do konfigurowania reguły routingu.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Ruch wychodzący z punktów końcowych centrów zdarzeń dla routingu

IoT Hub można skonfigurować w celu kierowania komunikatów do przestrzeni nazw centrów zdarzeń należących do klienta. Aby umożliwić funkcji routingu dostęp do zasobu centrów zdarzeń podczas ograniczeń zapory, IoT Hub musi mieć tożsamość usługi zarządzanej (Zobacz jak [utworzyć centrum z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity)). Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do centrów zdarzeń.

1. W Azure Portal przejdź do karty kontroli dostępu do centrów zdarzeń **(IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz **Event Hubs nadawcy danych** jako **rolę**, **użytkownika usługi Azure AD, grupę lub jednostkę usługi** jako **przypisujące dostęp do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** w centrach zdarzeń i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do centrów zdarzeń**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **routing wiadomości** .

5. Przejdź do sekcji **niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz pozycję **Centra zdarzeń** jako typ punktu końcowego.

6. Na wyświetlonej stronie Podaj nazwę punktu końcowego, wybierz przestrzeń nazw usługi Event Hub i wystąpienie, a następnie kliknij przycisk **Utwórz** .

Teraz Twój punkt końcowy centrów zdarzeń jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu centrów zdarzeń pomimo ograniczeń zapory. Możesz teraz używać tego punktu końcowego do konfigurowania reguły routingu.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Ruch wychodzący z punktów końcowych usługi Service Bus na potrzeby routingu

IoT Hub można skonfigurować w celu kierowania komunikatów do przestrzeni nazw usługi Service Bus należącej do klienta. Aby umożliwić funkcji routingu dostęp do zasobu usługi Service Bus podczas ograniczeń zapory, IoT Hub musi mieć tożsamość usługi zarządzanej (patrz jak [utworzyć centrum z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity)). Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do usługi Service Bus.

1. W Azure Portal przejdź do karty kontrola dostępu usługi Service Bus **(IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz pozycję **nadawca danych w usłudze Service Bus** jako **rolę**, **użytkownika lub grupę usługi Azure AD** , a następnie wybierz nazwę zasobu w usłudze, **która jest przypisana do** IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** w usłudze Service Bus i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tej usługi Service Bus**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **routing wiadomości** .

5. Przejdź do sekcji **niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz pozycję **Kolejka usługi Service Bus** lub **temat usługi Service Bus** (jeśli dotyczy) jako typ punktu końcowego.

6. Na wyświetlonej stronie Podaj nazwę punktu końcowego, wybierz swoją przestrzeń nazw usługi Service Bus i kolejkę lub temat (stosownie do potrzeb). Kliknij przycisk **Utwórz**.

Teraz niestandardowy punkt końcowy usługi Service Bus jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu usługi Service Bus pomimo ograniczeń zapory. Możesz teraz używać tego punktu końcowego do konfigurowania reguły routingu.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Wyjście z łączności z kontami magazynu na potrzeby przekazywania plików

Funkcja przekazywania plików IoT Hub umożliwia urządzeniom przekazywanie plików do konta magazynu należącego do klienta. Aby umożliwić przekazywanie plików do funkcji, oba urządzenia i IoT Hub muszą mieć łączność z kontem magazynu. Jeśli ograniczenia dotyczące zapory są stosowane na koncie magazynu, urządzenia muszą używać dowolnego mechanizmu obsługiwanego konta magazynu (w tym [prywatnych punktów końcowych](../private-link/create-private-endpoint-storage-portal.md), [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md) lub [bezpośredniej konfiguracji zapory](../storage/common/storage-network-security.md)) w celu uzyskania łączności. Analogicznie, jeśli istnieją ograniczenia dotyczące zapory na koncie magazynu, IoT Hub należy skonfigurować w celu uzyskania dostępu do zasobu magazynu za pomocą zaufanego wyjątku usług firmy Microsoft. W tym celu IoT Hub musi mieć tożsamość usługi zarządzanej (Zobacz, jak [utworzyć centrum z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity)). Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do konta magazynu.

1. W Azure Portal przejdź do karty **kontroli dostępu konta magazynu (IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz kolejno pozycje **Magazyn danych obiektów BLOB — współautor** jako **rola**, **użytkownik usługi Azure AD, Grupa lub jednostka usługi** jako **przypisanie dostępu do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** na koncie magazynu i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **przekazywanie plików** .

5. Na wyświetlonej stronie Wybierz kontener, który ma być używany w magazynie obiektów blob, w razie potrzeby skonfiguruj **Ustawienia powiadomień dotyczących plików**, **czas wygaśnięcia sygnatury dostępu współdzielonego**, **domyślny czas wygaśnięcia** i **maksymalną liczbę dostaw** . Wybierz pozycję **system przypisany** jako **Typ uwierzytelniania** do punktu końcowego magazynu. Kliknij przycisk **Utwórz**.

Teraz punkt końcowy magazynu dla przekazywania plików jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Wychodzące połączenia z kontami magazynu na potrzeby importowania/eksportowania urządzeń zbiorczych

IoT Hub obsługuje funkcje do [importowania/eksportowania](./iot-hub-bulk-identity-mgmt.md) informacji o urządzeniach zbiorczo z/do magazynu obiektów BLOB dostarczonych przez klienta. Aby umożliwić korzystanie z funkcji importu/eksportu zbiorczego, oba urządzenia i IoT Hub muszą mieć łączność z kontem magazynu.

Ta funkcja wymaga łączności IoT Hub z kontem magazynu. Aby uzyskać dostęp do zasobu usługi Service Bus podczas ograniczeń zapory, IoT Hub musi mieć tożsamość usługi zarządzanej (patrz jak [utworzyć centrum z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity)). Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do usługi Service Bus.

1. W Azure Portal przejdź do karty **kontroli dostępu konta magazynu (IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz kolejno pozycje **Magazyn danych obiektów BLOB — współautor** jako **rola**, **użytkownik usługi Azure AD, Grupa lub jednostka usługi** jako **przypisanie dostępu do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** na koncie magazynu i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

Za pomocą interfejsu API REST usługi Azure IoT można teraz [tworzyć zadania importowania eksportu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) , aby uzyskać informacje na temat korzystania z funkcji zbiorczego importowania/eksportowania. Należy pamiętać, że w treści żądania należy `storageAuthenticationType="identityBased"` podać wartość i użyć `inputBlobContainerUri="https://..."` oraz `outputBlobContainerUri="https://..."` jako adres URL danych wejściowych i wyjściowych konta magazynu.


Usługa Azure IoT Hub SDK obsługuje również tę funkcję w Menedżerze rejestru klienta usługi. Poniższy fragment kodu przedstawia sposób inicjowania zadania importowania lub eksportowania zadania w programie przy użyciu zestawu C# SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Aby korzystać z tej ograniczonej w regionach wersji zestawów SDK usługi Azure IoT z obsługą sieci wirtualnych dla języków C#, Java i Node. js:

1. Utwórz zmienną środowiskową o `EnableStorageIdentity` nazwie i ustaw jej wartość `1`na.

2. Pobierz zestaw SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node. js](https://aka.ms/vnetnodesdk)
 
W przypadku języka Python Pobierz naszą ograniczoną wersję z usługi GitHub.

1. Przejdź do [strony wersji usługi GitHub](https://aka.ms/vnetpythonsdk).

2. Pobierz następujący plik, który znajduje się u dołu strony wydania w nagłówku o nazwie **Assets**.
    > *azure_iot_hub-2.2.0_limited-PY2. py3-none-any. WHL*

3. Otwórz Terminal i przejdź do folderu z pobranym plikiem.

4. Uruchom następujące polecenie, aby zainstalować zestaw SDK usługi Python z obsługą sieci wirtualnych:
    > Instalacja PIP./azure_iot_hub-2.2.0_limited-PY2. py3-none-any. WHL


## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o funkcjach IoT Hub:

* [Routing komunikatów](./iot-hub-devguide-messages-d2c.md)
* [Przekazywanie plików](./iot-hub-devguide-file-upload.md)
* [Zbiorcze importowanie/eksportowanie urządzeń](./iot-hub-bulk-identity-mgmt.md) 
