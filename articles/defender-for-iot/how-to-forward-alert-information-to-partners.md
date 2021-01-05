---
title: Przekazywanie informacji o alercie
description: Informacje o alertach można wysyłać do systemów partnerskich, pracując z regułami przekazywania.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0b71f7ca3f812de1514612f8b0dd5915f3f81bc4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843566"
---
# <a name="forward-alert-information"></a>Przekazywanie informacji o alercie

Informacje o alertach można wysyłać do partnerów, którzy integrują się z usługą Azure Defender for IoT, z serwerami dziennika systemowego, adresami e-mail i nie tylko. Praca z regułami przekazywania umożliwia szybkie dostarczanie informacji o alercie do zainteresowanych stron.  

Dziennik systemowy i inne domyślne akcje przekazywania są dostarczane z systemem. Więcej akcji przekazywania mogą być dostępne podczas integracji z dostawcami partnerskimi, takimi jak Microsoft Azure wskaźnikiem, usługi ServiceNow lub Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Informacje o alercie.":::

Usługa Defender dla administratorów IoT ma uprawnienia do używania reguł przekazywania.

## <a name="about-forwarded-alert-information"></a>Informacje o przesyłanych alertach — informacje

Alerty zawierają informacje o szerokim zakresie zabezpieczeń i zdarzeń operacyjnych. Na przykład:

  - Data i godzina alertu

  - Aparat, który wykrył zdarzenie

  - Tytuł alertu i komunikat opisowy

  - Ważność alertu

  - Nazwa źródłowa i docelowa oraz adres IP

  - Wykryto podejrzany ruch

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Wykryto skanowanie adresów.":::

Odpowiednie informacje są wysyłane do systemów partnerskich podczas tworzenia reguł przekazywania.

## <a name="create-forwarding-rules"></a>Tworzenie reguł przekazywania

Aby utworzyć nową regułę przekazywania:

1. Wybierz pozycję **przekazywanie** w menu po stronie.

   :: Image Type = "Content" source = "Media/How-to-Work-with-Alerts-sensor/create-forwarding-rule-screen.png" Alt-text = "Utwórz ikonę reguły przesyłania dalej".:::

2. Wybierz pozycję **Utwórz regułę przekazywania**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Utwórz nową regułę przekazywania.":::

3. Wprowadź nazwę reguły przekazywania.

### <a name="forwarding-rule-criteria"></a>Kryteria reguły przekazywania 

Zdefiniuj kryteria, według których ma zostać wyzwolona reguła przekazywania. Praca z kryteriami reguł przekazywania ułatwia lokalizowanie i zarządzanie ilością informacji wysyłanych z czujnika do systemów zewnętrznych. Dostępne są następujące opcje:

**Protokoły**: wyzwalanie reguły przekazywania tylko wtedy, gdy wykryty ruch był uruchomiony przez określone protokoły. Wybierz z listy rozwijanej wymagane protokoły lub wybierz je wszystkie.

**Aparaty**: wybierz wymagane aparaty lub wybierz je wszystkie. Alerty z wybranych aparatów będą wysyłane.

**Poziomy ważności**: to jest minimalne zdarzenie do przesyłania dalej, w odniesieniu do poziomu ważności. Na przykład po wybraniu opcji **pomocnicze** alerty pomocnicze i alert powyżej tego poziomu zostaną przesłane dalej. Poziomy są wstępnie zdefiniowane.

### <a name="forwarding-rule-actions"></a>Akcje reguły przekazywania

Akcje reguły przekazywania instruują czujnik, aby przekazywać informacje o alertach do dostawców lub serwerów partnerskich. Dla każdej reguły przekazywania można utworzyć wiele akcji.

Oprócz akcji przekazywania dostarczonych z systemem inne akcje mogą zostać udostępnione podczas integracji z dostawcami partnerskimi. 

#### <a name="email-address-action"></a>Akcja adresu e-mail

Wyślij wiadomość zawierającą informacje o alercie. Możesz wprowadzić jeden adres e-mail dla każdej reguły.

Aby zdefiniować adres e-mail dla reguły przekazywania:

1. Wprowadź pojedynczy adres e-mail. Jeśli należy wysłać więcej niż jedną pocztę, należy utworzyć kolejną akcję.

2. Wprowadź strefę czasową dla sygnatury czasowej dla wykrywania alertu w SIEM.

3. Wybierz pozycję **Prześlij**.

#### <a name="syslog-server-actions"></a>Akcje serwera dziennika systemu

Obsługiwane są następujące formaty:

- Wiadomości SMS

- Komunikaty CEF

- Komunikaty LEEF

- Komunikaty obiektów

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Utwórz akcje reguły przekazywania.":::

Wprowadź następujące parametry:

- Nazwa i port hosta dziennika systemowego.

- Protokoły TCP i UDP.

- Strefa czasowa dla sygnatury czasowej wykrywania alertu w SIEM.

- Plik certyfikatu szyfrowania TLS i plik klucza dla serwerów CEF (opcjonalnie).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Skonfiguruj szyfrowanie dla reguły przekazywania.":::

| Pola wyjściowe wiadomości tekstowej dziennika systemowego | Opis |
|--|--|
| Data i godzina | Data i godzina odebrania informacji przez komputer z serwera dziennika systemowego. |
| Priorytet | User. alert |
| Hostname (Nazwa hosta) | Adres IP czujnika |
| Protokół | TCP lub UDP |
| Wiadomość | Czujnik: Nazwa czujnika.<br /> Alert: Tytuł alertu.<br /> Typ: typ alertu. Może to być **naruszenie protokołu**, **naruszenie zasad**, **złośliwe oprogramowanie**, **anomalia** lub **działanie.**<br /> Ważność: ważność alertu. Może być **ostrzegawczy**, **pomocniczy**, **główny** lub **krytyczny**.<br /> Źródło: nazwa urządzenia źródłowego.<br /> Źródłowy adres IP: źródłowego adresu IP urządzenia.<br /> Lokalizacja docelowa: nazwa urządzenia docelowego.<br /> Docelowy IP: adres IP urządzenia docelowego.<br /> Komunikat: komunikat alertu.<br /> Grupa alertów: Grupa alertów skojarzona z tym alertem. |


| Wynikowy obiekt dziennika systemu | Opis |
|--|--|
| Data i godzina |   Data i godzina odebrania informacji przez komputer z serwera dziennika systemowego. |  
| Priorytet |    User. alert | 
| Hostname (Nazwa hosta) |    Adres IP czujnika | 
| Wiadomość | Nazwa czujnika: nazwa urządzenia. <br /> Czas alertu: czas wykrycia alertu: może się różnić od czasu komputera serwera dziennika systemowego i zależy od konfiguracji strefy czasowej reguły przekazywania. <br /> Tytuł alertu: Tytuł alertu. <br /> Komunikat alertu: komunikat alertu. <br /> Ważność alertu: ważność alertu: **Ostrzeżenie**, **pomocniczy**, **główny** lub **krytyczny**. <br /> Typ alertu: **naruszenie protokołu**, **naruszenie zasad**, **złośliwe oprogramowanie**, **anomalia** lub **działania**. <br /> Protokół: protokół alertu.  <br /> **Source_MAC**: adres IP, nazwa, dostawca lub system operacyjny urządzenia źródłowego. <br /> Destination_MAC: adres IP, nazwa, dostawca lub system operacyjny miejsca docelowego. Jeśli brakuje danych, wartość będzie równa **N/A**. <br /> alert_group: Grupa alertów skojarzona z alertem. |


| Format danych wyjściowych CEF dziennika systemu | Opis |
|--|--|
| Data i godzina | Data i godzina odebrania informacji przez komputer z serwera dziennika systemowego. |
| Priorytet | User. alert | 
| Hostname (Nazwa hosta) | Adres IP czujnika |
| Wiadomość | CEF: 0 <br />Usługa Azure Defender dla IoT <br />Nazwa czujnika: nazwa urządzenia czujnika. <br />Wersja czujnika <br />Tytuł alertu: Tytuł alertu. <br />msg: komunikat alertu. <br />Protokół: protokół alertu. <br />ważność: **Ostrzeżenie**, **pomocniczy**, **główny** lub **krytyczny**. <br />Typ: **naruszenie protokołu**, **naruszenie zasad**, **złośliwe oprogramowanie**, **anomalia** lub **działania**. <br /> Początek: godzina wykrycia alertu. <br />Może się różnić od czasu komputera serwera dziennika systemu i zależy od konfiguracji strefy czasowej reguły przesyłania dalej. <br />src_ip: adres IP urządzenia źródłowego.  <br />dst_ip: adres IP urządzenia docelowego.<br />Cat: Grupa alertów skojarzona z alertem.  |

