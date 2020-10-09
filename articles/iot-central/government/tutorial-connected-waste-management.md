---
title: 'Samouczek: Tworzenie połączonej aplikacji zarządzania odpadami przy użyciu usługi Azure IoT Central'
description: 'Samouczek: informacje o tworzeniu tworzenia połączonej aplikacji zarządzania odpadami przy użyciu szablonów aplikacji platformy Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 53304f14286577bf0bc25b2a66442286527c5140
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90972430"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Samouczek: Tworzenie połączonej aplikacji zarządzania odpadami w IoT Central



W tym samouczku przedstawiono sposób tworzenia aplikacji do zarządzania odpadami w usłudze Azure IoT Central przy użyciu szablonu IoT Central **połączonego zarządzania odpadami** . 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]

> * Tworzenie połączonej aplikacji **zarządzania odpadami przy użyciu IoT Central platformy** Azure
> * Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
> * Eksploruj szablon urządzenia z odłączonymi odpadami
> * Eksplorowanie symulowanych urządzeń
> * Eksplorowanie i konfigurowanie reguł
> * Konfigurowanie zadań
> * Dostosowywanie oznakowania aplikacji za pomocą Whitelabeling

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:
-  Zalecana jest subskrypcja platformy Azure. Opcjonalnie możesz użyć bezpłatnej 7-dniowej wersji próbnej. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Tworzenie połączonej aplikacji zarządzania odpadami w IoT Central

W tej sekcji użyjesz **szablonu zarządzania odpadami** IoT Central platformy Azure, aby utworzyć przyłączoną aplikację zarządzania odpadami w programie IoT Central.

Aby utworzyć nową aplikację usługi Azure IoT Central połączonego zarządzania odpadami:  

