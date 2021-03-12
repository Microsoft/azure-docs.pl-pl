---
title: Wyświetlanie i konfigurowanie metryk usługi Azure IoT Connector for FHIR (wersja zapoznawcza)
description: W tym artykule opisano sposób wyświetlania i konfigurowania usługi Azure IoT Connector for FHIR (wersja zapoznawcza).
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 831c6df3f50bfff9b411660d9efc4cd78ee5e8d9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020634"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Wyświetlanie i konfigurowanie metryk usługi Azure IoT Connector for FHIR (wersja zapoznawcza) 

W tym artykule dowiesz się, jak wyświetlać i konfigurować łącznik usługi Azure IoT na potrzeby szybkich zasobów współdziałania w ramach usług opieki zdrowotnej (FHIR&#174;) * metryki.

> [!TIP]
> Aby dowiedzieć się, jak skonfigurować eksportowanie danych metryk, postępuj zgodnie ze wskazówkami zawartymi w temacie [Eksportowanie metryk usługi Azure IoT Connector for FHIR (wersja zapoznawcza) za pomocą ustawień diagnostycznych](iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Wyświetl metryki dla usługi Azure IoT Connector for FHIR (wersja zapoznawcza)

1. Zaloguj się do Azure Portal, a następnie wybierz usługę Azure API for FHIR. 

2. W okienku po lewej stronie wybierz pozycję **metryki**. 

3. Wybierz kartę **Łącznik IoT** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;IoT Connector&quot; z wykresami liniowymi wyświetlającymi liczbę komunikatów przychodzących i znormalizowanych." lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Wybierz łącznik IoT, aby wyświetlić jego metryki. Na przykład istnieją cztery łączniki IoT (*Łącznik 1*, *Łącznik 2* itd.) skojarzone z tym interfejsem API platformy Azure dla usługi FHIR.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;IoT Connector&quot; z kartami łączników IoT 1, 2, 3 i 4." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Wybierz okres (na przykład **1 godzina**, **24 godziny**, **7 dni** lub **niestandardowe**) metryk łącznika usługi IoT, które chcesz wyświetlić. Wybierając kartę **niestandardową** , można utworzyć określone kombinacje czasu/daty do wyświetlania metryk łącznika IoT.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;IoT Connector&quot;, na którym jest wyświetlany wykres liniowy okresu &quot;1-godzinnego&quot; dla łącznika 1." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Typy metryk dla łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza) 

> [!TIP]
> Informacje o przepływie danych w łączniku usługi Azure IoT dla FHIR można znaleźć w przewodniku rozwiązywania problemów z usługą Azure IoT Connector [for FHIR (wersja zapoznawcza)](iot-data-flow.md) i [usługą Azure IoT Connector for FHIR (wersja zapoznawcza)](iot-troubleshoot-guide.md) .

Metryki łącznika IoT, które można wyświetlić, są wymienione w poniższej tabeli:

|Typ metryki|Cel metryki| 
|-----------|--------------|
|Liczba wiadomości przychodzących|Przedstawia liczbę odebranych nieprzetworzonych wiadomości przychodzących (na przykład zdarzeń urządzeń).|
|Liczba znormalizowanych komunikatów|Wyświetla liczbę znormalizowanych komunikatów.|
|Liczba grup komunikatów|Wyświetla liczbę grup, które mają zagregowane komunikaty w wydzielonym przedziale czasu.|
|Średni czas oczekiwania na znormalizowany etap|Wyświetla średni czas oczekiwania na znormalizowany etap. Znormalizowany etap wykonuje normalizację dla nieprzetworzonych wiadomości przychodzących.|
|Średnie opóźnienie etapu grupy|Wyświetla Średnie opóźnienie etapu grupy. Etap grupy wykonuje buforowanie, agregowanie i grupowanie na znormalizowanych wiadomościach.| 
|Łączna liczba błędów|Wyświetla łączną liczbę błędów.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Skup się na metrykach usługi Azure IoT Connector for FHIR (wersja zapoznawcza) i skonfiguruj je

W tym przykładzie skupmy się na **liczbie metryk komunikatów przychodzących** .

1. Wybierz punkt w czasie, na którym chcesz się skoncentrować.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Zrzut ekranu przedstawiający okienko Metryka &quot;Liczba komunikatów przychodzących&quot; z wyróżnionym pojedynczym punktem w czasie na wykresie liniowym." lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. W okienku **Liczba wiadomości przychodzących** możesz dodatkowo dostosować metrykę, wybierając pozycję **Dodaj metrykę**, **Dodaj filtr** lub **Zastosuj podział**. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Zrzut ekranu przedstawiający okienko Metryka &quot;Liczba komunikatów przychodzących&quot; z wyróżnionymi przyciskami Dodaj metrykę, &quot;Dodaj filtr,&quot; i &quot;Zastosuj podział&quot;." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Podsumowanie 
Posiadanie dostępu do metryk płaszczyzny danych jest niezbędne do monitorowania i rozwiązywania problemów. Łącznik usługi Azure IoT dla programu FHIR ułatwia wykonywanie tych działań za pomocą metryk. 

## <a name="next-steps"></a>Następne kroki

Uzyskaj odpowiedzi na często zadawane pytania dotyczące łącznika usługi Azure IoT dla FHIR.

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla programu FHIR — często zadawane pytania](fhir-faq.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7. 
