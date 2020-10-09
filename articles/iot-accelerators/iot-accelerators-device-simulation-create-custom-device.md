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
ms.openlocfilehash: 302b863e7ad7d6df286adf53342356f279ab92d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "61450588"
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

Jeśli nie wdrożono jeszcze symulacji urządzenia, należy wykonać czynności opisane w przewodniku Szybki start [Wdrażanie i uruchamianie symulacji urządzenia usługi IoT na platformie Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Otwieranie symulacji urządzenia

Aby uruchomić symulację urządzenia w przeglądarce, przejdź najpierw do [akceleratorów rozwiązań usługi Microsoft Azure IoT](https://www.azureiotsolutions.com).

Może być konieczne zalogowanie się przy użyciu poświadczeń subskrypcji platformy Azure.

Następnie kliknij pozycję **Uruchom** na kafelku symulacji urządzenia, która została wdrożona w przewodniku Szybki start [Wdrażanie i uruchamianie symulacji urządzenia usługi IoT na platformie Azure](quickstart-device-simulation-deploy.md).

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

![Usuwanie modelu urządzenia](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Usuwanie modelu urządzenia

Każdy niestandardowy model urządzenia można usunąć. Aby usunąć model urządzenia, zaznacz pole wyboru obok modelu, a następnie kliknij pozycję **Usuń** na pasku akcji:

![Usuwanie modelu urządzenia](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia, klonowania i usuwania niestandardowych modeli urządzeń. Aby dowiedzieć się więcej na temat modeli urządzeń, zobacz następujący artykuł z instrukcjami:

> [!div class="nextstepaction"]
> [Utwórz zaawansowany model urządzenia](iot-accelerators-device-simulation-advanced-device.md)