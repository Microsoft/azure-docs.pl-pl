---
title: Azure IoT Hub dla sieci wirtualnych
description: Jak używać wzorca łączności sieci wirtualnych z IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: df38f9b3482847ea0415af5cb47540e244b0510b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739894"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub dla sieci wirtualnych z usługami Private Link i tożsamością zarządzaną

Domyślnie nazwy IoT Hub są mapowane na publiczny punkt końcowy z publicznie routowalnym adresem IP za pośrednictwem Internetu. Różni klienci współdzielą IoT Hub punktu końcowego, a urządzenia IoT w sieciach szerokich i lokalnych mogą uzyskać do niego dostęp.

![IoT Hub publiczny punkt końcowy](./media/virtual-network-support/public-endpoint.png)

IoT Hub, takie jak [routing](./iot-hub-devguide-messages-d2c.md) [komunikatów,](./iot-hub-devguide-file-upload.md)przekazywanie plików i zbiorcze [importowanie/eksportowanie](./iot-hub-bulk-identity-mgmt.md) urządzeń, również wymagają łączności z usługi IoT Hub do zasobu platformy Azure należącego do klienta za pośrednictwem jego publicznego punktu końcowego. Te ścieżki łączności tworzą zbiorczo ruch wychodzący z IoT Hub do zasobów klienta.

Możesz ograniczyć łączność z zasobami platformy Azure (w tym IoT Hub) za pośrednictwem sieci wirtualnej, której jesteś właścicielem i która działa. Są to między innymi następujące przyczyny:

* Wprowadzenie izolacji sieci dla centrum IoT Przez zapobieganie narażeniu na łączność z publicznym Internetem.

* Zapewnianie prywatnego połączenia z zasobów sieci lokalnej w celu zapewnienia, że dane i ruch są przesyłane bezpośrednio do sieci szkieletowej platformy Azure.

* Zapobieganie atakom eksfiltracji z poufnych sieci lokalnych. 

* Następujące ustalone wzorce łączności na całej platformie Azure przy [użyciu prywatnych punktów końcowych.](../private-link/private-endpoint-overview.md)

W tym artykule opisano sposób osiągnięcia tych celów przy użyciu usługi [Azure Private Link na](../private-link/private-link-overview.md) IoT Hub dla połączeń przychodzących i wyjątku zaufanego usługi firmy Microsoft dla łączności wychodzącej z IoT Hub do innych zasobów platformy Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Łączność ruch przychodzący do IoT Hub przy użyciu Azure Private Link

