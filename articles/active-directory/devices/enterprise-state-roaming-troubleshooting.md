---
title: Rozwiązywanie problemów Enterprise State Roaming w programie Azure Active Directory
description: Zawiera odpowiedzi na niektóre pytania, które Administratorzy IT mogą mieć informacje o ustawieniach i synchronizacji danych aplikacji.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: e427a68751cc348a7b0024fb01402bd15ca87d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705728"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Rozwiązywanie problemów Enterprise State Roaming ustawień w Azure Active Directory

Ten temat zawiera informacje dotyczące rozwiązywania problemów i diagnozowania problemów z programem Enterprise State Roaming i zawiera listę znanych problemów.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Ten artykuł ma zastosowanie do starszej przeglądarki opartej na języku HTML Microsoft Edge, która została uruchomiona z systemem Windows 10 w lipcu 2015. Artykuł nie ma zastosowania do nowej przeglądarki opartej na formacie chromu Microsoft Edge wydanej 15 stycznia 2020. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Kroki wstępne dotyczące rozwiązywania problemów 

Przed rozpoczęciem rozwiązywania problemów sprawdź, czy użytkownik i urządzenie zostały prawidłowo skonfigurowane oraz czy wszystkie wymagania Enterprise State Roaming są spełnione przez urządzenie i użytkownika. 

1. System Windows 10 z najnowszymi aktualizacjami i minimalna wersja 1511 (kompilacja systemu operacyjnego 10586 lub nowsza) jest zainstalowana na urządzeniu. 
1. Urządzenie jest dołączone do usługi Azure AD lub hybrydowej usługi Azure AD. Aby uzyskać więcej informacji, zobacz [jak uzyskać urządzenie pod kontrolą usługi Azure AD](overview.md).
1. Upewnij się, że **Enterprise State roaming** jest włączona dla dzierżawy w usłudze Azure AD, zgodnie z opisem w artykule [Aby włączyć Enterprise State roaming](enterprise-state-roaming-enable.md). Możesz włączyć roaming dla wszystkich użytkowników lub tylko dla wybranej grupy użytkowników.
1. Użytkownikowi przypisano licencję Azure Active Directory — wersja Premium.  
1. Urządzenie musi zostać ponownie uruchomione i użytkownik musi się zalogować, aby uzyskać dostęp do funkcji Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Informacje, które należy podać, gdy będziesz potrzebować pomocy
Jeśli nie możesz rozwiązać problemu z poniższymi wskazówkami, możesz skontaktować się z naszymi inżynierami pomocy technicznej. Podczas kontaktowania się z nimi należy uwzględnić następujące informacje:

* **Ogólny opis błędu**: czy komunikaty o błędach są widoczne dla użytkownika? W przypadku braku komunikatu o błędzie opisz szczegóły nieoczekiwane zachowanie. Jakie funkcje są włączone do synchronizacji i czego oczekuje użytkownik? Czy wiele funkcji nie jest synchronizowanych lub jest odizolowane od jednej?
* **Których dotyczy użytkownik** — czy synchronizacja działa i niepowodzenie dla jednego użytkownika lub wielu użytkowników? Ile urządzeń jest używanych na użytkownika? Czy wszystkie te elementy nie są synchronizowane lub niektóre z nich są synchronizowane, a niektóre nie są synchronizowane?
* **Informacje o użytkowniku** — tożsamość, która jest używana przez użytkownika do logowania się na urządzeniu? Jak użytkownik loguje się na urządzeniu? Czy są częścią wybranej grupy zabezpieczeń, którą można synchronizować? 
* **Informacje o urządzeniu** — czy to urządzenie jest przyłączone do usługi Azure AD lub przyłączone do domeny? Na czym polega kompilacja? Jakie są najnowsze aktualizacje?
* **Data/Godzina/Strefa czasowa** — dokładna data i godzina wykroczenia błędu (obejmuje strefę czasową)?

W tym informacje pomagają nam szybko rozwiązać swój problem.

## <a name="troubleshooting-and-diagnosing-issues"></a>Rozwiązywanie i diagnozowanie problemów

Ta sekcja zawiera sugestie dotyczące rozwiązywania problemów i diagnozowania zagadnień związanych z Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Weryfikowanie synchronizacji i strony ustawień "Synchronizuj ustawienia" 

