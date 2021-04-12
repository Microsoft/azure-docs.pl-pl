---
title: Autozarządzanie urządzeniami przy użyciu usługi Device Provisioning
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować zautomatyzowany proces, aby udostępnić i wycofać urządzenia IoT w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu usługi Device Provisioning Service (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ad1351b7c9a649a553ce54422b99a13c286437d6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107299"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Autozarządzanie urządzeniami w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu usługi Device Provisioning Service (DPS)

W tym artykule dowiesz się, jak zintegrować usługę Azure Digital bliźniaczych reprezentacji z [usługą Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md).

Rozwiązanie opisane w tym artykule umożliwi automatyzację procesu **_inicjowania obsługi_** i **_wycofywania_** IoT Hub urządzeń w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu usługi Device Provisioning. 

Aby uzyskać więcej _informacji na temat aprowizacji i_ _wycofywania_ etapów oraz lepiej zrozumieć zestaw ogólnych etapów zarządzania urządzeniami, które są wspólne dla wszystkich projektów IoT w przedsiębiorstwie, zobacz [sekcję *cykl życia urządzenia*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) w dokumentacji dotyczącej zarządzania urządzeniami w IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było skonfigurować Inicjowanie obsługi administracyjnej, należy skonfigurować następujące elementy:
* **wystąpienie usługi Azure Digital bliźniaczych reprezentacji**. Postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Konfiguracja wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md) w celu utworzenia wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Zbierz **_nazwę hosta_** wystąpienia w Azure Portal ([instrukcje](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* **Centrum IoT**. Aby uzyskać instrukcje, zobacz sekcję *tworzenie IoT Hub* w tym [IoT Hub przewodniku szybki start](../iot-hub/quickstart-send-telemetry-cli.md).
* [**Funkcja platformy Azure**](../azure-functions/functions-overview.md) , która aktualizuje informacje z cyfrowego przędzy na podstawie IoT Hub danych. Postępuj zgodnie z instrukcjami w temacie [*How to: pozyskiwanie danych usługi IoT Hub*](how-to-ingest-iot-hub-data.md) w celu utworzenia tej funkcji platformy Azure. Zbierz **_nazwę_** funkcji, aby użyć jej w tym artykule.

Ten przykład używa również **symulatora urządzeń** , który obejmuje obsługę administracyjną przy użyciu usługi Device Provisioning. Symulator urządzeń znajduje się tutaj: [usługa Azure Digital bliźniaczych reprezentacji i przykład integracji z IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Pobierz przykładowy projekt na swoją maszynę, przechodząc do linku przykładowego i wybierając przycisk *Pobierz zip* poniżej tytułu. Rozpakuj pobrany folder.

Na maszynie będzie wymagane [**Node.js**](https://nodejs.org/download) . Symulator urządzeń jest oparty na **Node.js**, w wersji 10.0. x lub nowszej.

## <a name="solution-architecture"></a>Architektura rozwiązania

Na poniższym obrazie przedstawiono architekturę tego rozwiązania przy użyciu narzędzia Azure Digital bliźniaczych reprezentacji z usługą Device Provisioning. Pokazuje zarówno proces aprowizacji, jak i wycofywania urządzenia.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Diagram urządzenia i kilka usług platformy Azure w kompleksowym scenariuszu. Dane są przepływane między urządzeniem termostatu a działem DPS i z powrotem. Dane są również przepływane z usługi DPS do IoT Hub i do Digital bliźniaczych reprezentacji na platformie Azure za pośrednictwem funkcji platformy Azure o nazwie &quot;Allocation&quot;. Dane z ręcznej akcji &quot;Usuń urządzenie&quot; są przepływem przez IoT Hub > Event Hubs > Azure Functions > Azure Digital bliźniaczych reprezentacji." lightbox="media/how-to-provision-using-dps/flows.png":::

Ten artykuł jest podzielony na dwie sekcje:
* [*Inicjowanie obsługi administracyjnej urządzenia przy użyciu usługi Device Provisioning*](#auto-provision-device-using-device-provisioning-service)
* [*Autowycofywanie urządzenia przy użyciu IoT Hub zdarzeń cyklu życia*](#auto-retire-device-using-iot-hub-lifecycle-events)

Aby uzyskać dokładniejsze wyjaśnienia poszczególnych kroków architektury, zobacz poszczególne sekcje w dalszej części artykułu.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Inicjowanie obsługi administracyjnej urządzenia przy użyciu usługi Device Provisioning

W tej sekcji zostanie dołączana usługa Device Provisioning do usługi Azure Digital bliźniaczych reprezentacji w celu samodzielnego udostępnienia urządzeń za pomocą poniższej ścieżki. Jest to fragment ze wszystkich pokazanych [wcześniej](#solution-architecture)architektury.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Diagram przepływu udostępniania — fragment diagramu architektury rozwiązania z numerami etykiet sekcji przepływu. Dane są przepływane między urządzeniem termostatu a działem DPS (1 w przypadku urządzenia > DPS i 5 dla usługi DPS > urządzeniu). Dane są również przepływane z usługi DPS do IoT Hub (4) oraz do programu Azure Digital bliźniaczych reprezentacji (3) za pośrednictwem funkcji platformy Azure o nazwie &quot;Allocation&quot; (2)." lightbox="media/how-to-provision-using-dps/provision.png":::

Oto opis przepływu procesu:
1. Urządzenie kontaktuje się z punktem końcowym DPS, przekazując informacje identyfikacyjne w celu potwierdzenia tożsamości.
2. Usługa DPS sprawdza poprawność tożsamości urządzenia, sprawdzając Identyfikator rejestracji i klucz na liście rejestracji, a następnie wywołuje [funkcję platformy Azure](../azure-functions/functions-overview.md) w celu wykonania przydziału.
3. Funkcja platformy Azure tworzy nowe [sznurki](concepts-twins-graph.md) w usłudze Azure Digital bliźniaczych reprezentacji dla urządzenia. Sznurek będzie mieć taką samą nazwę jak **Identyfikator rejestracji** urządzenia.
4. Usługa DPS rejestruje urządzenie w usłudze IoT Hub i wypełnia żądany stan dwuosiowy urządzenia.
5. Centrum IoT Hub zwraca informacje o IDENTYFIKATORze urządzenia oraz informacje o połączeniu usługi IoT Hub z urządzeniem. Urządzenie może teraz połączyć się z usługą IoT Hub.

W poniższych sekcjach opisano kroki umożliwiające skonfigurowanie tego przepływu urządzenia automatyczne Inicjowanie obsługi.

### <a name="create-a-device-provisioning-service"></a>Tworzenie usługi Device Provisioning

Po zainicjowaniu nowego urządzenia przy użyciu usługi Device Provisioning można utworzyć nową sznurek dla tego urządzenia w usłudze Azure Digital bliźniaczych reprezentacji o takiej samej nazwie jak identyfikator rejestracji.

Utwórz wystąpienie usługi Device Provisioning, które będzie używane do udostępniania urządzeń IoT. Możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub użyć Azure Portal: [*Szybki Start: skonfiguruj IoT Hub Device Provisioning Service przy użyciu Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Następujące polecenie interfejsu wiersza polecenia platformy Azure utworzy usługę Device Provisioning. Należy określić nazwę usługi Device Provisioning, grupę zasobów i region. Aby zobaczyć, które regiony obsługują usługę Device Provisioning, odwiedź stronę [*dostępne dla regionu platformy Azure*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
Polecenie można uruchomić w [Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli [na maszynie jest zainstalowany](/cli/azure/install-azure-cli)interfejs wiersza polecenia platformy Azure.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Dodawanie funkcji, która ma być używana z usługą Device Provisioning

W projekcie aplikacji funkcji, który został utworzony w sekcji [wymagania wstępne](#prerequisites) , utworzysz nową funkcję do użycia z usługą Device Provisioning. Ta funkcja będzie używana przez usługę Device Provisioning w [niestandardowych zasadach alokacji](../iot-dps/how-to-use-custom-allocation-policies.md) w celu aprowizacji nowego urządzenia.

Zacznij od otwarcia projektu aplikacji funkcji w programie Visual Studio na maszynie i wykonaj poniższe kroki.

#### <a name="step-1-add-a-new-function"></a>Krok 1. Dodawanie nowej funkcji 

Dodaj nową funkcję typu *http-Trigger* do projektu aplikacji funkcji w programie Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Zrzut ekranu przedstawiający widok programu Visual Studio umożliwiający dodanie funkcji platformy Azure typu wyzwalacz http w projekcie aplikacji funkcji." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Krok 2. wypełnienie kodu funkcji

Dodaj nowy pakiet NuGet do projektu: [Microsoft. Azure. Devices. Provisioning. Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Może być konieczne również dodanie większej liczby pakietów do projektu, jeśli pakiety używane w kodzie nie są już częścią projektu.

W nowo utworzonym pliku kodu funkcji wklej następujący kod, Zmień nazwę funkcji na *DpsAdtAllocationFunc. cs* i Zapisz plik.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3. publikowanie aplikacji funkcji na platformie Azure

Opublikuj projekt przy użyciu funkcji *DpsAdtAllocationFunc. cs* w aplikacji funkcji na platformie Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Utwórz rejestrację aprowizacji urządzeń

Następnie musisz utworzyć rejestrację w usłudze Device Provisioning przy użyciu **funkcji alokacji niestandardowej**. Postępuj zgodnie z instrukcjami, aby to zrobić, w sekcji [*Tworzenie rejestracji*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) w artykule niestandardowe zasady alokacji w dokumentacji usługi Device Provisioning.

Podczas przechodzenia przez ten przepływ upewnij się, że wybrano poniższe opcje, aby połączyć rejestrację z właśnie utworzoną funkcją.

* **Wybierz sposób przypisywania urządzeń do centrów**: niestandardowe (Użyj funkcji platformy Azure).
* **Wybierz centra IoT, do których ta grupa może być przypisana:** Wybierz nazwę Centrum IoT Hub lub wybierz przycisk *Połącz nowy Centrum IoT Hub* , a następnie wybierz Centrum IoT Hub z listy rozwijanej.

Następnie wybierz przycisk *Wybierz nową funkcję* , aby połączyć aplikację funkcji z grupą rejestracji. Następnie wprowadź następujące wartości:

* **Subskrypcja**: subskrypcja platformy Azure jest wypełniana automatycznie. Upewnij się, że jest to odpowiednia subskrypcja.
* **Aplikacja funkcji**: wybierz nazwę aplikacji funkcji.
* **Funkcja**: Wybierz DpsAdtAllocationFunc.

Zapisz szczegóły.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Zrzut ekranu okna Szczegóły grupy rejestracji celnej w celu wybrania opcji niestandardowe (Użyj funkcji platformy Azure) i nazwy Centrum IoT w sekcji Wybierz sposób przypisywania urządzeń do centrów i wybierz centra IoT, do których ta grupa może być przypisana. Ponadto wybierz subskrypcję, aplikację funkcji z listy rozwijanej i upewnij się, że wybrano pozycję DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

Po utworzeniu rejestracji **klucz podstawowy** rejestracji będzie później używany do konfigurowania symulatora urządzeń w tym artykule.

### <a name="set-up-the-device-simulator"></a>Konfigurowanie symulatora urządzeń

Ten przykład korzysta z symulatora urządzeń, który obejmuje obsługę administracyjną przy użyciu usługi Device Provisioning. Symulator urządzeń znajduje się tutaj: [usługa Azure Digital bliźniaczych reprezentacji i przykład integracji z IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Jeśli przykład nie został jeszcze pobrany, Pobierz go teraz, przechodząc do linku przykładowego i wybierając przycisk *Pobierz zip* poniżej tytułu. Rozpakuj pobrany folder.

#### <a name="upload-the-model"></a>Przekaż model

Symulator urządzeń jest urządzeniem typu termostat, który używa modelu z tym IDENTYFIKATORem: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Musisz przekazać ten model do usługi Azure Digital bliźniaczych reprezentacji, aby można było utworzyć sznurek tego typu dla urządzenia.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Aby uzyskać więcej informacji na temat modeli, zobacz [*How to: Manage models*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Konfigurowanie i uruchamianie symulatora

W oknie wiersza polecenia przejdź do pobranej przykładowej *integracji Digital bliźniaczych reprezentacji i IoT Hub* , która została wcześniej rozmieszczona, a następnie do katalogu *symulatora urządzenia* . Następnie zainstaluj zależności dla projektu przy użyciu następującego polecenia:

```cmd
npm install
```

Następnie w katalogu symulatora urządzenia Skopiuj plik. env. template do nowego pliku o nazwie. env i zbierz następujące wartości, aby wypełnić ustawienia:

* PROVISIONING_IDSCOPE: Aby uzyskać tę wartość, przejdź do usługi Device Provisioning w [Azure Portal](https://portal.azure.com/), a następnie wybierz pozycję *Przegląd* w obszarze Opcje menu i Wyszukaj *Identyfikator pola zakres*.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Zrzut ekranu przedstawiający widok Azure Portal strony Przegląd aprowizacji urządzeń w celu skopiowania wartości identyfikatora zakresu." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: możesz wybrać identyfikator rejestracji dla urządzenia.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: jest to klucz podstawowy skonfigurowanej wcześniejszej rejestracji. Aby ponownie uzyskać tę wartość, przejdź do usługi Device Provisioning w Azure Portal, wybierz pozycję *Zarządzaj rejestracjami*, a następnie wybierz utworzoną wcześniej grupę rejestracji i skopiuj *klucz podstawowy*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Zrzut ekranu przedstawiający widok Azure Portal strony Zarządzanie rejestracjami w usłudze Device Provisioning w celu skopiowania wartości klucza podstawowego SAS." lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

Teraz Użyj powyższych wartości, aby zaktualizować ustawienia pliku ENV.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Zapisz i zamknij plik.

### <a name="start-running-the-device-simulator"></a>Rozpocznij pracę z symulatorem urządzeń

W katalogu *Device-symulator* w oknie polecenia Uruchom symulatora urządzeń przy użyciu następującego polecenia:

```cmd
node .\adt_custom_register.js
```

Powinno zostać wyświetlone urządzenie zarejestrowane i połączone z IoT Hub, a następnie od momentu wysłania wiadomości.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Zrzut ekranu przedstawiający okno Polecenie wyświetlania rejestracji urządzeń i wysyłania komunikatów" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Walidacja

W wyniku przepływu, który został skonfigurowany w tym artykule, urządzenie zostanie automatycznie zarejestrowane w usłudze Azure Digital bliźniaczych reprezentacji. Aby znaleźć sznurki urządzenia w utworzonym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, użyj następującego polecenia [interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md) .

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Powinny być widoczne sznurki urządzenia znajdujące się w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Zrzut ekranu przedstawiający okno Polecenie pokazujący nowo utworzone sznurki." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Autowycofywanie urządzenia przy użyciu IoT Hub zdarzeń cyklu życia

W tej sekcji nastąpi dołączenie IoT Hub zdarzeń cyklu życia do usługi Azure Digital bliźniaczych reprezentacji w celu wycofania urządzeń za pomocą poniższej ścieżki. Jest to fragment ze wszystkich pokazanych [wcześniej](#solution-architecture)architektury.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Diagram przepływu urządzenia Wycofaj — fragment diagramu architektury rozwiązania z numerami etykiet sekcji przepływu. Urządzenie termostatu jest wyświetlane bez połączeń z usługami platformy Azure na diagramie. Dane z ręcznej akcji &quot;Usuń urządzenie&quot; są przesyłane za pośrednictwem IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital bliźniaczych reprezentacji (3)." lightbox="media/how-to-provision-using-dps/retire.png":::

Oto opis przepływu procesu:
1. Proces zewnętrzny lub ręczny wyzwala Usuwanie urządzenia w IoT Hub.
2. IoT Hub usuwa urządzenie i generuje zdarzenie [cyklu życia urządzenia](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) , które będzie kierowane do [centrum zdarzeń](../event-hubs/event-hubs-about.md).
3. Funkcja platformy Azure usuwa sznurek urządzenia w usłudze Azure Digital bliźniaczych reprezentacji.

W poniższych sekcjach opisano procedurę konfigurowania tego przepływu urządzeń.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Następnie utworzysz [centrum zdarzeń](../event-hubs/event-hubs-about.md) platformy Azure, aby otrzymywać IoT Hub zdarzenia cyklu życia. 

Wykonaj kroki opisane w sekcji [*Tworzenie centrum zdarzeń*](../event-hubs/event-hubs-create.md) — Szybki Start. Nazwij centrum zdarzeń *lifecycleevents*. Ta nazwa centrum zdarzeń zostanie użyta podczas konfigurowania IoT Hub tras i funkcji platformy Azure w następnych sekcjach.

Poniższy zrzut ekranu ilustruje tworzenie centrum zdarzeń.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Zrzut ekranu okna Azure Portal, aby utworzyć centrum zdarzeń o nazwie lifecycleevents." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Tworzenie zasad SAS dla centrum zdarzeń

Następnie należy utworzyć [zasady sygnatury dostępu współdzielonego (SAS)](../event-hubs/authorize-access-shared-access-signature.md) , aby skonfigurować centrum zdarzeń w aplikacji funkcji.
W tym celu
1. Przejdź do utworzonego centrum zdarzeń w Azure Portal a następnie wybierz pozycję **zasady dostępu współdzielonego** w opcjach menu po lewej stronie.
2. Wybierz pozycję **Dodaj**. W oknie *Dodawanie zasad SAS* , które zostanie otwarte, wprowadź wybraną nazwę zasady, a następnie zaznacz pole wyboru *nasłuchiwanie* .
3. Wybierz przycisk **Utwórz**.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Zrzut ekranu przedstawiający Azure Portal dodawania zasad SAS centrum zdarzeń." lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Konfigurowanie centrum zdarzeń za pomocą aplikacji funkcji

Następnie skonfiguruj aplikację funkcji platformy Azure, która została skonfigurowana w sekcji [wymagania wstępne](#prerequisites) , aby współdziałać z nowym centrum zdarzeń. W tym celu należy ustawić zmienną środowiskową wewnątrz aplikacji funkcji z parametrami połączenia centrum zdarzeń.

1. Otwórz właśnie utworzone zasady i skopiuj **Parametry połączenia — wartość klucza podstawowego** .

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby skopiować parametry połączenia — klucz podstawowy." lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Dodaj parametry połączenia jako zmienną w ustawieniach aplikacji funkcji za pomocą następującego polecenia platformy Azure. Polecenie można uruchomić w [Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli [na maszynie jest zainstalowany](/cli/azure/install-azure-cli)interfejs wiersza polecenia platformy Azure.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Dodawanie funkcji do wycofywania z IoT Hub zdarzenia cyklu życia

W projekcie aplikacji funkcji, który został utworzony w sekcji [wymagania wstępne](#prerequisites) , utworzysz nową funkcję, która wycofa istniejące urządzenie przy użyciu IoT Hub zdarzenia cyklu życia.

Aby uzyskać więcej informacji o zdarzeniach cyklu życia, zobacz [*IoT Hub zdarzenia telemetrii*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Aby uzyskać więcej informacji na temat używania Event Hubs z usługą Azure Functions, zobacz [*wyzwalacz usługi azure Event Hubs dla Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Zacznij od otwarcia projektu aplikacji funkcji w programie Visual Studio na maszynie i wykonaj poniższe kroki.

#### <a name="step-1-add-a-new-function"></a>Krok 1. Dodawanie nowej funkcji
     
Dodaj nową funkcję typu *wyzwalacz centrum zdarzeń* do projektu aplikacji funkcji w programie Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Zrzut ekranu przedstawiający okno programu Visual Studio, w którym można dodać funkcję platformy Azure typu wyzwalacz centrum zdarzeń w projekcie aplikacji funkcji." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Krok 2. wypełnienie kodu funkcji

W nowo utworzonym pliku kodu funkcji wklej następujący kod, Zmień nazwę funkcji na `DeleteDeviceInTwinFunc.cs` i Zapisz plik.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3. publikowanie aplikacji funkcji na platformie Azure

Opublikuj projekt przy użyciu funkcji *DeleteDeviceInTwinFunc. cs* w aplikacji funkcji na platformie Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Tworzenie trasy IoT Hub dla zdarzeń cyklu życia

Teraz skonfigurujesz trasę IoT Hub, aby kierować zdarzenia dotyczące cyklu życia urządzenia. W takim przypadku nastąpi nasłuchiwanie zdarzeń usunięcia urządzenia, identyfikowanych przez `if (opType == "deleteDeviceIdentity")` . Spowoduje to wyzwolenie usunięcia elementu wieloosiowego, sfinalizowania wycofania urządzenia i jego cyfrowego sznurka.

Najpierw musisz utworzyć punkt końcowy centrum zdarzeń w centrum IoT. Następnie dodasz trasę do centrum IoT Hub, aby wysyłać zdarzenia cyklu życia do tego punktu końcowego centrum zdarzeń.
Wykonaj następujące kroki, aby utworzyć punkt końcowy centrum zdarzeń:

1. W [Azure Portal](https://portal.azure.com/)przejdź do centrum IoT utworzonego w sekcji [wymagania wstępne](#prerequisites) i wybierz pozycję **routing wiadomości** w opcjach menu po lewej stronie.
2. Wybierz kartę **niestandardowe punkty końcowe** .
3. Wybierz pozycję **+ Dodaj** i wybierz pozycję **Centra zdarzeń** , aby dodać punkt końcowy typu centrów zdarzeń.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Zrzut ekranu przedstawiający okno programu Visual Studio, w którym można dodać niestandardowy punkt końcowy centrum zdarzeń." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. W oknie *Dodaj punkt końcowy centrum zdarzeń* , który zostanie otwarty, wybierz następujące wartości:
    * **Nazwa punktu końcowego**: wybierz nazwę punktu końcowego.
    * **Przestrzeń nazw centrum zdarzeń**: Wybierz przestrzeń nazw centrum zdarzeń z listy rozwijanej.
    * **Wystąpienie centrum zdarzeń**: wybierz nazwę centrum zdarzeń utworzoną w poprzednim kroku.
5. Wybierz przycisk **Utwórz**. Pozostaw to okno otwarte, aby dodać trasę w następnym kroku.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Zrzut ekranu przedstawiający okno programu Visual Studio, w którym można dodać punkt końcowy centrum zdarzeń." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Następnie dodasz trasę, która łączy się z punktem końcowym utworzonym w powyższym kroku, za pomocą zapytania routingu, które wysyła zdarzenia usuwania. Wykonaj następujące kroki, aby utworzyć trasę:

1. Przejdź do karty *trasy* , a następnie wybierz pozycję **Dodaj** , aby dodać trasę.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Zrzut ekranu przedstawiający okno programu Visual Studio, w którym można dodać trasę do wysyłania zdarzeń." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. Na stronie *Dodawanie trasy* wybierz następujące wartości:

   * **Nazwa**: wybierz nazwę trasy. 
   * **Punkt końcowy**: Wybierz punkt końcowy usługi Event Hub utworzony wcześniej z listy rozwijanej.
   * **Źródło danych**: wybierz pozycję *zdarzenia cyklu życia urządzenia*.
   * **Zapytanie routingu**: ENTER `opType='deleteDeviceIdentity'` . To zapytanie ogranicza zdarzenia cyklu życia urządzenia, aby wysyłać tylko zdarzenia Delete.

3. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Zrzut ekranu przedstawiający okno Azure Portal, aby dodać trasę do wysyłania zdarzeń cyklu życia." lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

Po przeprowadzeniu tego przepływu wszystko jest ustawione na wycofanie urządzeń.

### <a name="validate"></a>Walidacja

Aby wyzwolić proces wycofania, należy ręcznie usunąć urządzenie z IoT Hub.

Można to zrobić za pomocą [polecenia platformy Azure CLI](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) lub w Azure Portal. Wykonaj poniższe kroki, aby usunąć urządzenie z Azure Portal:

1. Przejdź do centrum IoT Hub, a następnie wybierz pozycję **urządzenia IoT** w obszarze Opcje menu po lewej stronie. 
2. Zobaczysz urządzenie z IDENTYFIKATORem rejestracji urządzenia wybranym w [pierwszej połowie tego artykułu](#auto-provision-device-using-device-provisioning-service). Alternatywnie można wybrać dowolne inne urządzenie do usunięcia, o ile ma on sznurki w usłudze Azure Digital bliźniaczych reprezentacji, dzięki czemu można sprawdzić, czy dwuosiowy jest automatycznie usuwany po usunięciu urządzenia.
3. Wybierz urządzenie i wybierz pozycję **Usuń**.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Zrzut ekranu przedstawiający Azure Portal usuwania sznurka urządzenia z urządzeń IoT." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Wyświetlanie zmian wprowadzonych w usłudze Azure Digital bliźniaczych reprezentacji może potrwać kilka minut.

Aby sprawdzić, czy usunięto dwuosiową urządzenie w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, użyj następującego polecenia [interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md) .

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Należy się dowiedzieć, że nie można już znaleźć sznurka urządzenia w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Zrzut ekranu przedstawiający okno Polecenie nie znaleziono sznurka." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są już potrzebne, wykonaj następujące kroki, aby je usunąć.

Korzystając z Azure Cloud Shell lub lokalnego interfejsu wiersza polecenia platformy Azure, możesz usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) . Spowoduje to usunięcie grupy zasobów. wystąpienie usługi Azure Digital bliźniaczych reprezentacji; Centrum IoT i Rejestracja urządzenia Hub; temat dotyczący siatki zdarzeń i skojarzonych subskrypcji; Przestrzeń nazw usługi Event Hub i obie Azure Functions aplikacje, w tym skojarzone zasoby, takie jak magazyn.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Następnie usuń przykładowy folder projektu pobrany z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

Cyfrowe bliźniaczych reprezentacji utworzone dla urządzeń są przechowywane jako płaska hierarchia w usłudze Azure Digital bliźniaczych reprezentacji, ale mogą być wzbogacane przy użyciu informacji o modelu i wielopoziomowej hierarchii dla organizacji. Aby dowiedzieć się więcej na temat koncepcji, Przeczytaj:

* [*Pojęcia: Digital bliźniaczych reprezentacji i wykres bliźniaczy*](concepts-twins-graph.md)

Aby uzyskać więcej informacji na temat korzystania z żądań HTTP z usługą Azure Functions, zobacz:

* [*Wyzwalacz żądania HTTP platformy Azure dla Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Można napisać logikę niestandardową, aby automatycznie podawać te informacje za pomocą modelu i danych grafu przechowywanych już w usłudze Azure Digital bliźniaczych reprezentacji. Aby dowiedzieć się więcej o zarządzaniu, uaktualnianiu i pobieraniu informacji z grafu bliźniaczych reprezentacji, zobacz następujące tematy:

* [*Instrukcje: Zarządzanie dwuosiową cyfrą*](how-to-manage-twin.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)