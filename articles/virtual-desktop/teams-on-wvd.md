---
title: Microsoft Teams na pulpicie wirtualnym systemu Windows — Azure
description: Jak korzystać z programu Microsoft Teams na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 11/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 101b3a05591a7815ba28756bb5b07e855b64e769
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505550"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Korzystanie z programu Microsoft Teams na pulpicie wirtualnym systemu Windows

>[!IMPORTANT]
>Optymalizacja multimediów dla zespołów jest obsługiwana w środowiskach Microsoft 365 administracji publicznej. Optymalizacja multimediów dla zespołów nie jest obsługiwana w przypadku GCC-High i DoD.

>[!NOTE]
>Optymalizacja multimediów dla programu Microsoft Teams jest dostępna tylko dla klienta klasycznego systemu Windows na komputerach z systemem Windows 10. Optymalizacje multimediów wymagają klienta klasycznego systemu Windows w wersji 1.2.1026.0 lub nowszej.

Program Microsoft Teams na pulpicie wirtualnym systemu Windows obsługuje rozmowy i współpracę. Optymalizacje multimediów obsługują również funkcję wywoływania i funkcji spotkania. Aby dowiedzieć się więcej na temat korzystania z zespołów Microsoft Teams w środowiskach infrastruktury pulpitów wirtualnych (VDI), zobacz [zespoły zwirtualizowanej infrastruktury pulpitowej](/microsoftteams/teams-for-vdi/).

