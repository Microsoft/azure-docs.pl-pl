---
title: Wycofaj certyfikaty X. 509 na platformie Azure IoT Central
description: Jak wycofać certyfikaty X. 509 za pomocą aplikacji IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000071"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Jak wycofać certyfikaty urządzeń X. 509 w aplikacji IoT Central

W trakcie cyklu życia rozwiązania IoT konieczne będzie wycofanie certyfikatów. Dwa główne przyczyny certyfikatów kroczących byłyby naruszenia zabezpieczeń i wygaśnięcia certyfikatów.

W przypadku naruszenia zabezpieczeń certyfikaty kroczące to najlepsze rozwiązanie w zakresie zabezpieczeń, które pomaga w zabezpieczeniu systemu. W ramach zamierzonej [metodologii](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), firma Microsoft zaleca, aby procesy dotyczące ponownego aktywnej ochrony były stosowane wraz ze środkami zapobiegawczymi. Należy uwzględnić w tych procesach zabezpieczeń następujące urządzenia. Częstotliwość wycofywania certyfikatów zależy od potrzeb związanych z bezpieczeństwem rozwiązania. Klienci z rozwiązaniami obejmującymi wysoce poufne dane mogą codziennie wycofać certyfikat, podczas gdy inne przydzielą swoje certyfikaty co dwa lata.


## <a name="obtain-new-x509-certificates"></a>Uzyskaj nowe certyfikaty X. 509

Własne certyfikaty X. 509 można utworzyć przy użyciu narzędzia, takiego jak OpenSSL. Takie podejście jest doskonałe do testowania certyfikatów X. 509, ale zapewnia pewne gwarancje związane z bezpieczeństwem. Tego podejścia należy używać wyłącznie do testowania, chyba że zostanie przygotowana do działania jako własny dostawca urzędu certyfikacji.

## <a name="enrollment-groups-and-security-breaches"></a>Grupy rejestracji i naruszenia zabezpieczeń

Aby zaktualizować rejestrację grupy w odpowiedzi na naruszenie zabezpieczeń, należy zastosować następujące podejście, które natychmiast aktualizuje bieżący certyfikat:

1. W lewym okienku przejdź do opcji **Administracja**  , a następnie wybierz pozycję **połączenie z urządzeniem**.

2. Wybierz pozycję **grupy rejestracji**, a następnie wybierz nazwę grupy na liście.

3. W obszarze Aktualizacja certyfikatu wybierz pozycję **Zarządzaj podstawową** lub **Zarządzaj pomocniczą**.

4. Dodaj i Sprawdź główny certyfikat X. 509 w grupie rejestracji.

   Wykonaj te kroki dla certyfikatów podstawowy i pomocniczy, jeśli oba zabezpieczenia zostały naruszone.

## <a name="enrollment-groups-and-certificate-expiration"></a>Grupy rejestracji i wygaśnięcie certyfikatu

Jeśli używasz wycofywania certyfikatów do obsługi wygaśnięć certyfikatów, użyj następującego podejścia, aby natychmiast zaktualizować bieżący certyfikat:

1. W lewym okienku przejdź do opcji **Administracja**  , a następnie wybierz pozycję **połączenie z urządzeniem**.

2. Wybierz pozycję **grupy rejestracji**, a następnie wybierz nazwę grupy na liście.

3. W obszarze Aktualizacja certyfikatu wybierz pozycję **Zarządzaj podstawową**.

4. Dodaj i Sprawdź główny certyfikat X. 509 w grupie rejestracji.

5. Później po wygaśnięciu certyfikatu pomocniczego Wróć i zaktualizuj ten certyfikat pomocniczy.

## <a name="individual-enrollments-and-security-breaches"></a>Indywidualne rejestracje i naruszenia zabezpieczeń

Jeśli używasz wycofywania certyfikatów w odpowiedzi na naruszenie zabezpieczeń, użyj następującego podejścia, aby natychmiast zaktualizować bieżący certyfikat:

1. Wybierz pozycję **urządzenia**i wybierz urządzenie.

2. Wybierz pozycję **Połącz**, a następnie wybierz pozycję Połącz metodę jako **rejestrację indywidualną**

3. Wybierz pozycję **Certyfikaty (X. 509)** jako mechanizm.

    ![Zarządzanie rejestracjami indywidualnymi](./media/how-to-roll-x509-certificates/certificate-update.png)

4. W obszarze Aktualizacja certyfikatu wybierz ikonę folderu, aby wybrać nowy certyfikat do przekazania dla wpisu rejestracji. Wybierz pozycję **Zapisz**.

    Wykonaj następujące kroki dla certyfikatów podstawowy i pomocniczy, jeśli oba zabezpieczenia zostały naruszone

## <a name="individual-enrollments-and-certificate-expiration"></a>Rejestracje indywidualne i wygaśnięcie certyfikatów

Jeśli aktualizujesz certyfikaty, aby obsługiwać wygaśnięcia certyfikatów, należy użyć dodatkowej konfiguracji certyfikatu w następujący sposób, aby ograniczyć przestoje dla urządzeń, które próbują zainicjować obsługę administracyjną.

Gdy certyfikat pomocniczy zbliża się do wygaśnięcia i musi być rzutowany, można go obrócić, aby użyć konfiguracji podstawowej. Obracanie między głównymi i dodatkowymi certyfikatami w ten sposób zmniejsza czas przestoju w przypadku urządzeń, które próbują zainicjować obsługę administracyjną.

1. Wybierz pozycję **urządzenia**i wybierz urządzenie.

2. Wybierz pozycję **Połącz**, a następnie wybierz pozycję Połącz metodę jako **rejestrację indywidualną**

3. Wybierz pozycję **Certyfikaty (X. 509)** jako mechanizm.

    ![Zarządzanie rejestracjami indywidualnymi](./media/how-to-roll-x509-certificates/certificate-update.png)

4. W przypadku dodatkowej aktualizacji certyfikatu wybierz ikonę folderu, aby wybrać nowy certyfikat do przekazania dla wpisu rejestracji. Wybierz pozycję **Zapisz**.

5. Później, po wygaśnięciu certyfikatu podstawowego, wróć i zaktualizuj ten certyfikat podstawowy.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wycofać certyfikaty X. 509 w aplikacji IoT Central platformy Azure, możesz [nawiązać połączenie z usługą azure IoT Central](concepts-get-connected.md).


