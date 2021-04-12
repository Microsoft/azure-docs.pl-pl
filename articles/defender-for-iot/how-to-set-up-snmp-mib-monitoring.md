---
title: Konfigurowanie monitorowania SNMP MIB
description: Monitorowanie kondycji czujnika można przeprowadzić przy użyciu protokołu SNMP. Czujnik reaguje na zapytania protokołu SNMP wysyłane z autoryzowanego serwera monitorowania.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 1ba52236f65c6c5daba68c67677cdc6adfb699b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781674"
---
# <a name="set-up-snmp-mib-monitoring"></a>Konfigurowanie monitorowania SNMP MIB

Monitorowanie kondycji czujnika można przeprowadzić za pomocą Simple Network Management Protocol (SNMP). Czujnik reaguje na zapytania protokołu SNMP wysyłane z autoryzowanego serwera monitorowania. Monitor SNMP okresowo sonduje identyfikatory OID (do 50 razy sekund).

Obsługiwane są wersje SNMP v2 lub SNMP v3. Protokół SNMP używa portu UDP jako protokołu transportowego z portem 161 (SNMP).

Przed rozpoczęciem konfigurowania monitorowania SNMP należy otworzyć port UDP 161 w zaporze.

## <a name="oids"></a>Identyfikatory

| Konsola zarządzania i czujnik | IDENTYFIKATOR OID | Format | Opis |
|--|--|--|--|
| Nazwa urządzenia | 1.3.6.1.2.1.1.5.0 | KLASIE | Nazwa urządzenia w lokalnej konsoli zarządzania |
| Dostawca | 1.3.6.1.2.1.1.4.0 | KLASIE | Pomoc techniczna firmy Microsoft (support.microsoft.com) |
| Platforma | 1.3.6.1.2.1.1.1.0 | KLASIE | Czujnik lub lokalna Konsola zarządzania |
| Numer seryjny | 1.3.6.1.4.1.9.9.53313.1 | KLASIE | Ciąg wykorzystywany przez licencję |
| Wersja oprogramowania | 1.3.6.1.4.1.9.9.53313.2 | KLASIE | Ciąg pełnej wersji Xsense i zarządzanie ciągiem pełnej wersji |
| Użycie procesora | 1.3.6.1.4.1.9.9.53313.3.1 | WARTOŚĆ GAUGE32 | Oznaczenie od zera do 100 |
| Temperatura procesora | 1.3.6.1.4.1.9.9.53313.3.2 | KLASIE | C oznaczenie od zera do 100 w oparciu o dane wejściowe systemu Linux |
| Użycie pamięci | 1.3.6.1.4.1.9.9.53313.3.3 | WARTOŚĆ GAUGE32 | Oznaczenie od zera do 100 |
| Użycie dysku | 1.3.6.1.4.1.9.9.53313.3.4 | WARTOŚĆ GAUGE32 | Oznaczenie od zera do 100 |
| Stan usługi | 1.3.6.1.4.1.9.9.53313.5 | KLASIE | W trybie online lub offline, jeśli jeden z czterech najważniejszych składników nie działa |
| Przepustowość | Poza zakresem 2,4 |  | Przepustowość odebrana na każdym interfejsie monitora w Xsense |

   - Nieistniejące klucze reagują na wartość null, HTTP 200 na podstawie [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - MIBs związane ze sprzętem (użycie procesora CPU, temperatura procesora, użycie pamięci, użycie dysku) powinno być testowane na wszystkich architekturach i czujników fizycznych. Temperatura procesora na maszynach wirtualnych nie powinna być stosowana.

Można pobrać dziennik zawierający wszystkie zapytania SNMP odbierane przez czujnik, w tym dane połączenia i dane pierwotne z pakietu.

Aby zdefiniować monitorowanie kondycji SNMP v2:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W okienku **odnajdywanie aktywne** wybierz pozycję **monitorowanie MIB usługi SNMP** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Edytuj okno protokołu SNMP.":::

3. W sekcji **dozwolone hosty** wybierz pozycję **Dodaj hosta** i wprowadź adres IP serwera, który wykonuje monitorowanie kondycji systemu.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Wprowadź adres IP dla dozwolonych hostów.":::

4. W sekcji **uwierzytelnianie** w polu **ciąg Community protokołu SNMP v2** wprowadź ciąg. Ciąg identyfikacyjny SNMP może zawierać do 32 znaków i zawierać dowolną kombinację znaków alfanumerycznych (wielkie litery, małe litery i cyfry). Spacje są niedozwolone.

5. Wybierz pozycję **Zapisz**.

Aby zdefiniować monitorowanie kondycji SNMP v3:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W okienku **Active Discovery** wybierz pozycję **monitorowanie MIB usługi SNMP** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Edytuj okno protokołu SNMP.":::

3. W sekcji **dozwolone hosty** wybierz pozycję **Dodaj hosta** i wprowadź adres IP serwera, który wykonuje monitorowanie kondycji systemu.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Wprowadź adres IP dla dozwolonych hostów.":::

4. W sekcji **uwierzytelnianie** ustaw następujące parametry:

    | Parametr | Opis |
    |--|--|
    | **Nazwa użytkownika** | Nazwa użytkownika SNMP może zawierać maksymalnie 32 znaków i zawierać dowolną kombinację znaków alfanumerycznych (wielkie litery, małe litery i cyfry). Spacje są niedozwolone. <br /> <br />Nazwa użytkownika dla uwierzytelniania SNMP v3 musi być skonfigurowana w systemie i na serwerze SNMP. |
    | **Password** (Hasło) | Wprowadź hasło uwierzytelniania z uwzględnieniem wielkości liter. Hasło uwierzytelniania może zawierać od 8 do 12 znaków i zawierać dowolną kombinację znaków alfanumerycznych (wielkie litery, małe litery i cyfry). <br /> <br/>Nazwa użytkownika dla uwierzytelniania SNMP v3 musi być skonfigurowana w systemie i na serwerze SNMP. |
    | **Typ uwierzytelniania** | Wybierz pozycję MD5 lub SHA. |
    | **Szyfrowanie** | Wybierz pozycję DES lub AES. |
    | **Klucz tajny** | Klucz musi zawierać dokładnie osiem znaków i zawierać dowolną kombinację znaków alfanumerycznych (wielkie litery, małe litery i cyfry). |

5. Wybierz pozycję **Zapisz**.

## <a name="see-also"></a>Zobacz też

[Eksportuj dzienniki rozwiązywania problemów](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
