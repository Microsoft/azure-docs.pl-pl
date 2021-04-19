---
title: Wdrażanie Azure IoT Central mostka urządzenia | Microsoft Docs
description: Wd wdrażają IoT Central urządzenia, aby połączyć inne chmury IoT z IoT Central aplikacji. Inne chmury IoT to Sigfox, Particle Device Cloud i The Things Network.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713744"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Użyj mostka IoT Central, aby połączyć inne chmury IoT z IoT Central

*Ten artykuł dotyczy administratorów.*

## <a name="azure-iot-central-device-bridge"></a>Azure IoT Central mostka urządzenia

Mostek IoT Central to rozwiązanie typu open source, które łączy inne chmury IoT z twoją IoT Central aplikacji. Przykłady innych chmur IoT to [Sigfox,](https://www.sigfox.com/) [Particle Device Cloud](https://www.particle.io/)i The Things [Network.](https://www.thethingsnetwork.org/) Mostek urządzenia działa przez przekazywanie danych z urządzeń połączonych z innymi chmurami IoT do IoT Central aplikacji. Mostek urządzenia przekazuje dane tylko do IoT Central, nie wysyła poleceń ani aktualizacji właściwości z IoT Central z powrotem do urządzeń.

Mostek urządzenia umożliwia połączenie możliwości usługi IoT Central z urządzeniami, takimi jak urządzenia do śledzenia zasobów połączone z siecią o niskim po zasilania, urządzenia monitorujące jakość powietrza w chmurze Particle Device Cloud lub urządzenia monitorujące wilgotność gleby w sieci Rzeczy. Możesz używać IoT Central aplikacji, takich jak reguły i analiza danych, tworzyć przepływy pracy w usługach Power Automate i Azure Logic Apps lub eksportować dane.

Rozwiązanie mostka urządzenia apowiuje kilka zasobów platformy Azure w ramach subskrypcji platformy Azure, które współpracują ze sobą w celu przekształcania i przekazywania komunikatów urządzeń do IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Wykonaj czynności [z przewodnika Szybki start Tworzenie Azure IoT Central](./quick-deploy-iot-central.md) aplikacji, aby utworzyć aplikację IoT Central przy użyciu szablonu aplikacji **niestandardowej > aplikacji niestandardowej.**

## <a name="overview"></a>Omówienie

Mostek IoT Central to rozwiązanie typu open source w usłudze GitHub. Używa on niestandardowego szablonu Azure Resource Manager wdrożyć kilka zasobów do subskrypcji platformy Azure, w tym aplikację funkcji platformy Azure.

Aplikacja funkcji jest podstawowym elementem mostka urządzenia. Odbiera żądania HTTP POST z innych platform IoT za pośrednictwem prostego webhook. Repozytorium [Azure IoT Central Device Bridge](https://github.com/Azure/iotc-device-bridge) zawiera przykłady, które pokazują, jak połączyć chmury Sigfox, Particle i The Things Network. To rozwiązanie można rozszerzyć, aby połączyć się z niestandardową chmurą IoT, jeśli platforma może wysyłać żądania HTTP POST do aplikacji funkcji.

Aplikacja funkcji przekształca dane do formatu akceptowanego przez usługę IoT Central i przekazuje je przy użyciu usługi device provisioning i interfejsów API klienta urządzenia:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Zrzut ekranu przedstawiający usługę Azure Functions.":::

Jeśli aplikacja IoT Central rozpozna identyfikator urządzenia w komunikacie przesyłanych dalej, dane telemetryczne z urządzenia będą wyświetlane w IoT Central. Jeśli identyfikator urządzenia nie zostanie rozpoznany przez aplikację IoT Central, aplikacja funkcji podejmie próbę zarejestrowania nowego urządzenia przy użyciu identyfikatora urządzenia. Nowe urządzenie jest wyświetlane jako **Urządzenie nieskojarzeniowe** na **stronie** Urządzenia w IoT Central aplikacji. Na stronie **Urządzenia** możesz skojarzyć nowe urządzenie z szablonem urządzenia, a następnie wyświetlić telemetrię.

## <a name="deploy-the-device-bridge"></a>Wdrażanie mostka urządzenia

Aby wdrożyć mostek urządzeń w subskrypcji:

1. W aplikacji IoT Central przejdź do strony **Administracja > połączenie** urządzenia.

    1. Zanotuj zakres **identyfikatorów**. Tej wartości należy użyć podczas wdrażania mostka urządzenia.

    1. Na tej samej stronie otwórz grupę **rejestracji SAS-IoT-Devices.** Na stronie **grupy SAS-IoT-Devices** skopiuj **klucz podstawowy**. Tej wartości należy użyć podczas wdrażania mostka urządzenia.

1. Użyj **poniższego przycisku Deploy to Azure** (Wd wdrażaj na platformie Azure), aby otworzyć Resource Manager szablonu aplikacji funkcji, który wdraża aplikację funkcji w subskrypcji. Użyj zakresu **identyfikatorów** i **klucza podstawowego z** poprzedniego kroku:

    [![Wdrażanie na platformie Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Po zakończeniu wdrażania należy zainstalować pakiety NPM wymagane przez funkcję:

1. W Azure Portal otwórz aplikację funkcji, która została wdrożona w subskrypcji. Następnie przejdź do **menu Narzędzia programowe > konsoli** programu . W konsoli programu uruchom następujące polecenia, aby zainstalować pakiety:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Uruchomienie tych poleceń może potrwać kilka minut. Możesz bezpiecznie zignorować wszystkie komunikaty ostrzegawcze.

1. Po zakończeniu instalacji pakietu wybierz pozycję Uruchom **ponownie** na **stronie Przegląd** aplikacji funkcji:

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Zrzut ekranu przedstawiający ponowne uruchomienie.":::

1. Funkcja jest teraz gotowa do użycia. Systemy zewnętrzne mogą używać żądań HTTP POST do wysyłania danych urządzenia za pośrednictwem mostka urządzenia do IoT Central aplikacji. Aby uzyskać adres URL funkcji, przejdź do > **IoTCIntegration > Code + Test > Pobierz adres URL funkcji:**

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Zrzut ekranu przedstawiający uzyskiwanie adresu URL funkcji.":::

Treści komunikatów wysyłane do mostka urządzenia muszą mieć następujący format:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Każdy klucz w obiekcie musi odpowiadać nazwie typu telemetrii w szablonie urządzenia w IoT Central `measurements` aplikacji. To rozwiązanie nie obsługuje określania identyfikatora interfejsu w treści komunikatu. Jeśli więc dwa różne interfejsy mają typ telemetrii o tej samej nazwie, miara pojawia się w obu strumieniach telemetrii w IoT Central aplikacji.

Możesz dołączyć `timestamp` pole w treści, aby określić datę i czas UTC komunikatu. To pole musi mieć format ISO 8601. Na przykład `2020-06-08T20:16:54.602Z`. Jeśli nie uwzględnisz znacznika czasu, zostanie użyta bieżąca data i godzina.

Pole można `modelId` uwzględnić w treści. Użyj tego pola, aby skojarzyć urządzenie z szablonem urządzenia podczas aprowizowania. Ta funkcja jest obsługiwana tylko przez [aplikacje w wersji 3.](howto-get-app-info.md)

Musi `deviceId` być alfanumeryczne, małe litery i może zawierać łączniki.

Jeśli nie uwzględnisz pola lub jeśli IoT Central nie rozpozna identyfikatora modelu, komunikat z nierozpoznaną wartością spowoduje utworzenie nowego nieskojarzonych urządzenia w `modelId` `deviceId` IoT Central.  Operator może ręcznie przeprowadzić migrację urządzenia do odpowiedniego szablonu urządzenia. Aby dowiedzieć się więcej, zobacz Zarządzanie urządzeniami w [Azure IoT Central aplikacji > Migrowanie urządzeń do szablonu.](howto-manage-devices.md)

W [aplikacjach w wersji 2](howto-get-app-info.md)nowe urządzenie jest wyświetlane **na Device Explorer > urządzenia nieskojarzonych.** Wybierz **pozycję Skojarz** i wybierz szablon urządzenia, aby rozpocząć odbieranie przychodzących danych telemetrycznych z urządzenia.

> [!NOTE]
> Dopóki urządzenie nie zostanie skojarzone z szablonem, wszystkie wywołania HTTP do funkcji zwracają stan błędu 403.

Aby włączyć rejestrowanie dla aplikacji funkcji za pomocą Application Insights, przejdź do tematu Monitorowanie dzienników > **w** aplikacji funkcji w Azure Portal. Wybierz **pozycję Włącz Application Insights**.

## <a name="provisioned-resources"></a>Aprowowane zasoby

Szablon Resource Manager apowiuje następujące zasoby w subskrypcji platformy Azure:

* Aplikacja funkcji
* Plan usługi App Service
* Konto magazynu
* Magazyn kluczy

Magazyn kluczy przechowuje klucz grupy sygnatury dostępu współdzielonego IoT Central aplikacji.

Aplikacja funkcji działa w [planie zużycia.](https://azure.microsoft.com/pricing/details/functions/) Chociaż ta opcja nie oferuje dedykowanych zasobów obliczeniowych, umożliwia mostkowi urządzenia obsługę setek komunikatów urządzeń na minutę, odpowiednich dla mniejszych floty urządzeń lub urządzeń, które wysyłają komunikaty rzadziej. Jeśli aplikacja zależy od przesyłania strumieniowego dużej liczby komunikatów urządzeń, zastąp plan zużycia dedykowanym planem [usługi App Service.](https://azure.microsoft.com/pricing/details/app-service/windows/) Ten plan oferuje dedykowane zasoby obliczeniowe, które zapewniają krótszy czas odpowiedzi serwera. Korzystając ze standardowego App Service, maksymalna zaobserwowana wydajność funkcji platformy Azure w tym repozytorium wynosi około 1500 komunikatów urządzeń na minutę. Aby dowiedzieć się więcej, zobacz [Opcje hostingu funkcji platformy Azure.](../../azure-functions/functions-scale.md)

Aby użyć dedykowanego App Service zamiast planu zużycia, przed wdrożeniem edytuj szablon niestandardowy. Wybierz **pozycję Edytuj szablon.**

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Zrzut ekranu przedstawiający edytowanie szablonu.":::

Zastąp następujący segment:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

with

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Następnie edytuj szablon, aby uwzględnić go w konfiguracji zasobu w obszarze Konfiguracja zawsze wł. zapewnia, że aplikacja `"alwaysOn": true` `functionapp` funkcji jest zawsze `"properties": {"SiteConfig": {...}}` uruchomiona. [](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan)

## <a name="examples"></a>Przykłady

W poniższych przykładach przedstawiono sposób konfigurowania mostka urządzenia dla różnych chmur IoT:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Przykład 1: Łączenie urządzeń Cząstki za pośrednictwem mostka urządzenia

Aby podłączyć urządzenie Particle za pośrednictwem mostka urządzenia do IoT Central, przejdź do konsoli Particle i utwórz nową integrację z urządzeniami webhook. Ustaw format **żądania na** **wartość JSON.**  W **obszarze Ustawienia zaawansowane** użyj następującego niestandardowego formatu treści:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Wklej adres **URL funkcji** z aplikacji funkcji platformy Azure. Zobaczysz, że urządzenia Particle są wyświetlane jako urządzenia nieskojarzonych w IoT Central. Aby dowiedzieć się więcej, zobacz wpis w blogu [Here's how to integrate your Particle-powered projects with Azure IoT Central](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) blog (Jak zintegrować projekty oparte na cząstkach z Azure IoT Central bloga).

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Przykład 2: Łączenie urządzeń Sigfox za pośrednictwem mostka urządzenia

Niektóre platformy mogą nie zezwalać na określanie formatu komunikatów urządzenia wysyłanych za pośrednictwem urządzenia webhook. W przypadku takich systemów należy przekonwertować ładunek komunikatu na oczekiwany format treści, zanim mostek urządzenia go przetnie. Konwersję można wykonać w tej samej funkcji platformy Azure, która uruchamia mostek urządzenia.

W tej sekcji pokazano, jak przekonwertować ładunek integracji z urządzeniem webhook Sigfox na format treści oczekiwany przez mostek urządzenia. Chmura Sigfox przesyła dane urządzenia w formacie szesnastkowym ciągu. Dla wygody mostek urządzenia zawiera funkcję konwersji dla tego formatu, która akceptuje podzbiór możliwych typów pól w ładunku urządzenia Sigfox: i `int` `uint` 8, 16, 32 lub 64 bitów; `float` 32-bitowych lub 64-bitowych; little-endian i big endian. Aby przetwarzać komunikaty z integracji z elementyem webhook Sigfox, należy wprowadzić następujące zmiany w pliku _IoTCIntegration/index.js_ w aplikacji funkcji.

Aby przekonwertować ładunek komunikatu, dodaj następujący kod przed wywołaniem funkcji w wierszu 21, zastępując zmienną definicją ładunku `handleMessage` `payloadDefinition` Sigfox:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Urządzenia Sigfox oczekują `204` kodu odpowiedzi. Dodaj następujący kod po wywołaniu w `handleMessage` wierszu 21:

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Przykład 3: Łączenie urządzeń z sieci Rzeczy za pośrednictwem mostka urządzenia

Aby połączyć urządzenia sieciowe Rzeczy z IoT Central:

* Dodaj nową integrację protokołu HTTP do aplikacji w witrynie The Things Network: **Application > Integrations > add integration > HTTP Integration**.
* Upewnij się, że aplikacja zawiera funkcję dekodera, która automatycznie konwertuje ładunek komunikatów urządzenia na dane JSON przed ich wysłaniem do funkcji platformy Azure: application **> Payload Functions > dekodera**.

W poniższym przykładzie przedstawiono funkcję dekodera JavaScript, która umożliwia dekodowanie typowych typów liczbowych z danych binarnych:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Po zdefiniowaniu integracji dodaj następujący kod przed wywołaniem funkcji w `handleMessage` wierszu 21 pliku *IoTCIntegration/index.js* aplikacji funkcji platformy Azure. Ten kod tłumaczy treść integracji HTTP na oczekiwany format.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Ograniczenia

Mostek urządzenia przekazuje komunikaty tylko do IoT Central i nie wysyła komunikatów z powrotem do urządzeń. Dlatego właściwości i polecenia nie działają w przypadku urządzeń łączących się z siecią IoT Central przez ten mostek urządzeń. Ponieważ operacje bliźniaczej reprezentacji urządzenia nie są obsługiwane, nie można zaktualizować właściwości urządzenia za pośrednictwem mostka urządzenia. Aby korzystać z tych funkcji, urządzenie musi łączyć się bezpośrednio IoT Central pomocą jednego z zestawów [SDK urządzeń Azure IoT.](../../iot-hub/iot-hub-devguide-sdks.md)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak wdrożyć mostek IoT Central, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzanie urządzeniami](howto-manage-devices.md)
