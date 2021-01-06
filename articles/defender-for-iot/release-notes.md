---
title: Co nowego w usłudze Azure Defender dla IoT
description: W tym artykule opisano nowości w najnowszej wersji usługi Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2021
ms.author: shhazam
ms.openlocfilehash: 0281ebcdf1da27513e9b9256b508d911ec7697b5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937722"
---
# <a name="whats-new"></a>Co nowego

Usługa Defender for IoT 10,0 zawiera ulepszenia funkcji, które zwiększają bezpieczeństwo, zarządzanie i użyteczność.

## <a name="security"></a>Zabezpieczenia

Ulepszenia odzyskiwania certyfikatu i hasła zostały wprowadzone do tej wersji.

### <a name="certificates"></a>Certyfikaty
  
Ta wersja pozwala:

- Przekazuj certyfikaty SSL bezpośrednio do czujników i lokalnych konsol zarządzania.
- Wykonaj walidację między lokalną konsolą zarządzania i połączonymi czujnikami oraz między konsolą zarządzania i konsolą zarządzania o wysokiej dostępności. Walidacja jest oparta na datach wygaśnięcia, autentyczności głównego urzędu certyfikacji i listach odwołania certyfikatów.  Jeśli walidacja nie powiedzie się, sesja nie będzie kontynuowana.

W przypadku uaktualnień:

- Podczas uaktualniania nie wprowadzono żadnych zmian w certyfikacie SSL ani w funkcji weryfikacji.
- Po uaktualnieniu Użytkownicy administracyjni usługi czujników i lokalnej konsoli zarządzania mogą zastąpić certyfikaty SSL lub aktywować weryfikację certyfikatu SSL z ustawień systemowych, z okna certyfikatu protokołu SSL.  

W przypadku nowych instalacji:

- Podczas pierwszego logowania użytkownicy muszą użyć certyfikatu SSL (zalecane) lub certyfikatu z podpisem własnym, który został wygenerowany lokalnie (niezalecane)
- Sprawdzanie poprawności certyfikatu jest domyślnie włączone dla nowych instalacji.

### <a name="password-recovery"></a>Odzyskiwanie hasła
  
Użytkownicy administracyjni z czujnikiem i lokalną konsolą zarządzania mogą teraz odzyskiwać hasła z portalu usługi Azure Defender dla IoT. Wcześniej Odzyskiwanie hasła wymagało interwencji zespołu pomocy technicznej.

## <a name="onboarding"></a>Dołączanie

### <a name="on-premises-management-console---committed-devices"></a>Lokalna Konsola zarządzania — urządzenia zatwierdzone

Po wstępnym logowaniu do lokalnej konsoli zarządzania użytkownicy są zobowiązani do przekazania pliku aktywacji. Plik zawiera zagregowaną liczbę urządzeń, które mają być monitorowane w sieci organizacji. Ta liczba jest określana jako liczba zatwierdzonych urządzeń.
Zatwierdzone urządzenia są definiowane podczas procesu dołączania w portalu usługi Azure Defender dla IoT, w którym jest generowany plik aktywacji.
W celu przekazania pliku aktywacji wymagane są aktualizacje użytkowników w pierwszym czasie i uaktualnienia.
Po początkowej aktywacji liczba urządzeń wykrytych w sieci może przekroczyć liczbę zatwierdzonych urządzeń. Może się to zdarzyć na przykład wtedy, gdy połączysz więcej czujników z konsolą zarządzania. W przypadku rozbieżności między liczbą wykrytych urządzeń i liczbą zatwierdzonych urządzeń w konsoli zarządzania zostanie wyświetlone ostrzeżenie. Jeśli wystąpi to zdarzenie, należy przekazać nowy plik aktywacji.

### <a name="pricing-page-options"></a>Opcje strony cennika

Strona cennika umożliwia dołączanie nowych subskrypcji do usługi Azure Defender dla IoT i Definiowanie zatwierdzonych urządzeń w sieci.  
Ponadto na stronie cennika można teraz zarządzać istniejącymi subskrypcjami związanymi z czujnikiem i aktualizowaniem zobowiązania dotyczącego urządzeń.

### <a name="view-and-manage-onboarded-sensors"></a>Wyświetlanie czujników dołączanych i zarządzanie nimi

Nowa strona witryny i portalu czujników umożliwia:

- Dodaj opisowe informacje o czujniku. Na przykład strefa skojarzona z czujnikiem lub tagami dowolnego tekstu.
- Wyświetl i Filtruj informacje o czujnikach. Na przykład Wyświetl szczegóły dotyczące czujników, które są połączone w chmurze lub lokalnie zarządzane, lub Wyświetl informacje dotyczące czujników w określonej strefie.  

## <a name="usability"></a>Łatwość obsługi

### <a name="azure-sentinel-new-connector-page"></a>Strona nowego łącznika Azure wskaźnikowego

Przeprojektowano stronę łącznika danych usługi Azure Defender for IoT na platformie Azure. Łącznik danych jest teraz oparty na subskrypcjach, a nie w centrach IoT. umożliwienie klientom lepszego zarządzania połączeniem konfiguracji z platformą Azure.

### <a name="azure-portal-permission-updates"></a>Azure Portal aktualizacje uprawnień  

Dodano obsługę czytnika zabezpieczeń i administratora zabezpieczeń.

## <a name="other-updates"></a>Inne aktualizacje

### <a name="access-group---zone-permissions"></a>Dostęp do strefy grupy dostępu
  
Zasady grupy dostępu lokalnej konsoli zarządzania nie będą zawierać opcji udzielenia dostępu do określonej strefy. Nie wprowadzono zmian w definiowaniu reguł, które korzystają z witryn, regionów i jednostek roboczych.   Po uaktualnieniu grupy dostępu zawierające reguły zezwalające na dostęp do określonych stref zostaną zmodyfikowane w taki sposób, aby zezwalały na dostęp do jego lokacji nadrzędnej, w tym wszystkich jej stref.

### <a name="terminology-changes"></a>Zmiany terminologii

Nazwa elementu zawartości została zmieniona na urządzenie w czujniku i lokalnej konsoli zarządzania, raportach i innych interfejsach rozwiązań.
W przypadku alertów dotyczących czujników i lokalnych konsoli zarządzania termin zarządzanie tym zdarzeniem nosi nazwę kroki zaradcze.

## <a name="next-steps"></a>Następne kroki

[Wprowadzenie do usługi Defender for IoT](getting-started.md)
