---
title: Informacje o ustawieniach mobilnych systemu Windows 10 — Azure Active Directory
description: Ustawienia, które będą miały dostęp do roamingu lub tworzenia kopii zapasowej w systemie Windows 10 z usługą ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6048ee9237640799b7bec37083e607fc74ffb8e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252971"
---
# <a name="windows-10-roaming-settings-reference"></a>Informacje dotyczące ustawień roamingu w systemie Windows 10

Poniżej znajduje się lista ustawień, które mają być przenoszone lub tworzone w ramach kopii zapasowej w systemie Windows 10. 

## <a name="devices-and-endpoints"></a>Urządzenia i punkty końcowe

W poniższej tabeli znajduje się podsumowanie informacji o urządzeniach i typach kont obsługiwanych przez środowisko synchronizacji, wykonywania kopii zapasowych i przywracania w systemie Windows 10.

| Typ konta i operacja | Klasyczna | Aplikacje mobilne |
| --- | --- | --- |
| Azure Active Directory: synchronizacja |Tak |Nie |
| Azure Active Directory: kopia zapasowa/przywracanie |Nie |Nie |
| Konto Microsoft: synchronizacja |Tak |Tak |
| Konto Microsoft: kopia zapasowa/przywracanie |Nie |Tak |

## <a name="what-is-backup"></a>Co to jest kopia zapasowa?

Ustawienia systemu Windows są zwykle synchronizowane domyślnie, ale w przypadku niektórych ustawień tworzona jest tylko kopia zapasowa, na przykład lista zainstalowanych aplikacji na urządzeniu. Kopie zapasowe są przeznaczone tylko dla urządzeń przenośnych i obecnie nie są dostępne dla Enterprise State Roaming użytkowników. Kopia zapasowa używa konto Microsoft i zapisuje ustawienia i dane aplikacji w usłudze OneDrive. Jeśli użytkownik wyłączy synchronizację na urządzeniu za pomocą aplikacji ustawienia, dane aplikacji, które zwykle synchronizują, staną się tylko kopiami zapasowymi. Dostęp do danych kopii zapasowej można uzyskać tylko za pomocą operacji przywracania podczas pierwszego uruchomienia nowego urządzenia. Kopie zapasowe można wyłączyć za pomocą ustawień urządzenia i można je zarządzać i usuwać za pomocą konta usługi OneDrive użytkownika.

## <a name="windows-settings-overview"></a>Przegląd ustawień systemu Windows

Następujące grupy ustawień są dostępne dla użytkowników końcowych do włączania/wyłączania synchronizacji ustawień na urządzeniach z systemem Windows 10.

* Motyw: tło pulpitu, kafelek użytkownika, położenie paska zadań itp. 
* Ustawienia programu Internet Explorer: historia przeglądania, wpisywane adresy URL, Ulubione itd. 
* Hasła: Menedżer poświadczeń systemu Windows, w tym profile Wi-Fi 
* Preferencje językowe: słownik pisowni, ustawienia języka systemu 
* Łatwość dostępu: Narrator, klawiatura ekranowa, Lupa 
* Inne ustawienia systemu Windows: Zobacz szczegóły ustawień systemu Windows
* Ustawienie przeglądarki Microsoft Edge: Ulubione Microsoft Edge, lista do czytania i inne ustawienia

