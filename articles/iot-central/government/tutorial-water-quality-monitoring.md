---
title: 'Samouczek: Tworzenie aplikacji do monitorowania jakości wody za pomocą usługi Azure IoT Central'
description: 'Samouczek: Dowiedz się, jak utworzyć aplikację do monitorowania jakości wody przy użyciu szablonów aplikacji Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ec999d63175f63c1de6e31fdb3f72c963d228c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024476"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Samouczek: Tworzenie aplikacji do monitorowania jakości wody w usłudze Azure IoT Central



W tym samouczku można przejść przez tworzenie aplikacji do monitorowania jakości wody w usłudze Azure IoT Central. Aplikację można utworzyć na podstawie szablonu aplikacji monitorowania jakości usługi Azure IoT Central **Water.**

Z tego samouczka dowiesz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Użyj szablonu **monitorowania jakości wody,** aby utworzyć aplikację do monitorowania jakości wody.
> * Eksploruj i dostosowuj pulpit nawigacyjny operatora.
> * Poznaj szablon urządzenia do monitorowania jakości wody.
> * Eksploruj symulowane urządzenia.
> * Eksploruj i konfiguruj reguły.
> * Konfigurowanie zadań.
> * Dostosuj znakowanie aplikacji za pomocą białego etykietowania.

## <a name="prerequisites"></a>Wymagania wstępne

Zaleca się, aby mieć subskrypcję platformy Azure, aby zakończyć ten samouczek. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji platformy Azure.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Tworzenie aplikacji do monitorowania jakości wody w usłudze Azure IoT Central

W tej sekcji użyj szablonu monitorowania jakości usługi Azure IoT Central **Water,** aby utworzyć aplikację do monitorowania jakości wody.

