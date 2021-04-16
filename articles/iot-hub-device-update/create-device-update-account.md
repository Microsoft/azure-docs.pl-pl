---
title: Tworzenie konta aktualizacji urządzenia w aktualizacji urządzenia dla Azure IoT Hub | Microsoft Docs
description: Utwórz konto aktualizacji urządzenia w aktualizacji urządzenia dla Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d3f7f4e1cdd56675d6084448abc810c9a41992f9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520143"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Usługa Device Update a zarządzanie zasobem usługi IoT Hub

Aby rozpocząć pracę z aktualizacją urządzenia, musisz utworzyć konto aktualizacji urządzenia, wystąpienie i ustawić role kontroli dostępu. 

## <a name="prerequisites"></a>Wymagania wstępne

* Dostęp do IoT Hub. Zaleca się używanie warstwy S1 (Standardowa) lub wyższych. 
* Obsługiwane przeglądarki:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Tworzenie konta aktualizacji urządzenia

1. Przejdź do [Azure Portal](https://portal.azure.com)

2. Kliknij pozycję Utwórz zasób i wyszukaj pozycję "Aktualizacja urządzenia dla IoT Hub"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Zrzut ekranu przedstawiający aktualizację urządzenia dla IoT Hub zasobów." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Kliknij pozycję Create -> Device Update for IoT Hub (Utwórz > aktualizacji urządzenia dla IoT Hub

4. Określanie subskrypcji platformy Azure, która ma zostać skojarzona z kontem aktualizacji urządzenia i grupą zasobów

5. Określanie nazwy i lokalizacji dla konta aktualizacji urządzenia

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły konta." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Możesz przejść do strony Produkty platformy Azure według [regionów,](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) aby odnaleźć regiony, w których jest dostępna IoT Hub aktualizacji urządzenia. Jeśli aktualizacja urządzenia IoT Hub nie jest dostępna w Twoim regionie, możesz utworzyć konto w dostępnym regionie, który znajduje się najbliżej Ciebie. 

6. Kliknij pozycję "Dalej: Przeglądanie + tworzenie>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Zrzut ekranu przedstawiający przegląd szczegółów konta." lightbox="media/create-device-update-account/account-review.png":::

7. Przejrzyj szczegóły, a następnie wybierz pozycję "Utwórz". Zobaczysz, że wdrożenie jest w toku. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Zrzut ekranu przedstawiający wdrażanie konta w toku." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. W ciągu kilku minut stan wdrożenia zmieni się na "ukończony". Kliknij pozycję "Przejdź do zasobu"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Zrzut ekranu przedstawiający ukończenie wdrażania konta." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Tworzenie wystąpienia aktualizacji urządzenia 

Wystąpienie aktualizacji urządzenia jest skojarzone z pojedynczym centrum IoT. Wybierz centrum IoT, które będzie używane z aktualizacją urządzenia. W tym kroku utworzymy nowe zasady dostępu współdzielonych, aby upewnić się, że aktualizacja urządzenia używa tylko wymaganych uprawnień do pracy z usługą IoT Hub (zapis w rejestrze i połączenie z usługą). Te zasady gwarantują, że dostęp jest ograniczony tylko do aktualizacji urządzenia.

Aby utworzyć wystąpienie aktualizacji urządzenia po utworzeniu konta.

1. Gdy znajdziesz się w nowo utworzonym zasobie konta, przejdź do bloku "Wystąpienia" zarządzania wystąpieniami

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Zrzut ekranu przedstawiający zarządzanie wystąpieniami w ramach konta." lightbox="media/create-device-update-account/instance-blade.png":::

2. Kliknij pozycję "Utwórz i określ nazwę wystąpienia, a następnie wybierz IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły wystąpienia." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Link IoT Hub do zasobu aktualizacji urządzenia nie musi być w tym samym regionie co konto aktualizacji urządzenia. Jednak w celu lepszej wydajności zaleca się, aby IoT Hub w regionie takim samym jak region lub w pobliżu regionu konta aktualizacji urządzenia. 

3. Kliknij pozycję „Utwórz”. Wystąpienie zostanie wyświetlony w stanie "Tworzenie". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Zrzut ekranu przedstawiający tworzenie wystąpienia." lightbox="media/create-device-update-account/instance-creating.png":::

4. Na ukończenie wdrożenia wystąpienia należy odzwyczyć 5–10 minut. Odśwież stan do momentu, gdy stan "Stan aprowowania" zostanie zamieniony na "Powodzenie".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Zrzut ekranu przedstawiający powodzenie tworzenia wystąpienia." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Konfigurowanie IoT Hub 

Aby aktualizacja urządzenia odbierała powiadomienia o zmianie z usługi IoT Hub, aktualizacja urządzenia integruje się z centrum zdarzeń "wbudowane". Kliknięcie przycisku "IoT Hub" umożliwia skonfigurowanie wymaganych tras komunikatów i zasad dostępu wymaganych do komunikowania się z urządzeniami IoT. 

Aby skonfigurować IoT Hub

1. Gdy stan wystąpienia "Provisioning State" zmieni się na "Succeeded" (Powodzenie), wybierz wystąpienie w bloku Zarządzanie wystąpieniami. Kliknij pozycję "Configure IoT Hub" (Konfiguruj IoT Hub).

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Zrzut ekranu przedstawiający IoT Hub dla wystąpienia." lightbox="media/create-device-update-account/instance-configure.png":::

2. Wybierz pozycję "Zgadzam się, aby wprowadzić te zmiany"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Zrzut ekranu przedstawiający wyrażanie zgody na IoT Hub dla wystąpienia." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Kliknij pozycję "Aktualizuj"

   > [!NOTE] 
   > Jeśli używasz warstwy Bezpłatna usługi Azure IoT Hub, dozwolona liczba tras komunikatów jest ograniczona do 5. Aktualizacja urządzenia dla IoT Hub musi skonfigurować 4 trasy komunikatów, aby działały zgodnie z oczekiwaniami. 

[Dowiedz się więcej o skonfigurowanych trasach komunikatów.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Konfigurowanie ról kontroli dostępu

Aby inni użytkownicy mieli dostęp do aktualizacji urządzenia, użytkownicy muszą mieć prawa dostępu do tego zasobu. 

1. Przejdź do kontroli dostępu (IAM) na koncie aktualizacji urządzenia

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Zrzut ekranu przedstawiający kontrolę dostępu na koncie aktualizacji urządzenia." lightbox="media/create-device-update-account/account-access-control.png":::

2. Kliknij pozycję "Dodaj przypisania ról"

3. W obszarze "Wybierz rolę" wybierz rolę Aktualizacja urządzenia z podanych opcji
     - Administrator aktualizacji urządzenia
     - Czytnik aktualizacji urządzeń
     - Administrator zawartości aktualizacji urządzenia
     - Czytnik zawartości aktualizacji urządzenia
     - Administrator wdrożeń aktualizacji urządzeń
     - Czytelnik wdrożeń aktualizacji urządzeń
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Zrzut ekranu przedstawiający przypisania ról kontroli dostępu w ramach konta aktualizacji urządzenia." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Dowiedz się więcej o kontroli dostępu opartej na rolach w aktualizacji urządzenia dla IoT Hub](device-update-control-access.md) 
    
4. Przypisywanie dostępu do użytkownika lub grupy usługi Azure AD
5. Klikanie pozycji Zapisz.
6. Teraz możesz używać funkcji aktualizacji urządzenia z poziomu IoT Hub

## <a name="next-steps"></a>Następne kroki

Spróbuj zaktualizować urządzenie przy użyciu jednego z następujących szybkich samouczków:

 - [Aktualizacja urządzenia w symulatorze](device-update-simulator.md)
 - [Aktualizacja urządzenia na urządzeniu Raspberry Pi](device-update-raspberry-pi.md)
 - [Aktualizacja urządzenia w agencie pakietów Ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

[Dowiedz się więcej o koncie i wystąpieniu aktualizacji urządzenia.](device-update-resources.md) 

[Dowiedz się więcej o rolach kontroli dostępu do aktualizacji urządzeń. ](device-update-control-access.md) 

