---
title: Aktualizowanie platformy Azure Percept DK przez powietrze
description: Dowiedz się, jak odbierać aktualizacje powietrza na platformie Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e627e582b47c5174e70f5d21d758148cde8dbdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022858"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>Aktualizowanie platformy Azure Percept DK przez powietrze

Postępuj zgodnie z tym przewodnikiem, aby dowiedzieć się, jak zaktualizować tablicę z systemem Azure Percept DK do środowiska AIR z aktualizacją urządzenia dla IoT Hub.

## <a name="import-your-update-file-and-manifest-file"></a>Zaimportuj plik aktualizacji i plik manifestu.

> [!NOTE]
> Jeśli aktualizacja została już zaimportowana, możesz przejść bezpośrednio do programu, aby **utworzyć zaktualizowaną grupę urządzeń**.

1. Przejdź do IoT Hub platformy Azure, który jest używany przez urządzenie Azure Percept. W panelu menu po lewej stronie wybierz pozycję **aktualizacje urządzeń** w obszarze **Automatyczne zarządzanie urządzeniami**.
 
1. Zobaczysz kilka kart w górnej części ekranu. Wybierz kartę **aktualizacje** .
 
1. Wybierz pozycję **+ Importuj nową aktualizację** poniżej nagłówka **gotowe do wdrożenia** .
 
