---
title: Informacje o urządzeniach w określonych strefach
description: Korzystanie z lokalnej konsoli zarządzania w celu uzyskania kompleksowych informacji o widoku dla określonej strefy
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776338"
---
# <a name="view-information-per-zone"></a>Wyświetl informacje na strefę


## <a name="view-a-device-map-for-a-zone"></a>Wyświetlanie mapy urządzeń dla strefy

Wyświetl mapę urządzenia dla wybranej strefy na czujniku. Ten widok przedstawia wszystkie elementy sieci powiązane z wybraną strefą, w tym czujniki, podłączone do nich urządzenia i inne informacje.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Zrzut ekranu przedstawiający mapę strefy.":::


- W oknie **Zarządzanie lokacją** wybierz pozycję **Wyświetl mapę strefy** na pasku zawierającym nazwę strefy.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Domyślny region do domyślnej jednostki biznesowej.":::

Zostanie wyświetlone okno **Mapowanie urządzeń** . Następujące narzędzia są dostępne do wyświetlania informacji dotyczących urządzeń i urządzeń z mapy. Aby uzyskać szczegółowe informacje o każdej z tych funkcji, zobacz *Podręcznik użytkownika platformy Defender for IoT*.

- **Widoki powiększenia mapy**: widok uproszczony, widok połączeń i widok szczegółowy. Widok wyświetlonej mapy różni się w zależności od poziomu powiększenia mapy. Można przełączać się między widokami mapy przez dostosowanie poziomów powiększenia.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Narzędzia do wyszukiwania map i układu**: narzędzia używane do wyświetlania różnych segmentów sieci, urządzeń, grup urządzeń lub warstw.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Zrzut ekranu przedstawiający widok narzędzia do wyszukiwania i układu.":::

- **Etykiety i wskaźniki na urządzeniach:** Na przykład liczba urządzeń zgrupowanych w podsieci w sieci IT. W tym przykładzie jest to 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Zrzut ekranu etykiet i wskaźników.":::

- **Wyświetl właściwości urządzenia**: na przykład czujnik monitorujący urządzenie i podstawowe właściwości urządzeń. Kliknij prawym przyciskiem myszy urządzenie, aby wyświetlić jego właściwości.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Zrzut ekranu przedstawiający widok właściwości urządzenia.":::

- **Alert skojarzony z urządzeniem:** Kliknij prawym przyciskiem myszy urządzenie, aby wyświetlić powiązane alerty.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Zrzut ekranu przedstawiający Widok wyświetlanie alertów.":::

## <a name="view-alerts-associated-with-a-zone"></a>Wyświetlanie alertów skojarzonych ze strefą

Aby wyświetlić alerty skojarzone z określoną strefą:

- Wybierz ikonę alertu w oknie **strefy** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Widok domyślnej jednostki biznesowej z przykładami.":::

Aby uzyskać więcej informacji, zobacz temat [Omówienie: Praca z alertami](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Wyświetlanie spisu urządzeń dla strefy

Aby wyświetlić spis urządzeń skojarzony z określoną strefą:

- W oknie **strefa** wybierz pozycję **Wyświetl spis urządzeń** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Zostanie wyświetlony ekran spisu urządzeń.":::

Aby uzyskać więcej informacji, zobacz temat [badanie wszystkich wykrywania czujników przedsiębiorstwa w spisie urządzeń](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Wyświetl dodatkowe informacje o strefie

Dostępne są następujące dodatkowe informacje o strefie:

- **Szczegóły strefy**: Wyświetl liczbę urządzeń, alertów i czujników skojarzonych ze strefą.

- **Szczegóły czujnika**: Wyświetl nazwę, adres IP i wersję każdego czujnika przypisanego do strefy.

- **Stan łączności**: jeśli czujnik jest odłączony, Połącz się z czujnika. Zobacz [czujniki połączeń z lokalną konsolą zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Postęp aktualizacji**: jeśli czujnik podłączony jest uaktualniany, zostaną wyświetlone Stany uaktualnienia. Podczas uaktualniania lokalna Konsola zarządzania nie odbiera informacji o urządzeniu z czujnika.

## <a name="next-steps"></a>Następne kroki

[Uzyskiwanie wglądu w globalne, regionalne i lokalne zagrożenia](how-to-gain-insight-into-global-regional-and-local-threats.md)
