---
title: Utwórz grupę urządzeń w aktualizacji urządzenia dla platformy Azure IoT Hub | Microsoft Docs
description: Tworzenie grupy urządzeń w ramach aktualizacji urządzenia dla platformy Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679520"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Tworzenie grup urządzeń w ramach aktualizacji urządzenia dla IoT Hub
Aktualizacja urządzenia dla IoT Hub umożliwia wdrażanie aktualizacji w grupie urządzeń IoT.

## <a name="prerequisites"></a>Wymagania wstępne

* [Dostęp do IoT Hub z aktualizacją urządzenia dla IoT Hub włączona](create-device-update-account.md). Zalecane jest użycie warstwy S1 (standardowa) lub wyższej dla IoT Hub. 
* Urządzenie IoT (lub symulator) obsługiwane dla aktualizacji urządzenia w ramach IoT Hub.
* [Co najmniej jedna aktualizacja została pomyślnie zaimportowana dla urządzenia z zainicjowaną obsługą administracyjną.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Dodawanie tagu do urządzeń  

Aktualizacja urządzenia dla IoT Hub umożliwia wdrażanie aktualizacji w grupie urządzeń IoT. Aby utworzyć grupę, najpierw należy dodać tag do docelowego zestawu urządzeń w IoT Hub. Tagi można pomyślnie dodać do urządzenia tylko po jego podłączeniu do aktualizacji urządzenia.

W poniższej dokumentacji opisano, jak dodać i zaktualizować tag.

### <a name="programmatically-update-device-twin"></a>Programowe aktualizowanie sznurka urządzenia

Przed zarejestrowaniem urządzenia w ramach aktualizacji urządzenia można zaktualizować sznurki urządzenia przy użyciu odpowiedniego znacznika. 
[Dowiedz się, jak dodać tagi przy użyciu przykładowej aplikacji .NET.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Informacje o właściwościach tagów](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Format tagu aktualizacji urządzenia

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Korzystanie z zadań

Istnieje możliwość zaplanowania zadania na wielu urządzeniach w celu dodania lub zaktualizowania tagu aktualizacji urządzenia po [poniższych przykładach](../iot-hub/iot-hub-devguide-jobs.md) . [Dowiedz się więcej](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Ta akcja dotyczy bieżącego przydziału komunikatów centrum IOT Hub i zaleca się zmianę tylko do 50 000 tagów bliźniaczych urządzeń w innym momencie, w przeciwnym razie może być konieczne zakupienie większej liczby jednostek IoT Hub w przypadku przekroczenia dziennego limitu przydziału IoT Hub komunikatów. Szczegóły można znaleźć w temacie [limity przydziału i ograniczanie przepustowości](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Aktualizacje bezpośrednich przędzy

Tagi mogą również być dodawane lub aktualizowane bezpośrednio w ramach urządzeń.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.

2. W okienku nawigacji po lewej stronie "IoT Devices" lub "IoT Edge" Znajdź urządzenie IoT i przejdź do tego samego urządzenia.

3. W obszarze urządzenia Usuń wszystkie istniejące wartości tagu aktualizacji urządzenia, ustawiając je na wartość null.

4. Dodaj nową wartość znacznika aktualizacji urządzenia, jak pokazano poniżej. [Przykładowy dokument JSON z tagami.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Ograniczenia

* Możesz dodać dowolną wartość do tagu z wyjątkiem "bez kategorii", która jest wartością zastrzeżoną.
* Wartość tagu nie może przekraczać 255 znaków.
* Wartość tagu może zawierać znaki alfanumeryczne i następujące znaki specjalne ".", "-", "_", "~".
* W nazwach tagów i grup jest rozróżniana wielkość liter.
* Urządzenie może mieć tylko jeden tag o nazwie ADUGroup, wszelkie kolejne Dodatki tagu o tej nazwie zastąpią istniejącą wartość dla nazwy tagu ADUGroup.
* Jedno urządzenie może należeć tylko do jednej grupy.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Utwórz grupę urządzeń, wybierając istniejący tag IoT Hub

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Wybierz IoT Hub wcześniej połączone z wystąpieniem aktualizacji urządzenia.

3. Wybierz opcję Aktualizacje urządzenia w obszarze Automatyczne zarządzanie urządzeniami na pasku nawigacyjnym po lewej stronie.

4. Wybierz kartę grupy w górnej części strony. Będzie można zobaczyć liczbę urządzeń podłączonych do aktualizacji urządzenia, które nie są jeszcze zgrupowane.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Zrzut ekranu przedstawiający urządzenia niezgrupowane." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Wybierz przycisk Dodaj, aby utworzyć nową grupę.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Zrzut ekranu dodawania grupy urządzeń." lightbox="media/create-update-group/add-group.png":::

6. Wybierz z listy znacznik IoT Hub a następnie wybierz pozycję Utwórz grupę aktualizacji.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Zrzut ekranu przedstawiający wybór tagu." lightbox="media/create-update-group/select-tag.png":::

7. Po utworzeniu grupy zobaczysz, że lista wykresów i grup zgodności aktualizacji jest aktualizowana.  Wykres zgodności aktualizacji przedstawia liczbę urządzeń w różnych stanach zgodności: w przypadku najnowszej aktualizacji dostępne są nowe aktualizacje, aktualizacje w toku i urządzenia, które nie zostały jeszcze pogrupowane. [Dowiedz się więcej o zgodności aktualizacji.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Zrzut ekranu przedstawiający widok zgodności aktualizacji." lightbox="media/create-update-group/updated-view.png":::

8. Powinna zostać wyświetlona nowo utworzona grupa i wszystkie dostępne aktualizacje dla urządzeń w nowej grupie. Aktualizację można wdrożyć do nowej grupy z tego widoku, klikając nazwę aktualizacji. Zobacz następny krok: Wdróż aktualizację, aby uzyskać więcej szczegółów.

## <a name="view-device-details-for-the-group-you-created"></a>Wyświetl szczegóły urządzenia dla utworzonej grupy

1. Przejdź do nowo utworzonej grupy i kliknij nazwę grupy.

2. Zostanie wyświetlona lista urządzeń będących częścią grupy wraz z ich właściwościami aktualizacji urządzenia. W tym widoku można także wyświetlić informacje o zgodności aktualizacji dla wszystkich urządzeń, które są członkami tej grupy. Wykres zgodności aktualizacji przedstawia liczbę urządzeń w różnych stanach zgodności: w najnowszej aktualizacji dostępne są nowe aktualizacje i aktualizacje w toku.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Zrzut ekranu przedstawiający widok szczegółów grupy urządzeń." lightbox="media/create-update-group/group-details.png":::

3. Możesz również kliknąć poszczególne urządzenia w grupie, aby przekierować je do strony szczegółów urządzenia w IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Zrzut ekranu przedstawiający widok szczegółów urządzenia." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Następne kroki 

[Wdróż aktualizację](deploy-update.md)

[Dowiedz się więcej o grupach urządzeń](device-update-groups.md)

[Dowiedz się więcej o zgodności aktualizacji.](device-update-compliance.md)