1. Kliknij pola w obszarze **Wybierz pozycję Importuj plik manifestu** i **Wybierz pozycję Aktualizuj pliki** , aby wybrać odpowiedni plik manifestu (JSON) i jeden plik aktualizacji (. SWU). Te pliki aktualizacji dla urządzenia Percept platformy Azure można znaleźć [tutaj](https://go.microsoft.com/fwlink/?linkid=2155625).
 
1. Zaznacz ikonę folderu lub pole tekstowe w obszarze **Wybierz kontener magazynu**, a następnie wybierz odpowiednie konto magazynu.
 
1. Jeśli kontener magazynu został już utworzony, można go użyć do ponownego użycia. W przeciwnym razie wybierz pozycję **+ kontener** , aby utworzyć nowy kontener magazynu dla aktualizacji OTA. Wybierz kontener, którego chcesz użyć, a następnie kliknij przycisk **Wybierz**.
 
    >[!Note]
    >Jeśli nie masz kontenera, zostanie wyświetlony monit o jego utworzenie.
 
1. Wybierz pozycję **Prześlij** , aby rozpocząć proces importowania. Proces przekazywania zajmie około 4 minut.
 
    >[!Note]
    >W celu uzyskania dostępu do wybranego kontenera magazynu może zostać wyświetlony monit o dodanie reguły specyfikacji żądania krzyżowego (CORS). Wybierz pozycję **Dodaj regułę i ponów próbę** , aby wykonać operację.
 
    >[!Note]
    >Ze względu na rozmiar obrazu może zostać wyświetlona strona **przesyłanie...** Do 5 minut przed wyświetleniem kolejnego kroku.
    
1. Rozpocznie się proces importowania i nastąpi przekierowanie do karty **historia importowania** na stronie **aktualizacje urządzeń** . Kliknij przycisk **Odśwież** , aby monitorować postęp podczas kończenia procesu importowania. W zależności od rozmiaru aktualizacji może to potrwać kilka minut lub dłużej (w godzinach szczytu należy oczekiwać, że usługa importowania zajmie do 1hr).

1. Gdy kolumna stan wskazuje, że importowanie zakończyło się powodzeniem, wybierz kartę **gotowe do wdrożenia** , a następnie kliknij przycisk **Odśwież**. Zaimportowana aktualizacja powinna być teraz widoczna na liście.
 
## <a name="create-a-device-update-group"></a>Tworzenie grupy aktualizacji urządzeń
Aktualizacja urządzenia dla IoT Hub umożliwia kierowanie aktualizacji do określonych grup usługi Azure Percept DKs. Aby utworzyć grupę, należy dodać tag do docelowego zestawu urządzeń na platformie Azure IoT Hub.

> [!NOTE]
> Jeśli grupa została już utworzona, możesz przejść bezpośrednio do następnego kroku.

Wymagania dotyczące tagów grup:
- Możesz dodać dowolną wartość do tagu z wyjątkiem **niesklasyfikowanej** wartości, która jest wartością zastrzeżoną.
- Wartość tagu nie może przekraczać 255 znaków.
- Wartość tagu może zawierać tylko następujące znaki specjalne: ".", "-", "_", "~".
- W nazwach tagów i grup jest uwzględniana wielkość liter.
- Urządzenie może mieć tylko jeden tag. Każdy kolejny tag dodany do urządzenia zastąpi poprzedni tag.
- Urządzenie może należeć tylko do jednej grupy.

1. Dodaj tag do swoich urządzeń.
    1. W okienku nawigacji po lewej stronie **IoT Edge** Znajdź pozycję Azure Percept DK i przejdź do jej **sznurka**.
    1. Dodaj nową **aktualizację urządzenia dla IoT Hub** wartość tagu, jak pokazano poniżej (Zmień ```<CustomTagValue>``` na wartość, np. AzurePerceptGroup1). Dowiedz się więcej o [tagach dokumentu JSON](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)dotyczących urządzeń.

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. Kliknij przycisk **Zapisz** i rozwiąż wszelkie problemy z formatowaniem.
 
1. Utwórz grupę, wybierając istniejący tag IoT Hub platformy Azure.
    1. Przejdź z powrotem do strony usługi Azure IoT Hub.
    1. Wybierz pozycję **aktualizacje urządzeń** w obszarze **Automatyczne zarządzanie urządzeniami** w panelu menu po lewej stronie.
    1. Wybierz kartę **grupy** . Na tej stronie zostanie wyświetlona liczba urządzeń niezgrupowane podłączonych do aktualizacji urządzenia.
    1. Wybierz pozycję **+ Dodaj** , aby utworzyć nową grupę.
    1. Wybierz z listy tag IoT Hub i kliknij przycisk **Prześlij**.
    1. Po utworzeniu grupy zostanie zaktualizowana lista schematów zgodności aktualizacji i grup. Wykres pokazuje liczbę urządzeń w różnych stanach zgodności: **w najnowszej aktualizacji** **dostępne są nowe aktualizacje**, **aktualizacje w toku** i **nie są jeszcze pogrupowane**.
 

## <a name="deploy-an-update"></a>Wdrażanie aktualizacji
1. Powinna zostać wyświetlona nowo utworzona grupa z nową aktualizacją wymienioną w obszarze **dostępne aktualizacje** (może być konieczne odświeżenie raz). Wybierz aktualizację.
 
1. Upewnij się, że wybrano poprawną grupę urządzeń jako grupę urządzeń docelowych. Wybierz **datę rozpoczęcia** i **godzinę rozpoczęcia** wdrożenia, a następnie kliknij pozycję **Utwórz wdrożenie**. 

    >[!CAUTION]
    >Ustawienie godziny rozpoczęcia w przeszłości spowoduje natychmiastowe wyzwolenie wdrożenia.
 
1. Sprawdź wykres zgodności. Powinna zostać wyświetlona aktualizacja, która jest teraz w toku.
 
1. Po zakończeniu aktualizacji wykres zgodności będzie odpowiadał nowemu statusowi aktualizacji.
 
1. Wybierz kartę **wdrożenia** w górnej części strony **aktualizacje urządzeń** .
 
1. Wybierz wdrożenie, aby wyświetlić szczegóły wdrożenia. Może być konieczne kliknięcie przycisku **Odśwież** do momentu zmiany **stanu** na **powodzenie**.

## <a name="next-steps"></a>Następne kroki

Twój zestaw dev Kit został pomyślnie zaktualizowany. Możesz kontynuować rozwój i pracę z Twoim Devkit.