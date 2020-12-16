---
title: 'Samouczek: Tworzenie aplikacji do monitorowania jakości wody przy użyciu usługi Azure IoT Central'
description: 'Samouczek: informacje na temat tworzenia aplikacji do monitorowania jakości wody przy użyciu szablonów aplikacji IoT Central platformy Azure.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 6e8f9b8b33d391c4e13c6076a8589d336455d8c0
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590226"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Samouczek: Tworzenie aplikacji do monitorowania jakości wody na platformie Azure IoT Central



Ten samouczek przeprowadzi Cię przez proces tworzenia aplikacji do monitorowania jakości wody na platformie Azure IoT Central. Aplikację można utworzyć przy użyciu szablonu aplikacji do **monitorowania jakości wody** w systemie Azure IoT Central.

Z tego samouczka dowiesz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Użyj szablonu **monitorowanie jakości wody** , aby utworzyć aplikację do monitorowania jakości wody.
> * Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora.
> * Eksploruj szablon urządzenia do monitorowania jakości wody.
> * Eksploruj symulowane urządzenia.
> * Eksplorowanie i konfigurowanie reguł.
> * Konfigurowanie zadań.
> * Dostosuj znakowanie aplikacji, używając białych etykiet.

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy, aby masz subskrypcję platformy Azure do ukończenia tego samouczka. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Tworzenie aplikacji do monitorowania jakości wody na platformie Azure IoT Central

W tej sekcji użyjesz szablonu **monitorowania jakości wody** platformy Azure IoT Central, aby utworzyć aplikację do monitorowania jakości wody.

