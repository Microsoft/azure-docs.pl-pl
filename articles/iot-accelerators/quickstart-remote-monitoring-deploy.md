---
title: Testowanie opartego na chmurze rozwiązania do monitorowania zdalnego IoT — Azure | Microsoft Docs
description: W tym przewodniku Szybki start pokazano, jak wdrożyć akcelerator rozwiązań usługi Azure IoT do zdalnego monitorowania oraz zalogować się do pulpitu nawigacyjnego rozwiązania i korzystać z niego.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/25/2019
ms.author: dobett
ms.openlocfilehash: 8d007fa7100a9880878e0b748c2c38402e5ea192
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540238"
---
# <a name="quickstart-try-a-cloud-based-remote-monitoring-solution"></a>Szybki start: testowanie opartego na chmurze rozwiązania do monitorowania zdalnego

W tym przewodniku Szybki start pokazano, jak wdrożyć akcelerator rozwiązania do monitorowania zdalnego usługi Azure IoT. W tym rozwiązaniu opartym na chmurze użyjesz strony **Pulpit nawigacyjny**, aby wyświetlić urządzenia symulowane na mapie, i strony **Konserwacja**, aby zareagować na alert dotyczący ciśnienia z symulowanego urządzenia typu „chiller” (chłodziarki). Ten akcelerator rozwiązań może posłużyć jako punkt wyjścia dla własnej implementacji lub do nauki.

W początkowym wdrożeniu akcelerator rozwiązań jest skonfigurowany dla firmy o nazwie Contoso. Jako operator w firmie Contoso zarządzasz różnego typu urządzeniami, takimi jak chłodziarki, wdrożonymi w różnych środowiskach fizycznych. Chłodziarka wysyła dane telemetryczne dotyczące temperatury, wilgotności i ciśnienia do akceleratora rozwiązania do monitorowania zdalnego.