1. Po dołączeniu komputera z systemem Windows 10 do domeny, która jest skonfigurowana tak, aby zezwalać na Enterprise State Roaming, zaloguj się przy użyciu konta służbowego. Przejdź do pozycji **Ustawienia**  >  **konta**  >  **zsynchronizuj ustawienia** i upewnij się, że synchronizacja oraz poszczególne ustawienia są włączone, a Góra strony Ustawienia wskazuje, że jest synchronizowana z kontem służbowym. Potwierdź, że to samo konto jest również używane jako konto logowania w obszarze **Ustawienia**  >  **konta**  >  **Twoich informacji**. 
1. Sprawdź, czy synchronizacja działa na wielu komputerach, wprowadzając pewne zmiany na oryginalnej maszynie, na przykład przenosząc pasek zadań do prawej lub górnej krawędzi ekranu. Obejrzyj zmianę Propaguj do drugiej maszyny w ciągu pięciu minut. 

   * Blokowanie i odblokowywanie ekranu (win + L) może pomóc wyzwolić synchronizację.
   * Musisz zalogować się przy użyciu tego samego konta na obu komputerach, aby przeprowadzić synchronizację do pracy — jako że Enterprise State Roaming jest powiązany z kontem użytkownika, a nie z kontem komputera.

**Potencjalny problem**: Jeśli kontrolki na stronie **Ustawienia** nie są dostępne i zobaczysz komunikat "niektóre funkcje systemu Windows są dostępne tylko wtedy, gdy używasz konta służbowego konto Microsoft lub". Ten problem może wystąpić w przypadku urządzeń, które są skonfigurowane do przyłączenia do domeny i zarejestrowania w usłudze Azure AD, ale urządzenie nie zostało jeszcze pomyślnie uwierzytelnione do usługi Azure AD. Możliwą przyczyną jest to, że należy zastosować zasady dotyczące urządzeń, ale ta aplikacja jest wykonywana asynchronicznie i może być opóźniona o kilka godzin. 

### <a name="verify-the-device-registration-status"></a>Weryfikowanie stanu rejestracji urządzenia

Enterprise State Roaming wymaga zarejestrowania urządzenia w usłudze Azure AD. Chociaż nie są one specyficzne dla Enterprise State Roaming, zgodnie z poniższymi instrukcjami mogą pomóc w upewnieniu się, że klient systemu Windows 10 jest zarejestrowany, i potwierdzić odcisk palca, adres URL ustawień usługi Azure AD, stan NGC i inne informacje.

1. Otwórz wiersz polecenia bez podwyższonego poziomu uprawnień. Aby to zrobić w systemie Windows, Otwórz uruchamianie uruchamiania (Win + R) i wpisz "cmd", aby otworzyć.
1. Po otwarciu wiersza polecenia wpisz "*dsregcmd.exe/status*".
1. W przypadku oczekiwanych danych wyjściowych wartość pola **AzureAdJoined** powinna być równa "yes", wartość pola **WamDefaultSet** powinna być równa "yes", a wartość pola **WamDefaultGUID** powinna być identyfikatorem GUID z "(AzureAd)" na końcu.

**Potencjalny problem**: **WamDefaultSet** i **AzureAdJoined** mają w wartości pola wartość "No", urządzenie zostało przyłączone do domeny i zarejestrowane w usłudze Azure AD, a urządzenie nie jest zsynchronizowane. Jeśli ta opcja jest wyświetlana, może być konieczne zaczekanie, aż zasady zostaną zastosowane lub uwierzytelnianie urządzenia nie powiodło się podczas nawiązywania połączenia z usługą Azure AD. Użytkownik może zaczekać kilka godzin, aby zastosować zasady. Inne kroki rozwiązywania problemów mogą obejmować ponowną próbę autorejestracji przez wylogowanie się i ponowne zarejestrowanie lub uruchomienie zadania w Harmonogram zadań. W niektórych przypadkach uruchomienie "*dsregcmd.exe/Leave*" w oknie wiersza polecenia z podwyższonym poziomem uprawnień, ponowne uruchomienie i próba rejestracji może pomóc w rozwiązaniu tego problemu.