1. Przejdź do [strony głównej usługi Azure IoT Central](https://aka.ms/iotcentral).

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. W przeciwnym razie zaloguj się przy użyciu konto Microsoft:

    ![Zaloguj się do konta organizacji](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Wybierz pozycję **Kompiluj** w okienku po lewej stronie IoT Central platformy Azure i wybierz pozycję Karta **rządowa** . W okienku instytucji rządowych są wyświetlane kilka szablonów aplikacji dla instytucji rządowych.

    ![Szablony aplikacji dla instytucji rządowych](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Wybierz szablon aplikacji do **monitorowania jakości wody** . Ten szablon aplikacji obejmuje szablon urządzenia z jakością wody, symulowane urządzenia, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.

1. Wybierz pozycję **Utwórz aplikację**. Zostanie otwarte okienko **Nowa aplikacja** z następującymi elementami:

    * **Nazwa aplikacji**: Domyślnie nazwa aplikacji to **monitorowanie jakości wody** , a po nim unikatowy ciąg identyfikacyjny generowany przez usługę Azure IoT Central. Jeśli chcesz, możesz wprowadzić nazwę wyświetlaną lub później zmienić nazwę aplikacji.
    * **Adres URL**: możesz wprowadzić dowolny adres URL lub później zmienić wartość adresu URL.
    * Jeśli masz subskrypcję platformy Azure, wprowadź wartości dla **katalogu**, **subskrypcji platformy Azure** i **regionu**. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe.

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zobacz [Tworzenie aplikacji](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) — Szybki Start.

1. Wybierz przycisk **Utwórz** w lewej dolnej części strony.

    ![Strona nowej aplikacji platformy Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Azure IoT Central informacje o rozliczeniach nowych aplikacji](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Aplikacja do monitorowania jakości wody została teraz utworzona przy użyciu szablonu **monitorowania jakości wody** dla systemu Azure IoT Central.

Twoja nowa aplikacja zawiera te wstępnie skonfigurowane składniki:

* Pulpity nawigacyjne operatorów
* Szablony urządzeń do monitorowania jakości wody
* Symulowane urządzenia do monitorowania jakości wody
* Zasady i zadania
* Znakowanie używające białych etykiet

Aplikację można modyfikować w dowolnym momencie.

Następnie Eksploruj aplikację i wprowadzaj pewne dostosowania.

## <a name="explore-and-customize-the-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora

Po utworzeniu aplikacji zostanie otwarte okienko **pulpitu nawigacyjnego szerokiej jakości wody** .

   ![Pulpit nawigacyjny monitorowania jakości wody](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Jako Konstruktor można tworzyć i dostosowywać widoki na pulpicie nawigacyjnym do użycia przez operatorów. Ale przed podjęciem próby dostosowania należy najpierw zapoznać się z pulpitem nawigacyjnym.

Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które omówiono w następnej sekcji.

Pulpit nawigacyjny zawiera następujące rodzaje kafelków:

* **Kafelek obrazu narzędzia Wide World wodne**: pierwszy kafelek w lewym górnym rogu pulpitu nawigacyjnego jest obrazem, który pokazuje fikcyjne narzędzie o nazwie Wide World. Kafelek można dostosować tak, aby używał własnego obrazu, lub można go usunąć.

* **Średnia liczba kafelków kluczowych wskaźników wydajności**: wskaźniki KPI, takie jak **średnia pH w ciągu ostatnich 30 minut** , znajdują się u góry okienka pulpitu nawigacyjnego. Można dostosować kafelki KPI i ustawić je na inny typ i zakres czasu.

* **Mapa obszaru monitorowania wody**: usługa Azure IoT Central używa Azure Maps, które można bezpośrednio ustawić w aplikacji, aby pokazać lokalizację urządzenia. Możesz również zmapować informacje o lokalizacji z aplikacji na urządzenie, a następnie użyć Azure Maps, aby wyświetlić informacje na mapie. Umieść kursor nad mapą i wypróbuj kontrolki.

* **Średni schemat dystrybucji cieplnej rozkładu pH**: możesz wybrać różne wykresy wizualizacji, aby wyświetlić dane telemetryczne urządzenia w sposób najbardziej odpowiedni dla Twojej aplikacji.

* **Wykres liniowy wskaźników jakości krytycznych**: można wizualizować dane telemetryczne urządzenia kreślone jako wykres liniowy w przedziale czasu.  

* **Koncentracja wykresu słupkowego agentów chemicznych**: można wizualizować dane telemetryczne urządzenia na wykresie słupkowym.

* **Przycisk akcji**: pulpit nawigacyjny zawiera kafelek akcji, które operator może zainicjować bezpośrednio z poziomu pulpitu nawigacyjnego monitorowania. Przykładem takich akcji jest Resetowanie właściwości urządzenia.

* **Kafelki listy właściwości**: pulpit nawigacyjny zawiera wiele kafelków właściwości, które reprezentują informacje o wartościach progowych, informacje o kondycji urządzenia i informacje o konserwacji.

### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Jako Konstruktor można dostosować widoki na pulpicie nawigacyjnym do użycia przez operatorów.

1. Wybierz pozycję **Edytuj** , aby dostosować okienko **pulpitu nawigacyjnego szerokiej jakości wody** . Możesz dostosować pulpit nawigacyjny, wybierając polecenie z menu **Edycja** . Gdy pulpit nawigacyjny jest w trybie edycji, można dodać nowe kafelki lub można skonfigurować istniejące pliki.

    ![Edytowanie pulpitu nawigacyjnego](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Wybierz pozycję **+ Nowy** , aby utworzyć nowy pulpit nawigacyjny, który można skonfigurować. Można mieć wiele pulpitów nawigacyjnych i nawigować między nimi z menu Pulpit nawigacyjny.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Eksplorowanie szablonu urządzenia do monitorowania jakości wody

Szablon urządzenia w usłudze Azure IoT Central definiuje możliwości urządzenia. Dostępne możliwości to dane telemetryczne, właściwości i polecenia. Jako Konstruktor można definiować szablony urządzeń w usłudze Azure IoT Central, które reprezentują możliwości połączonych urządzeń. Można również tworzyć symulowane urządzenia do testowania szablonu i aplikacji urządzenia.

Utworzona aplikacja do monitorowania jakości wody obejmuje szablon urządzenia do monitorowania jakości wody.

Aby wyświetlić szablon urządzenia:

1. Wybierz pozycję **Szablony urządzeń** w okienku po lewej stronie aplikacji na platformie Azure IoT Central.
1. Z listy szablonów urządzeń wybierz pozycję **monitor jakości wody**. Ten szablon urządzenia zostanie otwarty.

    ![Szablon urządzenia](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Zalecane jest dostosowanie następujących ustawień szablonu urządzenia:

1. Z menu szablonu urządzenia wybierz pozycję **Dostosuj**.
1. Przejdź do typu telemetrii **temperatury** .
1. Zmień wartość **Nazwa wyświetlana** na **zgłoszoną temperaturę**.
1. Zmień jednostkę miary lub ustaw wartość **minimalną** i **maksymalną**.
1. Wybierz pozycję **Zapisz**.

#### <a name="add-a-cloud-property"></a>Dodaj właściwość chmury

1. W menu szablonu urządzenia wybierz pozycję **Właściwość chmury**.
1. Aby dodać nową właściwość chmury, wybierz pozycję **+ Dodaj właściwość chmury**. Na platformie Azure IoT Central można dodać właściwość, która jest istotna dla urządzenia, ale nie powinna zostać wysłana przez urządzenie. Przykładem takiej właściwości jest próg alertu specyficzny dla obszaru instalacji, informacji o zasobach lub informacji o konserwacji.
1. Wybierz pozycję **Zapisz**.

### <a name="explore-views"></a>Eksploruj widoki

Szablon urządzenia do monitorowania jakości wody jest dostępny ze wstępnie zdefiniowanymi widokami. Widoki definiują sposób, w jaki operatory widzą dane urządzenia i ustawiają właściwości chmury. Eksploruj widoki i ćwicz wprowadzanie zmian.

  ![Widoki szablonów urządzeń](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Jeśli wprowadzisz jakiekolwiek zmiany, pamiętaj o wybraniu opcji **Publikuj** w celu opublikowania szablonu urządzenia.

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia

1. Wybierz pozycję **+ Nowy** , aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia.
1. Utwórz niestandardowy szablon urządzenia lub wybierz szablon urządzenia z wykazu urządzeń Azure IoT.

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

Aplikacja do monitorowania jakości wody utworzona na podstawie szablonu aplikacji ma dwa symulowane urządzenia. Te urządzenia są mapowane na szablon urządzenia do monitorowania jakości wody.

### <a name="view-the-devices"></a>Wyświetlanie urządzeń

1. Wybierz pozycję **urządzenia** w okienku po lewej stronie aplikacji.

   ![Urządzenia](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Wybierz jedno symulowane urządzenie.

    ![Wybierz urządzenie 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Na karcie **właściwości chmury** Zmień wartość **progową kwasowości (pH)** z **8** na **9**.
1. Przejrzyj kartę **Właściwości urządzenia** i kartę **pulpit nawigacyjny urządzenia** .

> [!NOTE]
> Wszystkie karty zostały skonfigurowane z **widoków szablonów urządzeń**.

### <a name="add-new-devices"></a>Dodaj nowe urządzenia

Na karcie **urządzenia** wybierz pozycję **+ Nowy** , aby dodać nowe urządzenie.

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

Na platformie Azure IoT Central można utworzyć reguły, które automatycznie monitorują dane telemetryczne urządzenia. Te reguły wyzwalają akcję w przypadku spełnienia dowolnego z nich warunków. Jedną z możliwych czynności jest wysłanie powiadomień e-mail. Inne możliwości obejmują akcję automatyzacji lub akcję elementu webhook w celu wysyłania danych do innych usług.

Utworzona aplikacja do monitorowania jakości wody ma dwie wstępnie skonfigurowane reguły.

### <a name="view-rules"></a>Wyświetl reguły

1. Wybierz pozycję **reguły** w okienku po lewej stronie aplikacji.

   ![Reguły](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Wybierz **alert o wysokim poziomie pH**, który jest jednym ze wstępnie skonfigurowanych reguł w aplikacji.

   ![Reguła alertu o wysokiej wartości pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   Reguła **alertu o wysokiej wartości pH** jest skonfigurowana do sprawdzania stanu kwasowości (pH), która jest większa niż 8.

Następnie Dodaj akcję poczty e-mail do reguły:

1. Wybierz pozycję **+ poczta e-mail**.
1. W polu **Nazwa wyświetlana** wprowadź **alert o wysokim poziomie pH**.
1. W polu **do** wprowadź adres e-mail skojarzony z kontem usługi Azure IoT Central.
1. Opcjonalnie wprowadź notatkę, która ma zostać dołączona do tekstu wiadomości e-mail.
1. Wybierz pozycję **gotowe** , aby zakończyć akcję.
1. Wybierz pozycję **Zapisz** , aby zapisać i aktywować nową regułę.

W ciągu kilku minut powinna zostać wysłana wiadomość e-mail, gdy zostanie spełniony skonfigurowany warunek.

> [!NOTE]
> Aplikacja wysyła wiadomość e-mail za każdym razem, gdy spełniony jest warunek. Wybierz opcję **Wyłącz** , aby zrezygnować z otrzymywania zautomatyzowanej wiadomości e-mail z tej reguły.
  
Aby utworzyć nową regułę, wybierz pozycję **reguły** w okienku po lewej stronie aplikacji, a następnie wybierz pozycję **+ Nowy**.

## <a name="configure-jobs"></a>Konfigurowanie zadań

Za pomocą zadań IoT Central platformy Azure można wyzwolić aktualizacje właściwości urządzenia lub chmury na wielu urządzeniach. Można również użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. Usługa Azure IoT Central automatyzuje przepływ pracy.

1. Wybierz pozycję **zadania** w okienku po lewej stronie aplikacji.
1. Wybierz pozycję **+ Nowy** i skonfiguruj co najmniej jedno zadanie.

## <a name="customize-your-application"></a>Dostosowywanie aplikacji

Jako Konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

1. Wybierz pozycję **Administracja**  >  **Dostosuj aplikację**.
1. W obszarze **logo aplikacji** wybierz pozycję **Zmień** , aby wybrać obraz do przekazania jako logo.
1. W obszarze **ikona przeglądarki** wybierz pozycję **Zmień** , aby wybrać obraz wyświetlany na kartach przeglądarki.
1. W obszarze **kolory przeglądarki** można zastąpić wartości domyślne kodami szesnastkowymi w formacie HTML.
1. Wybierz pozycję **Ustawienia** , aby zmienić wartość **motywu**.

   ![Dostosowywanie aplikacji](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Aktualizowanie obrazu aplikacji

1. Wybierz pozycję  >  **Ustawienia aplikacji** administracyjnej.

1. Użyj przycisku **Wybierz obraz** , aby wybrać obraz do przekazania jako obraz aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z aplikacji, Usuń aplikację, wykonując następujące czynności:

1. Otwórz kartę **Administracja** w okienku po lewej stronie aplikacji.
1. Wybierz pozycję **Ustawienia aplikacji** i wybierz przycisk **Usuń** .

    ![Usuwanie aplikacji](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o 

> [!div class="nextstepaction"]
> [pojęcia dotyczące monitorowania jakości wody](./concepts-waterqualitymonitoring-architecture.md).
