---
title: Eksportowanie metryk usługi Azure IoT Connector for FHIR (wersja zapoznawcza) za pomocą ustawień diagnostycznych
description: W tym artykule wyjaśniono, jak wyeksportować łącznik usługi Azure IoT for FHIR (wersja zapoznawcza) za pomocą ustawień diagnostycznych
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 00abad784048b67e9d89c12b9be3f631f586fb07
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574540"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Eksportowanie metryk usługi Azure IoT Connector for FHIR (wersja zapoznawcza) za pomocą ustawień diagnostycznych

W tym artykule dowiesz się, jak wyeksportować łącznik usługi Azure IoT pod kątem szybkich zasobów współdziałania usług opieki zdrowotnej (FHIR&#174;) * dzienników metryk. Funkcja umożliwiająca rejestrowanie metryk jest [**ustawieniami diagnostycznymi**](../azure-monitor/essentials/diagnostic-settings.md) w Azure Portal. 

> [!TIP]
> Postępuj zgodnie ze wskazówkami zawartymi w temacie [Włączanie rejestrowania diagnostycznego w usłudze Azure API for FHIR i Azure IoT Connector dla FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) w celu skonfigurowania rejestrowania inspekcji.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Włącz rejestrowanie metryk dla łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)
1. Aby włączyć rejestrowanie metryk dla łącznika usługi Azure IoT dla programu FHIR, wybierz usługę Azure API for FHIR w Azure Portal 

2. Przejdź do **ustawień diagnostycznych** 

3. Wybierz **+ Dodaj ustawienie diagnostyczne**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Wprowadź nazwę w oknie dialogowym **Nazwa ustawienia diagnostycznego** .

5. Wybierz metodę, która ma być używana do uzyskiwania dostępu do dzienników diagnostycznych:

    1. **Archiwizuj na koncie magazynu** na potrzeby inspekcji lub inspekcji ręcznej. Konto magazynu, które ma być używane, musi być już utworzone.
    2. **Przesyłaj strumieniowo do centrum zdarzeń** w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne. Aby można było skonfigurować ten krok, należy utworzyć przestrzeń nazw centrum zdarzeń i zasady centrum zdarzeń.
    3. **Przesyłaj strumieniowo do** obszaru roboczego Log Analytics w Azure monitor. Przed wybraniem tej opcji musisz utworzyć obszar roboczy analizy dzienników.

6. Wybierz **błędy, ruch i czas oczekiwania** dla łącznika usługi Azure IoT dla FHIR.  Wybierz dodatkowe kategorie metryk, które chcesz przechwycić dla interfejsu API platformy Azure dla FHIR.

7. Wybierz pozycję **Zapisz**

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Aby pierwsze dzienniki metryk były wyświetlane w wybranym repozytorium, może upłynąć do 15 minut.  
 
Aby uzyskać więcej informacji na temat sposobu pracy z dziennikami diagnostycznymi, zobacz [dokumentację dzienników zasobów platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="conclusion"></a>Podsumowanie 
Posiadanie dostępu do dzienników metryk jest niezbędne do monitorowania i rozwiązywania problemów.  Łącznik usługi Azure IoT dla programu FHIR umożliwia wykonywanie tych działań za pomocą dzienników metryk. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z często zadawanymi pytaniami dotyczącymi łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla FHIR — często zadawane pytania](fhir-faq.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7.