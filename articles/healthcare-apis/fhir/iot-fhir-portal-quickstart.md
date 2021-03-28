---
title: 'Szybki Start: wdrażanie łącznika usługi Azure IoT for FHIR (wersja zapoznawcza) przy użyciu Azure Portal'
description: W tym przewodniku szybki start dowiesz się, jak wdrażać, konfigurować i używać łącznika usługi Azure IoT dla funkcji FHIR w interfejsie API platformy Azure dla FHIR przy użyciu Azure Portal.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 3e293782e6f00852a51e0617a07eebd5d8c56261
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644851"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Szybki Start: wdrażanie łącznika usługi Azure IoT for FHIR (wersja zapoznawcza) przy użyciu Azure Portal

Łącznik usługi Azure IoT dla szybkich zasobów współdziałania (FHIR&#174;) * to opcjonalna funkcja interfejsu API platformy Azure dla FHIR, która zapewnia możliwość pozyskiwania danych z Internetu (IoMT) urządzeń. W fazie zapoznawczej Funkcja łącznika usługi Azure IoT dla FHIR jest dostępna bezpłatnie. W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:
- Wdrażanie i Konfigurowanie łącznika usługi Azure IoT dla FHIR przy użyciu Azure Portal
- Wysyłanie danych do łącznika usługi Azure IoT dla FHIR za pomocą symulowanego urządzenia
- Wyświetlanie zasobów utworzonych przez łącznik usługi Azure IoT dla FHIR w interfejsie API platformy Azure dla FHIR

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR — [wdrażanie interfejsu API platformy Azure dla FHIR przy użyciu Azure Portal](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Przejdź do interfejsu API platformy Azure dla zasobu FHIR

Otwórz [Azure Portal](https://portal.azure.com) i przejdź do zasobu **Azure API for FHIR** , dla którego chcesz utworzyć łącznik usługi Azure IoT dla funkcji FHIR.

[![Interfejs API platformy Azure dla zasobu FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

W menu nawigacji po lewej stronie kliknij pozycję **Łącznik IoT (wersja zapoznawcza)** w sekcji **Dodatki** , aby otworzyć stronę **Łączniki IoT** .

[![Funkcja łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Tworzenie nowego łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)

Kliknij przycisk **Dodaj** , aby otworzyć stronę **Tworzenie łącznika IoT** .

[![Dodawanie łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Wprowadź ustawienia dla nowego łącznika usługi Azure IoT dla FHIR. Kliknij przycisk **Utwórz** , a następnie zaczekaj na wdrożenie łącznika usługi Azure IoT dla FHIR.

> [!NOTE]
> Należy wybrać opcję **Utwórz** jako wartość dla listy rozwijanej **Typ rozwiązania** dla tej instalacji. 

[![Utwórz łącznik IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Ustawienie|Wartość|Opis |
|---|---|---|
|Nazwa łącznika|Unikatowa nazwa|Wprowadź nazwę identyfikującą łącznik usługi Azure IoT dla FHIR. Ta nazwa powinna być unikatowa w ramach interfejsu API platformy Azure dla zasobu FHIR. Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zaczynać się i kończyć literą lub cyfrą, a musi mieć długość od 3-24 znaków.|
|Typ rozwiązania|Odnośnik lub Utwórz|Wybierz pozycję **Wyszukaj** , jeśli masz proces poza pasmem, aby utworzyć zasoby FHIR [urządzenia](https://www.hl7.org/fhir/device.html) i [pacjenta](https://www.hl7.org/fhir/patient.html) w interfejsie API platformy Azure dla FHIR. Łącznik usługi Azure IoT dla FHIR będzie używać odwołań do tych zasobów podczas tworzenia [obserwacyjnego](https://www.hl7.org/fhir/observation.html) zasobu FHIR do reprezentowania danych urządzenia. Wybierz pozycję **Utwórz** , jeśli chcesz, aby usługa Azure IoT Connector FHIR tworzenie urządzeń bez systemu operacyjnego i zasobów pacjenta w interfejsie API platformy Azure dla FHIR przy użyciu odpowiednich wartości identyfikatora zawartych w danych urządzenia.|

Po zakończeniu instalacji zostanie wyświetlony nowo utworzony łącznik usługi Azure IoT dla FHIR na stronie **Łączniki IoT** .

[![Utworzono łącznik IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Konfigurowanie łącznika usługi Azure IoT for FHIR (wersja zapoznawcza)

Łącznik usługi Azure IoT dla FHIR potrzebuje dwóch szablonów mapowania do przekształcania komunikatów urządzenia w następujące zasoby: **Mapowanie urządzeń** i **FHIR**. Łącznik usługi Azure IoT dla programu FHIR nie jest w pełni funkcjonalny do momentu przekazania tych mapowań.

[![Brak mapowań łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Aby przekazać szablony mapowania, kliknij nowo wdrożony łącznik usługi Azure IoT dla programu FHIR, aby przejść do strony **Łącznik IoT** .

[![Łącznik IoT — kliknij](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Mapowanie urządzeń

Szablon mapowania urządzeń przekształca dane urządzenia w znormalizowany schemat. Na stronie **Łącznik IoT** kliknij przycisk **Konfiguruj mapowanie urządzeń** , aby przejść do strony **Mapowanie urządzeń** . 

[![Łącznik IoT kliknij pozycję Konfiguruj mapowanie urządzeń.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Na stronie **Mapowanie urządzenia** Dodaj następujący skrypt do edytora JSON, a następnie kliknij przycisk **Zapisz**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.telemetry.HeartRate)]",
        "patientIdExpression": "$.Properties.iotcentral-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.telemetry.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Mapowanie urządzeń łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>Mapowanie FHIR

Szablon mapowania FHIR przekształca znormalizowany komunikat do ego zasobu na podstawie FHIR. Na stronie **Łącznik IoT** kliknij przycisk **Konfiguruj mapowanie FHIR** , aby przejść na stronę **mapowania FHIR** .  

[![Łącznik IoT kliknij pozycję Konfiguruj mapowanie FHIR](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Na stronie **Mapowanie FHIR** Dodaj następujący skrypt do edytora JSON i kliknij przycisk **Zapisz**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![Mapowanie FHIR łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Generowanie parametrów połączenia

Urządzenie IoMT wymaga parametrów połączenia w celu nawiązania połączenia i wysłania komunikatów do łącznika usługi Azure IoT dla FHIR. Na stronie **Łącznik IoT** dla nowo wdrożonego łącznika usługi Azure IoT dla FHIR wybierz pozycję **Zarządzaj połączeniami klientów** . 

[![Łącznik IoT — zarządzanie połączeniami klienta](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Na stronie **połączenia** kliknij przycisk **Dodaj** , aby utworzyć nowe połączenie. 

[![Połączenia łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Podaj przyjazną nazwę dla tego połączenia w oknie nakładki, a następnie wybierz przycisk **Utwórz** .

[![Nowe połączenie łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Wybierz nowo utworzone połączenie ze strony **połączenia** i skopiuj wartość pola **podstawowe parametry połączenia** z okna nakładki po prawej stronie.

[![Parametry połączenia łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Zachowaj te parametry połączenia, aby można było ich użyć w późniejszym kroku. 

## <a name="connect-your-devices-to-iot"></a>Łączenie urządzeń z usługą IoT

Platforma Azure oferuje rozbudowany pakiet produktów IoT do łączenia urządzeń IoT i zarządzania nimi. Możesz utworzyć własne rozwiązanie na podstawie PaaS przy użyciu usługi Azure IoT Hub lub zacząć od platformy zarządzania aplikacjami IoT za pomocą usługi Azure IoT Central. W tym samouczku będziemy korzystać z usługi Azure IoT Central, która ma szablony rozwiązań ukierunkowanych na branżę, które ułatwią rozpoczęcie pracy.

Wdróż [szablon aplikacji do monitorowania ciągłego pacjenta](../../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template). Ten szablon obejmuje dwa symulowane urządzenia generujące dane czasu rzeczywistego, które ułatwiają rozpoczęcie pracy: **inteligentne znaczenie poprawek** i **inteligentnych nawiasów kolanowych**.

> [!NOTE]
> Za każdym razem, gdy prawdziwe urządzenia są gotowe, możesz użyć tej samej IoT Central aplikacji do dołączenia [urządzeń](../../iot-central/core/howto-set-up-template.md) i zamienić symulatory urządzeń. Dane urządzenia zostaną automatycznie uruchomione i przepływają do FHIR. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Łączenie danych IoT z łącznikiem usługi Azure IoT dla FHIR (wersja zapoznawcza)

Po wdrożeniu aplikacji IoT Central Twoje dwa wbudowane urządzenia symulowane rozpoczną generowanie danych telemetrycznych. Na potrzeby tego samouczka będziemy pozyskiwać dane telemetryczne z inteligentnych funkcji symulatora *poprawek* do FHIR za pośrednictwem łącznika usługi Azure IoT dla FHIR. Aby wyeksportować dane IoT do łącznika usługi Azure IoT dla programu FHIR, należy [skonfigurować ciągły eksport danych w ramach IoT Central](../../iot-central/core/howto-export-data.md). Najpierw musimy utworzyć połączenie z miejscem docelowym, a następnie utworzymy zadanie eksportu danych w celu ciągłego uruchomienia: 

Utwórz nowe miejsce docelowe:
- Przejdź do karty **miejsca docelowe** i Utwórz nowe miejsce docelowe.
- Zacznij od nadania lokalizacji docelowej unikatowej nazwy.
- Wybierz pozycję *Azure Event Hubs* jako typ docelowy.
- Udostępnij łącznik usługi Azure IoT dla parametrów połączenia FHIR uzyskanych w poprzednim kroku dla pola **Parametry połączenia** .

Utwórz nowy eksport danych:
- Po utworzeniu miejsca docelowego przejdź do karty **eksporty** i Utwórz nowy eksport danych. 
- Zacznij od podania, że dane wyeksportują unikatową nazwę.
- W obszarze **dane** wybierz opcję *Telemetria* jako *Typ danych do wyeksportowania*.
- W obszarze **Lokalizacja docelowa** wybierz nazwę docelową utworzoną w poprzedniej nazwie.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Wyświetlanie danych urządzenia w usłudze Azure API for FHIR

Można wyświetlić FHIR widoczne zasoby utworzone przez łącznik usługi Azure IoT dla FHIR w interfejsie API platformy Azure dla FHIR przy użyciu programu Poster. Skonfiguruj swoje [ogłoszenie, aby uzyskać dostęp do interfejsu API platformy Azure dla usługi FHIR](access-fhir-postman-tutorial.md) , a następnie `GET` Wyślij żądanie, aby `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` wyświetlić obserwacje zasobów FHIR z wartością współczynnika serca. 

> [!TIP]
> Upewnij się, że użytkownik ma odpowiednie uprawnienia dostępu do interfejsu API platformy Azure dla FHIRej płaszczyzny danych. Aby przypisać wymagane role płaszczyzny danych, użyj [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](configure-azure-rbac.md) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, można usunąć wystąpienie łącznika usługi Azure IoT dla FHIR, usuwając skojarzoną grupę zasobów lub skojarzoną usługę Azure API for FHIR lub łącznik usługi Azure IoT dla samego wystąpienia FHIR. 

Aby bezpośrednio usunąć wystąpienie usługi Azure IoT Connector for FHIR, zaznacz wystąpienie na stronie **Łączniki IoT** , aby przejść do strony **Łącznik IoT** , a następnie kliknij przycisk **Usuń** . Wybierz pozycję **tak** po wyświetleniu monitu o potwierdzenie. 

[![Usuń wystąpienie łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono funkcję łącznika usługi Azure IoT for FHIR w interfejsie API platformy Azure dla zasobu FHIR. Wybierz spośród kolejnych kroków, aby dowiedzieć się więcej o łączniku usługi Azure IoT dla FHIR:

Poznaj różne etapy przepływu danych w ramach łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla przepływu danych FHIR](iot-data-flow.md)

Dowiedz się, jak skonfigurować łącznik usługi IoT przy użyciu szablonów mapowania urządzeń i FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla szablonów mapowania FHIR](iot-mapping-templates.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7.
