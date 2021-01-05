---
title: Aktywowanie i Konfigurowanie czujnika
description: W tym artykule opisano sposób logowania i aktywowania konsoli czujnika.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/26/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9305609b624a96e4d785657dfb63af6639e132c4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842537"
---
# <a name="activate-and-set-up-your-sensor"></a>Aktywowanie i Konfigurowanie czujnika

W tym artykule opisano sposób aktywowania czujnika i wykonywania konfiguracji początkowej.

Użytkownicy z uprawnieniami administratora przeprowadzają aktywację podczas logowania po raz pierwszy i w przypadku konieczności zarządzania aktywacją. Instalator zapewnia, że czujnik jest skonfigurowany pod kątem optymalnego wykrywania i alertu.

Analitycy zabezpieczeń i użytkownicy tylko do odczytu nie mogą aktywować czujnika lub generować nowego hasła.

## <a name="sign-in-and-activation-for-administrator-users"></a>Logowanie i aktywacja dla użytkowników administratorów

Administratorzy, którzy logują się po raz pierwszy, powinni sprawdzić, czy mają dostęp do plików do aktywacji i odzyskiwania hasła, które zostały pobrane podczas dołączania do czujnika. W przeciwnym razie potrzebują uprawnień administrator zabezpieczeń platformy Azure, współautor subskrypcji lub właściciel subskrypcji, aby generować te pliki w portalu usługi Azure Defender dla IoT.

### <a name="first-time-sign-in-and-activation-checklist"></a>Pierwsze logowanie i Lista kontrolna aktywacji

Przed zalogowaniem się do konsoli czujnika Administratorzy powinni mieć dostęp do:

- Adres IP czujnika, który został zdefiniowany podczas instalacji.

- Poświadczenia logowania użytkownika dla czujnika. Jeśli pobrano plik ISO dla czujnika, Użyj poświadczeń domyślnych, które zostały odebrane podczas instalacji. Zalecamy utworzenie nowego użytkownika *administratora* po aktywacji.

- Początkowe hasło. W przypadku zakupu wstępnie skonfigurowanego czujnika z poziomu strzałki należy wygenerować hasło podczas pierwszego logowania.

- Plik aktywacji skojarzony z tym czujnikiem. Plik został wygenerowany i pobrany podczas dołączania czujnika do portalu usługi Defender for IoT.

- Certyfikat podpisany przez urząd certyfikacji SSL/TLS wymagany przez firmę.

### <a name="about-activation-files"></a>Informacje o plikach aktywacji

Czujnik został dołączony do usługi Azure Defender for IoT w określonym trybie zarządzania:

| Typ trybu | Opis |
|--|--|
| **Tryb połączony z chmurą** | Informacje wykrywane przez czujnik są wyświetlane w konsoli czujnika. Informacje o alertach są również dostarczane przez Centrum IoT Hub i mogą być udostępniane innym usługom platformy Azure, na przykład na platformie Azure. |
| **Tryb połączony lokalnie** | Informacje wykrywane przez czujnik są wyświetlane w konsoli czujnika. Informacje o wykrywaniu są również udostępniane w lokalnej konsoli zarządzania, jeśli czujnik jest podłączony do niego. |

Plik aktywacji połączonej lokalnie lub z chmurą został wygenerowany i pobrany dla tego czujnika podczas dołączania. Plik aktywacji zawiera instrukcje dotyczące trybu zarządzania czujnika. *Należy przekazać unikatowy plik aktywacji do każdego wdrażanego czujnika.*  Gdy logujesz się po raz pierwszy, musisz przekazać odpowiedni plik aktywacji dla tego czujnika.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender for IoT Portal — czujnik tablicy.":::

### <a name="about-certificates"></a>Informacje o certyfikatach

Po zainstalowaniu czujnika zostanie wygenerowany lokalny certyfikat z podpisem własnym, który będzie używany do uzyskiwania dostępu do konsoli czujnika. Gdy administrator zaloguje się po raz pierwszy, użytkownik zostanie poproszony o dołączenie certyfikatu SSL/TLS.