**Potencjalny problem**: pole dla elementu **SettingsUrl** jest puste, a urządzenie nie jest zsynchronizowane. Użytkownik mógł zostać ostatnio zalogowany na urządzeniu przed włączeniem Enterprise State Roaming w portalu Azure Active Directory. Uruchom ponownie urządzenie i zaloguj się. Opcjonalnie w portalu Wypróbuj administratora IT, który przejdzie do **Azure Active Directory**  >  **urządzeń**,  >  **Enterprise State roaming** wyłączyć i ponownie włączyć **użytkowników może synchronizować ustawienia i dane aplikacji między urządzeniami**. Po ponownym włączeniu ponownie uruchom urządzenie i zaloguj się. Jeśli to nie rozwiąże problemu, **SettingsUrl** może być puste, jeśli istnieje nieprawidłowy certyfikat urządzenia. W takim przypadku należy uruchomić polecenie "*dsregcmd.exe/Leave*" w oknie wiersza polecenia z podwyższonym poziomem uprawnień, ponownie uruchomić i ponowić próbę rejestracji.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming i Multi-Factor Authentication 

W pewnych warunkach Enterprise State Roaming nie mogą synchronizować danych w przypadku skonfigurowania usługi Azure Multi-Factor Authentication. Aby uzyskać więcej informacji na temat tych objawów, zobacz dokument pomocy technicznej [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potencjalny problem**: Jeśli na urządzeniu skonfigurowano wymaganie Multi-Factor Authentication w portalu Azure Active Directory, nie można zsynchronizować ustawień podczas logowania się do urządzenia z systemem Windows 10 przy użyciu hasła. Ten typ konfiguracji Multi-Factor Authentication jest przeznaczony do ochrony konta administratora platformy Azure. Użytkownicy administracyjni mogą nadal synchronizować się, logując się na urządzeniach z systemem Windows 10 przy użyciu numeru PIN Microsoft Passport for Work lub wykonując Multi-Factor Authentication podczas uzyskiwania dostępu do innych usług platformy Azure, takich jak Microsoft 365.

**Potencjalny problem**: Synchronizacja może zakończyć się niepowodzeniem, jeśli administrator skonfiguruje Active Directory Federation Services Multi-Factor Authentication zasady dostępu warunkowego, a token dostępu w urządzeniu wygaśnie. Upewnij się, że logujesz się i wylogujesz się przy użyciu numeru PIN Microsoft Passport for Work lub Ukończ Multi-Factor Authentication podczas uzyskiwania dostępu do innych usług platformy Azure, takich jak Microsoft 365.

### <a name="event-viewer"></a>Podgląd zdarzeń

Aby uzyskać zaawansowane Rozwiązywanie problemów, Podgląd zdarzeń może służyć do znajdowania określonych błędów. Opisano je w poniższej tabeli. Zdarzenia można znaleźć w obszarze Podgląd zdarzeń, > **Dzienniki aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **SettingSync-Azure** oraz w przypadku problemów związanych z tożsamościami w **dziennikach aplikacji i usług usługi**  >  **Microsoft**  >  **Windows**  >  **AAD**.

## <a name="known-issues"></a>Znane problemy

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronizacja nie działa na urządzeniach z załadowanymi bezpośrednio aplikacjami przy użyciu oprogramowania MDM

Dotyczy urządzeń z aktualizacją systemu Windows 10 (wersja 1607). W Podgląd zdarzeń poniżej dzienników SettingSync-Azure często pojawia się zdarzenie o IDENTYFIKATORze 6013 z błędem 80070259.

**Zalecana akcja**  
Upewnij się, że klient v1607 systemu Windows 10 ma 23 sierpnia 2016 aktualizację zbiorczą ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393,82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Ulubione programu Internet Explorer nie są synchronizowane

Dotyczy urządzeń z aktualizacją systemu Windows 10 listopad (wersja 1511).

**Zalecana akcja**  
Upewnij się, że klient systemu Windows 10 v1511 ma aktualizację zbiorczą 2016 lipca ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586,494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Nie można zsynchronizować motywu, a także danych chronionych za pomocą systemu Windows Information Protection 

Aby zapobiec wyciekom danych, dane chronione za pomocą [systemu windows Information Protection](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) nie będą synchronizowane za pośrednictwem Enterprise State roaming dla urządzeń korzystających z rocznicowej aktualizacji systemu Windows 10.

**Zalecana akcja**  
Brak. Przyszłe aktualizacje systemu Windows mogą rozwiązać ten problem.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Ustawienia daty, godziny i regionu nie są synchronizowane na urządzeniu przyłączonym do domeny 
  
Urządzenia, które są przyłączone do domeny, nie będą się synchronizować w celu ustawienia daty, godziny i regionu: czas automatyczny. Użycie czasu automatycznego może zastąpić inne ustawienia daty, godziny i regionu i spowodować, że te ustawienia nie są zsynchronizowane. 

**Zalecana akcja**  
Brak. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Pytanie kontroli konta użytkownika podczas synchronizowania haseł

Dotyczy urządzeń z aktualizacją systemu Windows 10 listopad (wersja 1511) z bezprzewodową kartą sieciową skonfigurowaną do synchronizacji haseł.

**Zalecana akcja**  
Upewnij się, że klient v1511 systemu Windows 10 ma aktualizację zbiorczą ([KB3140743](https://support.microsoft.com/kb/3140743) system operacyjny 10586,494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronizacja nie działa na urządzeniach korzystających z karty inteligentnej do logowania

W przypadku próby zalogowania się do urządzenia z systemem Windows za pomocą karty inteligentnej lub wirtualnej karty inteligentnej Synchronizacja ustawień przestanie działać.     

**Zalecana akcja**  
Brak. Przyszłe aktualizacje systemu Windows mogą rozwiązać ten problem.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Urządzenie przyłączone do domeny nie jest synchronizowane po opuszczeniu sieci firmowej     

Urządzenia przyłączone do domeny zarejestrowane w usłudze Azure AD mogą wystąpić błędy synchronizacji, jeśli urządzenie jest wyłączone poza lokacją przez dłuższy czas i nie można ukończyć uwierzytelniania domeny.

**Zalecana akcja**  
Połącz urządzenie z siecią firmową, aby można było wznowić synchronizację.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Urządzenie dołączone do usługi Azure AD nie jest zsynchronizowane, a użytkownik ma nazwę podmiotu zabezpieczeń użytkownika o mieszanym przypadku.

Jeśli użytkownik ma mieszane nazwy UPN przypadku (na przykład UserName zamiast username), a użytkownik znajduje się na urządzeniu dołączonym do usługi Azure AD, które zostało uaktualnione z systemu Windows 10 Build 10586 do 14393, nie można zsynchronizować urządzenia użytkownika. 

**Zalecana akcja**  
Użytkownik będzie musiał odłączyć urządzenie i ponownie dołączyć je do chmury. Aby to zrobić, zaloguj się jako administrator lokalny i Odłączaj urządzenie, przechodząc do **ustawień**  >  **system**  >  **About** i wybierz pozycję "Zarządzaj lub odłącz od pracy lub szkoły". Wyczyść poniższe pliki, a następnie ponownie Dołącz urządzenie do usługi Azure AD w obszarze **Ustawienia**  >  **system**  >  **About** i wybierz pozycję "Połącz z siecią firmową lub szkołą". Kontynuuj dołączanie urządzenia do Azure Active Directory i dokończ przepływ.

W kroku oczyszczania Oczyść następujące pliki:
- Settings. dat w `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Wszystkie pliki w folderze `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Identyfikator zdarzenia 6065:80070533 ten użytkownik nie może się zalogować, ponieważ to konto jest obecnie wyłączone  

W Podgląd zdarzeń w dziennikach SettingSync/Debug ten błąd może wystąpić, gdy poświadczenia użytkownika wygasły. Ponadto może wystąpić, gdy dzierżawa nie ma automatycznie AzureRMS aprowizacji. 

**Zalecana akcja**  
W pierwszym przypadku użytkownik powinien zaktualizować swoje poświadczenia i zalogować się do urządzenia przy użyciu nowych poświadczeń. Aby rozwiązać problem z AzureRMS, wykonaj kroki opisane w sekcji [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Identyfikator zdarzenia 1098: błąd: operacja brokera tokenu 0xCAA5001C nie powiodła się  

W Podgląd zdarzeń w ramach dzienników usługi AAD/operacyjnych ten błąd może się pojawić w przypadku zdarzenia 1104: usługa AAD Cloud AP wywołania wtyczki Get Error zwrócił błąd: 0xC000005F. Ten problem występuje, jeśli istnieją brakujące uprawnienia lub atrybuty własności.  

**Zalecana akcja**  
Wykonaj kroki opisane w [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem, zobacz [Omówienie usługi Enterprise State roaming](enterprise-state-roaming-overview.md).