---
title: Samouczek — Tworzenie wystąpienia IoT Edge analizy wideo na platformie Azure IoT Central (maszyna wirtualna z systemem Linux)
description: W tym samouczku pokazano, jak utworzyć wystąpienie IoT Edge analizy wideo na maszynie wirtualnej z systemem Linux do użycia z szablonem aplikacji analiza filmów wideo — obiekty i ruch.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832627"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Samouczek: Tworzenie wystąpienia IoT Edge dla analiz wideo (maszyna wirtualna z systemem Linux)

Azure IoT Edge to w pełni zarządzana usługa, która umożliwia lokalne dostarczanie analiz chmurowych przez wdrożenie i uruchomienie:

* Logika niestandardowa
* Usługi platformy Azure
* Sztuczna inteligencja

W IoT Edge te usługi działają bezpośrednio na międzyplatformowych urządzeniach IoT, co pozwala bezpiecznie uruchamiać rozwiązanie IoT i w dużej skali w chmurze lub w trybie offline.

W tym samouczku pokazano, jak przygotować urządzenie IoT Edge na maszynie wirtualnej platformy Azure. W wystąpieniu IoT Edge są uruchamiane moduły analizy wideo na żywo, które są używane przez szablon aplikacji Azure IoT Central Video Analytics — obiekt i ruch.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej platformy Azure z zainstalowanym środowiskiem uruchomieniowym Azure IoT Edge
> * Przygotuj instalację IoT Edge, aby hostować moduł analizy wideo na żywo i połączyć się z IoT Central

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać poprzednią [aplikację Tworzenie aplikacji wideo Analytics na platformie azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) lub [utworzyć analizę wideo w samouczku usługi Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) .

Potrzebna jest również subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć bezpłatnie na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="deploy-azure-iot-edge"></a>Wdróż Azure IoT Edge

Aby utworzyć maszynę wirtualną platformy Azure przy użyciu najnowszych IoT Edge środowiska uruchomieniowego oraz zainstalowanych modułów analizy wideo na żywo, wybierz następujący przycisk:

