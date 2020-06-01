---
title: Microsoft Teams na pulpicie wirtualnym systemu Windows — Azure
description: Jak korzystać z programu Microsoft Teams na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 90432d3aa0ce9ebdecc7d0314b1352e46db0ac47
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234566"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Korzystanie z programu Microsoft Teams na pulpicie wirtualnym systemu Windows

>[!IMPORTANT]
>Optymalizacja multimediów dla programu Microsoft Teams jest obecnie dostępna w publicznej wersji zapoznawczej. Zalecamy przeprowadzenie oceny środowiska użytkownika zoptymalizowanego dla zespołów przed wdrożeniem zespołów dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.

>[!NOTE]
>Optymalizacja multimediów dla programu Microsoft Teams jest dostępna tylko dla klienta klasycznego systemu Windows na komputerach z systemem Windows 10. Optymalizacje multimediów wymagają klienta klasycznego systemu Windows w wersji 1.2.1026.0 lub nowszej.

Program Microsoft Teams na pulpicie wirtualnym systemu Windows obsługuje rozmowy i współpracę. Optymalizacje multimediów obsługują również funkcję wywoływania i funkcji spotkania. Aby dowiedzieć się więcej na temat korzystania z zespołów Microsoft Teams w środowiskach infrastruktury pulpitów wirtualnych (VDI), zobacz [zespoły zwirtualizowanej infrastruktury pulpitowej](/microsoftteams/teams-for-vdi/).

