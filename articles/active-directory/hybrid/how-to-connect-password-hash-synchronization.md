---
title: Implementowanie synchronizacji skrótów haseł za pomocą Azure AD Connect synchronizacji | Microsoft Docs
description: Zawiera informacje na temat sposobu działania synchronizacji skrótów haseł i sposobu ich skonfigurowania.
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
ms.openlocfilehash: ee22ba3816e667bc58247fa81142e54587124fd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865301"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementowanie synchronizacji skrótów haseł za pomocą usługi synchronizacji programu Azure AD Connect
Ten artykuł zawiera informacje potrzebne do synchronizowania haseł użytkowników z wystąpienia usługi lokalna usługa Active Directory do wystąpienia usługi Azure Active Directory (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Jak działa synchronizacja skrótów haseł
Usługa domenowa Active Directory przechowuje hasła w postaci reprezentacji wartości skrótu rzeczywistego hasła użytkownika. Wartość skrótu jest wynikiem jednokierunkowej funkcji matematycznej *(algorytmu wyznaczania wartości skrótu).* Nie ma możliwości przywrócenia wyniku tej jednokierunkowej funkcji z powrotem do hasła w postaci zwykłego tekstu. 

Aby zsynchronizować hasło, Azure AD Connect wyodrębnia skrót hasła z lokalna usługa Active Directory wystąpienia. Dodatkowe przetwarzanie zabezpieczeń jest stosowane do skrótu hasła przed jego zsynchronizowaniem z usługą Azure Active Directory uwierzytelniania. Hasła są synchronizowane dla 1 użytkownika i w kolejności chronologicznej.

Rzeczywisty przepływ danych procesu synchronizacji skrótów haseł jest podobny do synchronizacji danych użytkownika. Hasła są jednak synchronizowane częściej niż standardowe okno synchronizacji katalogów dla innych atrybutów. Proces synchronizacji skrótów haseł jest uruchamiany co 2 minuty. Nie można zmodyfikować częstotliwości tego procesu. Synchronizacja hasła powoduje zastąpienie istniejącego hasła w chmurze.

Przy pierwszym włączeniu funkcji synchronizacji skrótów haseł wykonuje początkową synchronizację haseł wszystkich użytkowników w zakresie. Nie można jawnie zdefiniować podzestawu haseł użytkowników, które mają być synchronizowane. Jeśli jednak istnieje wiele łączników, można wyłączyć synchronizację skrótów haseł dla niektórych łączników, ale nie dla innych za pomocą polecenia cmdlet [Set-ADSyncAADPasswordSyncConfiguration.](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md)

Po zmianie hasła lokalnego zaktualizowane hasło jest synchronizowane, najczęściej w ciągu kilku minut.
Funkcja synchronizacji skrótów haseł automatycznie ponownych prób synchronizacji nie powiodła się. Jeśli podczas próby synchronizacji hasła wystąpi błąd, w podglądzie zdarzeń zostanie zarejestrowany błąd.

Synchronizacja hasła nie ma wpływu na aktualnie zalogowaną użytkownika.
Na bieżącą sesję usługi w chmurze nie ma natychmiast wpływu zsynchronizowana zmiana hasła, która występuje podczas logowania do usługi w chmurze. Jeśli jednak usługa w chmurze wymaga ponownego uwierzytelnienia, należy podać nowe hasło.

Użytkownik musi wprowadzić poświadczenia firmowe po raz drugi, aby uwierzytelnić się w usłudze Azure AD, niezależnie od tego, czy jest zalogowany w sieci firmowej. Ten wzorzec można zminimalizować, jednak jeśli użytkownik zaznaczy pole wyboru Nie wyloguj mnie (KMSI) podczas logowania. Ten wybór ustawia plik cookie sesji, który pomija uwierzytelnianie przez 180 dni. Zachowanie kmsi może zostać włączone lub wyłączone przez administratora usługi Azure AD. Ponadto można zmniejszyć liczbę monitów o hasło, włączając bezproblemowe logowanie jednokrotne [,](how-to-connect-sso.md)które automatycznie logowania użytkowników, gdy znajdują się na ich urządzeniach firmowych podłączonych do sieci firmowej.

> [!NOTE]
> Synchronizacja haseł jest obsługiwana tylko dla użytkownika typu obiektu w usłudze Active Directory. Nie jest obsługiwana dla typu obiektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Szczegółowy opis działania synchronizacji skrótów haseł

W poniższej sekcji szczegółowo opisano, jak działa synchronizacja skrótów haseł między usługami Active Directory i Azure AD.

![Szczegółowy przepływ haseł](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Co dwie minuty agent synchronizacji skrótów haseł na serwerze programu AD Connect żąda przechowywanych skrótów haseł (atrybutu unicodePwd) z kontrolera domeny.  To żądanie jest za pośrednictwem [standardowego protokołu replikacji MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) używanego do synchronizowania danych między komputerami domeny. Konto usługi musi mieć uprawnienia Replikuj zmiany katalogu i Replikuj zmiany katalogu Wszystkie usługi AD (domyślnie udzielane podczas instalacji), aby uzyskać skróty haseł.
2. Przed wysłaniem kontroler domeny szyfruje skrót hasła MD4 przy użyciu klucza, który jest skrótem [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) klucza sesji RPC i insygniami. Następnie wysyła wynik do agenta synchronizacji skrótów haseł za pośrednictwem RPC. Kontroler domeny przekazuje również do agenta synchronizacji przy użyciu protokołu replikacji kontrolera domeny, dzięki czemu agent będzie mógł odszyfrować kopertę.
3. Gdy agent synchronizacji skrótów haseł ma zaszyfrowaną kopertę, używa obiektu [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) i sosu do wygenerowania klucza w celu odszyfrowania odebranych danych do oryginalnego formatu MD4. Agent synchronizacji skrótów haseł nigdy nie ma dostępu do hasła w tekście. Agent synchronizacji skrótów haseł korzysta z rozwiązania MD5 wyłącznie w celu zapewnienia zgodności protokołu replikacji z kontrolerem domeny i jest używany tylko lokalnie między kontrolerem domeny a agentem synchronizacji skrótów haseł.
4. Agent synchronizacji skrótów haseł rozszerza 16-bajtowy binarny skrót hasła do 64 bajtów, najpierw konwertując skrót na 32-bajtowy ciąg szesnastkowym, a następnie konwertując ten ciąg z powrotem na plik binarny z kodowaniem UTF-16.
5. Agent synchronizacji skrótów haseł dodaje do 64-bajtowego pliku binarnego wartość ciągu inso 10-bajtowego składającą się z ciągu 10-bajtowego, aby dodatkowo chronić oryginalny skrót.
6. Następnie agent synchronizacji skrótów haseł łączy skrót MD4 z wartością insygnia na użytkownika i dodaje go do [funkcji PBKDF2.](https://www.ietf.org/rfc/rfc2898.txt) Używane jest 1000 iteracji algorytmu wyznaczania wartości skrótu [HMAC-SHA256.](/dotnet/api/system.security.cryptography.hmacsha256) 
7. Agent synchronizacji skrótów haseł pobiera wynikowy skrót 32-bajtowy, łączy w sobie zarówno ilość iteracji SHA256 na użytkownika, jak i liczbę iteracji SHA256 (do użycia przez usługę Azure AD), a następnie przesyła ciąg z usługi Azure AD Connect do usługi Azure AD za pośrednictwem TLS.</br> 
8. Gdy użytkownik próbuje zalogować się do usługi Azure AD i wprowadzi hasło, hasło zostanie uruchomione w tym samym procesie MD4+salt+PBKDF2+HMAC-SHA256. Jeśli wynikowy skrót jest odpowiedni dla skrótu przechowywanego w usłudze Azure AD, użytkownik wprowadził poprawne hasło i został uwierzytelniony.

> [!NOTE]
> Oryginalny skrót MD4 nie jest przesyłany do usługi Azure AD. Zamiast tego jest przesyłany skrót SHA256 oryginalnego skrótu MD4. W związku z tym, jeśli zostanie uzyskany skrót przechowywany w usłudze Azure AD, nie można go użyć w przypadku lokalnego ataku typu pass-the-hash.

### <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Podczas synchronizowania haseł wersja hasła w postaci zwykłego tekstu nie jest dostępna w funkcji synchronizacji skrótów haseł w usłudze Azure AD ani w żadnej ze skojarzonych usług.

Uwierzytelnianie użytkowników odbywa się względem usługi Azure AD, a nie względem własnego wystąpienia usługi Active Directory organizacji. Dane hasła SHA256 przechowywane w usłudze Azure AD — skrót oryginalnego skrótu MD4 — są bezpieczniejsze niż dane przechowywane w usłudze Active Directory. Ponadto, ponieważ nie można odszyfrować tego skrótu SHA256, nie można go odzyskać do środowiska usługi Active Directory organizacji i zaprezentować jako prawidłowe hasło użytkownika w ataku typu pass-the-hash.

### <a name="password-policy-considerations"></a>Zagadnienia dotyczące zasad haseł

Włączenie synchronizacji skrótów haseł ma wpływ na dwa typy zasad haseł:

* Zasady złożoności hasła
* Zasady wygasania haseł

#### <a name="password-complexity-policy"></a>Zasady złożoności hasła

Po włączeniu synchronizacji skrótów haseł zasady złożoności hasła w wystąpieniu lokalna usługa Active Directory zastępują zasady złożoności w chmurze dla zsynchronizowanych użytkowników. Aby uzyskać dostęp do usług Azure AD, możesz użyć wszystkich prawidłowych haseł lokalna usługa Active Directory wystąpienia usługi Azure AD.

> [!NOTE]
> Hasła użytkowników, którzy są tworzone bezpośrednio w chmurze, nadal podlegają zasadom haseł zdefiniowanym w chmurze.

#### <a name="password-expiration-policy"></a>Zasady wygasania haseł

Jeśli użytkownik znajduje się w zakresie synchronizacji skrótów haseł, domyślnie hasło konta w chmurze ma wartość *Nigdy nie wygasa.*

Możesz nadal logować się do usług w chmurze przy użyciu zsynchronizowanego hasła, które wygasło w środowisku lokalnym. Hasło do chmury zostanie zaktualizowane przy następnej zmianie hasła w środowisku lokalnym.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Jeśli istnieją zsynchronizowani użytkownicy, którzy współdziałają tylko ze zintegrowanymi usługami Azure AD i muszą również spełniać zasady wygasania haseł, można wymusić ich zgodność z zasadami wygasania haseł usługi Azure AD, włączając funkcję *EnforceCloudPasswordPolicyForPasswordSyncedUsers.*

Gdy *ustawienie EnforceCloudPasswordPolicyForPasswordSyncedUsers* jest wyłączone (co jest ustawieniem domyślnym), program Azure AD Connect ustawia atrybut PasswordPolicies synchronizowanych użytkowników na wartość "DisablePasswordExpiration". Jest to wykonywane za każdym razem, gdy hasło użytkownika jest synchronizowane i powoduje, że usługa Azure AD ignoruje zasady wygasania haseł w chmurze dla tego użytkownika. Możesz sprawdzić wartość atrybutu przy użyciu modułu Azure AD PowerShell za pomocą następującego polecenia:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Aby włączyć funkcję EnforceCloudPasswordPolicyForPasswordSyncedUsers, uruchom następujące polecenie przy użyciu modułu MSOnline programu PowerShell, jak pokazano poniżej. Dla parametru Włącz należy wpisać tak, jak pokazano poniżej:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Po włączeniu usługa Azure AD nie będzie do każdego zsynchronizowanego użytkownika usuwać wartości `DisablePasswordExpiration` z atrybutu PasswordPolicies. Zamiast tego wartość jest usuwana z obiektu PasswordPolicies podczas następnej synchronizacji skrótów haseł dla każdego użytkownika po następnej zmianie hasła w lokalnej `DisablePasswordExpiration` u usługi AD.

Zaleca się włączenie funkcji EnforceCloudPasswordPolicyForPasswordSyncedUsers przed włączeniem synchronizacji skrótów haseł, dzięki czemu początkowa synchronizacja skrótów haseł nie doda wartości do atrybutu `DisablePasswordExpiration` PasswordPolicies dla użytkowników.

Domyślne zasady haseł usługi Azure AD wymagają, aby użytkownicy zmieniali swoje hasła co 90 dni. Jeśli twoje zasady w u usługach AD również mają 90 dni, te dwie zasady powinny być zgodne. Jeśli jednak zasady usługi AD nie mają 90 dni, możesz zaktualizować zasady haseł usługi Azure AD, aby były zgodne, używając polecenia Set-MsolPasswordPolicy PowerShell.

Usługa Azure AD obsługuje oddzielne zasady wygasania haseł dla zarejestrowanej domeny.

Zastrzeżenia: Jeśli istnieją zsynchronizowane konta, które muszą mieć nie wygasające hasła w usłudze Azure AD, musisz jawnie dodać wartość do atrybutu PasswordPolicies obiektu użytkownika w usłudze `DisablePasswordExpiration` Azure AD.  Możesz to zrobić, uruchamiając następujące polecenie.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Polecenie Set-MsolPasswordPolicy programu PowerShell nie będzie działać w domenach federowanych. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Synchronizowanie haseł tymczasowych i "Wymusz zmianę hasła przy następnym logiu"

Typowe jest wymuszanie zmiany hasła przez użytkownika podczas pierwszego logowania, szczególnie po zresetowaniu hasła administratora.  Często jest to nazywane ustawieniem hasła "tymczasowego" i jest ono ukończone przez zaznaczenie flagi "Użytkownik musi zmienić hasło przy następnym logniu" w obiekcie użytkownika w usłudze Active Directory (AD).
  
Funkcja hasła tymczasowego pomaga zagwarantować, że przenoszenie własności poświadczeń zostanie ukończone przy pierwszym użyciu, aby zminimalizować czas, w którym więcej niż jedna osoba ma wiedzę o tym poświadczeniu.

Aby obsługiwać hasła tymczasowe w usłudze Azure AD dla zsynchronizowanych użytkowników, możesz włączyć funkcję *ForcePasswordChangeOnLogOn,* uruchamiając następujące polecenie na Azure AD Connect serwera:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Wymuś zmianę hasła użytkownika podczas następnego logowania wymaga zmiany hasła w tym samym czasie.  Azure AD Connect sama nie będzie samodzielnie bierze flagi wymuszania zmiany hasła; jest uzupełnieniem wykrytej zmiany hasła, która występuje podczas synchronizacji skrótów haseł.

> [!CAUTION]
> Tej funkcji należy używać tylko wtedy, gdy w dzierżawie włączono funkcję SSPR i funkcję zapisu zwrotnego haseł.  To dlatego, jeśli użytkownik zmieni swoje hasło za pośrednictwem SSPR, zostanie zsynchronizowane z usługą Active Directory.

#### <a name="account-expiration"></a>Wygaśnięcie konta

Jeśli Twoja organizacja używa atrybutu accountExpires w ramach zarządzania kontami użytkowników, ten atrybut nie jest synchronizowany z usługą Azure AD. W związku z tym wygasłe konto usługi Active Directory w środowisku skonfigurowanym do synchronizacji skrótów haseł będzie nadal aktywne w usłudze Azure AD. Jeśli konto wygasło, akcja przepływu pracy powinna wyzwolić skrypt programu PowerShell, który wyłącza konto usługi Azure AD użytkownika (użyj polecenia cmdlet [Set-AzureADUser).](/powershell/module/azuread/set-azureaduser) Z kolei po włączeniu konta należy włączona usługa Azure AD.

### <a name="overwrite-synchronized-passwords"></a>Zastępowanie zsynchronizowanych haseł

Administrator może ręcznie zresetować hasło przy użyciu Windows PowerShell.

W takim przypadku nowe hasło zastępuje zsynchronizowane hasło, a wszystkie zasady haseł zdefiniowane w chmurze są stosowane do nowego hasła.

Jeśli ponownie zmienisz hasło lokalne, nowe hasło zostanie zsynchronizowane z chmurą i zastąpi ręcznie zaktualizowane hasło.

Synchronizacja hasła nie ma wpływu na zalogowaną użytkownika platformy Azure. Na bieżącą sesję usługi w chmurze nie ma natychmiastowego wpływu zsynchronizowana zmiana hasła, która występuje po zalogowaniu się do usługi w chmurze. Kmsi wydłuża czas trwania tej różnicy. Jeśli usługa w chmurze wymaga ponownego uwierzytelnienia, musisz podać nowe hasło.

### <a name="additional-advantages"></a>Dodatkowe korzyści

- Ogólnie rzecz biorąc, synchronizacja skrótów haseł jest prostsza do zaimplementowania niż usługa federska. Nie wymaga żadnych dodatkowych serwerów i eliminuje zależność od usługi federowej o wysokiej dostępnej do uwierzytelniania użytkowników.
- Oprócz federacji można również włączyć synchronizację skrótów haseł. Może być używany jako rezerwowy w przypadku awarii usługi federacyjnej.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Proces synchronizacji skrótów haseł dla Azure AD Domain Services

Jeśli używasz usługi Azure AD Domain Services do zapewnienia starszego uwierzytelniania dla aplikacji i usług, które muszą używać protokołu Kerberos, LDAP lub NTLM, niektóre dodatkowe procesy są częścią przepływu synchronizacji skrótów haseł. Azure AD Connect korzysta z dodatkowego następującego procesu, aby zsynchronizować skróty haseł z usługą Azure AD do użycia w Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect powinny być instalowane i konfigurowane tylko do synchronizacji z lokalnymi AD DS lokalnymi. Instalowanie aplikacji w domenie zarządzanej Azure AD Connect w celu synchronizowania obiektów z Azure AD DS z usługą Azure AD nie jest obsługiwane.
>
> Azure AD Connect synchronizuje starsze skróty haseł tylko po włączeniu Azure AD DS dzierżawy usługi Azure AD. Poniższe kroki nie są używane, jeśli używasz tylko usługi Azure AD Connect do synchronizowania lokalnego środowiska AD DS z usługą Azure AD.
>
> Jeśli starsze aplikacje nie korzystają z uwierzytelniania NTLM ani prostych powiązań LDAP, zaleca się wyłączenie synchronizacji skrótów haseł NTLM dla Azure AD DS. Aby uzyskać więcej informacji, zobacz Wyłączanie słabych mechanizmów szyfrowania i Synchronizacja skrótów [poświadczeń NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect pobiera klucz publiczny dla wystąpienia dzierżawy usługi Azure AD Domain Services.
1. Gdy użytkownik zmieni swoje hasło, lokalnego kontrolera domeny przechowuje wynik zmiany hasła (skróty) w dwóch atrybutów:
    * *UnicodePwd* dla skrótu hasła NTLM.
    * *supplementalCredentials* dla skrótu hasła protokołu Kerberos.
1. Azure AD Connect wykrywa zmiany haseł za pośrednictwem kanału replikacji katalogu (zmiany atrybutów, które muszą być replikowane do innych kontrolerów domeny).
1. W przypadku każdego użytkownika, którego hasło Azure AD Connect, należy wykonać następujące czynności:
    * Generuje losowy 256-bitowy klucz symetryczny AES.
    * Generuje losowy wektor inicjowania wymagany do pierwszej rundy szyfrowania.
    * Wyodrębnia skróty haseł protokołu Kerberos z *atrybutów supplementalCredentials.*
    * Sprawdza, Azure AD Domain Services konfiguracji zabezpieczeń *SyncNtlmPasswords* ustawienie.
        * Jeśli to ustawienie jest wyłączone, program generuje losowy skrót NTLM o wysokiej entropii (inny niż hasło użytkownika). Ten skrót jest następnie łączone z dokładnymi skrótami haseł protokołu Kerberos z atrybutu *supplementalCrendetials* w jedną strukturę danych.
        * Jeśli ta opcja jest włączona, łączy wartość *atrybutu unicodePwd* z wyodrębnioną wartością skrótów haseł protokołu Kerberos z atrybutu *supplementalCredentials* w jedną strukturę danych.
    * Szyfruje pojedynczą strukturę danych przy użyciu klucza symetrycznego AES.
    * Szyfruje klucz symetryczny AES przy użyciu klucza publicznego Azure AD Domain Services dzierżawy.
1. Azure AD Connect przesyła zaszyfrowany klucz symetryczny AES, zaszyfrowaną strukturę danych zawierającą skróty haseł i wektor inicjowania do usługi Azure AD.
1. Usługa Azure AD przechowuje zaszyfrowany klucz symetryczny AES, zaszyfrowaną strukturę danych i wektor inicjowania dla użytkownika.
1. Usługa Azure AD wypycha zaszyfrowany klucz symetryczny AES, zaszyfrowaną strukturę danych i wektor inicjowania przy użyciu wewnętrznego mechanizmu synchronizacji w zaszyfrowanej sesji HTTP, aby Azure AD Domain Services.
1. Azure AD Domain Services pobiera klucz prywatny dla wystąpienia dzierżawy z usługi Azure Key Vault.
1. Dla każdego zaszyfrowanego zestawu danych (reprezentującego zmianę hasła jednego użytkownika) Azure AD Domain Services następnie wykonuje następujące kroki:
    * Używa swojego klucza prywatnego do odszyfrowania klucza symetrycznego AES.
    * Używa klucza symetrycznego AES z wektorem inicjowania, aby odszyfrować zaszyfrowaną strukturę danych zawierającą skróty haseł.
    * Zapisuje skróty haseł protokołu Kerberos odbierane do kontrolera Azure AD Domain Services domeny. Skróty są zapisywane w atrybutu *supplementalCredentials* obiektu użytkownika, który jest szyfrowany do Azure AD Domain Services klucza publicznego kontrolera domeny.
    * Azure AD Domain Services zapisuje otrzymany skrót hasła NTLM do Azure AD Domain Services domeny. Skrót jest zapisywany w atlicie *unicodePwd* obiektu użytkownika, który jest szyfrowany Azure AD Domain Services klucza publicznego kontrolera domeny.

## <a name="enable-password-hash-synchronization"></a>Włączanie synchronizacji skrótów haseł

>[!IMPORTANT]
>W przypadku migrowania z usługi AD FS (lub innych technologii federacji) do synchronizacji skrótów haseł zdecydowanie zalecamy, aby postępować zgodnie z naszym szczegółowym przewodnikiem wdrażania [opublikowanym tutaj.](https://aka.ms/adfstophsdpdownload)

W przypadku instalowania Azure AD Connect przy użyciu opcji **Ustawienia ekspresowe** synchronizacja skrótów haseł jest automatycznie włączona. Aby uzyskać więcej informacji, zobacz Getting started with Azure AD Connect using express settings (Wprowadzenie [do korzystania z ustawień ekspresowych).](how-to-connect-install-express.md)

Jeśli używasz ustawień niestandardowych podczas instalowania Azure AD Connect, synchronizacja skrótów haseł jest dostępna na stronie logowania użytkownika. Aby uzyskać więcej informacji, zobacz [Niestandardowa instalacja Azure AD Connect](how-to-connect-install-custom.md).

![Włączanie synchronizacji skrótów haseł](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizacja skrótów haseł i fips
Jeśli serwer został zablokowany zgodnie z normą FEDERAL Information Processing Standard (FIPS), rozwiązanie MD5 zostanie wyłączone.

**Aby włączyć rozwiązanie MD5 do synchronizacji skrótów haseł, wykonaj następujące kroki:**

1. Przejdź do folderu %programfiles%\Microsoft Azure AD Sync\Bin.
2. Otwórz miiserver.exe.config.
3. Przejdź do węzła konfiguracji/środowiska uruchomieniowego na końcu pliku.
4. Dodaj następujący węzeł: `<enforceFIPSPolicy enabled="false"/>`
5. Zapisz zmiany.

Ten fragment kodu powinien wyglądać tak:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Aby uzyskać informacje na temat zabezpieczeń i zgodności ze standardem FIPS, zobacz [Synchronizacja skrótów haseł, szyfrowanie](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)i zgodność ze standardem FIPS w usłudze Azure AD.

## <a name="troubleshoot-password-hash-synchronization"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł
Jeśli masz problemy z synchronizacją skrótów haseł, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł.](tshoot-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Następne kroki
* [Azure AD Connect synchronizacji: dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Uzyskaj plan wdrożenia krok po kroku na temat migracji z usług ADFS do synchronizacji skrótów haseł](https://aka.ms/authenticationDeploymentPlan)
