---
title: Samoobsługowe resetowanie haseł dla systemu Windows — Azure Active Directory
description: Jak włączyć funkcję samoobsługowego resetowania hasła przy użyciu zapomnianego hasła na ekranie logowania systemu Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f08161daf1d9c1a4431d9e3fba3ca741d88b16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743346"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Instrukcje: Włączanie resetowania hasła na ekranie logowania systemu Windows

W przypadku maszyn z systemem Windows 7, 8, 8,1 i 10 można umożliwić użytkownikom Resetowanie hasła na ekranie logowania do systemu Windows. Użytkownicy nie muszą już znajdować urządzenia z przeglądarką sieci Web, aby uzyskać dostęp do [portalu SSPR](https://aka.ms/sspr).

![Przykład ekranu logowania z systemem Windows 7 i 10 z pokazanym linkiem SSPR](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Ogólne ograniczenia

- Resetowanie hasła nie jest obecnie obsługiwane z poziomu Pulpit zdalny ani rozszerzonych sesji funkcji Hyper-V.
- Niektórzy dostawcy poświadczeń innych firm są znani, aby spowodować problemy z tą funkcją.
- Wyłączenie funkcji Kontrola konta użytkownika przez modyfikację [klucza rejestru EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) jest znane, aby powodować problemy.
- Ta funkcja nie działa w przypadku sieci z wdrożonym uwierzytelnianiem sieciowym 802.1 x i opcją "wykonaj bezpośrednio przed logowaniem użytkownika". W sieciach z wdrożonym uwierzytelnianiem sieci 802.1x zalecane jest używanie uwierzytelniania maszynowego w celu włączenia tej funkcji.
- Hybrydowe maszyny przyłączone do usługi Azure AD muszą mieć linię łączności sieciowej z kontrolerem domeny, aby użyć nowego hasła i zaktualizować buforowane poświadczenia.
- Jeśli używasz obrazu, przed uruchomieniem narzędzia Sysprep upewnij się, że pamięć podręczna sieci Web jest wyczyszczona dla wbudowanego administratora przed wykonaniem kroku CopyProfile. Więcej informacji na temat tego kroku można znaleźć w artykule dotyczącym pomocy technicznej [niska w przypadku używania niestandardowego domyślnego profilu użytkownika](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Następujące ustawienia są znane, aby zakłócać możliwość używania i resetowania haseł na urządzeniach z systemem Windows 10
    - Jeśli kombinacja klawiszy Ctrl + Alt + Del jest wymagana przez zasady w wersjach systemu Windows 10 przed v1809, **Resetowanie hasła** nie będzie działało.
    - Jeśli powiadomienia na ekranie blokady są wyłączone, pozycja **Resetuj hasło** nie będzie działać.
    - Ustawienie HideFastUserSwitching jest włączone lub ustawione na wartość 1
    - Ustawienie DontDisplayLastUserName jest włączone lub ustawione na wartość 1
    - Ustawienie NoLockScreen jest włączone lub ustawione na wartość 1
    - Ustawienie EnableLostMode jest określone na urządzeniu
    - Plik Explorer.exe został zastąpiony niestandardową powłoką
- Ta funkcja nie będzie działała w połączeniu następujących trzech ustawień.
    - Logowanie interakcyjne: nie wymagaj kombinacji klawiszy CTRL + ALT + DEL = wyłączone
    - DisableLockScreenAppNotifications = 1 lub włączony
    - Jednostka SKU systemu Windows nie jest w wersji Home ani Professional

## <a name="windows-10-password-reset"></a>Resetowanie hasła w systemie Windows 10

### <a name="windows-10-prerequisites"></a>Wymagania wstępne dotyczące systemu Windows 10

- Administrator musi włączyć funkcję samoobsługowego resetowania hasła w usłudze Azure AD z poziomu Azure Portal.
- **Użytkownicy muszą zarejestrować się w usłudze SSPR przed użyciem tej funkcji**
- Wymagania dotyczące serwera proxy sieci
   - Urządzenia z systemem Windows 10 
       - Port 443 do `passwordreset.microsoftonline.com` i`ajax.aspnetcdn.com`
       - Urządzenia z systemem Windows 10 obsługują tylko konfigurację serwera proxy na poziomie komputera
- Uruchom co najmniej system Windows 10, wersja kwiecień 2018 Update (v1803), a urządzenia muszą mieć jedną z tych opcji:
    - Dołączone do usługi Azure AD
    - hybrydowym dołączonym do usługi Azure AD.

### <a name="enable-for-windows-10-using-intune"></a>Włącz dla systemu Windows 10 przy użyciu usługi Intune

Wdrażanie zmiany konfiguracji w celu włączenia możliwości resetowania hasła z poziomu ekranu logowania przy użyciu usługi Intune jest metodą najbardziej elastyczną. Usługa Intune umożliwia wdrażanie zmiany konfiguracji dla określonej zdefiniowanej grupy maszyn. Ta metoda wymaga rejestracji urządzenia w usłudze Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Tworzenie zasad konfiguracji urządzenia w usłudze Intune

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Intune**.
1. Utwórz nowy profil konfiguracji urządzenia, przechodząc do profil **Konfiguracja** > urządzenia**Profile** > **Utwórz profil**
   - Podaj znaczącą nazwę profilu
   - Opcjonalnie podaj znaczący opis profilu
   - Platforma **Windows 10 i nowsze**
   - Typ profilu: **Niestandardowy**
1. Konfiguruj **Ustawienia**
   - **Dodaj** następujące ustawienie OMA-URI, aby włączyć link resetowania hasła
      - Podaj znaczącą nazwę, aby ułatwić zrozumienie działania ustawienia
      - Opcjonalnie podaj znaczący opis ustawienia
      - Ustaw pozycję **OMA-URI** na wartość `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - Ustaw pozycję **Typ danych** na **Liczba całkowita**
      - Ustaw pozycję **Wartość ** na **1**
      - Kliknij przycisk **OK** .
   - Kliknij przycisk **OK** .
1. Kliknij przycisk **Utwórz**
1. Te zasady mogą być przypisane do określonych użytkowników, urządzeń lub grup. Więcej informacji można znaleźć w artykule [przypisywanie profilów użytkowników i urządzeń w Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Włącz dla systemu Windows 10 przy użyciu rejestru

1. Zaloguj się do komputera PC z systemem Windows przy użyciu poświadczeń administracyjnych
1. Uruchom **regedit** jako administrator
1. Ustaw poniższy klucz rejestru
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Rozwiązywanie problemów z resetowaniem haseł systemu Windows 10

Dziennik inspekcji usługi Azure AD zawiera informacje dotyczące adresu IP i typu klienta, które są powiązane z żądaniem resetowania hasła.

![Przykładowe Resetowanie hasła w systemie Windows 7 w dzienniku inspekcji usługi Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Po zresetowaniu hasła przez użytkownika z ekranu logowania na urządzeniu z systemem Windows 10 `defaultuser1` zostaje utworzone konto tymczasowe o niskim poziomie uprawnień. To konto jest używane, aby zapewnić bezpieczeństwo procesu resetowania hasła. Samo konto ma losowo wygenerowane hasło, które nie jest wyświetlane na potrzeby logowania do urządzenia i zostanie automatycznie usunięte po zresetowaniu hasła przez użytkownika. Może `defaultuser` istnieć wiele profilów, ale można je bezpiecznie zignorować.

## <a name="windows-7-8-and-81-password-reset"></a>Resetowanie hasła dla systemu Windows 7, 8 i 8,1

### <a name="windows-7-8-and-81-prerequisites"></a>Wymagania wstępne dotyczące systemów Windows 7, 8 i 8,1

- Administrator musi włączyć funkcję samoobsługowego resetowania hasła w usłudze Azure AD z poziomu Azure Portal.
- **Użytkownicy muszą zarejestrować się w usłudze SSPR przed użyciem tej funkcji**
- Wymagania dotyczące serwera proxy sieci
   - Urządzenia z systemami Windows 7, 8 i 8,1
       - Port 443 do`passwordreset.microsoftonline.com`
- Poprawiony system operacyjny Windows 7 lub Windows 8.1.
- Protokół TLS 1,2 został włączony przy użyciu wskazówek dostępnych w [ustawieniach rejestru Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Jeśli na komputerze jest włączony więcej niż jeden dostawca poświadczeń innych firm, na ekranie logowania użytkownicy będą widzieć więcej niż jeden profil użytkownika.

> [!WARNING]
> Protokół TLS 1,2 musi być włączony, a nie tylko na wartość Autonegocjowanie

### <a name="install"></a>Instalowanie

1. Pobierz odpowiedni Instalator dla wersji systemu Windows, którą chcesz włączyć.
   - Oprogramowanie jest dostępne w centrum pobierania Microsoft w witrynie[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Zaloguj się na komputerze, na którym chcesz zainstalować program, a następnie uruchom Instalatora.
1. Po zakończeniu instalacji jest zdecydowanie zalecane.
1. Po ponownym uruchomieniu na ekranie logowania wybierz użytkownika, a następnie kliknij "zapomniane hasło?". w celu zainicjowania przepływu pracy resetowania hasła.
1. Ukończ przepływ pracy po wykonaniu kroków Onscreen, aby zresetować hasło.

![Przykładowo w systemie Windows 7 kliknięto "zapomniane hasło?" Przepływ SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalacja w trybie dyskretnym

- W przypadku instalacji dyskretnej Użyj polecenia "msiexec/i SsprWindowsLogon. PROD. msi/qn"
- W przypadku dezinstalacji dyskretnej Użyj polecenia "msiexec/x SsprWindowsLogon. PROD. msi/qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Rozwiązywanie problemów z resetowaniem haseł dla systemu Windows 7, 8 i 8,1

Zdarzenia będą rejestrowane zarówno na komputerze, jak i w usłudze Azure AD. Zdarzenia usługi Azure AD będą zawierać informacje o adresie IP i ClientType, w których wystąpiło Resetowanie hasła.

![Przykładowe Resetowanie hasła w systemie Windows 7 w dzienniku inspekcji usługi Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Jeśli wymagane jest dodatkowe rejestrowanie, klucz rejestru na komputerze można zmienić, aby włączyć pełne rejestrowanie. Włącz pełne rejestrowanie tylko do celów związanych z rozwiązywaniem problemów.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Aby włączyć pełne rejestrowanie, Utwórz `REG_DWORD: "EnableLogging"`a i ustaw wartość 1.
- Aby wyłączyć pełne rejestrowanie, Zmień wartość `REG_DWORD: "EnableLogging"` na 0.

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Teraz, po skonfigurowaniu resetowania haseł dla urządzeń z systemem Windows, jakie zmiany zostały wprowadzone dla użytkownika? Skąd użytkownicy będą wiedzieć, że mogą zresetować swoje hasło na ekranie logowania?

![Przykład ekranu logowania z systemem Windows 7 i 10 z pokazanym linkiem SSPR](./media/howto-sspr-windows/windows-reset-password.png)

Gdy użytkownicy próbują się zalogować, zobaczymy teraz link **resetowania** hasła lub **zapomnianego hasła** , które powoduje otwarcie funkcji samoobsługowego resetowania hasła na ekranie logowania. Ta funkcja umożliwia użytkownikom zresetowanie hasła bez konieczności uzyskiwania dostępu do przeglądarki internetowej przy użyciu innego urządzenia.

Wskazówki dotyczące używania tej funkcji będzie można znaleźć w artykule [Reset your work or school password (Resetowanie hasła służbowego)](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Następne kroki

[Zaplanuj metody uwierzytelniania, aby zezwolić](concept-authentication-methods.md)

[Konfigurowanie systemu Windows 10](https://docs.microsoft.com/windows/configuration/)
