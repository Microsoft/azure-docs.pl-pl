---
title: Samouczek — ręczne przełączenie w tryb failover usługi Azure IoT Hub | Microsoft Docs
description: Samouczek — informacje na temat ręcznego przełączania do trybu failover Centrum IoT Hub w innym regionie i potwierdzenia jego działania, a następnie powrotu do oryginalnego regionu i ponownego zaewidencjonowania.
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
ms.openlocfilehash: 3b6bc972f5c26c78ffff81f5bab8c2812cf2cb11
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622916"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Samouczek: Wykonywanie ręcznego przełączania do trybu failover dla Centrum IoT Hub

Ręczne przechodzenie w tryb failover to funkcja usługi IoT Hub, która umożliwia klientom [przełączanie w tryb failover](https://en.wikipedia.org/wiki/Failover) operacji centrum z regionu podstawowego do odpowiedniego sparowanego geograficznie regionu świadczenia usługi Azure. Ręczne przejście w tryb failover można wykonać w przypadku awarii regionalnej lub dłuższej awarii usługi. Można też wykonać planowane przejście w tryb failover, aby przetestować możliwości odzyskiwania po awarii, mimo że zalecamy użycie testowego centrum IoT Hub zamiast centrum w środowisku produkcyjnym. Funkcja ręcznego przełączania do trybu failover jest oferowana klientom bez dodatkowych kosztów dla centrów IoT utworzonych po 18 maja 2017.

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
> * Utwórz centrum IoT Hub przy użyciu witryny Azure Portal. 
> * Wykonaj przejście w tryb failover. 
> * Centrum zostanie uruchomione w lokalizacji dodatkowej.
> * Wykonaj powrót po awarii, aby przywrócić operacje centrum IoT Hub w lokalizacji podstawowej. 
> * Upewnij się, że centrum działa poprawnie we właściwej lokalizacji.

Aby uzyskać więcej informacji na temat ręcznej pracy awaryjnej i zainicjowanej przez firmę Microsoft trybu failover z IoT Hub, zobacz [odzyskiwanie po awarii między regionami](iot-hub-ha-dr.md#cross-region-dr).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym samouczku używa protokołu MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 

2. Kliknij pozycję **+ Utwórz zasób**, wybierz pozycję **Internet rzeczy**, a następnie pozycję **IoT Hub**.

   ![Zrzut ekranu przedstawiający tworzenie centrum IoT Hub](./media/tutorial-manual-failover/create-hub-01.png)

3. Wybierz kartę **podstawy** . Wypełnij poniższe pola.

    **Subskrypcja**: wybierz subskrypcję platformy Azure, której chcesz użyć.

    **Grupa zasobów**: kliknij pozycję **Utwórz nową** i wprowadź **ManlFailRG** jako nazwę grupy zasobów.

    **Region**: zaznacz region blisko siebie. W tym samouczku użyto regionu `West US 2`. Przejście w tryb failover można wykonać tylko między sparowanymi geograficznie regionami świadczenia usługi Azure. Region geograficznie sparowany z zachodnim stanem USA 2 to WestCentralUS.
    
   **Nazwa centrum IoT Hub**:podaj nazwę centrum IoT Hub. Nazwa centrum musi być unikatowa w skali globalnej. 

   ![Zrzut ekranu przedstawiający okienko Podstawowe umożliwiające utworzenie centrum IoT Hub](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Kliknij pozycję **Przejrzyj i utwórz**. Zostaną użyte domyślne ustawienia rozmiaru i skali. 

4. Przejrzyj informacje, a następnie kliknij pozycję **Utwórz**, aby utworzyć centrum IoT Hub. 

   ![Zrzut ekranu przedstawiający ostatni etap tworzenia centrum IoT Hub](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Ręczne przechodzenie w tryb failover

Istnieje limit dwóch przejść w tryb failover i dwóch powrotów po awarii dziennie w przypadku centrum IoT Hub.

1. Kliknij pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów **ManlFailRG**. Kliknij centrum na liście zasobów. 

1. W obszarze **Ustawienia** w okienku IoT Hub kliknij pozycję **tryb failover**.

   ![Zrzut ekranu przedstawiający okienko właściwości centrum IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

1. W okienku ręczny tryb failover zobaczysz **bieżącą lokalizację** i **lokalizację trybu failover**. Bieżąca lokalizacja zawsze wskazuje lokalizację, w której centrum jest obecnie aktywne. Lokalizacja trybu failover to standardowy [region z parowaniem geograficznym platformy Azure](../best-practices-availability-paired-regions.md) , który jest sparowany z bieżącą lokalizacją. Nie można zmienić wartości lokalizacji. W tym samouczku bieżąca lokalizacja jest `West US 2` i lokalizacją trybu failover `West Central US` .

   ![Zrzut ekranu przedstawiający okienko ręcznego przejścia w tryb failover](./media/tutorial-manual-failover/trigger-failover-02.png)

1. W górnej części okienka ręczny tryb failover kliknij pozycję **Uruchom tryb failover**. 

1. W okienku potwierdzenia Podaj nazwę Centrum IoT Hub, aby potwierdzić, że jest on przeznaczony do pracy w trybie failover. Następnie, aby zainicjować tryb failover, kliknij pozycję **tryb failover**.

   Ilość czasu potrzebnego do ręcznego przejścia w tryb failover jest proporcjonalna do liczby urządzeń zarejestrowanych w centrum. Jeśli na przykład masz 100 000 urządzeń, może to potrwać 15 minut, ale jeśli masz pięć milionów urządzeń, może to potrwać godzinę lub dłużej.

   ![Zrzut ekranu przedstawiający okienko potwierdzenia ręcznego przełączania do trybu failover](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Podczas ręcznego działania trybu failover jest wyświetlany transparent informujący o ręcznym przejściu w tryb failover. 

   ![Zrzut ekranu przedstawiający trwające ręczne przechodzenie w tryb failover](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Jeśli zamkniesz okienko IoT Hub i otworzysz je ponownie, klikając je w okienku Grupa zasobów, zobaczysz transparent informujący o tym, że centrum jest w trakcie ręcznego przełączania do trybu failover. 

   ![Zrzut ekranu przedstawiający IoT Hub tryb failover w toku](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Po zakończeniu bieżące regiony i przełączenia w tryb failover na stronie ręczny tryb failover są przerzucane, a centrum jest ponownie aktywne. W tym przykładzie bieżąca lokalizacja jest teraz `WestCentralUS` i lokalizacją trybu failover jest teraz `West US 2` . 

   ![Zrzut ekranu przedstawiający ukończone przejście w tryb failover](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Na stronie Przegląd jest również wyświetlany transparent wskazujący, że Ukończono pracę w trybie failover, a IoT Hub działa w systemie `West Central US` .

   ![Zrzut ekranu przedstawiający tryb failover jest zakończony na stronie Przegląd](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii 

Po ręcznym przejściu w tryb failover można przełączyć operacje centrum ponownie do oryginalnego regionu podstawowego — taka operacja jest nazywana powrotem po awarii. Jeśli właśnie wykonano przejście w tryb failover, zaczekaj około godziny przed zażądaniem powrotu po awarii. Jeśli spróbujesz wykonać powrót po awarii wcześniej, zostanie wyświetlony komunikat o błędzie.

Powrót po awarii przebiega podobnie do ręcznego przejścia w tryb failover. Poniżej przedstawiono procedurę: 

1. Aby wykonać powrót po awarii, wróć do centrum IoT Hub w okienku usługi IoT Hub.

2. W obszarze **Ustawienia** w okienku IoT Hub kliknij pozycję **tryb failover**. 

3. W górnej części okienka ręczny tryb failover kliknij pozycję **Uruchom tryb failover**. 

4. W okienku potwierdzenia Podaj nazwę Centrum IoT Hub, aby potwierdzić, że jest to konto, którego chcesz użyć do powrotu po awarii. Aby zainicjować powrót po awarii, kliknij przycisk OK. 

   ![Zrzut ekranu przedstawiający żądanie ręcznego powrotu po awarii](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Zostaną wyświetlone banery opisane w sekcji przechodzenia w tryb failover. Po zakończeniu powrotu po awarii jest on ponownie wyświetlany `West US 2` jako bieżąca lokalizacja oraz `West Central US` jako lokalizacja trybu failover ustawiona jako oryginalna.

## <a name="clean-up-resources"></a>Czyszczenie zasobów 

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
