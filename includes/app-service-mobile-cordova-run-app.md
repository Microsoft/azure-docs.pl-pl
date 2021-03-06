---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240223"
---
1. Przejdź do pliku rozwiązania w projekcie klienta (.sln) i otwórz go za pomocą programu Visual Studio.

2. W programie Visual Studio wybierz platformę rozwiązania (Android, iOS lub Windows) z listy rozwijanej obok strzałki rozpoczęcia. Wybierz konkretny emulator lub konkretne urządzenie wdrożenia, klikając listę rozwijaną zielonej strzałki. Możesz użyć domyślnej platformy Android i emulatora Ripple. Samouczki bardziej zaawansowane (np. powiadomienia wypychane) będą wymagać wybrania obsługiwanego urządzenia lub emulatora.

3. Otwórz plik `ToDoActivity.java` w tym folderze - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Nazwa aplikacji `ZUMOAPPNAME`to .

4. Przejdź do [witryny Azure portal](https://portal.azure.com/) i przejdź do utworzonej aplikacji mobilnej. Na `Overview` bloku poszukaj adresu URL, który jest publicznym punktem końcowym aplikacji mobilnej. Przykład - nazwa witryny dla mojej nazwy aplikacji "test123" będzie https://test123.azurewebsites.net.

5. Przejdź do `index.js` pliku w ZUMOAPPNAME/www/js/index.js i w `onDeviceReady()` metodzie, zastąp `ZUMOAPPURL` parametr publicznym punktem końcowym powyżej.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    Staje się
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Aby skompilować i uruchomić aplikację systemu Cordova, naciśnij klawisz F5 lub kliknij zieloną strzałkę. Jeśli w emulatorze widzisz okno dialogowe zabezpieczeń żądające dostępu do sieci, zaakceptuj je.

7. Po uruchomieniu aplikacji na urządzeniu lub emulatorze wpisz sensowny tekst w **wprowadź nowy tekst,** na przykład *Wypełnij samouczek,* a następnie kliknij przycisk **Dodaj.**

Zaplecze wstawia dane z żądania do tabeli TodoItem bazy danych SQL Database i zwraca do aplikacji mobilnej informacje na temat przechowywanych od niedawna elementów. W aplikacji mobilnej dane te są wyświetlane na liście.

Kroki od 3 do 5 możesz powtórzyć dla innych platform.