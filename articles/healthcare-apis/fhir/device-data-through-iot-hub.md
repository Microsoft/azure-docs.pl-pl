---
title: 'Samouczek: odbieranie danych urządzenia za pośrednictwem Azure IoT Hub'
description: Z tego samouczka dowiesz się, jak włączyć routing danych urządzenia z sieci IoT Hub do sieci Azure API for FHIR za pośrednictwem usługi Azure IoT Connector for FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 77d54ef0c9bef40af47c2fc48b9d4b5d6315b119
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780399"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Samouczek: odbieranie danych urządzenia za pośrednictwem Azure IoT Hub

Usługa Azure IoT Connector for projekt standardu Fast Healthcare Interoperability Resources (FHIR&#174;)* umożliwia pozysowanie danych z Internetu rzeczy medycznych (IoMT) do urządzeń Azure API for FHIR. W przewodniku Szybki start Deploy [Azure IoT Connector for FHIR (preview)](iot-fhir-portal-quickstart.md) using Azure Portal (Wdrażanie [](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) usługi Azure IoT Connector for FHIR )) został wyświetlony przykład urządzenia zarządzanego przez usługę Azure IoT Central wysyłającego dane telemetryczne do usługi Azure IoT Connector for FHIR. Usługa Azure IoT Connector for FHIR może również pracować z urządzeniami aprowizowanych i zarządzanymi za pośrednictwem Azure IoT Hub. Ten samouczek zawiera procedurę łączenia i rozsyłania danych urządzenia z Azure IoT Hub do usługi Azure IoT Connector for FHIR.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR zasobów z co najmniej jedną usługą Azure IoT Connector for FHIR — wdrażanie usługi [Azure IoT Connector for FHIR (wersja zapoznawcza) przy użyciu Azure Portal](iot-fhir-portal-quickstart.md)
- Azure IoT Hub zasobów połączonych za pomocą rzeczywistych lub symulowanych urządzeń — tworzenie [centrum IoT hub przy użyciu Azure Portal](../../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Jeśli używasz aplikacji Azure IoT Hub symulowanego urządzenia, możesz wybrać aplikację spośród różnych obsługiwanych języków i systemów.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Uzyskiwanie parametrów połączenia dla usługi Azure IoT Connector for FHIR (wersja zapoznawcza)

Azure IoT Hub wymaga parametrów połączenia, aby bezpiecznie nawiązać połączenie z usługą Azure IoT Connector for FHIR. Utwórz nowe ciągi połączenia dla usługi Azure IoT Connector for FHIR zgodnie z opisem w teście [Generowanie parametrów połączenia.](iot-fhir-portal-quickstart.md#generate-a-connection-string) Zachowaj te ciągi połączenia, które będą używane w następnym kroku.

Usługa Azure IoT Connector for FHIR używa wystąpienia usługi Azure Event Hub pod maską do odbierania komunikatów urządzeń. Utworzone powyżej ciągi połączenia są zasadniczo ciągami połączenia z tym bazowym centrum zdarzeń.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Łączenie Azure IoT Hub z usługą Azure IoT Connector for FHIR (wersja zapoznawcza)

Azure IoT Hub obsługuje funkcję o [](../../iot-hub/iot-hub-devguide-messages-d2c.md) nazwie routing komunikatów, która umożliwia wysyłanie danych urządzeń do różnych usług platformy Azure, takich jak Centrum zdarzeń, Konto magazynu i Service Bus. Usługa Azure IoT Connector for FHIR korzysta z tej funkcji do łączenia i wysyłania danych urządzenia z Azure IoT Hub do punktu końcowego centrum zdarzeń.

> [!NOTE] 
> Obecnie do tworzenia routingu komunikatów można [](../../iot-hub/tutorial-routing.md) używać tylko programu PowerShell lub polecenia interfejsu wiersza polecenia, ponieważ usługa Azure IoT Connector dla centrum zdarzeń platformy FHIR nie jest hostowana w subskrypcji klienta, dlatego nie będzie ona widoczna za pośrednictwem Azure Portal. Jednak po dodaniu obiektów trasy komunikatów przy użyciu programu PowerShell lub interfejsu wiersza polecenia są one widoczne na Azure Portal i można nimi zarządzać z tego środowiska.

Konfigurowanie routingu komunikatów składa się z dwóch kroków.

### <a name="add-an-endpoint"></a>Dodawanie punktu końcowego
Ten krok definiuje punkt końcowy, do którego IoT Hub przekierowywuje dane. Utwórz ten punkt końcowy przy użyciu polecenia [Add-AzIotHubRoutingEndpoint programu](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell lub [polecenia az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create) interfejsu wiersza polecenia zgodnie z preferencjami.

Oto lista parametrów do użycia z poleceniem w celu utworzenia punktu końcowego:

|Parametr programu PowerShell|Parametr interfejsu wiersza polecenia|Opis|
|---|---|---|
|ResourceGroupName|resource-group|Nazwa grupy zasobów zasobu IoT Hub zasobów.|
|Nazwa|nazwa centrum|Nazwa zasobu IoT Hub zasobów.|
|Nazwa punktu końcowego|nazwa punktu końcowego|Użyj nazwy, która ma zostać przypisana do tworzonego punktu końcowego.|
|Endpointtype|typ punktu końcowego|Typ punktu końcowego, z IoT Hub nawiązać połączenie. Użyj wartości literału "EventHub" dla programu PowerShell i "eventhub" dla interfejsu wiersza polecenia.|
|EndpointResourceGroup|endpoint-resource-group|Nazwa grupy zasobów dla usługi Azure IoT Connector dla zasobu Azure API for FHIR FHIR. Tę wartość można uzyskać na stronie Przegląd Azure API for FHIR.|
|EndpointSubscriptionId|identyfikator subskrypcji punktu końcowego|Identyfikator subskrypcji usługi Azure IoT Connector dla zasobu Azure API for FHIR FHIR. Tę wartość można uzyskać na stronie Przegląd Azure API for FHIR.|
|Connectionstring|połączenie — parametry|Parametrów połączenia z usługą Azure IoT Connector for FHIR. Użyj wartości uzyskanej w poprzednim kroku.|

### <a name="add-a-message-route"></a>Dodawanie trasy komunikatów
Ten krok definiuje trasę komunikatów przy użyciu utworzonego powyżej punktu końcowego. Utwórz trasę za pomocą polecenia [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) programu PowerShell lub [polecenia az iot hub route create](/cli/azure/iot/hub/route#az_iot_hub_route_create) interfejsu wiersza polecenia zgodnie z preferencjami.

Oto lista parametrów do użycia z poleceniem w celu dodania trasy komunikatu:

|Parametr programu PowerShell|Parametr interfejsu wiersza polecenia|Opis|
|---|---|---|
|ResourceGroupName|g|Nazwa grupy zasobów zasobu IoT Hub zasobów.|
|Nazwa|nazwa centrum|Nazwa zasobu IoT Hub zasobów.|
|Nazwa punktu końcowego|nazwa punktu końcowego|Nazwa utworzonego powyżej punktu końcowego.|
|RouteName|route-name|Nazwa, którą chcesz przypisać do tworzonej trasy komunikatów.|
|Element źródłowy|typ-źródłowy|Typ danych do wysłania do punktu końcowego. Użyj wartości literału "DeviceMessages" dla programu PowerShell i "devicemessages" dla interfejsu wiersza polecenia.|

## <a name="send-device-message-to-iot-hub"></a>Wysyłanie komunikatu urządzenia do IoT Hub

Użyj urządzenia (rzeczywistego lub symulowanego), aby wysłać przykładowy komunikat o akcji serca pokazany poniżej do Azure IoT Hub. Ten komunikat zostanie przekierowyny do usługi Azure IoT Connector for FHIR, gdzie komunikat zostanie przekształcony w zasób FHIR Observation i zapisany w Azure API for FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Pamiętaj, aby wysłać komunikat urządzenia [](iot-mapping-templates.md) zgodny z szablonami mapowania skonfigurowanymi w usłudze Azure IoT Connector for FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Wyświetlanie danych urządzenia w Azure API for FHIR

Możesz wyświetlić zasoby FHIR Observation utworzone przez usługę Azure IoT Connector for FHIR na Azure API for FHIR narzędzia Postman. Skonfiguruj program [Postman](access-fhir-postman-tutorial.md) w celu uzyskania dostępu do Azure API for FHIR i zażądaj wyświetlenia zasobów observation FHIR z wartością serca przesłaną w `GET` powyższym `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` przykładowym komunikacie.

> [!TIP]
> Upewnij się, że użytkownik ma odpowiedni dostęp do Azure API for FHIR danych. Użyj [kontroli dostępu opartej na rolach (RBAC)](configure-azure-rbac.md) platformy Azure, aby przypisać wymagane role płaszczyzny danych.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz usługę Azure IoT Hub do usługi Azure IoT Connector for FHIR. Wybierz poniższe kroki, aby dowiedzieć się więcej o usłudze Azure IoT Connector for FHIR:

Poznaj różne etapy przepływu danych w usłudze Azure IoT Connector for FHIR.

>[!div class="nextstepaction"]
>[Usługa Azure IoT Connector dla przepływu danych FHIR](iot-data-flow.md)

Dowiedz się, jak skonfigurować IoT Connector przy użyciu szablonów mapowania urządzenia i FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR mapping templates (Usługa Azure IoT Connector dla szablonów mapowań FHIR](iot-mapping-templates.md)

*W witrynie Azure Portal usługa Azure IoT Connector for FHIR jest nazywana IoT Connector (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym firmy HL7 i jest używany za zgodą hl7.