![Synchronizuj swoje ustawienia](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Ten artykuł ma zastosowanie do starszej przeglądarki opartej na języku HTML Microsoft Edge, która została uruchomiona z systemem Windows 10 w lipcu 2015. Artykuł nie ma zastosowania do nowej przeglądarki opartej na formacie chromu Microsoft Edge wydanej 15 stycznia 2020. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

Grupa ustawień przeglądarki Microsoft Edge (Ulubione, lista do czytania) Synchronizacja może być włączona lub wyłączona przez użytkowników końcowych za pomocą opcji menu ustawienia przeglądarki Microsoft Edge.

![Konto](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

W przypadku systemu Windows 10 w wersji 1803 lub nowszej, można włączyć lub wyłączyć synchronizację grupy ustawień programu Internet Explorer (Ulubione, adresy URL z określonym typem) przez użytkowników końcowych za pomocą opcji menu Ustawienia programu Internet Explorer. 

![Ustawienia](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Szczegóły ustawień systemu Windows

W poniższej tabeli inne wpisy w kolumnie grupa ustawień odnoszą się do ustawień, które można wyłączyć, przechodząc do pozycji Ustawienia > konta > zsynchronizuj ustawienia > innych ustawień systemu Windows. 

Wpisy wewnętrzne w kolumnie grupa ustawień odnoszą się do ustawień i aplikacji, które można wyłączyć tylko w ramach samej aplikacji lub wyłączając synchronizację dla całego urządzenia za pomocą ustawień zarządzania urządzeniami przenośnymi (MDM) lub zasady grupy.
Ustawienia, które nie są przekazywane ani synchronizowane, nie będą należeć do grupy.

| Ustawienia | Klasyczna | Aplikacje mobilne | Group (Grupa) |
| --- | --- | --- | --- |
| **Konta**: obraz konta |synchronizacja |X |Motyw |
| **Konta**: inne ustawienia konta |X |X | |
| **Advanced Mobile szerokopasmowa**: Nazwa sieci udostępniania połączenia internetowego (umożliwia Autowykrywanie hotspotów Wi-Fi mobilnych za pośrednictwem połączenia Bluetooth) |X |X |Hasła |
| **Dane aplikacji**: poszczególne aplikacje mogą synchronizować dane |Synchronizuj kopię zapasową |Synchronizuj kopię zapasową |internal |
| **Lista aplikacji**: lista zainstalowanych aplikacji |X |kopia zapasowa |Inne |
| **Bluetooth**: wszystkie ustawienia Bluetooth |X |X | |
| **Wiersz polecenia**: wiersz polecenia "ustawienia domyślne" |synchronizacja |X |internal |
| **Poświadczenia**: blokowanie poświadczeń |synchronizacja |synchronizacja |hasło |
| **Data, godzina i region**: automatyczna godzina (synchronizacja czasu internetowego) |synchronizacja |synchronizacja |language |
| **Data, godzina i region**: zegar 24-godzinny |synchronizacja |X |language |
| **Data, godzina i region**: Data i godzina |synchronizacja |X |language |
| **Data, godzina i region**: Strefa czasowa | |X |language |
| **Data, godzina i region**: czas letni |synchronizacja |X |language |
| **Data, godzina i region**: kraj/region |synchronizacja |X |language |
| **Data, godzina i region**: pierwszy dzień tygodnia |synchronizacja |X |language |
| **Data, godzina i region**: format regionu (ustawienia regionalne) |synchronizacja |X |language |
| **Data, godzina i region**: Data krótka |synchronizacja |X |language |
| **Data, godzina i region**: Data długa |synchronizacja |X |language |
| **Data, godzina i region**: godzina krótka |synchronizacja |X |language |
| **Data, godzina i region**: godzina długa |synchronizacja |X |language |
| **Personalizacja pulpitu**: kompozycja pulpitu (tło, systemowy kolor, domyślne dźwięki systemowe, wygaszacz ekranu) |synchronizacja |X |Motyw |
| **Personalizacja pulpitu**: tapeta pokazu slajdów |synchronizacja |X |Motyw |
| **Personalizacja pulpitu**: ustawienia paska zadań (pozycja, Autoukrywanie itp.) |synchronizacja |X |Motyw |
| **Personalizacja pulpitu**: układ ekranu startowego |X |kopia zapasowa | |
| **Urządzenia**: udostępnione drukarki, z którymi nawiązano połączenie |X |X |other |
| **Przeglądarka Microsoft Edge**: odczytywanie listy |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: Ulubione |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: najważniejsze witryny <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: wpisane adresy URL <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: ustawienia paska ulubionych <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: Pokaż przycisk Home <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: blokowanie wyskakujących okienek <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: pytaj, co należy zrobić przy każdym pobieraniu <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: Oferta zapisywania haseł <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: wysyłanie żądań nie Śledź <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: zapisywanie wpisów formularza <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: Pokaż wyszukiwanie i sugestie dotyczące witryn podczas wpisywania <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: Preferencje plików cookie <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: Zezwól lokacjom na zapisywanie chronionych licencji multimediów na moim urządzeniu <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Przeglądarka Microsoft Edge**: ustawienie czytnika ekranu <sup> [[1]](#footnote-1)</sup> |synchronizacja |synchronizacja |internal |
| **Duży kontrast**: włączone lub wyłączone |synchronizacja |X |ułatwienia dostępu |
| **Duży kontrast**: ustawienia motywu |synchronizacja |X |ułatwienia dostępu |
| **Internet Explorer**: otwieranie kart (URL i tytuł) |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: odczytywanie listy |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: wpisane adresy URL |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: historia przeglądania |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: Ulubione |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: wykluczone adresy URL |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: strony główne |synchronizacja |synchronizacja |Internet Explorer |
| **Internet Explorer**: sugestie dotyczące domen |synchronizacja |synchronizacja |Internet Explorer |
| **Klawiatura**: użytkownicy mogą włączać lub wyłączać klawiaturę ekranową |synchronizacja |X |ułatwienia dostępu |
| **Klawiatura**: Włącz funkcję Sticky Notes Yes (domyślnie wyłączona) |synchronizacja |X |ułatwienia dostępu |
| **Klawiatura**: Włącz klucze filtrów (domyślnie wyłączone) |synchronizacja |X |ułatwienia dostępu |
| **Klawiatura**: Włącz klawisze przełączania (domyślnie wyłączone) |synchronizacja |X |ułatwienia dostępu |
| **Internet Explorer**: język domeny: chiński (CHS) QWERTY — Włączanie samodzielnej nauki |synchronizacja |X |Język |
| **Język**: CHS QWERTY — Włączanie klasyfikacji dynamicznej kandydatów |synchronizacja |X |Język |
| **Język**: CHS QWERTY-char-Set chiński uproszczony |synchronizacja |X |Język |
| **Język**: CHS QWERTY-char-Set chiński tradycyjny |synchronizacja |X |Język |
| **Język**: CHS QWERTY — rozmyte pinyin |synchronizacja |kopia zapasowa |Język |
| **Język**: CHS par QWERTY — rozmyte |synchronizacja |kopia zapasowa |Język |
| **Język**: CHS QWERTY — pełen pinyin |synchronizacja |X |Język |
| **Język**: CHS QWERTY — podwójny pinyin |synchronizacja |X |Język |
| **Język**: CHS QWERTY — odczytywanie Autokorekty |synchronizacja |X |Język |
| **Język**: CHS — klawisz przełącznika QWERTY-C/E, Shift |synchronizacja |X |Język |
| **Język**: CHS QWERTY-C/E klawisz przełącznika, Ctrl |synchronizacja |X |Język |
| **Język**: CHS Wubi — tryb wprowadzania pojedynczego znaku |synchronizacja |X |Język |
| **Język**: CHS Wubi — pokazuje pozostałe kodowanie kandydata |synchronizacja |X |Język |
| **Język**: CHS Wubi-dźwięk, gdy 4-kodowanie jest nieprawidłowe |synchronizacja |X |Język |
| **Język**: CHT Bopomofo — Uwzględnij CJK EXT-A |synchronizacja |X |Język |
| **Język**: japoński IME — wpisywanie predykcyjne i słowa niestandardowe |synchronizacja |synchronizacja |Język |
| **Język**: KOREAŃSKI (Kor) IME |X |X |Język |
| **Język**: rozpoznawanie pisma ręcznego |X |X |Język |
| **Język**: profil języka |synchronizacja |kopia zapasowa |Język |
| **Język**: sprawdzanie pisowni i wyróżnianie błędów pisowni |synchronizacja |kopia zapasowa |Język |
| **Język**: Lista klawiatur |synchronizacja |kopia zapasowa |Język |
| **Ekran blokady**: wszystkie ustawienia ekranu blokady |X |X | |
| **Lupa**: wł. lub wył. (przełącznik główny) |X |X |Łatwość dostępu |
| **Lupa**: Włącz lub Wyłącz kolor niewersji (domyślnie wyłączone) |synchronizacja |X |Łatwość dostępu |
| **Lupa**: śledzenie — postępuj zgodnie z fokusem klawiatury |synchronizacja |X |Łatwość dostępu |
| **Lupa**: śledzenie — postępuj zgodnie z kursorem myszy |synchronizacja |X |Łatwość dostępu |
| **Lupa**: Rozpocznij, gdy użytkownicy logują się (domyślnie wyłączone) |synchronizacja |X |Łatwość dostępu |
| **Mysz**: zmiana rozmiaru kursora myszy |synchronizacja |X |other |
| **Mysz**: zmiana koloru kursora myszy |synchronizacja |X |other |
| **Mysz**: wszystkie inne ustawienia |X |X | |
| **Narrator**: szybkie uruchamianie |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą zmieniać wysokość głosu Narratora |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą włączać lub wyłączać wskazówki dotyczące odczytywania dla typowych elementów (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą włączać lub wyłączać, czy mogą słyszeć wpisane znaki (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: użytkownicy mogą włączać lub wyłączać, czy mogą słyszeć wpisane słowa (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: zawiera kursor wstawiania po programie narrator (domyślnie włączony) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: Włącz podświetlanie wizualizacji kursora (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: Odtwarzanie podpowiedzi audio (domyślnie włączone) |synchronizacja |X |Łatwość dostępu |
| **Narrator**: Aktywuj klucze na klawiaturze dotykowej po podniesieniu palca (domyślnie wyłączone) |synchronizacja |X |Łatwość dostępu |
| **Łatwość dostępu**: Ustawianie grubości miganego kursora |synchronizacja |X |Łatwość dostępu |
| **Łatwość dostępu**: Usuń obrazy tła (domyślnie wyłączone) |synchronizacja |X |Łatwość dostępu |
| **Włącz i uśpienie**: wszystkie ustawienia |X |X | |
| **Personalizacja ekranu startowego**: kolor akcentu (tylko telefon) |X |synchronizacja |Motyw |
| **Wpisywanie**: słownik ortograficzny |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Autokorekta błędnie napisanego wyrazu |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: wyróżnianie błędnie napisanych wyrazów |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Pokazuj sugestie tekstowe podczas pisania |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Dodaj odstęp po wybraniu sugestii tekstu |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Dodaj kropkę po dwukrotnym naciśnięciu spacji |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: wielkie litery w poszczególnych zdaniach |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Użyj wielkich liter po dwukrotnym naciśnięciu klawisza Shift |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: Odtwórz dźwięki klawiszy podczas pisania |synchronizacja |kopia zapasowa |Język |
| **Wpisywanie**: dane personalizacji dla klawiatury dotykowej |synchronizacja |kopia zapasowa |Język |
| **Wi-Fi**: profile Wi-Fi (tylko WPA) |synchronizacja |synchronizacja |Hasła |

###### <a name="footnote-1"></a>Przypis 1

Minimalna obsługiwana wersja systemu operacyjnego Windows Creator Update (kompilacja 15063). 

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem, zobacz [Omówienie usługi Enterprise State roaming](enterprise-state-roaming-overview.md).
