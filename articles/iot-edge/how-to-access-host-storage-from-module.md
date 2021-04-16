---
title: Używanie IoT Edge magazynu lokalnego urządzenia z modułu — Azure IoT Edge | Microsoft Docs
description: Użyj zmiennych środowiskowych i utwórz opcje, aby umożliwić dostęp modułu do IoT Edge magazynu lokalnego urządzenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 78752d4da42fe07461ae0e82b10343dc7219ad91
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482062"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Zapewnianie modułom dostępu do magazynu lokalnego na urządzeniu

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Oprócz przechowywania danych przy użyciu usług Azure Storage lub magazynu kontenerów urządzenia można również przeznaczyć magazyn na samym urządzeniu hosta IoT Edge w celu zwiększenia niezawodności, szczególnie w przypadku pracy w trybie offline.

## <a name="link-module-storage-to-device-storage"></a>Łączenie magazynu modułów z magazynem urządzenia

Aby włączyć połączenie z magazynu modułów do magazynu w systemie hosta, utwórz dla modułu zmienną środowiskową, która wskazuje folder magazynu w kontenerze. Następnie użyj opcji tworzenia, aby powiązać ten folder magazynu z folderem na maszynie hosta.

Jeśli na przykład chcesz włączyć centrum usługi IoT Edge przechowywanie komunikatów w magazynie lokalnym urządzenia i pobieranie ich później, możesz skonfigurować zmienne środowiskowe i opcje tworzenia w chmurze Azure Portal sekcji Ustawienia środowiska **uruchomieniowego.**

1. W przypadku IoT Edge i agenta IoT Edge dodaj zmienną środowiskową o nazwie **storageFolder,** która wskazuje katalog w module.
1. W przypadku IoT Edge i agenta IoT Edge dodaj powiązania w celu połączenia katalogu lokalnego na maszynie hosta z katalogiem w module. Na przykład:

   ![Dodawanie opcji tworzenia i zmiennych środowiskowych dla magazynu lokalnego](./media/how-to-access-host-storage-from-module/offline-storage.png)

Magazyn lokalny można również skonfigurować bezpośrednio w manifeście wdrożenia. Na przykład:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Zastąp `<HostStoragePath>` wartości i `<ModuleStoragePath>` ścieżką magazynu hosta i modułu. Obie wartości muszą być ścieżką bezwzględną.

Na przykład w systemie Linux oznacza, że katalog `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` **/etc/iotedge/storage** w systemie hosta jest mapowany na katalog **/iotedge/storage/** w kontenerze. W systemie Windows jako inny przykład oznacza, że katalog C: temp w systemie hosta jest mapowany na `"Binds":["C:\\temp:C:\\contemp"]` katalog **C: \\ contemp** w kontenerze. **\\**

Ponadto na urządzeniach z systemem Linux upewnij się, że profil użytkownika modułu ma wymagane uprawnienia do odczytu, zapisu i wykonywania w katalogu systemu hosta. Wracając do wcześniejszego przykładu włączania centrum IoT Edge do przechowywania komunikatów w magazynie lokalnym urządzenia, musisz udzielić uprawnień do jego profilu użytkownika UID 1000. (Agent IoT Edge działa jako główny, więc nie potrzebuje dodatkowych uprawnień). Istnieje kilka sposobów zarządzania uprawnieniami katalogu w systemach Linux, w tym użycie metody do zmiany właściciela katalogu, a następnie zmiany `chown` `chmod` uprawnień, takich jak:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Więcej szczegółowych informacji na temat opcji tworzenia można znaleźć w [dokumentów platformy Docker.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="encrypted-data-in-module-storage"></a>Zaszyfrowane dane w magazynie modułów

Gdy moduły wywołują IoT Edge API obciążenia demona w celu szyfrowania danych, klucz szyfrowania jest uzyskiwany przy użyciu identyfikatora modułu i identyfikatora generacji modułu. Identyfikator generacji jest używany do ochrony wpisów tajnych, jeśli moduł zostanie usunięty z wdrożenia, a następnie inny moduł o tym samym identyfikatorze modułu zostanie później wdrożony na tym samym urządzeniu. Identyfikator generacji modułu można wyświetlić za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az iot hub module-identity show](/cli/azure/iot/hub/module-identity).

Jeśli chcesz udostępniać pliki między modułami między generacjami, nie mogą one zawierać żadnych wpisów tajnych lub nie zostaną odszyfrowane.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowy przykład uzyskiwania dostępu do magazynu hostów z modułu, zobacz [Store data at the edge with Azure Blob Storage on IoT Edge](how-to-store-data-blob.md)(Przechowywanie danych na brzegu sieci za pomocą Azure Blob Storage na IoT Edge ).
