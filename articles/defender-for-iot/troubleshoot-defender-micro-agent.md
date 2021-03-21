---
title: Rozwiązywanie problemów z usługą Defender IoT Micro Agent (wersja zapoznawcza)
titleSuffix: Azure Defender for IoT
description: Dowiedz się, jak obsłużyć nieoczekiwane lub niewyjaśnione błędy.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 07198a5d0ef5d0a6c9eed97523c61826e451b7f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124180"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Rozwiązywanie problemów z usługą Defender IoT Micro Agent (wersja zapoznawcza)

W przypadku nieoczekiwanych lub niewyjaśnionych błędów Użyj następujących metod rozwiązywania problemów, aby spróbować rozwiązać problemy. Możesz również skontaktować się z zespołem produktu usługi Azure Defender for IoT, aby uzyskać pomoc w razie potrzeby.   

## <a name="service-status"></a>Stan usługi 

Aby wyświetlić stan usługi: 

1. Uruchom następujące polecenie

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Sprawdź, czy usługa jest stabilna, upewniając się `active` , że jest i że czas działania w procesie jest odpowiedni.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Upewnij się, że usługa jest stabilna, sprawdzając, czy jest ona aktywna i czy jest to właściwy czas.":::

Jeśli usługa jest wymieniona jako `inactive` , użyj następującego polecenia, aby uruchomić usługę:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Wiadomo, że usługa ulega awarii, jeśli czas działania procesu jest zbyt krótki. Aby rozwiązać ten problem, należy przejrzeć dzienniki.

## <a name="review-logs"></a>Przeglądanie dzienników 

Użyj poniższego polecenia, aby sprawdzić, czy usługa Defender IoT Micro agent działa z uprawnieniami głównymi.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Sprawdź, czy usługa Defender for IoT Micro agent działa z uprawnieniami głównymi.":::

Aby wyświetlić dzienniki, użyj następującego polecenia:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Aby ponownie uruchomić usługę, użyj następującego polecenia: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [obsługą funkcji i wycofaniem](edge-security-module-deprecation.md).