Prywatny punkt końcowy to prywatny adres IP przydzielony wewnątrz sieci wirtualnej należącej do klienta, za pośrednictwem której zasób platformy Azure jest osiągalny. Za Azure Private Link można skonfigurować prywatny punkt końcowy dla centrum IoT Hub, aby umożliwić usługom w sieci wirtualnej dostęp do sieci IoT Hub bez konieczności przesyłania ruchu do publicznego punktu końcowego usługi IoT Hub. Podobnie urządzenia lokalne mogą korzystać z wirtualnej sieci prywatnej [(VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub komunikacji równorzędnej [usługi ExpressRoute](https://azure.microsoft.com/services/expressroute/) w celu uzyskania łączności z siecią wirtualną i siecią IoT Hub (za pośrednictwem jej prywatnego punktu końcowego). W związku z tym można ograniczyć lub całkowicie zablokować łączność z publicznymi punktami końcowymi centrum IoT hub przy użyciu filtru [adresów IP](./iot-hub-ip-filtering.md) usługi IoT Hub lub przełącznika dostępu do sieci [publicznej.](iot-hub-public-network-access.md) Takie podejście zapewnia łączność z centrum przy użyciu prywatnego punktu końcowego dla urządzeń. Ta konfiguracja koncentruje się głównie na urządzeniach w sieci lokalnej. Ta konfiguracja nie jest zalecana w przypadku urządzeń wdrożonych w sieci całego obszaru.

![IoT Hub ruchu wychodzącego sieci wirtualnej](./media/virtual-network-support/virtual-network-ingress.png)

Przed przystąpieniem do pracy upewnij się, że spełnione są następujące wymagania wstępne:

* Utworzono [sieć wirtualną platformy Azure z](../virtual-network/quick-create-portal.md) podsiecią, w której zostanie utworzony prywatny punkt końcowy.

* W przypadku urządzeń, które działają w sieciach lokalnych, skonfiguruj wirtualną sieć prywatną [(VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub prywatną komunikacja równorzędną [Usługi ExpressRoute](https://azure.microsoft.com/services/expressroute/) z siecią wirtualną platformy Azure.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Konfigurowanie prywatnego punktu końcowego dla IoT Hub danych przychodzących

Prywatny punkt końcowy działa IoT Hub API urządzeń (takich jak komunikaty z urządzenia do chmury), a także interfejsów API usługi (takich jak tworzenie i aktualizowanie urządzeń).

1. W Azure Portal wybierz pozycję **Sieć,** Połączenia z prywatnym **punktem końcowym** i kliknij **pozycję + Prywatny punkt końcowy.**

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Zrzut ekranu przedstawiający miejsce dodawania prywatnego punktu końcowego dla IoT Hub":::

1. Podaj subskrypcję, grupę zasobów, nazwę i region, w których chcesz utworzyć nowy prywatny punkt końcowy. W idealnym przypadku prywatny punkt końcowy powinien zostać utworzony w tym samym regionie co centrum.

1. Kliknij **przycisk Dalej:** Zasób i podaj subskrypcję zasobu usługi IoT Hub, a następnie wybierz typ zasobu  **"Microsoft.Devices/IotHubs",** nazwę zasobu IoT Hub jako zasób, a pozycję **iotHub** jako docelowy podsieć.

1. Kliknij **przycisk Dalej: Konfiguracja** i podaj sieć wirtualną i podsieć, w których ma być utworzyć prywatny punkt końcowy. W razie potrzeby wybierz opcję integracji z prywatną strefą DNS platformy Azure.

1. Kliknij **przycisk Dalej: Tagi** i opcjonalnie podaj wszystkie tagi dla zasobu.

1. Kliknij **pozycję Przejrzyj i utwórz,** aby utworzyć zasób linku prywatnego.

### <a name="built-in-event-hub-compatible-endpoint"></a>Wbudowany punkt końcowy zgodny z centrum zdarzeń 

Dostęp [do wbudowanego punktu końcowego zgodnego z](iot-hub-devguide-messages-read-builtin.md) centrum zdarzeń można również uzyskać za pośrednictwem prywatnego punktu końcowego. Po skonfigurowaniu łącza prywatnego powinno być dostępne dodatkowe połączenie prywatnego punktu końcowego dla wbudowanego punktu końcowego. Jest to ten, który ma w `servicebus.windows.net` witrynie FQDN.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Obraz przedstawiający dwa prywatne punkty końcowe przy każdym IoT Hub łącza prywatnego":::

IoT Hub IP może [opcjonalnie](iot-hub-ip-filtering.md) kontrolować dostęp publiczny do wbudowanego punktu końcowego. 

Aby całkowicie zablokować dostęp sieci publicznej do centrum IoT [Hub,](iot-hub-public-network-access.md) wyłącz dostęp do sieci publicznej lub użyj filtru adresów IP, aby zablokować cały adres IP, i wybierz opcję zastosowania reguł do wbudowanego punktu końcowego.

### <a name="pricing-for-private-link"></a>Cennik usługi Private Link

Aby uzyskać szczegółowe informacje o [cenach, zobacz Azure Private Link cennik.](https://azure.microsoft.com/pricing/details/private-link)

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Łączność wychodząca z IoT Hub do innych zasobów platformy Azure

IoT Hub połączyć się z magazynem obiektów blob platformy Azure, centrum [](./iot-hub-devguide-file-upload.md)zdarzeń, zasobami usługi Service Bus w celu routingu komunikatów, [](./iot-hub-devguide-messages-d2c.md)przekazywania plików i zbiorczego [importowania/eksportowania](./iot-hub-bulk-identity-mgmt.md) urządzeń za pośrednictwem publicznego punktu końcowego zasobów. Powiązanie zasobu z siecią wirtualną domyślnie blokuje łączność z zasobem. W związku z tym ta konfiguracja uniemożliwia IoT Hub wysyłania danych do zasobów. Aby rozwiązać ten problem, włącz łączność między zasobem usługi IoT Hub a kontem magazynu, centrum zdarzeń lub zasobami usługi Service Bus za pośrednictwem opcji **zaufanej usługi firmy Microsoft.**

### <a name="turn-on-managed-identity-for-iot-hub"></a>Włączanie tożsamości zarządzanej dla IoT Hub

Aby umożliwić innym usługom znalezienie Centrum IoT jako zaufanej usługi firmy Microsoft, musi ono mieć przypisaną przez system tożsamość zarządzaną.

1. Przejdź do **tożsamości** w IoT Hub portalu

1. W **obszarze Stan** wybierz pozycję **Wł.,** a następnie kliknij przycisk **Zapisz.**

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Zrzut ekranu przedstawiający sposób włączanie tożsamości zarządzanej dla IoT Hub":::

Aby włączyć tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub update --name <iot-hub-resource-name> --set identity.type="SystemAssigned"
```

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Przypisywanie tożsamości zarządzanej do IoT Hub podczas tworzenia przy użyciu szablonu usługi ARM

Aby przypisać tożsamość zarządzaną do centrum IoT Podczas aprowizowania zasobów, użyj poniższego szablonu usługi ARM. Ten szablon usługi ARM ma dwa wymagane zasoby i oba muszą zostać wdrożone przed utworzeniem innych zasobów, takich jak `Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups` . 

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
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "createIotHub",
      "dependsOn": [
        "[resourceId('Microsoft.Devices/IotHubs', '<provide-a-valid-resource-name>')]"
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

Po wsłoceniu wartości dla zasobu , i możesz użyć interfejsu wiersza polecenia platformy Azure, aby wdrożyć zasób w istniejącej grupie zasobów przy `name` `location` `SKU.name` `SKU.tier` użyciu:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Po utworzeniu zasobu można pobrać tożsamość usługi zarządzanej przypisaną do centrum przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Cennik tożsamości zarządzanej

Funkcja wyjątków zaufanych usług firmy Microsoft jest bezpłatna. Opłaty za aprowizowane konta magazynu, centra zdarzeń lub zasoby usługi Service Bus są naliczane osobno.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Łączność ruchu wychodzącego z punktami końcowymi konta magazynu w celu routingu

IoT Hub przekierowywować komunikaty do konta magazynu należącego do klienta. Aby umożliwić funkcji routingu dostęp do konta magazynu, gdy obowiązują ograniczenia zapory, centrum musi używać tożsamości zarządzanej w celu uzyskania dostępu do konta magazynu. Najpierw centrum będzie potrzebować tożsamości [zarządzanej](#turn-on-managed-identity-for-iot-hub). Po aprowizowanie tożsamości zarządzanej wykonaj poniższe kroki, aby nadać tożsamości zasobów centrum uprawnienia RBAC platformy Azure do uzyskiwania dostępu do konta magazynu.

1. W Azure Portal przejdź do karty Kontrola dostępu **(IAM)** konta magazynu  i kliknij przycisk Dodaj w sekcji **Dodawanie przypisania** roli.

2. Wybierz pozycję Współautor danych obiektu **blob** usługi Storage [ (nie](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)współautor lub Współautor konta  magazynu) jako rolę **,** **użytkownika,** grupę lub jednostkę usługi Azure AD jako Przypisywanie dostępu do i wybierz nazwę zasobu usługi IoT Hub z listy rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne na** koncie magazynu i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** Na liście **Wyjątki** zaznacz pole wyboru Zezwalaj zaufanym **usługi firmy Microsoft na dostęp do tego konta magazynu.** Kliknij przycisk **Zapisz**.

4. Na stronie IoT Hub zasobu aplikacji przejdź do karty **Routing komunikatów.**

5. Przejdź do **sekcji Niestandardowe punkty końcowe** i kliknij pozycję **Dodaj.** Wybierz **pozycję Magazyn** jako typ punktu końcowego.

6. Na stronie, która zostanie pokazana, podaj nazwę punktu końcowego, wybierz kontener, który ma być używany w magazynie obiektów blob, podaj kodowanie i format nazwy pliku. Wybierz **pozycję Oparte na tożsamości** jako typ **uwierzytelniania** dla punktu końcowego magazynu. Kliknij przycisk **Utwórz**.

Teraz niestandardowy punkt końcowy magazynu jest ustawiony do używania tożsamości przypisanej przez system centrum i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory. Teraz możesz użyć tego punktu końcowego do skonfigurowania reguły routingu.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Łączność ruchu wychodzącego z punktami końcowymi usługi Event Hubs w celu routingu

IoT Hub można skonfigurować do rozsyłania komunikatów do przestrzeni nazw usługi Event Hubs należącej do klienta. Aby umożliwić funkcji routingu dostęp do zasobu usługi Event Hubs, gdy obowiązują ograniczenia zapory, IoT Hub musi użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobu usługi Event Hubs. Najpierw centrum będzie potrzebować tożsamości zarządzanej. Po utworzeniu tożsamości zarządzanej wykonaj poniższe kroki, aby nadać tożsamości zasobów centrum uprawnienia RBAC platformy Azure do uzyskiwania dostępu do centrów zdarzeń.

1. W obszarze Azure Portal przejdź do karty Kontrola dostępu **(IAM)** w centrum zdarzeń, a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania** roli.

2. Wybierz **Event Hubs data sender** jako rolę , użytkownika usługi Azure  **AD,** grupę lub jednostkę usługi jako Przypisywanie dostępu do i wybierz nazwę zasobu usługi IoT Hub z listy rozwijanej.  Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne w** centrach zdarzeń i włącz opcję **Zezwalaj** na dostęp z wybranych sieci. Na liście **Wyjątki** zaznacz pole wyboru Zezwalaj zaufanym **usługi firmy Microsoft na dostęp do centrów zdarzeń.** Kliknij przycisk **Zapisz**.

4. Na stronie IoT Hub zasobu aplikacji przejdź do karty **Routing komunikatów.**

5. Przejdź do **sekcji Niestandardowe punkty końcowe** i kliknij pozycję **Dodaj**. Wybierz **pozycję Event Hubs** jako typ punktu końcowego.

6. Na stronie, która zostanie pokazana, podaj nazwę punktu końcowego, wybierz przestrzeń nazw i wystąpienie usługi Event Hubs. Wybierz **pozycję Oparte na tożsamości** jako typ **uwierzytelniania** i kliknij **przycisk** Utwórz.

Teraz niestandardowy punkt końcowy usługi Event Hubs został ustawiony do używania tożsamości przypisanej przez system centrum i ma uprawnienia dostępu do zasobu usługi Event Hubs pomimo ograniczeń zapory. Teraz możesz użyć tego punktu końcowego do skonfigurowania reguły rozsyłania.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Łączność ruchu wychodzącego z punktami końcowymi usługi Service Bus w celu routingu

IoT Hub można skonfigurować do przekierowania komunikatów do przestrzeni nazw magistrali usług należących do klienta. Aby umożliwić funkcji routingu dostęp do zasobu usługi Service Bus w czasie stosowania ograniczeń zapory, użytkownik IoT Hub musi użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobu usługi Service Bus. Najpierw centrum będzie potrzebować tożsamości zarządzanej. Po aprowizowania tożsamości zarządzanej wykonaj poniższe kroki, aby nadać kontroli RBAC platformy Azure uprawnienia do tożsamości zasobu centrum w celu uzyskania dostępu do magistrali usług.

1. W Azure Portal przejdź do karty Kontrola dostępu **(IAM)** usługi  Service Bus i kliknij przycisk Dodaj w sekcji **Dodawanie przypisania** roli.

2. Wybierz **pozycję Nadawca** danych usługi Service Bus jako rolę  **,** użytkownik usługi **Azure AD,** grupę lub jednostkę usługi jako Przypisywanie dostępu do i wybierz nazwę zasobu usługi IoT Hub z listy rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne w** magistrali usług i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** Na liście **Wyjątki** zaznacz pole wyboru Zezwalaj zaufanym **usługi firmy Microsoft na dostęp do tej magistrali usług.** Kliknij przycisk **Zapisz**.

4. Na stronie IoT Hub zasobu aplikacji przejdź do karty **Routing komunikatów.**

5. Przejdź do **sekcji Niestandardowe punkty końcowe** i kliknij pozycję **Dodaj**. Wybierz **pozycję Kolejka usługi Service Bus** Service Bus **tematu** (jeśli dotyczy) jako typ punktu końcowego.

6. Na stronie, która zostanie pokazana, podaj nazwę punktu końcowego, wybierz przestrzeń nazw i kolejkę lub temat usługi Service Bus (jeśli ma to zastosowanie). Wybierz **pozycję Oparte na tożsamości** jako typ **uwierzytelniania** i kliknij **przycisk** Utwórz.

Teraz niestandardowy punkt końcowy usługi Service Bus jest ustawiony do używania tożsamości przypisanej przez system centrum i ma uprawnienia dostępu do zasobu usługi Service Bus pomimo ograniczeń zapory. Teraz możesz użyć tego punktu końcowego do skonfigurowania reguły routingu.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Łączność wychodząca z kontami magazynu w celu przekazywania plików

IoT Hub funkcji przekazywania plików umożliwia urządzeniu przekazywanie plików na konto magazynu należące do klienta. Aby umożliwić przekazywanie plików, zarówno urządzenia, jak i IoT Hub muszą mieć łączność z kontem magazynu. Jeśli na koncie magazynu obowiązują ograniczenia zapory, urządzenia muszą korzystać z dowolnego obsługiwanego mechanizmu [](../virtual-network/virtual-network-service-endpoints-overview.md)konta magazynu (w tym prywatnych punktów [końcowych,](../private-link/tutorial-private-endpoint-storage-portal.md)punktów końcowych usługi lub bezpośredniej konfiguracji [zapory)](../storage/common/storage-network-security.md)w celu uzyskania łączności. Podobnie jeśli na koncie magazynu obowiązują ograniczenia zapory, IoT Hub należy skonfigurować dostęp do zasobu magazynu za pośrednictwem zaufanego usługi firmy Microsoft magazynu. W tym celu IoT Hub musi mieć tożsamość zarządzaną. Po aprowizowanie tożsamości zarządzanej wykonaj poniższe kroki, aby udzielić tożsamości zasobów centrum uprawnień RBAC platformy Azure w celu uzyskania dostępu do konta magazynu.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. W Azure Portal przejdź do karty Kontrola dostępu **(IAM)** konta magazynu, a następnie kliknij przycisk **Dodaj** w sekcji Dodawanie **przypisania** roli.

2. Wybierz  pozycję Współautor danych obiektu blob usługi Storage [ (nie](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)Współautor ani Współautor konta  magazynu) jako rolę **,** użytkownik usługi **Azure AD,** grupa lub nazwa główna usługi jako Przypisywanie dostępu i wybierz nazwę zasobu usługi IoT Hub z listy rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne na** koncie magazynu i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** Na liście **Wyjątki** zaznacz pole wyboru Zezwalaj zaufanym **usługi firmy Microsoft na dostęp do tego konta magazynu.** Kliknij przycisk **Zapisz**.

4. Na stronie IoT Hub zasobu aplikacji przejdź do karty **Przekazywanie** plików.

5. Na stronie, która zostanie pokazane, wybierz kontener, który ma być używać w magazynie obiektów blob, skonfiguruj ustawienia powiadomień dotyczących **plików,** czas **wygaśnięcia** sygnatury dostępu współdzielonego, domyślny czas wygaśnięcia i maksymalną liczbę dostaw zgodnie z **potrzebami.** Wybierz **pozycję Oparte na tożsamości** jako typ **uwierzytelniania** dla punktu końcowego magazynu. Kliknij przycisk **Utwórz**. Jeśli w tym kroku wystąpi błąd, tymczasowo ustaw konto magazynu tak, aby zezwalało na dostęp ze wszystkich **sieci,** spróbuj ponownie. Zaporę na koncie magazynu można skonfigurować po zakończeniu konfiguracji przekazywania plików.

Teraz punkt końcowy magazynu na użytek przekazywania plików jest ustawiony do używania tożsamości przypisanej przez system centrum i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Łączność wychodząca z kontami magazynu w celu zbiorczego importowania/eksportowania urządzeń

IoT Hub obsługuje funkcję zbiorczego [importowania/eksportowania](./iot-hub-bulk-identity-mgmt.md) informacji o urządzeniach z/do obiektu blob magazynu dostarczonego przez klienta. Aby umożliwić działanie funkcji importu/eksportu zbiorczego, zarówno urządzenia, IoT Hub muszą mieć łączność z kontem magazynu.

Ta funkcja wymaga połączenia IoT Hub z kontem magazynu. Aby uzyskać dostęp do zasobu usługi Service Bus, gdy obowiązują ograniczenia zapory, IoT Hub musi mieć tożsamość zarządzaną. Po aprowizowania tożsamości zarządzanej wykonaj poniższe kroki, aby nadać kontroli RBAC platformy Azure uprawnienia do tożsamości zasobu centrum w celu uzyskania dostępu do magistrali usług.

1. W Azure Portal przejdź do karty Kontrola dostępu **(IAM)** konta magazynu  i kliknij przycisk Dodaj w sekcji **Dodawanie przypisania** roli.

2. Wybierz  pozycję Współautor danych obiektu blob usługi Storage [ (nie](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)Współautor ani Współautor konta  magazynu) jako rolę **,** użytkownik usługi **Azure AD,** grupa lub nazwa główna usługi jako Przypisywanie dostępu i wybierz nazwę zasobu usługi IoT Hub z listy rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne na** koncie magazynu i włącz opcję **Zezwalaj na** dostęp z wybranych sieci. Na liście **Wyjątki** zaznacz pole wyboru Zezwalaj zaufanym **usługi firmy Microsoft na dostęp do tego konta magazynu.** Kliknij przycisk **Zapisz**.

Teraz możesz użyć interfejsów API REST [](/rest/api/iothub/service/jobs/getimportexportjobs) usługi Azure IoT do tworzenia zadań eksportu importu, aby uzyskać informacje na temat sposobu korzystania z funkcji importowania/eksportowania zbiorczego. Musisz podać adres w treści żądania i użyć odpowiednio wartości i jako wejściowych i wyjściowych adresów `storageAuthenticationType="identityBased"` `inputBlobContainerUri="https://..."` URL konta `outputBlobContainerUri="https://..."` magazynu.

Azure IoT Hub SDK obsługują również tę funkcję w menedżerze rejestru klienta usługi. Poniższy fragment kodu przedstawia sposób inicjowania zadania importu lub zadania eksportu przy użyciu zestawu SDK języka C#.

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

Aby użyć tej wersji zestawów SDK usługi Azure IoT z obsługą sieci wirtualnej dla języków C#, Java i Node.js:

1. Utwórz zmienną środowiskową o `EnableStorageIdentity` nazwie i ustaw jej wartość na `1` .

2. Pobierz zestaw SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
W przypadku języka Python pobierz ograniczoną wersję z usługi GitHub.

1. Przejdź do strony [wydania usługi GitHub.](https://aka.ms/vnetpythonsdk)

2. Pobierz następujący plik, który znajduje się w dolnej części strony wydania w nagłówku o nazwie **assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Otwórz terminal i przejdź do folderu z pobranym plikiem.

4. Uruchom następujące polecenie, aby zainstalować zestaw SDK usługi języka Python z obsługą sieci wirtualnych:
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o IoT Hub funkcji:

* [Routing komunikatów](./iot-hub-devguide-messages-d2c.md)
* [Przekazywanie plików](./iot-hub-devguide-file-upload.md)
* [Zbiorcze importowanie/eksportowanie urządzeń](./iot-hub-bulk-identity-mgmt.md)
