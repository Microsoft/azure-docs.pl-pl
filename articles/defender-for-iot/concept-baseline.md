---
title: Testy bazowe i niestandardowe
description: Zapoznaj się z koncepcją planu bazowego usługi Azure Defender for IoT.
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779362"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Usługa Azure Defender dla linii bazowej i czeków niestandardowych

W tym artykule opisano usługę Defender dla linii bazowej usługi IoT oraz podsumowuje wszystkie skojarzone właściwości niestandardowych kontroli linii bazowej.

## <a name="baseline"></a>Punkt odniesienia

Linia bazowa ustala standardowe zachowanie poszczególnych urządzeń i ułatwia ustalenie nietypowego zachowania lub odchylenia od oczekiwanych norm.

## <a name="baseline-custom-checks"></a>Niestandardowe kontrole linii bazowej

Niestandardowe kontrole linii bazowej określają niestandardową listę kontroli dla każdej linii bazowej urządzenia przy użyciu **sznurka tożsamości modułu** urządzenia.

## <a name="setting-baseline-properties"></a>Ustawianie właściwości linii bazowej

1. W IoT Hub Znajdź i wybierz urządzenie, które chcesz zmienić.

1. Kliknij urządzenie, a następnie kliknij moduł **azureiotsecurity** .

1. Kliknij pozycję **moduł identyfikacja sznurka**.

1. Przekaż **niestandardowy plik kontroli linii bazowej** na urządzenie.

1. Dodaj właściwości linii bazowej do programu Defender-IoT-Micro-Agent, a następnie kliknij przycisk **Zapisz**.

### <a name="baseline-custom-check-file-example"></a>Przykład podstawowego pliku sprawdzania niestandardowego

Aby skonfigurować niestandardowe kontrole linii bazowej:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Właściwości niestandardowego sprawdzania kontroli linii bazowej

| Nazwa| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Wymagane: prawda |Prawidłowe wartości: **wartość logiczna** |Wartość domyślna: **Fałsz** |Maksymalny przedział czasu przed wysłaniem komunikatów o wysokim priorytecie.|
|baselineCustomChecksFilePath |Wymagane: prawda|Prawidłowe wartości: **String**, **null** |Wartość domyślna: **null** |Pełna ścieżka do podstawowej konfiguracji XML|
|baselineCustomChecksFileHash |Wymagane: prawda|Prawidłowe wartości: **String**, **null** |Wartość domyślna: **null** |`sha256sum` pliku konfiguracyjnego XML. Aby uzyskać dodatkowe informacje, użyj [odwołania sha256sum](https://linux.die.net/man/1/sha256sum) . |

Aby zapoznać się z dodatkowymi przykładami linii bazowej, zobacz [przykład niestandardowego punktu odniesienia-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) i [niestandardowy punkt odniesienia — 2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Następne kroki

- Uzyskiwanie dostępu do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- [Badanie urządzenia](how-to-investigate-device.md)
- Omówienie i eksplorowanie [zaleceń dotyczących zabezpieczeń](concept-recommendations.md)
- Poznawanie i eksplorowanie [alertów zabezpieczeń](concept-security-alerts.md)