1. Przejdź do [strony głównej usługi Azure IoT Central](https://aka.ms/iotcentral).

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. W przeciwnym razie zaloguj się za pomocą konta Microsoft:

    ![Logowanie się do konta organizacji](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Wybierz **pozycję Kompilacja** w lewym okienku usługi Azure IoT Central i wybierz kartę **Administracji** łzowej. W okienku rządowym przedstawiono kilka szablonów aplikacji rządowych.

    ![Szablony aplikacji rządowych](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Wybierz szablon aplikacji **monitorowania jakości wody.** Ten szablon aplikacji zawiera szablon urządzenia jakości wody, symulowane urządzenia, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.

1. Wybierz pozycję **Utwórz aplikację**. Zostanie otwarte okienko **Nowa aplikacja** i wyświetlają następujące elementy:

    * **Nazwa aplikacji:** Domyślnie nazwa aplikacji to **Monitorowanie jakości wody,** po którym następuje unikatowy ciąg identyfikatorów generowany przez usługę Azure IoT Central. Jeśli chcesz, możesz wprowadzić nazwę wyświetlaną lub później zmienić nazwę aplikacji.
    * **ADRES URL:** Możesz wprowadzić dowolny adres URL lub później zmienić wartość adresu URL.
    * Jeśli masz subskrypcję platformy Azure, wprowadź wartości **dla usługi Directory,** **subskrypcja platformy Azure**i **Region**. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i wypełnić wymagane informacje kontaktowe.

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zobacz [Tworzenie szybkiego startu aplikacji.](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

1. Wybierz przycisk **Utwórz** w lewej dolnej części strony.

    ![Nowa strona aplikacji usługi Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Informacje rozliczeniowe dotyczące nowej aplikacji Usługi Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Aplikacja do monitorowania jakości wody została utworzona przy użyciu szablonu monitorowania jakości usługi Azure IoT Central **Water.**

Nowa aplikacja jest wyposażona w te wstępnie skonfigurowane składniki:

* Pulpity nawigacyjne operatora
* Szablony urządzeń monitorujących jakość wody
* Symulowane urządzenia do monitorowania jakości wody
* Zasady i miejsca pracy
* Znakowanie, które używa białego etykietowania

Aplikację można zmodyfikować w dowolnym momencie.

Następnie zapoznaj się z aplikacją i dokonać pewnych dostosowań.

## <a name="explore-and-customize-the-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora

Po utworzeniu aplikacji zostanie otwarte **okienko pulpitu nawigacyjnego o jakości wody na całym świecie.**

   ![Deska rozdzielcza do monitorowania jakości wody](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Jako konstruktor możesz tworzyć i dostosowywać widoki na pulpicie nawigacyjnym do użytku przez operatorów. Zanim jednak spróbujesz dostosować, najpierw eksploruj pulpit nawigacyjny.

Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które zostały omówione w następnej sekcji.

Pulpit nawigacyjny zawiera następujące rodzaje kafelków:

* **Wide World water utility image tile**: Pierwszy kafelek w lewym górnym rogu deski rozdzielczej jest obraz, który pokazuje fikcyjne narzędzie o nazwie Wide World. Kafelek można dostosować do użycia własnego obrazu lub usunąć kafelek.

* **Średnie kafelki kpi pH**: płytki kluczowego wskaźnika wydajności, takie jak **średnie pH w ciągu ostatnich 30 minut,** znajdują się w górnej części okienka pulpitu nawigacyjnego. Można dostosować kafelki kluczowych wskaźników wydajności i ustawić każdy z nich na inny typ i zakres czasu.

* **Mapa obszaru monitorowania wody:** usługa Azure IoT Central korzysta z usługi Azure Maps, którą można bezpośrednio ustawić w aplikacji w celu wyświetlenia lokalizacji urządzenia. Można również mapować informacje o lokalizacji z aplikacji na urządzeniu, a następnie za pomocą usługi Azure Maps, aby wyświetlić informacje na mapie. Umieść wskaźnik myszy na mapie i wypróbuj kontrolki.

* **Wykres mapy cieplnej dystrybucji pH:** Można wybrać różne wykresy wizualizacji, aby wyświetlić dane telemetryczne urządzenia w sposób najbardziej odpowiedni dla danego zastosowania.

* **Wykres liniowy krytycznych wskaźników jakości:** Można wizualizować dane telemetryczne urządzenia wykreślone jako wykres liniowy w zakresie czasu.  

* **Stężenie środków chemicznych wykres słupkowy:** Można wizualizować telemetrii urządzenia na wykresie słupkowym.

* **Przycisk akcji:** Pulpit nawigacyjny zawiera kafelek dla akcji, które operator może zainicjować bezpośrednio z pulpitu nawigacyjnego monitorowania. Resetowanie właściwości urządzenia jest przykładem takich akcji.

* **Kafelki listy właściwości:** Pulpit nawigacyjny zawiera wiele kafelków właściwości, które reprezentują informacje o progu, informacje o kondycji urządzenia i informacje o konserwacji.

### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Jako konstruktor możesz dostosować widoki na pulpicie nawigacyjnym do użytku przez operatorów.

1. Wybierz **pozycję Edytuj,** aby dostosować **okienko pulpitu nawigacyjnego o jakości wody na całym świecie.** Pulpit nawigacyjny można dostosować, wybierając polecenia w menu **Edycja.** Po przejściu pulpitu nawigacyjnego w trybie edycji można dodać nowe kafelki lub skonfigurować istniejące pliki.

    ![Edytowanie pulpitu nawigacyjnego](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Wybierz **+ Nowy,** aby utworzyć nowy pulpit nawigacyjny, który można skonfigurować. Możesz mieć wiele pulpitów nawigacyjnych i poruszać się między nimi z menu pulpitu nawigacyjnego.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Poznaj szablon urządzenia do monitorowania jakości wody

Szablon urządzenia w usłudze Azure IoT Central definiuje możliwości urządzenia. Dostępne funkcje to dane telemetryczne, właściwości i polecenia. Jako konstruktor możesz zdefiniować szablony urządzeń w usłudze Azure IoT Central, które reprezentują możliwości połączonych urządzeń. Można również utworzyć symulowane urządzenia, aby przetestować szablon urządzenia i aplikację.

Stworzona aplikacja do monitorowania jakości wody jest wyposażona w szablon urządzenia do monitorowania jakości wody.

Aby wyświetlić szablon urządzenia:

1. Wybierz **szablony urządzeń** w lewym okienku aplikacji w usłudze Azure IoT Central.
1. Z listy szablonów urządzeń wybierz pozycję **Monitor jakości wody**. Otworzy się ten szablon urządzenia.

    ![Szablon urządzenia](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Przećwicz dostosowywanie następujących ustawień szablonu urządzenia:

1. Z menu szablonu urządzenia wybierz polecenie **Dostosuj**.
1. Przejdź do typu **Telemetria temperatury.**
1. Zmień wartość **Nazwa wyświetlana** na **Temperatura zgłoszona**.
1. Zmień jednostkę miary lub ustaw **wartość Min** i **Wartość Maksymalna**.
1. Wybierz pozycję **Zapisz**.

#### <a name="add-a-cloud-property"></a>Dodawanie właściwości w chmurze

1. Z menu szablonu urządzenia wybierz **polecenie Chmura**.
1. Aby dodać nową właściwość chmury, wybierz pozycję **+ Dodaj właściwość chmury**. W usłudze Azure IoT Central można dodać właściwość, która jest odpowiednia dla urządzenia, ale nie oczekuje się, że zostanie wysłana przez urządzenie. Jednym z przykładów takiej właściwości jest próg alertu specyficzny dla obszaru instalacji, informacji o zasobach lub informacji o konserwacji.
1. Wybierz pozycję **Zapisz**.

### <a name="explore-views"></a>Eksploruj widoki

Szablon urządzenia do monitorowania jakości wody zawiera wstępnie zdefiniowane widoki. Widoki definiują sposób, w jaki operatorzy widzą dane urządzenia i ustawiają właściwości chmury. Poznaj widoki i praktykuj wprowadzanie zmian.

  ![Widoki szablonu urządzenia](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Jeśli wniesiesz jakiekolwiek zmiany, wybierz **pozycję Publikuj,** aby opublikować szablon urządzenia.

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia

1. Wybierz **+ Nowy,** aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia.
1. Utwórz niestandardowy szablon urządzenia lub wybierz szablon urządzenia z katalogu urządzeń Usługi Azure IoT.

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

Aplikacja do monitorowania jakości wody utworzona na podstawie szablonu aplikacji ma dwa symulowane urządzenia. Urządzenia te są mapowane do szablonu urządzenia monitorującego jakość wody.

### <a name="view-the-devices"></a>Wyświetlanie urządzeń

1. Wybierz **pozycję Urządzenia** w lewym okienku aplikacji.

   ![Urządzenia](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Wybierz jedno symulowane urządzenie.

    ![Wybierz urządzenie 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Na karcie **Właściwości chmury** zmień wartość **progową Kwasowość (pH)** z **8** na **9**.
1. Zapoznaj się z kartą **Właściwości urządzenia** i pulpit nawigacyjny **urządzenia.**

> [!NOTE]
> Wszystkie karty zostały skonfigurowane z **widoków szablonu urządzenia**.

### <a name="add-new-devices"></a>Dodawanie nowych urządzeń

Na karcie **Urządzenia** wybierz pozycję **+ Nowy,** aby dodać nowe urządzenie.

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

W usłudze Azure IoT Central można tworzyć reguły, które automatycznie monitorują dane telemetryczne urządzenia. Reguły te wyzwalają akcję, gdy spełniony jest którykolwiek z warunków. Jedną z możliwych czynności jest wysyłanie powiadomień e-mail. Inne możliwości obejmują akcję usługi Microsoft Flow lub akcję elementu webhook do wysyłania danych do innych usług.

Utworzona aplikacja do monitorowania jakości wody ma dwie wstępnie skonfigurowane reguły.

### <a name="view-rules"></a>Wyświetlanie reguł

1. Wybierz **reguły** w lewym okienku aplikacji.

   ![Reguły](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Wybierz **alert wysokiego pH**, który jest jedną ze wstępnie skonfigurowanych reguł w aplikacji.

   ![Reguła wysokiego alertu pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   Reguła **alertu o wysokim pH** jest skonfigurowana tak, aby sprawdzała, czy stan kwasowości (pH) jest większy niż 8.

Następnie dodaj akcję e-mail do reguły:

1. Wybierz **+ E-mail**.
1. W polu **Nazwa wyświetlana** wprowadź **alert Wysokiego pH**.
1. W polu **Do** wprowadź adres e-mail skojarzony z kontem Usługi Azure IoT Central.
1. Opcjonalnie wprowadź notatkę, która ma być uwzględnina w tekście wiadomości e-mail.
1. Wybierz **pozycję Gotowe,** aby ukończyć akcję.
1. Wybierz **pozycję Zapisz,** aby zapisać i aktywować nową regułę.

W ciągu kilku minut powinieneś otrzymywać wiadomości e-mail po spełnieniu skonfigurowanego warunku.

> [!NOTE]
> Aplikacja wysyła wiadomości e-mail za każdym razem, gdy warunek jest spełniony. Wybierz **pozycję Wyłącz,** aby reguła przestała otrzymywać automatyczną wiadomość e-mail z tej reguły.
  
Aby utworzyć nową regułę, wybierz pozycję **Reguły** w lewym okienku aplikacji, a następnie wybierz pozycję **+Nowy**.

## <a name="configure-jobs"></a>Konfigurowanie zadań

Dzięki zadaniem usługi Azure IoT Central można wyzwolić aktualizacje właściwości urządzenia lub chmury na wielu urządzeniach. Zadania można również używać do wyzwalania poleceń urządzenia na wielu urządzeniach. Usługa Azure IoT Central automatyzuje przepływ pracy.

1. Wybierz **pozycję Zadania** w lewym okienku aplikacji.
1. Wybierz **+Nowy** i skonfiguruj jedno lub więcej zadań.

## <a name="customize-your-application"></a>Dostosowywanie aplikacji

Jako konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

1. Wybierz **pozycję Administracja** > **Dostosuj aplikację**.
1. W obszarze **Logo aplikacji**wybierz **pozycję Zmień,** aby wybrać obraz do przesłania jako logo.
1. W **obszarze Ikona przeglądarki**wybierz pozycję **Zmień,** aby wybrać obraz wyświetlany na kartach przeglądarki.
1. W **obszarze Kolory przeglądarki**można zastąpić wartościami domyślnymi kodami kolorów szesnastkowych HTML.
1. Wybierz **ustawienia,** aby zmienić wartość **motywu**.

   ![Dostosowywanie aplikacji](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Aktualizowanie obrazu aplikacji

1. Wybierz ustawienia aplikacji **administracyjnej** > **Application settings**.

1. Użyj przycisku **Wybierz obraz,** aby wybrać obraz do przesłania jako obraz aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z aplikacji, usuń aplikację, wykonując następujące czynności:

1. Otwórz kartę **Administracja** w lewym okienku aplikacji.
1. Wybierz **pozycję Ustawienia aplikacji** i wybierz przycisk **Usuń.**

    ![Usuwanie aplikacji](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pojęciach monitorowania jakości wody.](./concepts-waterqualitymonitoring-architecture.md)
