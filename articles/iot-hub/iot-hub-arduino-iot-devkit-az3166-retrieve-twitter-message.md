---
title: Pobieranie komunikatu usługi Twitter za pomocą Azure Functions | Microsoft Docs
description: Użyj czujnika ruchu, aby wykrywać wstrząsy i używać Azure Functions, aby znaleźć losowy Tweet z określonym przez Ciebie określonym identyfikatorem.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147899"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Wstrząsanie i wstrząsanie tweetu — pobieranie komunikatu usługi Twitter za pomocą Azure Functions

W tym projekcie dowiesz się, jak za pomocą czujnika ruchu wyzwolić zdarzenie przy użyciu Azure Functions. Aplikacja pobiera losowy Tweet z #hashtag skonfigurowanym we własnym szkicie Arduino. Tweet zostanie wyświetlony na ekranie DevKit.

## <a name="what-you-need"></a>Potrzebne elementy

Zakończ [przewodnik wprowadzenie](./iot-hub-arduino-iot-devkit-az3166-get-started.md) , aby:

* DevKit połączenie z siecią Wi-Fi.
* Przygotuj środowisko programistyczne.

Aktywna subskrypcja platformy Azure. Jeśli go nie masz, możesz zarejestrować się, korzystając z jednej z następujących metod:

