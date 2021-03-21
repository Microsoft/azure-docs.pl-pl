---
title: Wdrażanie aktualizacji przy użyciu aktualizacji urządzenia dla platformy Azure IoT Hub | Microsoft Docs
description: Wdróż aktualizację za pomocą aktualizacji urządzenia dla platformy Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679513"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Wdróż aktualizację przy użyciu aktualizacji urządzenia dla IoT Hub

Dowiedz się, jak wdrożyć aktualizację na urządzeniu IoT przy użyciu aktualizacji urządzenia dla IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

* [Dostęp do IoT Hub z aktualizacją urządzenia dla IoT Hub włączona](create-device-update-account.md). Zalecane jest użycie warstwy S1 (standardowa) lub wyższej dla IoT Hub. 
* [Co najmniej jedna aktualizacja została pomyślnie zaimportowana dla urządzenia z zainicjowaną obsługą administracyjną.](import-update.md) 
* Urządzenie IoT (lub symulator) obsługiwane dla aktualizacji urządzenia w ramach IoT Hub.
* [Do urządzenia IoT, które próbujesz zaktualizować, został przypisany tag. Urządzenie należy do co najmniej jednej grupy aktualizacji.](create-update-group.md)
* Obsługiwane przeglądarki:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Wdrażanie aktualizacji

1. Przejdź do [Azure Portal](https://portal.azure.com)

2. Przejdź do bloku aktualizacja urządzenia IoT Hub.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Wybierz kartę grupy w górnej części strony. [Dowiedz się więcej](device-update-groups.md) o grupach urządzeń. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Karta grupy" lightbox="media/deploy-update/updated-view.png":::

4. Wyświetlanie listy wykresów i grup zgodności aktualizacji. Powinna zostać wyświetlona nowa aktualizacja dla grupy urządzeń z linkiem do aktualizacji w obszarze oczekujące aktualizacje (może być konieczne odświeżenie raz). [Dowiedz się więcej o zgodności aktualizacji.](device-update-compliance.md) 

5. Wybierz dostępną aktualizację.

6. Upewnij się, że wybrano poprawną grupę jako grupę docelową. Zaplanuj wdrożenie, a następnie wybierz pozycję Wdróż aktualizację.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Wybierz aktualizację" lightbox="media/deploy-update/select-update.png":::

7. Wyświetl wykres zgodności. Powinna zostać wyświetlona aktualizacja, która jest teraz w toku. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aktualizacja w toku" lightbox="media/deploy-update/update-in-progress.png":::

8. Po pomyślnym zaktualizowaniu urządzenia powinna zostać wyświetlona aktualizacja wykresu zgodności i szczegółów wdrożenia w celu odzwierciedlenia tego samego. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aktualizacja powiodła się" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorowanie wdrożenia aktualizacji

1. Wybierz kartę wdrożenia w górnej części strony.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta wdrożenia" lightbox="media/deploy-update/deployments-tab.png":::

2. Wybierz utworzone wdrożenie, aby wyświetlić szczegóły wdrożenia.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Szczegóły wdrożenia" lightbox="media/deploy-update/deployment-details.png":::

3. Wybierz pozycję Odśwież, aby wyświetlić najnowsze Szczegóły stanu. Kontynuuj ten proces do momentu, aż stan zmieni się na powodzenie.


## <a name="retry-an-update-deployment"></a>Ponawianie próby wdrożenia aktualizacji

Jeśli z jakiegoś powodu wdrożenie nie powiedzie się, można ponowić wdrożenie dla niezakończonych urządzeń. 

1. Przejdź do karty wdrożenia, a następnie wybierz wdrożenie, które nie powiodło się. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Szczegóły wdrożenia" lightbox="media/deploy-update/deployment-details.png":::

2. Kliknij stan urządzenia "Niepowodzenie" w okienku szczegółowe informacje o wdrożeniu.

3. Kliknij pozycję "Ponów próbę urządzeń zakończonych niepowodzeniem" i Potwierdź powiadomienie o potwierdzeniu. 

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie typowych problemów](troubleshoot-device-update.md)