Dzięki optymalizacji multimediów dla zespołów Microsoft Teams klient klasyczny systemu Windows obsługuje dźwięk i wideo lokalnie na potrzeby wywołań i spotkań zespołów. Nadal możesz korzystać z zespołów Microsoft Teams w systemie Windows Virtual Desktop z innymi klientami bez zoptymalizowanych połączeń i spotkań. Zespoły i funkcje współpracy są obsługiwane na wszystkich platformach. Aby przekierować urządzenia lokalne w sesji zdalnej, zapoznaj się z tematem [dostosowywanie Remote Desktop Protocol właściwości dla puli hostów](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z programu Microsoft Teams na pulpicie wirtualnym systemu Windows, należy wykonać następujące czynności:

- [Przygotuj sieć](/microsoftteams/prepare-network/) dla programu Microsoft Teams.
- Zainstaluj [klienta klasycznego systemu Windows](connect-windows-7-and-10.md) na urządzeniu z systemem Windows 10, które spełnia [wymagania sprzętowe](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)zespołów Microsoft Teams.
- Nawiązywanie połączenia z maszyną wirtualną z systemem Windows 10 lub systemem Windows 10 Enterprise.

## <a name="install-the-teams-desktop-app"></a>Zainstaluj aplikację Teams Desktop

W tej sekcji pokazano, jak zainstalować aplikację Team Desktop w obrazie maszyny wirtualnej z systemem Windows 10 lub Windows 10 Enterprise. Aby dowiedzieć się więcej, zapoznaj się z [tematem Instalowanie lub aktualizowanie aplikacji Team Desktop w infrastrukturze VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/).

### <a name="prepare-your-image-for-teams"></a>Przygotowanie obrazu dla zespołów

Aby włączyć instalację zespołów dla poszczególnych komputerów, należy dla hosta ustawić następujący klucz rejestru:

1. Z menu Start Uruchom polecenie **regedit** jako administrator. Przejdź do **HKEY_LOCAL_MACHINE \software\microsoft\teams**.
2. Utwórz następującą wartość dla klucza zespoły:

| Nazwa             | Typ   | Dane/wartość  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Instalowanie usługi WebSocket zespołów

Zainstaluj [usługę WebSocket](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4vkL6) na obrazie maszyny wirtualnej. Jeśli wystąpi błąd instalacji, zainstaluj [najnowszy pakiet redystrybucyjny Microsoft Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) i spróbuj ponownie.

### <a name="install-microsoft-teams"></a>Zainstaluj program Microsoft Teams

Aplikację Team Desktop można wdrożyć przy użyciu instalacji na komputerze. Aby zainstalować program Microsoft Teams w środowisku pulpitu wirtualnego systemu Windows:

1. Pobierz [pakiet MSI Teams](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/) , który jest zgodny z Twoim środowiskiem. Zalecamy używanie Instalatora 64-bitowego w 64-bitowym systemie operacyjnym.
2. Uruchom to polecenie, aby zainstalować plik MSI na maszynie wirtualnej hosta.

      ```console
      msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
      ```

      Spowoduje to zainstalowanie zespołów do folderu Program Files (x86) w 64-bitowym systemie operacyjnym i folderze Program Files w 32-bitowym systemie operacyjnym. W tym momencie zostanie ukończona konfiguracja złota obrazu. Instalacja zespołów dla poszczególnych maszyn jest wymagana w przypadku konfiguracji nietrwałych.

      Przy następnym otwarciu zespołów w sesji zostanie wyświetlony monit o podanie poświadczeń.

      > [!NOTE]
      > Użytkownicy i Administratorzy nie mogą wyłączyć automatycznego uruchamiania dla zespołów podczas logowania.

      Aby odinstalować plik MSI z maszyny wirtualnej hosta, uruchom następujące polecenie:

      ```console
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Spowoduje to odinstalowanie zespołów z folderu Program Files (x86) lub plików programu w zależności od środowiska systemu operacyjnego.

      > [!NOTE]
      > W przypadku instalowania zespołów przy użyciu ustawienia MSI ALLUSER = 1 aktualizacje automatyczne zostaną wyłączone. Zalecamy konieczność aktualizacji zespołów co najmniej raz w miesiącu. Aby dowiedzieć się więcej o wdrażaniu aplikacji klasycznych zespołów, zapoznaj się z tematem [Wdróż aplikację Team Desktop na maszynie wirtualnej](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Sprawdź, czy optymalizacje multimediów zostały załadowane

Po zainstalowaniu usługi WebSocket i aplikacji Team Desktop wykonaj następujące kroki, aby sprawdzić, czy załadowano optymalizacje nośników zespołów:

1. Wybierz obraz profilu użytkownika, a następnie wybierz pozycję **informacje**.
2. Wybierz **wersję**.

      W przypadku załadowania optymalizacji multimediów transparent będzie przedstawiał **WVD nośnik zoptymalizowany**. Jeśli transparent pokazuje, że **multimedia WVD nie są połączone**, zamknij aplikację Teams i spróbuj ponownie.

3. Wybierz obraz profilu użytkownika, a następnie wybierz pozycję **Ustawienia**.

      W przypadku załadowania optymalizacji multimediów urządzenia audio i aparaty dostępne lokalnie zostaną wyliczone w menu urządzenie. Jeśli menu pokazuje **zdalny dźwięk**, zamknij aplikację Teams i spróbuj ponownie. Jeśli urządzenia nadal nie są wyświetlane w menu, Wróć do pozycji [Zainstaluj program Microsoft Teams](#install-microsoft-teams) i upewnij się, że ukończono proces instalacji.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Korzystanie z zespołów w środowisku zwirtualizowanym różni się od używania zespołów w środowisku niezwirtualizowanym. Aby uzyskać więcej informacji o ograniczeniach zespołów w środowiskach zwirtualizowanych, zapoznaj się z [zespołami dla zwirtualizowanej infrastruktury pulpitów](/microsoftteams/teams-for-vdi#known-issues-and-limitations/).

### <a name="client-deployment-installation-and-setup"></a>Wdrażanie, instalacja i instalacja klienta

- W przypadku instalacji na komputerze zespoły w infrastrukturze VDI nie są automatycznie aktualizowane w taki sam sposób, w jaki klienci nie korzystają z zespołów VDI. Aby zaktualizować klienta programu, należy zaktualizować obraz maszyny wirtualnej, instalując nowy plik MSI.
- Zespoły wyświetlają obecnie tylko strefę czasową UTC w rozmowie, kanałach i kalendarzu.
- Optymalizacja multimediów dla zespołów jest obsługiwana tylko dla klienta klasycznego systemu Windows na komputerach z systemem Windows 10.
- Używanie jawnych serwerów proxy HTTP zdefiniowanych w punkcie końcowym nie jest obsługiwane.

### <a name="calls-and-meetings"></a>Wywołania i spotkania

- Klient Team Desktop w środowiskach klasycznych systemu Windows nie obsługuje zdarzeń na żywo. Na razie zalecamy dołączenie wydarzeń na żywo z [klienta sieci Web zespołów](https://teams.microsoft.com) w sesji zdalnej.
- Minimalizacja aplikacji zespołów podczas rozmowy lub spotkania może spowodować, że przychodzące źródło wideo znika po rozszerzeniu aplikacji.
- Wywołania lub spotkania nie obsługują obecnie udostępniania aplikacji. Sesje pulpitu obsługują udostępnianie pulpitu.
- Po udostępnieniu pulpitu w konfiguracji z obsługą kilku monitorów wszystkie monitory są udostępniane.
- Nadaj formantowi kontrolę i Przejmij kontrolę nie są obecnie obsługiwane.
- Zespoły w systemie Windows Virtual Desktop obsługują tylko jedno przychodzące dane wejściowe wideo w danym momencie. Oznacza to, że za każdym razem, gdy ktoś podejmie próbę udostępnienia ekranu, pojawi się jego ekran zamiast ekranu lidera spotkania.
- Ze względu na ograniczenia protokołu WebRTC rozwiązanie przychodzące i wychodzące strumień wideo jest ograniczone do 720.
- Aplikacja Teams nie obsługuje przycisków HID ani kontrolek LED z innymi urządzeniami.

Znane problemy dotyczące zespołów, które nie są związane z zwirtualizowanymi środowiskami, można znaleźć [w temacie zespoły pomocy technicznej w Twojej organizacji](/microsoftteams/known-issues/) .

## <a name="feedback"></a>Opinia

Prześlij opinię na temat zespołów Microsoft Teams na pulpicie wirtualnym systemu Windows w witrynie Teams [UserVoice](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Zbierz dzienniki zespołów

Jeśli wystąpią problemy z aplikacją Team Desktop w środowisku pulpitu wirtualnego systemu Windows, Zbierz dzienniki klienta w obszarze **%AppData%\Microsoft\Teams\logs.txt** na maszynie wirtualnej hosta.

Jeśli wystąpią problemy z wywołaniami i spotkaniami, Zbierz dzienniki klienta sieci Web zespołów z kombinacją klawiszy **Ctrl**  +  **Alt**  +  **SHIFT**  +  **1**. Dzienniki zostaną zapisane w **dzienniku diagnostyki%userprofile%\Downloads\MSTeams DATE_TIME. txt** na maszynie wirtualnej hosta.

## <a name="contact-microsoft-teams-support"></a>Skontaktuj się z pomocą techniczną Microsoft Teams

Aby skontaktować się z pomocą techniczną Microsoft Teams, przejdź do [Centrum administracyjnego Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/contact-support-for-business-products?view=o365-worldwide&tabs=online).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie Remote Desktop Protocol właściwości dla puli hostów

Dostosowanie właściwości Remote Desktop Protocol puli hostów (RDP), takich jak środowisko monitorowania wieloskładnikowego lub włączenie funkcji przekierowywania mikrofonu i audio, pozwala zapewnić użytkownikom optymalne środowisko na podstawie ich potrzeb.

Ustaw następujące właściwości protokołu RDP, aby włączyć przekierowywanie mikrofonu i aparatu:

- `audiocapturemode:i:1`Włącza przechwytywanie audio z urządzenia lokalnego i redirets aplikacje audio w sesji zdalnej.
- `audiomode:i:0`odtwarza dźwięk na komputerze lokalnym.
- `camerastoredirect:s:*`przekierowuje wszystkie kamery.

Aby dowiedzieć się więcej, zapoznaj się z tematem [dostosowywanie Remote Desktop Protocol właściwości dla puli hostów](customize-rdp-properties.md).