1. Przejdź do witryny sieci Web [strony głównej platformy Azure IoT Central](https://aka.ms/iotcentral) .

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. w przeciwnym razie zaloguj się przy pomocy konto Microsoft:

    ![Określanie konta organizacji](./media/tutorial-connectedwastemanagement/sign-in.png)

2. W lewym okienku kliknij pozycję **kompilacja** , a następnie wybierz pozycję Karta **rządowa** . Na stronie Administracja jest wyświetlana kilka szablonów aplikacji rządowych.

    ![Kompiluj szablony aplikacji dla instytucji rządowych](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Wybierz szablon aplikacja **zarządzania odpadami** . Ten szablon zawiera przykładowy szablon urządzenia połączonego pojemnika, symulowane urządzenie, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.    

2. Kliknij pozycję **Utwórz aplikację**. spowoduje to otwarcie formularza tworzenia **nowej aplikacji** z następującymi polami:
    * **Nazwa aplikacji**. Domyślnie aplikacja korzysta z *zarządzania przyłączonymi odpadami* , a następnie unikatowy ciąg identyfikatora, który IoT Central generowany. Opcjonalnie możesz wybrać przyjazną nazwę aplikacji. Nazwę aplikacji można zmienić później.
    * **URL** — Opcjonalnie możesz wybrać żądany adres URL. Adres URL można zmienić później. 
    * Jeśli masz subskrypcję platformy Azure, wprowadź swój *katalog, subskrypcję platformy Azure i region*. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](../core/quick-deploy-iot-central.md).

5. Kliknij przycisk **Utwórz** w dolnej części strony. 

    ![Strona aplikacji Azure IoT Central tworzenie połączonych odpadów](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![IoT Central Azure — tworzenie połączonych informacji rozliczeniowych](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Utworzono przyłączoną aplikację zarządzania odpadami przy użyciu **szablonu zarządzania odpadami**w usłudze Azure IoT Central. 

Gratulacje! Nowo utworzona aplikacja zawiera wstępnie skonfigurowane:
* Przykładowe pulpity nawigacyjne operatorów
* Przykład wstępnie zdefiniowanych połączonych odpadów — szablony urządzeń bin
* Symulowane połączone urządzenia bin
* Wstępnie skonfigurowane reguły i zadania
* Przykładowe oznakowanie przy użyciu białej etykietki 

Jest to Twoja aplikacja i możesz ją zmodyfikować w dowolnym momencie. Teraz eksplorujemy aplikację i wprowadzasz pewne dostosowania.  

## <a name="explore-and-customize-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
Po utworzeniu aplikacji można wystawić na **pulpicie nawigacyjnym zarządzanie odpadami w całej sieci**.

   ![Pulpit nawigacyjny zarządzania połączonymi odpadami](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Jako Konstruktor można tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Przed rozpoczęciem dostosowywania skontaktuj się z pulpitem nawigacyjnym. 

> [!NOTE]
> Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które zostaną zbadane w następnej sekcji. 

Pulpit nawigacyjny składa się z różnych rodzajów kafelków:

* ***Kafelek obrazów narzędzi obejmujących cały świat***: pierwszy kafelek na pulpicie nawigacyjnym to kafelek obrazu fikcyjnego narzędzia do "całego świata". Możesz dostosować kafelek i umieścić własny obraz lub usunąć go. 

* ***Kafelek obrazu bin śmieci***: możesz użyć kafelków obrazów i zawartości, aby utworzyć wizualną reprezentację monitorowanego urządzenia wraz z tekstem opisowym. 

* ***KAFELEK KPI na poziomie wypełnienia***: kafelek wyświetla wartość raportowaną przez czujnik *poziomu wypełnienia* w pojemniku. *Poziom wypełnienia* i inne czujniki, takie jak *miernik odor* lub *waga* w pojemniku, mogą być monitorowane zdalnie. Operator może podejmować działania, takie jak wysyłanie ciężarówki do pobrania. 

*  ***Mapa obszaru monitorowania odpadów***: mapa używa Azure Maps, którą można skonfigurować bezpośrednio w usłudze Azure IoT Central. Kafelek Mapa Wyświetla lokalizację urządzenia. Spróbuj umieścić kursor na mapie i wypróbuj kontrolki na mapie, takie jak powiększenie, Powiększ lub rozwiń.

     ![Mapa pulpitu nawigacyjnego zarządzania połączonymi odpadami](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Wypełnienie, odor, wykres słupkowy poziomu wagi**: można wizualizować jedno lub wiele danych telemetrii urządzenia na wykresie słupkowym. Możesz również rozwinąć wykres słupkowy.  

  ![Wykres słupkowy pulpitu nawigacyjnego zarządzania odpadami](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Kafelek zawartości usługi Fields**: pulpit nawigacyjny zawiera link do integracji z usługami pól systemu Dynamics 365 w aplikacji Azure IoT Central. Na przykład możesz użyć usług pól, aby utworzyć bilety do wysyłania biletów na śmieci. 


### <a name="customize-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego 

Jako Konstruktor można dostosować widoki na pulpicie nawigacyjnym dla operatorów. Możesz spróbować:
1. Kliknij pozycję **Edytuj** , aby dostosować **pulpit nawigacyjny Zarządzanie połączonymi odpadami w całym świecie**. Możesz dostosować pulpit nawigacyjny, klikając menu **Edycja** . Gdy pulpit nawigacyjny jest w trybie **edycji** , możesz dodać nowe kafelki lub skonfigurować 

    ![Edytowanie pulpitu nawigacyjnego](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Możesz również kliknąć pozycję **+ Nowy** , aby utworzyć nowy pulpit nawigacyjny i skonfigurować go od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i można przechodzić między pulpitami nawigacyjnymi z menu Pulpit nawigacyjny. 

## <a name="explore-connected-waste-bin-device-template"></a>Eksploruj szablon urządzenia z odłączonymi odpadami

Szablon urządzenia w usłudze Azure IoT Central definiuje możliwość urządzenia, które może być Telemetria, właściwości lub polecenia. Jako Konstruktor można zdefiniować szablony urządzeń, które reprezentują możliwości urządzeń, z którymi będziesz się łączyć. 
 

Aplikacja do **zarządzania odpadami** jest dostarczana z przykładowym szablonem urządzenia połączonego pojemnika.

Aby wyświetlić szablon urządzenia:

1. Kliknij pozycję **Szablony urządzeń** w lewym okienku aplikacji w IoT Central. 

    ![Zrzut ekranu przedstawiający listę szablonów urządzeń w aplikacji](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Na liście szablony urządzeń zostanie wyświetlony **pojemnik połączone odpady**. Otwórz program, klikając nazwę.

3. Zapoznaj się z możliwościami szablonu urządzenia. Zobaczysz, że definiuje on czujniki, takie jak *poziom wypełnienia*, *odor*, *waga*, *Lokalizacja*i inne.

   ![Zrzut ekranu przedstawiający szczegóły szablonu urządzenia bin podłączonych odpadów](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Dostosowywanie szablonu urządzenia

Spróbuj dostosować następujące elementy:
1. Przejdź do **dostosowywania** z menu szablonu urządzenia
2. Znajdź `Odor meter` Typ telemetrii
3. Zaktualizuj **nazwę wyświetlaną** `Odor meter` do `Odor level`
4. Możesz również spróbować zaktualizować jednostkę miary lub ustawić wartość *minimalną* i *maksymalną wartość*
5. **Zapisz** wszystkie zmiany 

### <a name="add-a-cloud-property"></a>Dodaj właściwość chmury 

1. Przejdź do **właściwości chmury** z menu szablonu urządzenia
2. Dodaj nową właściwość chmury, klikając pozycję **+ Dodaj właściwość chmury**. W IoT Central można dodać właściwość, która jest istotna dla urządzenia, ale nie powinna zostać wysłana przez urządzenie. Przykładowo Właściwość chmury może być progiem alertu specyficznym dla obszaru instalacji, informacji o zasobach lub informacji o konserwacji oraz innych informacji. 
3. **Zapisz** wszystkie zmiany 
 
### <a name="views"></a>Widoki 
* Szablon urządzenia połączone odpady jest dostarczany ze wstępnie zdefiniowanymi widokami. Przejrzyj widoki i możesz wprowadzić aktualizacje. Widoki definiują sposób, w jaki operatory zobaczą dane urządzenia, ale również umieszczają właściwości chmury. 

  ![Widoki szablonów urządzeń](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Opublikuj 

* Jeśli zostały wprowadzone jakiekolwiek zmiany, upewnij się, że szablon urządzenia jest **opublikowany** . 

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia 

* Wybierz pozycję **+ Nowy** , aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia. Można utworzyć niestandardowy szablon urządzenia od podstaw lub wybrać szablon urządzenia z wykazu urządzeń platformy Azure. 

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

W IoT Central można tworzyć symulowane urządzenia do testowania szablonu i aplikacji urządzenia. 

Aplikacja do **zarządzania połączonymi odpadami** ma dwa symulowane urządzenia zamapowane na szablon urządzenia z dołączonymi odpadami. 

### <a name="to-view-the-devices"></a>Aby wyświetlić urządzenia:

1. Przejdź do **urządzenia** z IoT Central lewym okienku. 

   ![Devices](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Wybierz i kliknij urządzenie bin połączone odpady.  

     ![Urządzenie 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Przejdź do karty **właściwości chmury** , a następnie spróbuj zaktualizować `Bin full alert threshold` wartość z `95` do `100` . 
* Eksploruj kartę **Właściwości urządzenia** i kartę **pulpit nawigacyjny urządzenia** . 

> [!NOTE]
> Należy pamiętać, że wszystkie karty zostały skonfigurowane z **widoków szablonów urządzeń**.

### <a name="add-new-devices"></a>Dodaj nowe urządzenia

* Nowe urządzenia można dodać, klikając pozycję **+ Nowy** na karcie **urządzenia** . 

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

W usłudze Azure IoT Central można tworzyć reguły automatycznego monitorowania w ramach telemetrii urządzenia i wyzwalać akcje po spełnieniu jednego lub większej liczby warunków. Akcje mogą obejmować wysyłanie powiadomień e-mail, wyzwalanie akcji Microsoft Flow lub akcji elementu webhook w celu wysyłania danych do innych usług.

Aplikacja do **zarządzania połączonymi odpadami** ma cztery przykładowe reguły.

### <a name="to-view-rules"></a>Aby wyświetlić reguły:
1. Przejdź do **reguły** z okienka IoT Central lewe okienko

   ![Reguły](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Wybierz **pełny alert dotyczący zasobnika**

     ![Pełny alert dotyczący bin](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. `Bin full alert`Sprawdza **warunek** `Fill level is greater than or equal to Bin full alert threshold` .

    `Bin full alert threshold`Jest to *Właściwość chmury* zdefiniowana w `Connected waste bin` szablonie urządzenia. 

Teraz Utwórzmy akcję poczty e-mail.

### <a name="create-an-email-action"></a>Utwórz akcję poczty e-mail
Aby skonfigurować akcję poczty e-mail na liście akcji reguły:
1. Wybierz pozycję **+ poczta e-mail**. 
2. Wprowadź *alert o wysokim poziomie pH* jako przyjazną **nazwę wyświetlaną** dla akcji.
3. Wprowadź adres e-mail skojarzony z kontem IoT Central w programie **do**. 
4. Opcjonalnie wprowadź notatkę, która ma zostać dołączona do tekstu wiadomości e-mail.
5. Wybierz pozycję **gotowe** , aby zakończyć akcję.
6. Wybierz pozycję **Zapisz** , aby zapisać i aktywować nową regułę. 

Po spełnieniu skonfigurowanego **warunku** należy otrzymać wiadomość e-mail.

> [!NOTE]
> Aplikacja wyśle wiadomość e-mail za każdym razem, gdy warunek zostanie spełniony. **Wyłączenie** reguły w celu zatrzymania otrzymywania wiadomości e-mail z reguły zautomatyzowanej. 
  
Aby utworzyć nową regułę: 
1. Wybierz pozycję **+ Nowy** w obszarze **zasady** w okienku po lewej stronie.

## <a name="configure-jobs"></a>Konfigurowanie zadań

W IoT Central zadania umożliwiają wyzwalanie aktualizacji właściwości urządzenia lub chmury na wielu urządzeniach. Oprócz właściwości można także użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. IoT Central automatyzuje przepływ pracy. 

1. Przejdź do obszaru **zadania** w okienku po lewej stronie. 
2. Kliknij pozycję **+ Nowy** i skonfiguruj co najmniej jedno zadanie. 


## <a name="customize-your-application"></a>Dostosowywanie aplikacji 

Jako Konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

### <a name="to-change-the-application-theme"></a>Aby zmienić motyw aplikacji:

1. Przejdź do pozycji **administracja > Dostosuj aplikację**.
3. Użyj przycisku **Zmień** , aby wybrać obraz do przekazania jako **logo aplikacji**.
4. Użyj przycisku **Zmień** , aby wybrać obraz **ikony przeglądarki** , który będzie wyświetlany na kartach przeglądarki.
5. Możesz również zastąpić domyślne **kolory przeglądarki** , dodając szesnastkowe kody kolorów html.

   ![IoT Central dostosowywanie aplikacji przez platformę Azure](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Możesz również zmienić obrazy aplikacji, przechodząc do opcji **administracja > ustawienia aplikacji** i **Wybierz obraz** przycisk, aby wybrać obraz do przekazania jako obraz aplikacji.
7. Na koniec możesz również zmienić **kompozycję** , klikając pozycję **Ustawienia** w obszarze tytuł aplikacji.

  
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. Otwórz kartę Administracja w lewym okienku aplikacji IoT Central.
2. Wybierz pozycję Ustawienia aplikacji, a następnie kliknij przycisk Usuń u dołu strony.

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat
 
> [!div class="nextstepaction"]
> [Pojęcia związane z zarządzaniem odpadami](./concepts-connectedwastemanagement-architecture.md)
