---
title: Informacje o integracji Cisco ISE pxGrid
titleSuffix: Azure Defender for IoT
description: Mostkowanie możliwości usługi Defender for IoT za pomocą rozwiązania Cisco ISE pxGrid zapewnia zespołom ds. zabezpieczeń niepoprzedzające widoczność urządzeń w ekosystemie przedsiębiorstwa.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/28/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3f1fb099aa18ebec5a7e2063740556cf806302e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98558699"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Informacje o integracji Cisco ISE pxGrid

Usługa Defender for IoT oferuje jedyną platformę internetową i IoT cyberbezpieczeństwa utworzoną przez ekspertów z firmy Blue-Team z rekordem śledzenia mającym na celu podwyższenie poziomu infrastruktury krajowej oraz jedyną platformę z opatentowaną analizą zagrożeń obsługującą usługę ICS i uczenie maszynowe. Usługa Defender for IoT oferuje następujące informacje:

- Natychmiastowy wgląd w szczegółowe informacje dotyczące urządzeń i luk w zabezpieczeniach, znanych i nieaktualnych zagrożeń.

- Oparta na usłudze ICS Szczegółowa baza wiedzy o protokołach, urządzeniach, aplikacjach i ich zachowaniach.

- Automatyczna Technologia modelowania zagrożeń dla usługi ICS do przewidywania najbardziej najprawdopodobniej ścieżek skierowanych do nich ataków usługi ICS za pośrednictwem własnej analizy.

## <a name="powerful-device-visibility-and-control"></a>Zaawansowane widoczność i kontrola urządzeń

Integracja usługi Defender for IoT z programem Cisco ISE pxGrid zapewnia nowy poziom scentralizowanego wglądu, monitorowania i kontroli dla krajobrazu.

Te platformy mostkowe umożliwiają automatyczne wgląd i ochronę urządzeń do wcześniej nieosiągalnej usługi ICS i urządzeń IIoT.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Widoczność i IIoT urządzeń: kompleksowa i Szczegółowa

Opatentowana usługa Defender dla technologii IoT zapewnia kompleksową i dogłębną obsługę funkcji odnajdywania i IIoT urządzeń oraz zarządzanie spisem danych w przedsiębiorstwie.

Typy urządzeń, producenci, otwarte porty, numery seryjne, poprawki oprogramowania układowego, adresy IP oraz dane adresów MAC i inne są aktualizowane w czasie rzeczywistym. Usługa Defender for IoT może dodatkowo wzmocnić widoczność, odnajdywanie i analizę z tej linii bazowej dzięki integracji ze krytycznymi źródłami danych przedsiębiorstwa. Na przykład CMDB, DNS, zapory i interfejsy API sieci Web.

Ponadto usługa Defender for IoT umożliwia łączenie pasywnego monitorowania i opcjonalnych technik selektywnego sondowania w celu zapewnienia najbardziej dokładnej i szczegółowej inwentaryzacji urządzeń w organizacjach przemysłowych i krytycznych infrastruktury.

### <a name="bridged-capabilities"></a>Możliwości połączone z mostkiem

Mostkowanie tych funkcji za pomocą rozwiązania Cisco ISE pxGrid zapewnia zespołom ds. zabezpieczeń niepoprzedzające widoczność urządzeń w ekosystemie przedsiębiorstwa.

Bezproblemowe, niezawodne integracje z programem Cisco ISE pxGrid uniemożliwiają nieodnajdywanie urządzeń i nie ma żadnych informacji o urządzeniu.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Przykład elementu OUI kategorii punktów końcowych.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Przykładowe punkty końcowe w systemie":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Zrzut ekranu atrybutów znajdujących się w systemie.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Pokrycie przypadku użycia: zasady ISE na podstawie atrybutów usługi Defender for IoT

Korzystaj z zaawansowanych zasad ISE w oparciu o usługę Defender na potrzeby głębokiego uczenia, aby obsłużyć wymagania dotyczące przypadków użycia usługi ICS i IoT.

Praca z zasadami pozwala zamknąć cykl zabezpieczeń i zapewnić zgodność sieci w czasie rzeczywistym.

Klienci mogą na przykład używać usługi Defender for IoT i atrybutów IoT do tworzenia zasad, które obsługują następujące przypadki użycia, takie jak:

- Utwórz zasady autoryzacji, aby umożliwić znanym i autoryzowanym urządzeniom uwzględnienie w poufnej strefie na podstawie dozwolonych atrybutów — na przykład konkretnej wersji oprogramowania układowego dla Rockwell Automation PLCs.

- Powiadamiaj zespoły zabezpieczeń o wykryciu urządzenia ICS w strefie niezwiązanej.

- Koryguj komputery z nieaktualnymi lub niezgodnymi dostawcami.

- Kwarantanna i blokuj urządzenia zgodnie z potrzebami.

- Generowanie raportów w programie PLCs lub RTUs działającego oprogramowania układowego z znanymi lukami w zabezpieczeniach (CVEs).

### <a name="about-this-article"></a>Informacje o tym artykule

W tym artykule opisano sposób konfigurowania usługi pxGrid i usługi Defender for IoT w celu zapewnienia, że usługa Defender for IoT wprowadza atrybuty do firmy Cisco ISE.

### <a name="getting-more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat wymagań dotyczących integracji Cisco ISE pxGrid, zobacz <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Wymagania dotyczące systemu integracyjnego

W tym artykule opisano wymagania systemowe integracji:

Wymagania usługi Defender for IoT

- Usługa Defender dla IoT w wersji 2,5

Wymagania firmy Cisco

- pxGrid w wersji 2,0

- Cisco ISE w wersji 2,4

Wymagania dotyczące sieci

- Sprawdź, czy urządzenie usługi Defender for IoT ma dostęp do interfejsu zarządzania Cisco ISE.

- Sprawdź, czy masz dostęp do usługi Defender dla wszystkich urządzeń IoT w przedsiębiorstwie w interfejsie wiersza polecenia.

> [!NOTE]
> Tylko urządzenia z adresami MAC są synchronizowane z programem Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Konfiguracja Cisco ISE pxGrid

W tym artykule opisano, jak:

- Konfigurowanie komunikacji z usługą pxGrid

- Subskrybowanie tematu urządzenia punktu końcowego

- Generowanie certyfikatów klienta

- Definiowanie ustawień pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Konfigurowanie komunikacji z usługą pxGrid

W tym artykule opisano sposób konfigurowania komunikacji z usługą pxGrid.

Aby skonfigurować komunikację:

1. Zaloguj się do programu Cisco ISE.

1. Wybierz  > **system** administracyjny i **wdrożenie**.

1. Wybierz wymagany węzeł. Na karcie Ustawienia Ogólne zaznacz **pole wyboru pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Upewnij się, że jest zaznaczone pole wyboru pxgrid.":::

1. Wybierz kartę **Konfiguracja profilowania** .

1. Zaznacz **pole wyboru pxGrid**. Dodaj opis.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Zrzut ekranu przedstawiający Dodawanie opisu":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Subskrybowanie tematu urządzenia punktu końcowego

Sprawdź, czy węzeł ISE pxGrid zasubskrybował temat urządzenia punktu końcowego. Przejdź do strony **Administracja** > **pxGrid Services** > **Web clients**. W tym miejscu możesz sprawdzić, czy ISE subskrybuje temat urządzenia punktu końcowego.

## <a name="generate-certificates"></a>Generowanie certyfikatów klienta

W tym artykule opisano sposób generowania certyfikatów.

Aby wygenerować:

1. Wybierz pozycję **Administracja**  >  **pxGrid Services**, a następnie wybierz pozycję **Certyfikaty**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Wybierz kartę certyfikaty w celu wygenerowania certyfikatu.":::

1. W polu **Chcę** zaznaczyć opcję **Generuj pojedynczy certyfikat (bez żądania podpisania certyfikatu)**.

1. Wypełnij pozostałe pola i wybierz pozycję **Utwórz**.

1. Utwórz klucz certyfikatu klienta i certyfikat serwera, a następnie przekonwertuj je na format magazynu kluczy języka Java. Należy je skopiować do każdej usługi Defender for IoT Sensor w sieci.

## <a name="define-pxgrid-settings"></a>Definiowanie ustawień pxGrid

Aby zdefiniować ustawienia:

1. Wybierz pozycję **Administracja**  >  **pxGrid Services** , a następnie wybierz pozycję **Ustawienia**.

1. Włącz **automatyczne zatwierdzanie nowych kont opartych na certyfikatach** i **Zezwalanie na tworzenie kont opartych na hasłach.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Upewnij się, że są zaznaczone oba pola wyboru.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Konfigurowanie usługi Defender dla urządzeń IoT

W tym artykule opisano sposób konfigurowania usługi Defender dla urządzeń IoT w celu komunikowania się z usługą pxGrid. Konfigurację należy przeprowadzić na wszystkich urządzeniach usługi Defender for IoT, które będą wstrzyknąć dane do programu Cisco ISE.

Aby skonfigurować urządzenia:

1. Zaloguj się do interfejsu wiersza polecenia czujnika.

1. Skopiuj `trust.jks` i, które zostały wcześniej utworzone w czujniku. Skopiuj je do: `/var/cyberx/properties/` .

1. Edytuj `/var/cyberx/properties/pxgrid.properties` :

    1. Dodaj klucz i zaufanie, a następnie Zapisz nazwy plików i hasła.

    2. Dodaj nazwę hosta wystąpienia pxGrid.

    3. `Enabled=true`

1. Uruchom ponownie urządzenie.

1. Powtórz te kroki dla każdego urządzenia w przedsiębiorstwie, które będzie dodawać dane.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Wyświetlanie wykrywania i zarządzanie nimi w programie Cisco ISE

1. Wykrywanie punktów końcowych są wyświetlane na   >  karcie **punkty końcowe** widoczności kontekstu ISE.

1. Wybierz pozycję  >  **profilowanie** zasad  >  **Dodaj**  >  **reguły**  >  **+ warunek**  >  **Utwórz nowy warunek**.

1. Wybierz pozycję **atrybut** i użyj słowników urządzeń IoT, aby skompilować regułę profilowania na podstawie wprowadzonych atrybutów. Następujące atrybuty są wstrzykiwane.

    - Typ urządzenia

    - Poprawka sprzętowa

    - Adres IP

    - Adres MAC

    - Nazwa

    - Identyfikator produktu

    - Protokół

    - Numer seryjny

    - Poprawka oprogramowania

    - Dostawca

Synchronizowane są tylko urządzenia z adresami MAC.

## <a name="troubleshooting-and-logs"></a>Rozwiązywanie problemów i dzienniki

Dzienniki można znaleźć w:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przekazywać informacje o alertach](how-to-forward-alert-information-to-partners.md).
