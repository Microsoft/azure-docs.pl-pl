---
title: 'Samouczek: odbieranie danych urządzenia za pomocą usługi Azure IoT Hub'
description: W tym samouczku dowiesz się, jak włączyć routing danych urządzeń z IoT Hub do interfejsu API platformy Azure dla FHIR za pośrednictwem łącznika usługi Azure IoT dla FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 6c364cf84bada2a951ef3f224ea836885f0e3c1e
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636320"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Samouczek: odbieranie danych urządzenia za pomocą usługi Azure IoT Hub

Łącznik usługi Azure IoT dla szybkich zasobów współdziałania (FHIR&#174;) * oferuje możliwość pozyskiwania danych z urządzeń z Internetu (IoMT) do usługi Azure API dla FHIR. [Łącznik wdrażania usługi Azure IoT dla FHIR (wersja zapoznawcza) korzystający z narzędzia Azure Portal](iot-fhir-portal-quickstart.md) szybki start przedstawia przykład urządzenia zarządzanego przez platformę Azure IoT Central [wysyłanie danych telemetrycznych](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) do łącznika usługi Azure IoT dla FHIR. Łącznik usługi Azure IoT dla FHIR może również współdziałać z urządzeniami, które są obsługiwane i zarządzane za pomocą usługi Azure IoT Hub. Ten samouczek zawiera opis procedury łączenia i kierowania danych urządzenia z usługi IoT Hub Azure FHIR do usługi Azure IoT Connector dla.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Interfejs API platformy Azure dla zasobu FHIR z co najmniej jednym łącznikiem usługi Azure IoT dla FHIR — [wdrażanie łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza) przy użyciu Azure Portal](iot-fhir-portal-quickstart.md)
- Zasób platformy Azure IoT Hub połączony z rzeczywistymi lub symulowanymi urządzeniami — [Tworzenie Centrum IoT Hub przy użyciu Azure Portal](../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Jeśli używasz aplikacji symulowanego urządzenia IoT Hub platformy Azure, możesz wybrać wybraną aplikację spośród różnych obsługiwanych języków i systemów.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Pobierz parametry połączenia dla łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)

Usługa Azure IoT Hub wymaga parametrów połączenia, aby bezpiecznie połączyć się z łącznikiem usługi Azure IoT dla FHIR. Utwórz nowe parametry połączenia dla łącznika usługi Azure IoT dla FHIR zgodnie z opisem w artykule [generowanie parametrów połączenia](iot-fhir-portal-quickstart.md#generate-a-connection-string). Zachowaj te parametry połączenia, aby można było używać ich w następnym kroku.

Łącznik usługi Azure IoT dla programu FHIR używa wystąpienia centrum zdarzeń platformy Azure pod okapem do odbierania komunikatów o urządzeniach. Parametry połączenia utworzone powyżej są zasadniczo parametrami połączenia do tego podstawowego centrum zdarzeń.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Łączenie IoT Hub platformy Azure z łącznikiem usługi Azure IoT dla FHIR (wersja zapoznawcza)

Usługa Azure IoT Hub obsługuje funkcję o nazwie [Routing komunikatów](../iot-hub/iot-hub-devguide-messages-d2c.md) , która zapewnia możliwość wysyłania danych urządzenia do różnych usług platformy Azure, takich jak centrum zdarzeń, konto magazynu i Service Bus. Łącznik usługi Azure IoT dla programu FHIR korzysta z tej funkcji, aby połączyć i wysłać dane urządzenia z usługi Azure IoT Hub do punktu końcowego centrum zdarzeń.

> [!NOTE] 
> Teraz można użyć programu PowerShell lub interfejsu wiersza polecenia do [tworzenia routingu wiadomości](../iot-hub/tutorial-routing.md) , ponieważ łącznik usługi Azure IoT dla centrum zdarzeń FHIR nie jest obsługiwany w ramach subskrypcji klienta, dlatego nie będzie on widoczny przez Azure Portal. Chociaż po dodaniu obiektów tras komunikatów przy użyciu programu PowerShell lub interfejsu wiersza polecenia są one widoczne w Azure Portal i można z niej zarządzać.

Skonfigurowanie routingu wiadomości składa się z dwóch kroków.

### <a name="add-an-endpoint"></a>Dodawanie punktu końcowego
Ten krok definiuje punkt końcowy, do którego IoT Hub będzie kierować dane. Utwórz ten punkt końcowy przy użyciu polecenia [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) środowiska PowerShell lub [AZ IoT Hub Routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create) interfejsu wiersza polecenia na podstawie preferencji.

Poniżej znajduje się lista parametrów do użycia z poleceniem do utworzenia punktu końcowego:

|Parametr programu PowerShell|Parametr interfejsu wiersza polecenia|Opis|
|---|---|---|
|ResourceGroupName|resource-group|Nazwa grupy zasobów dla zasobu IoT Hub.|
|Nazwa|Nazwa centrum|Nazwa zasobu IoT Hub.|
|Nazwapunktukoncowego|Nazwa punktu końcowego|Użyj nazwy, która ma zostać przypisana do tworzonego punktu końcowego.|
|Punkt końcowy|Typ punktu końcowego|Typ punktu końcowego, który IoT Hub musi nawiązać połączenie. Użyj wartości literału "EventHub" dla programu PowerShell i "eventhub" dla interfejsu wiersza polecenia.|
|EndpointResourceGroup|punkt końcowy — Grupa zasobów|Nazwa grupy zasobów dla łącznika usługi Azure IoT dla FHIR Azure API dla zasobu FHIR. Tę wartość można uzyskać na stronie Przegląd interfejsu API platformy Azure dla FHIR.|
|EndpointSubscriptionId|punkt końcowy — Identyfikator subskrypcji|Identyfikator subskrypcji łącznika usługi Azure IoT dla FHIR Azure API dla zasobu FHIR. Tę wartość można uzyskać na stronie Przegląd interfejsu API platformy Azure dla FHIR.|
|Przekształcon|połączenie — parametry|Parametry połączenia z łącznikiem usługi Azure IoT dla FHIR. Użyj wartości uzyskanej w poprzednim kroku.|

### <a name="add-a-message-route"></a>Dodawanie trasy wiadomości
Ten krok definiuje trasę komunikatów przy użyciu punktu końcowego utworzonego powyżej. Utwórz trasę przy użyciu polecenia [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) środowiska PowerShell lub [AZ IoT Hub Route Create](/cli/azure/iot/hub/route#az-iot-hub-route-create) interfejsu wiersza polecenia na podstawie preferencji.

Poniżej znajduje się lista parametrów do użycia z poleceniem dodawania trasy wiadomości:

|Parametr programu PowerShell|Parametr interfejsu wiersza polecenia|Opis|
|---|---|---|
|ResourceGroupName|g|Nazwa grupy zasobów dla zasobu IoT Hub.|
|Nazwa|Nazwa centrum|Nazwa zasobu IoT Hub.|
|Nazwapunktukoncowego|Nazwa punktu końcowego|Nazwa utworzonego punktu końcowego.|
|RouteName|Nazwa trasy|Nazwa, która ma zostać przypisana do tworzonej trasy wiadomości.|
|Element źródłowy|Typ źródła|Typ danych do wysłania do punktu końcowego. Użyj wartości literału "DeviceMessages" dla programu PowerShell i "DeviceMessages" dla interfejsu wiersza polecenia.|

## <a name="send-device-message-to-iot-hub"></a>Wyślij komunikat urządzenia do IoT Hub

Użyj urządzenia (prawdziwe lub symulowane), aby wysłać przykładowy komunikat o współczynniku serca przedstawiony poniżej do usługi Azure IoT Hub. Ten komunikat zostanie rozesłany do łącznika usługi Azure IoT dla programu FHIR, w którym komunikat zostanie przekształcony w zasób FHIR i zapisany w interfejsie API platformy Azure dla FHIR.

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
> Upewnij się, że wysłano komunikat urządzenia, który jest zgodny z [szablonami mapowania](iot-mapping-templates.md) skonfigurowanymi przy użyciu łącznika usługi Azure IoT dla FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Wyświetlanie danych urządzenia w usłudze Azure API for FHIR

Można wyświetlić FHIR widoczne zasoby utworzone przez łącznik usługi Azure IoT dla FHIR w interfejsie API platformy Azure dla FHIR przy użyciu programu Poster. Skonfiguruj swoje [ogłoszenie, aby uzyskać dostęp do interfejsu API platformy Azure dla usługi FHIR](access-fhir-postman-tutorial.md) , a następnie `GET` Wyślij żądanie, aby `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` wyświetlić obserwacje zasobów FHIR z wartością współczynnika serca przesłaną w powyższym przykładowym komunikacie.

> [!TIP]
> Upewnij się, że użytkownik ma odpowiednie uprawnienia dostępu do interfejsu API platformy Azure dla FHIRej płaszczyzny danych. Aby przypisać wymagane role płaszczyzny danych, użyj [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](configure-azure-rbac.md) .


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start opisano konfigurowanie usługi Azure IoT Hub do kierowania danych urządzenia do łącznika usługi Azure IoT dla FHIR. Wybierz spośród kolejnych kroków, aby dowiedzieć się więcej o łączniku usługi Azure IoT dla FHIR:

Poznaj różne etapy przepływu danych w ramach łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla przepływu danych FHIR](iot-data-flow.md)

Dowiedz się, jak skonfigurować łącznik usługi IoT przy użyciu szablonów mapowania urządzeń i FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla szablonów mapowania FHIR](iot-mapping-templates.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7.