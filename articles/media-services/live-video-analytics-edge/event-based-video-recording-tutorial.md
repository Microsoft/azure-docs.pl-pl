---
title: Nagrywanie filmów wideo na podstawie zdarzeń do chmury i odtwarzanie z samouczka dotyczącego chmury — Azure
description: W tym samouczku dowiesz się, jak za pomocą usługi Azure Live Video Analytics na Azure IoT Edge zarejestrować rejestrowanie wideo oparte na zdarzeniach w chmurze i odtworzyć je z poziomu chmury.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a2388a01544d2158e7ca6f1692df07b14ec03a93
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773556"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Samouczek: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z chmury

W ramach tego samouczka nauczysz się, jak za pomocą usługi Azure Live Video Analytics na Azure IoT Edge selektywnie rejestrować fragmenty aktywnego źródła wideo do Azure Media Services w chmurze. Ten przypadek użycia jest określany mianem [rejestrowania wideo opartego na zdarzeniach](event-based-video-recording-concept.md) (EVR) w tym samouczku. Aby nagrać fragmenty wideo na żywo, będziesz używać modelu AI wykrywania obiektów do wyszukiwania obiektów w wideo i rejestrowania klipów wideo tylko w przypadku wykrycia określonego typu obiektu. Dowiesz się również, jak odtworzyć zarejestrowane klipy wideo przy użyciu Media Services. Ta funkcja jest przydatna w różnych scenariuszach, w których istnieje potrzeba zachowania archiwum interesujących klipów wideo. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj odpowiednie zasoby.
> * Przejrzyj kod, który wykonuje EVR.
> * Uruchom przykładowy kod.
> * Sprawdź wyniki i Obejrzyj wideo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

