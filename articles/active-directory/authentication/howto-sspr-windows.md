---
title: Samoobsługowe resetowanie haseł dla urządzeń z systemem Windows — Azure Active Directory
description: Informacje na temat włączania Azure Active Directory samoobsługowego resetowania hasła na ekranie logowania systemu Windows.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491389"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Włącz Azure Active Directory samoobsługowego resetowania hasła na ekranie logowania systemu Windows

Funkcja samoobsługowego resetowania hasła (SSPR) zapewnia użytkownikom w Azure Active Directory (Azure AD) możliwość zmiany lub resetowania hasła oraz braku administratora lub pomocy technicznej. Zazwyczaj użytkownicy otwierają przeglądarkę sieci Web na innym urządzeniu w celu uzyskania dostępu do [portalu SSPR](https://aka.ms/sspr). Aby usprawnić środowisko pracy na komputerach z systemem Windows 7, 8, 8,1 i 10, można umożliwić użytkownikom Resetowanie hasła na ekranie logowania systemu Windows.

![Przykład ekranu logowania z systemem Windows 7 i 10 z pokazanym linkiem SSPR](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> W tym samouczku przedstawiono administratora, w jaki sposób włączyć SSPR dla urządzeń z systemem Windows w przedsiębiorstwie.
>
> Jeśli Twój zespół IT nie włączył możliwości korzystania z usługi SSPR z urządzenia z systemem Windows lub masz problemy podczas logowania, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

## <a name="general-limitations"></a>Ogólne ograniczenia

Następujące ograniczenia dotyczą korzystania z programu SSPR z ekranu logowania systemu Windows:

- Resetowanie hasła nie jest obecnie obsługiwane z poziomu Pulpit zdalny ani rozszerzonych sesji funkcji Hyper-V.
- Niektórzy dostawcy poświadczeń innych firm są znani, aby spowodować problemy z tą funkcją.
- Wyłączenie funkcji Kontrola konta użytkownika przez modyfikację [klucza rejestru EnableLUA](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) jest znane, aby powodować problemy.
- Ta funkcja nie działa w przypadku sieci z wdrożonym uwierzytelnianiem sieciowym 802.1 x i opcją "wykonaj bezpośrednio przed logowaniem użytkownika". W przypadku sieci z wdrożonym uwierzytelnianiem sieciowym 802.1 x zaleca się używanie uwierzytelniania maszynowego w celu włączenia tej funkcji.
- Hybrydowe maszyny przyłączone do usługi Azure AD muszą mieć linię łączności sieciowej z kontrolerem domeny, aby użyć nowego hasła i zaktualizować buforowane poświadczenia. Oznacza to, że urządzenia muszą znajdować się w sieci wewnętrznej organizacji lub w sieci VPN z dostępem do sieci do lokalnego kontrolera domeny.
- Jeśli używasz obrazu, przed uruchomieniem narzędzia Sysprep upewnij się, że pamięć podręczna sieci Web jest wyczyszczona dla wbudowanego administratora przed wykonaniem kroku CopyProfile. Więcej informacji na temat tego kroku można znaleźć w artykule dotyczącym pomocy technicznej [niska w przypadku używania niestandardowego domyślnego profilu użytkownika](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Następujące ustawienia są znane, aby zakłócać możliwość używania i resetowania haseł na urządzeniach z systemem Windows 10:
    - Jeśli kombinacja klawiszy Ctrl + Alt + Del jest wymagana przez zasady w systemie Windows 10, **Resetowanie hasła** nie będzie działało.
    - Jeśli powiadomienia ekranu blokady są wyłączone, **Resetowanie hasła** nie będzie działało.
    - *HideFastUserSwitching* jest ustawiona na wartość Enabled lub 1
    - *DontDisplayLastUserName* jest ustawiona na wartość Enabled lub 1
    - *NoLockScreen* jest ustawiona na wartość Enabled lub 1
    - *EnableLostMode* jest ustawiona na urządzeniu
    - Plik Explorer.exe został zastąpiony niestandardową powłoką
- Ta funkcja nie będzie działała w połączeniu następujących trzech ustawień.
    - Logowanie interakcyjne: nie wymagaj kombinacji klawiszy CTRL + ALT + DEL = wyłączone
    - *DisableLockScreenAppNotifications* = 1 lub włączony
    - Jednostka SKU systemu Windows nie jest w wersji Home ani Professional

> [!NOTE]
> Te ograniczenia mają zastosowanie również do resetowania numeru PIN usługi Windows Hello dla firm z ekranu blokady urządzenia.
>

## <a name="windows-10-password-reset"></a>Resetowanie hasła w systemie Windows 10

Aby skonfigurować urządzenie z systemem Windows 10 pod kątem SSPR na ekranie logowania, zapoznaj się z następującymi wymaganiami wstępnymi i czynnościami konfiguracyjnymi.

### <a name="windows-10-prerequisites"></a>Wymagania wstępne dotyczące systemu Windows 10

- Administrator [musi włączyć funkcję samoobsługowego resetowania hasła w usłudze Azure AD z poziomu Azure Portal](tutorial-enable-sspr.md).
- Użytkownicy muszą zarejestrować się w usłudze SSPR przed użyciem tej funkcji w witrynie [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Aby nie używać SSPR z ekranu logowania systemu Windows, wszyscy użytkownicy muszą podać informacje kontaktowe uwierzytelniania, zanim będą mogli zresetować swoje hasła.
- Wymagania dotyczące serwera proxy sieci:
    - Port 443 do `passwordreset.microsoftonline.com` i `ajax.aspnetcdn.com`
    - Urządzenia z systemem Windows 10 obsługują tylko konfigurację serwera proxy na poziomie komputera.
- Uruchom co najmniej system Windows 10, wersja kwiecień 2018 Update (v1803), a urządzenia muszą mieć jedną z tych opcji:
    - Dołączone do usługi Azure AD
    - hybrydowym dołączonym do usługi Azure AD.

### <a name="enable-for-windows-10-using-intune"></a>Włącz dla systemu Windows 10 przy użyciu usługi Intune

Wdrożenie zmiany konfiguracji w celu włączenia SSPR na ekranie logowania przy użyciu usługi Intune to najbardziej elastyczna metoda. Usługa Intune umożliwia wdrażanie zmiany konfiguracji dla określonej zdefiniowanej grupy maszyn. Ta metoda wymaga rejestracji urządzenia w usłudze Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Tworzenie zasad konfiguracji urządzenia w usłudze Intune

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Intune**.
1. Utwórz nowy profil konfiguracji urządzenia, przechodząc do opcji profile **konfiguracji urządzeń**  >  , a następnie wybierz pozycję **+ Utwórz profil** .
   - W przypadku **platformy** wybierz *system Windows 10 i nowsze*
   - W obszarze **Typ profilu** wybierz pozycję *niestandardowy* .
1. Wybierz pozycję **Utwórz**, a następnie podaj opisową nazwę profilu, na przykład *SSPR ekranu logowania systemu Windows 10*

    Opcjonalnie podaj opis profilu, a następnie wybierz przycisk **dalej**.
1. W obszarze *Ustawienia konfiguracji* wybierz pozycję **Dodaj** i podaj następujące ustawienie OMA-URI, aby włączyć link resetowania hasła:
      - Podaj opisową nazwę, aby wyjaśnić, co robi to ustawienie, na przykład *Dodaj link SSPR*.
      - Opcjonalnie podaj zrozumiały opis ustawienia.
      - Ustaw pozycję **OMA-URI** na wartość `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - Ustaw pozycję **Typ danych** na **Liczba całkowita**
      - Ustaw pozycję **Wartość** na **1**

    Wybierz pozycję **Dodaj**, a następnie przycisk **dalej**.
1. Zasady mogą być przypisane do określonych użytkowników, urządzeń lub grup. Należy przypisać profil zgodnie z potrzebami środowiska, najlepiej do grupy testowej urządzeń, a następnie wybrać przycisk **dalej**.

    Aby uzyskać więcej informacji, zobacz [przypisywanie profilów użytkowników i urządzeń w Microsoft Intune](/mem/intune/configuration/device-profile-assign).

1. Skonfiguruj reguły zastosowania jako odpowiednie dla danego środowiska, np. Aby *przypisać profil, jeśli wersja systemu operacyjnego to Windows 10 Enterprise*, a następnie wybierz przycisk **dalej**.
1. Przejrzyj swój profil, a następnie wybierz pozycję **Utwórz**.

### <a name="enable-for-windows-10-using-the-registry"></a>Włącz dla systemu Windows 10 przy użyciu rejestru

Aby włączyć SSPR na ekranie logowania przy użyciu klucza rejestru, wykonaj następujące czynności:

1. Zaloguj się do komputera z systemem Windows przy użyciu poświadczeń administracyjnych.
1. Naciśnij pozycję **Windows**  +  **R** , aby otworzyć okno dialogowe *uruchamiania* , a następnie uruchom polecenie **regedit** jako administrator
1. Ustaw poniższy klucz rejestru:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Rozwiązywanie problemów z resetowaniem haseł systemu Windows 10

Jeśli masz problemy z używaniem SSPR z ekranu logowania systemu Windows, dziennik inspekcji usługi Azure AD zawiera informacje o adresie IP i elemencie *ClientType* , w którym wystąpiło Resetowanie hasła, jak pokazano w poniższym przykładzie danych wyjściowych:

![Przykładowe Resetowanie hasła w systemie Windows 7 w dzienniku inspekcji usługi Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Po zresetowaniu hasła przez użytkownika z ekranu logowania na urządzeniu z systemem Windows 10 zostaje utworzone konto tymczasowe o niskim poziomie uprawnień `defaultuser1` . To konto jest używane, aby zapewnić bezpieczeństwo procesu resetowania hasła.

Samo konto ma losowo wygenerowane hasło, które nie jest wyświetlane na potrzeby logowania do urządzenia i jest automatycznie usuwane po zresetowaniu hasła przez użytkownika. `defaultuser`Może istnieć wiele profilów, ale można je bezpiecznie zignorować.

## <a name="windows-7-8-and-81-password-reset"></a>Resetowanie hasła dla systemu Windows 7, 8 i 8,1

Aby skonfigurować urządzenie z systemem Windows 7, 8 lub 8,1 na potrzeby usługi SSPR na ekranie logowania, zapoznaj się z następującymi wymaganiami wstępnymi i czynnościami konfiguracyjnymi.

### <a name="windows-7-8-and-81-prerequisites"></a>Wymagania wstępne dotyczące systemów Windows 7, 8 i 8,1

- Administrator [musi włączyć funkcję samoobsługowego resetowania hasła w usłudze Azure AD z poziomu Azure Portal](tutorial-enable-sspr.md).
- Użytkownicy muszą zarejestrować się w usłudze SSPR przed użyciem tej funkcji w witrynie [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Aby nie używać SSPR z ekranu logowania systemu Windows, wszyscy użytkownicy muszą podać informacje kontaktowe uwierzytelniania, zanim będą mogli zresetować swoje hasła.
- Wymagania dotyczące serwera proxy sieci:
    - Port 443 do `passwordreset.microsoftonline.com`
- Poprawiony system operacyjny Windows 7 lub Windows 8.1.
- Protokół TLS 1,2 został włączony przy użyciu wskazówek dostępnych w [ustawieniach rejestru Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings#tls-12).
- Jeśli na komputerze jest włączony więcej niż jeden dostawca poświadczeń innych firm, na ekranie logowania użytkownicy zobaczą więcej niż jeden profil użytkownika.

> [!WARNING]
> Protokół TLS 1,2 musi być włączony, a nie tylko na wartość Autonegocjowanie.

### <a name="install"></a>Instalowanie

W przypadku systemów Windows 7, 8 i 8,1 na komputerze musi być zainstalowany niewielki składnik, aby włączyć SSPR na ekranie logowania. Aby zainstalować ten składnik SSPR, wykonaj następujące czynności:

1. Pobierz odpowiedni Instalator dla wersji systemu Windows, którą chcesz włączyć.

    Instalator oprogramowania jest dostępny w centrum pobierania Microsoft na stronie [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Zaloguj się na komputerze, na którym chcesz zainstalować program, a następnie uruchom Instalatora.
1. Po zakończeniu instalacji jest zdecydowanie zalecane.
1. Po ponownym uruchomieniu na ekranie logowania wybierz użytkownika i wybierz pozycję "zapomniane hasło?". w celu zainicjowania przepływu pracy resetowania hasła.
1. Ukończ przepływ pracy po wykonaniu kroków Onscreen, aby zresetować hasło.

![Przykładowo w systemie Windows 7 kliknięto "zapomniane hasło?" Przepływ SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalacja w trybie dyskretnym

Składnik SSPR można zainstalować lub odinstalować bez monitowania przy użyciu następujących poleceń:

- W przypadku instalacji dyskretnej Użyj polecenia "msiexec/i SsprWindowsLogon.PROD.msi/qn"
- W przypadku dezinstalacji dyskretnej Użyj polecenia "msiexec/x SsprWindowsLogon.PROD.msi/qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Rozwiązywanie problemów z resetowaniem haseł dla systemu Windows 7, 8 i 8,1

Jeśli masz problemy z użyciem SSPR z ekranu logowania systemu Windows, zdarzenia są rejestrowane zarówno na komputerze, jak i w usłudze Azure AD. Zdarzenia usługi Azure AD zawierają informacje o adresie IP i elemencie ClientType, w którym wystąpiło Resetowanie hasła, jak pokazano w następujących przykładowych danych wyjściowych:

![Przykładowe Resetowanie hasła w systemie Windows 7 w dzienniku inspekcji usługi Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Jeśli wymagane jest dodatkowe rejestrowanie, klucz rejestru na komputerze można zmienić, aby włączyć pełne rejestrowanie. Włącz pełne rejestrowanie do celów związanych z rozwiązywaniem problemów przy użyciu następującej wartości klucza rejestru:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Aby włączyć pełne rejestrowanie, Utwórz a `REG_DWORD: "EnableLogging"` i ustaw wartość 1.
- Aby wyłączyć pełne rejestrowanie, Zmień wartość `REG_DWORD: "EnableLogging"` na 0.

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Jakie zmiany dla użytkownika są skonfigurowane dla urządzeń z systemem Windows SSPR? Skąd użytkownicy będą wiedzieć, że mogą zresetować swoje hasło na ekranie logowania? Poniższe przykładowe zrzuty ekranu przedstawiają dodatkowe opcje użytkownika resetowania hasła przy użyciu SSPR:

![Przykład ekranu logowania z systemem Windows 7 i 10 z pokazanym linkiem SSPR](./media/howto-sspr-windows/windows-reset-password.png)

Gdy użytkownik próbuje się zalogować, zobaczy link **resetowania** hasła lub **zapomnianego hasła** , które otwiera środowisko samoobsługowego resetowania hasła na ekranie logowania. Ta funkcja umożliwia użytkownikom zresetowanie hasła bez konieczności uzyskiwania dostępu do przeglądarki internetowej przy użyciu innego urządzenia.

Więcej informacji na temat korzystania z tej funkcji można znaleźć w temacie [Resetowanie hasła](../user-help/active-directory-passwords-update-your-own-password.md) służbowego

## <a name="next-steps"></a>Następne kroki

Aby uprościć rejestrację użytkowników, możesz [wstępnie wypełnić informacje kontaktowe uwierzytelniania użytkownika dla SSPR](howto-sspr-authenticationdata.md).
