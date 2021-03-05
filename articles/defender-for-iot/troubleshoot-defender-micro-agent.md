---
title: Rozwiązywanie problemów z usługą Defender IoT Micro Agent
titleSuffix: Azure Defender for IoT
description: Dowiedz się, jak obsłużyć nieoczekiwane lub niewyjaśnione błędy.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: dade0d0d5dc4d690ea94f20deaf956b1e079bad7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124180"
---
# <a name="defender-iot-micro-agent-troubleshooting"></a>Rozwiązywanie problemów z usługą Defender IoT Micro Agent 

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
