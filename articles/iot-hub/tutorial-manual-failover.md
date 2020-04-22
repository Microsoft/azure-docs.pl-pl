---
title: Ręczne przechodzenie w tryb failover w centrum Azure IoT Hub | Microsoft Docs
description: Dowiedz się, jak wykonać ręczną pracę awaryjną centrum IoT hub do innego regionu i potwierdzić, że działa, a następnie zwrócić go do oryginalnego regionu i sprawdzić go ponownie.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 86b39beb2958194f7c86409c5d78992616234b05
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769910"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Samouczek: Wykonywanie ręcznego trybu failover dla centrum IoT

Ręczne przechodzenie w tryb failover to funkcja usługi IoT Hub, która umożliwia klientom [przełączanie w tryb failover](https://en.wikipedia.org/wiki/Failover) operacji centrum z regionu podstawowego do odpowiedniego sparowanego geograficznie regionu świadczenia usługi Azure. Ręczne przejście w tryb failover można wykonać w przypadku awarii regionalnej lub dłuższej awarii usługi. Można też wykonać planowane przejście w tryb failover, aby przetestować możliwości odzyskiwania po awarii, mimo że zalecamy użycie testowego centrum IoT Hub zamiast centrum w środowisku produkcyjnym. Funkcja ręcznego przechodzenia w tryb failover jest oferowana klientom bez dodatkowych kosztów.

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
> * Utwórz centrum IoT Hub przy użyciu witryny Azure Portal. 
> * Wykonaj przejście w tryb failover. 
> * Centrum zostanie uruchomione w lokalizacji dodatkowej.
> * Wykonaj powrót po awarii, aby przywrócić operacje centrum IoT Hub w lokalizacji podstawowej. 
> * Upewnij się, że centrum działa poprawnie we właściwej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 

2. Kliknij pozycję **+ Utwórz zasób**, wybierz pozycję **Internet rzeczy**, a następnie pozycję **IoT Hub**.

   ![Zrzut ekranu przedstawiający tworzenie centrum IoT Hub](./media/tutorial-manual-failover/create-hub-01.png)

3. Wybierz kartę **Podstawy.** Wypełnij następujące pola.

    **Subskrypcja**: wybierz subskrypcję platformy Azure, której chcesz użyć.

    **Grupa zasobów**: kliknij pozycję **Utwórz nową** i wprowadź **ManlFailRG** jako nazwę grupy zasobów.

    **Region**: wybierz region blisko ciebie. W tym samouczku użyto regionu `West US 2`. Przejście w tryb failover można wykonać tylko między sparowanymi geograficznie regionami świadczenia usługi Azure. Region połączony geograficznie z zachodnimi stanami USA 2 to WestCentralUS.
    
   **Nazwa centrum IoT Hub**:podaj nazwę centrum IoT Hub. Nazwa centrum musi być unikatowa w skali globalnej. 

   ![Zrzut ekranu przedstawiający okienko Podstawowe umożliwiające utworzenie centrum IoT Hub](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Kliknij pozycję **Przegląd + utwórz**. Zostaną użyte domyślne ustawienia rozmiaru i skali. 

4. Przejrzyj informacje, a następnie kliknij pozycję **Utwórz**, aby utworzyć centrum IoT Hub. 

   ![Zrzut ekranu przedstawiający ostatni etap tworzenia centrum IoT Hub](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Ręczne przechodzenie w tryb failover

Istnieje limit dwóch przejść w tryb failover i dwóch powrotów po awarii dziennie w przypadku centrum IoT Hub.

1. Kliknij pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów **ManlFailRG**. Kliknij centrum na liście zasobów. 

1. W obszarze **Ustawienia** w okienku Centrum IoT kliknij pozycję **Przetwórda w pracy awaryjnej**.

   ![Zrzut ekranu przedstawiający okienko właściwości centrum IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

1. W okienku Ręczne tryb failover zobaczysz **bieżącą lokalizację** i **lokalizację trybu failover**. Bieżąca lokalizacja zawsze wskazuje lokalizację, w której koncentrator jest aktualnie aktywny. Lokalizacja trybu failover jest standardowym [regionem platformy Azure sparowanym geograficznie,](../best-practices-availability-paired-regions.md) który jest sparowany z bieżącą lokalizacją. Nie można zmienić wartości lokalizacji. W tym samouczku bieżąca lokalizacja jest `West US 2` `West Central US`i lokalizacja pracy awaryjnej jest .

   ![Zrzut ekranu przedstawiający okienko ręcznego przejścia w tryb failover](./media/tutorial-manual-failover/trigger-failover-02.png)

1. U góry okienka Ręczne tryb failover kliknij pozycję **Rozpocznij pracę awaryjną**. 

1. W okienku potwierdzenia wpisz nazwę centrum IoT hub, aby potwierdzić, że jest to ta, którą chcesz przewinąć w błąd. Następnie, aby zainicjować przetwórz pracy awaryjnej, kliknij przycisk **Failover**.

   Ilość czasu potrzebnego do ręcznego przejścia w tryb failover jest proporcjonalna do liczby urządzeń zarejestrowanych w centrum. Jeśli na przykład masz 100 000 urządzeń, może to potrwać 15 minut, ale jeśli masz pięć milionów urządzeń, może to potrwać godzinę lub dłużej.

   ![Zrzut ekranu przedstawiający okienko ręcznego przejścia w tryb failover](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Gdy proces ręcznego pracy awaryjnej jest uruchomiony, baner pojawia się powiedzieć, że ręczne przejście awaryjne jest w toku. 

   ![Zrzut ekranu przedstawiający trwające ręczne przechodzenie w tryb failover](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Jeśli zamkniesz okienko Centrum IoT i otworzysz je ponownie, klikając go w okienku Grupy zasobów, zostanie wyświetlony baner informujący o tym, że koncentrator znajduje się w środku ręcznego trybu failover. 

   ![Zrzut ekranu przedstawiający przewijanie awaryjne usługi IoT Hub w toku](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Po jego zakończeniu bieżące i trybu failover regiony na stronie Ręczne tryb failover są przerzucane i koncentrator jest ponownie aktywny. W tym przykładzie bieżąca `WestCentralUS` lokalizacja jest teraz `West US 2`i lokalizacja pracy awaryjnej jest teraz . 

   ![Zrzut ekranu przedstawiający ukończone przejście w tryb failover](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Na stronie przeglądu znajduje się również baner wskazujący, że przewijanie awaryjne i centrum IoT hub jest uruchomiony w `West Central US`.

   ![Zrzut ekranu przedstawiający przełączenie w pełni w pełni jest na stronie przeglądu](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii 

Po ręcznym przejściu w tryb failover można przełączyć operacje centrum ponownie do oryginalnego regionu podstawowego — taka operacja jest nazywana powrotem po awarii. Jeśli właśnie wykonano przejście w tryb failover, zaczekaj około godziny przed zażądaniem powrotu po awarii. Jeśli spróbujesz wykonać powrót po awarii wcześniej, zostanie wyświetlony komunikat o błędzie.

Powrót po awarii przebiega podobnie do ręcznego przejścia w tryb failover. Poniżej przedstawiono procedurę: 

1. Aby wykonać powrót po awarii, wróć do centrum IoT Hub w okienku usługi IoT Hub.

2. W obszarze **Ustawienia** w okienku Centrum IoT kliknij pozycję **Przetwórda w pracy awaryjnej**. 

3. U góry okienka Ręczne tryb failover kliknij pozycję **Rozpocznij pracę awaryjną**. 

4. W okienku potwierdzenia wpisz nazwę centrum IoT hub, aby potwierdzić, że jest to ten, który chcesz powiększyć po awarii. Aby zainicjować powrót po awarii, kliknij przycisk OK. 

   ![Zrzut ekranu przedstawiający żądanie ręcznego powrotu po awarii](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Zostaną wyświetlone banery opisane w sekcji przechodzenia w tryb failover. Po zakończeniu powrotu po awarii `West US 2` ponownie jest wyświetlany `West Central US` jako bieżącej lokalizacji i jako lokalizacji pracy awaryjnej, jak ustawiono pierwotnie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Aby usunąć zasoby utworzone w ramach tego samouczka, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT Hub i samej grupy zasobów. 

1. Kliknij pozycję **Grupy zasobów**. 

2. Znajdź i wybierz grupę zasobów **ManlFailRG**. Kliknij je, aby je otworzyć. 

3. Kliknij pozycję **Usuń grupę zasobów**. Po wyświetleniu monitu wprowadź nazwę grupy zasobów i kliknij przycisk **Usuń**, aby potwierdzić. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania i wykonywania ręcznego przejścia w tryb failover oraz żądania powrotu po awarii przez wykonanie następujących zadań:

> [!div class="checklist"]
> * Utwórz centrum IoT Hub przy użyciu witryny Azure Portal. 
> * Wykonaj przejście w tryb failover. 
> * Centrum zostanie uruchomione w lokalizacji dodatkowej.
> * Wykonaj powrót po awarii, aby przywrócić operacje centrum IoT Hub w lokalizacji podstawowej. 
> * Upewnij się, że centrum działa poprawnie we właściwej lokalizacji.

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Zarządzanie stanem urządzenia IoT](tutorial-device-twins.md)
