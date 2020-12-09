---
title: Implementowanie synchronizacji skrótów haseł z synchronizacją Azure AD Connect | Microsoft Docs
description: Zawiera informacje o sposobie działania synchronizacji skrótów haseł i konfiguracji.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47d7d541ed7d9805641ffdfde381d482c8700006
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858743"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementowanie synchronizacji skrótów haseł za pomocą usługi synchronizacji programu Azure AD Connect
Ten artykuł zawiera informacje potrzebne do synchronizacji haseł użytkowników z wystąpienia lokalnego Active Directory do wystąpienia Azure Active Directory opartego na chmurze (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Jak działa synchronizacja skrótów haseł
Usługa domeny Active Directory przechowuje hasła w postaci reprezentacji wartości skrótu rzeczywiste hasło użytkownika. Wartość skrótu jest wynikiem jednokierunkowej funkcji matematycznej ( *algorytmem wyznaczania wartości skrótu*). Nie ma możliwości przywrócenia wyniku tej jednokierunkowej funkcji z powrotem do hasła w postaci zwykłego tekstu. 

Aby zsynchronizować hasło, Azure AD Connect Sync wyodrębnia skrót hasła z lokalnego wystąpienia Active Directory. Dodatkowe przetwarzanie zabezpieczeń jest stosowane do skrótu hasła przed jego zsynchronizowaniem z usługą uwierzytelniania Azure Active Directory. Hasła są synchronizowane osobno dla poszczególnych użytkowników i w kolejności chronologicznej.

Rzeczywisty przepływ danych procesu synchronizacji skrótów haseł jest podobny do synchronizacji danych użytkownika. Hasła są jednak synchronizowane częściej niż okno synchronizacja katalogu standardowego dla innych atrybutów. Proces synchronizacji skrótów haseł jest uruchamiany co 2 minuty. Nie można zmodyfikować częstotliwości tego procesu. Synchronizacja hasła powoduje zastąpienie istniejącego hasła w chmurze.

Po pierwszym włączeniu funkcji synchronizacji skrótów haseł wykonywana jest początkowa synchronizacja haseł wszystkich użytkowników należących do zakresu. Nie można jawnie zdefiniować podzbioru haseł użytkowników, które mają zostać zsynchronizowane. Jeśli jednak istnieje wiele łączników, można wyłączyć synchronizację skrótów haseł dla niektórych łączników, ale nie inne za pomocą polecenia cmdlet [Set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) .

Po zmianie hasła lokalnego zaktualizowane hasło jest synchronizowane najczęściej, w ciągu kilku minut.
Funkcja synchronizacji skrótów haseł automatycznie ponawia próbę synchronizacji nie powiodła się. Jeśli wystąpi błąd podczas próby zsynchronizowania hasła, w Podglądzie zdarzeń zostanie zarejestrowany błąd.

Synchronizacja hasła nie ma wpływu na użytkownika, który jest aktualnie zalogowany.
Bieżąca sesja usługi w chmurze nie ma natychmiastowego wpływania na zsynchronizowaną zmianę hasła, która występuje, podczas gdy użytkownik jest zalogowany, do usługi w chmurze. Jeśli jednak usługa w chmurze wymaga ponownego uwierzytelnienia, należy podać nowe hasło.

Użytkownik musi po raz drugi wprowadzić poświadczenia firmowe w celu uwierzytelnienia w usłudze Azure AD, niezależnie od tego, czy są zalogowani do sieci firmowej. Ten wzorzec można jednak zminimalizować, jeśli użytkownik wybierze pole wyboru nie wylogowuj mnie (KMSI) przy logowaniu. Ten wybór ustawia plik cookie sesji, który pomija uwierzytelnianie przez 180 dni. Zachowanie KMSI może być włączane lub wyłączane przez administratora usługi Azure AD. Ponadto można zmniejszyć monity o hasło, włączając [bezproblemową rejestrację jednokrotną](how-to-connect-sso.md), która automatycznie podpisuje użytkowników w przypadku, gdy znajdują się one na urządzeniach firmowych podłączonych do sieci firmowej.

> [!NOTE]
> Synchronizacja haseł jest obsługiwana tylko dla użytkownika typu Object w Active Directory. Nie jest obsługiwany dla typu obiektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Szczegółowy opis sposobu działania synchronizacji skrótów haseł

W poniższej sekcji opisano szczegółowo, jak działa synchronizacja skrótów haseł między Active Directory i usługą Azure AD.

![Szczegółowy przepływ hasła](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Co dwie minuty Agent synchronizacji skrótów haseł na serwerze programu AD Connect żąda zapisanych skrótów haseł (atrybut unicodePwd) z kontrolera domeny.  To żądanie jest realizowane za pośrednictwem standardowego protokołu replikacji [MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) służącego do synchronizowania danych między kontrolerami domeny. Konto usługi musi mieć zmiany w katalogu replikacji i replikowanie katalogu zmienia wszystkie uprawnienia usługi AD (przyznane domyślnie podczas instalacji), aby uzyskać skróty haseł.
2. Przed wysłaniem kontroler domeny szyfruje skrót hasła algorytmu MD4 przy użyciu klucza, który jest skrótem [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) klucza sesji RPC i soli. Następnie wysyła wynik do agenta synchronizacji skrótów haseł za pośrednictwem usługi RPC. Kontroler domeny przekazuje także sól do agenta synchronizacji przy użyciu protokołu replikacji kontrolera domeny, dzięki czemu agent będzie mógł odszyfrować kopertę.
3. Gdy Agent synchronizacji skrótów haseł ma zaszyfrowaną kopertę, używa [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) i soli do wygenerowania klucza w celu odszyfrowania odebranych danych z powrotem do oryginalnego formatu MD4. Agent synchronizacji skrótów haseł nigdy nie ma dostępu do hasła w postaci zwykłego tekstu. Użycie algorytmu MD5 przez agenta synchronizacji skrótów haseł jest przeznaczone wyłącznie do zapewnienia zgodności protokołu z kontrolerem domeny i jest używane tylko lokalnie między kontrolerem domeny i agentem synchronizacji skrótów haseł.
4. Agent synchronizacji skrótów haseł rozszerza wartość 16-bajtowego skrótu hasła binarnego na 64 bajtów przez konwertowanie skrótu do 32-bajtowego ciągu szesnastkowego, a następnie przekonwertowanie tego ciągu z powrotem na dane binarne przy użyciu kodowania UTF-16.
5. Agent synchronizacji skrótów haseł dodaje sól dla użytkownika, składającą się z 10-bajtowej soli długości, do 64-bajtowego pliku binarnego w celu dalszej ochrony oryginalnego skrótu.
6. Agent synchronizacji skrótów haseł łączy skrót MD4 ze znakiem poszczególnych użytkowników i wprowadza je do funkcji [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iteracji algorytmem wyznaczania wartości skrótu [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256) . 
7. Agent synchronizacji skrótów haseł pobiera wynikowy 32-bajtowy, łączy zarówno sól dla użytkownika, jak i liczbę iteracji SHA256 (na użytek usługi Azure AD), a następnie przesyła ciąg z Azure AD Connect do usługi Azure AD za pośrednictwem protokołu TLS.</br> 
8. Gdy użytkownik próbuje zalogować się do usługi Azure AD i wprowadzi swoje hasło, jest ono uruchamiane za pomocą tego samego procesu MD4 + sól + PBKDF2 + HMAC-SHA256. Jeśli wynikowy skrót jest zgodny z wartością skrótu przechowywaną w usłudze Azure AD, użytkownik wprowadził poprawne hasło i zostanie uwierzytelniony.

> [!NOTE]
> Oryginalny skrót MD4 nie jest przesyłany do usługi Azure AD. Zamiast tego jest przesyłany Skrót SHA256 oryginalnego skrótu MD4. W związku z tym, jeśli zostanie uzyskany skrót przechowywany w usłudze Azure AD, nie można go używać w przypadku ataku typu "nastąpi lokalne".

### <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Podczas synchronizowania haseł wersja zwykłego tekstu hasła nie jest udostępniona funkcji synchronizacji skrótów haseł w usłudze Azure AD ani żadnej z skojarzonych usług.

Uwierzytelnianie użytkowników odbywa się w odniesieniu do usługi Azure AD, a nie w odniesieniu do własnego wystąpienia Active Directory organizacji. Dane hasła SHA256 przechowywane w usłudze Azure AD — skrót oryginalnego skrótu algorytmu MD4 — jest bezpieczniejszy niż to, co jest przechowywane w Active Directory. Ponadto, ponieważ nie można odszyfrować tego skrótu SHA256, nie może zostać przywrócony do środowiska Active Directory organizacji i przedstawiony jako prawidłowe hasło użytkownika w ataku typu Pass-the-hash.

### <a name="password-policy-considerations"></a>Zagadnienia dotyczące zasad haseł

Istnieją dwa typy zasad haseł, na które mają wpływ włączenie synchronizacji skrótów haseł:

* Zasady złożoności haseł
* Zasady wygasania haseł

#### <a name="password-complexity-policy"></a>Zasady złożoności haseł

Gdy synchronizacja skrótów haseł jest włączona, zasady złożoności haseł w wystąpieniu Active Directory lokalnego przesłaniają zasady złożoności w chmurze dla synchronizowanych użytkowników. Aby uzyskać dostęp do usług Azure AD, możesz użyć wszystkich prawidłowych haseł z lokalnego wystąpienia Active Directory.

> [!NOTE]
> Hasła użytkowników utworzonych bezpośrednio w chmurze są nadal objęte zasadami haseł zdefiniowanymi w chmurze.

#### <a name="password-expiration-policy"></a>Zasady wygasania haseł

Jeśli użytkownik należy do zakresu synchronizacji skrótów haseł, domyślnie hasło konta w chmurze jest ustawione na *nigdy nie wygasa*.

Możesz nadal logować się do usług w chmurze, używając zsynchronizowanego hasła, które wygasło w środowisku lokalnym. Hasło w chmurze zostanie zaktualizowane przy następnym zmianie hasła w środowisku lokalnym.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

W przypadku synchronizacji użytkowników, którzy współpracują z usługami zintegrowanymi z usługą Azure AD i muszą być zgodni z zasadami wygasania haseł, można wymusić ich zgodność z zasadami wygasania haseł usługi Azure AD, włączając funkcję *EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

Gdy *EnforceCloudPasswordPolicyForPasswordSyncedUsers* jest wyłączone (jest to ustawienie domyślne), Azure AD Connect ustawia atrybut PasswordPolicies synchronizowanych użytkowników na "DisablePasswordExpiration". Jest to wykonywane za każdym razem, gdy hasło użytkownika jest synchronizowane i powoduje, że usługa Azure AD zignoruje zasady wygaśnięcia hasła w chmurze dla tego użytkownika. Wartość atrybutu można sprawdzić przy użyciu modułu Azure AD PowerShell za pomocą następującego polecenia:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Aby włączyć funkcję EnforceCloudPasswordPolicyForPasswordSyncedUsers, uruchom następujące polecenie przy użyciu modułu MSOnline PowerShell, jak pokazano poniżej. Należy wpisać wartość tak dla parametru Enable, jak pokazano poniżej:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Po włączeniu usługa Azure AD nie przechodzi do każdego synchronizowanego użytkownika w celu usunięcia `DisablePasswordExpiration` wartości z atrybutu PasswordPolicies. Zamiast tego `DisablePasswordExpiration` wartość jest usuwana z PasswordPolicies podczas następnej synchronizacji skrótów haseł dla każdego użytkownika, przy następnej zmianie hasła w lokalnej usłudze AD.

Zaleca się włączenie EnforceCloudPasswordPolicyForPasswordSyncedUsers przed włączeniem synchronizacji skrótów haseł, dzięki czemu początkowa synchronizacja skrótów haseł nie dodaje `DisablePasswordExpiration` wartości do atrybutu PasswordPolicies dla użytkowników.

Domyślne zasady haseł usługi Azure AD wymagają od użytkowników zmiany haseł co 90 dni. Jeśli zasady usługi AD są również 90 dni, dwie zasady powinny być zgodne. Jeśli jednak zasady usługi AD nie są 90 dni, można zaktualizować zasady haseł usługi Azure AD w celu dopasowania przy użyciu polecenia Set-MsolPasswordPolicy PowerShell.

Usługa Azure AD obsługuje oddzielne zasady wygasania haseł dla zarejestrowanej domeny.

Zastrzeżenie: Jeśli istnieją zsynchronizowane konta, które muszą mieć niewygasające hasła w usłudze Azure AD, musisz jawnie dodać `DisablePasswordExpiration` wartość do atrybutu PasswordPolicies obiektu użytkownika w usłudze Azure AD.  Można to zrobić, uruchamiając następujące polecenie.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Polecenie Set-MsolPasswordPolicy PowerShell nie będzie działało w domenach federacyjnych. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Synchronizowanie haseł tymczasowych i "Wymuś zmianę hasła przy następnym logowaniu"

Typowo, aby wymusić zmianę hasła przez użytkownika podczas pierwszego logowania, szczególnie po wystąpieniu resetowania hasła administratora.  Jest on często znany jako ustawienie "tymczasowego" hasła i jest wykonywane przez sprawdzenie flagi "użytkownik musi zmienić hasło przy następnym logowaniu" w obiekcie użytkownika w Active Directory (AD).
  
Funkcja hasła tymczasowego pomaga zapewnić, że przeniesienie własności poświadczenia odbywa się przy pierwszym użyciu, aby zminimalizować czas, w którym więcej niż jedna osoba ma wiedzę na temat tego poświadczenia.

Aby obsłużyć tymczasowe hasła w usłudze Azure AD dla synchronizowanych użytkowników, można włączyć funkcję *ForcePasswordChangeOnLogOn* , uruchamiając następujące polecenie na serwerze Azure AD Connect:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Wymuszanie użytkownikowi zmiany hasła przy następnym logowaniu wymaga zmiany hasła w tym samym czasie.  Azure AD Connect nie spowoduje pobrania przez siebie flagi "Wymuszaj zmianę hasła". jest uzupełnieniem wykrytej zmiany hasła, która występuje podczas synchronizacji skrótów haseł.

> [!CAUTION]
> Tej funkcji należy używać tylko wtedy, gdy w dzierżawie są włączone SSPR i zapisywanie zwrotne haseł.  Jest tak dlatego, że jeśli użytkownik zmieni hasło za pośrednictwem SSPR, zostanie zsynchronizowany z Active Directory.

#### <a name="account-expiration"></a>Wygaśnięcie konta

Jeśli organizacja używa atrybutu accountExpires jako części zarządzania kontami użytkowników, ten atrybut nie jest synchronizowany z usługą Azure AD. W związku z tym wygasłe konto Active Directory w środowisku skonfigurowanym do synchronizacji skrótów haseł będzie nadal aktywne w usłudze Azure AD. Zalecamy, aby Jeśli konto wygasło, Akcja przepływu pracy powinna wyzwolić skrypt programu PowerShell, który wyłącza konto usługi Azure AD użytkownika (Użyj polecenia cmdlet [Set-AzureADUser](/powershell/module/azuread/set-azureaduser) ). Po włączeniu konta należy włączyć wystąpienie usługi Azure AD.

### <a name="overwrite-synchronized-passwords"></a>Zastąp zsynchronizowane hasła

Administrator może ręcznie zresetować hasło przy użyciu programu Windows PowerShell.

W takim przypadku nowe hasło zastępuje zsynchronizowane hasło, a wszystkie zasady haseł zdefiniowane w chmurze zostaną zastosowane do nowego hasła.

Jeśli zmienisz hasło lokalne ponownie, nowe hasło zostanie zsynchronizowane z chmurą i zastąpi je ręcznie zaktualizowane hasło.

Synchronizacja hasła nie ma wpływu na użytkownika platformy Azure, który jest zalogowany. Bieżąca sesja usługi w chmurze nie ma natychmiastowego wpływania na zsynchronizowaną zmianę hasła, która występuje, gdy użytkownik jest zalogowany do usługi w chmurze. KMSI wydłuża czas trwania tej różnicy. Gdy usługa w chmurze wymaga ponownego uwierzytelnienia, należy podać nowe hasło.

### <a name="additional-advantages"></a>Dodatkowe korzyści

- Zwykle synchronizacja skrótów haseł jest prostsza do zaimplementowania niż usługa federacyjna. Nie wymaga żadnych dodatkowych serwerów i eliminuje zależność od usługi federacyjnej o wysokiej dostępności do uwierzytelniania użytkowników.
- Oprócz Federacji można również włączyć synchronizację skrótów haseł. Mogą być używane jako rezerwowe, jeśli usługa federacyjna napotyka awarię.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Proces synchronizacji skrótów haseł dla Azure AD Domain Services

Jeśli używasz Azure AD Domain Services w celu zapewnienia starszego uwierzytelniania dla aplikacji i usług, które muszą korzystać z protokołu Kerberos, LDAP lub NTLM, niektóre dodatkowe procesy są częścią przepływu synchronizacji skrótów haseł. Azure AD Connect wykorzystuje dodatkowy następujący proces do synchronizowania skrótów haseł w usłudze Azure AD do użycia w programie Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Instalowanie Azure AD Connect w domenie zarządzanej AD DS platformy Azure nie jest obsługiwane do synchronizowania obiektów z powrotem do usługi Azure AD.
>
> Azure AD Connect synchronizuje tylko starsze skróty haseł po włączeniu AD DS platformy Azure dla dzierżawy usługi Azure AD. Poniższe kroki nie są używane, jeśli do synchronizowania środowiska lokalnego AD DS z usługą Azure AD jest używana tylko Azure AD Connect.
>
> Jeśli starsze aplikacje nie korzystają z uwierzytelniania NTLM lub prostych powiązań LDAP, zalecamy wyłączenie synchronizacji skrótów haseł NTLM dla AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [wyłączanie słabych mechanizmów szyfrowania i synchronizacji skrótów poświadczeń NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect Pobiera klucz publiczny dla wystąpienia Azure AD Domain Services dzierżawcy.
1. Po zmianie hasła przez użytkownika lokalny kontroler domeny przechowuje wynik zmiany hasła (skróty) w dwóch atrybutach:
    * wartość *unicodePwd* dla skrótu hasła NTLM.
    * *supplementalCredentials* dla skrótu hasła protokołu Kerberos.
1. Azure AD Connect wykrywa zmiany haseł za pośrednictwem kanału replikacji katalogu (zmiany atrybutów, które muszą być replikowane do innych kontrolerów domeny).
1. W przypadku każdego użytkownika, którego hasło zostało zmienione, Azure AD Connect wykonuje następujące czynności:
    * Generuje losowy klucz symetryczny AES 256-bitowy.
    * Generuje losowy wektor inicjujący, który jest wymagany podczas pierwszej rundy szyfrowania.
    * Wyodrębnia skróty haseł protokołu Kerberos z atrybutów *supplementalCredentials* .
    * Azure AD Domain Services sprawdza ustawienie *SyncNtlmPasswords* zabezpieczeń konfiguracji.
        * Jeśli to ustawienie jest wyłączone, generuje losowy, wieloznaczny skrót protokołu NTLM (różny od hasła użytkownika). Ten skrót jest następnie połączony z dokładnymi skrótami hasła protokołu Kerberos z atrybutu *supplementalCrendetials* do jednej struktury danych.
        * Jeśli ta funkcja jest włączona, łączy wartość atrybutu *unicodePwd* z wyodrębnionymi skrótami hasła protokołu Kerberos z atrybutu *supplementalCredentials* do jednej struktury danych.
    * Szyfruje pojedynczą strukturę danych przy użyciu klucza symetrycznego AES.
    * Szyfruje klucz symetryczny AES przy użyciu klucza publicznego Azure AD Domain Services dzierżawy.
1. Azure AD Connect przesyła szyfrowany klucz symetryczny AES, strukturę zaszyfrowanych danych zawierających skróty haseł oraz wektor inicjujący do usługi Azure AD.
1. Usługa Azure AD przechowuje zaszyfrowany klucz symetryczny AES, strukturę zaszyfrowanych danych i wektor inicjalizacji dla użytkownika.
1. Usługa Azure AD wypycha zaszyfrowany klucz symetryczny AES, strukturę zaszyfrowanych danych i wektor inicjalizacji przy użyciu wewnętrznego mechanizmu synchronizacji przez zaszyfrowaną sesję HTTP do Azure AD Domain Services.
1. Azure AD Domain Services Pobiera klucz prywatny dla wystąpienia dzierżawcy z magazynu kluczy platformy Azure.
1. W przypadku każdego zaszyfrowanego zestawu danych (reprezentującego zmianę hasła pojedynczego użytkownika) Azure AD Domain Services następnie wykonuje następujące czynności:
    * Używa swojego klucza prywatnego do odszyfrowania klucza symetrycznego AES.
    * Używa klucza symetrycznego AES z wektorem inicjalizacji w celu odszyfrowania struktury zaszyfrowanych danych, która zawiera skróty haseł.
    * Zapisuje wartości skrótów haseł protokołu Kerberos, które otrzymuje do kontrolera domeny Azure AD Domain Services. Skróty są zapisywane w atrybucie *supplementalCredentials* obiektu użytkownika, który jest szyfrowany do klucza publicznego Azure AD Domain Services kontrolera domeny.
    * Azure AD Domain Services zapisuje skrót hasła NTLM otrzymany na Azure AD Domain Services kontrolerze domeny. Skrót jest zapisywany w atrybucie *unicodePwd* obiektu użytkownika, który jest szyfrowany do klucza publicznego Azure AD Domain Services kontrolera domeny.

## <a name="enable-password-hash-synchronization"></a>Włączanie synchronizacji skrótów haseł

>[!IMPORTANT]
>W przypadku migrowania z AD FS (lub innych technologii federacyjnych) do synchronizacji skrótów haseł zdecydowanie zalecamy zapoznanie się z naszym szczegółowym przewodnikiem wdrażania opublikowanym [tutaj](https://aka.ms/adfstophsdpdownload).

W przypadku instalowania Azure AD Connect przy użyciu opcji **Ustawienia ekspresowe** synchronizacja skrótów haseł jest włączana automatycznie. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD Connect przy użyciu ustawień ekspresowych](how-to-connect-install-express.md).

W przypadku korzystania z ustawień niestandardowych podczas instalowania Azure AD Connect na stronie logowania użytkownika jest dostępna synchronizacja skrótów haseł. Aby uzyskać więcej informacji, zobacz [Instalacja Niestandardowa Azure AD Connect](how-to-connect-install-custom.md).

![Włączanie synchronizacji skrótów haseł](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizacja skrótów haseł i FIPS
Jeśli serwer został zablokowany zgodnie z normą FIPS (Federal Information Processing Standard), wówczas MD5 jest wyłączony.

**Aby włączyć MD5 dla synchronizacji skrótów haseł, wykonaj następujące czynności:**

1. Przejdź do%programfiles%\Azure usługi AD Sync\Bin.
2. Otwórz miiserver.exe.config.
3. Przejdź do węzła Konfiguracja/środowisko uruchomieniowe na końcu pliku.
4. Dodaj następujący węzeł: `<enforceFIPSPolicy enabled="false"/>`
5. Zapisz zmiany.

Aby uzyskać odwołanie, ten fragment kodu powinien wyglądać następująco:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Aby uzyskać informacje o zabezpieczeniach i standardzie FIPS, zobacz [synchronizowanie skrótów haseł, szyfrowanie i zgodność ze standardem FIPS usługi Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł
Jeśli masz problemy z synchronizacją skrótów haseł, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Następne kroki
* [Synchronizacja Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Zapoznaj się z planem wdrożenia krok po kroku na potrzeby migracji z usług AD FS do synchronizacji skrótów haseł](https://aka.ms/authenticationDeploymentPlan)
