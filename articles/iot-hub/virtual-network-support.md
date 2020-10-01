---
title: Obsługa IoT Hub platformy Azure dla sieci wirtualnych
description: Jak używać wzorca łączności sieci wirtualnych z IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: jlian
ms.openlocfilehash: 6c562f7a5d9c7c02c737898821eef5ee5271eea4
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613904"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub obsługa sieci wirtualnych z linkiem prywatnym i tożsamością zarządzaną

Domyślnie nazwy hostów IoT Hub są mapowane na publiczny punkt końcowy z publicznie rutowanym adresem IP przez Internet. Różni klienci korzystają z tego IoT Hub publicznego punktu końcowego, a urządzenia IoT w sieci rozległej i sieci lokalne mogą uzyskać do niego dostęp.

![IoT Hub publiczny punkt końcowy](./media/virtual-network-support/public-endpoint.png)

IoT Hub funkcje, w tym [Routing komunikatów](./iot-hub-devguide-messages-d2c.md), [przekazywanie plików](./iot-hub-devguide-file-upload.md)i [zbiorcze importowanie/eksportowanie urządzeń](./iot-hub-bulk-identity-mgmt.md) , wymagają także łączności od IoT Hub do zasobu platformy Azure należącego do klienta za pośrednictwem publicznego punktu końcowego. Te ścieżki łączności zbiorowo tworzą ruch wychodzący z IoT Hub do zasobów klientów.

Możesz chcieć ograniczyć łączność z zasobami platformy Azure (w tym IoT Hub) za pośrednictwem sieci wirtualnej, która jest posiadana i działa. Oto następujące przyczyny:

* Wprowadzenie izolacji sieci dla Centrum IoT Hub przez zapobieganie zagrożeniu łączności do publicznego Internetu.

* Umożliwienie łączności prywatnej z zasobów sieci lokalnej dzięki zapewnieniu, że dane i ruch są przekazywane bezpośrednio do sieci szkieletowej platformy Azure.

* Zapobieganie atakom eksfiltracji z poufnych sieci lokalnych. 

* Następujące wzorce łączności dla całej platformy Azure korzystające z [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md).

W tym artykule opisano, jak osiągnąć te cele przy użyciu [prywatnego linku platformy Azure](../private-link/private-link-overview.md) dla łączności przychodzącej do IoT Hub i używania wyjątku usług firmy Microsoft dla ruchu wychodzącego z IoT Hub do innych zasobów platformy Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Połączenia przychodzące do IoT Hub przy użyciu prywatnego linku platformy Azure

