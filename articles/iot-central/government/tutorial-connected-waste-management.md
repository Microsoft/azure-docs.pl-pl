---
title: 'Samouczek: Tworzenie połączonej aplikacji zarządzania odpadami przy użyciu usługi Azure IoT Central'
description: 'Samouczek: uczenie się tworzenia połączonej aplikacji zarządzania odpadami przy użyciu szablonów aplikacji IoT Central platformy Azure'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 65245b3c4b7e18670682f7e9e890453e32337644
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820083"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Samouczek: Tworzenie połączonej aplikacji zarządzania odpadami

W tym samouczku pokazano, jak za pomocą usługi Azure IoT Central utworzyć przyłączoną aplikację zarządzania odpadami. 

W tym celu dowiesz się, jak: 

> [!div class="checklist"]
> Aby utworzyć aplikację, użyj szablonu *zarządzania odpadami* w usłudze Azure IoT Central.
> Eksploruj i dostosowuj pulpit nawigacyjny operatora. Zapoznaj się z szablonem urządzenia z dołączonymi odpadami.
> Eksploruj symulowane urządzenia.
> Eksplorowanie i konfigurowanie reguł.
> Konfigurowanie zadań.
> Dostosuj znakowanie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Zalecana jest subskrypcja platformy Azure. Alternatywnie możesz użyć bezpłatnej 7-dniowej wersji próbnej. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Tworzenie aplikacji na platformie Azure IoT Central

W tej sekcji użyjesz szablonu Zarządzanie połączonymi odpadami, aby utworzyć aplikację w usłudze Azure IoT Central. Oto kroki tej procedury:

1. Przejdź do [usługi Azure IoT Central](https://aka.ms/iotcentral).

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. W przeciwnym razie zaloguj się przy użyciu konto Microsoft:

    ![Zrzut ekranu przedstawiający logowanie do firmy Microsoft.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. W okienku po lewej stronie wybierz pozycję **kompilacja**. Następnie wybierz kartę **Administracja** . Na stronie Administracja jest wyświetlana kilka szablonów aplikacji rządowych.

    ![Zrzut ekranu strony kompilacji usługi Azure IoT Central.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Wybierz szablon aplikacja **zarządzania odpadami** . Ten szablon zawiera przykładowy szablon urządzenia połączonego pojemnika, symulowane urządzenie, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.    

1. Wybierz pozycję **Utwórz aplikację**, aby otworzyć okno dialogowe **Nowa aplikacja** . Wypełnij informacje dla następujących pól:
    * **Nazwa aplikacji**. Domyślnie aplikacja używa **zarządzania połączonymi odpadami**, a następnie unikatowego ciągu identyfikatora, który generuje usługa Azure IoT Central. Opcjonalnie możesz wybrać przyjazną nazwę aplikacji. Nazwę aplikacji można zmienić później.
    * **Adres URL**. Opcjonalnie możesz wybrać żądany adres URL. Adres URL można zmienić później. 
    * **Plan cenowy**. Jeśli masz subskrypcję platformy Azure, wprowadź swój katalog, subskrypcję platformy Azure i region w odpowiednich polach okna dialogowego **Informacje o rozliczeniach** . Jeśli nie masz subskrypcji, wybierz opcję **bezpłatna** , aby włączyć 7-dniową subskrypcję wersji próbnej i uzupełnij wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zobacz [Szybki Start — Tworzenie aplikacji IoT Central platformy Azure](../core/quick-deploy-iot-central.md).

1. W dolnej części strony wybierz pozycję **Utwórz**. 

    ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowej aplikacji w usłudze Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Zrzut ekranu przedstawiający okno dialogowe informacje o rozliczeniach IoT Central platformy Azure.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Nowo utworzona aplikacja zawiera wstępnie skonfigurowane:
* Przykładowe pulpity nawigacyjne operatora.
* Przykładowe wstępnie zdefiniowane szablony urządzeń bin
* Symulowane połączone urządzenia bin.
* Zasady i zadania.
* Przykładowe znakowanie. 

Jest to Twoja aplikacja i możesz ją zmodyfikować w dowolnym momencie. Teraz eksplorujemy aplikację i wprowadzasz pewne dostosowania.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 

Zapoznaj się z **pulpitem nawigacyjnym zarządzanie odpadami na całym świecie**, który jest widoczny po utworzeniu aplikacji.

   ![Zrzut ekranu przedstawiający pulpit nawigacyjny zarządzania odpadami na całym świecie.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Jako Konstruktor można tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Po pierwsze Przyjrzyjmy się pulpitowi nawigacyjnemu. 

>[!NOTE]
>Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które będą widoczne w następnej sekcji. 

Pulpit nawigacyjny składa się z różnych kafelków:

* **Kafelek obrazów narzędzi obejmujących cały świat**: pierwszy kafelek na pulpicie nawigacyjnym to kafelek obrazu fikcyjnej postaci "odpady rozległe". Możesz dostosować kafelek i umieścić go we własnym obrazie lub usunąć. 

* **Kafelek obrazu pojemnika z odpadami**: możesz użyć kafelków obrazów i zawartości, aby utworzyć wizualną reprezentację monitorowanego urządzenia wraz z opisem. 

* **KAFELEK KPI na poziomie wypełnienia**: ten kafelek wyświetla wartość raportowaną przez czujnik *poziomu wypełnienia* w pojemniku. Poziom wypełnienia i inne czujniki, takie jak *licznik odor* lub *waga* w pojemniku, mogą być monitorowane zdalnie. Operator może wykonać akcję, na przykład wysłanie wózka do pobrania z kosza. 

* **Mapa obszaru monitorowania odpadów**: ten kafelek używa Azure Maps, które można skonfigurować bezpośrednio w usłudze Azure IoT Central. Kafelek Mapa przedstawia lokalizację urządzenia. Spróbuj umieścić kursor na mapie i wypróbuj kontrolki na mapie, takie jak Powiększ, Powiększ lub rozwiń.

     ![Zrzut ekranu mapy pulpitu nawigacyjnego połączonego zarządzania odpadami.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Wypełnienie, odor, wykres słupkowy poziomu wagi**: można wizualizować jeden lub wiele rodzajów danych telemetrii urządzenia na wykresie słupkowym. Możesz również rozwinąć wykres słupkowy.  

  ![Zrzut ekranu przedstawiający wykres słupkowy pulpitu nawigacyjnego połączonego zarządzania odpadami.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Usługi Field Services**: pulpit nawigacyjny zawiera link umożliwiający integrację z usługami pól usługi Dynamics 365 w aplikacji Azure IoT Central. Na przykład możesz użyć usług pól, aby utworzyć bilety do wysyłania na śmieci usługi zbierania danych. 


### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego 

Możesz dostosować pulpit nawigacyjny, wybierając menu **Edycja** . Następnie możesz dodać nowe kafelki lub skonfigurować istniejące. Oto jak wygląda pulpit nawigacyjny w trybie edycji: 

![Zrzut ekranu pulpitu nawigacyjnego szablonu zarządzania odpadami w trybie edycji.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Możesz również wybrać opcję **+ Nowy** , aby utworzyć nowy pulpit nawigacyjny i skonfigurować go od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i można przełączać się między pulpitami nawigacyjnymi z menu Pulpit nawigacyjny. 

## <a name="explore-the-device-template"></a>Eksplorowanie szablonu urządzenia

Szablon urządzenia w usłudze Azure IoT Central definiuje możliwości urządzenia, które mogą uwzględniać dane telemetryczne, właściwości lub polecenia. Jako Konstruktor można zdefiniować szablony urządzeń, które reprezentują możliwości urządzeń, z którymi będziesz się łączyć. 

Aplikacja do zarządzania połączonymi odpadami zawiera przykładowy szablon dla połączonego urządzenia bin.

Aby wyświetlić szablon urządzenia:

1. W obszarze Azure IoT Central w lewym okienku aplikacji wybierz pozycję **Szablony urządzeń**. 

    ![Zrzut ekranu przedstawiający listę szablonów urządzeń w aplikacji.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. Na liście **Szablony urządzeń** wybierz pozycję **połączone odpady bin**.

1. Zapoznaj się z możliwościami szablonu urządzenia. Można zobaczyć, że definiuje on czujniki, takie jak **poziom wypełnienia**, **odor**, **waga** i **Lokalizacja**.

   ![Zrzut ekranu przedstawiający szczegóły szablonu urządzenia połączonego pojemnika.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Spróbuj dostosować następujące elementy:
1. Z menu szablonu urządzenia wybierz pozycję **Dostosuj**.
1. Znajdź Typ telemetrii **miernika odor** .
1. Zaktualizuj **nazwę wyświetlaną** **miernika odor** do **poziomu odor**.
1. Spróbuj zaktualizować jednostkę miary lub ustaw wartość **minimalną** i **maksymalną**.
1. Wybierz pozycję **Zapisz**. 

### <a name="add-a-cloud-property"></a>Dodaj właściwość chmury 

Oto kroki tej procedury:
1. W menu szablonu urządzenia wybierz pozycję **Właściwość chmury**.
1. Wybierz pozycję **+ Dodaj właściwość chmury**. Na platformie Azure IoT Central można dodać właściwość, która jest istotna dla urządzenia, ale nie powinna być wysyłana przez urządzenie. Na przykład właściwość chmury może być progiem alertu specyficznym dla obszaru instalacji, informacji o zasobach lub informacji o konserwacji. 
1. Wybierz pozycję **Zapisz**. 
 
### <a name="views"></a>Widoki 
Szablon urządzenia połączone odpady jest dostarczany ze wstępnie zdefiniowanymi widokami. Przejrzyj widoki i zaktualizuj je, jeśli chcesz. Widoki definiują sposób, w jaki operatory zobaczą dane urządzenia i właściwości chmury danych wejściowych. 

  ![Zrzut ekranu przedstawiający widoki szablonów urządzeń zarządzania odpadami.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publikowanie 

W przypadku wprowadzenia jakichkolwiek zmian pamiętaj, aby opublikować szablon urządzenia. 

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia 

Aby utworzyć nowy szablon urządzenia, wybierz pozycję **+ Nowy** i postępuj zgodnie z instrukcjami. Możesz utworzyć niestandardowy szablon urządzenia od podstaw lub wybrać szablon urządzenia z wykazu urządzeń platformy Azure. 

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

Na platformie Azure IoT Central można tworzyć symulowane urządzenia do testowania szablonu i aplikacji urządzenia. 

Aplikacja do zarządzania połączonymi odpadami ma dwa symulowane urządzenia skojarzone z szablonem urządzenia z dołączonymi odpadami. 

### <a name="view-the-devices"></a>Wyświetlanie urządzeń

1. W lewym okienku usługi Azure IoT Central wybierz pozycję **urządzenie**. 

   ![Zrzut ekranu przedstawiający urządzenia szablonu zarządzania odpadami.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Wybierz pozycję **połączone odpady pojemnika** .  

     ![Zrzut ekranu przedstawiający właściwości urządzenia związanego z szablonem zarządzania odpadami.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Przejdź do karty **właściwości chmury** . Zaktualizuj wartość **progu pełnego alertu** dotyczącego usługi Bin z **95** do **100**. 

Zapoznaj się z kartami **Właściwości urządzenia** i **pulpit nawigacyjny urządzenia** . 

> [!NOTE]
> Wszystkie karty zostały skonfigurowane z widoków szablonów urządzeń.

### <a name="add-new-devices"></a>Dodaj nowe urządzenia

Nowe urządzenia można dodać, wybierając pozycję **+ Nowy** na karcie **urządzenia** . 

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

Na platformie Azure IoT Central można utworzyć reguły, aby automatycznie monitorować dane telemetryczne urządzenia i wyzwalać akcje po spełnieniu jednego lub większej liczby warunków. Akcje mogą obejmować wysyłanie powiadomień e-mail, wyzwalanie akcji w programie w celu automatyzacji lub uruchamianie akcji elementu webhook w celu wysyłania danych do innych usług.

Aplikacja do zarządzania połączonymi odpadami ma cztery przykładowe reguły.

### <a name="view-rules"></a>Wyświetl reguły
1. W lewym okienku IoT Central platformy Azure wybierz pozycję **reguły**.

   ![Zrzut ekranu przedstawiający reguły szablonu zarządzania odpadami.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Wybierz pozycję **Kup pełny alert**.

     ![Zrzut ekranu przedstawiający pełny alert dotyczący bin.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. **Pełny alert dotyczący zasobnika** sprawdza następujący warunek: **poziom wypełnienia jest większy lub równy progowi pełnego alertu dotyczącego** zapętlenia.

    **Próg pełnego alertu** dotyczącego usługi bin jest właściwością chmury, która jest zdefiniowana w szablonie urządzenia połączonego pojemnika. 

Teraz Utwórzmy akcję poczty e-mail.

### <a name="create-an-email-action"></a>Utwórz akcję poczty e-mail

Na liście **Akcje** reguły można skonfigurować akcję poczty e-mail:
1. Wybierz pozycję **+ poczta e-mail**. 
1. W obszarze **Nazwa wyświetlana** wprowadź **alert o wysokim poziomie pH**.
1. W polu **do** wprowadź adres e-mail skojarzony z kontem usługi Azure IoT Central. 
1. Opcjonalnie wprowadź notatkę, która ma zostać dołączona do tekstu wiadomości e-mail.
1. Wybierz pozycję **gotowe**  >  **Zapisywanie**. 

Otrzymasz teraz wiadomość e-mail, gdy zostanie spełniony skonfigurowany warunek.

>[!NOTE]
>Aplikacja wysyła wiadomość e-mail za każdym razem, gdy spełniony jest warunek. Wyłączenie reguły w celu zatrzymania otrzymywania wiadomości e-mail z reguły zautomatyzowanej. 
  
Aby utworzyć nową regułę, w lewym okienku **reguły** wybierz pozycję **+ Nowy**.

## <a name="configure-jobs"></a>Konfigurowanie zadań

Na platformie Azure IoT Central zadania umożliwiają wyzwalanie aktualizacji właściwości urządzenia lub chmury na wielu urządzeniach. Można również użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. Usługa Azure IoT Central automatyzuje przepływ pracy. 

1. W lewym okienku IoT Central platformy Azure wybierz pozycję **zadania**. 
1. Wybierz pozycję **+ Nowy** i skonfiguruj co najmniej jedno zadanie. 

## <a name="customize-your-application"></a>Dostosowywanie aplikacji 

Jako Konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

### <a name="change-the-application-theme"></a>Zmień motyw aplikacji

Oto kroki tej procedury:
1. Przejdź do pozycji **Administracja**  >  **Dostosuj aplikację**.
1. Wybierz pozycję **Zmień** , aby wybrać obraz do przekazania do **logo aplikacji**.
1. Wybierz pozycję **Zmień** , aby wybrać obraz do przekazania **ikonie przeglądarki** (obraz, który będzie wyświetlany na kartach przeglądarki).
1. Możesz również zastąpić domyślne kolory przeglądarki, dodając szesnastkowe kody kolorów HTML. Użyj w tym celu pól **nagłówka** i **akcentu** .

   ![Zrzut ekranu przedstawiający szablon zarządzania połączonymi odpadami dostosowuje aplikację.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Możesz również zmienić obrazy aplikacji. Wybierz pozycję  >  **Ustawienia aplikacji** administracyjnej  >  **Wybierz obraz** , aby wybrać obraz do przekazania jako obraz aplikacji.
1. Na koniec możesz również zmienić motyw, wybierając pozycję **Ustawienia** w witrynie tytułowej aplikacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. W lewym okienku aplikacji IoT Central platformy Azure wybierz pozycję **Administracja**.
1. Wybierz pozycję **Ustawienia aplikacji**  >  **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pojęcia związane z zarządzaniem odpadami](./concepts-connectedwastemanagement-architecture.md)
