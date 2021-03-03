---
title: Samouczek — modyfikowanie Azure IoT Edge na żywo modułów analizy wideo
description: W tym samouczku przedstawiono sposób modyfikowania i kompilowania dynamicznych modułów bramy analitycznych filmów wideo, które są używane przez szablon aplikacji Analiza wideo — obiekt i ruch.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: cbff2e774fae1f71624aa3cdbd83bfb21e3a9586
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721467"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Samouczek: modyfikowanie i kompilowanie modułów bramy analizy wideo na żywo

W tym samouczku pokazano, jak zmodyfikować kod modułu IoT Edge dla modułów LVA (Live Video Analytics).

Poprzednie samouczki używają wstępnie skompilowanych obrazów modułów.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym samouczku potrzebne są:


> [!div class="checklist"]
> * [Node.js](https://nodejs.org/en/download/) v10 lub nowszy
> * [Visual Studio Code](https://code.visualstudio.com/Download) z zainstalowanym rozszerzeniem [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)
> * Aparat [platformy Docker](https://www.docker.com/products/docker-desktop)
> * [Azure Container Registry](../../container-registry/index.yml) do hostowania Twoich wersji modułów.
> * Konto [Azure Media Services](../../media-services/index.yml) . Jeśli zostały wykonane poprzednie samouczki, możesz ponownie użyć utworzonego wcześniej.

## <a name="clone-the-repository"></a>Klonowanie repozytorium

Jeśli repozytorium nie zostało jeszcze sklonowane, użyj następującego polecenia, aby sklonować je do odpowiedniej lokalizacji na komputerze lokalnym:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Otwórz lokalny folder repozytorium *wideo-Analytics* z vs Code.

## <a name="edit-the-deploymentamd64json-file"></a>Edytuj deployment.amd64.jspliku

1. Jeśli jeszcze tego nie zrobiono, Utwórz folder o nazwie *ref-Apps/LVA-Edge-IoT-Central-Gateway/Storage* w lokalnej kopii repozytorium **LVA-Gateway** . Ten folder jest ignorowany przez usługę git, aby zapobiec przypadkowemu sprawdzeniu poufnych informacji.
1. Skopiuj plik *deployment.amd64.js* z folderu *Setup* do folderu *Storage* .
1. W VS Code Otwórz plik *Storage/deployment.amd64.jsna komputerze* .
1. Edytuj `registryCredentials` sekcję, aby dodać poświadczenia Azure Container Registry.
1. Edytuj `LvaEdgeGatewayModule` sekcję modułu, aby dodać nazwę obrazu i nazwę konta AMS w `env:amsAccountName:value` .
1. Edytuj `lvaYolov3` sekcję modułu i Dodaj nazwę obrazu.
1. Edytuj `lvaEdge` sekcję modułu i Dodaj nazwę obrazu.
1. Aby uzyskać więcej informacji na temat kończenia konfiguracji, zobacz artykuł [Tworzenie aplikacji do analizy filmów wideo na platformie Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md) .

## <a name="build-the-code"></a>Kompilowanie kod

1. Przed podjęciem próby skompilowania kodu po raz pierwszy Użyj terminalu VS Code, aby uruchomić `npm install` polecenie. To polecenie powoduje zainstalowanie wymaganych pakietów i uruchomienie skryptów Instalatora.

    > [!TIP]
    > W przypadku ściągania nowej wersji repozytorium GitHub usługi Repository Usuń folder *node_modules* i uruchom `npm install` ponownie.

1. Edytuj *imageConfig.js./Setup/w* pliku, aby zaktualizować obraz o nazwie opartej na nazwie rejestru kontenerów:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Użyj terminalu VS Code, aby uruchomić `docker login [your server].azurecr.io` polecenie. Użyj tych samych poświadczeń, które zostały podane w manifeście wdrożenia dla modułów.

1. Użyj terminalu VS Code, aby uruchomić polecenie **patch npm wersja** . Ten skrypt kompilacji wdraża obrazy w rejestrze kontenerów. Dane wyjściowe w oknie terminalu VS Code pokazują, jeśli kompilacja zakończy się pomyślnie.

1. Wersja obrazu **LvaEdgeGatewayModule** zwiększa się za każdym razem, gdy kompilacja zakończy się. Musisz użyć tej wersji w pliku manifestu wdrożenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zakończysz pracę z aplikacją, możesz usunąć wszystkie utworzone zasoby w następujący sposób:

1. W aplikacji IoT Central przejdź do strony **aplikacji** w sekcji **Administracja** . Następnie wybierz pozycję **Usuń**.
1. W Azure Portal Usuń grupę zasobów **LVA-RG** .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już szablon aplikacji Analiza wideo — obiekty i wykrywanie ruchu oraz moduły LVA IoT Edge, sugerowany następny krok to Dowiedz się więcej na temat:

> [!div class="nextstepaction"]
> [Tworzenie rozwiązań dla handlu detalicznego za pomocą usługi Azure IoT Central](overview-iot-central-retail.md)