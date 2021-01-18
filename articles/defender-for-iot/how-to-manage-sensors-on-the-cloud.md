---
title: Dodawanie czujników i zarządzanie nimi w portalu Defender for IoT
description: Dowiedz się, jak dołączać i wyświetlać czujniki oraz zarządzać nimi w portalu usługi Defender for IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 427ea3884a3db6ba33405014435cf1f962670064
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562713"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Dodawanie czujników i zarządzanie nimi w portalu Defender for IoT

W tym artykule opisano sposób dołączania i wyświetlania czujników oraz zarządzania nimi w [portalu usługi Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Dołączanie czujników

Czujnik można dołączyć, rejestrując go za pomocą usługi Azure Defender dla IoT i pobierając plik aktywacji czujnika.

### <a name="register-the-sensor"></a>Rejestrowanie czujnika

W celu przeprowadzenia rejestracji:

1. Przejdź do strony **powitalnej** w [portalu Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Wybierz pozycję **czujnik** dołączania.
1. Utwórz nazwę czujnika. Zalecamy dołączenie adresu IP czujnika, który został zainstalowany jako część nazwy, lub użycie łatwej do zidentyfikowania nazwy. Zapewni to łatwiejsze śledzenie i spójność nazw rejestracji w portalu usługi Azure [Defender dla IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) oraz adres IP wdrożonego czujnika wyświetlanego w konsoli czujnika.
1. Skojarz czujnik z subskrypcją platformy Azure.
1. Wybierz tryb zarządzania czujnikiem przy użyciu przełącznika **połączonego z chmurą** . Jeśli przełącznik jest włączony, czujnik jest połączony z chmurą. Jeśli przełącznik jest wyłączony, czujnik jest zarządzany lokalnie.

   - **Czujniki połączone z chmurą**: informacje wykrywane przez czujnik są wyświetlane w konsoli czujnika. Informacje o alertach są dostarczane przez Centrum IoT Hub i mogą być udostępniane innym usługom platformy Azure, na przykład na platformie Azure.

   - **Czujniki zarządzane lokalnie**: informacje, które wykrywa czujników, są wyświetlane w konsoli czujnika. Jeśli pracujesz w sieci gapped i chcesz, aby ujednolicony widok wszystkich informacji został wykryty przez wiele zarządzanych lokalnie czujników, Pracuj z lokalną konsolą zarządzania.

   W przypadku czujników połączonych z chmurą Nazwa zdefiniowana podczas dołączania jest nazwą, która pojawia się w konsoli czujnika. Tej nazwy nie można zmienić bezpośrednio z poziomu konsoli. W przypadku czujników zarządzanych lokalnie nazwa zastosowana podczas dołączania zostanie zapisana na platformie Azure i będzie można ją zaktualizować w konsoli czujnika.

1. Wybierz Centrum IoT Hub, które będzie działać jako brama między tym czujnikiem i usługą Azure Defender dla IoT.
1. Jeśli czujnik jest połączony z chmurą, skojarz go z usługą IoT Hub, a następnie zdefiniuj nazwę i strefę lokacji. Możesz również dodać tagi opisowe. Nazwa, strefa i Tagi witryny są opisowymi wpisami na [stronie witryny i czujniki](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Pobierz plik aktywacji czujnika

Plik aktywacji czujnika zawiera instrukcje dotyczące trybu zarządzania czujnika. Użytkownik pobiera unikatowy plik aktywacji dla każdego wdrażanego czujnika. Użytkownik, który loguje się do konsoli czujnika po raz pierwszy przekazuje plik aktywacji do czujnika.

Aby pobrać plik aktywacji:

1. Na stronie **czujnik** dołączania wybierz pozycję **Pobierz plik aktywacji**.
1. Udostępnij plik użytkownikowi, który loguje się do konsoli czujnika po raz pierwszy.

## <a name="view-onboarded-sensors"></a>Wyświetlanie czujników dołączanych

W [portalu Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)można wyświetlić podstawowe informacje na temat czujników dołączanych. 

1. Wybierz **Lokacje i czujniki**.
1. Na stronie **witryny i czujniki** Użyj narzędzi filtrujących i wyszukiwania, aby znaleźć potrzebne informacje o czujnikach.

Dostępne są następujące informacje:

- Ile czujników zostało dodanych
- Liczba czujników, które są połączone z chmurą i zarządzane lokalnie
- Centrum skojarzone z czujnikiem dołączanym
- Szczegóły dodane do czujnika, takie jak nazwa przypisana do czujnika podczas dołączania, strefa skojarzona z czujnikiem lub inne informacje opisowe dodane za pomocą tagów

## <a name="manage-onboarded-sensors"></a>Zarządzanie czujnikami dołączanymi

[Portal usługi Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) służy do zarządzania zadaniami związanymi z czujnikami.

### <a name="export"></a>Eksportowanie

Aby wyeksportować informacje z czujnika dołączania, wybierz ikonę **eksportowania** w górnej części strony **witryny i czujniki** .

### <a name="edit"></a>Edytuj

Narzędzia do edytowania lokacji **i czujników** umożliwiają dodawanie i edytowanie nazw lokacji, strefy i tagów.

### <a name="delete"></a>Usuń

W przypadku usunięcia czujnika połączonego z chmurą informacje nie będą wysyłane do centrum IoT Hub. Usuń połączone lokalnie czujniki, gdy nie będziesz już pracować z nimi.

Aby usunąć Czujnik:

1. Wybierz wielokropek (**...**) dla czujnika, który chcesz usunąć. 
1. Potwierdź usunięcie.

### <a name="reactivate"></a>Uaktywnij ponownie

Możesz chcieć zaktualizować tryb, w którym zarządzasz czujnikiem. Przykład:

- **Pracuj w trybie podłączonym do chmury, a nie w trybie zarządzanym lokalnie**: Aby to zrobić, zaktualizuj plik aktywacji dla czujnika połączonego lokalnie z plikiem aktywacji dla czujnika połączonego z chmurą. Po ponownym uaktywnieniu wykrywania czujników są wyświetlane zarówno w czujnikach, jak i w [portalu usługi Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started). Po pomyślnym przekazaniu pliku reaktywacji nowo wykryte informacje o alercie są wysyłane do platformy Azure.

- **Pracuj w trybie połączonym lokalnie, a nie w trybie połączonym z chmurą**: Aby to zrobić, zaktualizuj plik aktywacji dla czujnika połączonego z chmurą z plikiem aktywacji dla lokalnego czujnika zarządzanego. Po ponownej aktywacji informacje wykrywania czujnika są wyświetlane tylko w czujniku.

- **Skojarz czujnik z nowym centrum IoT Hub**: Aby to zrobić, zarejestruj się ponownie i przekaż nowy plik aktywacji.

Aby ponownie uaktywnić Czujnik:

1. Przejdź do strony **witryny i czujniki** w [portalu Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Wybierz czujnik, dla którego chcesz przekazać nowy plik aktywacji.

3. Usuń czujnik.

4. Ponownie Dołącz czujnik **z strony** dołączania w trybie nowy lub z nowym centrum IoT.

5. Pobierz plik aktywacji ze strony **pobierania pliku aktywacji** .

6. Zaloguj się do konsoli czujnika usługi Defender for IoT.

7. W konsoli czujnika wybierz pozycję **Ustawienia systemu** , a następnie wybierz pozycję **ponowna aktywacja**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Przekaż plik aktywacji, aby ponownie aktywować czujnik.":::

8. Wybierz pozycję **Przekaż** i wybierz zapisany plik.

9. Wybierz pozycję **Aktywuj**. 

## <a name="see-also"></a>Zobacz też

[Aktywowanie i konfigurowanie czujnika](how-to-activate-and-set-up-your-sensor.md)
