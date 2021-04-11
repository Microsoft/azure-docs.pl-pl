---
title: Aktualizowanie platformy Azure Percept w trybie DK do środowiska AIR (OTA)
description: Dowiedz się, jak odbierać aktualizacje środowiska AIR (OTA) na platformie Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: a3f586f853201534bbaa613e8538d55485ffe147
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063121"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Aktualizowanie platformy Azure Percept w trybie DK do środowiska AIR (OTA)

Postępuj zgodnie z tym przewodnikiem, aby dowiedzieć się, jak zaktualizować system operacyjny i oprogramowanie układowe tablicy nośnej platformy Azure Percept w wersji zaszeregowej (OTA) z aktualizacją urządzenia dla IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacji usługi Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie zestawu deweloperskiego z siecią Wi-Fi, utworzono IoT Hub i połączono swój zestaw deweloperski z IoT Hub
- [Aktualizacja urządzenia dla IoT Hub została pomyślnie skonfigurowana](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Zaimportuj plik aktualizacji i plik manifestu

> [!NOTE]
> Jeśli aktualizacja została już zaimportowana, możesz przejść bezpośrednio do **tworzenia grupy aktualizacji urządzenia**.

1. [Pobierz odpowiedni plik manifestu (JSON) i plik aktualizacji (. SWU) dla urządzenia Percept platformy Azure](https://go.microsoft.com/fwlink/?linkid=2155625).

1. Przejdź do IoT Hub platformy Azure, który jest używany przez urządzenie Azure Percept. W panelu menu po lewej stronie wybierz pozycję **aktualizacje urządzeń** w obszarze **Automatyczne zarządzanie urządzeniami**.

1. Zobaczysz kilka kart w górnej części ekranu. Wybierz kartę **aktualizacje** .

1. Wybierz pozycję **+ Importuj nową aktualizację** poniżej nagłówka **gotowe do wdrożenia** .

1. Kliknij pola w obszarze **Wybierz pozycję Importuj plik manifestu** i **Wybierz pozycję Aktualizuj pliki** , aby wybrać plik manifestu (JSON) i plik aktualizacji (. SWU).

1. Zaznacz ikonę folderu lub pole tekstowe w obszarze **Wybierz kontener magazynu** i wybierz odpowiednie konto magazynu. Jeśli kontener magazynu został już utworzony, możesz go wykorzystać. W przeciwnym razie wybierz pozycję **+ kontener** , aby utworzyć nowy kontener magazynu dla aktualizacji OTA. Wybierz kontener, którego chcesz użyć, a następnie kliknij przycisk **Wybierz**.

1. Wybierz pozycję **Prześlij** , aby rozpocząć proces importowania. Ze względu na rozmiar obrazu proces przesłania może potrwać do 5 minut.

    > [!NOTE]
    > W celu uzyskania dostępu do wybranego kontenera magazynu może zostać wyświetlony monit o dodanie reguły specyfikacji żądania krzyżowego (CORS). Wybierz pozycję **Dodaj regułę i ponów próbę** , aby wykonać operację.

1. Po rozpoczęciu procesu importowania nastąpi przekierowanie do karty **historia importowania** na stronie **aktualizacje urządzeń** . Kliknij przycisk **Odśwież** , aby monitorować postęp podczas kończenia procesu importowania. W zależności od rozmiaru aktualizacji może to potrwać kilka minut lub dłużej (w godzinach szczytu usługa importowania może trwać do 1 godziny).

1. Gdy kolumna **stan** wskazuje, że importowanie zakończyło się pomyślnie, wybierz kartę **gotowe do wdrożenia** , a następnie kliknij przycisk **Odśwież**. Zaimportowana aktualizacja powinna być teraz widoczna na liście.

## <a name="create-a-device-update-group"></a>Tworzenie grupy aktualizacji urządzeń

Aktualizacja urządzenia dla IoT Hub umożliwia kierowanie aktualizacji do określonych grup usługi Azure Percept DKs. Aby utworzyć grupę, należy dodać tag do docelowego zestawu urządzeń na platformie Azure IoT Hub.

> [!NOTE]
> Jeśli grupa została już utworzona, możesz przejść do następnej sekcji.

Wymagania dotyczące tagów grup:

- Możesz dodać dowolną wartość do tagu z wyjątkiem "bez kategorii", która jest wartością zastrzeżoną.
- Wartość tagu nie może przekraczać 255 znaków.
- Wartość tagu może zawierać tylko następujące znaki specjalne: ".", "-", "_", "~".
- W nazwach tagów i grup jest uwzględniana wielkość liter.
- Urządzenie może mieć tylko jeden tag. Każdy kolejny tag dodany do urządzenia zastąpi poprzedni tag.
- Urządzenie może należeć tylko do jednej grupy.

1. Dodaj tag do swoich urządzeń:

    1. W okienku nawigacji po lewej stronie **IoT Edge** Znajdź pozycję Azure Percept DK i przejdź do jej **sznurka**.

    1. Dodaj nową **aktualizację urządzenia dla IoT Hub** wartość tagu, jak pokazano poniżej ( ```<CustomTagValue>``` odwołuje się do wartości tagu/nazwy, np. AzurePerceptGroup1). Dowiedz się więcej o [tagach dokumentu JSON](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)dotyczących urządzeń.

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Kliknij przycisk **Zapisz** i rozwiąż wszelkie problemy z formatowaniem.

1. Utwórz grupę, wybierając istniejący tag IoT Hub platformy Azure:

    1. Przejdź z powrotem do strony usługi Azure IoT Hub.

    1. Wybierz pozycję **aktualizacje urządzeń** w obszarze **Automatyczne zarządzanie urządzeniami** w panelu menu po lewej stronie.

    1. Wybierz kartę **grupy** . Na tej stronie zostanie wyświetlona liczba urządzeń niezgrupowane podłączonych do aktualizacji urządzenia.

    1. Wybierz pozycję **+ Dodaj** , aby utworzyć nową grupę.

    1. Wybierz z listy tag IoT Hub i kliknij przycisk **Prześlij**.

    1. Po utworzeniu grupy zostanie zaktualizowana lista schematów zgodności aktualizacji i grup. Wykres pokazuje liczbę urządzeń w różnych stanach zgodności: **w najnowszej aktualizacji** **dostępne są nowe aktualizacje**, **aktualizacje w toku** i **nie są jeszcze pogrupowane**.

## <a name="deploy-an-update"></a>Wdrażanie aktualizacji

1. Powinna zostać wyświetlona nowo utworzona grupa z nową aktualizacją wymienioną w obszarze **dostępne aktualizacje** (może być konieczne odświeżenie raz). Wybierz aktualizację.

1. Upewnij się, że wybrano poprawną grupę urządzeń jako grupę urządzeń docelowych. Wybierz **datę rozpoczęcia** i **godzinę rozpoczęcia** wdrożenia, a następnie kliknij pozycję **Utwórz wdrożenie**.

    > [!CAUTION]
    > Ustawienie godziny rozpoczęcia w przeszłości spowoduje natychmiastowe wyzwolenie wdrożenia.

1. Sprawdź wykres zgodności. Powinna zostać wyświetlona aktualizacja, która jest teraz w toku.

1. Po zakończeniu aktualizacji wykres zgodności będzie odpowiadał nowemu statusowi aktualizacji.

1. Wybierz kartę **wdrożenia** w górnej części strony **aktualizacje urządzeń** .

1. Wybierz wdrożenie, aby wyświetlić szczegóły wdrożenia. Może być konieczne kliknięcie przycisku **Odśwież** do momentu zmiany **stanu** na **powodzenie**.

## <a name="next-steps"></a>Następne kroki

Twój zestaw dev Kit został pomyślnie zaktualizowany. Możesz kontynuować programowanie i pracę z Twoim zestawem deweloperskim.