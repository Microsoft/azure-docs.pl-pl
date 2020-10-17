---
title: Udostępnianie usługi Translator z/Azure Functions, Cognitive Services, IoT DevKit
description: Użyj mikrofonu w DevKit IoT, aby odebrać wiadomość głosową, a następnie przetworzyć ją na przetłumaczony tekst w języku angielskim przy użyciu Cognitive Services platformy Azure
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147857"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Korzystanie z usługi IoT DevKit AZ3166 z usługami Azure Functions i Cognitive Services w celu tłumaczenia języka

W tym artykule dowiesz się, jak udostępnić usługę IoT DevKit jako tłumaczenie języka za pomocą [usługi Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Rejestruje swój głos i tłumaczy go na tekst w języku angielskim widoczny na ekranie DevKit.

[Zestawu deweloperskiego IoT DevKit](https://aka.ms/iot-devkit) to cała tablica zgodny z programem Arduino z rozbudowanymi urządzeniami peryferyjnymi i czujnikami. Możesz to zrobić za pomocą [usługi Azure IoT Device Workbench](https://aka.ms/iot-workbench) lub pakietu rozszerzeń [narzędzi Azure iot Tools](https://aka.ms/azure-iot-tools) w Visual Studio Code. [Wykaz projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) zawiera przykładowe aplikacje, które ułatwiają prototyp rozwiązań IoT.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym samouczku, najpierw wykonaj następujące zadania:

* Przygotuj swój DevKit, wykonując kroki opisane w temacie [Connect IoT DEVKIT AZ3166 to Azure IoT Hub w chmurze](./iot-hub-arduino-iot-devkit-az3166-get-started.md).

## <a name="create-azure-cognitive-service"></a>Tworzenie usługi poznawczej platformy Azure

1. W Azure Portal kliknij pozycję **Utwórz zasób** i Wyszukaj pozycję **Speech**. Wypełnij formularz, aby utworzyć usługę rozpoznawania mowy.
  ![Usługa rozpoznawania mowy](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Przejdź do właśnie utworzonej usługi mowy, kliknij pozycję **klucze** , aby skopiować i zanotować **Klucz1** do DevKit do niego dostęp.
  ![Kopiuj klucze](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

1. Upewnij się, że Twój DevKit IoT **nie jest podłączony** do komputera. Najpierw Rozpocznij VS Code, a następnie połącz DevKit z komputerem.

1. Kliknij `F1` , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Otwórz przykłady...**. Następnie wybierz pozycję **IoT DevKit** jako tablicę.

1. Na stronie Przykłady usługi IoT Workbench Znajdź pozycję **translator DevKit** , a następnie kliknij pozycję **Otwórz przykład**. Następnie wybiera domyślną ścieżkę do pobrania przykładowego kodu.
  ![Otwórz przykład](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Korzystanie z usługi mowy z Azure Functions

1. W VS Code kliknij pozycję `F1` , wpisz i wybierz pozycję **Azure IoT Device Workbench: Udostępnianie usług platformy Azure.**... ![ Udostępnianie usług platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Postępuj zgodnie z instrukcjami, aby zakończyć Inicjowanie obsługi usługi Azure IoT Hub i Azure Functions.
   ![Kroki inicjowania obsługi administracyjnej](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Zanotuj utworzoną przez siebie nazwę urządzenia usługi Azure IoT Hub.

1. Otwórz `Functions\DevKitTranslatorFunction.cs` i zaktualizuj następujące wiersze kodu z zanotowaną nazwą urządzenia i kluczem usługi rozpoznawania mowy.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Kliknij pozycję `F1` , wpisz i wybierz pozycję **Azure IoT Device Workbench: Wdróż na platformie Azure...**. Jeśli VS Code prosi o potwierdzenie ponownego wdrożenia, kliknij przycisk **tak**.
   ![Ostrzeżenie dotyczące wdrażania](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Upewnij się, że wdrożenie zakończyło się pomyślnie.
   ![Wdrożenie powiodło się](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. W Azure Portal przejdź do sekcji **funkcje aplikacje** i Znajdź właśnie utworzoną aplikację funkcji platformy Azure. Kliknij pozycję `devkit_translator` , a następnie kliknij pozycję **</> Pobierz adres URL funkcji** , aby skopiować adres URL.
   ![Kopiuj adres URL funkcji](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Wklej adres URL do `azure_config.h` pliku.
   ![Konfiguracja platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Jeśli aplikacja funkcji nie działa prawidłowo, zapoznaj się z sekcją [często zadawanych pytań](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) , aby rozwiązać ten problem.

## <a name="build-and-upload-device-code"></a>Kompilowanie i przekazywanie kodu urządzenia

1. Przełącz DevKit do **trybu konfiguracji** , wykonując następujące:
   * Przytrzymaj przycisk **A**.
   * Przycisk naciśnij i zwolnij klawisz **resetowania** .

   Zostanie wyświetlony ekran z DevKit IDENTYFIKATORem i **konfiguracją**.

   ![Tryb konfiguracji DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Kliknij pozycję `F1` , wpisz i wybierz pozycję **Azure IoT Device Workbench: Konfigurowanie ustawień urządzenia... > parametry połączenia urządzenia konfiguracji**. Wybierz pozycję **wybierz IoT Hub parametry połączenia urządzenia** , aby skonfigurować ją do DevKit.
   ![Konfigurowanie parametrów połączenia](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Powiadomienie zostanie wyświetlone po pomyślnym zakończeniu.
   ![Pomyślne skonfigurowanie parametrów połączenia](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Kliknij przycisk `F1` ponownie, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia**. Rozpocznie Kompilowanie i przekazywanie kodu do DevKit.
   ![Przekazywanie urządzenia](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testowanie projektu

Po zainicjowaniu aplikacji postępuj zgodnie z instrukcjami na ekranie zestawu deweloperskiego. Domyślny język źródłowy to chiński.

Aby wybrać inny język tłumaczenia:

1. Naciśnij przycisk A, aby przejść do trybu instalacji.

2. Naciśnij przycisk B, aby przewinąć wszystkie obsługiwane języki źródłowe.

3. Naciśnij przycisk A, aby potwierdzić wybór języka źródłowego.

4. Naciśnij i przytrzymaj przycisk B podczas mówienia, a następnie zwolnij przycisk B, aby zainicjować tłumaczenie.

5. Przetłumaczony tekst w języku angielskim zostanie wyświetlony na ekranie.

![Przewiń do wybranego języka](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Wynik tłumaczenia](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na ekranie wyniku tłumaczenia można wykonać następujące czynności:

- Naciśnij przyciski A i B, aby przewijać i wybrać język źródłowy.

- Naciśnij przycisk B, aby zacząć mówić. Aby wysłać głos i uzyskać tekst tłumaczenia, zwolnij przycisk B.

## <a name="how-it-works"></a>Jak to działa

![Mini-rozwiązanie — dźwięk do tweetu — diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

DevKit IoT rejestruje głos, a następnie wysyła żądanie HTTP, aby wyzwolić Azure Functions. Azure Functions wywołuje interfejs API rozpoznawania mowy usługi poznawczej w celu przeprowadzenia tłumaczenia. Po Azure Functions Pobiera tekst tłumaczenia, wysyła do urządzenia komunikat C2D. Następnie tłumaczenie zostanie wyświetlone na ekranie.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z tematem [często zadawanych pytań dotyczących usługi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać usługi IoT DevKit jako translatora przy użyciu Azure Functions i Cognitive Services. W tym instruktażu pokazano, jak:

> [!div class="checklist"]
> * Użyj zadania Visual Studio Code, aby zautomatyzować postanowienia dotyczące chmury
> * Konfigurowanie parametrów połączenia urządzenia usługi Azure IoT
> * Wdrażanie funkcji platformy Azure
> * Testowanie tłumaczenia wiadomości głosowych

Przejdź do innych samouczków, aby dowiedzieć się:

> [!div class="nextstepaction"]
> [Połącz usługę IoT DevKit AZ3166 z akceleratorem rozwiązania do monitorowania zdalnego usługi Azure IoT](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)