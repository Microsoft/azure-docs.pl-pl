---
author: ericmitt
ms.author: ericmitt
ms.service: iot-pnp
ms.topic: include
ms.date: 07/13/2020
ms.openlocfilehash: 3ab9161c006a88a254b247a921512698ed46fa95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352778"
---
1. Otwórz program Azure IoT Explorer.

1. Jeśli połączenie z usługą IoT Hub nie zostało jeszcze dodane, na stronie **centra IoT** wybierz pozycję **+ Dodaj połączenie**. Wprowadź parametry połączenia dla Centrum IoT, które zostało utworzone wcześniej, a następnie wybierz pozycję **Zapisz**.

1. Na stronie **ustawienia Plug and Play IoT** wybierz pozycję **+ Dodaj > folderu lokalnego** i wybierz folder *modele* lokalne, do którego zapisano pliki modelu.

1. Na stronie **centra IoT** kliknij nazwę centrum, z którym chcesz współpracować. Zostanie wyświetlona lista urządzeń zarejestrowanych w usłudze IoT Hub.

1. Kliknij **Identyfikator urządzenia** utworzonego wcześniej.

1. Menu po lewej stronie zawiera różne typy informacji dostępnych dla urządzenia.

1. Wybierz pozycję **IoT Plug and Play Components** , aby wyświetlić informacje o modelu dla Twojego urządzenia.

1. Można wyświetlić różne składniki urządzenia. Składnik domyślny i wszystkie dodatkowe. Wybierz składnik, z którym chcesz współpracować.

1. Wybierz stronę **telemetrię** , a następnie wybierz pozycję **Uruchom** , aby wyświetlić dane telemetryczne wysyłane przez urządzenie dla tego składnika.

1. Wybierz stronę **właściwości (tylko do odczytu)** , aby wyświetlić właściwości tylko do odczytu zgłoszone dla tego składnika.

1. Wybierz stronę **właściwości (zapisywalne)** , aby wyświetlić właściwości zapisywalne, które można zaktualizować dla tego składnika.

1. Wybierz właściwość o **nazwie**, wprowadź dla niej nową wartość i wybierz opcję **Aktualizuj żądaną wartość**.

1. Aby wyświetlić nową wartość, wybierz przycisk **Odśwież** .

1. Wybierz stronę **polecenia** , aby wyświetlić wszystkie polecenia dla tego składnika.

1. Wybierz polecenie, które chcesz przetestować, jeśli istnieje. Wybierz pozycję **Wyślij polecenie** , aby wywołać polecenie na urządzeniu. Urządzenie można zobaczyć, odpowiadając na polecenie w oknie wiersza polecenia, w którym jest uruchomiony przykładowy kod.
