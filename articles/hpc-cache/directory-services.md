---
title: Używanie grup rozszerzonych w pamięci podręcznej platformy Azure HPC
description: Jak skonfigurować usługi katalogowe na potrzeby dostępu klienta do miejsc docelowych magazynu w pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/22/2020
ms.author: v-erkel
ms.openlocfilehash: 28265861c98cceaedf7d2662f6526a9f62fe68de
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803769"
---
# <a name="configure-directory-services"></a>Konfigurowanie usług katalogowych

Ustawienia **usług katalogowych** umożliwiają przechowywanie w pamięci podręcznej platformy Azure HPC źródła zewnętrznego w celu uwierzytelniania użytkowników w celu uzyskania dostępu do magazynu zaplecza.

Może być konieczne włączenie **grup rozszerzonych** , jeśli przepływ pracy zawiera elementy docelowe magazynu NFS i klientów należących do więcej niż 16 grup.

Po kliknięciu przycisku w celu włączenia grup rozszerzonych musisz wybrać źródło, które będzie używane przez pamięć podręczną Azure HPC, aby uzyskać poświadczenia użytkowników i grup.

* [Active Directory](#configure-active-directory) — Pobierz poświadczenia z serwera zewnętrznego Active Directory. Nie można użyć Azure Active Directory dla tego zadania.
* [Plik prosty](#configure-file-download) — `/etc/group` pliki do pobrania i `/etc/passwd` z lokalizacji sieciowej.
* [LDAP](#configure-ldap) — uzyskiwanie poświadczeń z źródła zgodnego z protokołem Lightweight Directory Access Protocol (LDAP).

> [!NOTE]
> Upewnij się, że pamięć podręczna ma dostęp do źródła informacji o grupie z wewnątrz jego zabezpieczonej podsieci.<!-- + details/examples -->

Pole **pobranej nazwy użytkownika** pokazuje stan najnowszego pobierania informacji o grupie.

![zrzut ekranu strony ustawień strony usług katalogowych w portalu, z wybraną opcją tak dla grup rozszerzonych, a menu rozwijane z etykietą pobieranie źródła jest otwarte](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Konfigurowanie usługi Active Directory

W tej sekcji opisano sposób konfigurowania pamięci podręcznej w celu uzyskania poświadczeń użytkowników i grup z zewnętrznego serwera Active Directory (AD).

W obszarze **Szczegóły usługi Active Directory** podaj następujące wartości:

* **Podstawowy system DNS** — Określ adres IP serwera nazw domen, który może być używany przez pamięć podręczną do rozpoznawania nazwy domeny usługi AD.

* **Pomocniczy serwer DNS** (opcjonalnie) — wprowadź adres serwera nazw, który ma być używany, jeśli serwer podstawowy jest niedostępny.

* **Nazwa domeny DNS usługi AD** — podaj w pełni kwalifikowaną nazwę domeny serwera usługi AD, do którego zostanie dołączona pamięć podręczna, aby uzyskać poświadczenia.

* **Nazwa serwera pamięci podręcznej (konto komputera)** — Ustaw nazwę, która zostanie przypisana do tej pamięci podręcznej HPC, gdy zostanie przyłączony do domeny usługi AD. Określ nazwę łatwą do rozpoznania jako tę pamięć podręczną. Nazwa może składać się z maksymalnie 15 znaków i może zawierać wielkie lub małe litery, cyfry i łączniki (-).

W sekcji **poświadczenia** Podaj nazwę użytkownika i hasło administratora usługi AD, które mogą być używane przez pamięć podręczną Azure HPC w celu uzyskania dostępu do serwera usługi AD. Te informacje są szyfrowane, gdy są przechowywane, i nie można ich zbadać.

Zapisz ustawienia, klikając przycisk w górnej części strony.

![zrzut ekranu przedstawiający sekcję Szczegóły pobierania z wypełnionymi wartościami Active Directory](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Konfiguruj pobieranie plików

Te wartości są wymagane, jeśli chcesz pobrać pliki z informacjami o użytkowniku i grupie. Pliki muszą być w standardzie Linux/UNIX `/etc/group` i `/etc/passwrd` formacie.

* **Identyfikator URI pliku użytkownika** — wprowadź pełny identyfikator URI dla tego `/etc/passwrd` pliku.
* **Identyfikator URI pliku grupy** — wprowadź pełny identyfikator URI dla tego `/etc/group` pliku.

![zrzut ekranu przedstawiający sekcję Szczegóły pobierania dla prostego pliku do pobrania](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Konfigurowanie protokołu LDAP

Wypełnij te wartości, jeśli chcesz użyć źródła LDAP spoza usługi AD w celu uzyskania poświadczeń użytkowników i grup. Jeśli potrzebujesz pomocy dotyczącej tych wartości, skontaktuj się z administratorem LDAP.

* **Serwer LDAP** — wprowadź w pełni kwalifikowaną nazwę domeny lub adres IP serwera LDAP, który ma być używany. <!-- only one, not up to 3 -->

* **Podstawowa** nazwa wyróżniająca LDAP — Określ podstawową nazwę bazową dla domeny LDAP w formacie DN. Zwróć się do administratora LDAP, jeśli Twoja podstawowa nazwa wyróżniająca nie jest znana.

Serwer i podstawowa nazwa wyróżniająca są jedynymi wymaganymi ustawieniami do działania protokołu LDAP, ale dodatkowe opcje sprawiają, że połączenie jest bezpieczniejsze.

![zrzut ekranu przedstawiający obszar konfiguracja LDAP na stronie ustawień strony usług katalogowych](media/group-download-details-ldap.png)

W sekcji **bezpieczny dostęp** można włączyć szyfrowanie i sprawdzanie poprawności certyfikatu dla połączenia LDAP. Po kliknięciu przycisku **tak** , aby włączyć szyfrowanie, dostępne są następujące opcje:

* **Wymagaj ważnego certyfikatu** — po ustawieniu tego ustawienia certyfikat serwera LDAP zostanie zweryfikowany względem urzędu certyfikacji w polu Identyfikator URI poniżej.

* **Identyfikator URI certyfikatu urzędu certyfikacji** — określ ścieżkę do certyfikatu autorytatywnego. Może to być link do certyfikatu zweryfikowanego przez urząd certyfikacji lub certyfikatu z podpisem własnym. To pole jest wymagane do użycia ustawienia certyfikaty zweryfikowane zewnętrznie.

* **Pobierz certyfikat** ponownie — wybierz pozycję **tak** , jeśli chcesz próbować pobrać certyfikat zaraz po przesłaniu tych ustawień.

Wypełnij sekcję **poświadczenia** , jeśli chcesz używać poświadczeń statycznych na potrzeby zabezpieczeń LDAP.

* **Powiązywanie** nazwy wyróżniającej — wprowadź nazwę wyróżniającą powiązania, która ma być używana do uwierzytelniania na serwerze LDAP. (Użyj formatu nazwy domeny).
* **Powiązywanie hasła** — Podaj hasło dla nazwy wyróżniającej powiązania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o dostępie klientów w temacie [Instalowanie pamięci podręcznej platformy Azure HPC](hpc-cache-mount.md)
* Jeśli poświadczenia nie są pobierane prawidłowo, skontaktuj się z administratorem, aby uzyskać informacje o źródle poświadczeń. W razie konieczności Otwórz [bilet pomocy technicznej](hpc-cache-support-ticket.md) .