[![Przycisk Wdrażanie na platformie Azure dla szablonu iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

Skorzystaj z informacji podanych w poniższej tabeli, aby ukończyć formularz **wdrożenia niestandardowego** :

| Pole | Wartość |
| ----- | ----- |
| Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
| Grupa zasobów | *LVA-RG* — Grupa zasobów utworzona w poprzednim samouczku. |
| Region (Region)       | *East US* |
| Prefiks etykiety DNS | Wybierz unikatowy prefiks DNS dla maszyny wirtualnej. Musi zawierać wszystkie znaki alfabetu, cyfry i znaki specjalne. |
| Nazwa użytkownika administratora | *AzureUser* |
| Hasło administratora | Wprowadź hasło. Zanotuj hasło w pliku *scratchpad.txt* , użyjesz go później. |
| Identyfikator zakresu | **Identyfikator zakresu** zanotowany w pliku *scratchpad.txt* w poprzednim samouczku po dodaniu urządzenia bramy. |
| Identyfikator urządzenia | *brama-001* — urządzenie bramy utworzone w poprzednim samouczku. |
| Klucz urządzenia | **Klucz podstawowy urządzenia** zanotowano w pliku *scratchpad.txt* w poprzednim samouczku po dodaniu urządzenia bramy. |
| Host aplikacji IoT Central | **Adres URL aplikacji** zanotowany w pliku *scratchpad.txt* w poprzednim samouczku. Na przykład *Traders.azureiotcentral.com*. |
| Token interfejsu API aplikacji IoT Central | **Token interfejsu API operatora** zanotowano w pliku *scratchpad.txt* w poprzednim samouczku. |
| Klucz aprowizacji urządzenia IoT Central | **Klucz podstawowy dla grupy SAS-IoT-Devices** został zanotowany w pliku *scratchpad.txt* w poprzednim samouczku. |
| Rozmiar maszyny wirtualnej | *Standard_DS1_v2* |
| Ubuntu wersja systemu operacyjnego | *18,04 – LTS* |
| Lokalizacja | *[resourceing (). Location]* |

Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Ukończenie wdrożenia zazwyczaj trwa około trzy minuty. Po zakończeniu wdrażania przejdź do grupy zasobów **LVA-RG** w Azure Portal.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Upewnij się, że środowisko uruchomieniowe IoT Edge ładuje moduły

W Azure Portal przejdź do grupy zasobów **LVA-RG** i wybierz maszynę wirtualną. Następnie w sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**.

Naciśnij klawisz **Enter** , aby uzyskać `login:` monit. Użyj *AzureUser* jako nazwy użytkownika i hasła, które zostały wybrane podczas tworzenia maszyny wirtualnej.

Uruchom następujące polecenie, aby sprawdzić wersję środowiska uruchomieniowego IoT Edge. W momencie pisania wersja jest 1.0.9:

```bash
sudo iotedge --version
```

Utwórz listę modułów IoT Edge za pomocą polecenia:

```bash
sudo iotedge list
```

Wdrożenie skonfigurował następujące pięć modułów IoT Edge do uruchomienia:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

Wdrożenie spowodowało utworzenie niestandardowego środowiska IoT Edge z modułami wymaganymi do analizy wideo na żywo. Wdrożenie zaktualizował domyślną **konfigurację. YAML** , aby upewnić się, że środowisko uruchomieniowe IoT Edge używało usługi IoT Device Provisioning do nawiązywania połączenia z IoT Central. Wdrożenie spowodowało również utworzenie pliku o nazwie **state.js** w folderze **/Data/Storage** w celu zapewnienia dodatkowych danych konfiguracyjnych dla modułów. Aby uzyskać więcej informacji, zobacz samouczek [Tworzenie wystąpienia IoT Edge dla usługi Video Analytics (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) .

Aby rozwiązać problem z urządzeniem IoT Edge, zobacz [Rozwiązywanie problemów z urządzeniem IoT Edge](../../iot-edge/troubleshoot.md)

## <a name="use-the-rtsp-simulator"></a>Korzystanie z symulatora RTSP

Jeśli nie masz prawdziwych urządzeń w aparacie do łączenia się z urządzeniem IoT Edge, możesz użyć dwóch symulowanych urządzeń z kamerą w szablonie aplikacji Analiza wideo. W tej sekcji przedstawiono sposób użycia symulowanego strumienia wideo na urządzeniu IoT Edge.

Te instrukcje używają [serwera multimediów Live555](http://www.live555.com/mediaServer/) jako symulatora RTSP w kontenerze platformy Docker.

> [!NOTE]
> Odwołania do oprogramowania innych firm w tym repozytorium służą wyłącznie do celów informacyjnych i wygodnych. Firma Microsoft nie zatwierdza ani nie udziela praw do oprogramowania innych firm. Aby uzyskać więcej informacji, zobacz [Live555 Media Server](http://www.live555.com/mediaServer/).

Użyj następującego polecenia, aby uruchomić narzędzie **rtspvideo** w kontenerze platformy Docker na maszynie wirtualnej IoT Edge. Kontener platformy Docker tworzy strumień w tle RTSP:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Użyj następującego polecenia, aby wyświetlić listę kontenerów platformy Docker:

```bash
sudo docker ps
```

Lista zawiera kontener o nazwie **live555**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zakończysz pracę z aplikacją, możesz usunąć wszystkie utworzone zasoby w następujący sposób:

1. W aplikacji IoT Central przejdź do strony **aplikacji** w sekcji **Administracja** . Następnie wybierz pozycję **Usuń**.
1. W Azure Portal Usuń grupę zasobów **LVA-RG** .
1. Na maszynie lokalnej Zatrzymaj kontener platformy Docker **przeglądarki amp** .

## <a name="next-steps"></a>Następne kroki

Wdrożono środowisko uruchomieniowe IoT Edge, moduły LVA oraz strumień symulacji Live555 na maszynie wirtualnej z systemem Linux działającej na platformie Azure.

Aby zarządzać kamerami, postępuj zgodnie z następnym samouczkiem

> [!div class="nextstepaction"]
> [Monitorowanie i zarządzanie aplikacją do wykrywania ruchu wideo i obiektów](./tutorial-video-analytics-manage.md)