Dzięki optymalizacji multimediów dla zespołów Microsoft Teams klient klasyczny systemu Windows obsługuje dźwięk i wideo lokalnie na potrzeby wywołań i spotkań zespołów. Nadal możesz korzystać z zespołów Microsoft Teams w systemie Windows Virtual Desktop z innymi klientami bez zoptymalizowanych połączeń i spotkań. Zespoły i funkcje współpracy są obsługiwane na wszystkich platformach. Aby przekierować urządzenia lokalne w sesji zdalnej, zapoznaj się z tematem [dostosowywanie Remote Desktop Protocol właściwości dla puli hostów](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z programu Microsoft Teams na pulpicie wirtualnym systemu Windows, należy wykonać następujące czynności:

- [Przygotuj sieć](/microsoftteams/prepare-network/) dla programu Microsoft Teams.
- Zainstaluj [klienta pulpitu systemu Windows](connect-windows-7-10.md) na urządzeniu z systemem Windows 10 lub Windows 10 IoT Enterprise spełniającym wymagania sprzętowe zespołów firmy Microsoft [dla zespołów na komputerze z systemem Windows](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/).
- Nawiązywanie połączenia z maszyną wirtualną z systemem Windows 10 lub systemem Windows 10 Enterprise.

## <a name="install-the-teams-desktop-app"></a>Zainstaluj aplikację Teams Desktop

W tej sekcji pokazano, jak zainstalować aplikację Team Desktop w obrazie maszyny wirtualnej z systemem Windows 10 lub Windows 10 Enterprise. Aby dowiedzieć się więcej, zapoznaj się z [tematem Instalowanie lub aktualizowanie aplikacji Team Desktop w infrastrukturze VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi).

### <a name="prepare-your-image-for-teams"></a>Przygotowanie obrazu dla zespołów

Aby włączyć optymalizację multimediów dla zespołów, należy dla hosta ustawić następujący klucz rejestru:

1. Z menu Start Uruchom polecenie **regedit** jako administrator. Przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**. Utwórz klucz zespołów, jeśli jeszcze nie istnieje.

2. Utwórz następującą wartość dla klucza zespoły:

| Nazwa             | Typ   | Dane/wartość  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Instalowanie usługi WebSocket zespołów

Zainstaluj najnowszą [pulpit zdalny usługę readresatora protokołu WebRTC](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) na obrazie maszyny wirtualnej. Jeśli wystąpi błąd instalacji, zainstaluj [najnowszy pakiet redystrybucyjny Microsoft Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) i spróbuj ponownie.

#### <a name="latest-websocket-service-versions"></a>Najnowsze wersje usługi WebSocket

W poniższej tabeli wymieniono najnowsze wersje usługi WebSocket:

|Wersja        |Data wydania  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>Aktualizacje dla wersji 1.0.2006.11001

- Rozwiązano problem polegający na tym, że aplikacja Teams jest zminimalizowana podczas rozmowy lub spotkania z powodu przychodzącego wideo do porzucenia.
- Dodano obsługę wybierania jednego monitora, który ma zostać udostępniony w sesjach pulpitu z obsługą wielu monitorów.

### <a name="install-microsoft-teams"></a>Zainstaluj program Microsoft Teams

Aplikację Team Desktop można wdrożyć przy użyciu instalacji na komputerze lub na użytkownika. Aby zainstalować program Microsoft Teams w środowisku pulpitu wirtualnego systemu Windows:

1. Pobierz [pakiet MSI Teams](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) , który jest zgodny z Twoim środowiskiem. Zalecamy używanie Instalatora 64-bitowego w 64-bitowym systemie operacyjnym.

      > [!IMPORTANT]
      > Najnowsza aktualizacja programu Teams Client Version 1.3.00.21759 rozwiązała problem polegający na tym, że zespoły pokazały strefę czasową UTC w rozmowie, kanałach i kalendarzu. Nowa wersja klienta będzie zawierać strefę czasową sesji zdalnej.

2. Uruchom jedno z następujących poleceń, aby zainstalować plik MSI na maszynie wirtualnej hosta:

    - Instalacja na użytkownika

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        Ten proces jest instalacją domyślną, która instaluje zespoły w folderze **% AppData%** użytkownika. Zespoły nie będą działały prawidłowo z instalacją na użytkownika w przypadku nietrwałej instalacji.

    - Instalacja na komputer

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        Spowoduje to zainstalowanie zespołów do folderu Program Files (x86) w 32-bitowym systemie operacyjnym i folderze Program Files w 64-bitowym systemie operacyjnym. W tym momencie zostanie ukończona konfiguracja złota obrazu. Instalacja zespołów dla poszczególnych maszyn jest wymagana w przypadku konfiguracji nietrwałych.

        Istnieją dwie flagi, które mogą być ustawiane podczas instalowania zespołów, **ALLUSER = 1** i **ALLUSERS = 1**. Ważne jest, aby zrozumieć różnicę między tymi parametrami. Parametr **ALLUSER = 1** jest używany tylko w środowiskach infrastruktury VDI do określania instalacji dla komputera. Parametr **ALLUSERS = 1** może być używany w środowiskach innych niż infrastruktura VDI i VDI. Po ustawieniu tego parametru **zespoły Machine-Wide Instalator** pojawiają się w oknie Programy i funkcje w panelu sterowania, a także aplikacje & funkcje w ustawieniach systemu Windows. Wszyscy użytkownicy z poświadczeniami administratora na komputerze mogą odinstalowywać zespoły.

        > [!NOTE]
        > Użytkownicy i Administratorzy nie mogą wyłączyć automatycznego uruchamiania dla zespołów podczas logowania.

3. Aby odinstalować plik MSI z maszyny wirtualnej hosta, uruchom następujące polecenie:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Spowoduje to odinstalowanie zespołów z folderu Program Files (x86) lub plików programu w zależności od środowiska systemu operacyjnego.

      > [!NOTE]
      > W przypadku instalowania zespołów przy użyciu ustawienia MSI ALLUSER = 1 aktualizacje automatyczne zostaną wyłączone. Zalecamy konieczność aktualizacji zespołów co najmniej raz w miesiącu. Aby dowiedzieć się więcej o wdrażaniu aplikacji klasycznych zespołów, zapoznaj się z tematem [Wdróż aplikację Team Desktop na maszynie wirtualnej](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Sprawdź, czy optymalizacje multimediów zostały załadowane

Po zainstalowaniu usługi WebSocket i aplikacji Team Desktop wykonaj następujące kroki, aby sprawdzić, czy załadowano optymalizacje nośników zespołów:

1. Zamknij i uruchom ponownie aplikację Teams.

2. Wybierz obraz profilu użytkownika, a następnie wybierz pozycję **informacje**.

3. Wybierz **wersję**.

      W przypadku załadowania optymalizacji multimediów transparent będzie przedstawiał **WVD nośnik zoptymalizowany**. Jeśli transparent pokazuje, że **multimedia WVD nie są połączone** , zamknij aplikację Teams i spróbuj ponownie.

4. Wybierz obraz profilu użytkownika, a następnie wybierz pozycję **Ustawienia**.

      W przypadku załadowania optymalizacji multimediów urządzenia audio i aparaty dostępne lokalnie zostaną wyliczone w menu urządzenie. Jeśli menu pokazuje **zdalny dźwięk** , zamknij aplikację Teams i spróbuj ponownie. Jeśli urządzenia nadal nie są wyświetlane w menu, sprawdź ustawienia prywatności na komputerze lokalnym. Upewnij się, że w obszarze **Ustawienia**  >  uprawnienia do aplikacji **ochrony prywatności**  >  **App permissions** ustawienie **Zezwalaj aplikacjom na dostęp do mikrofonu** jest **włączone**. Rozłącz się z sesją zdalną, a następnie ponownie połącz i sprawdź urządzenia audio i wideo. Aby dołączyć wywołania i spotkania z wideo, należy również udzielić aplikacji dostępu do aparatu.

      Jeśli Optymalizacja nie zostanie załadowana, Odinstaluj, a następnie ponownie zainstaluj zespoły i sprawdź ponownie.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Korzystanie z zespołów w środowisku zwirtualizowanym różni się od używania zespołów w środowisku niezwirtualizowanym. Aby uzyskać więcej informacji o ograniczeniach zespołów w środowiskach zwirtualizowanych, zapoznaj się z [zespołami dla zwirtualizowanej infrastruktury pulpitów](/microsoftteams/teams-for-vdi#known-issues-and-limitations).

### <a name="client-deployment-installation-and-setup"></a>Wdrażanie, instalacja i instalacja klienta

- W przypadku instalacji na komputerze zespoły w infrastrukturze VDI nie są automatycznie aktualizowane w taki sam sposób, w jaki klienci nie korzystają z zespołów VDI. Aby zaktualizować klienta programu, należy zaktualizować obraz maszyny wirtualnej, instalując nowy plik MSI.
- Optymalizacja multimediów dla zespołów jest obsługiwana tylko dla klienta klasycznego systemu Windows na komputerach z systemem Windows 10.
- Używanie jawnych serwerów proxy HTTP zdefiniowanych w punkcie końcowym nie jest obsługiwane.

### <a name="calls-and-meetings"></a>Wywołania i spotkania

- Klient Team Desktop w środowiskach klasycznych systemu Windows nie obsługuje zdarzeń na żywo. Na razie zalecamy dołączenie wydarzeń na żywo z [klienta sieci Web zespołów](https://teams.microsoft.com) w sesji zdalnej.
- Wywołania lub spotkania nie obsługują obecnie udostępniania aplikacji. Sesje pulpitu obsługują udostępnianie pulpitu.
- Nadaj formantowi kontrolę i Przejmij kontrolę nie są obecnie obsługiwane.
- Zespoły w systemie Windows Virtual Desktop obsługują tylko jedno przychodzące dane wejściowe wideo w danym momencie. Oznacza to, że za każdym razem, gdy ktoś podejmie próbę udostępnienia ekranu, pojawi się jego ekran zamiast ekranu lidera spotkania.
- Ze względu na ograniczenia protokołu WebRTC rozwiązanie przychodzące i wychodzące strumień wideo jest ograniczone do 720.
- Aplikacja Teams nie obsługuje przycisków HID ani kontrolek LED z innymi urządzeniami.

Znane problemy dotyczące zespołów, które nie są związane z zwirtualizowanymi środowiskami, można znaleźć [w temacie zespoły pomocy technicznej w Twojej organizacji](/microsoftteams/known-issues) .

## <a name="uservoice-site"></a>Witryna UserVoice

Prześlij opinię na temat zespołów Microsoft Teams na pulpicie wirtualnym systemu Windows w witrynie Teams [UserVoice](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Zbierz dzienniki zespołów

Jeśli wystąpią problemy z aplikacją Team Desktop w środowisku pulpitu wirtualnego systemu Windows, Zbierz dzienniki klienta w folderze **% AppData% \Microsoft\Teams\logs.txt** na maszynie wirtualnej hosta.

Jeśli wystąpią problemy z wywołaniami i spotkaniami, Zbierz dzienniki klienta sieci Web zespołów z kombinacją klawiszy **Ctrl**  +  **Alt**  +  **SHIFT**  +  **1**. Dzienniki zostaną zapisane w **DATE_TIME.txtdzienniku diagnostyki%USERPROFILE%\Downloads\MSTeams** na maszynie wirtualnej hosta.

## <a name="contact-microsoft-teams-support"></a>Skontaktuj się z pomocą techniczną Microsoft Teams

Aby skontaktować się z pomocą techniczną Microsoft Teams, przejdź do [Centrum administracyjnego Microsoft 365](/microsoft-365/admin/contact-support-for-business-products).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie Remote Desktop Protocol właściwości dla puli hostów

Dostosowanie właściwości Remote Desktop Protocol puli hostów (RDP), takich jak środowisko monitorowania wieloskładnikowego lub włączenie funkcji przekierowywania mikrofonu i audio, pozwala zapewnić użytkownikom optymalne środowisko na podstawie ich potrzeb.

Włączanie przekierowań urządzeń nie jest wymagane w przypadku korzystania z zespołów z optymalizacją multimediów. Jeśli używasz zespołów bez optymalizacji multimediów, ustaw następujące właściwości protokołu RDP, aby włączyć przekierowywanie mikrofonu i aparatu:

- `audiocapturemode:i:1` Włącza przechwytywanie audio z urządzenia lokalnego i przekierowuje aplikacje audio w sesji zdalnej.
- `audiomode:i:0` odtwarza dźwięk na komputerze lokalnym.
- `camerastoredirect:s:*` przekierowuje wszystkie kamery.

Aby dowiedzieć się więcej, zapoznaj się z tematem [dostosowywanie Remote Desktop Protocol właściwości dla puli hostów](customize-rdp-properties.md).
