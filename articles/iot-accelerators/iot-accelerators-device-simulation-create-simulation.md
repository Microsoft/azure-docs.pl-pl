---
title: Tworzenie nowej symulacji usługi IoT — Azure | Microsoft Docs
description: W tym samouczku użyjesz symulacji urządzenia do utworzenia i uruchomienia nowej symulacji.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: e0d7310c4863c8dd431b991a2c249f2d8e257aeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852357"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Samouczek: tworzenie i uruchamianie symulacji urządzenia usługi IoT

W tym samouczku użyjesz symulacji urządzenia do utworzenia i uruchomienia nowej symulacji usługi IoT, która będzie korzystać z co najmniej jednego urządzenia symulowanego.

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Wyświetlanie wszystkich symulacji aktywnych i historycznych
> * Tworzenie i uruchamianie nowej symulacji
> * Wyświetlanie metryk symulacji
> * Zatrzymywanie symulacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka będzie potrzebne wdrożone wystąpienie symulacji urządzenia w subskrypcji platformy Azure.

Jeśli nie wdrożono jeszcze symulacji urządzenia, zobacz [wdrażanie symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) w serwisie GitHub.

## <a name="view-simulations"></a>Wyświetlanie symulacji

Wybierz pozycję **Symulacje** na pasku menu. Strona **Symulacje** zawiera informacje na temat wszystkich dostępnych symulacji. Na tej stronie można zobaczyć symulacje działające w danej chwili oraz symulacje uruchomione wcześniej. Aby ponownie uruchomić poprzednią symulację, kliknij kafelek symulacji w celu otwarcia jej szczegółów:

![Symulacje](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Tworzenie symulacji

Aby utworzyć symulację, kliknij pozycję **+ Nowa symulacja** w prawym górnym rogu.

Symulacja składa się z co najmniej jednego modelu urządzenia. Model urządzenia definiuje zachowanie, telemetrię i format wiadomości komunikatów urządzenia, które ma być symulowane.

Aby dodać model urządzenia, kliknij pozycję **+ Dodaj typ urządzenia** i z listy wybierz model urządzenia. Symulacja może obejmować więcej niż jeden model urządzenia. Każdy model urządzenia może mieć inną liczbę urządzeń i częstotliwość komunikatów.

Po ukończeniu formularza nowej symulacji kliknij pozycję **Rozpocznij symulację**, aby uruchomić symulację.

W zależności od liczby urządzeń symulowanych skonfigurowanie i uruchomienie symulacji może potrwać kilka minut:

![Nowa symulacja](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Zatrzymywanie symulacji

Po kliknięciu pozycji **Rozpocznij symulację** zostanie wyświetlona strona szczegółów symulacji. Ta strona szczegółów zawiera statystyki i metryki symulacji pobierane na żywo z usługi IoT Hub. Możesz także przejść do tej strony szczegółów, klikając kafelek symulacji na stronie **Symulacje**.

Aby zakończyć symulację, kliknij pozycję **Zatrzymaj symulację** na pasku akcji w prawym górnym rogu.

![Zatrzymywanie symulacji](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia, uruchamiania i zatrzymywania symulacji. Pokazano również, jak wyświetlać szczegóły symulacji. Aby dowiedzieć się więcej na temat sposobu uruchamiania symulacji, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Tworzenie niestandardowego symulowanego urządzenia](iot-accelerators-device-simulation-create-custom-device.md)
