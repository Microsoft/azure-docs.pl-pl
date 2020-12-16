---
title: 'Samouczek: Tworzenie aplikacji do monitorowania zużycia wody przy użyciu usługi Azure IoT Central'
description: 'Samouczek: informacje na temat tworzenia aplikacji do monitorowania zużycia wody przy użyciu IoT Central szablonów aplikacji platformy Azure.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 476a0c2d75dcbee5c1ed4d758e0cbc9d4726ff95
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587200"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Samouczek: Tworzenie aplikacji do monitorowania zużycia wody przy użyciu usługi Azure IoT Central

W tym samouczku pokazano, jak utworzyć aplikację monitorowania zużycia wody na platformie Azure IoT Central przy użyciu szablonu aplikacji do monitorowania zużycia wody IoT Central platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj szablonu monitorowania zużycia wody IoT Central platformy Azure, aby utworzyć aplikację monitorowania zużycia wody.
> * Eksploruj i dostosowuj pulpit nawigacyjny operatora.
> * Eksploruj szablony urządzeń.
> * Eksploruj symulowane urządzenia.
> * Eksplorowanie i konfigurowanie reguł.
> * Konfigurowanie zadań.
> * Dostosuj znakowanie aplikacji, używając białych etykiet.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Tworzenie aplikacji do monitorowania zużycia wody przy użyciu usługi Azure IoT Central

W tej sekcji Szablon monitorowania zużycia wody IoT Central platformy Azure służy do tworzenia aplikacji do monitorowania zużycia wody w usłudze Azure IoT Central.

Aby utworzyć nową aplikację monitorowania zużycia wody w usłudze Azure IoT Central:

