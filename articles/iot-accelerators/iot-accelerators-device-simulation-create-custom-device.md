---
title: Tworzenie niestandardowego urządzenia symulowanego — Azure | Microsoft Docs
description: W tym samouczku użyjesz symulacji urządzenia w celu tworzenia niestandardowego urządzenia symulowanego do użycia w symulacjach.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 1fe86aef832223a7485036343b4b12d8bb526e06
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852391"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Samouczek: tworzenie niestandardowego urządzenia symulowanego

W tym samouczku użyjesz symulacji urządzenia w celu tworzenia niestandardowego urządzenia symulowanego do użycia w symulacjach. Aby rozpocząć pracę z symulacją urządzenia, możesz użyć jednego z uwzględnionych przykładowych urządzeń symulowanych. Możesz również utworzyć niestandardowe urządzenie symulowane zgodnie z opisem w tym artykule. Aby uzyskać więcej opcji dostosowywania, zobacz [Create an advanced device model (Tworzenie zaawansowanego modelu urządzenia)](iot-accelerators-device-simulation-advanced-device.md).

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Wyświetlanie listy modeli urządzeń symulowanych
> * Tworzenie niestandardowego urządzenia symulowanego
> * Klonowanie modelu urządzenia
> * Usuwanie modelu urządzenia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka będzie potrzebne wdrożone wystąpienie symulacji urządzenia w subskrypcji platformy Azure.

Jeśli nie wdrożono jeszcze symulacji urządzenia, zobacz [wdrażanie symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) w serwisie GitHub.

## <a name="view-your-device-models"></a>Wyświetlanie modeli urządzeń

Na pasku narzędzi wybierz pozycję **Modele urządzeń**. Strona **Modele urządzeń** zawiera listę wszystkich modeli urządzeń dostępnych w danym wystąpieniu symulacji urządzenia:

![Modele urządzeń](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Tworzenie modelu urządzenia

Kliknij pozycję **+ Dodaj modele urządzeń** w prawym górnym rogu strony:

![Dodawanie modelu urządzenia](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

W tym samouczku utworzysz symulowaną lodówkę, która wysyła dane temperatury i wilgotności.

Wypełnij formularz, używając poniższych informacji:

| Ustawienie             | Wartość                                                |
| ------------------- | ---------------------------------------------------- |
| Nazwa modelu urządzenia   | Lodówka                                         |
| Opis modelu   | Lodówka z czujnikami temperatury i wilgotności |
| Wersja             | 1.0                                                  |

> [!NOTE]
> Nazwa modelu urządzenia musi być unikatowa.

Kliknij pozycję **+ Dodaj punkt danych**, aby dodać punkty danych na potrzeby temperatury i wilgotności z następującymi wartościami:

| Punkt danych          | Zachowanie        | Wartość minimalna | Wartość maksymalna | Jednostka |
| ------------------- | --------------- | --------- | --------- | ---- |
| Temperatura         | Losowe          | -50       | 100       | F    |
| Wilgotność            | Losowe          | 0         | 100       | %    |

Kliknij pozycję **Zapisz**, aby zapisać model urządzenia.

![Tworzenie modelu urządzenia](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

Lodówka znajduje się teraz na liście modeli urządzeń. Aby przejść na kolejną stronę i zobaczyć swoją lodówkę, być może trzeba będzie kliknąć pozycję **Dalej**.

## <a name="clone-a-device-model"></a>Klonowanie modelu urządzenia

Klonowanie modelu urządzenia umożliwia utworzenie kopii istniejącego modelu urządzenia. Następnie można edytować kopię, aby dostosować ją do własnych potrzeb. Klonowanie pozwala zaoszczędzić czas, jeśli trzeba utworzyć podobne modele urządzeń.

Aby sklonować model urządzenia, zaznacz pole wyboru obok modelu, a następnie kliknij pozycję **Klonuj** na pasku akcji:

![Zrzut ekranu, który podświetla wybrany model i przycisk klonowania.](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Usuwanie modelu urządzenia

Każdy niestandardowy model urządzenia można usunąć. Aby usunąć model urządzenia, zaznacz pole wyboru obok modelu, a następnie kliknij pozycję **Usuń** na pasku akcji:

![Usuwanie modelu urządzenia](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia, klonowania i usuwania niestandardowych modeli urządzeń. Aby dowiedzieć się więcej na temat modeli urządzeń, zobacz następujący artykuł z instrukcjami:

> [!div class="nextstepaction"]
> [Utwórz zaawansowany model urządzenia](iot-accelerators-device-simulation-advanced-device.md)