Przeczytaj te artykuły przed rozpoczęciem:

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md) 
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Samouczek: opracowywanie modułu IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Jak edytować wdrożenie. * .template.jsna](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Sekcja [Zadeklaruj trasy w IoT Edge manifeście wdrożenia](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące tego samouczka:

* [Visual Studio Code](https://code.visualstudio.com/) na swoim komputerze deweloperskim przy użyciu narzędzi i rozszerzeń [języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) .

    > [!TIP]
    > Może zostać wyświetlony monit o zainstalowanie platformy Docker. Zignoruj ten monit.
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na komputerze deweloperskim.
* Ukończ [skrypt konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)i [Skonfiguruj środowisko](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Po zakończeniu tych kroków będziesz mieć odpowiednie zasoby platformy Azure wdrożone w ramach subskrypcji platformy Azure:

* Azure IoT Hub
* Konto usługi Azure Storage
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure, w której zainstalowano [środowisko uruchomieniowe IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md)

## <a name="concepts"></a>Pojęcia

Nagrywanie wideo oparte na zdarzeniach dotyczy procesu nagrywania wideo wyzwalanego przez zdarzenie. To zdarzenie może zostać wygenerowane z:
- Przetwarzanie samego sygnału wideo, na przykład podczas wykrywania przenoszonego obiektu w filmie wideo.
- Niezależne źródło, na przykład otwarcie drzwi. 

Alternatywnie można wyzwolić nagrywanie tylko wtedy, gdy usługa inferencing wykryje, że wystąpiło określone zdarzenie. W tym samouczku użyjesz wideo o pojazdach poruszających się na autostradach i nagrywaj klipy wideo po wykryciu wózka.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty" są następnie kierowane do węzła źródła IoT Hub wykresu multimediów. Po odebraniu takiego komunikatu węzeł IoT Hub Source na grafie multimediów wyzwala węzeł [procesora bramy sygnałów](media-graph-concept.md#signal-gate-processor) . Następnie zostanie otwarty węzeł procesora bramy sygnałów przez skonfigurowany czas. Przepływy wideo przez bramę do węzła ujścia zasobów dla tego czasu trwania. Ta część strumienia na żywo jest następnie rejestrowana za pośrednictwem węzła [ujścia zasobów](media-graph-concept.md#asset-sink) do [zasobu](terminology.md#asset) na koncie Azure Media Services.

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Przed rozpoczęciem upewnij się, że zostały spełnione trzeci punktor w sekcji [wymagania wstępne](#prerequisites). Po zakończeniu działania skryptu konfiguracji zasobów wybierz nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików utworzonych w katalogu ~/CloudDrive/LVA-Sample.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    Parametry połączenia IoT Hub umożliwiają używanie Visual Studio Code do wysyłania poleceń do modułów brzegowych za pośrednictwem usługi Azure IoT Hub.
    
1. Następnie przejdź do folderu src/Edge i Utwórz plik o nazwie **. env**.
1. Skopiuj zawartość z pliku ~/CloudDrive/LVA-Sample/.env. Tekst powinien wyglądać następująco:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Sprawdzanie pliku szablonu 

W poprzednim kroku uruchomiono Visual Studio Code i otwarto folder, który zawiera przykładowy kod.

W Visual Studio Code przejdź do węzła src/Edge. Zobaczysz utworzony plik ENV i kilka plików szablonów wdrożenia. Ten szablon definiuje, które moduły krawędzi zostaną wdrożone na urządzeniu brzegowym (maszyna wirtualna z systemem Linux Azure). Plik ENV zawiera wartości dla zmiennych używanych w tych szablonach, takich jak poświadczenia Media Services.

Otwórz w programie src/Edge/deployment.objectCounter.template.js. W sekcji **modułów** znajdują się cztery wpisy, które odpowiadają elementom wymienionym w poprzedniej sekcji "koncepcje":

* **lvaEdge**: to jest analiza filmów wideo na żywo w IoT Edge module.
* **yolov3**: jest to moduł AI zbudowany przy użyciu modelu Yolo v3.
* **rtspsim**: to jest symulator RTSP.
* **objectCounter**: to jest moduł, który szuka określonych obiektów w wynikach z yolov3.

W przypadku modułu objectCounter Zobacz ciąg ($ {MODULEs. objectCounter}) użyty dla wartości "Image". Jest to oparte na [samouczku](../../iot-edge/tutorial-develop-for-linux.md) dotyczącym tworzenia modułu IoT Edge. Visual Studio Code automatycznie rozpoznaje, że kod modułu objectCounter jest w obszarze src/Edge/modules/objectCounter. 

Zapoznaj się z [tą sekcją](../../iot-edge/module-composition.md#declare-routes) jak deklarować trasy w manifeście wdrażania IoT Edge. Następnie zapoznaj się z trasami w pliku JSON szablonu. Zwróć uwagę na to, jak:

* LVAToObjectCounter służy do wysyłania określonych zdarzeń do określonego punktu końcowego w module objectCounter.
* ObjectCounterToLVA jest używany do wysyłania zdarzenia wyzwalacza do określonego punktu końcowego (który powinien być IoT Hub węzeł źródłowy) w module lvaEdge.
* objectCounterToIoTHub jest używany jako narzędzie do debugowania, które ułatwia wyświetlanie danych wyjściowych z objectCounter podczas uruchamiania tego samouczka.

> [!NOTE]
> Sprawdź żądane właściwości modułu objectCounter, które są skonfigurowane do wyszukiwania obiektów oznaczonych jako "Ciężarówka" o poziomie ufności równym co najmniej 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge 

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym, oraz ustawienia konfiguracji dla tych modułów. Wykonaj następujące kroki, aby wygenerować manifest z pliku szablonu, a następnie wdróż go na urządzeniu brzegowym.

Korzystając z Visual Studio Code, postępuj zgodnie z [tymi instrukcjami](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) , aby zalogować się do platformy Docker. Następnie wybierz opcję **Kompiluj i wypchnij IoT Edge rozwiązanie**. Dla tego kroku Użyj elementu src/Edge/deployment.objectCounter.template.js.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty":::

Ta akcja powoduje utworzenie modułu objectCounter do zliczania obiektów i wypchnięcie obrazu do Azure Container Registry.

* Sprawdź, czy istnieją zmienne środowiskowe CONTAINER_REGISTRY_USERNAME_myacr i CONTAINER_REGISTRY_PASSWORD_myacr zdefiniowane w pliku ENV.

Ten krok powoduje utworzenie manifestu wdrażania IoT Edge przy użyciu src/Edge/config/deployment.objectCounter.amd64.jsw systemie. Kliknij prawym przyciskiem myszy ten plik, a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty":::

Jeśli jest to pierwszy samouczek z funkcją analizy filmów wideo na żywo na IoT Edge, Visual Studio Code monit o wprowadzenie IoT Hub parametrów połączenia. Możesz skopiować go z appsettings.jspliku.

Następnie Visual Studio Code prosi o wybranie urządzenia IoT Hubowego. Wybierz urządzenie IoT Edge, które powinno być LVA-Sample-Device.

Na tym etapie wdrożenie modułów brzegowych na urządzeniu IoT Edge zostało rozpoczęte.
W ciągu około 30 sekund Odśwież IoT Hub platformy Azure w lewej dolnej części w Visual Studio Code. Należy sprawdzić, czy zostały wdrożone cztery moduły o nazwie lvaEdge, rtspsim, yolov3 i objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty":::

## <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Aby wyświetlić zdarzenia z modułu objectCounter i z poziomu modułu analizy filmów wideo na żywo w IoT Edge, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Wyszukaj pozycję **Azure IoT Hub** w lewym dolnym rogu.
1. Rozwiń węzeł **urządzenia** .
1. Kliknij prawym przyciskiem myszy plik LVA-Sample-Device i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty":::
    
## <a name="run-the-program"></a>Uruchamianie programu

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty"
              }
            ]
          }
        }
        ```
    
   * Wywołanie GraphInstanceActivate w celu uruchomienia wystąpienia grafu i uruchomienia przepływu wideo
   * Drugie wywołanie GraphInstanceList, aby pokazać, że wystąpienie grafu jest w stanie uruchomienia
     
1. Dane wyjściowe w oknie **terminalu** są wstrzymywane teraz po **naciśnięciu klawisza ENTER w celu kontynuowania** monitu. W tej chwili nie zaznaczaj **klawisza ENTER** . Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Jeśli teraz przełączysz się do okna **danych wyjściowych** w Visual Studio Code, zobaczysz komunikaty wysyłane do IoT Hub przez analizę wideo na żywo w module IoT Edge.

   Te komunikaty zostały omówione w poniższej sekcji.
1. Wystąpienie grafu będzie nadal działać i nagrać wideo. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Przejrzyj komunikaty zgodnie z opisem w następnej sekcji. Następnie, aby zatrzymać wystąpienie, Wróć do okna **terminalu** i wybierz **klawisz ENTER**. Kolejna seria wywołań jest wykonywana w celu oczyszczenia zasobów przy użyciu:

   * Wywołanie GraphInstanceDeactivate, aby dezaktywować wystąpienie grafu.
   * Wywołanie GraphInstanceDelete do usunięcia wystąpienia.
   * Wywołanie GraphTopologyDelete do usunięcia topologii.
   * Końcowe wywołanie GraphTopologyList, aby pokazać, że lista jest teraz pusta.

## <a name="interpret-the-results"></a>Interpretacja wyników 

Po uruchomieniu grafu multimediów na żywo analiza filmów wideo w IoT Edge module wysyła do centrum IoT Edge pewne zdarzenia diagnostyczne i operacyjne. Zdarzenia te są wyświetlane w oknie **dane wyjściowe** Visual Studio Code. Zawierają one sekcję treści i sekcję applicationProperties. Aby zrozumieć, co reprezentuje Ta sekcja, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach właściwości aplikacji i zawartość treści są zdefiniowane przez moduł analizy wideo na żywo.

## <a name="diagnostics-events"></a>Zdarzenia diagnostyki

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished 

Po utworzeniu wystąpienia grafu multimedialnego węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP uruchomionym w kontenerze symulatora RTSP. Jeśli to się powiedzie, drukuje to zdarzenie. Typem zdarzenia jest Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Komunikat jest zdarzeniem diagnostycznym (MediaSessionEstablished). Wskazuje, że węzeł źródłowy RTSP (podmiot) ustanowił połączenie z symulatorem RTSP i zaczął odbierać (symulowane) kanały informacyjne na żywo.
* Sekcja subject w applicationProperties odwołuje się do węzła w topologii grafu, z którego został wygenerowany komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* Sekcja eventType w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* Sekcja eventTime wskazuje czas wystąpienia zdarzenia. Jest to czas, w którym ruch wideo (plik MKV) został uruchomiony do modułu jako strumień na żywo.
* Sekcja treść zawiera dane dotyczące zdarzenia diagnostycznego, w tym przypadku szczegóły dotyczące [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="operational-events"></a>Zdarzenia operacyjne

Gdy wykres multimedialny zostanie uruchomiony przez pewien czas, ostatecznie uzyskasz zdarzenie z modułu objectCounter. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

Sekcja applicationProperties zawiera czas zdarzenia. Jest to czas, gdy moduł objectCounter zaobserwowano, że wyniki z modułu yolov3 zawierają obiekty zainteresowania (ciężarówki).

Więcej z tych zdarzeń można zobaczyć w przypadku wykrycia innych wagonów w filmie wideo.

### <a name="recordingstarted-event"></a>Zdarzenie RecordingStarted

Niemal natychmiast po wysłaniu zdarzenia przez licznik obiektów zobaczysz zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStarted:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

Sekcja subject w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował tę wiadomość. Sekcja treść zawiera informacje o lokalizacji wyjściowej. W tym przypadku jest to nazwa elementu Azure Media Services, do którego jest nagrywane wideo. Zanotuj tę wartość.

### <a name="recordingavailable-event"></a>Zdarzenie RecordingAvailable

Gdy węzeł ujścia zasobów przekazał wideo do elementu zawartości, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingAvailable:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie oznacza, że na potrzeby odtwarzaczy lub klientów zapisało wystarczającą ilość danych, aby rozpocząć odtwarzanie filmu wideo. Sekcja subject w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość. Sekcja treść zawiera informacje o lokalizacji wyjściowej. W tym przypadku jest to nazwa elementu Azure Media Services, do którego jest nagrywane wideo.

### <a name="recordingstopped-event"></a>Zdarzenie RecordingStopped

Jeśli sprawdzisz ustawienia aktywacji (maximumActivationTime) dla węzła procesora usługi Signal Gate w [topologii](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), zobaczysz, że brama jest skonfigurowana do zamykania po przejściu przez 30 sekund filmu wideo przez program. Około 30 sekund po zdarzeniu RecordingStarted powinno zostać wyświetlone zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStopped. To zdarzenie oznacza, że węzeł ujścia zasobów zatrzymał nagrywanie wideo dla elementu zawartości.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie wskazuje, że nagrywanie zostało zatrzymane. Sekcja subject w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość. Sekcja treść zawiera informacje o lokalizacji wyjściowej. W tym przypadku jest to nazwa elementu Azure Media Services, do którego jest nagrywane wideo.

## <a name="media-services-asset"></a>Media Services zasób  

Możesz przeanalizować Media Services element zawartości, który został utworzony przez Graf, logując się do Azure Portal i wyświetlając wideo.

1. Otwórz przeglądarkę internetową i przejdź do [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.
1. Znajdź konto Media Services wśród zasobów znajdujących się w Twojej subskrypcji. Otwórz okienko konto.
1. Wybierz pozycję **elementy zawartości** z listy **Media Services** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty":::
1. Znajdziesz element zawartości o nazwie sampleAssetFromEVR-LVAEdge-{DateTime}. Jest to nazwa podana we właściwości outputLocation zdarzenia RecordingStarted. AssetNamePattern w topologii określa, jak ta nazwa została wygenerowana.
1. Wybierz element zawartości. 
1. Na stronie Szczegóły zasobu wybierz pozycję **Utwórz nowy** w polu tekstowym **adres URL przesyłania strumieniowego** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Graf multimedialny&quot;:::

Diagram jest obrazkową reprezentacją [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Zaliczane są cztery moduły IoT Edge:

* Analiza filmów wideo na żywo w module IoT Edge.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI używa modelu [Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykrywać wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów, które są określane jako licznik obiektów na diagramie. Utworzysz licznik obiektów i wdróżesz go w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia symulowanego wideo na żywo ruchu na autostradach i wysłania tego wideo do dwóch ścieżek:

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP. Następnie węzeł przekazuje ramki, jako obrazy, do modułu AI YOLO v3, który jest detektorem obiektów. Węzeł otrzymuje wyniki, czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł objectCounter jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Moduł sprawdza te komunikaty i szuka obiektów określonego typu, które zostały skonfigurowane za pomocą ustawienia. Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty &quot;znalezione obiekty":::
1. W otwartym Kreatorze Zaakceptuj opcje domyślne i wybierz pozycję **Dodaj**. Aby uzyskać więcej informacji, zobacz [odtwarzanie wideo](video-playback-concept.md).

    > [!TIP]
    > Upewnij się, że [punkt końcowy przesyłania strumieniowego jest uruchomiony](../latest/streaming-endpoint-concept.md).
1. Gracz powinien załadować wideo. Wybierz opcję **Odtwórz** , aby ją wyświetlić.

> [!NOTE]
> Ponieważ źródłem wideo był kontener symulowania kanału informacyjnego aparatu, sygnatury czasowe w filmie wideo są powiązane z momentem aktywowania wystąpienia grafu i po jego zdezaktywowaniu. Jeśli używasz kontrolek odtwarzania wbudowanych dla [odtwarzania wielodniowego](playback-multi-day-recordings-tutorial.md) samouczka, zobaczysz sygnatury czasowe w wideo wyświetlonym na ekranie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz wypróbować inne samouczki, zaczekaj na utworzone zasoby. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

* Użyj [aparatu IP](https://en.wikipedia.org/wiki/IP_camera) z obsługą protokołu RTSP, zamiast korzystać z symulatora RTSP. W celu wyszukania kamer IP z obsługą protokołu RTSP na [stronie ONVIF zgodne produkty](https://www.onvif.org/conformant-products/) można wyszukać urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 (a nie z maszyną wirtualną systemu Linux). To urządzenie musi znajdować się w tej samej sieci co kamera IP. Postępuj zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Następnie postępuj zgodnie z instrukcjami podanymi w obszarze [Wdróż pierwszy IoT Edge module w ramach](../../iot-edge/quickstart-linux.md) szybkiego startu urządzenia z systemem Linux, aby zarejestrować urządzenie w usłudze Azure IoT Hub.
