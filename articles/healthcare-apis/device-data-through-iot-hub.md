---
title: 'Samouczek: odbieranie danych urządzenia za pomocą usługi Azure IoT Hub'
description: W tym samouczku dowiesz się, jak włączyć routing danych urządzeń z IoT Hub do interfejsu API platformy Azure dla FHIR za pośrednictwem łącznika IoT.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: 95e3e2be175fa810b1b966a7dda5a0e53e23d780
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536737"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Samouczek: odbieranie danych urządzenia za pomocą usługi Azure IoT Hub

Łącznik IoT oferuje możliwość pozyskiwania danych z urządzeń z Internetu (IoMT) w usłudze Azure API dla FHIR. [Łącznik wdrażania IoT (wersja zapoznawcza) korzystający z narzędzia Azure Portal](iot-fhir-portal-quickstart.md) szybki start przedstawia przykład urządzenia zarządzanego przez platformę Azure IoT Central [wysyłanie danych telemetrycznych](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) do łącznika IoT. Łącznik IoT może również współdziałać z urządzeniami, które są obsługiwane i zarządzane za pomocą usługi Azure IoT Hub. Ten samouczek zawiera procedurę łączenia i kierowania danych urządzenia z usługi Azure IoT Hub do łącznika IoT.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Interfejs API platformy Azure dla zasobu FHIR z co najmniej jednym łącznikiem IoT — [wdrażanie łącznika IoT (wersja zapoznawcza) przy użyciu Azure Portal](iot-fhir-portal-quickstart.md)
- Zasób platformy Azure IoT Hub połączony z rzeczywistymi lub symulowanymi urządzeniami — [Tworzenie Centrum IoT Hub przy użyciu Azure Portal](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Jeśli używasz aplikacji symulowanego urządzenia IoT Hub platformy Azure, możesz wybrać wybraną aplikację spośród różnych obsługiwanych języków i systemów.

## <a name="get-connection-string-for-iot-connector-preview"></a>Pobierz parametry połączenia dla łącznika IoT (wersja zapoznawcza)

Usługa Azure IoT Hub wymaga parametrów połączenia, aby bezpiecznie połączyć się z łącznikiem IoT. Utwórz nowe parametry połączenia dla łącznika IoT, zgodnie z opisem w artykule [generowanie parametrów połączenia](iot-fhir-portal-quickstart.md#generate-a-connection-string). Zachowaj te parametry połączenia, aby można było używać ich w następnym kroku.

Łącznik IoT używa wystąpienia usługi Azure Event Hub pod okapem do odbierania komunikatów o urządzeniach. Parametry połączenia utworzone powyżej są zasadniczo parametrami połączenia do tego podstawowego centrum zdarzeń.

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>Łączenie IoT Hub platformy Azure z łącznikiem IoT (wersja zapoznawcza)

Usługa Azure IoT Hub obsługuje funkcję o nazwie [Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) , która zapewnia możliwość wysyłania danych urządzenia do różnych usług platformy Azure, takich jak centrum zdarzeń, konto magazynu i Service Bus. Łącznik IoT korzysta z tej funkcji, aby połączyć i wysłać dane urządzenia z usługi Azure IoT Hub do punktu końcowego centrum zdarzeń.

> [!NOTE] 
> Teraz można użyć programu PowerShell lub interfejsu wiersza polecenia do [tworzenia routingu wiadomości](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) , ponieważ centrum zdarzeń łącznika usługi IoT nie jest hostowane w subskrypcji klienta, dlatego nie będzie ono widoczne przez Azure Portal. Chociaż po dodaniu obiektów tras komunikatów przy użyciu programu PowerShell lub interfejsu wiersza polecenia są one widoczne w Azure Portal i można z niej zarządzać.

Skonfigurowanie routingu wiadomości składa się z dwóch kroków.

### <a name="add-an-endpoint"></a>Dodawanie punktu końcowego
Ten krok definiuje punkt końcowy, do którego IoT Hub będzie kierować dane. Utwórz ten punkt końcowy przy użyciu polecenia [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) środowiska PowerShell lub [AZ IoT Hub Routing-Endpoint Create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) interfejsu wiersza polecenia na podstawie preferencji.

Poniżej znajduje się lista parametrów do użycia z poleceniem do utworzenia punktu końcowego:

|Parametr programu PowerShell|Parametr interfejsu wiersza polecenia|Opis|
|---|---|---|
|ResourceGroupName|resource-group|Nazwa grupy zasobów dla zasobu IoT Hub.|
|Nazwa|Nazwa centrum|Nazwa zasobu IoT Hub.|
|Nazwapunktukoncowego|Nazwa punktu końcowego|Użyj nazwy, która ma zostać przypisana do tworzonego punktu końcowego.|
|Punkt końcowy|Typ punktu końcowego|Typ punktu końcowego, który IoT Hub musi nawiązać połączenie. Użyj wartości literału "EventHub" dla programu PowerShell i "eventhub" dla interfejsu wiersza polecenia.|
|EndpointResourceGroup|punkt końcowy — Grupa zasobów|Nazwa grupy zasobów dla interfejsu API platformy Azure usługi IoT Connector dla zasobu FHIR. Tę wartość można uzyskać na stronie Przegląd interfejsu API platformy Azure dla FHIR.|
|EndpointSubscriptionId|punkt końcowy — Identyfikator subskrypcji|Identyfikator subskrypcji dla interfejsu API platformy Azure usługi IoT Connector dla zasobu FHIR. Tę wartość można uzyskać na stronie Przegląd interfejsu API platformy Azure dla FHIR.|
|Przekształcon|połączenie — parametry|Parametry połączenia z łącznikiem IoT. Użyj wartości uzyskanej w poprzednim kroku.|

### <a name="add-a-message-route"></a>Dodawanie trasy wiadomości
Ten krok definiuje trasę komunikatów przy użyciu punktu końcowego utworzonego powyżej. Utwórz trasę przy użyciu polecenia [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) środowiska PowerShell lub [AZ IoT Hub Route Create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) interfejsu wiersza polecenia na podstawie preferencji.

Poniżej znajduje się lista parametrów do użycia z poleceniem do utworzenia punktu końcowego:

|Parametr programu PowerShell|Parametr interfejsu wiersza polecenia|Opis|
|---|---|---|
|ResourceGroupName|g|Nazwa grupy zasobów dla zasobu IoT Hub.|
|Nazwa|Nazwa centrum|Nazwa zasobu IoT Hub.|
|Nazwapunktukoncowego|Nazwa punktu końcowego|Nazwa utworzonego punktu końcowego.|
|RouteName|Nazwa trasy|Nazwa, która ma zostać przypisana do tworzonej trasy wiadomości.|
|Element źródłowy|Typ źródła|Typ danych do wysłania do punktu końcowego. Użyj wartości literału "DeviceMessages" dla programu PowerShell i "DeviceMessages" dla interfejsu wiersza polecenia.|

## <a name="send-device-message-to-iot-hub"></a>Wyślij komunikat urządzenia do IoT Hub

Użyj urządzenia (prawdziwe lub symulowane), aby wysłać przykładowy komunikat o współczynniku serca przedstawiony poniżej do usługi Azure IoT Hub. Ten komunikat zostanie rozesłany do łącznika IoT, w którym komunikat zostanie przekształcony w zaFHIR obserwacji i zapisany w interfejsie API platformy Azure dla FHIR.

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
> Upewnij się, że wysłano komunikat urządzenia, który jest zgodny z [szablonami mapowania](iot-mapping-templates.md) skonfigurowanymi przy użyciu łącznika IoT.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Wyświetlanie danych urządzenia w usłudze Azure API for FHIR

Można wyświetlić FHIRe zasoby, które zostały utworzone przez łącznik IoT w interfejsie API platformy Azure dla FHIR przy użyciu programu Poster. Skonfiguruj swoje [ogłoszenie, aby uzyskać dostęp do interfejsu API platformy Azure dla usługi FHIR](access-fhir-postman-tutorial.md) , a następnie `GET` Wyślij żądanie, aby `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` wyświetlić obserwacje zasobów FHIR z wartością współczynnika serca przesłaną w powyższym przykładowym komunikacie.

> [!TIP]
> Upewnij się, że Twój usx'er ma odpowiedni dostęp do interfejsu API platformy Azure dla FHIR płaszczyzny danych. Użyj [Access Control opartej na rolach platformy Azure](configure-azure-rbac.md) , aby przypisać wymagane role płaszczyzny danych.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start opisano konfigurowanie usługi Azure IoT Hub w celu kierowania danych urządzenia do łącznika IoT. Wybierz spośród kolejnych kroków, aby dowiedzieć się więcej o łączniku IoT:

Poznaj różne etapy przepływu danych w ramach łącznika IoT.

>[!div class="nextstepaction"]
>[Przepływ danych łącznika IoT](iot-data-flow.md)

Dowiedz się, jak skonfigurować łącznik usługi IoT przy użyciu szablonów mapowania urządzeń i FHIR.

>[!div class="nextstepaction"]
>[Szablony mapowania łącznika IoT](iot-mapping-templates.md)

FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.

