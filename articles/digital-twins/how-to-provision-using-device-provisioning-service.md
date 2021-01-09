---
title: Autozarządzanie urządzeniami przy użyciu usługi DPS
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować zautomatyzowany proces, aby udostępnić i wycofać urządzenia IoT w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu usługi Device Provisioning Service (DPS).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e783e5dd3b0f1952928d1c36c682c5be1cba2599
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044394"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Autozarządzanie urządzeniami w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu usługi Device Provisioning Service (DPS)

W tym artykule dowiesz się, jak zintegrować usługę Azure Digital bliźniaczych reprezentacji z [usługą Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md).

Rozwiązanie opisane w tym artykule umożliwi automatyzację procesu **_inicjowania obsługi_** i **_wycofywania_** IoT Hub urządzeń w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu usługi Device Provisioning. 

Aby uzyskać więcej _informacji na temat aprowizacji i_ _wycofywania_ etapów oraz lepiej zrozumieć zestaw ogólnych etapów zarządzania urządzeniami, które są wspólne dla wszystkich projektów IoT w przedsiębiorstwie, zobacz [sekcję *cykl życia urządzenia*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) w dokumentacji dotyczącej zarządzania urządzeniami w IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfigurowania aprowizacji należy mieć **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** , które zawiera modele i bliźniaczych reprezentacji. To wystąpienie należy również skonfigurować z możliwością aktualizacji informacji cyfrowych przędzy na podstawie danych. 

Jeśli jeszcze tego nie zrobiono, możesz go utworzyć, postępując zgodnie z samouczkiem Digital bliźniaczych reprezentacji na platformie Azure [*: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md). Ten samouczek przeprowadzi Cię przez proces konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji z modelami i bliźniaczych reprezentacji, połączonymi [IoT Hub](../iot-hub/about-iot-hub.md)platformy Azure i kilkoma [funkcjami platformy Azure](../azure-functions/functions-overview.md) w celu propagowania przepływu danych.

