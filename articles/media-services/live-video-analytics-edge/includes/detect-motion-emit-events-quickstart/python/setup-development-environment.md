---
ms.openlocfilehash: be983a643b45847d2a44db018b1383aa56ab2302
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691194"
---
1. Klonuj repozytorium z tej lokalizacji: https://github.com/Azure-Samples/live-video-analytics-iot-edge-python .
1. W Visual Studio Code Otwórz folder, w którym repozytorium zostało pobrane.
1. W obszarze Visual Studio Code przejdź do folderu *src/Cloud-to-Device-App* . W tym miejscu Utwórz plik i nadaj mu nazwę *appsettings.js*. Ten plik będzie zawierać ustawienia, które są konieczne do uruchomienia programu.
1. Skopiuj zawartość z pliku *~/clouddrive/lva-sample/appsettings.js* , która została wygenerowana wcześniej w tym przewodniku Szybki Start.

    Tekst powinien wyglądać podobnie do poniższego wyniku.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Przejdź do folderu *src/Edge* i Utwórz plik o nazwie *. env*.
1. Skopiuj zawartość pliku */CloudDrive/LVA-Sample/Edge-Deployment/.env* . Tekst powinien wyglądać podobnie do poniższego kodu.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    