1. Przejdź do witryny sieci Web [strony głównej platformy Azure IoT Central](https://aka.ms/iotcentral) .

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. W przeciwnym razie zaloguj się przy użyciu konto Microsoft.

    ![Określanie konta organizacji](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Wybierz pozycję **kompilacja** w okienku po lewej stronie, a następnie wybierz pozycję Karta **rządowa** . Na stronie **Administracja** jest wyświetlana kilka szablonów aplikacji rządowych.

   ![Kompiluj szablony aplikacji dla instytucji rządowych](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Wybierz szablon aplikacja do **monitorowania zużycia wody** .
Ten szablon zawiera przykładowy szablon urządzenia do zużycia wody, symulowane urządzenie, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.

1. Wybierz pozycję **Utwórz aplikację** , aby otworzyć formularz tworzenia **nowej aplikacji** z następującymi polami:
    * **Nazwa aplikacji**: Domyślnie aplikacja używa *monitorowania zużycia wody* oraz unikatowego ciągu identyfikatora, który generuje usługa Azure IoT Central. Opcjonalnie możesz wybrać przyjazną nazwę aplikacji. Nazwę aplikacji można zmienić później.
    * **Adres URL**: usługa Azure IoT Central automatycznie generuje adres URL na podstawie nazwy aplikacji. Możesz zaktualizować adres URL do swoich potrzeb. Możesz również zmienić adres URL później.
    * Jeśli masz subskrypcję platformy Azure, wprowadź swój **katalog**, **subskrypcję platformy Azure** i informacje o **lokalizacji** . Jeśli nie masz subskrypcji, możesz wybrać opcję **7-dniowa bezpłatna wersja próbna** i uzupełnić wymagane informacje kontaktowe.

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zobacz [Tworzenie aplikacji — szybki start](../core/quick-deploy-iot-central.md).

1. Wybierz pozycję **Utwórz** w dolnej części strony.

    ![Strona nowej aplikacji IoT Central platformy Azure](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Strona informacji dotyczących rozliczeń w usłudze Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Aplikacja do monitorowania zużycia wody została już utworzona przy użyciu szablonu monitorowania zużycia wody w usłudze Azure IoT Central.

Aplikacja do monitorowania zużycia wody jest wstępnie skonfigurowana:

* Przykładowe pulpity nawigacyjne operatora.
* Przykład wstępnie zdefiniowanego przepływu wody i szablonów urządzeń z zaworem.
* Symulowany przepływ wody i urządzenia przenośne.
* Zasady i zadania.
* Przykładowe oznakowanie przy użyciu białych etykiet.

Jest to Twoja aplikacja i możesz ją zmodyfikować w dowolnym momencie. Teraz eksplorujmy aplikację i wprowadzasz pewne dostosowania.

## <a name="explore-and-customize-the-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora

Po utworzeniu aplikacji zostanie otwarty **pulpit nawigacyjny panoramicznego zużycia wody** .

   ![Pulpit nawigacyjny monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Jako Konstruktor można tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Przeanalizujmy pulpit nawigacyjny przed próbą dostosowania go.

> [!NOTE]
> Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które zostaną omówione w następnej sekcji.
  
Pulpit nawigacyjny składa się z różnych rodzajów kafelków:

* **Kafelek obrazu narzędzia Wide World wodne**: pierwszy kafelek na pulpicie nawigacyjnym to kafelek obrazu fikcyjnej wody w całej sieci. Kafelek można dostosować, wstawiając własny obraz lub usuwając go.
* **KAFELEK KWW średniego przepływu wody**: kafelek KPI jest skonfigurowany do wyświetlania jako przykład *średniej w ciągu ostatnich 30 minut*. Można dostosować kafelek KPI i ustawić go na inny typ i zakres czasu.
* **Kafelki poleceń urządzeń**: te kafelki obejmują kafelki **zamknięcia**, **zawór otwarty** i **Ustaw położenie zaworów** . Wybranie poleceń spowoduje przejście do strony polecenia symulowanego urządzenia. Na platformie Azure IoT Central *polecenie* jest typem *możliwości urządzenia* . Omawiane koncepcje zostaną omówione później w sekcji "szablon urządzenia" w tym samouczku.
* **Mapa obszaru dystrybucji wody**: mapa używa Azure Maps, które można skonfigurować bezpośrednio w usłudze Azure IoT Central. Kafelek Mapa przedstawia lokalizację urządzenia. Umieść kursor nad mapą i wypróbuj kontrolki na mapie, takie jak *Powiększ*, *Powiększ* lub *Rozwiń*.

    ![Mapa pulpitu nawigacyjnego monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Wykres liniowy ( **średnia przepływów** ) i **schemat warunków środowiska**: można wizualizować jeden lub wiele telemetrii urządzeń, które są kreślone jako wykres liniowy w żądanym zakresie czasu.
* **Średni wykres mapę cieplnąego ciśnienia zaworu**: można wybrać typ wizualizacji mapę cieplną danych telemetrycznych urządzenia, które mają być rozłożone w ramach przedziału czasu z indeksem koloru.
* **Kafelek resetowania progów alertów**: można dołączyć kafelki zawartości z wezwaniem do działania i osadzić łącze ze stroną akcji. W takim przypadku próg resetowania alertu prowadzi do **zadań** aplikacji, w którym można uruchamiać aktualizacje właściwości urządzenia. Ta opcja zostanie zbadana później w sekcji "Konfigurowanie zadań" w tym samouczku.
* **Kafelki właściwości**: pulpit nawigacyjny wyświetla **informacje operacyjne zaworu**, **progi alertów przepływu** oraz kafelki **informacji o konserwacji** .

### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Jako Konstruktor można dostosować widoki na pulpicie nawigacyjnym dla operatorów.

1. Wybierz pozycję **Edytuj** , aby dostosować **pulpit nawigacyjny wykorzystania całego świata**. Możesz dostosować pulpit nawigacyjny, wybierając menu **Edycja** . Gdy pulpit nawigacyjny jest w trybie **edycji** , można dodawać nowe kafelki lub je konfigurować.

     ![Edytuj pulpit nawigacyjny](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Wybierz pozycję **+ Nowy** , aby utworzyć nowy pulpit nawigacyjny i skonfigurować go od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i można przechodzić między pulpitami nawigacyjnymi w menu Pulpit nawigacyjny.

## <a name="explore-the-device-template"></a>Eksplorowanie szablonu urządzenia

Szablon urządzenia w usłudze Azure IoT Central definiuje możliwość urządzenia, które może być Telemetria, właściwości lub polecenia. Jako Konstruktor można zdefiniować co najmniej jeden szablon urządzenia w usłudze Azure IoT Central, który reprezentuje możliwość łączenia urządzeń.

Aplikacja do monitorowania zużycia wody obejmuje dwa szablony urządzeń referencyjnych, które reprezentują *Miernik przepływu* *i urządzenie przenośne* .

Aby wyświetlić szablon urządzenia:

1. Wybierz pozycję **Szablony urządzeń** w lewym okienku aplikacji na platformie Azure IoT Central. Na liście **Szablony urządzeń** zostaną wyświetlone dwa szablony urządzeń, **zawory inteligentne** i **Miernik przepływu**.

   ![Szablon urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Wybierz szablon urządzenia **miernika przepływu** i zapoznaj się z możliwościami urządzenia.

     ![Licznik przepływu szablonu urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Aby dostosować szablon urządzenia:

1. Przejdź do pozycji **Dostosowywanie** w menu **Szablony urządzeń** .
1. Znajdź `Temperature` Typ telemetrii.
1. Zaktualizuj **nazwę wyświetlaną** `Temperature` do `Reported temperature` .
1. Zaktualizuj jednostkę miary lub ustaw wartość **minimalną** oraz **wartość maksymalną**.
1. Wybierz pozycję **Zapisz** , aby zapisać zmiany.

### <a name="add-a-cloud-property"></a>Dodaj właściwość chmury

1. Przejdź do pozycji **właściwości chmury** w menu **Szablony urządzeń** .
1. Dodaj nową właściwość chmury, wybierając pozycję **+ Dodaj właściwość chmury**.
    Na platformie Azure IoT Central można dodać właściwość, która jest odpowiednia dla urządzenia. Przykładowo Właściwość chmury może być progiem alertu specyficznym dla obszaru instalacji, informacji o zasobach lub innych informacji o obsłudze.
1. Wybierz pozycję **Zapisz** , aby zapisać zmiany.

### <a name="views"></a>Widoki

Szablon urządzenia monitora zużycia wody jest dostępny ze wstępnie zdefiniowanymi widokami. Przejrzyj widoki i możesz wprowadzić aktualizacje. Widoki definiują sposób, w jaki operatory widzą dane urządzenia, ale również wprowadzają właściwości chmury.

  ![Widoki szablonów urządzeń](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikowanie

W przypadku wprowadzenia jakichkolwiek zmian upewnij się, że szablon urządzenia jest **opublikowany** .

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia

Wybierz pozycję **+ Nowy** , aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia.
Możesz utworzyć niestandardowy szablon urządzenia od podstaw lub wybrać szablon urządzenia z wykazu urządzeń platformy Azure.

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

Na platformie Azure IoT Central można tworzyć symulowane urządzenia do testowania szablonu i aplikacji urządzenia. Aplikacja do monitorowania zużycia wody ma dwa symulowane urządzenia zamapowane na **Miernik przepływu** i **inteligentne** szablony urządzeń.

### <a name="view-the-devices"></a>Wyświetlanie urządzeń

1. Wybierz pozycję **urządzenia**  >  **wszystkie urządzenia** w lewym okienku.

   ![Okienko wszystkie urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Wybierz pozycję **inteligentny zawór 1**.

    ![Zawór inteligentny 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Na karcie **polecenia** można zobaczyć trzy polecenia urządzenia (**zawory zamykające**, **zawór otwarty** i **Ustaw zawór**), które są możliwościami zdefiniowanymi w **szablonie urządzenia** przenośnego.

1. Przejrzyj kartę **Właściwości urządzenia** i kartę **pulpit nawigacyjny urządzenia** .

> [!NOTE]
> Należy pamiętać, że wszystkie karty są konfigurowane z widoków szablonów urządzeń.

### <a name="add-new-devices"></a>Dodaj nowe urządzenia

Dodaj nowe urządzenia, wybierając pozycję **+ Nowy** na karcie **urządzenia** .

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

Na platformie Azure IoT Central można utworzyć reguły, aby automatycznie monitorować dane telemetryczne i wyzwalacze urządzenia po spełnieniu co najmniej jednego warunku. Akcje mogą obejmować wysyłanie powiadomień e-mail lub wyzwalanie akcji programu Microsoft PowerShell lub akcji elementu webhook w celu wysyłania danych do innych usług.

Utworzona aplikacja do monitorowania zużycia wody ma trzy wstępnie skonfigurowane reguły.

### <a name="view-rules"></a>Wyświetl reguły

1. Wybierz pozycję **reguły** w okienku po lewej stronie.

   ![Okienko reguł](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Wybierz **alert o wysokim poziomie pH**, który jest jednym ze wstępnie skonfigurowanych reguł w aplikacji.

     ![Alert o wysokim poziomie pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert`Zasada jest skonfigurowana do sprawdzania pod kątem warunku `Acidity (pH)` `greater than` `Max flow threshold` . Maksymalny próg przepływu to właściwość chmury zdefiniowana w **szablonie urządzenia** przenośnego. Wartość `Max flow threshold` jest ustawiana na wystąpienie urządzenia.

Teraz Utwórzmy akcję poczty e-mail.

Aby dodać akcję do reguły:

1. Wybierz pozycję **+ poczta e-mail**.
1. Wprowadź **alert o wysokim poziomie pH** jako przyjazną **nazwę wyświetlaną** dla akcji.
1. Wprowadź adres e-mail **skojarzony z kontem** usługi Azure IoT Central w systemie.
1. Opcjonalnie wprowadź notatkę, która ma zostać dołączona do tekstu wiadomości e-mail.
1. Wybierz pozycję **gotowe** , aby zakończyć akcję.
1. Wybierz pozycję **Zapisz** , aby zapisać i aktywować nową regułę.

W ciągu kilku minut otrzymasz wiadomość e-mail po spełnieniu skonfigurowanego warunku.

> [!NOTE]
> Aplikacja wysyła wiadomość e-mail za każdym razem, gdy spełniony jest warunek. Wybierz pozycję **Wyłącz** , aby wyłączyć zasadę, aby nie otrzymywać wiadomości e-mail z reguły zautomatyzowanej.
  
Aby utworzyć nową regułę:

* Wybierz pozycję **+ Nowy** na karcie **zasady** w okienku po lewej stronie.

## <a name="configure-jobs"></a>Konfigurowanie zadań

Na platformie Azure IoT Central zadania umożliwiają wyzwalanie aktualizacji właściwości urządzenia lub chmury na wielu urządzeniach. Oprócz właściwości można także użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. Usługa Azure IoT Central automatyzuje przepływ pracy.

1. Wybierz pozycję **zadania** w okienku po lewej stronie.
1. Wybierz pozycję **+ Nowy** i skonfiguruj co najmniej jedno zadanie.

## <a name="customize-your-application"></a>Dostosowywanie aplikacji

Jako Konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

1. Wybierz pozycję **Administracja**  >  **Dostosuj aplikację**.
1. Aby wybrać obraz do przekazania jako **logo aplikacji**, wybierz przycisk **Zmień** .
1. Aby wybrać obraz **ikony przeglądarki** , który będzie wyświetlany na kartach przeglądarki, wybierz przycisk **Zmień** .
1. Możesz również zastąpić domyślne **kolory przeglądarki** , dodając szesnastkowe kody kolorów html.

   ![Opcje dla logo aplikacji, ikony przeglądarki i kolory przeglądarki](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Możesz również zmienić obrazy aplikacji, wybierając pozycję **Administracja**  >  **Ustawienia aplikacji**. Aby wybrać obraz do przekazania jako obraz aplikacji, wybierz przycisk **Wybierz obraz** .
1. Na koniec możesz również zmienić **motyw** , wybierając ikonę **Ustawienia** w prawym górnym rogu aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, usuń ją.

1. Wybierz pozycję **Administracja** w lewym okienku aplikacji IoT Central platformy Azure.
1. Wybierz pozycję **Ustawienia aplikacji**, a następnie wybierz pozycję **Usuń** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o 

> [!div class="nextstepaction"]
> [Pojęcia dotyczące monitorowania zużycia wody](./concepts-waterconsumptionmonitoring-architecture.md).
