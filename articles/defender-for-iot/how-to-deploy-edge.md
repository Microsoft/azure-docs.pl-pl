---
title: Wdróż moduł zabezpieczeń IoT Edge
description: Dowiedz się więcej o sposobie wdrażania agenta zabezpieczeń usługi Defender for IoT na IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: cd79c928afdb3563b47374869cff577f9221d360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705776"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Wdrażanie modułu zabezpieczeń na urządzeniu IoT Edge

Moduł **Defender for IoT** oferuje kompleksowe rozwiązanie zabezpieczeń dla urządzeń IoT Edge.
Moduł zabezpieczeń zbiera, agreguje i analizuje pierwotne dane zabezpieczeń z systemu operacyjnego i systemu kontenerów w celu podejmowania działań i alertów dotyczących zabezpieczeń.
Aby dowiedzieć się więcej, zobacz [moduł zabezpieczeń dla IoT Edge](security-edge-architecture.md).

W tym artykule dowiesz się, jak wdrożyć moduł zabezpieczeń na urządzeniu IoT Edge.

## <a name="deploy-security-module"></a>Wdróż moduł zabezpieczeń

Wykonaj następujące kroki, aby wdrożyć moduł Defender Security for IoT dla IoT Edge.

### <a name="prerequisites"></a>Wymagania wstępne

1. W IoT Hub upewnij się, że urządzenie jest [zarejestrowane jako urządzenie IoT Edge](../iot-edge/how-to-register-device.md#register-a-new-device).

1. Usługa Defender dla IoT Edge Module wymaga, aby [platforma z inspekcją](https://linux.die.net/man/8/auditd) została zainstalowana na urządzeniu IoT Edge.

    - Zainstaluj platformę, uruchamiając następujące polecenie na urządzeniu IoT Edge:

    `sudo apt-get install auditd audispd-plugins`

    - Sprawdź, czy inspekcja jest aktywna, uruchamiając następujące polecenie:

    `sudo systemctl status auditd`<br>
    - Oczekiwana odpowiedź: `active (running)`

### <a name="deployment-using-azure-portal"></a>Wdrażanie przy użyciu Azure Portal

1. W Azure Portal Otwórz **witrynę Marketplace**.

1. Wybierz pozycję **Internet rzeczy**, a następnie wyszukaj pozycję **Azure Security Center for IoT** i wybierz ją.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Wybieranie usługi Defender dla IoT":::

1. Wybierz pozycję **Utwórz** , aby skonfigurować wdrożenie.

1. Wybierz **subskrypcję** platformy Azure IoT Hub, a następnie wybierz **IoT Hub**.<br>Wybierz pozycję **Wdróż na urządzeniu** przeznaczonym dla jednego urządzenia lub wybierz pozycję **Wdróż w odpowiedniej skali** , aby wybrać miejsce docelowe wielu urządzeń, a następnie wybierz pozycję **Utwórz**. Aby uzyskać więcej informacji na temat wdrażania w skali, zobacz artykuł [jak wdrożyć](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >W przypadku wybrania **wdrożenia w odpowiedniej skali** należy dodać nazwę i szczegóły urządzenia przed przejściem do karty **Dodawanie modułów** w poniższych instrukcjach.

Wykonaj każdy krok, aby ukończyć wdrażanie IoT Edge dla usługi Defender for IoT.

#### <a name="step-1-modules"></a>Krok 1: moduły

1. Wybierz moduł **AzureSecurityCenterforIoT** .
1. Na karcie **Ustawienia modułu** Zmień **nazwę** na **azureiotsecurity**.
1. Na karcie **zmienne środowiskowe** Dodaj zmienną w razie potrzeby (na przykład możesz dodać *poziom debugowania* i ustawić ją na jedną z następujących wartości: "krytyczne", "błąd", "ostrzeżenie" lub "informacje").
1. Na karcie **Opcje tworzenia kontenera** Dodaj następującą konfigurację:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Na karcie **Ustawienia sznurka modułu** Dodaj następującą konfigurację:

   Właściwość sznurka modułu:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Zawartość właściwości sznurka modułu: 

   ```json
     {

     }
   ```
    
   Więcej informacji o konfigurowaniu agenta znajduje się w temacie [Konfigurowanie agentów zabezpieczeń](./how-to-agent-configuration.md).

1. Wybierz pozycję **Aktualizuj**.

#### <a name="step-2-runtime-settings"></a>Krok 2. Ustawienia środowiska uruchomieniowego

1. Wybierz pozycję **Ustawienia środowiska uruchomieniowego**.
2. W obszarze **centrum brzegowe** Zmień **obraz** na **MCR.Microsoft.com/azureiotedge-Hub:1.0.8.3**.

    >[!Note]
    > Obecnie jest obsługiwana wersja 1.0.8.3 lub starsza.

3. Sprawdź, czy **opcja Utwórz** jest ustawiona na następującą konfigurację:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. Wybierz pozycję **Zapisz**.

5. Wybierz opcję **Dalej**.

#### <a name="step-3-specify-routes"></a>Krok 3. Określanie tras

1. Na karcie **Określanie tras** upewnij się, że masz trasę (jawną lub niejawną), która przekaże komunikaty z modułu **azureiotsecurity** do **$upstream** zgodnie z poniższymi przykładami. Tylko wtedy, gdy trasa jest na miejscu, wybierz pozycję **dalej**.

   Przykładowe trasy:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Wybierz opcję **Dalej**.

#### <a name="step-4-review-deployment"></a>Krok 4. przegląd wdrożenia

- Na karcie **Przegląd wdrożenia** Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz** , aby zakończyć wdrażanie.

## <a name="diagnostic-steps"></a>Kroki diagnostyczne

Jeśli wystąpi problem, dzienniki kontenerów są najlepszym sposobem poznania stanu urządzenia modułu zabezpieczeń IoT Edge. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Sprawdź, czy wymagane kontenery są zainstalowane i działają zgodnie z oczekiwaniami

1. Uruchom następujące polecenie na urządzeniu IoT Edge:

    `sudo docker ps`

1. Sprawdź, czy są uruchomione następujące kontenery:

   | Nazwa | OBRAZ |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Jeśli nie ma minimalnych wymaganych kontenerów, sprawdź, czy IoT Edge manifest wdrożenia jest wyrównany z zalecanymi ustawieniami. Aby uzyskać więcej informacji, zobacz [wdrażanie modułu IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspekcja dzienników modułów pod kątem błędów

1. Uruchom następujące polecenie na urządzeniu IoT Edge:

   `sudo docker logs azureiotsecurity`

1. Aby uzyskać pełne dzienniki, Dodaj następującą zmienną środowiskową do wdrożenia modułu **azureiotsecurity** : `logLevel=Debug` .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do przewodnika krok po kroku dotyczący konfiguracji modułu.
> [!div class="nextstepaction"]
> [Przewodnik po konfiguracji modułu](./how-to-agent-configuration.md)