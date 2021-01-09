---
title: Samouczek — Tworzenie reguł i zarządzanie nimi w aplikacji IoT Central platformy Azure
description: W tym samouczku przedstawiono sposób, w jaki reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym oraz automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail, gdy reguła jest wyzwalana.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a1991860e1599020c5a91c09dfb30a96ed442ff7
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033850"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Samouczek: Tworzenie reguły i konfigurowanie powiadomień w aplikacji usługi Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Za pomocą usługi Azure IoT Central można zdalnie monitorować połączone urządzenia. Reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym i automatyczne wywoływanie akcji, na przykład wysłanie wiadomości e-mail. W tym artykule wyjaśniono, jak utworzyć reguły monitorowania danych telemetrycznych wysyłanych przez urządzenia.

Urządzenia używają telemetrii do wysyłania danych liczbowych z urządzenia. Reguła jest wyzwalana, gdy wybrana Telemetria przekracza określony próg.

W tym samouczku utworzysz regułę do wysyłania wiadomości e-mail, gdy temperatura w symulowanym urządzeniu czujnika przekracza 70 &deg; F.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie reguły
> * Dodaj akcję poczty e-mail

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wykonaj czynności [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) i [Dodawanie symulowanego urządzenia do aplikacji IoT Central](./quick-create-simulated-device.md) przewodników Szybki Start, aby utworzyć szablon urządzenia **kontrolera czujnika** do pracy.

## <a name="create-a-rule"></a>Tworzenie reguły

Aby utworzyć regułę telemetrii, szablon urządzenia musi zawierać co najmniej jedną wartość telemetrii. W tym samouczku jest używane urządzenie symulowanego **kontrolera czujnika** , które wysyła dane telemetryczne temperatury i wilgotności. Dodano ten szablon urządzenia i utworzono symulowane urządzenie na stronie [Dodawanie symulowanego urządzenia do aplikacji do IoT Central](./quick-create-simulated-device.md) przewodnika Szybki Start. Reguła monitoruje temperaturę zgłoszoną przez urządzenie i wysyła wiadomość e-mail, gdy znajdzie się ona powyżej 70 stopni.

> [!NOTE]
> Istnieje limit 50 reguł dla aplikacji.

1. W lewym okienku wybierz pozycję **reguły**.

1. Jeśli nie utworzono jeszcze żadnych reguł, zobaczysz następujący ekran:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="Zrzut ekranu pokazujący pustą listę reguł":::

1. Wybierz pozycję **+ Nowy** , aby dodać nową regułę.

1. Wprowadź nazwę _monitora temperatury_ , aby zidentyfikować regułę, a następnie naciśnij klawisz ENTER.

1. Wybierz szablon urządzenia **kontrolera czujnika** . Domyślnie reguła ma zastosowanie automatycznie do wszystkich urządzeń skojarzonych z szablonem urządzenia. Aby odfiltrować podzestaw urządzeń, wybierz opcję **+ Filtr** i Użyj właściwości urządzenia w celu zidentyfikowania urządzeń. Aby wyłączyć regułę, przełącz przycisk **włączone/wyłączone** :

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="Zrzut ekranu pokazujący wybór szablonu urządzenia w definicji reguły":::

### <a name="configure-the-rule-conditions"></a>Skonfiguruj warunki reguły

Warunki określają kryteria monitorowane przez regułę. W tym samouczku skonfigurujesz regułę do uruchamiania, gdy temperatura przekracza 70 &deg; F.

1. Wybierz pozycję **temperatura** na liście rozwijanej **telemetrii** .

1. Następnie wybierz pozycję **jest większy niż** **Operator** i wprowadź _70_ jako **wartość**.

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="Zrzut ekranu pokazujący warunek temperatury dla reguły":::

1. Opcjonalnie można ustawić **agregację czasu**. Po wybraniu agregacji czasu należy również wybrać typ agregacji, taki jak Average lub sum z listy rozwijanej agregacji.

    * Bez agregacji reguła wyzwala dla każdego punktu danych telemetrii, który spełnia warunek. Na przykład, jeśli skonfigurujesz regułę do wyzwalania, gdy temperatura jest powyżej 70, reguła jest wyzwalana niemal natychmiast, gdy temperatura urządzenia przekroczy tę wartość.
    * Przy agregacji reguła jest wyzwalana, gdy wartość zagregowana punktów danych telemetrii w przedziale czasu spełnia warunek. Na przykład, jeśli skonfigurujesz regułę do wyzwalania, gdy temperatura przekracza 70 i średni czas agregacji wynoszący 10 minut, reguła jest wyzwalana, gdy urządzenie zgłosi średnią temperaturę większą niż 70, obliczoną w ciągu 10 minut.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="Zrzut ekranu pokazujący łączny warunek wypełnienia":::

Do reguły można dodać wiele warunków, wybierając pozycję **+ warunek**. W przypadku określenia wielu warunków wszystkie warunki muszą być spełnione, aby reguła była wyzwalana. Każdy warunek jest przyłączony przez klauzulę niejawną `AND` . Jeśli używasz agregacji czasu z wieloma warunkami, wszystkie wartości telemetryczne muszą być agregowane.

### <a name="configure-actions"></a>Skonfiguruj akcje

Po zdefiniowaniu warunku należy skonfigurować akcje do wykonania, gdy reguła zostanie wygenerowane. Akcje są wywoływane, gdy wszystkie warunki określone w regule mają wartość true.

1. Wybierz pozycję **+ poczta e-mail** w sekcji **Akcje** .

1. Wprowadź _ostrzegawczą temperaturę_ jako nazwę wyświetlaną akcji, adres e-mail w polu **do** i _Sprawdź urządzenie_ . jako notatka, która ma być wyświetlana w treści wiadomości e-mail.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, którzy zostali dodani do aplikacji i zarejestrowali się co najmniej raz. Dowiedz się więcej na temat [zarządzania użytkownikami](howto-administer.md) w usłudze Azure IoT Central.

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="Zrzut ekranu pokazujący akcję e-mail dla reguły":::

1. Aby zapisać akcję, wybierz pozycję **gotowe**. Do reguły można dodać wiele akcji.

1. Aby zapisać regułę, wybierz pozycję **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i zacznie monitorować dane telemetryczne wysyłane do aplikacji. Gdy warunek określony w regule zostanie spełniony, reguła wyzwala skonfigurowaną akcję poczty e-mail.

Gdy reguła zostanie wyświetlona, otrzymasz wiadomość e-mail z powiadomieniem:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Zrzut ekranu przedstawiający wiadomość e-mail z powiadomieniem":::

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebna, usuń ją, otwierając regułę i wybierając pozycję **Usuń**.

## <a name="enable-or-disable-a-rule"></a>Włączanie lub wyłączanie reguły

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz przycisk **włączone/wyłączone** w regule, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń objętych zakresem reguły.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Włączanie lub wyłączanie reguły dla określonych urządzeń

Wybierz regułę, którą chcesz dostosować. Aby zawęzić zakres reguły do urządzeń, które mają być monitorowane, należy użyć co najmniej jednego filtru w sekcji **urządzenia docelowe** .

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodawanie akcji

Po zdefiniowaniu reguły opartej na progach zalecanym następnym krokiem jest zapoznanie się z tematem:

> [!div class="nextstepaction"]
> [Skonfiguruj ciągły eksport danych](./howto-export-data.md).
