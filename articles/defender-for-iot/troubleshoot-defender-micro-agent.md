---
title: Rozwiązywanie problemów z usługą Defender IoT Micro Agent (wersja zapoznawcza)
description: Dowiedz się, jak obsłużyć nieoczekiwane lub niewyjaśnione błędy.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011423"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Rozwiązywanie problemów z usługą Defender IoT Micro Agent (wersja zapoznawcza)

W przypadku wystąpienia nieoczekiwanego błędu można użyć tych metod rozwiązywania problemów w celu rozwiązania problemu. Możesz również skontaktować się z zespołem produktu usługi Azure Defender for IoT, aby uzyskać pomoc w razie potrzeby.   

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

Wiadomo, że usługa jest nieprzerwana, jeśli proces działania procesu jest krótszy niż 2 minuty. Aby rozwiązać ten problem, należy [przejrzeć dzienniki](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Weryfikowanie uprawnień głównych w programie Micro Agent

Użyj poniższego polecenia, aby sprawdzić, czy usługa Defender IoT Micro agent działa z uprawnieniami głównymi.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Sprawdź, czy usługa Defender for IoT Micro agent działa z uprawnieniami głównymi.":::
## <a name="review-the-logs"></a>Przejrzyj dzienniki 

Aby przejrzeć dzienniki, użyj następującego polecenia:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Szybkie przeglądy dzienników

Jeśli wystąpi problem podczas uruchamiania programu Micro Agent, można uruchomić program Micro Agent w stanie tymczasowym, który umożliwi wyświetlenie dzienników przy użyciu następującego polecenia:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Uruchom ponownie usługę

Aby ponownie uruchomić usługę, użyj następującego polecenia: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [obsługą funkcji i wycofaniem](edge-security-module-deprecation.md).
