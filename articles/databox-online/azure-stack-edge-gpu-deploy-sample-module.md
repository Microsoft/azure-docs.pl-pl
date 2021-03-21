---
title: Wdrażanie modułu GPU na urządzeniu z systemem Azure Stack Edge z procesorem GPU Pro | Microsoft Docs
description: W tym artykule opisano sposób włączania obliczeń i wprowadzania danych do Azure Stack brzegowych urządzeń z systemem Pro za pośrednictwem lokalnego interfejsu użytkownika.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4142542c9f17ae464a996df310c50d73d8711d8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438203"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Wdrażanie modułu IoT z włączonym procesorem GPU na urządzeniu z systemem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

W tym artykule opisano sposób wdrażania modułu IoT Edge z włączonym procesorem GPU na urządzeniu z systemem Azure Stack Edge. 

W tym artykule omówiono sposób wykonywania następujących zadań:
  - Przygotuj Azure Stack EDGE Pro, aby uruchomić moduł GPU.
  - Pobierz i zainstaluj przykładowy kod z repozytorium git.
  - Skompiluj rozwiązanie i Wygeneruj manifest wdrożenia.
  - Wdróż rozwiązanie na urządzeniu Azure Stack brzeg Pro.
  - Monitoruj dane wyjściowe modułu.


## <a name="about-sample-module"></a>Informacje o przykładowym module

Moduł przykładowego procesora GPU w tym artykule zawiera przykładowy kod testu porównawczego PyTorch i TensorFlow dla procesora CPU w porównaniu z procesorem GPU.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Masz dostęp do procesora GPU z włączonym 1 węzłem Azure Stack Edge na urządzeniu. To urządzenie jest aktywowane przy użyciu zasobu na platformie Azure. Zobacz [Aktywowanie urządzenia](azure-stack-edge-gpu-deploy-activate.md).
- Na tym urządzeniu skonfigurowano obliczenia. Wykonaj kroki opisane w [samouczku: Konfigurowanie obliczeń na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-configure-compute.md).
- Azure Container Registry (ACR). Przejdź do bloku **klucze dostępu** i zanotuj ACR logowania, nazwę użytkownika i hasło. Aby uzyskać więcej informacji, przejdź do [przewodnika Szybki Start: Tworzenie prywatnego rejestru kontenerów przy użyciu Azure Portal](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- Następujące zasoby programistyczne na kliencie systemu Windows:
    - [Interfejs wiersza polecenia platformy Azure 2,0 lub nowszej](https://aka.ms/installazurecliwindows)
    - [Program Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Może być konieczne utworzenie konta w celu pobrania i zainstalowania oprogramowania.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)    
    - [Rozszerzenie języka Python dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - PIP, aby zainstalować pakiety języka Python (zwykle dołączone do instalacji języka Python)

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

1. Przejdź do [wzorca inteligentnej krawędzi platformy Azure w usłudze Azure Samples](https://github.com/azure-samples/azure-intelligent-edge-patterns). Klonuj lub Pobierz plik ZIP dla kodu. 

    ![Pobierz plik zip](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Wyodrębnij pliki z pliku zip. Możesz również klonować przykłady.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Kompilowanie i wdrażanie modułu

1. Otwórz folder **GpuReferenceModules** w Visual Studio Code.

    ![Otwórz GPUReferenceModules w VS Code](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Otwórz *deployment.template.js* i zidentyfikuj parametry, do których odwołuje się rejestr kontenerów. W poniższym pliku są używane CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD i CONTAINER_REGISTRY_NAME.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Utwórz nowy plik. Podaj wartości parametrów rejestru kontenerów (Użyj tych określonych w poprzednim kroku) w następujący sposób: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Poniżej przedstawiono przykładowy plik *ENV* :
    
    ![Utwórz i Zapisz plik ENV](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Zapisz plik w formacie *ENV* w folderze **SampleSolution** .

5. Aby zalogować się do platformy Docker, wprowadź następujące polecenie w Visual Studio Code zintegrowanym terminalu. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Przejdź do sekcji **klucze dostępu** w rejestrze kontenerów w Azure Portal. Skopiuj i użyj nazwy rejestru, hasła i serwera logowania.

    ![Klucze dostępu w rejestrze kontenerów](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    Po dostarczeniu poświadczeń logowanie kończy się powodzeniem.

    ![Pomyślne logowanie](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Wypchnij obraz do usługi Azure Container Registry. W Eksploratorze VS Code zaznacz i kliknij prawym przyciskiem myszy **deployment.template.jsna** pliku, a następnie wybierz **rozwiązanie Kompiluj i wypchnij IoT Edge**. 

    ![Rozwiązanie do kompilowania i wypychania IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Jeśli nie zainstalowano rozszerzenia Python i Python, zostaną one zainstalowane podczas kompilowania i wypychania rozwiązania. Spowoduje to jednak wydłużenie czasu kompilacji. 

    Po zakończeniu tego kroku zobaczysz moduł w rejestrze kontenerów.

    ![Moduł w rejestrze kontenerów](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Aby utworzyć manifest wdrożenia, kliknij prawym przyciskiem myszy **deployment.template.jsna** , a następnie wybierz polecenie **Generuj IoT Edge manifest wdrożenia**. 

    ![Generowanie manifestu wdrażania IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    Powiadomienie informuje o ścieżce, w której został wygenerowany manifest wdrożenia. Manifest to `deployment.amd64.json` plik wygenerowany w folderze **konfiguracyjnym** . 

8. Wybierz **deployment.amd64.jsw** pliku w folderze **config** , a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**. Nie należy używać **deployment.template.jsw** pliku. 

    ![Tworzenie wdrożenia dla pojedynczego urządzenia](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    W oknie **dane wyjściowe** powinien zostać wyświetlony komunikat o pomyślnym wdrożeniu.

    ![Wdrożenie zakończyło się pomyślnie](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>Monitorowanie modułu  

1. W palecie poleceń programu VS Code wybierz i uruchom polecenie **Azure IoT Hub: wybierz centrum IoT Hub**.

2. Wybierz subskrypcję i centrum IoT Hub zawierające urządzenie usługi IoT Edge, które chcesz skonfigurować. W takim przypadku wybierz subskrypcję używaną do wdrożenia urządzenia z usługą Azure Stack Edge, a następnie wybierz urządzenie IoT Edge utworzone dla urządzenia z Azure Stack Edge. Dzieje się tak w przypadku konfigurowania obliczeń za pośrednictwem Azure Portal w poprzednich krokach.

3. W Eksploratorze VS Code rozwiń sekcję IoT Hub platformy Azure. W obszarze **urządzenia** powinna zostać wyświetlona IoT Edge urządzenie odpowiadające urządzeniu Azure Stack EDGE Pro. 

    1. Wybierz to urządzenie, kliknij prawym przyciskiem myszy i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.
  
        ![Rozpocznij monitorowanie](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Przejdź do pozycji **urządzenia > moduły** i sprawdź, czy **moduł GPU** jest uruchomiony.

        ![Moduł w IoT Hub](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. Terminal VS Code powinien także pokazać zdarzenia IoT Hub jako dane wyjściowe monitorowania dla urządzenia z Azure Stack Edge.

        ![Dane wyjściowe monitorowania](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Można zobaczyć, że czas potrzebny do wykonania tego samego zestawu operacji (5000 iteracji kształtu) przez procesor GPU jest mniejszy niż w przypadku procesora CPU.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o sposobie [konfigurowania procesora GPU do korzystania z modułu](azure-stack-edge-j-series-configure-gpu-modules.md).