* Aktywuj [bezpłatne 30-dniowe konto wersji próbnej Microsoft Azure](https://azure.microsoft.com/free/)
* Zarezerwuj środki na korzystanie z [platformy Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , jeśli jesteś subskrybentem MSDN lub Visual Studio

## <a name="open-the-project-folder"></a>Otwieranie folderu projektu

Zacznij od otwarcia folderu projektu. 

### <a name="start-vs-code"></a>Rozpocznij VS Code

* Upewnij się, że DevKit jest podłączony do komputera.

* Uruchom program VS Code.

* Połącz DevKit z komputerem.

   > [!NOTE]
   > Podczas uruchamiania VS Code może zostać wyświetlony komunikat o błędzie informujący o tym, że nie można znaleźć Arduino IDE lub powiązanego pakietu tablicy. Jeśli wystąpi ten błąd, Zamknij VS Code i ponownie uruchom środowisko IDE Arduino. VS Code powinien teraz znaleźć ścieżkę IDE Arduino.

### <a name="open-the-arduino-examples-folder"></a>Otwórz folder przykładów Arduino

Rozwiń sekcję **przykłady Arduino** po lewej stronie, przejdź do **przykładów zestawu deweloperskiego AZ3166 > AzureIoT**, a następnie wybierz pozycję **ShakeShake**. Zostanie otwarte nowe okno VS Code, w którym zostanie wyświetlony folder projektu. Jeśli nie widzisz sekcji zestawu DEWELOPERSKIEGO AZ3166, upewnij się, że urządzenie jest prawidłowo połączone i ponownie uruchom Visual Studio Code.  
![mini-Solution — przykłady](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Możesz również otworzyć przykładowy projekt z palety poleceń. Kliknij przycisk `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie Znajdź i wybierz **Arduino: przykłady**.

## <a name="provision-azure-services"></a>Udostępnianie usług platformy Azure

W oknie rozwiązanie Uruchom zadanie za pomocą `Ctrl+P` (macOS:), `Cmd+P` wprowadzając polecenie `task cloud-provision` .

W terminalu VS Code interaktywny wiersz polecenia przeprowadzi Cię przez proces aprowizacji wymaganych usług platformy Azure:

![Zrzut ekranu przedstawia wiersz polecenia interakcyjnego terminalu Visual Studio Code.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Jeśli podczas próby zalogowania się do platformy Azure Strona zawiesza się w stanie ładowania, zapoznaj się z [krokem "zawiesz na stronie logowania" w temacie często zadawane pytania dotyczące usługi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modyfikuj #hashtag

Otwórz `ShakeShake.ino` i wyszukaj ten wiersz kodu:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Zastąp ciąg `iot` w nawiasach klamrowych z preferowanym elementem hasztagów. DevKit później pobiera losowy Tweet, który zawiera element hasztagów określony w tym kroku.

## <a name="deploy-azure-functions"></a>Wdrażanie usługi Azure Functions

Użyj `Ctrl+P` (macOS: `Cmd+P` ) do uruchomienia `task cloud-deploy` , aby rozpocząć wdrażanie kodu Azure Functions:

![Zrzut ekranu przedstawia Visual Studio Code, w którym można uruchomić zadanie Cloud-Deploy w celu wdrożenia Azure Functions kodu.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Czasami funkcja platformy Azure może nie działać prawidłowo. Aby rozwiązać ten problem, w przypadku wystąpienia tego problemu zapoznaj się z [sekcją "błąd kompilacji" dotyczącej często zadawanych pytań dotyczących IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Kompilowanie i przekazywanie kodu urządzenia

Następnie Skompiluj i przekaż kod urządzenia.

### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload` .

2. Na terminalu zostanie wyświetlony komunikat z prośbą o wprowadzenie do trybu konfiguracji. W tym celu:

   * Przycisk przytrzymania

   * Wypchnij i zwolnij przycisk Resetuj.

3. Na ekranie zostanie wyświetlony Identyfikator DevKit i "Konfiguracja".

### <a name="macos"></a>macOS

1. Przełącz DevKit do trybu konfiguracji:

   Naciśnij przycisk, a następnie wypchnij i zwolnij przycisk Reset. Na ekranie zostanie wyświetlony komunikat "Konfiguracja".

2. Użyj `Cmd+P` do uruchomienia `task device-upload` , aby ustawić parametry połączenia, które są pobierane z `task cloud-provision` kroku.

### <a name="verify-upload-and-run"></a>Weryfikuj, przekazuj i uruchamiaj

Teraz parametry połączenia są ustawiane, sprawdza i przekazuje aplikację, a następnie uruchamia ją. 

1. VS Code zaczyna weryfikować i przekazywać szkic Arduino do DevKit:

   ![Zrzut ekranu przedstawia Visual Studio Code sprawdzanie i przekazywanie szkicu Arduino.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. Zestaw deweloperski wykonuje ponowny rozruch i uruchamia kod.

Może zostać wyświetlony komunikat o błędzie "błąd: AZ3166: nieznany pakiet". Ten błąd występuje, gdy indeks pakietu tablicy nie został poprawnie odświeżony. Aby rozwiązać ten problem, sprawdź [błąd "nieznany pakiet" w temacie IoT DevKit — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testowanie projektu

Po zainicjowaniu aplikacji kliknij i zwolnij przycisk a, a następnie delikatnie wstrząsnąć tablicę DevKit. Ta akcja spowoduje pobranie losowego tweetu zawierającego określony wcześniej element hasztagów. W ciągu kilku sekund zostanie wyświetlony Tweet na ekranie DevKit:

### <a name="arduino-application-initializing"></a>Trwa inicjowanie aplikacji Arduino...

![Arduino — inicjowanie aplikacji](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Naciśnij klawisz, aby wstrząsnąć...

![Naciśnij klawisz-A-do-wstrząsania](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Gotowe do wstrząsania...

![Gotowe do wstrząsania](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Trwa przetwarzanie...

![Przetwarzanie](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Naciśnij klawisz B, aby odczytać...

![Naciśnij klawisz-B-do odczytu](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Wyświetl losowy Tweet...

![Wyświetl-a-losowo-Tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Naciśnij przycisk ponownie, a następnie wstrząsać o nowym tweetie.
- Naciśnij przycisk B, aby przewinąć resztę tweetu.

## <a name="how-it-works"></a>Jak to działa

![Diagram przedstawia urządzenie przenośne wysyłające zdarzenie do centrum usługi Azure I o T, które wyzwala aplikację funkcji platformy Azure, aby zażądać tweetu wysyłanego z powrotem do aplikacji i przekazana do centrum oraz do urządzenia przenośnego.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Szkic Arduino wysyła zdarzenie do IoT Hub platformy Azure. To zdarzenie służy do wyzwalania aplikacji Azure Functions. Aplikacja Azure Functions zawiera logikę umożliwiającą połączenie z interfejsem API usługi Twitter i pobranie tweetu. Następnie tekst tweetu jest zawijany do wiadomości C2D (z chmury do urządzenia) i wysyłany z powrotem do urządzenia.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcjonalne: Użyj własnego tokenu okaziciela usługi Twitter

W celach testowych Ten przykładowy projekt używa wstępnie skonfigurowanego tokenu okaziciela usługi Twitter. Istnieje jednak [Limit szybkości](https://dev.twitter.com/rest/reference/get/search/tweets) dla każdego konta usługi Twitter. Jeśli chcesz rozważyć użycie własnego tokenu, wykonaj następujące kroki:

1. Przejdź do [portalu dla deweloperów w serwisie Twitter](https://dev.twitter.com/) , aby zarejestrować nową aplikację w usłudze Twitter.

2. [Pobierz klucz klienta i wpisy tajne klienta](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) aplikacji.

3. Użyj [pewnego narzędzia](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) do wygenerowania tokenu okaziciela usługi Twitter z tych dwóch kluczy.

4. W [Azure Portal](https://portal.azure.com/){: target = "_blank"} uzyskaj dostęp do **grupy zasobów** i Znajdź funkcję Azure Function (Type: App Service) dla projektu "wstrząsania, wstrząsania". Nazwa zawsze zawiera "wstrząs..." parametry.

   ![Zrzut ekranu przedstawiający Azure Portal pokazuje usługę App Service dla projektu.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Zaktualizuj kod dla `run.csx` usługi **functions > shakeshake-CS** własnym tokenem:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Zrzut ekranu przedstawia kod C# funkcji, w której można wprowadzić token.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Zapisz plik, a następnie kliknij przycisk **Uruchom**.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jak rozwiązywać problemy lub przekazać Opinie. 

### <a name="problems"></a>Problemy

Jeden problem zobaczysz, czy ekran nie wyświetla "No tweetów", podczas gdy każdy krok został pomyślnie uruchomiony. Ten stan zwykle odbywa się przy pierwszym wdrożeniu i uruchomieniu przykładu, ponieważ aplikacja funkcji wymaga dowolnego miejsca od kilku sekund do największej minuty na zimne uruchomienie aplikacji. 

Lub, gdy uruchamiasz kod, istnieje kilka Blips, które powodują ponowne uruchomienie aplikacji. W takim przypadku aplikacja urządzenia może uzyskać limit czasu pobierania tweetu. W takim przypadku można wypróbować jedną lub obie te metody w celu rozwiązania problemu:

1. Kliknij przycisk Resetuj w DevKit, aby ponownie uruchomić aplikację urządzenia.

2. W [Azure Portal](https://portal.azure.com/)Znajdź utworzoną aplikację Azure Functions i uruchom ją ponownie:

   ![Zrzut ekranu przedstawia Azure Portal z aplikacją Azure Functions i przyciskiem Uruchom ponownie.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

Jeśli występują inne problemy, zapoznaj się z tematem [często zadawanych pytań dotyczących usługi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak podłączyć urządzenie DevKit do akceleratora rozwiązania do monitorowania zdalnego usługi Azure IoT i pobrać Tweet, poniżej przedstawiono sugerowane następne kroki:

* [Omówienie akceleratora rozwiązań do monitorowania zdalnego usługi Azure IoT](/azure/iot-suite/)