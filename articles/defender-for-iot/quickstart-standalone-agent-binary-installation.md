---
title: Instalowanie usługi Defender for IoT Micro Agent (wersja zapoznawcza)
description: Informacje na temat instalowania i uwierzytelniania programu Defender Micro Agent.
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: ecde9f42acc5cf4a924a657f4f0cdc545c4a668e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782728"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>Instalowanie usługi Defender for IoT Micro Agent (wersja zapoznawcza)

Ten artykuł zawiera wyjaśnienie sposobu instalowania i uwierzytelniania programu Defender Micro Agent.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem modułu Defender for IoT należy utworzyć tożsamość modułu w IoT Hub. Aby uzyskać więcej informacji na temat tworzenia tożsamości modułu, zobacz [Tworzenie sznurka modułu usługi Defender IoT Micro Agent (wersja zapoznawcza)](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Zainstaluj pakiet

Zainstaluj i skonfiguruj repozytorium pakietu Microsoft, wykonując [te instrukcje](/windows-server/administration/linux-package-repository-for-microsoft-software). 

W przypadku Debian 9 instrukcje nie obejmują repozytorium, które należy dodać, użyj następujących poleceń, aby dodać repozytorium: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Aby zainstalować pakiet Defender Micro Agent na Debian i dystrybucje oparte na systemie Linux Ubuntu, użyj następującego polecenia:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Metody uwierzytelniania Micro Agent 

Dwie opcje używane do uwierzytelniania usługi Defender dla programu IoT Micro Agent są następujące: 

- Parametry połączenia tożsamości modułu. 

- Certyfikatu.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Uwierzytelnianie przy użyciu parametrów połączenia tożsamości modułu

Upewnij się, że [wymagania wstępne](#prerequisites) dotyczące tego artykułu zostały spełnione i że utworzono tożsamość modułu przed rozpoczęciem wykonywania tych kroków. 

#### <a name="get-the-module-identity-connection-string"></a>Pobierz parametry połączenia tożsamości modułu

Aby uzyskać parametry połączenia tożsamości modułu z IoT Hub: 

1. Przejdź do IoT Hub i wybierz centrum.

1. W menu po lewej stronie w sekcji **Explorer** wybierz pozycję **urządzenia IoT**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Wybierz pozycję urządzenia IoT z menu po lewej stronie.":::

1. Wybierz urządzenie z listy Identyfikator urządzenia, aby wyświetlić stronę **szczegółów urządzenia** .

1. Wybierz kartę **tożsamości modułów**   , a następnie wybierz moduł **DefenderIotMicroAgent**   z listy tożsamości modułów skojarzonych z urządzeniem.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Wybierz kartę tożsamości modułów.":::

1. Na stronie **szczegóły tożsamości modułu** skopiuj klucz podstawowy, wybierając przycisk **Kopiuj** .

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Wybierz przycisk Kopiuj, aby skopiować klucz podstawowy.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Konfigurowanie uwierzytelniania przy użyciu parametrów połączenia tożsamości modułu

Aby skonfigurować agenta do uwierzytelniania przy użyciu parametrów połączenia tożsamości modułu:

1. Umieść plik o nazwie `connection_string.txt` zawierający parametry połączenia zakodowane w formacie UTF-8 w ścieżce katalogu agenta usługi Defender `/var/defender_iot_micro_agent` , wprowadzając następujące polecenie:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    Powinna znajdować się `connection_string.txt` w następującej lokalizacji ścieżki `/var/defender_iot_micro_agent/connection_string.txt` .

1. Uruchom ponownie usługę za pomocą tego polecenia:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Uwierzytelnianie przy użyciu certyfikatu

Aby uwierzytelnić się przy użyciu certyfikatu:

1. Pozyskaj certyfikat, wykonując [te instrukcje](../iot-hub/iot-hub-security-x509-get-started.md).

1. Umieść część certyfikatu zaszyfrowanej przez PEM i klucz prywatny, w programie do katalogu agenta usługi Defender w pliku o nazwie `certificate_public.pem` i `certificate_private.pem` . 

1. Umieść odpowiednie parametry połączenia w `connection_string.txt` pliku. parametry połączenia powinny wyglądać następująco: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Ten ciąg ostrzega agenta usługi Defender, aby można było oczekiwać certyfikatu do uwierzytelnienia. 

1. Uruchom ponownie usługę przy użyciu następującego polecenia:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Weryfikowanie instalacji

Aby sprawdzić poprawność instalacji:

1. Upewnienie się, że Micro agent działa prawidłowo, za pomocą następującego polecenia:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Upewnij się, że usługa jest stabilna, upewniając się, że jest `active` i że czas działania procesu jest właściwy

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Upewnij się, że usługa jest stabilna i aktywna.":::
 
## <a name="testing-the-system-end-to-end"></a>Testowanie kompleksowego systemu 

Aby przetestować system od końca do końca, można utworzyć plik wyzwalacza na urządzeniu. Plik wyzwalacza spowoduje, że skanowanie linii bazowej w agencie wykryje plik jako naruszenie zasad linii bazowej. 

Utwórz plik w systemie plików przy użyciu następującego polecenia:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Zalecenie dotyczące niepowodzenia walidacji linii bazowej będzie odbywać się w centrum z `CceId` DEFENDER_FOR_IOT_TEST_CHECKS Debian-0,0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Zalecenie dotyczące niepowodzenia walidacji linii bazowej, które występuje w centrum." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Poczekaj do 1 godziny, aby rekomendacja była widoczna w centrum. 

## <a name="micro-agent-versioning"></a>Obsługa wersji Micro Agent 

Aby zainstalować określoną wersję programu Defender IoT Micro Agent, uruchom następujące polecenie: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Następne kroki

[Kompilowanie programu Defender Micro Agent z kodu źródłowego](quickstart-building-the-defender-micro-agent-from-source.md)