Prywatny punkt końcowy jest prywatnym adresem IP przydzielonym wewnątrz sieci wirtualnej należącej do klienta, za pomocą którego jest dostępny zasób platformy Azure. Za pomocą linku prywatnego platformy Azure można skonfigurować prywatny punkt końcowy dla Centrum IoT Hub, aby umożliwić usługom w sieci wirtualnej dostęp do IoT Hub bez konieczności wysyłania ruchu do publicznego punktu końcowego IoT Hub. Podobnie urządzenia lokalne mogą używać [wirtualnej sieci prywatnej (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub komunikacji równorzędnej [ExpressRoute](https://azure.microsoft.com/services/expressroute/) w celu uzyskania łączności z siecią wirtualną i IoT Hub (za pośrednictwem prywatnego punktu końcowego). W związku z tym możesz ograniczyć lub całkowicie zablokować łączność z publicznymi punktami końcowymi usługi IoT Hub przy użyciu [IoT Hub filtr IP](./iot-hub-ip-filtering.md) i [skonfigurować Routing, aby nie wysyłał żadnych danych do wbudowanego punktu końcowego](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint). Takie podejście utrzymuje łączność z koncentratorem przy użyciu prywatnego punktu końcowego dla urządzeń. Głównym fokusem tego Instalatora są urządzenia znajdujące się w sieci lokalnej. Ta konfiguracja nie jest zalecana w przypadku urządzeń wdrożonych w sieci rozległej.

![IoT Hub engress sieci wirtualnej](./media/virtual-network-support/virtual-network-ingress.png)

Przed kontynuowaniem upewnij się, że spełniono następujące wymagania wstępne:

* Utworzono Sieć [wirtualną platformy Azure](../virtual-network/quick-create-portal.md) z podsiecią, w której zostanie utworzony prywatny punkt końcowy.

* W przypadku urządzeń, które działają w sieciach lokalnych, skonfiguruj [wirtualną sieć prywatną (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub prywatną komunikację równorzędną [ExpressRoute](https://azure.microsoft.com/services/expressroute/) w sieci wirtualnej platformy Azure.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Skonfiguruj prywatny punkt końcowy dla IoT Hub ruchu przychodzącego

Prywatny punkt końcowy działa w przypadku IoT Hub interfejsów API urządzeń (takich jak komunikaty z urządzenia do chmury) oraz interfejsów API usługi (takich jak tworzenie i aktualizowanie urządzeń).

1. W Azure Portal wybierz pozycję **Sieć**, **połączenia prywatne punktów końcowych**, a następnie kliknij pozycję **+ prywatny punkt końcowy**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Zrzut ekranu przedstawiający miejsce dodania prywatnego punktu końcowego dla IoT Hub":::

1. Podaj subskrypcję, grupę zasobów, nazwę i region, aby utworzyć nowy prywatny punkt końcowy w programie. W idealnym przypadku należy utworzyć prywatny punkt końcowy w tym samym regionie, w którym znajduje się centrum.

1. Kliknij przycisk **Dalej: zasób**i podaj subskrypcję dla zasobu IoT Hub, a następnie wybierz pozycję **"Microsoft. Devices/IotHubs"** jako typ zasobu, nazwę IoT Hub jako **zasób**, a **iotHub** jako element docelowy.

1. Kliknij przycisk **Dalej: Konfiguracja** i podaj sieć wirtualną i podsieć, aby utworzyć prywatny punkt końcowy w programie. W razie potrzeby wybierz opcję integracji z prywatną strefą DNS platformy Azure.

1. Kliknij przycisk **Dalej: Tagi**i opcjonalnie Podaj wszystkie znaczniki dla zasobu.

1. Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć prywatny zasób linku.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>Wbudowany punkt końcowy zgodny z centrum zdarzeń nie obsługuje dostępu za pośrednictwem prywatnego punktu końcowego

[Wbudowany punkt końcowy zgodny z centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md) nie obsługuje dostępu za pośrednictwem prywatnego punktu końcowego. Po skonfigurowaniu prywatny punkt końcowy Centrum IoT jest przeznaczony tylko do łączności przychodzącej. Używanie danych z wbudowanego punktu końcowego zgodnego z centrum zdarzeń może odbywać się tylko za pośrednictwem publicznego Internetu. 

[Filtr IP](iot-hub-ip-filtering.md) IoT Hub również nie kontroluje publicznego dostępu do wbudowanego punktu końcowego. Aby całkowicie zablokować dostęp do sieci publicznej do centrum IoT, należy: 

1. Konfigurowanie dostępu do prywatnego punktu końcowego dla IoT Hub
1. Wyłącz [dostęp do sieci publicznej](iot-hub-public-network-access.md) lub Użyj filtru IP, aby zablokować wszystkie adresy IP
1. Zatrzymywanie korzystania z wbudowanego punktu końcowego centrum zdarzeń przez [skonfigurowanie routingu, aby nie wysyłał do niego danych](iot-hub-devguide-messages-d2c.md)
1. Wyłącz [trasę rezerwową](iot-hub-devguide-messages-d2c.md#fallback-route)
1. Skonfiguruj ruch wychodzący do innych zasobów platformy Azure przy użyciu [zaufanej usługi firmy Microsoft](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-for-private-link"></a>Cennik linku prywatnego

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Ruch wychodzący z IoT Hub do innych zasobów platformy Azure

IoT Hub może połączyć się z magazynem obiektów blob platformy Azure, centrum zdarzeń, zasobami usługi Service Bus na potrzeby [routingu komunikatów](./iot-hub-devguide-messages-d2c.md), [przekazywania plików](./iot-hub-devguide-file-upload.md)oraz [zbiorczego importowania/eksportowania urządzeń](./iot-hub-bulk-identity-mgmt.md) za pośrednictwem publicznego punktu końcowego zasobów. Powiązanie zasobu z siecią wirtualną domyślnie blokuje łączność z zasobem. W związku z tym taka konfiguracja uniemożliwia IoT Hub pracy podczas wysyłania danych do zasobów. Aby rozwiązać ten problem, Włącz łączność z zasobów usługi IoT Hub do konta magazynu, centrum zdarzeń lub zasobów usługi Service Bus za pomocą opcji **zaufanej usługi firmy Microsoft** .

### <a name="turn-on-managed-identity-for-iot-hub"></a>Włącz tożsamość zarządzaną dla IoT Hub

Aby zezwolić innym usługom na znalezienie Centrum IoT jako zaufanej usługi firmy Microsoft, musi on mieć zarządzaną przez system tożsamość skojarzoną.

1. Przejdź do **tożsamości** w portalu IoT Hub

1. W obszarze **stan**wybierz pozycję **włączone**, a następnie kliknij pozycję **Zapisz**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Zrzut ekranu przedstawiający miejsce dodania prywatnego punktu końcowego dla IoT Hub":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Przypisywanie tożsamości zarządzanej do IoT Hub podczas tworzenia przy użyciu szablonu ARM

Aby przypisać tożsamość zarządzaną do centrum IoT w czasie aprowizacji zasobów, użyj szablonu ARM poniżej:

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

Po podstawianiu wartości dla zasobu, `name` `location` `SKU.name` i `SKU.tier` można użyć interfejsu wiersza polecenia platformy Azure do wdrożenia zasobu w istniejącej grupie zasobów przy użyciu:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Po utworzeniu zasobu można pobrać tożsamość usługi zarządzanej przypisanej do centrum przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Cennik dla tożsamości zarządzanej

Funkcja wyjątków zaufanych usług pierwszej firmy Microsoft jest bezpłatna. Opłaty za zainicjowane konta magazynu, Centra zdarzeń lub zasoby usługi Service Bus są stosowane oddzielnie.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Ruch wychodzący z punktów końcowych konta magazynu dla routingu

IoT Hub może kierować komunikaty do konta magazynu należącego do klienta. Aby umożliwić funkcji routingu dostęp do konta magazynu w trakcie ograniczeń zapory, IoT Hub musi mieć [zarządzaną tożsamość](#turn-on-managed-identity-for-iot-hub). Gdy zarządzana tożsamość zostanie zainicjowana, wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do konta magazynu.

1. W Azure Portal przejdź do karty **kontroli dostępu konta magazynu (IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz opcję **współautor danych obiektu blob magazynu** ([*nie* współautor lub współautor konta magazynu](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) jako **rolę**, **użytkownika usługi Azure AD, grupy lub jednostki usługi** jako **przypisujące dostęp do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** na koncie magazynu i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **routing wiadomości** .

5. Przejdź do sekcji **niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz pozycję **Storage** jako typ punktu końcowego.

6. Na wyświetlonej stronie Podaj nazwę punktu końcowego, wybierz kontener, który ma być używany w magazynie obiektów blob, podaj kodowanie i format nazwy pliku. Wybierz pozycję **tożsamość** jako **Typ uwierzytelniania** w punkcie końcowym magazynu. Kliknij przycisk **Utwórz**.

Teraz niestandardowy punkt końcowy magazynu jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory. Możesz teraz używać tego punktu końcowego do konfigurowania reguły routingu.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Ruch wychodzący z punktów końcowych centrów zdarzeń dla routingu

IoT Hub można skonfigurować w celu kierowania komunikatów do przestrzeni nazw centrów zdarzeń należących do klienta. Aby umożliwić funkcji routingu dostęp do zasobu centrów zdarzeń podczas ograniczeń zapory, IoT Hub musi mieć zarządzaną tożsamość. Po utworzeniu tożsamości zarządzanej wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do centrów zdarzeń.

1. W Azure Portal przejdź do karty kontroli dostępu do centrów zdarzeń **(IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz **Event Hubs nadawcy danych** jako **rolę**, **użytkownika usługi Azure AD, grupę lub jednostkę usługi** jako **przypisujące dostęp do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** w centrach zdarzeń i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do centrów zdarzeń**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **routing wiadomości** .

5. Przejdź do sekcji **niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz pozycję **Centra zdarzeń** jako typ punktu końcowego.

6. Na wyświetlonej stronie Podaj nazwę punktu końcowego, wybierz przestrzeń nazw usługi Event Hub i wystąpienie, a następnie kliknij przycisk **Utwórz** .

Teraz Twój punkt końcowy centrów zdarzeń jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu centrów zdarzeń pomimo ograniczeń zapory. Możesz teraz używać tego punktu końcowego do konfigurowania reguły routingu.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Ruch wychodzący z punktów końcowych usługi Service Bus na potrzeby routingu

IoT Hub można skonfigurować w celu kierowania komunikatów do przestrzeni nazw usługi Service Bus należącej do klienta. Aby umożliwić funkcji routingu dostęp do zasobu usługi Service Bus podczas ograniczeń zapory, IoT Hub musi mieć zarządzaną tożsamość. Gdy zarządzana tożsamość zostanie zainicjowana, wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do usługi Service Bus.

1. W Azure Portal przejdź do karty kontrola dostępu usługi Service Bus **(IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz pozycję **nadawca danych w usłudze Service Bus** jako **rolę**, **użytkownika lub grupę usługi Azure AD** , a następnie wybierz nazwę zasobu w usłudze, **która jest przypisana do** IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** w usłudze Service Bus i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tej usługi Service Bus**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **routing wiadomości** .

5. Przejdź do sekcji **niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz **kolejno pozycje Usługa Service Bus** lub **temat Service Bus** (zgodnie z opisem) jako typ punktu końcowego.

6. Na wyświetlonej stronie Podaj nazwę punktu końcowego, wybierz swoją przestrzeń nazw usługi Service Bus i kolejkę lub temat (stosownie do potrzeb). Kliknij przycisk **Utwórz**.

Teraz niestandardowy punkt końcowy usługi Service Bus jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu usługi Service Bus pomimo ograniczeń zapory. Możesz teraz używać tego punktu końcowego do konfigurowania reguły routingu.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Wyjście z łączności z kontami magazynu na potrzeby przekazywania plików

Funkcja przekazywania plików IoT Hub umożliwia urządzeniom przekazywanie plików do konta magazynu należącego do klienta. Aby umożliwić przekazywanie plików do funkcji, oba urządzenia i IoT Hub muszą mieć łączność z kontem magazynu. Jeśli ograniczenia dotyczące zapory są stosowane na koncie magazynu, urządzenia muszą używać dowolnego mechanizmu obsługiwanego konta magazynu (w tym [prywatnych punktów końcowych](../private-link/create-private-endpoint-storage-portal.md), [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md)lub [bezpośredniej konfiguracji zapory](../storage/common/storage-network-security.md)) w celu uzyskania łączności. Analogicznie, jeśli istnieją ograniczenia dotyczące zapory na koncie magazynu, IoT Hub należy skonfigurować w celu uzyskania dostępu do zasobu magazynu za pomocą zaufanego wyjątku usług firmy Microsoft. W tym celu IoT Hub musi mieć zarządzaną tożsamość. Gdy zarządzana tożsamość zostanie zainicjowana, wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do konta magazynu.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. W Azure Portal przejdź do karty **kontroli dostępu konta magazynu (IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz opcję **współautor danych obiektu blob magazynu** ([*nie* współautor lub współautor konta magazynu](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) jako **rolę**, **użytkownika usługi Azure AD, grupy lub jednostki usługi** jako **przypisujące dostęp do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** na koncie magazynu i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów IoT Hub przejdź do karty **przekazywanie plików** .

5. Na wyświetlonej stronie Wybierz kontener, który ma być używany w magazynie obiektów blob, w razie potrzeby skonfiguruj **Ustawienia powiadomień dotyczących plików**, **czas wygaśnięcia sygnatury dostępu współdzielonego**, **domyślny czas wygaśnięcia**i **maksymalną liczbę dostaw** . Wybierz pozycję **tożsamość** jako **Typ uwierzytelniania** w punkcie końcowym magazynu. Kliknij przycisk **Utwórz**. Jeśli w tym kroku zostanie wyświetlony błąd, tymczasowo Ustaw konto magazynu tak, aby zezwalało na dostęp ze **wszystkich sieci**, a następnie spróbuj ponownie. Po zakończeniu konfigurowania przekazywania plików można skonfigurować zaporę na koncie magazynu.

Teraz punkt końcowy magazynu dla przekazywania plików jest skonfigurowany do korzystania z tożsamości przypisanej do systemu centrum i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Wychodzące połączenia z kontami magazynu na potrzeby importowania/eksportowania urządzeń zbiorczych

IoT Hub obsługuje funkcje do [importowania/eksportowania](./iot-hub-bulk-identity-mgmt.md) informacji o urządzeniach zbiorczo z/do magazynu obiektów BLOB dostarczonych przez klienta. Aby umożliwić korzystanie z funkcji importu/eksportu zbiorczego, oba urządzenia i IoT Hub muszą mieć łączność z kontem magazynu.

Ta funkcja wymaga łączności IoT Hub z kontem magazynu. Aby uzyskać dostęp do zasobu usługi Service Bus podczas ograniczeń zapory, IoT Hub musi mieć zarządzaną tożsamość. Gdy zarządzana tożsamość zostanie zainicjowana, wykonaj poniższe kroki, aby udzielić kontroli RBAC tożsamości zasobu centrum w celu uzyskania dostępu do usługi Service Bus.

1. W Azure Portal przejdź do karty **kontroli dostępu konta magazynu (IAM)** , a następnie kliknij przycisk **Dodaj** w sekcji **Dodawanie przypisania roli** .

2. Wybierz opcję **współautor danych obiektu blob magazynu** ([*nie* współautor lub współautor konta magazynu](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) jako **rolę**, **użytkownika usługi Azure AD, grupy lub jednostki usługi** jako **przypisujące dostęp do** i wybierz nazwę zasobu IoT Hub na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **zapory i sieci wirtualne** na koncie magazynu i Włącz opcję **Zezwalaj na dostęp z wybranych sieci** . Na liście **wyjątków** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

Za pomocą interfejsów API REST usługi Azure IoT można teraz [tworzyć zadania importowania eksportu](https://docs.microsoft.com/rest/api/iothub/service/jobs/getimportexportjobs) , aby uzyskać informacje na temat korzystania z funkcji zbiorczego importowania/eksportowania. Musisz podać `storageAuthenticationType="identityBased"` w treści żądania, a `inputBlobContainerUri="https://..."` `outputBlobContainerUri="https://..."` także jako wejściowe i wyjściowe adresy URL konta magazynu.

Zestawy SDK platformy Azure IoT Hub obsługują również tę funkcję w Menedżerze rejestru klienta usługi. Poniższy fragment kodu przedstawia sposób inicjowania zadania importowania lub eksportowania zadania w programie przy użyciu zestawu C# SDK.

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

1. Utwórz zmienną środowiskową o nazwie `EnableStorageIdentity` i ustaw jej wartość na `1` .

2. Pobierz zestaw SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
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
