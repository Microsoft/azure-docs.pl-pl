---
title: Utwórz konto aktualizacji urządzenia w aktualizacji urządzenia dla platformy Azure IoT Hub | Microsoft Docs
description: Utwórz konto aktualizacji urządzenia w usłudze aktualizacja urządzenia dla platformy Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558486"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Usługa Device Update a zarządzanie zasobem usługi IoT Hub

Aby rozpocząć pracę z aktualizacją urządzenia, musisz utworzyć konto aktualizacji urządzenia, wystąpienie i ustawić role kontroli dostępu. 

## <a name="prerequisites"></a>Wymagania wstępne

* Dostęp do IoT Hub. Zaleca się użycie warstwy S1 (standardowa) lub wyższej. 
* Obsługiwane przeglądarki:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Utwórz konto aktualizacji urządzenia

1. Przejdź do [Azure Portal](https://portal.azure.com)

2. Kliknij pozycję Utwórz zasób i wyszukaj ciąg "Aktualizacja urządzenia dla IoT Hub"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Zrzut ekranu przedstawiający aktualizację urządzenia dla IoT Hub zasobu." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Kliknij przycisk Utwórz > aktualizację urządzenia dla IoT Hub

4. Określ subskrypcję platformy Azure, która ma zostać skojarzona z kontem aktualizacji urządzenia i grupą zasobów

5. Określ nazwę i lokalizację konta aktualizacji urządzenia

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły konta." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Aby poznać regiony, w których jest dostępna aktualizacja urządzenia IoT Hub, można przejść do [strony produkty na platformie Azure](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) . Jeśli aktualizacja urządzenia dla IoT Hub nie jest dostępna w Twoim regionie, możesz utworzyć konto w najbliższym regionie. 

6. Kliknij przycisk "dalej: recenzja + Utwórz>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Zrzut ekranu przedstawiający przegląd szczegółów konta." lightbox="media/create-device-update-account/account-review.png":::

7. Przejrzyj szczegóły, a następnie wybierz pozycję "Utwórz". Zobaczysz, że wdrożenie jest w toku. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Zrzut ekranu wdrożenia konta jest w toku." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Stan wdrożenia zostanie zmieniony na "ukończone" w ciągu kilku minut. Kliknij pozycję "przejdź do zasobu"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Zrzut ekranu przedstawiający wdrażanie konta zostało zakończone." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Tworzenie wystąpienia aktualizacji urządzenia 

Wystąpienie aktualizacji urządzenia jest skojarzone z pojedynczym Centrum IoT Hub. Wybierz Centrum IoT Hub, które będzie używane z aktualizacją urządzenia. W tym kroku utworzymy nowe zasady dostępu współdzielonego w celu zapewnienia, że aktualizacja urządzenia będzie używać tylko wymaganych uprawnień do pracy z IoT Hub (zapis rejestru i połączenie usługi). Te zasady zapewniają, że dostęp jest ograniczony tylko do aktualizacji urządzenia.

Aby utworzyć wystąpienie aktualizacji urządzenia po utworzeniu konta.

1. Po utworzeniu nowo utworzonego zasobu konta przejdź do bloku Zarządzanie wystąpieniami "wystąpienia"

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Zrzut ekranu przedstawiający Zarządzanie wystąpieniami w ramach konta." lightbox="media/create-device-update-account/instance-blade.png":::

2. Kliknij pozycję "Utwórz i określ nazwę wystąpienia i wybierz IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły wystąpienia." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > IoT Hub połączenie z zasobem aktualizacji urządzenia nie musi znajdować się w tym samym regionie co konto aktualizacji urządzenia. Jednak w celu uzyskania lepszej wydajności zaleca się, aby IoT Hub znajdować się w regionie, który jest taki sam jak lub blisko regionu konta aktualizacji urządzenia. 

3. Kliknij pozycję „Utwórz”. Wystąpienie zostanie wyświetlone w stanie "Tworzenie". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Zrzut ekranu przedstawiający tworzenie wystąpienia." lightbox="media/create-device-update-account/instance-creating.png":::

4. Zezwól na 5-10 minut na ukończenie wdrożenia wystąpienia. Odśwież stan, aby zobaczyć "stan aprowizacji", przejdź do "powodzenie".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Zrzut ekranu przedstawiający tworzenie wystąpienia powiodło się." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Konfigurowanie IoT Hub 

Aby aktualizacja urządzenia mogła odbierać powiadomienia o zmianach z IoT Hub, aktualizacja urządzenia jest zintegrowana z centrum zdarzeń "wbudowane". Kliknięcie przycisku "Konfiguruj IoT Hub" konfiguruje wymagane trasy komunikatów i zasady dostępu wymagane do komunikowania się z urządzeniami IoT. 

Aby skonfigurować IoT Hub

1. Gdy wystąpienie "stan aprowizacji" zmieni się na "powodzenie", wybierz wystąpienie w bloku Zarządzanie wystąpieniem. Kliknij pozycję "Konfiguruj IoT Hub"

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Zrzut ekranu przedstawiający Konfigurowanie IoT Hub wystąpienia." lightbox="media/create-device-update-account/instance-configure.png":::

2. Wybierz pozycję "Zgadzam się wprowadzić te zmiany"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Zrzut ekranu przedstawiający wyrażenie zgody na konfigurowanie IoT Hub wystąpienia." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Kliknij pozycję "Aktualizuj".

[Dowiedz się więcej na temat skonfigurowanych tras komunikatów.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Konfigurowanie ról kontroli dostępu

Aby inni użytkownicy mieli dostęp do aktualizacji urządzenia, użytkownicy muszą mieć udzielony dostęp do tego zasobu. 

1. Przejdź do kontroli dostępu (IAM) w ramach konta aktualizacji urządzenia

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Zrzut ekranu kontroli dostępu w ramach konta aktualizacji urządzenia." lightbox="media/create-device-update-account/account-access-control.png":::

2. Kliknij pozycję "Dodaj przypisania ról".

3. W obszarze "Wybierz rolę" Wybierz rolę aktualizacji urządzenia z podaną opcją.
     - Administrator aktualizacji urządzenia
     - Czytnik aktualizacji urządzenia
     - Administrator zawartości urządzenia z aktualizacją
     - Czytnik zawartości urządzenia aktualizacji
     - Administrator wdrożeń aktualizacji urządzeń
     - Czytnik wdrożeń aktualizacji urządzeń
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Zrzut ekranu przypisań roli kontroli dostępu w ramach konta aktualizacji urządzenia." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Informacje na temat kontroli dostępu opartej na rolach w aktualizacji urządzenia dla IoT Hub](device-update-control-access.md) 
    
4. Przypisywanie dostępu do użytkownika lub grupy usługi Azure AD
5. Klikanie pozycji Zapisz.
6. Teraz można przystąpić do korzystania z funkcji aktualizacji urządzenia z poziomu IoT Hub

## <a name="next-steps"></a>Następne kroki

Spróbuj zaktualizować urządzenie, korzystając z jednego z następujących krótkich samouczków:

 - [Aktualizacja urządzenia w symulatorze](device-update-simulator.md)
 - [Aktualizacja urządzenia na Raspberry Pi](device-update-raspberry-pi.md)
 - [Aktualizacja urządzenia na Ubuntu Server 18,04 x64 Package Agent](device-update-ubuntu-agent.md)

[Informacje o koncie i wystąpieniu aktualizacji urządzenia.](device-update-resources.md) 

[Dowiedz się więcej o rolach kontroli dostępu do aktualizacji urządzeń. ](device-update-control-access.md) 

