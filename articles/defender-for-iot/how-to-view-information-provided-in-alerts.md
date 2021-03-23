---
title: Komunikaty alertów — informacje
description: Wybierz alert z okna alerty, aby przejrzeć szczegóły.
ms.date: 3/21/2021
ms.topic: how-to
ms.openlocfilehash: 2fa2b265c7d3983ca6ae2d7507392dd37afabd27
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781487"
---
# <a name="about-alert-messages"></a>Komunikaty alertów — informacje

Wybierz alert z okna **alerty** , aby przejrzeć szczegóły alertu. Szczegóły obejmują następujące informacje:

- Metadane alertu

- Informacje o ruchu, urządzeniach i zdarzeniu

- Linki do podłączonych urządzeń w mapie urządzeń

- Komentarze zdefiniowane przez analityków i administratorów zabezpieczeń

- Zalecenia dotyczące badania zdarzenia

## <a name="alert-metadata"></a>Metadane alertu

Szczegóły alertu obejmują następujące metadane alertu:

  - Identyfikator alertu

  - Aparat zasad, który wyzwolił alert

  - Data i godzina, o której alert został wyzwolony

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Wykryto nieautoryzowaną łączność z Internetem.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informacje o urządzeniach, ruchu i zdarzeniu

Komunikat o alercie zawiera informacje na temat:

  - Wykryte urządzenia.

  - Ruch wykrywany między urządzeniami, takimi jak protokoły i kody funkcji.

  - Szczegółowe informacje na temat skutków zdarzenia.

Tych informacji można użyć podczas decydowania o sposobie zarządzania zdarzeniem alertu.

## <a name="links-to-connected-devices-in-the-device-map"></a>Linki do podłączonych urządzeń w mapie urządzeń

Aby dowiedzieć się więcej na temat urządzeń podłączonych do wykrytych urządzeń, można wybrać obraz urządzenia w alercie i wyświetlić podłączone urządzenia na mapie.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="Operacja RCP nie powiodła się.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Zrzut ekranu urządzeń.":::

Mapa mapuje do wybranego urządzenia i innych podłączonych do niego urządzeń. Mapa wyświetla również okno dialogowe **Szybkie właściwości** dla urządzeń wykrytych w alertach.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Komentarze zdefiniowane przez analityków i administratorów zabezpieczeń 

Alerty mogą zawierać listę wstępnie zdefiniowanych komentarzy. Na przykład komentarze mogą być instrukcjami dotyczącymi działań zaradczych w celu podjęcia lub nazwiska osób kontaktowych się z wydarzeniem.

Gdy zarządzasz zdarzeniem alertu, możesz wybrać komentarz lub komentarze najlepiej odzwierciedlające stan zdarzenia lub kroki, które należy wykonać w celu zbadania alertu.

Wybrane Komentarze są zapisywane w komunikacie alertu. Praca z komentarzami usprawnia komunikację między osobami i zespołami podczas badania zdarzenia alertu. W efekcie komentarze mogą przyspieszyć czas reakcji na zdarzenie.

Administrator lub analityk zabezpieczeń wstępnie definiuje Komentarze. Wybrane Komentarze nie są przekazywane do systemów partnerskich zdefiniowanych w regułach przekazywania.

Po przejrzeniu informacji w alercie można wykonać rozmaite kroki śledczej, które ułatwiają zarządzanie wydarzeniem alertu. Na przykład:

- Analizuj ostatnie aktywność urządzenia (raport wyszukiwania danych). 

- Analizuj inne zdarzenia, które wystąpiły w tym samym czasie (oś czasu zdarzenia). 

- Analizuj kompleksowy ruch zdarzeń (plik PCAP).

## <a name="pcap-files"></a>Pliki PCAP

W niektórych przypadkach można uzyskać dostęp do pliku PCAP z komunikatu alertu. Może to być przydatne, jeśli chcesz uzyskać bardziej szczegółowe informacje o skojarzonym ruchu sieciowym.

Aby pobrać plik PCAP, wybierz pozycję :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="Pobierz ikonę."::: w prawym górnym rogu okna dialogowego **szczegóły alertu** .

## <a name="recommendations-for-investigating-an-event"></a>Zalecenia dotyczące badania zdarzenia 

W obszarze **rekomendacji** alertu są wyświetlane informacje, które mogą pomóc w lepszym zrozumieniu zdarzenia. Przejrzyj te informacje przed zarządzaniem zdarzeniem alertu lub wykonaniem akcji na urządzeniu lub w sieci.

## <a name="see-also"></a>Zobacz też

[Przyspiesz przepływy pracy alertów](how-to-accelerate-alert-incident-response.md)

[Zarządzanie zdarzeniem alertu](how-to-manage-the-alert-event.md)
