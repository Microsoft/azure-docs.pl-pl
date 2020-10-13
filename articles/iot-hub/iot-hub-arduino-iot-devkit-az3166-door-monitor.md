---
title: Wyślij wiadomość e-mail, gdy drzwi są otwierane przy użyciu Azure Functions
description: Monitoruj czujnik magnetyczny, aby wykryć, kiedy drzwi są otwarte, i użyj Azure Functions do wysyłania powiadomień e-mail.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: d4f77cec793e13ff943c173d88948eb5b5de0957
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356251"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitor drzwi — używanie Azure Functions i SendGrid, wysyłanie wiadomości e-mail po otwarciu drzwi           

Zestawu deweloperskiego IoT DevKit zawiera wbudowany czujnik magnetyczny. W tym projekcie wykryjesz obecność lub brak pobliskich silnych pól magnetycznych — w tym przypadku pochodzi z małego, stałego magnesu.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie dowiesz się:
- Jak używać czujnika magnetycznego zestawu deweloperskiego IoT DevKit do wykrywania przenoszenia w pobliżu magnesu.
- Jak wysłać powiadomienie na adres e-mail za pomocą usługi SendGrid.

> [!NOTE]
> W celu praktycznego użycia tego projektu wykonaj następujące zadania:
> - Zainstaluj magnes do krawędzi drzwi.
> - Zainstaluj DevKit na drzwiach jamb blisko magnesu. Otwarcie lub zamknięcie drzwi spowoduje wyzwolenie czujnika, co spowodowało odebranie powiadomienia e-mail o zdarzeniu.

## <a name="what-you-need"></a>Potrzebne elementy

Zakończ [przewodnik wprowadzenie](iot-hub-arduino-iot-devkit-az3166-get-started.md) , aby:

* Połączono z usługą DevKit do Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli go nie masz, możesz zarejestrować się, korzystając z jednej z następujących metod:

* Aktywuj [bezpłatne 30-dniowe konto wersji próbnej Microsoft Azure](https://azure.microsoft.com/free/).
* Zarezerwuj środki na korzystanie z [platformy Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , jeśli jesteś subskrybentem MSDN lub Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Wdrażanie usługi SendGrid na platformie Azure

[SendGrid](https://sendgrid.com/) to oparta na chmurze platforma dostarczania poczty e-mail. Ta usługa zostanie użyta do wysyłania powiadomień e-mail.

> [!NOTE]
> Jeśli usługa SendGrid została już wdrożona, możesz bezpośrednio przeprowadzić [wdrożenie IoT Hub na platformie Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Wdrożenie SendGrid

Aby zainicjować obsługę administracyjną usług platformy Azure, użyj przycisku **Wdróż na platformie Azure** . Ten przycisk umożliwia szybkie i łatwe wdrażanie projektów typu "open source" do Microsoft Azure.

Kliknij przycisk **Wdróż na platformie Azure** poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Jeśli jeszcze nie zalogowano się na koncie platformy Azure, zaloguj się teraz. 

Zobaczysz teraz formularz rejestracji w usłudze SendGrid.

![Wdrożenie SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Wypełnij formularz rejestracji:

   * **Grupa zasobów**: Utwórz grupę zasobów, aby hostować usługę SendGrid, lub Użyj istniejącej. Zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Name**: nazwa usługi SendGrid. Wybierz unikatową nazwę, która różni się od innych usług, które mogą mieć.

   * **Hasło**: usługa wymaga hasła, które nie będzie używane dla wszystkich elementów w tym projekcie.

   * **Adres e-mail**: usługa SendGrid wyśle weryfikację na ten adres e-mail.

Zaznacz opcję **Przypnij do pulpitu nawigacyjnego** , aby łatwiej znaleźć tę aplikację w przyszłości, a następnie kliknij przycisk **Kup** , aby przesłać formularz logowania.
 
### <a name="sendgrid-api-key-creation"></a>Tworzenie klucza interfejsu API SendGrid

Po zakończeniu wdrażania kliknij je, a następnie kliknij przycisk **Zarządzaj** . Zostanie wyświetlona strona konta SendGrid, na której należy zweryfikować swój adres e-mail.

![SendGrid Zarządzaj](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na stronie SendGrid kliknij pozycję **Ustawienia**  >  **klucze interfejsu API**  >  **Utwórz klucz interfejsu API**.

![SendGrid najpierw Utwórz interfejs API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na stronie **Tworzenie klucza interfejsu API** wprowadź **nazwę klucza interfejsu API** i kliknij przycisk **Utwórz & widok**.

![SendGrid Create API Second](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klucz interfejsu API jest wyświetlany tylko raz. Pamiętaj, aby bezpiecznie skopiować i zapisać go, ponieważ jest używany w następnym kroku.

## <a name="deploy-iot-hub-in-azure"></a>Wdrażanie IoT Hub na platformie Azure

Poniższe kroki spowodują udostępnienie innych usług związanych z usługą Azure IoT i wdrożenie Azure Functions dla tego projektu.

Kliknij przycisk **Wdróż na platformie Azure** poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Zostanie wyświetlony formularz rejestracji.

![Wdrożenie IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Wypełnij pola w formularzu rejestracji.

   * **Grupa zasobów**: Utwórz grupę zasobów, aby hostować usługę SendGrid, lub Użyj istniejącej. Zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nazwa Centrum IoT**: Nazwa Centrum IoT. Wybierz unikatową nazwę, która różni się od innych usług, które mogą mieć.

   * **Jednostka SKU usługi IoT Hub**: F1 (ograniczona do jednej subskrypcji) jest bezpłatna. Więcej informacji o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Z wiadomości e-mail**: to pole powinno mieć ten sam adres e-mail, który był używany podczas konfigurowania usługi SendGrid.

Zaznacz opcję **Przypnij do pulpitu nawigacyjnego** , aby łatwiej znaleźć tę aplikację w przyszłości, a następnie kliknij przycisk **Kup** , aby przejść do następnego kroku.
 
## <a name="build-and-upload-the-code"></a>Kompiluj i przekaż kod

Następnie załaduj przykładowy kod w VS Code i Zainicjuj obsługę wymaganych usług platformy Azure.

### <a name="start-vs-code"></a>Rozpocznij VS Code

- Upewnij się, że DevKit **nie** jest podłączony do komputera.
- Uruchom program VS Code.
- Połącz DevKit z komputerem.

> [!NOTE]
> Po uruchomieniu VS Code może zostać wyświetlony komunikat o błędzie z informacją o tym, że nie może znaleźć Arduino IDE ani powiązanego pakietu tablicy. Jeśli zostanie wyświetlony ten błąd, Zamknij VS Code, ponownie uruchom środowisko IDE Arduino, a VS Code powinna prawidłowo zlokalizować ścieżkę środowiska IDE Arduino.

### <a name="open-arduino-examples-folder"></a>Otwórz Arduino przykładów folderów

Rozwiń sekcję **przykłady Arduino** po lewej stronie, przejdź do **przykładów zestawu deweloperskiego AZ3166 > AzureIoT**, a następnie wybierz pozycję **DoorMonitor**. Ta akcja powoduje otwarcie nowego okna VS Code z folderem projektu.

![Mini-Solution — przykłady](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Możesz również otworzyć przykładową aplikację z palety poleceń. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie Znajdź i wybierz **Arduino: przykłady**.

### <a name="provision-azure-services"></a>Udostępnianie usług platformy Azure

W oknie rozwiązanie Uruchom zadanie aprowizacji w chmurze:
- Typ `Ctrl+P` (macOS: `Cmd+P` ).
- Wprowadź `task cloud-provision` w podanym polu tekstowym.

W terminalu VS Code interaktywny wiersz polecenia przeprowadzi Cię przez proces aprowizacji wymaganych usług platformy Azure. Zaznacz wszystkie te same elementy z listy monitów, które zostały wcześniej zainicjowane w ramach [wdrażania IoT Hub na platformie Azure](#deploy-iot-hub-in-azure).

![Udostępnianie w chmurze](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Jeśli podczas próby zalogowania się do platformy Azure Strona zawiesza się w stanie ładowania, zapoznaj się z [sekcją "Strona zawiesza się po zalogowaniu" w artykule IoT DevKit — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) , aby rozwiązać ten problem. 

### <a name="build-and-upload-the-device-code"></a>Kompilowanie i przekazywanie kodu urządzenia

Następnie Przekaż kod dla urządzenia.

#### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload` .

2. Na terminalu zostanie wyświetlony komunikat z prośbą o wprowadzenie do trybu konfiguracji. Aby to zrobić, przytrzymaj przycisk A, a następnie wypchnij i zwolnij przycisk Resetuj. Na ekranie zostanie wyświetlony numer identyfikacyjny DevKit i *konfigurację*słowa.

#### <a name="macos"></a>macOS

1. Ustaw DevKit do trybu konfiguracji: przytrzymaj przycisk A, a następnie wypchnij i zwolnij przycisk Resetuj. Na ekranie zostanie wyświetlony komunikat "Konfiguracja".

2. Kliknij `Cmd+P` , aby uruchomić `task device-upload` .

#### <a name="verify-upload-and-run-the-sample-app"></a>Weryfikowanie, przekazywanie i uruchamianie przykładowej aplikacji

Parametry połączenia, które są pobierane z kroku [udostępniania usług platformy Azure](#provision-azure-services) , są teraz ustawione. 

VS Code następnie zacznie weryfikować i przekazywać szkic Arduino do DevKit.

![Zrzut ekranu przedstawia Visual Studio Code sprawdzanie i przekazywanie szkicu Arduino.](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamia kod.

> [!NOTE]
> Czasami może zostać wyświetlony komunikat o błędzie "błąd: AZ3166: nieznany pakiet". Ten błąd występuje, gdy indeks pakietu tablicy nie został poprawnie odświeżony. Aby rozwiązać ten problem, zapoznaj się z [sekcją opracowywanie często ZAdawanych pytań dotyczących IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testowanie projektu

Program jest inicjowany najpierw, gdy DevKit jest w obecności trwałego pola magnetycznego.

Po inicjacji `Door closed` jest wyświetlana na ekranie. W przypadku zmiany pola magnetycznego stan zmieni się na `Door opened` . Za każdym razem, gdy zmieni się stan drzwi, otrzymasz powiadomienie e-mail. (Odbieranie tych wiadomości e-mail może potrwać do 5 minut).

![Magnets blisko czujnika: drzwi zamknięte](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnets blisko czujnika: drzwi zamknięte")

![Magnes przesunięty z czujnika: otwarte drzwi](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnes przesunięty z czujnika: otwarte drzwi")

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z tematem [często zadawanych pytań dotyczących usługi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub Połącz się przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak podłączyć urządzenie DevKit do akceleratora rozwiązania do monitorowania zdalnego usługi Azure IoT i użyć usługi SendGrid do wysłania wiadomości e-mail. Oto sugerowany następny krok:[Omówienie akceleratora rozwiązań zdalnego monitorowania usługi Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
