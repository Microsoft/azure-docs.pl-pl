---
title: 'Szybki Start: dołączanie czujników w usłudze Azure Defender dla IoT'
description: Dowiedz się, jak dołączać czujniki dla wdrożenia usługi Azure Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: rkarlin
ms.openlocfilehash: e90bdcfe22828d85ebeeb74314617fc6eac81385
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094860"
---
# <a name="quickstart-deploy-and-onboard-a-sensor"></a>Szybki Start: wdrażanie i dołączanie czujnika

Ten artykuł zawiera omówienie procesu wdrażania czujnika. Czujniki muszą zostać dołączone do portalu usługi Azure Defender dla IoT.

Ten proces wymaga uzyskania wstępnie skonfigurowanego czujnika lub uzyskania certyfikowanego urządzenia czujnika i samodzielnego zainstalowania oprogramowania czujnika.

Jeśli pracujesz z certyfikowanym urządzeniem czujnika, przed rozpoczęciem zaleca się zapoznanie się z [przewodnikiem dotyczącym specyfikacji sprzętu usługi Azure Defender for IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .


> [!NOTE]
> Nie trzeba dołączać do lokalnej konsoli zarządzania usługi Azure Defender for IoT.


Czujniki dołączania umożliwiają:

|||
|------ | ----------- |
| **Zdefiniuj nazwę czujnika** | Nazwij czujnik, który jest dołączany, i skojarz go z IoT Hub lub subskrypcją.<br /><br />Aby uzyskać więcej informacji, zobacz **czujniki** dołączania.|
|**Wybierz subskrypcję i liczbę zatwierdzonych urządzeń**|Wybierz subskrypcję i liczbę urządzeń objętych subskrypcją. Wprowadź liczbę przyrostów wynoszącą 1000.|
| **Zdefiniuj tryb zarządzania czujnikiem** | Zdefiniuj miejsce, w którym jest wyświetlany element zawartości, alert i inne informacje wykryte przez czujnik. Jest to ustalane na podstawie zdefiniowanego **trybu zarządzania czujnikami** .<br /><br />**Tryb zarządzany lokalnie**: informacje wykryte przez czujnik są wyświetlane w konsoli czujnika. Informacje o wykrywaniu są również udostępniane w lokalnej konsoli zarządzania, jeśli czujnik jest podłączony do niego.<br /><br />**Tryb zarządzany w chmurze**: informacje wykryte przez czujnik są wyświetlane w konsoli czujnika. Ponadto informacje o alertach są dostarczane przez IoT Hub i mogą być udostępniane innym usługom platformy Azure, na przykład na platformie Azure.<br />Aby uzyskać więcej informacji, zobacz **czujnik** dołączania. |
| **Pobierz plik aktywacji czujnika** | W przypadku czujników **zarządzanych lokalnie** plik aktywacji jest używany do zarządzania autoryzowanymi okresami aktywacji czujnika.<br /><br />W przypadku czujników **zarządzanych przez chmurę** plik aktywacji jest używany jako połączenie między czujnikiem i IoT Hub. Aby uzyskać więcej informacji, zobacz Dołączanie **czujników**. |
| **Przekaż plik aktywacji do czujnika** | Pliki aktywacji muszą zostać przekazane do czujników przedsiębiorstwa. Monitorowanie sieci i dostęp do funkcji konsoli czujników nie będą dostępne do czasu przekazania pliku aktywacji. Aby uzyskać więcej informacji, zobacz **przekazywanie plików aktywacji czujników**. |
| **Zaktualizuj parametry sieci czujnika przed aktywacją** | Parametry aktualizacji zdefiniowane podczas instalacji czujnika. Aby uzyskać więcej informacji, zobacz **błąd! Nie znaleziono źródła odwołania**.|


**Aby wdrożyć Czujnik:**

1. Przejdź do usługi Azure Defender dla IoT z Azure Portal.

2. Wybierz pozycję **Rozpocznij.**

3. W sekcji **odnajdywanie sieci** wybierz pozycję **Konfiguruj**.

   ![Usługa Azure Defender for IoT odnajduje widok sieci](media/updates/image5.png)

4. Wybierz opcję uzyskania czujnika.

   ![Widok sektora sieci usługi Azure Defender dla IoT](media/updates/image6.png)

  - **Kup wstępnie skonfigurowany czujnik**: Firma Microsoft i Strzałka współpracują ze sobą, aby zapewnić wstępnie skonfigurowane czujniki. Aby zakupić wstępnie skonfigurowany czujnik, skontaktuj się ze strzałką w: <hardware.sales@arrow.com> . Czujnik zostanie dostarczony do Twojej infrastruktury. Najnowsza wersja jest zainstalowana.

  - **Przenoszenie własnego urządzenia (instalacja ISO)**: rozwiązanie działa na certyfikowanych urządzeniach. Przewodnik po [specyfikacjach sprzętu usługi Azure Defender for IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) jest używany w przypadku zakupu urządzenia certyfikowanego.

    - Wybierz wersję z menu **Wybierz wersję** .

    - Wybierz pozycję **Pobierz** i Zapisz plik. Zapoznaj się z **przewodnikiem instalacji usługi Azure Defender for IoT** , aby uzyskać szczegółowe informacje na temat pobierania obrazu ISO i instalowania oprogramowania czujnika.

5. Po zainstalowaniu oprogramowania na czujniku lub otrzymaniu wstępnie skonfigurowanego czujnika wykonaj zadania konfiguracji sieci. Szczegóły można znaleźć w [przewodniku konfigurowania sieci](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="onboard-a-sensor"></a>Dołączanie czujnika

Czujniki muszą zostać dołączone do portalu usługi Azure Defender dla IoT. Dołączanie odbywa się w dwóch fazach:

1. Zarejestruj czujnik przy użyciu portalu usługi Azure Defender dla IoT.

2. Pobierz plik aktywacji dla czujnika. Plik zostanie później przekazany do czujnika.

**Aby uzyskać dostęp do strony dołączania:**

1. Przejdź **do strony wprowadzenie** do usługi Microsoft Azure Defender dla usługi IoT.

2. Wybierz **2)** dołączenie z usługi Azure Defender dla IoT.

   ![Zrzut ekranu przedstawiający widok strony przechodzenia](media/updates/image7.png)

3. Zostanie otwarta strona **czujnika** dołączania. Dostępne są opcje umożliwiające zarejestrowanie czujnika i pobranie pliku aktywacji.

   ![Zrzut ekranu przedstawiający widok strony czujnika przechodzenia](media/quickstart/onboard-sensors.png)

**Aby zarejestrować Czujnik:**

1. Wybierz nazwę czujnika. W przypadku czujników zarządzanych w chmurze Nazwa zdefiniowana w tym miejscu jest stosowana nazwa wyświetlana w konsoli czujnika: tej nazwy nie można zmienić z poziomu konsoli. W przypadku czujników zarządzanych lokalnie nazwa zastosowana w tym miejscu będzie przechowywana na platformie Azure, ale można ją zaktualizować w konsoli czujnika. Zaleca się uwzględnienie adresu IP czujnika, który został zainstalowany jako część nazwy, lub użycie łatwo rozpoznawalnej nazwy. Zapewni to łatwiejsze śledzenie i spójność nazw rejestracji w portalu usługi Azure Defender dla IoT oraz adres IP wdrożonego czujnika wyświetlanego w konsoli czujnika.

2. Wybierz subskrypcję z listy rozwijanej subskrypcji.
3. W polu **zatwierdzone urządzenia** wprowadź liczbę urządzeń objętych subskrypcją. Należy wprowadzić ponownie ten numer dla każdej czujnika, który jest skojarzony z tą samą subskrypcją.  Na przykład jeśli subskrypcja "A" jest skojarzona z 6000 urządzeń, wprowadź 6000 dla każdego czujnika skojarzonego z subskrypcją "A".   
4. Wybierz tryb zarządzania czujnikiem przy użyciu przełącznika **połączenia z chmurą** . Jeśli przełącznik jest włączony, czujnik jest zarządzany przez **chmurę**. Jeśli przełącznik jest wyłączony, czujnik jest **zarządzany lokalnie**.


| Tryb zarządzania czujnikiem | Opis                                                |
| ---------------------- | ---------------------------------------------------------  |
| **Zarządzane przez chmurę**          | Informacje wykryte przez czujnik są wyświetlane w konsoli czujnika. Ponadto informacje o alertach są dostarczane przez IoT Hub i mogą być udostępniane innym usługom platformy Azure, na przykład na platformie Azure.<br /><br />Wybierz IoT Hub do skojarzenia z tym czujnikiem.<br /><br />Należy przekazać plik aktywacji zarządzanej w chmurze do czujników zarządzanych przez chmurę.<br /><br />Szczegółowe informacje znajdują się w temacie **przekazywanie plików aktywacji czujników** . |
| **Zarządzane lokalnie**        | Informacje wykryte przez czujniki zarządzane lokalnie są wyświetlane w konsoli czujnika. Jeśli pracujesz w sieci gapped i chcesz, aby ujednolicony widok wszystkich informacji został wykryty przez wiele zarządzanych lokalnie czujników, Pracuj z lokalną konsolą zarządzania.<br /><br />Czujniki *zarządzane lokalnie* są skojarzone z subskrypcją platformy Azure i zawierają instrukcje dotyczące czujnika okresu ważności aktywacji.<br /><br />Wybierz subskrypcję, która ma zostać skojarzona z tym czujnikiem.<br /><br />Należy przekazać lokalnie zarządzany plik aktywacji do każdego czujnika. Szczegółowe informacje znajdują się w temacie **przekazywanie plików aktywacji czujników** . |

5. Wybierz pozycję **Zarejestruj**.

6. Na stronie Pobierz plik aktywacji wybierz pozycję **Pobierz plik aktywacji** .

   ![Zrzut ekranu przedstawiający widok czujnika dołączania](media/updates/image9.png)

7. Zapisz plik. Nazwa pliku jest w formacie: `<hub_name>_<sensor_name>` . Nazwa czujnika odnosi się do nazwy zdefiniowanej powyżej.

8. Wybierz pozycję **Zakończ**.

9. Aby ukończyć proces dołączania czujnika, Przekaż plik do czujnika z poziomu konsoli usługi Azure Defender dla IoT.
 
## <a name="upload-a-sensor-activation-file"></a>Przekaż plik aktywacji czujnika

W tym artykule opisano sposób przekazywania pliku aktywacji do czujnika. Plik powinien zostać odebrany podczas dołączania czujnika.

**Zarządzane lokalnie pliki aktywacji**

Zarządzane lokalnie czujniki są skojarzone z subskrypcją platformy Azure.  Plik aktywacji dla zarządzanych lokalnie czujników zawiera datę wygaśnięcia. Jeden miesiąc przed tą datą w górnej części konsoli czujnika pojawia się komunikat ostrzegawczy. Ostrzeżenie pozostanie do momentu zaktualizowania pliku aktywacji.

Po wygaśnięciu pliku aktywacji można kontynuować pracę z usługą Azure Defender dla usługi IoT.

**Pliki aktywacji zarządzanej w chmurze**

Czujniki zarządzane przez chmurę są skojarzone z IoT Hub platformy Azure. Te czujniki nie są ograniczone przez okresy czasu pliku aktywacji. Plik aktywacji czujników zarządzanych przez chmurę służy do zapewnienia połączenia z IoT Hub.

Aby uzyskać szczegółowe informacje o centrach IoT, zobacz [Informacje o IoT Hub](../iot-hub/about-iot-hub.md).

**Aby przekazać plik aktywacji:**

1. Sprawdź, czy masz:

   - Adres IP dla czujnika zdefiniowanego podczas instalacji

   - Poświadczenia logowania użytkownika potrzebne dla czujnika

2. Uzyskaj dostęp do konsoli usługi Azure Defender dla IoT z przeglądarki przy użyciu adresu IP dla tego czujnika.

3. Zaloguj się do konsoli czujnika usługi Azure Defender for IoT.

   ![Logowanie w usłudze Azure Defender dla konsoli IoT — widok](media/updates/image11.png)

4. Po pomyślnym zalogowaniu zostanie otwarty ekran aktywacja. Wybierz pozycję Przekaż i wybierz zapisany plik aktywacji.

   ![Widok aktywacji usługi Azure Defender for IoT](media/updates/image12.png)

5. Zatwierdź warunki i postanowienia.

6. Wybierz pozycję **Aktywuj**. Parametry konfiguracji sieci czujnika zostały zdefiniowane podczas instalacji oprogramowania lub podczas zakupu czujnika. Zdefiniowano następujące parametry:
   - Adres IP
   - DNS  
   - Brama domyślna
   - Maska podsieci
   - Nazwa hosta
 
   Przed aktywowaniem czujnika można zaktualizować te informacje, ponieważ:
   - Należy zmienić zdefiniowane wstępnie skonfigurowane parametry  
   - Chcesz ponownie skonfigurować parametry sieciowe po instalacji, możesz również zdefiniować ustawienia proxy przed aktywowaniem czujnika.

7. Wybierz łącze **Konfiguracja sieci czujnika** formularz okno dialogowe Aktywacja.  

   ![Zrzut ekranu przedstawiający edytowanie widoku konfiguracji sieci](media/updates/image13.png)

8. Wyświetlane są parametry zdefiniowane podczas instalacji. Dostępna jest również opcja Definiuj proxy. Zaktualizuj zgodnie z wymaganiami i wybierz pozycję **Zapisz**.
 
 
## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wdrażania i dołączania czujników. Aby dowiedzieć się więcej na temat rozpoczynania pracy, zobacz następujący artykuł:

- [Rozpoczęcie pracy](getting-started.md)