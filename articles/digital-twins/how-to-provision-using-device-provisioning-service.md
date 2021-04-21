---
title: Automatyczne zarządzanie urządzeniami przy użyciu usługi Device Provisioning Service
titleSuffix: Azure Digital Twins
description: Zobacz, jak skonfigurować zautomatyzowany proces aprowizowania i wycofywania urządzeń IoT w usłudze Azure Digital Twins przy użyciu usługi Device Provisioning Service (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 51b5714f9009cbe48aa49c6a04a1434cec12396e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790693"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Automatyczne zarządzanie urządzeniami w usłudze Azure Digital Twins przy użyciu usługi Device Provisioning Service (DPS)

W tym artykule dowiesz się, jak zintegrować usługę Azure Digital Twins z usługą [Device Provisioning Service (DPS).](../iot-dps/about-iot-dps.md)

Rozwiązanie opisane w tym artykule umożliwi zautomatyzowanie **** procesu **** aprowizowania i wycofywania urządzeń IoT Hub w usłudze Azure Digital Twins przy użyciu usługi Device Provisioning Service. 

Aby uzyskać więcej informacji  na temat etapów aprowizować i wycofywania oraz lepiej zrozumieć zestaw ogólnych etapów [  ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) zarządzania urządzeniami, które są wspólne dla wszystkich projektów IoT przedsiębiorstwa, zobacz sekcję Cykl życia urządzenia w dokumentacji zarządzania urządzeniami firmy IoT Hub. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było skonfigurować aprowizowanie, należy skonfigurować następujące elementy:
* wystąpienie **Azure Digital Twins .** Postępuj zgodnie z instrukcjami z tematu [*Instrukcje: Konfigurowanie wystąpienia*](how-to-set-up-instance-portal.md) i uwierzytelniania, aby utworzyć wystąpienie usługi Azure Digital Twins. Zbierz nazwę hosta **_wystąpienia w_** Azure Portal ([instrukcje](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* centrum **IoT.** Aby uzyskać instrukcje, zobacz *sekcję Tworzenie IoT Hub* tego przewodnika [IoT Hub Szybki start.](../iot-hub/quickstart-send-telemetry-cli.md)
* funkcja [**platformy Azure,**](../azure-functions/functions-overview.md) która aktualizuje informacje o bliźniaczych reprezentacjiach cyfrowych na IoT Hub danych. Aby utworzyć tę funkcję platformy Azure, postępuj zgodnie z instrukcjami z tematu Jak pozysować dane centrum [*IoT*](how-to-ingest-iot-hub-data.md) Hub. Zbierz nazwę **_funkcji do_** użycia w tym artykule.

W tym przykładzie jest również **używany symulator urządzenia,** który obejmuje aprowizowanie przy użyciu usługi Device Provisioning Service. Symulator urządzenia znajduje się tutaj: [Azure Digital Twins i IoT Hub Przykład integracji](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Pobierz przykładowy projekt na swojej maszynie, przechodząc do linku przykładowego i wybierając przycisk **Przeglądaj** kod pod tytułem. Spowoduje to pobranie przykładu do repozytorium GitHub, które można pobrać jako *plik . Zip* file (Plik ZIP), wybierając **przycisk Code (Kod)** i download **ZIP (Pobierz plik ZIP).** 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Zrzut ekranu przedstawiający repozytorium digital-twins-iothub-integration w witrynie GitHub. Po wybraniu przycisku Kod zostanie wyświetlone małe okno dialogowe, w którym wyróżniony jest przycisk Pobierz plik ZIP." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Rozpakować pobrany folder.

Konieczne będzie [**Node.js**](https://nodejs.org/download) na maszynie. Symulator urządzenia jest oparty na **Node.js**, wersji 10.0.x lub nowszej.

## <a name="solution-architecture"></a>Architektura rozwiązania

Na poniższej ilustracji przedstawiono architekturę tego rozwiązania przy użyciu usługi Azure Digital Twins z usługą Device Provisioning Service. Pokazuje on zarówno przepływ aprowrowi, jak i wycofywania urządzeń.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Diagram urządzenia i kilku usług platformy Azure w scenariuszu typu end-to-end. Dane przepływają tam i z powrotem między urządzeniem termostatu i dpsem. Dane również przepływają z usługi DPS do IoT Hub, a Azure Digital Twins za pośrednictwem funkcji platformy Azure z etykietą &quot;Alokacja&quot;. Dane z ręcznej akcji &quot;Usuń urządzenie&quot; przepływają przez IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Ten artykuł jest podzielony na dwie sekcje:
* [*Automatyczne aprowizowanie urządzenia przy użyciu usługi Device Provisioning Service*](#auto-provision-device-using-device-provisioning-service)
* [*Automatyczne wycofywanie urządzenia przy użyciu IoT Hub zdarzeń cyklu życia*](#auto-retire-device-using-iot-hub-lifecycle-events)

Aby uzyskać bardziej szczegółowe wyjaśnienia poszczególnych kroków w architekturze, zobacz ich poszczególne sekcje w dalszej części artykułu.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Automatyczne aprowizowanie urządzenia przy użyciu usługi Device Provisioning Service

W tej sekcji dołączysz usługę Device Provisioning Service, aby Azure Digital Twins do automatycznego aprowizowania urządzeń za pośrednictwem poniższej ścieżki. Jest to fragment pełnej architektury pokazanej [wcześniej.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Diagram przepływu aprowności — fragment diagramu architektury rozwiązania z sekcjami przepływu z etykietami liczb. Dane przepływają między urządzeniem termostatu a dps (1 dla urządzenia > DPS i 5 dla dps > dps). Dane również przepływają z usługi DPS do IoT Hub (4) i do Azure Digital Twins (3) za pośrednictwem funkcji platformy Azure z etykietą &quot;Alokacja&quot; (2)." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

Oto opis przepływu procesu:
1. Urządzenie kontaktuje się z punktem końcowym usługi DPS, przekazując informacje identyfikujące w celu potwierdzenia tożsamości.
2. Usługa DPS weryfikuje tożsamość urządzenia, weryfikując identyfikator rejestracji i klucz względem listy rejestracji, i wywołuje funkcję platformy [Azure](../azure-functions/functions-overview.md) w celu przydzielenia.
3. Funkcja platformy Azure tworzy nową [bliźniacze Azure Digital Twins](concepts-twins-graph.md) dla urządzenia. Bliźniacze reprezentacji będą mieć taką samą nazwę jak identyfikator **rejestracji urządzenia**.
4. DpS rejestruje urządzenie w centrum IoT i wypełnia żądany stan bliźniaczej reprezentacji urządzenia.
5. Centrum IoT hub zwraca informacje o identyfikatorze urządzenia i informacje o połączeniu centrum IoT z urządzeniem. Urządzenie może teraz łączyć się z centrum IoT.

Poniższe sekcje zawierają instrukcje dotyczące skonfigurowania przepływu automatycznego aprowizowania urządzeń.

### <a name="create-a-device-provisioning-service"></a>Tworzenie usługi Device Provisioning Service

Po aprowizowania nowego urządzenia przy użyciu usługi Device Provisioning Service można utworzyć nową bliźniacę tego urządzenia w usłudze Azure Digital Twins o takiej samej nazwie jak identyfikator rejestracji.

Utwórz wystąpienie usługi Device Provisioning Service, które będzie używane do aprowizowania urządzeń IoT. Możesz skorzystać z poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub skorzystać z Azure Portal: Szybki start: konfigurowanie interfejsu [*IoT Hub Device Provisioning Service pomocą Azure Portal.*](../iot-dps/quick-setup-auto-provision.md)

Następujące polecenie interfejsu wiersza polecenia platformy Azure utworzy usługę Device Provisioning Service. Musisz określić nazwę usługi Device Provisioning Service, grupę zasobów i region. Aby zobaczyć, które regiony obsługują usługę Device Provisioning Service, odwiedź stronę [*Dostępne produkty platformy Azure według regionów.*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)
Polecenie można uruchomić w [programie Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli na maszynie jest zainstalowany [interfejs wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Dodawanie funkcji do użycia z usługą Device Provisioning Service

W projekcie aplikacji funkcji utworzonym w sekcji wymagań wstępnych utworzysz nową funkcję do użycia z usługą Device Provisioning Service. [](#prerequisites) Ta funkcja będzie używana przez usługę Device Provisioning Service w niestandardowych zasadach [alokacji](../iot-dps/how-to-use-custom-allocation-policies.md) do aprowizowania nowego urządzenia.

Rozpocznij od otwarcia projektu aplikacji funkcji w Visual Studio na maszynie i wykonaj poniższe kroki.

#### <a name="step-1-add-a-new-function"></a>Krok 1. Dodawanie nowej funkcji 

Dodaj nową funkcję typu *WYZWALACZ HTTP* do projektu aplikacji funkcji w Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png" alt-text="Zrzut ekranu przedstawiający widok Visual Studio, aby dodać funkcję platformy Azure typu Wyzwalacz HTTP w projekcie aplikacji funkcji." lightbox="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Krok 2. Wypełnianie kodu funkcji

Dodaj nowy pakiet NuGet do projektu: [Microsoft.Azure.Devices.Provisioning.Service.](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) Może być również konieczne dodanie większej liczby pakietów do projektu, jeśli pakiety używane w kodzie nie są jeszcze częścią projektu.

W nowo utworzonym pliku kodu funkcji wklej następujący kod, zmień nazwę funkcji na *DpsAdtAllocationFunc.cs* i zapisz plik.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3. Publikowanie aplikacji funkcji na platformie Azure

Opublikuj projekt za pomocą *funkcji DpsAdtAllocationFunc.cs* w aplikacji funkcji na platformie Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Tworzenie rejestracji usługi Device Provisioning

Następnie należy utworzyć rejestrację w usłudze Device Provisioning Przy użyciu **niestandardowej funkcji alokacji**. Postępuj zgodnie z instrukcjami w sekcji [*Tworzenie*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) rejestracji artykułu niestandardowych zasad alokacji w dokumentacji usługi Device Provisioning Service.

Podczas korzystania z tego przepływu upewnij się, że wybierasz następujące opcje, aby połączyć rejestrację z właśnie utworzoną funkcją.

* **Wybierz sposób przypisywania urządzeń do centrów:** Niestandardowy (użyj funkcji platformy Azure).
* **Wybierz centra IoT, do których można przypisać tę grupę:** Wybierz nazwę centrum IoT hub lub wybierz przycisk Połącz nowe centrum *IoT* Hub, a następnie wybierz centrum IoT Hub z listy rozwijanej.

Następnie wybierz przycisk *Wybierz nową funkcję,* aby połączyć aplikację funkcji z grupą rejestracji. Następnie wprowadź następujące wartości:

* **Subskrypcja:** Subskrypcja platformy Azure jest wypełniana automatycznie. Upewnij się, że jest to właściwa subskrypcja.
* **Aplikacja funkcji:** wybierz nazwę aplikacji funkcji.
* **Funkcja:** wybierz pozycję DpsAdtAllocationFunc.

Zapisz swoje szczegóły.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Zrzut ekranu przedstawiający okno Szczegóły grupy rejestracji niestandardowych w celu wybrania wartości Niestandardowe (Użyj funkcji platformy Azure) i nazwy centrum IoT w sekcjach Wybierz sposób przypisywania urządzeń do centrów i Wybierz centra IoT, do których można przypisać tę grupę. Ponadto wybierz swoją subskrypcję i aplikację funkcji z listy rozwijanej i wybierz pozycję DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

Po utworzeniu rejestracji klucz **podstawowy rejestracji** zostanie później użyty do skonfigurowania symulatora urządzenia dla tego artykułu.

### <a name="set-up-the-device-simulator"></a>Konfigurowanie symulatora urządzenia

W tym przykładzie użyto symulatora urządzenia, który obejmuje aprowizowanie przy użyciu usługi Device Provisioning Service. Symulator urządzenia znajduje się w sekcji [Azure Digital Twins i IoT Hub Przykład](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) integracji urządzeń pobranych w sekcji [Wymagania](#prerequisites) wstępne.

#### <a name="upload-the-model"></a>Przekazywanie modelu

Symulator urządzenia to urządzenie z termostatem, które używa modelu o tym identyfikatorze: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Przed utworzeniem bliźniaczej reprezentacji tego typu dla urządzenia musisz przekazać ten model do Azure Digital Twins do tej grupy.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Aby uzyskać więcej informacji na temat modeli, zapoznaj się z [*tematem Jak zarządzać modelami.*](how-to-manage-model.md#upload-models)

#### <a name="configure-and-run-the-simulator"></a>Konfigurowanie i uruchamianie symulatora

W oknie polecenia przejdź do pobranego przykładowego pliku Azure Digital Twins i IoT Hub *Integration,* który został rozpakowany wcześniej, a następnie do katalogu *device-simulator.* Następnie zainstaluj zależności dla projektu przy użyciu następującego polecenia:

```cmd
npm install
```

Następnie w katalogu symulatora urządzeń skopiuj plik env.template do nowego pliku o nazwie env i zbierz następujące wartości, aby wypełnić ustawienia:

* PROVISIONING_IDSCOPE: Aby uzyskać tę wartość, przejdź do usługi device provisioning w usłudze [Azure Portal, a](https://portal.azure.com/)następnie wybierz pozycję Przegląd w opcjach menu i poszukaj pola *Zakres identyfikatorów.* 

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Zrzut ekranu przedstawiający Azure Portal strony przeglądu aprowowania urządzeń w celu skopiowania wartości zakres identyfikatorów." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: możesz wybrać identyfikator rejestracji dla swojego urządzenia.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: jest to klucz podstawowy dla wcześniej ustawionej rejestracji. Aby ponownie uzyskać tę wartość, przejdź do usługi device provisioning service w usłudze Azure Portal, wybierz pozycję Zarządzaj rejestracjami, a następnie wybierz utworzoną wcześniej grupę rejestracji i skopiuj *klucz podstawowy*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Zrzut ekranu przedstawiający Azure Portal strony zarządzania rejestracjami usługi device provisioning service w celu skopiowania wartości klucza podstawowego sygnatury dostępu współdzielonego." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Teraz użyj powyższych wartości, aby zaktualizować ustawienia pliku env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Zapisz i zamknij plik.

### <a name="start-running-the-device-simulator"></a>Uruchamianie symulatora urządzenia

Nadal w *katalogu device-simulator* w oknie polecenia uruchom symulator urządzenia przy użyciu następującego polecenia:

```cmd
node .\adt_custom_register.js
```

Urządzenie powinno być zarejestrowane i połączone z IoT Hub, a następnie rozpoczynać wysyłanie komunikatów.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Zrzut ekranu przedstawiający okno Polecenie rejestracji urządzenia i wysyłania komunikatów" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Walidacja

W wyniku przepływu, który został ustawiony w tym artykule, urządzenie zostanie automatycznie zarejestrowane w Azure Digital Twins. Użyj następującego [Azure Digital Twins wiersza](how-to-use-cli.md) polecenia, aby znaleźć bliźniacze reprezentacji urządzenia w Azure Digital Twins utworzonego wystąpienia.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

W wystąpieniu aplikacji powinna być Azure Digital Twins bliźniacze urządzenie.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Zrzut ekranu przedstawiający okno Polecenie bliźniaczej reprezentacji." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Automatyczne wycofywanie urządzenia przy użyciu IoT Hub zdarzeń cyklu życia

W tej sekcji dołączysz zdarzenia cyklu IoT Hub, aby Azure Digital Twins automatycznie wycofywano urządzenia za pośrednictwem poniższej ścieżki. Jest to fragment pełnej architektury pokazanej [wcześniej.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Diagram przepływu Wycofywanie urządzenia — fragment diagramu architektury rozwiązania z sekcjami etykietowania liczb przepływu. Urządzenie termostatu jest wyświetlane na diagramie bez połączeń z usługami platformy Azure. Dane z ręcznej akcji &quot;Usuń urządzenie&quot; przepływają przez IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Twins (3)." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

Oto opis przepływu procesu:
1. Proces zewnętrzny lub ręczny wyzwala usunięcie urządzenia w IoT Hub.
2. IoT Hub usuwa urządzenie i generuje zdarzenie [](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) cyklu życia urządzenia, które zostanie kierowane do [centrum zdarzeń](../event-hubs/event-hubs-about.md).
3. Funkcja platformy Azure usuwa bliźniacze reprezentacji urządzenia w Azure Digital Twins.

Poniższe sekcje zawierają instrukcje służące do skonfigurowania tego przepływu automatycznego wycofywania urządzeń.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Następnie utworzysz centrum zdarzeń platformy [Azure](../event-hubs/event-hubs-about.md) do odbierania zdarzeń IoT Hub cyklu życia. 

Wykonaj kroki opisane w przewodniku Szybki start [*Tworzenie centrum*](../event-hubs/event-hubs-create.md) zdarzeń. Nadaj centrum zdarzeń *nazwę lifecycleevents*. Tej nazwy centrum zdarzeń użyjesz podczas IoT Hub trasy i funkcji platformy Azure w następnych sekcjach.

Poniższy zrzut ekranu przedstawia tworzenie centrum zdarzeń.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Zrzut ekranu przedstawiający Azure Portal tworzenia centrum zdarzeń o nazwie lifecycleevents." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Tworzenie zasad sygnatury dostępu współdzielonego dla centrum zdarzeń

Następnie należy utworzyć zasady sygnatury dostępu współdzielonego [(SAS),](../event-hubs/authorize-access-shared-access-signature.md) aby skonfigurować centrum zdarzeń za pomocą aplikacji funkcji.
W tym celu
1. Przejdź do centrum zdarzeń utworzonego w centrum zdarzeń Azure Portal a następnie wybierz pozycję Zasady dostępu współdzielonych **w** opcjach menu po lewej stronie.
2. Wybierz pozycję **Dodaj**. W *oknie Dodawanie zasad* sygnatury dostępu współdzielonego, które zostanie otwarte, wprowadź nazwę wybranej zasady i zaznacz pole wyboru *Nasłuchiwać.*
3. Wybierz przycisk **Utwórz**.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby dodać zasady sygnatury dostępu współdzielonego centrum zdarzeń." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Konfigurowanie centrum zdarzeń za pomocą aplikacji funkcji

Następnie skonfiguruj aplikację funkcji platformy Azure skonfigurowaną w sekcji [wymagań](#prerequisites) wstępnych do pracy z nowym centrum zdarzeń. W tym celu należy ustawiać zmienną środowiskową wewnątrz aplikacji funkcji przy użyciu parametrów połączenia centrum zdarzeń.

1. Otwórz właśnie utworzone zasady i skopiuj wartość **Connection string-primary key (Ciąg** połączenia — klucz podstawowy).

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Zrzut ekranu przedstawiający Azure Portal skopiowania parametrów połączenia — klucz podstawowy." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Dodaj ciąg połączenia jako zmienną w ustawieniach aplikacji funkcji za pomocą następującego polecenia interfejsu wiersza polecenia platformy Azure. Polecenie można uruchomić w [programie Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli na maszynie jest zainstalowany interfejs [wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Dodawanie funkcji w celu wycofania z IoT Hub zdarzeń cyklu życia

W projekcie aplikacji funkcji utworzonym w sekcji wymagań wstępnych utworzysz nową funkcję w celu wycofania istniejącego urządzenia przy użyciu IoT Hub cyklu życia. [](#prerequisites)

Aby uzyskać więcej informacji o zdarzeniach cyklu życia, zobacz IoT Hub Zdarzenia inne niż [*telemetryczne.*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) Aby uzyskać więcej informacji na temat używania Event Hubs z usługą Azure Functions, zobacz [*Azure Event Hubs wyzwalacza Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Rozpocznij od otwarcia projektu aplikacji funkcji w Visual Studio na maszynie i wykonaj poniższe kroki.

#### <a name="step-1-add-a-new-function"></a>Krok 1. Dodawanie nowej funkcji
     
Dodaj nową funkcję typu *Wyzwalacz centrum zdarzeń* do projektu aplikacji funkcji w Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png" alt-text="Zrzut ekranu przedstawiający Visual Studio dodawania funkcji platformy Azure typu Wyzwalacz centrum zdarzeń w projekcie aplikacji funkcji." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Krok 2. Wypełnianie kodu funkcji

W nowo utworzonym pliku kodu funkcji wklej następujący kod, zmień nazwę funkcji na `DeleteDeviceInTwinFunc.cs` i zapisz plik.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3. Publikowanie aplikacji funkcji na platformie Azure

Opublikuj projekt za pomocą *funkcji DeleteDeviceInTwinFunc.cs* w aplikacji funkcji na platformie Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Tworzenie trasy IoT Hub dla zdarzeń cyklu życia

Teraz skonfigurujesz trasę w celu IoT Hub zdarzeń cyklu życia urządzenia. W tym przypadku będziesz nasłuchiwać zdarzeń usuwania urządzeń identyfikowanych `if (opType == "deleteDeviceIdentity")` przez . Spowoduje to usunięcie elementu bliźniaczej reprezentacji cyfrowej, finalizując wycofanie urządzenia i jego bliźniaczej reprezentacji cyfrowej.

Najpierw należy utworzyć punkt końcowy centrum zdarzeń w centrum IoT. Następnie dodasz trasę w centrum IoT Hub, aby wysyłać zdarzenia cyklu życia do tego punktu końcowego centrum zdarzeń.
Wykonaj następujące kroki, aby utworzyć punkt końcowy centrum zdarzeń:

1. W [Azure Portal](https://portal.azure.com/)przejdź do centrum IoT utworzonego w [](#prerequisites) sekcji wymagań  wstępnych i wybierz pozycję Routing komunikatów w opcjach menu po lewej stronie.
2. Wybierz **kartę Niestandardowe punkty** końcowe.
3. Wybierz **pozycję + Dodaj** i wybierz pozycję Centra **zdarzeń,** aby dodać punkt końcowy typu centrum zdarzeń.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Zrzut ekranu przedstawiający okno Visual Studio dodawania niestandardowego punktu końcowego centrum zdarzeń." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. W oknie *Dodawanie otwartego punktu końcowego centrum* zdarzeń wybierz następujące wartości:
    * **Nazwa punktu końcowego:** wybierz nazwę punktu końcowego.
    * **Przestrzeń nazw centrum zdarzeń:** wybierz przestrzeń nazw centrum zdarzeń z listy rozwijanej.
    * **Wystąpienie centrum zdarzeń:** wybierz nazwę centrum zdarzeń utworzonego w poprzednim kroku.
5. Wybierz przycisk **Utwórz**. Nie otwieraj tego okna, aby dodać trasę w następnym kroku.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Zrzut ekranu przedstawiający okno Visual Studio dodawania punktu końcowego centrum zdarzeń." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Następnie dodasz trasę, która łączy się z punktem końcowym utworzonym w powyższym kroku, przy użyciu zapytania routingu, które wysyła zdarzenia usuwania. Wykonaj następujące kroki, aby utworzyć trasę:

1. Przejdź do karty *Trasy i* wybierz pozycję **Dodaj,** aby dodać trasę.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Zrzut ekranu przedstawiający Visual Studio, aby dodać trasę do wysyłania zdarzeń." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. Na stronie *Dodawanie trasy,* która zostanie otwarta, wybierz następujące wartości:

   * **Nazwa:** wybierz nazwę trasy. 
   * **Punkt** końcowy: wybierz utworzony wcześniej punkt końcowy usługi Event Hubs z listy rozwijanej.
   * **Źródło danych:** wybierz *pozycję Zdarzenia cyklu życia urządzenia.*
   * **Zapytanie dotyczące routingu:** wprowadź `opType='deleteDeviceIdentity'` . To zapytanie ogranicza zdarzenia cyklu życia urządzenia tylko do wysyłania zdarzeń usuwania.

3. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby dodać trasę do wysyłania zdarzeń cyklu życia." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Po zakończeniu tego przepływu wszystko jest ustawione na end-to-end wycofywanie urządzeń.

### <a name="validate"></a>Walidacja

Aby wyzwolić proces wycofania, musisz ręcznie usunąć urządzenie z IoT Hub.

Można to zrobić za pomocą polecenia interfejsu wiersza polecenia [platformy Azure](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) lub w Azure Portal. Wykonaj poniższe kroki, aby usunąć urządzenie w Azure Portal:

1. Przejdź do centrum IoT Hub i wybierz pozycję **Urządzenia IoT** w opcjach menu po lewej stronie. 
2. Zobaczysz urządzenie z identyfikatorem rejestracji urządzenia, który został wybrany w [pierwszej połowie tego artykułu.](#auto-provision-device-using-device-provisioning-service) Alternatywnie możesz wybrać dowolne inne urządzenie do usunięcia, o ile ma ono bliźniacze reprezentacji w u Azure Digital Twins, aby można było sprawdzić, czy bliźniacze reprezentacji jest automatycznie usuwane po usunięciu urządzenia.
3. Wybierz urządzenie, a następnie wybierz pozycję **Usuń.**

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Zrzut ekranu przedstawiający Azure Portal bliźniaczej reprezentacji urządzenia z urządzeń IoT." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Może mi potrwać kilka minut, aby zmiany zostaną odzwierciedlone w Azure Digital Twins.

Użyj następującego [Azure Digital Twins wiersza](how-to-use-cli.md) polecenia, aby sprawdzić, czy bliźniacze reprezentacji urządzenia w wystąpieniu Azure Digital Twins została usunięta.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Powinno być widać, że bliźniaczej reprezentacji urządzenia nie można już znaleźć w Azure Digital Twins wystąpienia.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Zrzut ekranu przedstawiający okno Polecenie nie znaleziono bliźniaczej reprezentacji." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w tym artykule, wykonaj następujące kroki, aby je usunąć.

Za pomocą Azure Cloud Shell lub lokalnego interfejsu wiersza polecenia platformy Azure można usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą [polecenia az group delete.](/cli/azure/group#az_group_delete) Spowoduje to usunięcie grupy zasobów. wystąpienie Azure Digital Twins; centrum IoT i rejestracja urządzenia centrum; temat usługi Event Grid i skojarzone subskrypcje; przestrzeń nazw usługi Event Hubs i obie Azure Functions, w tym skojarzone zasoby, takie jak magazyn.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Następnie usuń przykładowy folder projektu pobrany z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

Bliźniacze reprezentacji cyfrowych utworzone dla urządzeń są przechowywane jako płaska hierarchia w Azure Digital Twins, ale można je wzbogacić o informacje o modelu i hierarchię wielopoziomową dla organizacji. Aby dowiedzieć się więcej na temat tej koncepcji, przeczytaj:

* [*Pojęcia: bliźniacze bliźniacze reprezentacji i graf bliźniaczych reprezentacji*](concepts-twins-graph.md)

Aby uzyskać więcej informacji na temat używania żądań HTTP z usługą Azure Functions, zobacz:

* [*Wyzwalacz żądania Http platformy Azure dla Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Możesz napisać logikę niestandardową, aby automatycznie dostarczać te informacje przy użyciu modelu i danych grafu już przechowywanych w Azure Digital Twins. Aby dowiedzieć się więcej na temat zarządzania, uaktualniania i pobierania informacji z grafu bliźniaczych reprezentacji, zobacz następujące informacje:

* [*How-to: Manage a digital twin (Jak zarządzać bliźniaczą reprezentacjią cyfrową)*](how-to-manage-twin.md)
* [*How-to: Query the twin graph (Graf bliźniaczej reprezentacji zapytania)*](how-to-query-graph.md)