| Format danych wyjściowych LEEF dziennika systemu | Opis |
|--|--|
| Data i godzina |   Data i godzina odebrania informacji przez komputer z serwera dziennika systemowego. |  
| Priorytet |    User. alert | 
| Hostname (Nazwa hosta) |    Adres IP czujnika |
| Wiadomość | Nazwa czujnika: nazwa urządzenia usługi Azure Defender dla IoT. <br />LEEF: 1.0 <br />Usługa Azure Defender dla IoT <br />Czujnik  <br />Wersja czujnika <br />Alert usługi Azure Defender dla IoT <br />title: Tytuł alertu. <br />msg: komunikat alertu. <br />Protokół: protokół alertu.<br />ważność: **Ostrzeżenie**, **pomocniczy**, **główny** lub **krytyczny**. <br />Typ: typ alertu: **naruszenie protokołu**, **naruszenie zasad**, **złośliwe oprogramowanie**, **anomalia** lub **działania**. <br />Początek: godzina alertu.Należy pamiętać, że może się to różnić od czasu komputera z serwerem dziennika systemu. (Jest to zależne od konfiguracji strefy czasowej). <br />src_ip: adres IP urządzenia źródłowego.<br />dst_ip: adres IP urządzenia docelowego. <br />Cat: Grupa alertów skojarzona z alertem. |

Po wprowadzeniu wszystkich informacji wybierz pozycję **Prześlij**.

#### <a name="netwitness-action"></a>Akcja Monitoraow

Wyślij informacje o alercie do serwera z monitorem.

Aby zdefiniować parametry przekazywania monitora:

1. Wprowadź **nazwę hosta** i informacje o **porcie** dla monitora.

2. Wprowadź strefę czasową dla sygnatury czasowej dla wykrywania alertu w SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Dodaj strefę czasową do reguły przekazywania.":::

3. Wybierz pozycję **Prześlij**.

#### <a name="integrated-vendor-actions"></a>Akcje zintegrowanego dostawcy

Być może system został zintegrowany z zabezpieczeniami, zarządzaniem urządzeniami lub innym dostawcą branżowym. Te integracje pozwalają:

  - Wyślij informacje o alercie.

  - Wyślij informacje o spisie urządzeń.

  - Komunikuj się z zaporami po stronie dostawcy.

Integracje pomagają w mostkach z wcześniej silosowymi rozwiązaniami związanymi z zabezpieczeniami, wzbogacają widoczność urządzeń i przyspieszają reagowanie na cały system.

Skorzystaj z sekcji Actions (akcje), aby wprowadzić poświadczenia i inne informacje wymagane do komunikowania się z dostawcami zintegrowanymi.

Aby uzyskać szczegółowe informacje na temat konfigurowania reguł przekazywania dla integracji, zapoznaj się z odpowiednimi artykułami dotyczącymi integracji partnera.

### <a name="test-forwarding-rules"></a>Reguły przekazywania testów

Przetestuj połączenie między czujnikiem a serwerem partnerskim, który jest zdefiniowany w regułach przekazywania:

1. Wybierz regułę z okna dialogowego **reguła przekazywania** .

2. Zaznacz pole **więcej** .

3. Wybierz pozycję **Wyślij wiadomość testową**.

4. Przejdź do systemu partnerskiego, aby sprawdzić, czy informacje wysyłane przez czujnik zostały odebrane.

### <a name="edit-and-delete-forwarding-rules"></a>Edytuj i Usuń reguły przekazywania 

Aby edytować regułę przekazywania:

- Na ekranie **reguła przekazywania** wybierz pozycję **Edytuj** w menu rozwijanym **więcej** . Wprowadź żądane zmiany i wybierz pozycję **Prześlij**.

Aby usunąć regułę przekazywania:

- Na ekranie **reguła przekazywania** wybierz pozycję **Usuń** w menu rozwijanym **więcej** . W oknie dialogowym **ostrzeżenia** wybierz **przycisk OK**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Reguły przekazywania i reguły wykluczania alertów

Administrator może mieć zdefiniowane reguły wykluczania alertów. Te reguły ułatwiają administratorom dokładniejszą kontrolę nad wyzwalaniem alertów przez wygenerowanie czujnika do ignorowania zdarzeń alertów na podstawie różnych parametrów. Te parametry mogą obejmować adresy urządzeń, nazwy alertów lub konkretne czujniki.

Oznacza to, że zdefiniowane reguły przekazywania mogą być ignorowane na podstawie reguł wykluczeń utworzonych przez administratora. Reguły wykluczania są zdefiniowane w lokalnej konsoli zarządzania.

## <a name="see-also"></a>Zobacz także

[Przyspiesz przepływy pracy alertów](how-to-accelerate-alert-incident-response.md)
