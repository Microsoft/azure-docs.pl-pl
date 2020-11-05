---
title: Wyświetlanie i konfigurowanie metryk usługi Azure IoT Connector for FHIR (wersja zapoznawcza)
description: W tym artykule wyjaśniono sposób wyświetlania i konfigurowania łącznika usługi Azure IoT for FHIR (wersja zapoznawcza)
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 1cdae789b8286be408735fff92e2de46e28ce514
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394292"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Wyświetlanie i konfigurowanie metryk usługi Azure IoT Connector for FHIR (wersja zapoznawcza) 

W tym artykule dowiesz się, jak wyświetlać i konfigurować łącznik usługi Azure IoT dla metryk FHIR *. 

> [!TIP]
> Postępuj zgodnie ze wskazówkami zawartymi w artykule [Eksportowanie łącznika usługi Azure IoT for FHIR (wersja zapoznawcza) za pomocą ustawień diagnostycznych](./iot-metrics-diagnostics-export.md) , aby dowiedzieć się, jak skonfigurować eksportowanie danych metryk.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Wyświetl metryki dla usługi Azure IoT Connector for FHIR (wersja zapoznawcza)
1. Aby wyświetlić metryki dla łączników IoT, wybierz usługę Azure API for FHIR w Azure Portal. 

2. Przejdź do **metryk** 

3. Wybierz kartę **Łącznik IoT** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Wybierz łącznik usługi IoT, aby wyświetlić jego metryki (na przykład: istnieją (4) łączniki IoT skojarzone z tym interfejsem API platformy Azure dla usługi FHIR).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> Karta **niestandardowa** umożliwia tworzenie określonych kombinacji czasu/daty na potrzeby wyświetlania metryk łącznika IoT.

5. Wybierz okres czasu wyświetlania metryk łącznika usługi IoT (na przykład: 1 godzina, 24 godziny, 7 dni lub niestandardowe).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT Connector3" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Typy metryk dla łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza) 
Wyświetlone metryki łącznika IoT są następujące:

|Typ metryk|Cel metryki| 
|-----------|--------------|
|Liczba wiadomości przychodzących|Liczba odebranych nieprzetworzonych wiadomości przychodzących (na przykład: zdarzenia urządzenia).|
|Liczba znormalizowanych komunikatów|Liczba znormalizowanych komunikatów.|
|Liczba grup komunikatów|Liczba grup, które mają komunikaty zagregowane w wydzielonym przedziale czasu.|
|Średni czas oczekiwania na znormalizowany etap|Średnie opóźnienie etapu normalizacji. Normalizacja etap polega na normalizacji w nieprzetworzonych wiadomościach przychodzących.|
|Średnie opóźnienie etapu grupy|Średnie opóźnienie etapu grupy. Etapem grupy jest przeprowadzenie buforowania, agregowanie i grupowanie w znormalizowanych komunikatach.| 
|Łączna liczba błędów|Łączna liczba błędów.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Koncentrowanie się i Konfigurowanie łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)
W tym przykładzie będziemy koncentrować się na **liczbie metryk komunikatów przychodzących** .

1. Wybierz punkt w czasie, na którym chcesz się skoncentrować.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT Connector4" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Na tym ekranie możesz **dodać metrykę** , **dodać filtr** i **zastosować podział** do dalszych dostosowań. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT Connector5" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Podsumowanie 
Posiadanie dostępu do metryk płaszczyzny danych jest niezbędne do monitorowania i rozwiązywania problemów.  Łącznik usługi Azure IoT dla programu FHIR ułatwia wykonywanie tych działań za pomocą metryk. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z często zadawanymi pytaniami dotyczącymi łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla FHIR — często zadawane pytania](fhir-faq.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza).

FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.