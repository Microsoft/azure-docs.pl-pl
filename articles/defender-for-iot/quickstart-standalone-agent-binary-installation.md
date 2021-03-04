---
title: Instalowanie usługi Defender for IoT Micro Agent (wersja zapoznawcza)
titleSuffix: Azure Defender for IoT
description: Informacje na temat instalowania i uwierzytelniania programu Defender Micro Agent.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/3/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 4125fc3c2f03104eeca108a73d83f2fad44bebc1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051756"
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

- Parametry połączenia. 

- Certyfikatu.

### <a name="authenticate-using-a-connection-string"></a>Uwierzytelnianie przy użyciu parametrów połączenia

Aby uwierzytelnić się przy użyciu parametrów połączenia:

1. Umieść plik o nazwie `connection_string.txt` zawierający parametry połączenia zakodowane w formacie UTF-8 w ścieżce katalogu agenta usługi Defender `/var/defender_iot_micro_agent` , wprowadzając następujące polecenie:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    Teraz powinien znajdować się `connection_string.txt` w następującej lokalizacji ścieżki `/var/defender_iot_micro_agent/connection_string.txt` .

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