Dostępne są dwa poziomy zabezpieczeń:

- Zapoznaj się z określonymi wymaganiami dotyczącymi certyfikatów i szyfrowania, przekazując certyfikat podpisany przez urząd certyfikacji.
- Zezwalaj na sprawdzanie poprawności między konsolą zarządzania i połączonymi czujnikami. Sprawdzanie poprawności jest oceniane względem listy odwołania certyfikatów i daty wygaśnięcia certyfikatu. *Jeśli walidacja nie powiedzie się, komunikacja między konsolą zarządzania i czujnikiem zostanie zatrzymana, a w konsoli zostanie wyświetlony komunikat o błędzie walidacji.* Ta opcja jest domyślnie włączona po instalacji.  

Konsola obsługuje następujące typy certyfikatów:

- Infrastruktura kluczy prywatnych i korporacyjnych (prywatna infrastruktura PKI)
- Infrastruktura kluczy publicznych (publiczna infrastruktura PKI)
- Wygenerowane lokalnie na urządzeniu (lokalnie z podpisem własnym) 

  > WAŻNE Zalecamy, aby nie używać domyślnego certyfikatu z podpisem własnym. Certyfikat nie jest zabezpieczony i powinien być używany tylko w środowiskach testowych. Nie można zweryfikować właściciela certyfikatu, a zabezpieczenia systemu nie są obsługiwane. Nie należy używać tej opcji w przypadku sieci produkcyjnych.

### <a name="sign-in-and-activate-the-sensor"></a>Logowanie i aktywowanie czujnika

Aby zalogować się i aktywować:

1. Przejdź do konsoli czujnika z przeglądarki przy użyciu adresu IP zdefiniowanego podczas instalacji. Zostanie otwarte okno dialogowe logowania.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Usługa Azure Defender dla czujnika IoT.":::

