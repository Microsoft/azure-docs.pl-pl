---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 8cae7ac2d3b961120cb9100f5072dc141769afff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559789"
---
## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym samouczku użyjesz rozszerzenia narzędzi Azure IoT Tools, aby skompilować moduł i utworzyć **obraz kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.

Do przechowywania obrazów kontenerów możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker to [Azure Container Registry](../articles/container-registry/index.yml) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry.

Jeśli nie masz jeszcze rejestru kontenerów, wykonaj następujące kroki, aby utworzyć nowy rejestr na platformie Azure:

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Registry**.

2. Podaj następujące wartości, aby utworzyć rejestr kontenerów:

   | Pole | Wartość |
   | ----- | ----- |
   | Subskrypcja | Wybierz subskrypcję z listy rozwijanej. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Nazwa rejestru | Podaj unikatową nazwę. |
   | Lokalizacja | Wybierz bliską lokalizację. |
   | SKU | Wybierz pozycję **Podstawowa**. |

3. Wybierz przycisk **Utwórz**.

4. Po utworzeniu rejestru kontenerów przejdź do niego, a następnie w okienku po lewej stronie wybierz pozycję **klucze dostępu** z menu znajdującego się w obszarze **Ustawienia**. 

5. Kliknij, aby włączyć administratora, aby wyświetlić **nazwę użytkownika** i **hasło** dla rejestru kontenerów.

6. Skopiuj wartości dla opcji **serwer logowania**, **Nazwa użytkownika** i **hasło** i Zapisz je w wygodnym miejscu. Te wartości są używane w tym samouczku, aby zapewnić dostęp do rejestru kontenerów.

   ![Kopiuj serwer logowania, nazwę użytkownika i hasło dla rejestru kontenerów](./media/iot-edge-create-container-registry/registry-access-key.png)