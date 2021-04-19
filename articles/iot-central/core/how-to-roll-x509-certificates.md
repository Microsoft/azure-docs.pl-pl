---
title: Wycofywanie certyfikatów X.509 w Azure IoT Central
description: Jak rzutować certyfikaty X.509 za pomocą IoT Central aplikacji
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c25af944b4c748307f4f974ca8616ecc9f7b28c3
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714528"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Jak wycofać certyfikaty urządzeń X.509 w IoT Central Application

W trakcie cyklu życia rozwiązania IoT należy wycofać certyfikaty. Dwoma głównymi przyczynami stopniowego wycofywania certyfikatów jest naruszenie zabezpieczeń i wygaśnięcie certyfikatu.

W przypadku naruszenia zabezpieczeń certyfikaty stopniowe to najlepsze rozwiązanie w zakresie zabezpieczeń, które pomaga zabezpieczyć system. W ramach [metodologii Przyjmij naruszenia](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)firma Microsoft opowiada się za koniecznością posiadania reaktywnych procesów zabezpieczeń oraz środków prewencyjnych. W ramach tych procesów zabezpieczeń należy uwzględniać stopniowe certyfikaty urządzeń. Częstotliwość, z jaką certyfikaty są wycofywane, zależy od wymagań w zakresie zabezpieczeń rozwiązania. Klienci z rozwiązaniami obejmującymi wysoce poufne dane mogą codziennie wycofywały certyfikaty, a inni co kilka lat.


## <a name="obtain-new-x509-certificates"></a>Uzyskiwanie nowych certyfikatów X.509

Możesz utworzyć własne certyfikaty X.509 za pomocą narzędzia takiego jak OpenSSL. Takie podejście jest doskonałe do testowania certyfikatów X.509, ale zapewnia kilka gwarancji bezpieczeństwa. Tego podejścia należy używać tylko do testowania, chyba że użytkownik jest przygotowany do działania jako własny dostawca urzędu certyfikacji.

## <a name="enrollment-groups-and-security-breaches"></a>Grupy rejestracji i naruszenia zabezpieczeń

Aby zaktualizować rejestrację grupową w odpowiedzi na naruszenie zabezpieczeń, należy użyć następującego podejścia, które natychmiast aktualizuje bieżący certyfikat:

1. Przejdź do **opcji Administracja** w okienku po lewej stronie i wybierz **pozycję Połączenie urządzenia.**

2. Wybierz **pozycję Grupy** rejestracji i wybierz nazwę grupy na liście.

3. W przypadku aktualizacji certyfikatu wybierz pozycję **Zarządzaj podstawową lub** **Zarządzaj pomocniczą usługą**.

4. Dodaj i zweryfikuj główny certyfikat X.509 w grupie rejestracji.

   Wykonaj te kroki dla certyfikatów podstawowych i pomocniczych, jeśli oba zostaną naruszone.

## <a name="enrollment-groups-and-certificate-expiration"></a>Grupy rejestracji i wygaśnięcie certyfikatu

Jeśli używasz certyfikatów do obsługi wygasania certyfikatów, użyj następującego podejścia, aby natychmiast zaktualizować bieżący certyfikat:

1. Przejdź do **opcji Administracja** w okienku po lewej stronie i wybierz **pozycję Połączenie urządzenia.**

2. Wybierz **pozycję Grupy rejestracji** i wybierz nazwę grupy na liście.

3. W przypadku aktualizacji certyfikatu wybierz pozycję **Zarządzaj podstawową .**

4. Dodaj i sprawdź główny certyfikat X.509 w grupie rejestracji.

5. Później, gdy certyfikat pomocniczy wygasł, wróć i zaktualizuj ten certyfikat pomocniczy.

## <a name="individual-enrollments-and-security-breaches"></a>Indywidualne rejestracje i naruszenia zabezpieczeń

W przypadku stopniowego wycofywania certyfikatów w odpowiedzi na naruszenie zabezpieczeń użyj następującego podejścia, aby natychmiast zaktualizować bieżący certyfikat:

1. Wybierz **pozycję Urządzenia** i wybierz urządzenie.

2. Wybierz **pozycję Połącz** i wybierz pozycję Połącz metodę jako **rejestrację indywidualną**

3. Wybierz **pozycję Certyfikaty (X.509)** jako mechanizm.

    ![Zarządzanie rejestracjami indywidualnymi](./media/how-to-roll-x509-certificates/certificate-update.png)

4. W przypadku aktualizacji certyfikatu wybierz ikonę folderu, aby wybrać nowy certyfikat do przesłania dla wpisu rejestracji. Wybierz pozycję **Zapisz**.

    Wykonaj następujące kroki dla certyfikatów podstawowych i pomocniczych, jeśli oba zostaną naruszone

## <a name="individual-enrollments-and-certificate-expiration"></a>Rejestracje indywidualne i wygaśnięcie certyfikatu

W przypadku stopniowego wycofywania certyfikatów w celu obsługi wygasania certyfikatów należy użyć konfiguracji certyfikatu pomocniczego w następujący sposób, aby skrócić czas przestoju dla urządzeń próbujących aprowizować certyfikaty.

Gdy certyfikat pomocniczy zbliża się do wygaśnięcia i musi zostać wycofany, można obrócić do przy użyciu konfiguracji podstawowej. Obracanie między certyfikatami podstawowymi i pomocniczym w ten sposób zmniejsza przestoje w przypadku urządzeń próbujących aprowizować.

1. Wybierz **pozycję** Urządzenia i wybierz urządzenie.

2. Wybierz **pozycję Połącz** i wybierz pozycję Połącz metodę jako **Rejestrację indywidualną**

3. Wybierz **pozycję Certyfikaty (X.509)** jako mechanizm.

    ![Zarządzanie rejestracjami indywidualnymi](./media/how-to-roll-x509-certificates/certificate-update.png)

4. W przypadku dodatkowej aktualizacji certyfikatu wybierz ikonę folderu, aby wybrać nowy certyfikat do przesłania dla wpisu rejestracji. Wybierz pozycję **Zapisz**.

5. Później, gdy certyfikat podstawowy wygasł, wróć i zaktualizuj ten certyfikat podstawowy.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak rzutować certyfikaty X.509 w aplikacji Azure IoT Central, możesz nawiązyć połączenie z Azure IoT Central [.](concepts-get-connected.md)