1. Wprowadź poświadczenia zdefiniowane podczas instalacji czujnika. Jeśli masz wstępnie skonfigurowany czujnik z strzałki, najpierw Wygeneruj hasło. Aby uzyskać więcej informacji na temat odzyskiwania hasła, zobacz [Badanie niepowodzenia hasła podczas początkowego logowania](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Po zalogowaniu się zostanie otwarte okno dialogowe **Aktywacja** . Wybierz pozycję **Przekaż** i przejdź do pliku aktywacji, który został pobrany podczas dołączania do czujnika.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Wybierz pozycję Przekaż i przejdź do pliku aktywacji.":::

1. Wybierz łącze **Konfiguracja sieci czujnika** , jeśli chcesz zmienić konfigurację sieci czujnika przed aktywacją. [Przed aktywacją Sprawdź konfigurację sieci czujnika aktualizacji](#update-sensor-network-configuration-before-activation).

1. Zaakceptuj warunki i postanowienia.

1. Wybierz pozycję **Aktywuj**. Zostanie otwarte okno dialogowe certyfikat protokołu SSL/TLS.

1. Zdefiniuj nazwę certyfikatu.
1. Przekaż pliki CRT i Key.
1. Wprowadź hasło i Przekaż plik PEM, jeśli jest to wymagane.
1. Wybierz pozycję **Dalej**. Zostanie otwarty ekran walidacji. Domyślnie sprawdzanie poprawności między konsolą zarządzania i połączonymi czujnikami jest włączone.
1. Wyłącz tryb **włączania walidacji całego systemu** , aby wyłączyć weryfikację. Zalecamy włączenie walidacji.
1. Wybierz pozycję **Zapisz**.  

Po przekazaniu certyfikatu podpisanego przez urząd certyfikacji może być konieczne odświeżenie ekranu.

Aby uzyskać informacje o przekazywaniu nowego certyfikatu, obsługiwanych parametrach certyfikatu i pracy z poleceniami certyfikatu CLI, zobacz [zarządzanie indywidualnymi czujnikami](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Zaktualizuj konfigurację sieci czujnika przed aktywacją  

Parametry konfiguracji sieci czujnika zostały zdefiniowane podczas instalacji oprogramowania lub podczas zakupu wstępnie skonfigurowanego czujnika. Zdefiniowano następujące parametry:

- Adres IP
- DNS
- Brama domyślna
- Maska podsieci
- Nazwa hosta

Możesz chcieć zaktualizować te informacje przed aktywowaniem czujnika. Na przykład może zajść potrzeba zmiany wstępnie skonfigurowanych parametrów zdefiniowanych przez strzałkę. Można również zdefiniować ustawienia proxy przed aktywowaniem czujnika.

Aby zaktualizować parametry konfiguracji sieci czujnika:

1. Wybierz łącze **Konfiguracja sieci czujnika** formularz okno dialogowe **Aktywacja** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Konfiguracja sieci czujnika.":::

2. Wyświetlane są parametry zdefiniowane podczas instalacji. Dostępna jest również opcja definiowania serwera proxy. Zaktualizuj wszystkie ustawienia zgodnie z potrzebami i wybierz pozycję **Zapisz**.

### <a name="subsequent-sign-ins"></a>Kolejne logowania

Po pierwszym aktywowaniu Konsola usługi Azure Defender for IoT zostanie otwarta po zalogowaniu się, nie wymagając pliku aktywacji. Wymagane są tylko poświadczenia logowania.

Po zalogowaniu zostanie otwarta Konsola usługi Azure Defender dla IoT.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Usługa Azure Defender dla programu IoT Console.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Początkowa konfiguracja i nauka (dla administratorów)

Po pierwszym zalogowaniu czujnik usługi Azure Defender for IoT rozpocznie automatyczne monitorowanie sieci. Zasoby sieci będą wyświetlane w sekcji Mapa zasobów i spis zasobów. Usługa Azure Defender for IoT rozpocznie wykrywanie wszystkich zdarzeń związanych z zabezpieczeniami i operacyjną w sieci, a następnie powiadamia o nich. Następnie można tworzyć raporty i kwerendy na podstawie wykrytych informacji.

Początkowo to działanie jest wykonywane w trybie uczenia, co powoduje, że czujnik jest poznanie typowego działania sieci. Na przykład czujnik zawiera informacje o zasobach odnalezionych w sieci, protokołach wykrytych w sieci i transferach plików, które występują między określonymi zasobami. To działanie będzie działaniem linii bazowej sieci.

### <a name="review-and-update-basic-system-settings"></a>Przejrzyj i zaktualizuj podstawowe ustawienia systemu

Sprawdź ustawienia systemowe czujnika, aby upewnić się, że czujnik jest skonfigurowany pod kątem optymalnego wykrywania i alertu.

Zdefiniuj ustawienia systemowe czujnika. Na przykład:

- Definiowanie usługi ICS (lub IoT) i podsieci rozdzielonych.

- Definiowanie aliasów portów dla protokołów specyficznych dla lokacji.

- Zdefiniuj sieci VLAN i nazwy, które są używane.

- Jeśli jest używany protokół DHCP, zdefiniuj legalne zakresy DHCP.

- Zdefiniuj integrację z serwerami Active Directory i poczty e-mail.

### <a name="disable-learning-mode"></a>Wyłącz tryb uczenia

Po dostosowaniu ustawień systemowych można zezwolić na działanie usługi Azure Defender for IoT Sensor w trybie uczenia do momentu, gdy wykrycie systemu dokładnie odzwierciedla aktywność sieci.

Tryb nauki powinien działać od około 2 do 6 tygodni, w zależności od rozmiaru i złożoności sieci. Po wyłączeniu trybu nauki wszystkie działania, które różnią się od działania linii bazowej, spowodują wyzwolenie alertu.

Aby wyłączyć tryb uczenia:

- Wybierz pozycję **Ustawienia systemowe** i wyłącz opcję **uczenie** .

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Logowanie za pierwszym razem dla analityków zabezpieczeń i użytkowników tylko do odczytu

Przed zalogowaniem się Sprawdź, czy masz:

- Adres IP czujnika.
- Poświadczenia logowania udostępniane przez administratora.

## <a name="console-tools-overview"></a>Narzędzia konsoli: przegląd

Dostęp do narzędzi konsoli programu można uzyskać z menu po stronie.

**Nawigacja** 

| Okno | Ikona | Opis |
| -----------|--|--|
| Pulpit nawigacyjny | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Wyświetl intuicyjną migawkę stanu zabezpieczeń sieci. |
| Mapa urządzenia | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Wyświetlanie urządzeń sieciowych, połączeń urządzeń i właściwości urządzenia na mapie. Dostępne są różne opcje powiększenia, wyróżnienia i filtru umożliwiające wyświetlenie sieci. |
| Spis urządzeń | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | W spisie urządzeń jest wyświetlany szeroki zakres atrybutów urządzeń wykrywanych przez ten czujnik. Dostępne są następujące opcje: <br /> — Filtrowanie informacji zgodnie z polami tabeli i wyświetlanie przefiltrowanych informacji. <br /> — Eksportowanie informacji do pliku CSV. <br /> -Zaimportuj szczegóły rejestru systemu Windows.|
| Alerty | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Wyświetlaj alerty po wystąpieniu naruszeń zasad, odchylenia od zachowania linii bazowej lub wykryciu dowolnego typu podejrzanych działań w sieci. |
| Raporty | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Wyświetlanie raportów opartych na zapytaniach wyszukiwania danych. |

**Analizy**

| Okno| Ikona | Opis |
|---|---|---|
| Oś czasu zdarzeń | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Wyświetlanie osi czasu z informacjami o alertach, zdarzeniach sieciowych (informacyjnych) i operacjach użytkownika, takich jak logowania użytkowników i usuwanie użytkowników.|

**Nawigacja**

| Okno | Ikona | Opis |
|---|---|---|
| Wyszukiwanie danych | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Generuj kompletne i szczegółowe informacje o urządzeniach sieciowych w różnych warstwach. |
| Trendy i statystyki | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Wyświetlaj trendy i statystyki w szerokim zakresie elementów widget. |
| Ocena ryzyka | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Wyświetl okno **luki w zabezpieczeniach** . |

**Administracja**

| Okno | Ikona | Opis |
|---|---|---|
| Użytkownicy | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Zdefiniuj użytkowników i role z różnymi poziomami dostępu. |
| Przekazywanie danych | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Przekazywanie informacji o alercie do partnerów, którzy integrują się z usługą Defender for IoT, z adresami e-mail, serwerami webhook i innymi. <br /> Szczegóły można znaleźć w temacie [przekazywanie informacji o alercie](how-to-forward-alert-information-to-partners.md) . |
| Ustawienia systemowe | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Skonfiguruj ustawienia systemowe. Na przykład Zdefiniuj ustawienia DHCP, podaj szczegóły serwera poczty lub Utwórz aliasy portów. |
| Ustawienia importu | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Wyświetl okno **Importowanie ustawień** . Można ręcznie zmieniać informacje o urządzeniu.<br /> Aby uzyskać szczegółowe informacje, zobacz [Importowanie informacji o urządzeniu](how-to-import-device-information.md) . |

**Pomoc techniczna**

| Okno| Ikona | Opis |
|----|---|---|
| Pomoc techniczna | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Skontaktuj się [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) , aby uzyskać pomoc. |

### <a name="see-also"></a>Zobacz także

[Dołączanie czujnika](getting-started.md#4-onboard-a-sensor)

[Zarządzaj plikami aktywacji czujników](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Kontroluj, jaki ruch jest monitorowany](how-to-control-what-traffic-is-monitored.md)
