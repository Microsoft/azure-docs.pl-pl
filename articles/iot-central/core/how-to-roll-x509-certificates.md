---
title: Wycofaj certyfikaty X. 509 na platformie Azure IoT Central
description: Jak wycofać certyfikaty X. 509 za pomocą aplikacji IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 76e2b9542d20b5788a2875dec89d447ce38276a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88122065"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Jak wycofać certyfikaty urządzeń X. 509 w aplikacji IoT Central

W trakcie cyklu życia rozwiązania IoT konieczne będzie wycofanie certyfikatów. Dwa główne przyczyny certyfikatów kroczących byłyby naruszenia zabezpieczeń i wygaśnięcia certyfikatów. 

Certyfikaty kroczące to najlepsze rozwiązanie w zakresie zabezpieczeń, które pomaga w zabezpieczeniu systemu w przypadku naruszenia. W ramach zamierzonej [metodologii](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), firma Microsoft zaleca, aby procesy dotyczące ponownego aktywnej ochrony były stosowane wraz ze środkami zapobiegawczymi. Należy uwzględnić w tych procesach zabezpieczeń następujące urządzenia. Częstotliwość wycofywania certyfikatów zależy od potrzeb związanych z bezpieczeństwem rozwiązania. Klienci z rozwiązaniami obejmującymi wysoce poufne dane mogą codziennie wycofać certyfikat, podczas gdy inne przydzielą swoje certyfikaty co dwa lata.


## <a name="obtain-new-x509-certificates"></a>Uzyskaj nowe certyfikaty X. 509

Własne certyfikaty X. 509 można utworzyć przy użyciu narzędzia, takiego jak OpenSSL. Takie podejście jest doskonałe do testowania certyfikatów X. 509, ale zapewnia pewne gwarancje związane z bezpieczeństwem. Tego podejścia należy używać wyłącznie do testowania, chyba że zostanie przygotowana do działania jako własny dostawca urzędu certyfikacji.


## <a name="enrollment-groups-and-security-breaches"></a>Grupy rejestracji i naruszenia zabezpieczeń

Aby zaktualizować rejestrację grupy w odpowiedzi na naruszenie zabezpieczeń, należy zastosować następujące podejście, które natychmiast aktualizuje bieżący certyfikat:

1. W lewym okienku przejdź do obszarze **Administracja**  , a następnie kliknij pozycję **połączenie z urządzeniem**.

2. Kliknij pozycję **grupy rejestracji**, a następnie kliknij nazwę grupy na liście.

    ![Połączenie z urządzeniem](./media/how-to-roll-x509-certificates/device-connection.png)


3. W przypadku aktualizacji certyfikatu kliknij pozycję **Zarządzaj podstawową** lub **Zarządzaj pomocniczymi**.

    ![Zarządzanie certyfikatami](./media/how-to-roll-x509-certificates/certificates.png)


4. Dodaj i Sprawdź główny certyfikat X. 509 w grupie rejestracji.

   Wykonaj te kroki dla certyfikatów podstawowy i pomocniczy, jeśli oba zabezpieczenia zostały naruszone.



## <a name="enrollment-groups-and-certificate-expiration"></a>Grupy rejestracji i wygaśnięcie certyfikatu

W przypadku wycofywania certyfikatów do obsługi wygaśnięć certyfikatów należy natychmiast zaktualizować bieżący certyfikat przy użyciu następujących metod:

1. W lewym okienku przejdź do obszarze **Administracja**  , a następnie kliknij pozycję **połączenie z urządzeniem**. 

2. Kliknij pozycję **grupy rejestracji**, a następnie kliknij nazwę grupy na liście.

    ![Połączenie z urządzeniem](./media/how-to-roll-x509-certificates/device-connection.png)


3. W przypadku aktualizacji certyfikatu kliknij przycisk **Zarządzaj podstawowym**.

    ![Połączenie z urządzeniem](./media/how-to-roll-x509-certificates/manage-certs.png)

4. Dodaj i Sprawdź główny certyfikat X. 509 w grupie rejestracji.

5. Później po wygaśnięciu certyfikatu pomocniczego Wróć i zaktualizuj ten certyfikat pomocniczy.



## <a name="individual-enrollments-and-security-breaches"></a>Indywidualne rejestracje i naruszenia zabezpieczeń

Jeśli używasz wycofywania certyfikatów w odpowiedzi na naruszenie zabezpieczeń, użyj następującego podejścia, aby natychmiast zaktualizować bieżący certyfikat:


1. Kliknij pozycję **urządzenia**i wybierz urządzenie. 

2. Kliknij przycisk **Połącz**, a następnie wybierz pozycję Połącz metodę jako **rejestrację indywidualną**

3. Wybierz pozycję **Certyfikaty (X. 509)** jako mechanizm.

    ![Zarządzanie rejestracjami indywidualnymi](./media/how-to-roll-x509-certificates/certificate-update.png)

4. W przypadku aktualizacji certyfikatu kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania dla wpisu rejestracji. Kliknij przycisk **Zapisz**.

    Wykonaj następujące kroki dla certyfikatów podstawowy i pomocniczy, jeśli oba zabezpieczenia zostały naruszone



## <a name="individual-enrollments-and-certificate-expiration"></a>Rejestracje indywidualne i wygaśnięcie certyfikatów

Jeśli aktualizujesz certyfikaty, aby obsługiwać wygaśnięcia certyfikatów, należy użyć dodatkowej konfiguracji certyfikatu w następujący sposób, aby ograniczyć przestoje dla urządzeń, które próbują zainicjować obsługę administracyjną.

Później, gdy certyfikat pomocniczy zbliża się również do wygaśnięcia i musi zostać przeniesiona, można obrócić do korzystania z konfiguracji podstawowej. Obracanie między głównymi i dodatkowymi certyfikatami w ten sposób zmniejsza czas przestoju w przypadku urządzeń, które próbują zainicjować obsługę administracyjną.

1. Kliknij pozycję **urządzenia**i wybierz urządzenie.

2. Kliknij przycisk **Połącz**, a następnie wybierz pozycję Połącz metodę jako **rejestrację indywidualną**

3. Wybierz pozycję **Certyfikaty (X. 509)** jako mechanizm.

    ![Zarządzanie rejestracjami indywidualnymi](./media/how-to-roll-x509-certificates/certificate-update.png)

4. W przypadku dodatkowej aktualizacji certyfikatu kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania dla wpisu rejestracji. Kliknij przycisk **Zapisz**.


5. Później, po wygaśnięciu certyfikatu podstawowego, wróć i zaktualizuj ten certyfikat podstawowy.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wycofać certyfikaty X. 509 w aplikacji IoT Central platformy Azure, możesz [nawiązać połączenie z usługą azure IoT Central](concepts-get-connected.md).


