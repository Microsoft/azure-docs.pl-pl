---
title: Definiowanie nowego typu urządzenia bramy w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jako konstruktora, jak zdefiniować nowy typ urządzenia bramy IoT w aplikacji Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2411eab50cc921a09ba55780b3c6620744a78f3f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758134"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Definiowanie nowego typu urządzenia bramy IoT w aplikacji Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jako konstruktor rozwiązań, jak używać szablonu urządzenia bramy do definiowania urządzenia bramy w aplikacji IoT Central. Następnie skonfiguruj kilka urządzeń podrzędnych, które łączą się z aplikacją IoT Central za pośrednictwem urządzenia bramy. 

W tym samouczku utworzysz szablon urządzenia bramy **inteligentnego budynku.** Urządzenie bramy **inteligentnego budynku** ma relacje z innymi urządzeniami podrzędnymi.

![Diagram relacji między urządzeniem bramy a urządzeniami podrzędnymi](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Oprócz umożliwienia urządzeniom niższego szczebla komunikowania się z aplikacją IoT Central, urządzenie bramy może również:

* Wyślij własną dane telemetryczne, takie jak temperatura.
* Odpowiadanie na zapisywalne aktualizacje właściwości wprowadzone przez operatora. Na przykład operator może zamieć interwał wysyłania danych telemetrycznych.
* Odpowiadaj na polecenia, takie jak ponowne uruchamianie urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz [utworzyć aplikację Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Tworzenie szablonów urządzeń podrzędnych

W tym samouczku użyto szablonów urządzeń dla urządzenia **Czujnik S1** i urządzenia **czujnika obłożenia RS40** do generowania symulowanych urządzeń podrzędnych.

Aby utworzyć szablon urządzenia dla urządzenia **Czujnik S1:**

1. W lewym okienku wybierz pozycję **Szablony urządzeń**. Następnie **+** wybierz, aby rozpocząć dodawanie szablonu.

1. Przewiń w dół, aż zobaczysz kafelek urządzenia **Czujnik S1.** Zaznacz kafelek, a następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Recenzja** wybierz pozycję **Utwórz,** aby dodać szablon urządzenia do aplikacji. 

Aby utworzyć szablon urządzenia dla ***RS40 Czujnik obłożenia** urządzenia:

1. W lewym okienku wybierz pozycję **Szablony urządzeń**. Następnie **+** wybierz, aby rozpocząć dodawanie szablonu.

1. Przewiń w dół, aż zobaczysz kafelek urządzenia ***RS40 Czujnik obłożenia.** Zaznacz kafelek, a następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Recenzja** wybierz pozycję **Utwórz,** aby dodać szablon urządzenia do aplikacji. 

Masz teraz szablony urządzeń dla dwóch typów urządzeń podrzędnych:

![Szablony urządzeń dla urządzeń podrzędnych](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Tworzenie szablonu urządzenia bramy

W tym samouczku można utworzyć szablon urządzenia dla urządzenia bramy od podstaw. Ten szablon służy później do tworzenia symulowanego urządzenia bramy w aplikacji.

Aby dodać nowy szablon urządzenia bramy do aplikacji:

1. W lewym okienku wybierz pozycję **Szablony urządzeń**. Następnie **+** wybierz, aby rozpocząć dodawanie szablonu.

1. Na stronie **Wybierz typ szablonu** wybierz kafelek **Urządzenie IoT,** a następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Dostosowywanie urządzenia** zaznacz pole wyboru **Urządzenie bramy.**

1. Na stronie **Recenzja** wybierz pozycję **Utwórz**. 

1. Wprowadź **urządzenie bramy inteligentnego budynku** jako nazwę szablonu.

1. Na stronie **Tworzenie modelu możliwości** wybierz kafelek **Niestandardowy.**

1. Wybierz, **+** aby dodać interfejs.  Wybierz **standardowy** interfejs Informacje o urządzeniu.

### <a name="add-relationships"></a>Dodawanie relacji

Następnie dodajesz relacje do szablonów szablonów urządzeń podrzędnych:

1. W szablonie **urządzenia bramy inteligentnego budynku** wybierz pozycję **Relacje**.

1. Wybierz **+ Dodaj relację**. Wprowadź **czujnik środowiska** jako nazwę wyświetlaną i wybierz czujnik **S1** jako cel.

1. Wybierz **ponownie pozycję + Dodaj relację.** Wprowadź **czujnik obłożenia** jako nazwę wyświetlaną i wybierz **RS40 Czujnik obłożenia** jako cel.

1. Wybierz pozycję **Zapisz**.

![Szablon urządzenia bramy inteligentnego budynku, przedstawiający relacje](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia bramy może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do urządzenia ani ich odbierane.

Aby dodać właściwości chmury do szablonu **urządzenia bramy inteligentnego budynku.**

1. W szablonie **urządzenia bramy inteligentnego budynku** wybierz pozycję Właściwości **chmury**.

1.  Użyj informacji w poniższej tabeli, aby dodać dwie właściwości chmury do szablonu urządzenia bramy.

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Date   |
    | Nazwa klienta     | Brak          | Ciąg |

2. Wybierz pozycję **Zapisz**.

### <a name="create-views"></a>Tworzenie widoków

Jako konstruktor możesz dostosować aplikację, aby wyświetlić odpowiednie informacje o urządzeniu czujnika środowiska operatorowi. Dostosowania umożliwiają operatorowi zarządzanie urządzeniami czujników środowiskowych podłączonych do aplikacji. Można utworzyć dwa typy widoków dla operatora do interakcji z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń.

Aby wygenerować widoki domyślne dla szablonu **urządzenia bramy inteligentnego budynku:**

1. W szablonie **urządzenia bramy inteligentnego budynku** wybierz pozycję **Widoki**.

1. Wybierz **polecenie Generuj widoki domyślne** i upewnij się, że wszystkie opcje są zaznaczone.

1. Wybierz **pozycję Generuj domyślny widok pulpitu nawigacyjnego**.

## <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Przed utworzeniem symulowanego urządzenia bramy lub podłączeniem rzeczywistego urządzenia bramy należy opublikować szablon urządzenia.

Aby opublikować szablon urządzenia bramy:

1. Wybierz szablon **urządzenia bramy inteligentnego budynku** na stronie **Szablony urządzeń.**

2. Wybierz pozycję **Publikuj**.

3. W oknie **dialogowym Publikowanie szablonu urządzenia** wybierz pozycję **Publikuj**.

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **Urządzenia** i dla operatora. W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez utworzenia nowej wersji. Można jednak wprowadzać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia. Te aktualizacje nie powodują tworzenia nowej wersji. Po wyrobieniu jakichkolwiek zmian wybierz **pozycję Publikuj,** aby wypchnąć te zmiany do operatora.

## <a name="create-the-simulated-devices"></a>Tworzenie symulowanych urządzeń

W tym samouczku użyto symulowanych urządzeń podrzędnych i symulowanego urządzenia bramy.

Aby utworzyć symulowane urządzenie bramy:

1. Na stronie **Urządzenia** wybierz **pozycję Inteligentne urządzenie bramy budynku** na liście szablonów urządzeń.

1. Wybierz, **+** aby rozpocząć dodawanie nowego urządzenia.

1. Zachowaj **wygenerowany identyfikator urządzenia** i **nazwę urządzenia**. Upewnij się, że przełącznik **symulowane** jest **włączony**. Wybierz pozycję **Utwórz**.

Aby utworzyć symulowane urządzenia podrzędne:

1. Na stronie **Urządzenia** wybierz pozycję **Czujnik obłożenia RS40** na liście szablonów urządzeń.

1. Wybierz, **+** aby rozpocząć dodawanie nowego urządzenia.

1. Zachowaj **wygenerowany identyfikator urządzenia** i **nazwę urządzenia**. Upewnij się, że przełącznik **symulowane** jest **włączony**. Wybierz pozycję **Utwórz**.

1. Na stronie **Urządzenia** wybierz **pozycję Czujnik S1** na liście szablonów urządzeń.

1. Wybierz, **+** aby rozpocząć dodawanie nowego urządzenia.

1. Zachowaj **wygenerowany identyfikator urządzenia** i **nazwę urządzenia**. Upewnij się, że przełącznik **symulowane** jest **włączony**. Wybierz pozycję **Utwórz**.

![Symulowane urządzenia w aplikacji](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Dodawanie relacji podrzędnych urządzeń do urządzenia bramy

Teraz, gdy masz symulowane urządzenia w aplikacji, można utworzyć relacje między urządzeniami podrzędnymi a urządzeniem bramy:

1. Na stronie **Urządzenia** wybierz **pozycję Czujnik S1** na liście szablonów urządzeń, a następnie wybierz symulowane urządzenie Czujnik **S1.**

1. Wybierz **pozycję Połącz z bramą**.

1. W oknie **dialogowym Łączenie z bramą** wybierz szablon **urządzenia bramy inteligentnego budynku,** a następnie wybierz symulowane wystąpienie utworzone wcześniej.

1. Wybierz **pozycję Dołącz**.

1. Na stronie **Urządzenia** wybierz **RS40 Czujnik obłożenia** na liście szablonów urządzeń, a następnie wybierz symulowane urządzenie **czujnika obłożenia RS40.**

1. Wybierz **pozycję Połącz z bramą**.

1. W oknie **dialogowym Łączenie z bramą** wybierz szablon **urządzenia bramy inteligentnego budynku,** a następnie wybierz symulowane wystąpienie utworzone wcześniej.

1. Wybierz **pozycję Dołącz**.

Oba symulowane urządzenia podrzędne są teraz połączone z symulowanym urządzeniem bramy. Jeśli przejdziesz do widoku **Urządzenia podrzędne** dla urządzenia bramy, zobaczysz powiązane urządzenia podrzędne:

![Widok urządzeń podrzędnych](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Wybierz szablon urządzenia bramy i wystąpienie urządzenia bramy, a następnie wybierz pozycję **Dołącz**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Utwórz nową bramę IoT jako szablon urządzenia.
* Tworzenie właściwości chmury.
* Tworzenie dostosowań.
* Zdefiniuj wizualizację danych telemetrycznych urządzenia.
* Dodawanie relacji.
* Opublikuj szablon urządzenia.

> [!NOTE]
> Generowanie kodu opartego na kodzie VS nie jest obecnie obsługiwane dla urządzeń bramy modelowanych w centrum IoT.

Następnie, jako programista urządzeń, możesz dowiedzieć się, jak:

> [!div class="nextstepaction"]
> [Dodawanie urządzenia usługi Azure IoT Edge do aplikacji Azure IoT Central](tutorial-add-edge-as-leaf-device.md)
