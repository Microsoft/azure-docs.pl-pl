---
title: Samouczek — Definiowanie nowego typu urządzenia bramy na platformie Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć konstruktora, jak zdefiniować nowy typ urządzenia bramy IoT w aplikacji IoT Central platformy Azure.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e42e1791693342592e391a14422b1441c9cbbd31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832389"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Samouczek — Definiowanie nowego typu urządzenia bramy IoT w aplikacji IoT Central platformy Azure

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak Konstruktor rozwiązań, jak używać szablonu urządzenia bramy do definiowania urządzenia bramy w aplikacji IoT Central. Następnie należy skonfigurować kilka urządzeń podrzędnych, które łączą się z aplikacją IoT Central za pomocą urządzenia bramy. 

W tym samouczku utworzysz szablon urządzenia bramy **inteligentnego kompilowania** . Inteligentne urządzenie bramy do **tworzenia** ma relacje z innymi urządzeniami podrzędnymi.

![Diagram relacji między urządzeniem bramy a urządzeniami podrzędnymi](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Ponadto, aby umożliwić urządzeniom podrzędnym komunikowanie się z aplikacją IoT Central, urządzenie bramy może również:

* Wyślij własne dane telemetryczne, takie jak temperatura.
* Odpowiadanie na zapisywalne aktualizacje właściwości wykonywane przez operatora. Na przykład operator może zmienić interwał wysyłania danych telemetrycznych.
* Odpowiadanie na polecenia, takie jak ponowne uruchamianie urządzenia.

> [!div class="checklist"]
> Tworzenie szablonów urządzeń podrzędnych tworzenie szablonu urządzenia bramy publikowanie szablonu urządzenia tworzenie symulowanych urządzeń

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz [utworzyć aplikację usługi Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Tworzenie szablonów urządzeń podrzędnych

W tym samouczku do generowania symulowanych urządzeń podrzędnych są stosowane szablony urządzeń **czujnika S1** i urządzenie **czujnika zajętości RS40** .

Aby utworzyć szablon urządzenia dla **czujnika S1** :

1. W okienku po lewej stronie wybierz pozycję **Szablony urządzeń**. Następnie wybierz **+** , aby rozpocząć Dodawanie szablonu.

1. Przewiń w dół do momentu wyświetlenia kafelka urządzenia **czujnika S1** . Wybierz kafelek, a następnie wybierz przycisk **Dalej: Dostosuj**.

1. Na stronie **Przegląd** wybierz pozycję **Utwórz** , aby dodać szablon urządzenia do aplikacji. 

Aby utworzyć szablon urządzenia dla urządzenia **czujnika RS40ego** :

1. W okienku po lewej stronie wybierz pozycję **Szablony urządzeń**. Następnie wybierz **+** , aby rozpocząć Dodawanie szablonu.

1. Przewiń w dół do momentu, gdy zobaczysz kafelek dla urządzenia **czujnika RS40** . Wybierz kafelek, a następnie wybierz przycisk **Dalej: Dostosuj**.

1. Na stronie **Przegląd** wybierz pozycję **Utwórz** , aby dodać szablon urządzenia do aplikacji. 

Masz teraz szablony urządzeń dla dwóch typów urządzeń podrzędnych:

![Szablony urządzeń dla urządzeń podrzędnych](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Tworzenie szablonu urządzenia bramy

W tym samouczku utworzysz szablon urządzenia dla urządzenia bramy od podstaw. Ten szablon jest używany później do tworzenia symulowanego urządzenia bramy w aplikacji.

Aby dodać nowy szablon urządzenia bramy do aplikacji:

1. W okienku po lewej stronie wybierz pozycję **Szablony urządzeń**. Następnie wybierz **+** , aby rozpocząć Dodawanie szablonu.

1. Na stronie **Wybieranie typu szablonu** wybierz kafelek **urządzenie IoT** , a następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Dostosowywanie urządzenia** zaznacz pole wyboru **urządzenie bramy** .

1. Na stronie **Przegląd** wybierz pozycję **Utwórz**. 

1. Wprowadź nazwę szablonu **inteligentnego kompilowania urządzenia bramy** .

1. Na stronie **Tworzenie modelu możliwości** wybierz kafelek **niestandardowy** .

1. Wybierz **+** , aby dodać interfejs.  Wybierz standardowy interfejs **informacji o urządzeniu** .

### <a name="add-relationships"></a>Dodaj relacje

Następnie Dodaj relacje do szablonów dla szablonów urządzeń podrzędnych:

1. W szablonie **urządzenia bramy inteligentnego kompilowania** wybierz pozycję **relacje**.

1. Wybierz pozycję **+ Dodaj relację**. Wprowadź **czujnik środowiska** jako nazwę wyświetlaną i wybierz **czujnik S1** jako element docelowy.

1. Ponownie wybierz pozycję **+ Dodaj relację** . Wprowadź **czujnik zajętości** jako nazwę wyświetlaną, a następnie wybierz opcję **czujnik zajętości RS40** jako element docelowy.

1. Wybierz pozycję **Zapisz**.

![Inteligentny szablon urządzenia bramy, wyświetlanie relacji](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia bramy może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia.

Aby dodać właściwości chmury do szablonu **urządzenia do tworzenia inteligentnej bramy** .

1. W szablonie **urządzenia bramy inteligentnego kompilowania** wybierz pozycję **właściwości chmury**.

1.  Skorzystaj z informacji podanych w poniższej tabeli, aby dodać dwie właściwości chmury do szablonu urządzenia bramy.

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Date (Data)   |
    | Nazwa klienta     | Brak          | Ciąg |

2. Wybierz pozycję **Zapisz**.

### <a name="create-views"></a>Tworzenie widoków

Jako Konstruktor można dostosować aplikację tak, aby wyświetlała odpowiednie informacje o urządzeniu czujnika środowiska z operatorem. Twoje dostosowania umożliwiają operatorowi zarządzanie urządzeniami czujnika środowiskowego podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora, który ma być używany do współpracy z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń.

Aby wygenerować domyślne widoki dla szablonu **urządzenia inteligentnej bramy do kompilowania** :

1. W szablonie **urządzenia bramy inteligentnego kompilowania** wybierz opcję **widoki**.

1. Wybierz opcję **Generuj kafelek widoki domyślne** i upewnij się, że wszystkie opcje są zaznaczone.

1. Wybierz pozycję **Generuj domyślne widoki pulpitu nawigacyjnego**.

## <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowane urządzenie bramy lub połączyć rzeczywiste urządzenie bramy, należy opublikować szablon urządzenia.

Aby opublikować szablon urządzenia bramy:

1. Na stronie **Szablony urządzeń** wybierz szablon **inteligentnego budowania urządzenia bramy** .

2. Kliknij pozycję **Opublikuj**.

3. W oknie dialogowym **Publikowanie szablonu urządzenia** wybierz pozycję **Publikuj**.

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** i do operatora. W opublikowanym szablonie urządzenia nie można edytować modelu urządzenia bez tworzenia nowej wersji. Można jednak wykonywać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia. Te aktualizacje nie powodują utworzenia nowej wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj**  , aby wypchnąć te zmiany do operatora.

## <a name="create-the-simulated-devices"></a>Tworzenie symulowanych urządzeń

W tym samouczku są stosowane symulowane urządzenia podrzędne i symulowane urządzenie bramy.

Aby utworzyć symulowane urządzenie bramy:

1. Na stronie **urządzenia** wybierz pozycję **Inteligentne tworzenie urządzenia bramy** na liście szablonów urządzeń.

1. Wybierz **+** , aby rozpocząć dodawanie nowego urządzenia.

1. Zachowaj wygenerowany **Identyfikator urządzenia** i **nazwę urządzenia**. Upewnij się, że **symulowany** przełącznik jest **włączony**. Wybierz przycisk **Utwórz**.

Aby utworzyć symulowane urządzenia podrzędne:

1. Na stronie **urządzenia** wybierz pozycję **czujnik zajętości RS40** na liście szablonów urządzeń.

1. Wybierz **+** , aby rozpocząć dodawanie nowego urządzenia.

1. Zachowaj wygenerowany **Identyfikator urządzenia** i **nazwę urządzenia**. Upewnij się, że **symulowany** przełącznik jest **włączony**. Wybierz przycisk **Utwórz**.

1. Na stronie **urządzenia** wybierz pozycję **czujnik S1** na liście szablonów urządzeń.

1. Wybierz **+** , aby rozpocząć dodawanie nowego urządzenia.

1. Zachowaj wygenerowany **Identyfikator urządzenia** i **nazwę urządzenia**. Upewnij się, że **symulowany** przełącznik jest **włączony**. Wybierz przycisk **Utwórz**.

![Symulowane urządzenia w aplikacji](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Dodawanie relacji urządzenia podrzędnego do urządzenia bramy

Teraz, gdy w aplikacji znajdują się symulowane urządzenia, można utworzyć relacje między urządzeniami podrzędnymi a urządzeniem bramy:

1. Na stronie **urządzenia** wybierz pozycję **czujnik S1** na liście szablonów urządzeń, a następnie wybierz urządzenie z **czujnikiem S1** .

1. Wybierz pozycję **Połącz z bramą**.

1. W oknie dialogowym **nawiązywanie połączenia z bramą** wybierz szablon **urządzenia inteligentnej bramy** , a następnie wybierz utworzone wcześniej wystąpienie symulowane.

1. Wybierz pozycję **Dołącz**.

1. Na stronie **urządzenia** wybierz pozycję **czujnik zajętości RS40** na liście szablonów urządzeń, a następnie wybierz urządzenie **czujnika zajętości RS40** .

1. Wybierz pozycję **Połącz z bramą**.

1. W oknie dialogowym **nawiązywanie połączenia z bramą** wybierz szablon **urządzenia inteligentnej bramy** , a następnie wybierz utworzone wcześniej wystąpienie symulowane.

1. Wybierz pozycję **Dołącz**.

Symulowane urządzenia podrzędne są teraz połączone z urządzeniem bramy symulowane. W przypadku przejścia do widoku **urządzenia podrzędne** dla urządzenia bramy można zobaczyć powiązane urządzenia podrzędne:

![Widok urządzeń podrzędnych](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Wybierz szablon urządzenia bramy i wystąpienie urządzenia bramy, a następnie wybierz pozycję **Dołącz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Utwórz nową bramę IoT jako szablon urządzenia.
* Utwórz właściwości chmury.
* Utwórz dostosowania.
* Zdefiniuj wizualizację dla danych telemetrycznych urządzenia.
* Dodaj relacje.
* Opublikuj szablon urządzenia.

Następnie jako deweloper urządzenia możesz dowiedzieć się, jak:

> [!div class="nextstepaction"]
> [Dodawanie urządzenia Azure IoT Edge do aplikacji IoT Central platformy Azure](tutorial-add-edge-as-leaf-device.md)