W tym przewodniku Szybki Start jest wdrażana **podstawowa** wersja akceleratora rozwiązania dla celów testowych i demonstracyjnych, które minimalizują koszty. Aby uzyskać więcej informacji o różnych wersjach, które można wdrożyć, zobacz [podstawowe i standardowe wdrożenia](iot-accelerators-remote-monitoring-deploy-cli.md#basic-and-standard-deployments).

Do wykonania kroków tego przewodnika Szybki start jest potrzebna aktywna subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W przypadku wdrażania akceleratora rozwiązania w ramach subskrypcji platformy Azure musisz ustawić niektóre opcje konfiguracji.

Zaloguj się do witryny [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) przy użyciu poświadczeń konta platformy Azure.

Kliknij kafelek **Zdalne monitorowanie**. Na stronie **Zdalne monitorowanie** kliknij pozycję **Wypróbuj teraz**:

![Wybieranie pozycji Zdalne monitorowanie](./media/quickstart-remote-monitoring-deploy/remotemonitoring.png)

Wybierz **mikrousługi w języku C#** jako **Opcje wdrażania**. Implementacje Java i C# mają te same funkcje.

W polu **Nazwa rozwiązania** wprowadź unikatową nazwę akceleratora rozwiązania do monitorowania zdalnego. W tym przewodniku Szybki start używamy nazwy **contoso-rm**.

W polach **Subskrypcja** i **Region** wybierz wartości, których chcesz użyć do wdrożenia akceleratora rozwiązania. Zwykle jest wybierany region znajdujący się najbliżej. W tym przewodniku Szybki start używamy regionu **Wschodnie stany USA**.
Możesz wybrać opcję **Visual Studio Enterprise**, ale w tym celu musisz być [administratorem globalnym lub użytkownikiem](iot-accelerators-permissions.md).

Aby rozpocząć wdrożenie, kliknij pozycję **Utwórz**. Ten proces trwa co najmniej pięć minut:

![Szczegóły rozwiązania do monitorowania zdalnego](./media/quickstart-remote-monitoring-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logowanie się do rozwiązania

Po zakończeniu wdrożenia w Twojej subskrypcji platformy Azure na kafelku rozwiązania zostanie wyświetlony zielony znacznik wyboru i tekst **Gotowe**. Teraz możesz zalogować się do pulpitu nawigacyjnego akceleratora rozwiązania do monitorowania zdalnego.

Na stronie **Aprowizowane rozwiązania** kliknij nowy akcelerator rozwiązania do monitorowania zdalnego:

![Wybieranie nowego rozwiązania](./media/quickstart-remote-monitoring-deploy/choosenew.png)

Możesz przejrzeć informacje o akceleratorze rozwiązania do monitorowania zdalnego w wyświetlonym panelu. Wybierz pozycję **Przejdź do własnego akceleratora rozwiązania**, aby wyświetlić akcelerator rozwiązania do monitorowania zdalnego:

![Panel rozwiązania](./media/quickstart-remote-monitoring-deploy/solutionpanel.png)

Kliknij pozycję **Zaakceptuj**, aby zaakceptować żądanie uprawnień. W przeglądarce zostanie wyświetlony pulpit nawigacyjny rozwiązania do monitorowania zdalnego:

[![Pulpit nawigacyjny rozwiązania](./media/quickstart-remote-monitoring-deploy/solutiondashboard-inline.png)](./media/quickstart-remote-monitoring-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="view-your-devices"></a>Wyświetlanie urządzeń

Pulpit nawigacyjny rozwiązania przedstawia następujące informacje o symulowanych urządzeniach firmy Contoso:

* Panel **Statystyki urządzeń** zawiera podsumowanie alertów i łączną liczbę urządzeń. W domyślnym wdrożeniu firma Contoso ma 10 urządzeń symulowanych różnych typów.

* Panel **Lokalizacje urządzeń** pokazuje lokalizacje fizyczne urządzeń. Kolor pinezki wskazuje, czy są dostępne alerty z urządzenia.

* Panel **Alerty** podaje szczegółowe informacje o alertach z urządzeń.

* Panel **Telemetria** pokazuje dane telemetryczne z urządzeń. Możesz wyświetlać różne strumienie telemetrii, klikając typy telemetrii u góry.

* Panel **Analiza** przedstawia połączone informacje o alertach z urządzeń.

## <a name="respond-to-an-alert"></a>Odpowiadanie na alert

Operator w firmie Contoso może monitorować urządzenia na pulpicie nawigacyjnym rozwiązania. Panel **Statystyki urządzeń** wskazuje obecność alertów krytycznych, a w panelu **Alerty** widać, że większość z nich pochodzi z chłodziarki. W przypadku chłodziarek firmy Contoso ciśnienie wewnętrzne ponad 250 PSI wskazuje, że urządzenie nie działa poprawnie.

### <a name="identify-the-issue"></a>Identyfikowanie problemu

Na stronie **Pulpit nawigacyjny** w panelu **Alerty** jest wyświetlany alert **Zbyt duże ciśnienie chłodziarki**. Chłodziarka jest reprezentowana przez czerwoną pinezkę na mapie (może być konieczne przesunięcie i powiększenie widoku na mapie):

[![Pulpit nawigacyjny pokazuje alert o ciśnieniu i urządzenie na mapie](./media/quickstart-remote-monitoring-deploy/dashboardalarm-inline.png)](./media/quickstart-remote-monitoring-deploy/dashboardalarm-expanded.png#lightbox)

W panelu **Alerty** kliknij pozycję **...** w kolumnie **Eksploruj** obok reguły **Zbyt duże ciśnienie chłodziarki**. Ta akcja spowoduje przejście do strony **Konserwacja** — można na niej wyświetlić szczegóły reguły, która wyzwoliła alert.

Na stronie konserwacji **Zbyt duże ciśnienie chłodziarki** są wyświetlane szczegóły reguły, która wyzwoliła alerty. Ponadto przedstawia czas wystąpienia alertów i urządzenie, które je wyzwoliło:

[![Na stronie obsługa jest wyświetlana lista alertów, które zostały wyzwolone](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-expanded.png#lightbox)

Zidentyfikowano problem, który wyzwolił alert, i skojarzone urządzenie. Następne kroki umożliwiają operatorowi potwierdzenie alertu i rozwiązanie problemu.

### <a name="fix-the-issue"></a>Rozwiązywanie problemu

Aby pokazać innym operatorom, że pracujesz nad alertem, zaznacz go, a następnie zmień wartość ustawienia **Stan alertu** na wartość **Potwierdzono**:

[![Wybierz i Potwierdź alert](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-expanded.png#lightbox)

Wartość w kolumnie stanu zmieni się na **Potwierdzono**.

Aby wykonać działanie dotyczące chłodziarki, przewiń w dół do pozycji **Informacje pokrewne**, wybierz chłodziarkę z listy **Urządzenia z alertami**, a następnie wybierz pozycję **Zadania**:

[![Wybierz urządzenie i Zaplanuj akcję](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-expanded.png#lightbox)

W panelu **Zadania** wybierz pozycję **Uruchom metodę** a następnie metodę **EmergencyValveRelease**. Dodaj nazwę zadania **ChillerPressureRelease** i kliknij przycisk **Zastosuj**. Te ustawienia pozwalają utworzyć dla Ciebie zadanie, które jest wykonywane natychmiast.

Aby wyświetlić stan zadania, wróć do strony **Konserwacja** i wyświetl listę zadań w widoku **Zadania**. Może być konieczne odczekanie kilku sekund, zanim będzie można zobaczyć, że zadanie zostało uruchomione:

[![Stan zadań w widok Zadania](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-expanded.png#lightbox)

### <a name="check-the-pressure-is-back-to-normal"></a>Sprawdzanie, czy ciśnienie wróciło do normalnego poziomu

Aby wyświetlić dane telemetryczne dotyczące ciśnienia chłodziarki, przejdź do strony **Pulpit nawigacyjny**, wybierz pozycję **Ciśnienie** w panelu telemetrii i upewnij się, że chłodziarka **chiller-02.0** ma normalne ciśnienie:

[![Ciśnienie z powrotem do normalnego](./media/quickstart-remote-monitoring-deploy/pressurenormal-inline.png)](./media/quickstart-remote-monitoring-deploy/pressurenormal-expanded.png#lightbox)

Aby zamknąć zdarzenie, przejdź do strony **Konserwacja**, wybierz alert i ustaw stan **Zamknięto**:

[![Wybieranie i zamykanie alertu](./media/quickstart-remote-monitoring-deploy/maintenanceclose-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceclose-expanded.png#lightbox)

Wartość w kolumnie stanu zmieni się na **Zamknięto**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do samouczków, nie usuwaj wdrożenia akceleratora rozwiązania do monitorowania zdalnego.

Jeśli akcelerator rozwiązania nie jest już potrzebny, usuń go ze strony [wstępnie zainicjowanych rozwiązań](https://www.azureiotsolutions.com/Accelerators#dashboard) , wybierając ją, a następnie klikając pozycję **Usuń rozwiązanie**:

![Zrzut ekranu przedstawiający stronę "rozwiązania udostępnione" z wyróżnionym akceleratorem rozwiązania.](media/quickstart-remote-monitoring-deploy/deletesolution.png)


![Usuwanie rozwiązania](media/quickstart-remote-monitoring-deploy/deletesolution-page.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono akcelerator rozwiązania do monitorowania zdalnego i wykonano zadanie monitorowania przy użyciu urządzeń symulowanych w domyślnym wdrożeniu firmy Contoso.

Aby dowiedzieć się więcej na temat akceleratora rozwiązań korzystającego z urządzeń symulowanych, przejdź do następującego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie urządzeń IoT](iot-accelerators-remote-monitoring-monitor.md)