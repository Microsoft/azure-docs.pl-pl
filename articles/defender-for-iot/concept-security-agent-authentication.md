---
title: Uwierzytelnianie agenta zabezpieczeń
titleSuffix: Azure Defender for IoT
description: Wykonaj uwierzytelnianie mikroagenta, używając dwóch możliwych metod.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: b0304bd191626adb71041fb0561862b988ee25cd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124588"
---
# <a name="micro-agent-authentication-methods"></a>Metody uwierzytelniania Micro Agent 

Istnieją dwie opcje uwierzytelniania w usłudze Defender dla programu IoT Micro Agent: 

- Parametry połączenia 

- Certyfikat 

## <a name="authentication-using-a-connection-string"></a>Uwierzytelnianie przy użyciu parametrów połączenia 

Aby można było użyć parametrów połączenia, należy dodać plik, który używa parametrów połączenia zakodowanych w formacie UTF-8 w katalogu agenta usługi Defender w pliku o nazwie `connection_string.txt` . Na przykład

```azurecli
echo “<connection string>” > connection_string.txt 
```

Po wykonaniu tej czynności należy ponownie uruchomić usługę za pomocą tego polecenia.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Uwierzytelnianie przy użyciu certyfikatu 


Aby przeprowadzić uwierzytelnianie przy użyciu certyfikatu: 

1. Umieść w katalogu agenta usługi Defender część publiczną zaszyfrowanej przez PEM, w pliku o nazwie `certificate_public.pem` .
1. Umieść klucz prywatny szyfrowany przez PEM w katalogu agenta usługi Defender w pliku o nazwie `certificate_private.pem` .
1. Umieść odpowiednie parametry połączenia w pliku o nazwie `connection_string.txt` . Na przykład

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Ta akcja wskazuje, że Agent usługi Defender będzie oczekiwał certyfikatu do uwierzytelniania. 

1. Uruchom ponownie usługę przy użyciu następującego kodu: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Upewnij się, że Micro agent działa prawidłowo 

1. Uruchom następujące polecenie: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Sprawdź, czy usługa jest stabilna, upewniając się, że jest ona **aktywna** i czy czas działania procesu jest odpowiedni: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Upewnij się, że usługa jest stabilna, upewniając się, że jest ona aktywna.":::

## <a name="next-steps"></a>Następne kroki

Sprawdź [wzorzec Security stan — CIS](concept-security-posture.md).
