---
title: Wdrażanie IoT Edge modułu zabezpieczeń
description: Dowiedz się, jak wdrożyć agenta zabezpieczeń usługi Defender for IoT na IoT Edge.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71efb0bb12d1e20f918481a086fd411d3a237e33
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813600"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Wdrażanie modułu zabezpieczeń na urządzeniu IoT Edge urządzeniu

**Moduł usługi Defender dla IoT** zapewnia kompleksowe rozwiązanie w zakresie zabezpieczeń dla IoT Edge mobilnych.
Moduł zabezpieczeń zbiera, agreguje i analizuje nieprzetworzone dane zabezpieczeń z systemu operacyjnego i systemu kontenerów w celu analizowania rekomendacji i alertów dotyczących zabezpieczeń z akcjami.
Aby dowiedzieć się więcej, zobacz [Moduł zabezpieczeń dla IoT Edge](security-edge-architecture.md).

W tym artykule dowiesz się, jak wdrożyć moduł zabezpieczeń na urządzeniu IoT Edge urządzeniu.

## <a name="deploy-security-module"></a>Wdrażanie modułu zabezpieczeń

Aby wdrożyć moduł zabezpieczeń usługi Defender dla IoT dla usługi IoT Edge, należy wykonać poniższe IoT Edge.

### <a name="prerequisites"></a>Wymagania wstępne

1. Na IoT Hub upewnij się, że urządzenie jest zarejestrowane [jako IoT Edge urządzenie.](../iot-edge/how-to-register-device.md#register-a-new-device)

1. Moduł defender IoT Edge wymaga, aby na urządzeniu IoT Edge zainstalowano platformę [AuditD.](https://linux.die.net/man/8/auditd)

    - Zainstaluj platformę, uruchamiając następujące polecenie na IoT Edge urządzeniu:

    `sudo apt-get install auditd audispd-plugins`

    - Sprawdź, czy inspekcja jest aktywna, uruchamiając następujące polecenie:

    `sudo systemctl status auditd`<br>
    - Oczekiwana odpowiedź: `active (running)`

### <a name="deployment-using-azure-portal"></a>Wdrażanie przy użyciu Azure Portal

1. W witrynie Azure Portal **Marketplace.**

1. Wybierz **Internet rzeczy**, a następnie wyszukaj Azure Security Center **dla IoT** i wybierz go.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Wybieranie usługi Defender dla IoT":::

1. Wybierz **pozycję Utwórz,** aby skonfigurować wdrożenie.

1. Wybierz subskrypcję **platformy** Azure dla IoT Hub, a następnie wybierz **IoT Hub**.<br>Wybierz **pozycję Deploy to a device to target** a single device (Wd wdrażaj na jednym urządzeniu) lub wybierz pozycję Deploy at Scale **(Wd** wdrażaj na dużą skalę), aby wybrać wiele urządzeń docelowych, a następnie wybierz pozycję Create **(Utwórz).** Aby uzyskać więcej informacji na temat wdrażania na dużą skalę, [zobacz Jak wdrożyć usługę](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Jeśli wybrano **pozycję Wd wdrażaj** na dużą skalę, dodaj nazwę i szczegóły urządzenia przed kontynuowaniem do karty **Dodawanie** modułów w poniższych instrukcjach.

Wykonaj każdy krok, aby ukończyć IoT Edge wdrożenia usługi Defender dla IoT.

#### <a name="step-1-modules"></a>Krok 1. Moduły

1. Wybierz moduł **AzureSecurityCenterforIoT.**
1. Na karcie **Ustawienia modułu** zmień nazwę **na** **azureiotsecurity.**
1. Na  karcie Zmienne środowiskowe dodaj zmienną w razie potrzeby (na przykład możesz dodać poziom *debugowania* i ustawić ją na jedną z następujących wartości: "Krytyczny", "Błąd", "Ostrzeżenie" lub "Informacje").
1. Na karcie **Opcje tworzenia kontenera** dodaj następującą konfigurację:

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

1. Na karcie **Ustawienia bliźniaczej reprezentacji** modułu dodaj następującą konfigurację:

   Właściwość bliźniaczej reprezentacji modułu:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Zawartość właściwości bliźniaczej reprezentacji modułu: 

   ```json
     {

     }
   ```
    
   Aby uzyskać więcej informacji na temat konfigurowania agenta, zobacz [Konfigurowanie agentów zabezpieczeń](./how-to-agent-configuration.md).

1. Wybierz pozycję **Aktualizuj**.

#### <a name="step-2-runtime-settings"></a>Krok 2. Ustawienia środowiska uruchomieniowego

1. Wybierz **pozycję Ustawienia środowiska uruchomieniowego.**
2. W **obszarze Centrum krawędzi** zmień **obraz** na **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.

    >[!Note]
    > Obecnie jest obsługiwana wersja 1.0.8.3 lub starsza.

3. Sprawdź, **czy opcja** Utwórz jest ustawiona na następującą konfigurację:

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

1. Na karcie **Określanie tras** upewnij się, że masz trasę (jawną lub niejawną), która będzie przesyłać komunikaty z modułu **azureiotsecurity** do usługi **$upstream** zgodnie z poniższymi przykładami. Tylko wtedy, gdy trasa jest na miejscu, wybierz pozycję **Dalej.**

   Przykładowe trasy:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Wybierz opcję **Dalej**.

#### <a name="step-4-review-deployment"></a>Krok 4. Przegląd wdrożenia

- Na karcie **Przeglądanie wdrożenia** przejrzyj informacje o wdrożeniu, a następnie wybierz **pozycję Utwórz,** aby ukończyć wdrożenie.

## <a name="diagnostic-steps"></a>Kroki diagnostyczne

Jeśli wystąpi problem, dzienniki kontenerów to najlepszy sposób na nauczenia się stanu urządzenia IoT Edge modułu zabezpieczeń. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Sprawdź, czy wymagane kontenery zostały zainstalowane i działają zgodnie z oczekiwaniami

1. Uruchom następujące polecenie na urządzeniu IoT Edge urządzenia:

    `sudo docker ps`

1. Sprawdź, czy są uruchomione następujące kontenery:

   | Nazwa | OBRAZ |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Jeśli minimalne wymagane kontenery nie są obecne, sprawdź, czy manifest IoT Edge wdrożenia jest dopasowany do zalecanych ustawień. Aby uzyskać więcej informacji, zobacz [Deploy IoT Edge module (Wdrażanie IoT Edge modułu](#deployment-using-azure-portal)).

### <a name="inspect-the-module-logs-for-errors"></a>Sprawdzanie dzienników modułu pod celu sprawdzenia, czy występują błędy

1. Uruchom następujące polecenie na urządzeniu IoT Edge urządzenia:

   `sudo docker logs azureiotsecurity`

1. Aby uzyskać bardziej szczegółowe dzienniki, dodaj następującą zmienną środowiskową do **wdrożenia modułu azureiotsecurity:** `logLevel=Debug` .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do podręcznika konfiguracji modułu.
> [!div class="nextstepaction"]
> [Przewodnik po konfiguracji modułu](./how-to-agent-configuration.md)