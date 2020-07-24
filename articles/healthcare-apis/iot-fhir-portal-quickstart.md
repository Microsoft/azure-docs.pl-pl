---
title: 'Szybki Start: wdrażanie łącznika IoT (wersja zapoznawcza) przy użyciu Azure Portal'
description: W tym przewodniku szybki start dowiesz się, jak wdrażać, konfigurować i korzystać z funkcji łącznika IoT usługi Azure API for FHIR przy użyciu Azure Portal.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: bc85765666ba3baeae7ec795118f615f6b4b4368
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102190"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>Szybki Start: wdrażanie łącznika IoT (wersja zapoznawcza) przy użyciu Azure Portal

Łącznik IoT to opcjonalna funkcja interfejsu API platformy Azure dla usługi FHIR, która zapewnia możliwość pozyskiwania danych z urządzeń z Internetu (IoMT). W fazie zapoznawczej Funkcja łącznika IoT jest dostępna bezpłatnie. W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:
- Wdrażanie i Konfigurowanie łącznika IoT przy użyciu Azure Portal
- Wysyłanie danych do łącznika IoT za pomocą symulowanego urządzenia
- Wyświetlanie zasobów utworzonych przez łącznik IoT w usłudze Azure API for FHIR

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR — [wdrażanie interfejsu API platformy Azure dla FHIR przy użyciu Azure Portal](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Przejdź do interfejsu API platformy Azure dla zasobu FHIR

Otwórz [Azure Portal](https://portal.azure.com) i przejdź do zasobu **Azure API for FHIR** , dla którego chcesz utworzyć funkcję łącznika IoT.

[![Interfejs API platformy Azure dla zasobu FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

W menu nawigacji po lewej stronie kliknij pozycję **Łącznik IoT (wersja zapoznawcza)** w sekcji **Dodatki** , aby otworzyć stronę **Łączniki IoT** .

[![Funkcja łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>Tworzenie nowego łącznika IoT (wersja zapoznawcza)

Kliknij przycisk **Dodaj** , aby otworzyć stronę **Tworzenie łącznika IoT** .

[![Dodawanie łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Wprowadź ustawienia dla nowego łącznika usługi IoT. Kliknij przycisk **Utwórz** , a następnie zaczekaj na wdrożenie łącznika IoT.

> [!NOTE]
> Należy wybrać opcję **Utwórz** jako wartość dla listy rozwijanej **Typ rozwiązania** dla tej instalacji. 

[![Utwórz łącznik IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Ustawienie|Wartość|Opis |
|---|---|---|
|Nazwa łącznika|Unikatowa nazwa|Wprowadź nazwę identyfikującą łącznik IoT. Ta nazwa powinna być unikatowa w ramach interfejsu API platformy Azure dla zasobu FHIR. Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zaczynać się i kończyć literą lub cyfrą, a musi mieć długość od 3-24 znaków.|
|Typ rozwiązania|Odnośnik lub Utwórz|Wybierz pozycję **Wyszukaj** , jeśli masz proces poza pasmem, aby utworzyć zasoby FHIR [urządzenia](https://www.hl7.org/fhir/device.html) i [pacjenta](https://www.hl7.org/fhir/patient.html) w interfejsie API platformy Azure dla FHIR. Łącznik IoT będzie używać odwołań do tych zasobów podczas tworzenia zasobu [obserwacji](https://www.hl7.org/fhir/observation.html) FHIR do reprezentowania danych urządzenia. Wybierz pozycję **Utwórz** , jeśli chcesz, aby łącznik IoT utworzył urządzenie bez systemu operacyjnego i zasoby pacjenta w interfejsie API platformy Azure dla FHIR przy użyciu odpowiednich wartości identyfikatora znajdujących się w danych urządzenia.|

Po zakończeniu instalacji zostanie wyświetlony nowo utworzony łącznik IoT na stronie **Łączniki IoT** .

[![Utworzono łącznik IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>Konfigurowanie łącznika IoT (wersja zapoznawcza)

Łącznik IoT potrzebuje dwóch szablonów mapowania do przekształcenia komunikatów urządzenia w FHIRe zasoby na podstawie: **Mapowanie urządzeń** i **Mapowanie FHIR**. Łącznik IoT nie jest w pełni funkcjonalny, dopóki nie zostaną przekazane te mapowania.

[![Brak mapowań łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Aby przekazać szablony mapowania, kliknij nowo wdrożony łącznik IoT, aby przejść do strony **Łącznik IoT** .

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
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
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

Urządzenie IoMT wymaga parametrów połączenia w celu nawiązania połączenia i wysłania komunikatów do łącznika usługi IoT. Na stronie **Łącznik usługi IoT** dla nowo wdrożonego łącznika IoT wybierz przycisk **Zarządzaj połączeniami klienta** . 

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

Wdróż [szablon aplikacji do monitorowania ciągłego pacjenta](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template). Ten szablon obejmuje dwa symulowane urządzenia generujące dane czasu rzeczywistego, które ułatwiają rozpoczęcie pracy: **inteligentne znaczenie poprawek** i **inteligentnych nawiasów kolanowych**.

> [!NOTE]
> Za każdym razem, gdy prawdziwe urządzenia są gotowe, możesz użyć tej samej IoT Central aplikacji do dołączenia [urządzeń](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) i zamienić symulatory urządzeń. Dane urządzenia zostaną automatycznie uruchomione i przepływają do FHIR. 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>Łączenie danych IoT z łącznikiem IoT (wersja zapoznawcza)
Po wdrożeniu aplikacji IoT Central Twoje dwa wbudowane urządzenia symulowane rozpoczną generowanie danych telemetrycznych. Na potrzeby tego samouczka będziemy pozyskiwać dane telemetryczne z inteligentnych funkcji symulatora *poprawek* do FHIR za pośrednictwem łącznika IoT. Aby wyeksportować dane IoT do łącznika IoT, należy [skonfigurować ciągły eksport danych w ramach IoT Central](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export). Na stronie Eksport danych ciągłych:
- Wybierz pozycję *Azure Event Hubs* jako miejsce docelowe eksportowania.
- Zaznacz pole wyboru *Użyj wartości parametrów połączenia* dla **Event Hubs przestrzeni nazw** .
- Podaj parametry połączenia łącznika IoT uzyskane w poprzednim kroku dla pola **parametrów połączenia** .
- Zachowaj opcję **telemetrii** *dla* **danych do wyeksportowania** .

## <a name="view-device-data-in-azure-api-for-fhir"></a>Wyświetlanie danych urządzenia w usłudze Azure API for FHIR

Można wyświetlić FHIR widoczne zasoby utworzone przez łącznik IoT w interfejsie API platformy Azure dla FHIR przy użyciu programu Poster. Skonfiguruj swoje [ogłoszenie, aby uzyskać dostęp do interfejsu API platformy Azure dla usługi FHIR](access-fhir-postman-tutorial.md) , a następnie `GET` Wyślij żądanie, aby `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` wyświetlić obserwacje zasobów FHIR z wartością współczynnika serca. 

> [!TIP]
> Upewnij się, że użytkownik ma odpowiednie uprawnienia dostępu do interfejsu API platformy Azure dla FHIRej płaszczyzny danych. Użyj [Access Control opartej na rolach platformy Azure](configure-azure-rbac.md) , aby przypisać wymagane role płaszczyzny danych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, można usunąć wystąpienie łącznika IoT, usuwając skojarzoną grupę zasobów lub skojarzony interfejs API platformy Azure dla usługi FHIR lub sam wystąpienie łącznika IoT. 

Aby bezpośrednio usunąć wystąpienie łącznika usługi IoT, wybierz wystąpienie ze strony **Łączniki IoT** , aby przejść do strony **Łącznik usługi IoT** , a następnie kliknij przycisk **Usuń** . Wybierz pozycję **tak** po wyświetleniu monitu o potwierdzenie. 

[![Usuń wystąpienie łącznika IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono funkcję łącznika IoT w interfejsie API platformy Azure dla zasobu FHIR. Wybierz spośród kolejnych kroków, aby dowiedzieć się więcej o łączniku IoT:

Poznaj różne etapy przepływu danych w ramach łącznika IoT.

>[!div class="nextstepaction"]
>[Przepływ danych łącznika IoT](iot-data-flow.md)

Dowiedz się, jak skonfigurować łącznik usługi IoT przy użyciu szablonów mapowania urządzeń i FHIR.

>[!div class="nextstepaction"]
>[Szablony mapowania łącznika IoT](iot-mapping-templates.md)

FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.
