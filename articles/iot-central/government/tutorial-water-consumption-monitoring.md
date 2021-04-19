---
title: 'Samouczek: tworzenie aplikacji do monitorowania zużycia wody za pomocą Azure IoT Central'
description: 'Samouczek: tworzenie aplikacji do monitorowania zużycia wody przy użyciu Azure IoT Central szablonów aplikacji.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: b65013bbf21faa8bffdcf799a991952b69f5fead
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714474"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Samouczek: tworzenie aplikacji do monitorowania zużycia wody za pomocą Azure IoT Central

W tym samouczku pokazano, jak utworzyć aplikację Azure IoT Central monitorowania zużycia wody przy użyciu szablonu aplikacji Azure IoT Central monitorowania zużycia wody.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj szablonu Azure IoT Central monitorowania zużycia wody, aby utworzyć aplikację do monitorowania zużycia wody.
> * Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora.
> * Eksplorowanie szablonów urządzeń.
> * Eksplorowanie symulowanych urządzeń.
> * Eksplorowanie i konfigurowanie reguł.
> * Konfigurowanie zadań.
> * Dostosowywanie brandingu aplikacji przy użyciu etykietowania białych etykiet.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na stronie tworzenia [konta platformy Azure.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Tworzenie aplikacji do monitorowania zużycia wody za pomocą Azure IoT Central

W tej sekcji użyjemy szablonu monitorowania zużycia Azure IoT Central, aby utworzyć aplikację do monitorowania zużycia wody w Azure IoT Central.

Aby utworzyć nową aplikację Azure IoT Central monitorowania zużycia wody:

1. Przejdź do witryny [Azure IoT Central strony głównej.](https://aka.ms/iotcentral)

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz do uzyskiwania dostępu do tej subskrypcji. W przeciwnym razie zaloguj się przy użyciu konto Microsoft.

    ![Określanie konta organizacji](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Wybierz **pozycję Build** (Kompilacja) w okienku po lewej stronie i wybierz **kartę Government (Rząd).** Na **stronie Dla** instytucji rządowych jest wyświetlanych kilka szablonów aplikacji dla instytucji rządowych.

   ![Tworzenie szablonów aplikacji dla instytucji rządowych](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Wybierz szablon **aplikacji do monitorowania zużycia** wody.
Ten szablon zawiera przykładowy szablon urządzenia zużycie wody, urządzenie symulowane, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.

1. Wybierz **pozycję Utwórz aplikację,** aby otworzyć **formularz Tworzenia** nowej aplikacji z następującymi polami:
    * **Nazwa aplikacji:** domyślnie aplikacja  używa monitorowania zużycia wody, po którym następuje unikatowy ciąg identyfikatora, Azure IoT Central generowany. Opcjonalnie wybierz przyjazną nazwę aplikacji. Nazwę aplikacji można również zmienić później.
    * **Adres URL** Azure IoT Central automatycznie wygenerowany adres URL na podstawie nazwy aplikacji. Możesz zaktualizować adres URL do swoich potrzeb. Adres URL można również zmienić później.
    * Jeśli masz subskrypcję platformy Azure, wprowadź informacje **o** katalogu, **subskrypcji platformy Azure** i **lokalizacji.** Jeśli nie masz subskrypcji, możesz wybrać **opcję 7-dniowej** bezpłatnej wersji próbnej i wypełnić wymagane informacje kontaktowe.

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zobacz [Tworzenie aplikacji w przewodniku Szybki start](../core/quick-deploy-iot-central.md).

1. Wybierz pozycję **Utwórz** w dolnej części strony.

    ![Azure IoT Central nowa aplikacja](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central informacji o rozliczeniach](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Utworzono aplikację do monitorowania zużycia wody przy użyciu szablonu monitorowania zużycia Azure IoT Central wody.

Aplikacja do monitorowania zużycia wody ma wstępnie skonfigurowaną konfigurację:

* Przykładowe pulpity nawigacyjne operatora.
* Przykładowe wstępnie zdefiniowane szablony przepływu wody i urządzenia z przepływem wody.
* Symulowany przepływ wody i urządzenia inteligentne.
* Reguły i zadania.
* Przykładowe znakowanie przy użyciu etykietowania białych.

Jest to Twoja aplikacja i możesz ją zmodyfikować w dowolnym momencie. Teraz przyjrzyjmy się aplikacji i dokonajmy pewnych dostosowań.

## <a name="explore-and-customize-the-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora

Po utworzeniu aplikacji zostanie otwarty przykładowy pulpit nawigacyjny **Zużycie wody w sieci Wide** World.

   ![Pulpit nawigacyjny monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Jako konstruktor możesz tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Zanim spróbujesz go dostosować, zapoznaj się z pulpitem nawigacyjnym.

> [!NOTE]
> Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które zostaną zbadane w następnej sekcji.
  
Pulpit nawigacyjny składa się z różnych rodzajów kafelków:

* **Kafelek obrazu narzędzia Wide World Water Utility:** pierwszy kafelek na pulpicie nawigacyjnym to kafelek obrazu fikcyjnego narzędzia water Wide World Water. Kafelek można dostosować, wstawiając własny obraz lub usuwając go.
* **Kafelek wskaźnika KPI** średniego przepływu wody: kafelek kluczowego wskaźnika wydajności jest skonfigurowany do wyświetlania jako przykład średniej *z ostatnich 30 minut.* Kafelek kluczowego wskaźnika wydajności można dostosować i ustawić dla niego inny typ i zakres czasu.
* **Kafelki poleceń urządzenia:** te kafelki obejmują kafelki **Zamknij kafle,** **Otwórz kafle** i **Ustaw położenie na** kafelku. Wybranie poleceń umożliwia wybranie strony polecenia urządzenia symulowanego. W Azure IoT Central polecenie *jest* *typem* możliwości urządzenia. Tę koncepcję poznamy później w sekcji "Szablon urządzenia" tego samouczka.
* **Mapa obszaru dystrybucji wody:** mapa używa Azure Maps, które można skonfigurować bezpośrednio w Azure IoT Central. Kafelek mapy wyświetla lokalizację urządzenia. Umieść kursor na mapie i wypróbuj kontrolki na mapie, takie jak *powiększenie,* *pomniejszenie* lub *rozwinięcie*.

    ![Mapa pulpitu nawigacyjnego monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Wykres liniowy przepływu średniej** wody i wykres liniowy Warunek środowiskowy: można wizualizować jedną lub wiele telemetrii urządzeń wykreślonych jako wykres liniowy w żądanym zakresie czasu.
* **Wykres mapy cieplnej średniego** ciśnienia wody: możesz wybrać typ wizualizacji mapy cieplnej danych telemetrycznych urządzenia, które mają być dystrybuowane w zakresie czasu z indeksem kolorów.
* **Kafelek zawartości Resetuj progi alertów:** możesz dołączyć kafelki zawartości wezwanie do akcji i osadzić link do strony akcji. W takim przypadku próg alertu resetowania powoduje uruchomienie zadania **aplikacji,** w którym można uruchamiać aktualizacje właściwości urządzenia. Ta opcja zostanie zbadana w dalszej części tego samouczka w sekcji "Konfigurowanie zadań".
* **Kafelki właściwości:** na pulpicie nawigacyjnym są **wyświetlane informacje operacyjne o przepływie,** **progi alertów przepływu** i **kafelki z informacjami o konserwacji.**

### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Jako konstruktor możesz dostosowywać widoki na pulpicie nawigacyjnym dla operatorów.

1. Wybierz **pozycję Edytuj,** aby dostosować **pulpit nawigacyjny zużycie wody w sieci Wide World.** Pulpit nawigacyjny można dostosować, wybierając menu **Edytuj.** Gdy pulpit nawigacyjny będzie w **trybie edycji,** możesz dodać nowe kafelki lub je skonfigurować.

     ![Edytuj pulpit nawigacyjny](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Wybierz **pozycję + Nowy,** aby utworzyć nowy pulpit nawigacyjny i skonfigurować go od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i przechodzić między nimi w menu pulpitów nawigacyjnych.

## <a name="explore-the-device-template"></a>Eksplorowanie szablonu urządzenia

Szablon urządzenia w Azure IoT Central definiuje możliwości urządzenia, które mogą być telemetrią, właściwością lub poleceniem. Jako konstruktor możesz zdefiniować jeden lub więcej szablonów urządzeń w Azure IoT Central, które reprezentują możliwości urządzeń, które będą nawiązywane.

Aplikacja do monitorowania zużycia wody zawiera dwa szablony urządzeń referencyjnych, które reprezentują miernik *przepływu* i *inteligentne urządzenie przenośne.*

Aby wyświetlić szablon urządzenia:

1. Wybierz **pozycję Szablony** urządzeń w lewym okienku aplikacji w Azure IoT Central. Na **liście Szablony urządzeń** zobaczysz dwa szablony urządzeń: **Smart Meter** i **Flow meter**.

   ![Szablon urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Wybierz szablon **urządzenia Miernik** usługi Flow i zapoznaj się z jego możliwościami.

     ![Miernik przepływu szablonu urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Aby dostosować szablon urządzenia:

1. Przejdź do **pozycji Dostosuj** w menu **Szablony** urządzeń.
1. Znajdź typ `Temperature` telemetrii.
1. Zaktualizuj nazwę **wyświetlaną** `Temperature` na `Reported temperature` .
1. Zaktualizuj jednostkę miary lub ustaw wartości **Minimum i** **Maksimum.**
1. Wybierz **pozycję Zapisz,** aby zapisać zmiany.

### <a name="add-a-cloud-property"></a>Dodawanie właściwości w chmurze

1. Przejdź do **pozycji Właściwości chmury** w menu **Szablony** urządzeń.
1. Dodaj nową właściwość w chmurze, wybierając **pozycję + Dodaj właściwość chmury**.
    W Azure IoT Central możesz dodać właściwość odpowiednią dla urządzenia. Na przykład właściwość w chmurze może być progiem alertów specyficznym dla obszaru instalacji, informacji o zasobach lub innych informacji dotyczących konserwacji.
1. Wybierz **pozycję Zapisz,** aby zapisać zmiany.

### <a name="views"></a>Widoki

Szablon urządzenia do monitorowania zużycia wody zawiera wstępnie zdefiniowane widoki. Eksploruj widoki i możesz poznawać aktualizacje. Widoki definiują sposób, w jaki operatorzy widzą dane urządzenia, ale także określają właściwości chmury.

  ![Widoki szablonów urządzeń](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikowanie

Jeśli zostały wprowadzone jakiekolwiek zmiany, pamiętaj, aby **opublikować** szablon urządzenia.

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia

Wybierz **pozycję + Nowy,** aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia.
Możesz utworzyć niestandardowy szablon urządzenia od podstaw lub wybrać szablon urządzenia z katalogu urządzeń platformy Azure.

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

W Azure IoT Central można tworzyć symulowane urządzenia do testowania szablonu urządzenia i aplikacji. Aplikacja do monitorowania zużycia wody ma dwa symulowane urządzenia zamapowane na miernik **przepływu** i **szablony urządzeń Smart Meter.**

### <a name="view-the-devices"></a>Wyświetlanie urządzeń

1. Wybierz **pozycję Urządzenia** Wszystkie  >  **urządzenia** w okienku po lewej stronie.

   ![Okienko Wszystkie urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Wybierz **pozycję Smart Smart 1.**

    ![Smart Smart 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Na karcie **Polecenia** są dostępne trzy polecenia urządzenia ( Zamknij **menu,** Otwórz **ekran** i Ustaw pozycję **urządzenia),** które są możliwościami zdefiniowanymi w szablonie urządzenia **Smart Bydgo.**

1. Zapoznaj się **z kartą Właściwości** urządzenia i **kartą Pulpit nawigacyjny** urządzenia.

> [!NOTE]
> Pamiętaj, że wszystkie karty są konfigurowane z widoków szablonu urządzenia.

### <a name="add-new-devices"></a>Dodawanie nowych urządzeń

Dodaj nowe urządzenia, wybierając **pozycję + Nowe** na **karcie** Urządzenia.

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

W Azure IoT Central można utworzyć reguły automatycznego monitorowania telemetrii urządzenia i wyzwalania akcji, gdy zostanie spełniony co najmniej jeden z warunków. Akcje mogą obejmować wysyłanie powiadomień e-mail lub wyzwalanie akcji microsoft Power Automate lub akcji webhook w celu wysyłania danych do innych usług.

Utworzona aplikacja do monitorowania zużycia wody ma trzy wstępnie skonfigurowane reguły.

### <a name="view-rules"></a>Wyświetlanie reguł

1. Wybierz **pozycję Reguły** w okienku po lewej stronie.

   ![Okienko Reguły](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Wybierz **pozycję Alert przepływu dużej** wody, który jest jedną ze wstępnie skonfigurowanych reguł w aplikacji.

     ![Alert o wysokim pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Reguła `High water flow alert` jest skonfigurowana do sprawdzania pod względem warunku `Flow` . `greater than` `Max flow threshold` Maksymalny próg przepływu to właściwość w chmurze zdefiniowana w **szablonie urządzenia Smart Funkcji.** Wartość jest `Max flow threshold` ustawiana dla każdego wystąpienia urządzenia.

Teraz utwórzmy akcję poczty e-mail.

Aby dodać akcję do reguły:

1. Wybierz **pozycję + Wyślij wiadomość e-mail.**
1. Wprowadź **Alert o wysokim przepływie** jako przyjazną nazwę **wyświetlaną** akcji.
1. Wprowadź adres e-mail skojarzony z Twoim kontem Azure IoT Central w **adresie do**.
1. Opcjonalnie wprowadź notatkę, która ma być uwzględniana w tekście wiadomości e-mail.
1. Wybierz **pozycję Gotowe,** aby zakończyć akcję.
1. Wybierz **pozycję Zapisz,** aby zapisać nową regułę.
1. Włącz regułę.

Po upływie kilku minut powinna zostać przesłana wiadomość e-mail po spełnionym skonfigurowanym warunku.

> [!NOTE]
> Aplikacja wysyła wiadomość e-mail za każdym razem, gdy warunek zostanie spełniony. Wybierz **pozycję Wyłącz,** aby wyłączyć regułę, aby zatrzymać odbieranie wiadomości e-mail z reguły automatycznej.
  
Aby utworzyć nową regułę:

* Wybierz **pozycję + Nowy** na karcie **Reguły** w okienku po lewej stronie.

## <a name="configure-jobs"></a>Konfigurowanie zadań

W Azure IoT Central zadania umożliwiają wyzwalanie aktualizacji właściwości urządzenia lub chmury na wielu urządzeniach. Oprócz właściwości można również używać zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. Azure IoT Central automatycznie przepływ pracy.

1. Wybierz **pozycję Zadania** w okienku po lewej stronie.
1. Wybierz **pozycję + Nowy** i skonfiguruj jedno lub więcej zadań.

## <a name="customize-your-application"></a>Dostosowywanie aplikacji

Konstruktor może zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

1. Wybierz **pozycję**  >  **Administracja Dostosuj aplikację.**
1. Aby wybrać obraz do przekazania jako **logo aplikacji,** wybierz **przycisk** Zmień.
1. Aby wybrać obraz **ikony Przeglądarka,** który będzie wyświetlany na kartach przeglądarki, wybierz **przycisk** Zmień.
1. Możesz również zastąpić domyślne kolory **przeglądarki,** dodając szesnastkowe kody kolorów HTML.

   ![Opcje logo aplikacji, ikony przeglądarki i kolorów przeglądarki](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Obrazy aplikacji można również zmienić, wybierając **pozycję Ustawienia aplikacji**  >  **administracyjnej.** Aby wybrać obraz do przekazania jako obraz aplikacji, wybierz **przycisk Wybierz** obraz.
1. Na koniec możesz również zmienić **motyw,** wybierając **ikonę Ustawienia** w prawym górnym rogu aplikacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal używać tej aplikacji, usuń ją.

1. Wybierz **pozycję Administracja** w lewym okienku aplikacji Azure IoT Central aplikacji.
1. Wybierz **pozycję Ustawienia** aplikacji, a następnie wybierz pozycję **Usuń** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o 

> [!div class="nextstepaction"]
> [Pojęcia związane z monitorowaniem zużycia wody](./concepts-waterconsumptionmonitoring-architecture.md).
