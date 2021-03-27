---
title: Aktualizacja urządzenia dla systemu operacyjnego Azure w czasie rzeczywistym | Microsoft Docs
description: Wprowadzenie do aktualizacji urządzenia dla systemu operacyjnego Azure w czasie rzeczywistym
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629057"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Samouczek dotyczący aktualizacji urządzeń z platformą Azure IoT Hub przy użyciu systemu operacyjnego Azure w czasie rzeczywistym (RTO)

W tym samouczku przedstawiono sposób tworzenia aktualizacji urządzenia dla agenta IoT Hub w usłudze Azure RTO NetX Duo. Udostępnia także proste interfejsy API dla deweloperów, które umożliwiają deweloperom integrację możliwości aktualizacji urządzeń z aplikacjami. Zapoznaj się z [przykładami](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) najważniejszych tablic ewaluacyjnych, które obejmują przewodniki wprowadzenie, aby dowiedzieć się, jak konfigurować, kompilować i wdrażać aktualizacje za pośrednictwem środowiska AIR (OTA) na urządzeniach.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Rozpoczęcie pracy
> * Oznaczanie urządzenia
> * Tworzenie grupy urządzeń
> * Wdróż aktualizację obrazu
> * Monitorowanie wdrożenia aktualizacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne
* Dostęp do IoT Hub. Zaleca się użycie warstwy S1 (standardowa) lub wyższej.
* Wystąpienie aktualizacji urządzenia i konto połączone z IoT Hub. Postępuj zgodnie z przewodnikiem, aby [utworzyć i połączyć](http://create-device-update-account.md/) konto aktualizacji urządzenia, jeśli nie zostało to wcześniej zrobione.

## <a name="get-started"></a>Rozpoczęcie pracy

Każdy projekt przykładowej usługi Azure RTO dla konkretnych tablic zawiera kod i dokumentację dotyczącą sposobu używania aktualizacji urządzenia do IoT Hub. 
1. Pobierz przykładowe pliki charakterystyczne dla tablicy z [przykładów platformy Azure RTO i aktualizacji urządzeń](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
2. Znajdź folder docs z pobranego przykładu.
3. W witrynie docs postępuj zgodnie z instrukcjami dotyczącymi przygotowania zasobów platformy Azure, konta i rejestrowania urządzeń IoT.
5. Następnie postępuj zgodnie z dokumentacją, aby utworzyć nowy obraz oprogramowania układowego i zaimportował manifest dla tablicy.
6. Dalej Publikuj obraz oprogramowania układowego i manifest do aktualizacji urządzenia dla IoT Hub.
7. Na koniec Pobierz i Uruchom projekt na urządzeniu.

Dowiedz się więcej o [usłudze Azure RTO](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Oznaczanie urządzenia

1. Zadbaj, aby aplikacja urządzenia działała z poprzedniego kroku.
2. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.
3. W okienku nawigacji po lewej stronie urządzenia IoT Znajdź urządzenie IoT i przejdź do sznurka urządzenia.
4. W obszarze urządzenia Usuń wszystkie istniejące wartości tagu aktualizacji urządzenia, ustawiając je na wartość null.
5. Dodaj nową wartość znacznika aktualizacji urządzenia, jak pokazano poniżej.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Utwórz grupę aktualizacji

1. Przejdź do IoT Hub, który został wcześniej połączony z wystąpieniem aktualizacji urządzenia.
2. Wybierz opcję Aktualizacje urządzenia w obszarze Automatyczne zarządzanie urządzeniami na pasku nawigacyjnym po lewej stronie.
3. Wybierz kartę grupy w górnej części strony. 
4. Wybierz przycisk Dodaj, aby utworzyć nową grupę.
5. Wybierz z listy tag IoT Hub utworzony w poprzednim kroku. Wybierz pozycję Utwórz grupę aktualizacji.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Zrzut ekranu przedstawiający wybór znacznika." lightbox="media/create-update-group/select-tag.PNG":::

[Dowiedz się więcej](create-update-group.md) na temat dodawania tagów i tworzenia grup aktualizacji

## <a name="deploy-new-firmware"></a>Wdróż nowe oprogramowanie układowe

1. Po utworzeniu grupy powinna zostać wyświetlona nowa aktualizacja dla grupy urządzeń z linkiem do aktualizacji w obszarze oczekujące aktualizacje. Może być konieczne odświeżenie jednego z nich. 
2. Kliknij dostępną aktualizację.
3. Upewnij się, że wybrano poprawną grupę jako grupę docelową. Zaplanuj wdrożenie, a następnie wybierz pozycję Wdróż aktualizację.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Wybierz aktualizację" lightbox="media/deploy-update/select-update.png":::

4. Wyświetl wykres zgodności. Powinna zostać wyświetlona aktualizacja, która jest teraz w toku. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aktualizacja w toku" lightbox="media/deploy-update/update-in-progress.png":::

5. Po pomyślnym zaktualizowaniu urządzenia powinna zostać wyświetlona aktualizacja wykresu zgodności i szczegółów wdrożenia w celu odzwierciedlenia tego samego. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aktualizacja powiodła się" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorowanie wdrożenia aktualizacji

1. Wybierz kartę wdrożenia w górnej części strony.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta wdrożenia" lightbox="media/deploy-update/deployments-tab.png":::

2. Wybierz utworzone wdrożenie, aby wyświetlić szczegóły wdrożenia.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Szczegóły wdrożenia" lightbox="media/deploy-update/deployment-details.png":::

3. Wybierz pozycję Odśwież, aby wyświetlić najnowsze Szczegóły stanu. Kontynuuj ten proces do momentu, aż stan zmieni się na powodzenie.

Ukończono pomyślnie kompleksową aktualizację obrazu przy użyciu aktualizacji urządzenia dla IoT Hub na urządzeniu Raspberry Pi 3 B + +. 

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Oczyść konto, wystąpienie, IoT Hub i urządzenie IoT aktualizacji urządzenia. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure RTO i sposobie jej działania z usługą Azure IoT, zobacz https://azure.com/rtos .