Poniższe wartości będą potrzebne w dalszej części tego artykułu od momentu skonfigurowania wystąpienia. Jeśli musisz ponownie zebrać te wartości, Skorzystaj z poniższych linków, aby uzyskać instrukcje.
* **_Nazwa hosta_** wystąpienia usługi Azure Digital bliźniaczych reprezentacji ([Znajdź w portalu](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_Parametry połączenia_** parametrów połączenia z usługą Azure Event Hubs ([Znajdź w portalu](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

Ten przykład używa również **symulatora urządzeń** , który obejmuje obsługę administracyjną przy użyciu usługi Device Provisioning. Symulator urządzeń znajduje się tutaj: [usługa Azure Digital bliźniaczych reprezentacji i przykład integracji z IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Pobierz przykładowy projekt na swoją maszynę, przechodząc do linku przykładowego i wybierając przycisk *Pobierz zip* poniżej tytułu. Rozpakuj pobrany folder.

Symulator urządzeń jest oparty na **Node.js**, w wersji 10.0. x lub nowszej. [*Przygotowanie środowiska programistycznego*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) opisuje sposób instalowania Node.js na potrzeby tego samouczka w systemie Windows lub Linux.

## <a name="solution-architecture"></a>Architektura rozwiązania

Na poniższym obrazie przedstawiono architekturę tego rozwiązania przy użyciu narzędzia Azure Digital bliźniaczych reprezentacji z usługą Device Provisioning. Pokazuje zarówno proces aprowizacji, jak i wycofywania urządzenia.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Widok urządzenia i kilku usług platformy Azure w kompleksowym scenariuszu. Dane są przepływane między urządzeniem termostatu a działem DPS i z powrotem. Dane są również przepływane z usługi DPS do IoT Hub i do Digital bliźniaczych reprezentacji na platformie Azure za pośrednictwem funkcji platformy Azure o nazwie &quot;Allocation&quot;. Dane z ręcznej akcji &quot;Usuń urządzenie&quot; są przepływem przez IoT Hub > Event Hubs > Azure Functions > Azure Digital bliźniaczych reprezentacji.":::

Ten artykuł jest podzielony na dwie sekcje:
* [*Inicjowanie obsługi administracyjnej urządzenia przy użyciu usługi Device Provisioning*](#auto-provision-device-using-device-provisioning-service)
* [*Autowycofywanie urządzenia przy użyciu IoT Hub zdarzeń cyklu życia*](#auto-retire-device-using-iot-hub-lifecycle-events)

Aby uzyskać dokładniejsze wyjaśnienia poszczególnych kroków architektury, zobacz poszczególne sekcje w dalszej części artykułu.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Inicjowanie obsługi administracyjnej urządzenia przy użyciu usługi Device Provisioning

W tej sekcji zostanie dołączana usługa Device Provisioning do usługi Azure Digital bliźniaczych reprezentacji w celu samodzielnego udostępnienia urządzeń za pomocą poniższej ścieżki. Jest to fragment ze wszystkich pokazanych [wcześniej](#solution-architecture)architektury.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Zainicjuj obsługę przepływu — fragment diagramu architektury rozwiązania z numerami etykiet sekcji przepływu. Dane są przepływane między urządzeniem termostatu a działem DPS (1 w przypadku urządzenia > DPS i 5 dla usługi DPS > urządzeniu). Dane są również przepływane z usługi DPS do IoT Hub (4) oraz do programu Azure Digital bliźniaczych reprezentacji (3) za pośrednictwem funkcji platformy Azure o nazwie &quot;Allocation&quot; (2).":::

Oto opis przepływu procesu:
1. Urządzenie kontaktuje się z punktem końcowym DPS, przekazując informacje identyfikacyjne w celu potwierdzenia tożsamości.
2. Usługa DPS sprawdza poprawność tożsamości urządzenia, sprawdzając Identyfikator rejestracji i klucz na liście rejestracji, a następnie wywołuje [funkcję platformy Azure](../azure-functions/functions-overview.md) w celu wykonania przydziału.
3. Funkcja platformy Azure tworzy nowe [sznurki](concepts-twins-graph.md) w usłudze Azure Digital bliźniaczych reprezentacji dla urządzenia.
4. Usługa DPS rejestruje urządzenie w usłudze IoT Hub i wypełnia żądany stan dwuosiowy urządzenia.
5. Centrum IoT Hub zwraca informacje o IDENTYFIKATORze urządzenia oraz informacje o połączeniu usługi IoT Hub z urządzeniem. Urządzenie może teraz połączyć się z usługą IoT Hub.

W poniższych sekcjach opisano kroki umożliwiające skonfigurowanie tego przepływu urządzenia automatyczne Inicjowanie obsługi.

### <a name="create-a-device-provisioning-service"></a>Tworzenie usługi Device Provisioning

Po zainicjowaniu nowego urządzenia przy użyciu usługi Device Provisioning można utworzyć nową sznurek dla tego urządzenia w usłudze Azure Digital bliźniaczych reprezentacji.

Utwórz wystąpienie usługi Device Provisioning, które będzie używane do udostępniania urządzeń IoT. Możesz użyć poniższych instrukcji interfejsu wiersza polecenia platformy Azure lub użyć Azure Portal: [*Szybki Start: skonfiguruj IoT Hub Device Provisioning Service przy użyciu Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Następujące polecenie interfejsu wiersza polecenia platformy Azure utworzy usługę Device Provisioning. Należy określić nazwę, grupę zasobów i region. Polecenie można uruchomić w [Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli [na maszynie jest zainstalowany](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)interfejs wiersza polecenia platformy Azure.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Następnie utworzysz funkcję wyzwalaną przez żądanie HTTP w aplikacji funkcji. Możesz użyć aplikacji funkcji utworzonej w kompleksowym samouczku ([*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)) lub własnych.

Ta funkcja będzie używana przez usługę Device Provisioning w [niestandardowych zasadach alokacji](../iot-dps/how-to-use-custom-allocation-policies.md) w celu aprowizacji nowego urządzenia. Aby uzyskać więcej informacji na temat korzystania z żądań HTTP z usługą Azure Functions, zobacz [*wyzwalacz żądań HTTP platformy Azure dla Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md).

W projekcie aplikacji funkcji Dodaj nową funkcję. Ponadto Dodaj nowy pakiet NuGet do projektu: `Microsoft.Azure.Devices.Provisioning.Service` .

W nowo utworzonym pliku kodu funkcji wklej następujący kod.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIotHub_allocate.cs":::

Zapisz plik, a następnie ponownie Opublikuj swoją aplikację funkcji. Instrukcje dotyczące publikowania aplikacji funkcji znajdują się w sekcji [*publikowanie aplikacji*](tutorial-end-to-end.md#publish-the-app) w kompleksowym samouczku.

### <a name="configure-your-function"></a>Skonfiguruj funkcję

Następnie musisz ustawić zmienne środowiskowe w aplikacji funkcji z wcześniejszych wersji, zawierającej odwołanie do utworzonego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. W przypadku korzystania z kompleksowego samouczka ([*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)) ustawienie zostanie już skonfigurowane.

Dodaj ustawienie za pomocą tego polecenia platformy Azure:

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Upewnij się, że uprawnienia i zarządzane przypisanie roli tożsamości są poprawnie skonfigurowane dla aplikacji funkcji, zgodnie z opisem w sekcji [*przypisywanie uprawnień do aplikacji funkcji*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) w kompleksowym samouczku.

### <a name="create-device-provisioning-enrollment"></a>Utwórz rejestrację aprowizacji urządzeń

Następnie musisz utworzyć rejestrację w usłudze Device Provisioning przy użyciu **funkcji alokacji niestandardowej**. Postępuj zgodnie z instrukcjami, aby to zrobić w sekcjach [*Tworzenie rejestracji*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) i [*wyprowadzanie unikatowych kluczy urządzeń*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) w artykule usługi Device Provisioning Services — informacje o niestandardowych zasadach alokacji.

Podczas przechodzenia przez ten przepływ nastąpi połączenie rejestracji z właśnie utworzoną funkcją, wybierając funkcję w trakcie tego kroku, aby **wybrać sposób przypisywania urządzeń do centrów**. Po utworzeniu rejestracji nazwa rejestracji i podstawowy lub pomocniczy klucz SAS będą później używane do konfigurowania symulatora urządzeń w tym artykule.

### <a name="set-up-the-device-simulator"></a>Konfigurowanie symulatora urządzeń

Ten przykład korzysta z symulatora urządzeń, który obejmuje obsługę administracyjną przy użyciu usługi Device Provisioning. Symulator urządzeń znajduje się tutaj: [usługa Azure Digital bliźniaczych reprezentacji i przykład integracji z IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Jeśli przykład nie został jeszcze pobrany, Pobierz go teraz, przechodząc do linku przykładowego i wybierając przycisk *Pobierz zip* poniżej tytułu. Rozpakuj pobrany folder.

Otwórz okno polecenia i przejdź do pobranego folderu, a następnie do katalogu *Device-symulator* . Zainstaluj zależności dla projektu przy użyciu następującego polecenia:

```cmd
npm install
```

Następnie skopiuj plik *ENV. Template* do nowego pliku o nazwie *ENV* i wprowadź następujące ustawienia:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Zapisz i zamknij plik.

### <a name="start-running-the-device-simulator"></a>Rozpocznij pracę z symulatorem urządzeń

W katalogu *Device-symulator* w oknie polecenia Uruchom symulatora urządzeń przy użyciu następującego polecenia:

```cmd
node .\adt_custom_register.js
```

Powinno zostać wyświetlone urządzenie zarejestrowane i połączone z IoT Hub, a następnie od momentu wysłania wiadomości.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="okno Polecenie przedstawiające rejestrowanie urządzeń i wysyłanie komunikatów":::

### <a name="validate"></a>Walidacja

W wyniku przepływu, który został skonfigurowany w tym artykule, urządzenie zostanie automatycznie zarejestrowane w usłudze Azure Digital bliźniaczych reprezentacji. Aby znaleźć sznurki urządzenia w utworzonym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, użyj następującego polecenia [interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md) .

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Powinny być widoczne sznurki urządzenia znajdujące się w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="okno Polecenie pokazujący nowo utworzone sznury":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Autowycofywanie urządzenia przy użyciu IoT Hub zdarzeń cyklu życia

W tej sekcji nastąpi dołączenie IoT Hub zdarzeń cyklu życia do usługi Azure Digital bliźniaczych reprezentacji w celu wycofania urządzeń za pomocą poniższej ścieżki. Jest to fragment ze wszystkich pokazanych [wcześniej](#solution-architecture)architektury.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Wycofywanie przepływu urządzenia — fragment diagramu architektury rozwiązania z numerami etykiet sekcji przepływu. Urządzenie termostatu jest wyświetlane bez połączeń z usługami platformy Azure na diagramie. Dane z ręcznej akcji &quot;Usuń urządzenie&quot; są przesyłane za pośrednictwem IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital bliźniaczych reprezentacji (3).":::

Oto opis przepływu procesu:
1. Proces zewnętrzny lub ręczny wyzwala Usuwanie urządzenia w IoT Hub.
2. IoT Hub usuwa urządzenie i generuje zdarzenie [cyklu życia urządzenia](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) , które będzie kierowane do [centrum zdarzeń](../event-hubs/event-hubs-about.md).
3. Funkcja platformy Azure usuwa sznurek urządzenia w usłudze Azure Digital bliźniaczych reprezentacji.

W poniższych sekcjach opisano procedurę konfigurowania tego przepływu urządzeń.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Teraz musisz utworzyć [centrum zdarzeń](../event-hubs/event-hubs-about.md)platformy Azure, które będzie używane do odbierania IoT Hub zdarzeń cyklu życia. 

Wykonaj kroki opisane w sekcji [*Tworzenie centrum zdarzeń*](../event-hubs/event-hubs-create.md) — Szybki Start, używając następujących informacji:
* Jeśli używasz kompleksowego samouczka ([*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)), możesz ponownie użyć grupy zasobów utworzonej na potrzeby kompleksowego samouczka.
* Nazwij centrum zdarzeń *lifecycleevents* lub inny wybór i Zapamiętaj utworzoną przestrzeń nazw. Zostaną one użyte podczas konfigurowania funkcji cyklu życia i IoT Hub trasy w następnych sekcjach.

### <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Następnie utworzysz funkcję wyzwalającą Event Hubs wewnątrz aplikacji funkcji. Możesz użyć aplikacji funkcji utworzonej w kompleksowym samouczku ([*Samouczek: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md)) lub własnych. 

Nazwij wyzwalacz centrum zdarzeń *lifecycleevents* i Połącz wyzwalacz centrum zdarzeń z centrum zdarzeń utworzonym w poprzednim kroku. Jeśli użyto innej nazwy centrum zdarzeń, Zmień ją na zgodną z nazwą wyzwalacza poniżej.

Ta funkcja będzie używać zdarzenia cyklu życia urządzenia IoT Hub, aby wycofać istniejące urządzenie. Aby uzyskać więcej informacji o zdarzeniach cyklu życia, zobacz [*IoT Hub zdarzenia telemetrii*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Aby uzyskać więcej informacji na temat używania Event Hubs z usługą Azure Functions, zobacz [*wyzwalacz usługi azure Event Hubs dla Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

W opublikowanej aplikacji funkcji Dodaj nową klasę funkcji typu *wyzwalacz centrum zdarzeń* i wklej kod poniżej.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIotHub_delete.cs":::

Zapisz projekt, a następnie ponownie Opublikuj aplikację funkcji. Instrukcje dotyczące publikowania aplikacji funkcji znajdują się w sekcji [*publikowanie aplikacji*](tutorial-end-to-end.md#publish-the-app) w kompleksowym samouczku.

### <a name="configure-your-function"></a>Skonfiguruj funkcję

Następnie musisz ustawić zmienne środowiskowe w aplikacji funkcji z wcześniejszych wersji, zawierającej odwołanie do utworzonego wystąpienia usługi Azure Digital bliźniaczych reprezentacji i centrum zdarzeń. Jeśli używasz kompleksowego samouczka ([*Samouczek: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md)), pierwsze ustawienie zostanie już skonfigurowane.

Dodaj ustawienie za pomocą tego polecenia platformy Azure. Polecenie można uruchomić w [Cloud Shell](https://shell.azure.com)lub lokalnie, jeśli [na maszynie jest zainstalowany](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)interfejs wiersza polecenia platformy Azure.

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Następnie należy skonfigurować zmienną środowiskową funkcji do nawiązywania połączenia z nowo utworzonym centrum zdarzeń.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Upewnij się, że uprawnienia i zarządzane przypisanie roli tożsamości są poprawnie skonfigurowane dla aplikacji funkcji, zgodnie z opisem w sekcji [*przypisywanie uprawnień do aplikacji funkcji*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) w kompleksowym samouczku.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Tworzenie trasy IoT Hub dla zdarzeń cyklu życia

Teraz musisz skonfigurować trasę IoT Hub, aby kierować zdarzenia dotyczące cyklu życia urządzenia. W takim przypadku nastąpi nasłuchiwanie zdarzeń usunięcia urządzenia, identyfikowanych przez `if (opType == "deleteDeviceIdentity")` . Spowoduje to wyzwolenie usunięcia elementu wieloosiowego, sfinalizowania wycofania urządzenia i jego cyfrowego sznurka.

Instrukcje dotyczące tworzenia trasy IoT Hub są opisane w tym artykule: [*użyj IoT Hub Routing komunikatów do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych*](../iot-hub/iot-hub-devguide-messages-d2c.md). W sekcji *zdarzenia telemetrii* objaśniono, że można użyć **zdarzeń cyklu życia urządzenia** jako źródła danych dla trasy.

Kroki, które należy wykonać w ramach tej konfiguracji, to:
1. Utwórz niestandardowy punkt końcowy centrum zdarzeń IoT Hub. Ten punkt końcowy powinien wskazywać centrum zdarzeń utworzone w sekcji [*Tworzenie centrum zdarzeń*](#create-an-event-hub) .
2. Dodaj trasę *zdarzeń cyklu życia urządzenia* . Użyj punktu końcowego utworzonego w poprzednim kroku. Można ograniczyć zdarzenia cyklu życia urządzenia, aby wysyłać tylko zdarzenia Delete przez dodanie zapytania routingu `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Dodawanie trasy":::

Po przeprowadzeniu tego przepływu wszystko jest ustawione na wycofanie urządzeń.

### <a name="validate"></a>Walidacja

Aby wyzwolić proces wycofania, należy ręcznie usunąć urządzenie z IoT Hub.

W [pierwszej połowie tego artykułu](#auto-provision-device-using-device-provisioning-service)utworzono urządzenie w IoT Hub i odpowiadające im sznurki cyfrowe. 

Teraz przejdź do IoT Hub i usuń to urządzenie (możesz to zrobić za pomocą [polecenia interfejsu CLI platformy Azure](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest&preserve-view=true#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) lub w [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

Urządzenie zostanie automatycznie usunięte z usługi Azure Digital bliźniaczych reprezentacji. 

Aby sprawdzić, czy usunięto dwuosiową urządzenie w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, użyj następującego polecenia [interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md) .

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Należy się dowiedzieć, że nie można już znaleźć sznurka urządzenia w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Nie znaleziono okno Polecenie":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są już potrzebne, wykonaj następujące kroki, aby je usunąć.

Korzystając z Azure Cloud Shell lub lokalnego interfejsu wiersza polecenia platformy Azure, możesz usunąć wszystkie zasoby platformy Azure w grupie zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Spowoduje to usunięcie grupy zasobów. wystąpienie usługi Azure Digital bliźniaczych reprezentacji; Centrum IoT i Rejestracja urządzenia Hub; temat dotyczący siatki zdarzeń i skojarzonych subskrypcji; Przestrzeń nazw usługi Event Hub i obie Azure Functions aplikacje, w tym skojarzone zasoby, takie jak magazyn.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Następnie usuń przykładowy folder projektu pobrany z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

Cyfrowe bliźniaczych reprezentacji utworzone dla urządzeń są przechowywane jako płaska hierarchia w usłudze Azure Digital bliźniaczych reprezentacji, ale mogą być wzbogacane przy użyciu informacji o modelu i wielopoziomowej hierarchii dla organizacji. Aby dowiedzieć się więcej na temat koncepcji, Przeczytaj:

* [*Pojęcia: Digital bliźniaczych reprezentacji i wykres bliźniaczy*](concepts-twins-graph.md)

Można napisać logikę niestandardową, aby automatycznie podawać te informacje za pomocą modelu i danych grafu przechowywanych już w usłudze Azure Digital bliźniaczych reprezentacji. Aby dowiedzieć się więcej o zarządzaniu, uaktualnianiu i pobieraniu informacji z grafu bliźniaczych reprezentacji, zobacz następujące tematy:

* [*Instrukcje: Zarządzanie dwuosiową cyfrą*](how-to-manage-twin.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)