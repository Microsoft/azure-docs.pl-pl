---
title: Rozwiązywanie problemów z lokalną ochroną hasłem usługi Azure AD
description: Dowiedz się, jak rozwiązywać problemy z ochroną hasłem w usłudze Azure AD dla środowiska lokalnego Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508d3942626c319221f4b690aaf444e034195bf
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966631"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Rozwiązywanie problemów: lokalna Ochrona hasłem w usłudze Azure AD

Po wdrożeniu ochrony hasłem usługi Azure AD może być wymagane rozwiązywanie problemów. W tym artykule szczegółowo przedstawiono kilka typowych kroków rozwiązywania problemów.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agent DC nie może zlokalizować serwera proxy w katalogu

Głównym objawem tego problemu jest 30017 zdarzeń w dzienniku zdarzeń administratora agenta kontrolera domeny.

Zwykle przyczyną tego problemu jest to, że serwer proxy nie został jeszcze zarejestrowany. Jeśli serwer proxy został zarejestrowany, może wystąpić pewne opóźnienie wynikające z opóźnienia replikacji usługi AD do momentu, gdy określony Agent kontrolera domeny będzie mógł zobaczyć ten serwer proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Agent DC nie może komunikować się z serwerem proxy

Głównym objawem tego problemu jest 30018 zdarzeń w dzienniku zdarzeń administratora agenta kontrolera domeny. Ten problem może mieć kilka możliwych przyczyn:

1. Agent DC znajduje się w izolowanej części sieci, która nie zezwala na łączność sieciową z zarejestrowanymi serwerem proxy. Ten problem może być niegroźny, o ile inni agenci kontrolera domeny mogą komunikować się z serwerem proxy w celu pobierania zasad haseł z platformy Azure. Po pobraniu te zasady zostaną następnie uzyskane przez izolowany kontroler domeny za pośrednictwem replikacji plików zasad w udziale Sysvol.

1. Komputer hosta serwera proxy blokuje dostęp do punktu końcowego mapowania punktów końcowych RPC (port 135)

   Instalator serwera proxy ochrony hasłem usługi Azure AD automatycznie tworzy regułę ruchu przychodzącego zapory systemu Windows, która umożliwia dostęp do portu 135. Jeśli ta reguła zostanie później usunięta lub wyłączona, agenci DC nie będą mogli komunikować się z usługą proxy. Jeśli wbudowana zapora systemu Windows została wyłączona zamiast innego produktu zapory, należy skonfigurować zaporę w taki sposób, aby zezwalała na dostęp do portu 135.

1. Komputer hosta serwera proxy blokuje dostęp do punktu końcowego RPC (dynamiczny lub statyczny), który jest wysłuchiwany przez usługę serwera proxy

   Instalator serwera proxy ochrony hasłem usługi Azure AD automatycznie tworzy regułę ruchu przychodzącego zapory systemu Windows, która umożliwia dostęp do wszystkich portów przychodzących nasłuchiwania przez usługę serwera proxy ochrony hasłem usługi Azure AD. Jeśli ta reguła zostanie później usunięta lub wyłączona, agenci DC nie będą mogli komunikować się z usługą proxy. Jeśli wbudowana zapora systemu Windows została wyłączona zamiast innego produktu zapory, należy skonfigurować zaporę tak, aby zezwalała na dostęp do wszystkich portów przychodzących nasłuchiwanych przez usługę serwera proxy ochrony hasłem usługi Azure AD. Ta konfiguracja może być bardziej szczegółowa, jeśli usługa serwera proxy została skonfigurowana do nasłuchiwania na określonym statycznym porcie RPC (za pomocą `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet).

1. Komputer hosta proxy nie jest skonfigurowany tak, aby umożliwić kontrolerom domeny możliwość zalogowania się na komputerze. To zachowanie jest kontrolowane przez przypisanie uprawnień użytkownika "dostęp do tego komputera z sieci". Wszystkie kontrolery domeny we wszystkich domenach w lesie muszą mieć przyznane to uprawnienie. To ustawienie jest często ograniczone w ramach większego nakładu pracy w sieci.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Usługa serwera proxy nie może komunikować się z platformą Azure

1. Upewnij się, że komputer proxy ma połączenie z punktami końcowymi wymienionymi w [wymaganiach dotyczących wdrażania](howto-password-ban-bad-on-premises-deploy.md).

1. Upewnij się, że las i wszystkie serwery proxy są zarejestrowane w ramach tej samej dzierżawy platformy Azure.

   To wymaganie można sprawdzić, uruchamiając  `Get-AzureADPasswordProtectionProxy` `Get-AzureADPasswordProtectionDCAgent` polecenia cmdlet i programu PowerShell, a następnie porównując `AzureTenant` Właściwość każdego zwróconego elementu. W celu poprawnej operacji raportowana nazwa dzierżawy musi być taka sama dla wszystkich agentów kontrolerów domeny i serwerów proxy.

   Jeśli istnieje warunek niezgodności rejestracji dzierżawy platformy Azure, ten problem może zostać rozwiązany przez uruchomienie `Register-AzureADPasswordProtectionProxy` poleceń cmdlet i/lub `Register-AzureADPasswordProtectionForest` programu PowerShell zgodnie z wymaganiami, co pozwala upewnić się, że do wszystkich rejestracji są używane poświadczenia z tej samej dzierżawy platformy Azure.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Agent DC nie może zaszyfrować lub odszyfrować plików zasad haseł

Ochrona hasłem w usłudze Azure AD ma krytyczną zależność od funkcji szyfrowania i odszyfrowywania dostarczonych przez usługę dystrybucji kluczy firmy Microsoft. Błędy szyfrowania lub odszyfrowywania mogą być w manifeście z różnymi objawami i mieć kilka możliwych przyczyn.

1. Upewnij się, że usługa KDS jest włączona i działa na wszystkich kontrolerach domeny z systemem Windows Server 2012 lub nowszym w domenie.

   Domyślnie tryb uruchamiania usługi KDS jest konfigurowany jako ręczny (wyzwalacz uruchomienia). Ta konfiguracja oznacza, że po raz pierwszy klient próbuje użyć usługi, jest uruchamiana na żądanie. Ten domyślny tryb uruchamiania usługi jest akceptowalny do działania ochrony hasłem usługi Azure AD.

   Jeśli tryb uruchamiania usługi KDS został skonfigurowany jako wyłączony, ta konfiguracja musi zostać rozwiązana, zanim Ochrona hasłem usługi Azure AD będzie działać prawidłowo.

   Prostym testem tego problemu jest ręczne uruchomienie usługi KDS przy użyciu konsoli MMC zarządzania usługami lub innych narzędzi do zarządzania (na przykład uruchom polecenie "net start kdssvc" z konsoli wiersza polecenia). Usługa KDS powinna zostać uruchomiona pomyślnie i nadal działać.

   Najbardziej powszechną przyczyną głównej przyczyny uruchomienia usługi KDS jest to, że obiekt kontrolera domeny Active Directory znajduje się poza domyślną jednostką organizacyjną Kontrolery domeny. Ta konfiguracja nie jest obsługiwana przez usługę KDS i nie jest ograniczeniem narzuconym przez ochronę hasłem usługi Azure AD. Rozwiązaniem tego problemu jest przeniesienie obiektu kontrolera domeny do lokalizacji w ramach domyślnej jednostki organizacyjnej Kontrolery domeny.

1. Niezgodna zmiana formatu szyfrowanego buforu KDS z systemu Windows Server 2012 R2 na system Windows Server 2016

   Poprawka zabezpieczeń KDS została wprowadzona w systemie Windows Server 2016, która modyfikuje format szyfrowanych buforów KDS; bufory te czasami nie mogą zostać odszyfrowane w systemach Windows Server 2012 i Windows Server 2012 R2. Odwrotny kierunek to odpowiednie bufory KDS-Encrypted w systemie Windows Server 2012, a system Windows Server 2012 R2 zawsze zostanie pomyślnie odszyfrowany w systemie Windows Server 2016 i nowszych. Jeśli kontrolery domeny w domenach Active Directory korzystają z różnych systemów operacyjnych, mogą zostać zgłoszone sporadyczne błędy odszyfrowywania ochrony hasłem w usłudze Azure AD. Nie jest możliwe dokładne przewidywalność czasu ani objawów tych awarii ze względu na charakter poprawki zabezpieczeń i zważywszy, że nie jest to deterministyczny Agent DC ochrony hasłem usługi Azure AD, na którym kontroler domeny będzie szyfrować dane w danym momencie.

   Nie ma żadnego obejścia tego problemu, który jest inny niż w przypadku niezgodności z tymi niezgodnymi systemami operacyjnymi w domenach Active Directory. Innymi słowy, należy uruchomić tylko kontrolery domeny z systemem Windows Server 2012 i Windows Server 2012 R2 lub tylko systemu Windows Server 2016 i nowszych kontrolerów domeny.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>Agent kontrolera domeny uzna, że Las nie został zarejestrowany

Objawem tego problemu jest 30016 zdarzeń rejestrowanych w kanale Agent\Admin kontrolera domeny, który znajduje się w części:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Istnieją dwie możliwe przyczyny tego problemu.

1. Las rzeczywiście nie został zarejestrowany. Aby rozwiązać ten problem, uruchom polecenie Register-AzureADPasswordProtectionForest zgodnie z opisem w temacie [wymagania dotyczące wdrażania](howto-password-ban-bad-on-premises-deploy.md).
1. Las został zarejestrowany, ale agent DC nie może odszyfrować danych rejestracji lasu. Ten przypadek ma taką samą przyczynę główną jak problem #2 wymieniony powyżej w obszarze [Agent kontrolera domeny nie może zaszyfrować lub odszyfrować plików zasad haseł](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files). W prosty sposób potwierdzić, że ten błąd będzie widoczny tylko w przypadku agentów DC działających na kontrolerach domeny systemu Windows Server 2012 lub Windows Server 2012R2, podczas gdy agenci DC działający w systemie Windows Server 2016 i nowszych kontrolerach domeny są prawidłowe. Obejście jest takie samo: uaktualnienie wszystkich kontrolerów domeny do systemu Windows Server 2016 lub nowszego.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Hasła słabe są akceptowane, ale nie powinny być

Ten problem może mieć kilka przyczyn.

1. Agenci kontrolera domeny korzystają z publicznej wersji zapoznawczej, która wygasła. Zapoznaj się z [oprogramowaniem agenta publicznej wersji zapoznawczej](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Agenci kontrolera domeny nie mogą pobrać zasad lub nie mogą odszyfrować istniejących zasad. Sprawdź możliwe przyczyny w powyższych tematach.

1. Tryb wymuszania zasad haseł nadal jest ustawiony na inspekcję. Jeśli ta konfiguracja jest w tej sytuacji, skonfiguruj ją ponownie, aby wymusić korzystanie z portalu ochrony hasłem usługi Azure AD. Aby uzyskać więcej informacji, zobacz [tryby operacji](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Zasady dotyczące haseł zostały wyłączone. Jeśli ta konfiguracja jest aktywna, skonfiguruj ją ponownie, aby była dostępna przy użyciu portalu ochrony hasłem usługi Azure AD. Aby uzyskać więcej informacji, zobacz [tryby operacji](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Nie zainstalowano oprogramowania agenta kontrolera domeny na wszystkich kontrolerach domeny w domenie. W takiej sytuacji trudno jest zapewnić, że zdalni klienci systemu Windows będą kierowani do określonego kontrolera domeny podczas operacji zmiany hasła. Jeśli uważasz, że dla konkretnego kontrolera domeny, w którym zainstalowano oprogramowanie Agent DC, został pomyślnie skierowany użytkownik, możesz sprawdzić, sprawdzając, czy dziennik zdarzeń administratora agenta kontrolera domeny ma co najmniej jedno zdarzenie, aby udokumentować wynik weryfikacji hasła. Jeśli nie ma żadnego zdarzenia dla użytkownika, którego hasło zostało zmienione, zmiana hasła prawdopodobnie została przetworzona przez inny kontroler domeny.

   Alternatywny Test polega na tym, że podczas logowania bezpośrednio do kontrolera domeny, na którym jest zainstalowane oprogramowanie Agent DC, należy setting\changing hasła. Ta technika nie jest zalecana w przypadku domen Active Directory produkcyjnych.

   Podczas przyrostowego wdrażania oprogramowania agenta kontrolera domeny jest obsługiwane z uwzględnieniem tych ograniczeń, firma Microsoft zdecydowanie zaleca, aby oprogramowanie Agent DC zostało zainstalowane na wszystkich kontrolerach domeny w domenie najszybciej, jak to możliwe.

1. Algorytm weryfikacji hasła może faktycznie działać zgodnie z oczekiwaniami. Zobacz [jak oceniane są hasła](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Nie można ustawić słabego hasła DSRM Ntdsutil.exe

Active Directory zawsze zweryfikuje nowe hasło trybu naprawy usług katalogowych, aby upewnić się, że spełnia wymagania dotyczące złożoności hasła domeny; to sprawdzanie poprawności wywołuje również metody filtrowania hasła, takie jak ochrona hasłem usługi Azure AD. Jeśli nowe hasło trybu DSRM zostanie odrzucone, zostanie wyświetlony następujący komunikat o błędzie:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Gdy ochrona hasłem w usłudze Azure AD rejestruje zdarzenia w dzienniku zdarzeń weryfikacji hasła dla hasła Active Directory DSRM, oczekuje się, że komunikaty dziennika zdarzeń nie będą zawierać nazwy użytkownika. Takie zachowanie występuje, ponieważ konto trybu DSRM jest kontem lokalnym, które nie jest częścią rzeczywistej domeny Active Directory.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Podwyższanie poziomu repliki kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła DSRM

W procesie podwyższania poziomu kontrolera domeny nowe hasło trybu naprawy usług katalogowych zostanie przesłane do istniejącego kontrolera domeny w domenie w celu weryfikacji. Jeśli nowe hasło trybu DSRM zostanie odrzucone, zostanie wyświetlony następujący komunikat o błędzie:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Podobnie jak w przypadku powyższego problemu, wszystkie zdarzenia weryfikacji hasła ochrony hasła usługi Azure AD będą mieć pustą nazwę użytkownika w tym scenariuszu.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Obniżanie poziomu kontrolera domeny nie powiodło się z powodu słabego hasła administratora lokalnego

Jest ona obsługiwana, aby obniżyć poziom kontrolera domeny, w którym nadal działa oprogramowanie agenta kontrolera sieci. Administratorzy powinni mieć jednak świadomość, że oprogramowanie agenta DC nadal wymusza bieżące zasady haseł podczas procedury obniżania poziomu. Nowe hasło do konta administratora lokalnego (określone jako część operacji obniżania poziomu) jest weryfikowane jak każde inne hasło. Firma Microsoft zaleca, aby w ramach procedury obniżania poziomu kontrolera domeny wybierać bezpieczne hasła dla kont administratorów lokalnych.

Po pomyślnym obniżeniu poziomu i ponownym uruchomieniu kontrolera domeny i ponownym uruchomieniu programu jako normalny serwer członkowski oprogramowanie Agent DC zostanie przywrócone do trybu pasywnego. Może on zostać odinstalowany w dowolnym momencie.

## <a name="booting-into-directory-services-repair-mode"></a>Rozruch w trybie naprawy usług katalogowych

Jeśli kontroler domeny jest uruchamiany w trybie naprawy usług katalogowych, biblioteka DLL filtru haseł agenta DC wykryje ten warunek i spowoduje wyłączenie wszystkich działań weryfikacji hasła lub wymuszania, niezależnie od obecnie aktywnej konfiguracji zasad. Biblioteka DLL filtru haseł agenta DC będzie rejestrować zdarzenie ostrzeżenia 10023 w dzienniku zdarzeń administratora, na przykład:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Oprogramowanie agenta dla publicznej wersji zapoznawczej nie wygasło

W okresie publicznej wersji zapoznawczej ochrony za pomocą hasła usługi Azure AD oprogramowanie Agent DC zostało trwale zakodowane w celu zatrzymania przetwarzania żądań weryfikacji hasła w następujących datach:

* Wersja 1.2.65.0 zatrzyma przetwarzanie żądań weryfikacji hasła na 1 2019 września.
* Wersja 1.2.25.0 i wcześniejsze zatrzymane przetwarzanie żądań weryfikacji hasła 1 2019 lipca.

Zgodnie z ostatecznym terminem wszystkie wersje agenta kontrolera domeny z ograniczoną ilością czasu wyemitują zdarzenie 10021 w dzienniku zdarzeń administratora agenta kontrolera domeny w czasie rozruchu, który będzie wyglądać następująco:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Po upływie terminu ostatecznego wszystkie wersje agenta kontrolera domeny z ograniczoną ilością czasu wyemitują zdarzenie 10022 w dzienniku zdarzeń administratora agenta kontrolera domeny w czasie rozruchu, który będzie wyglądać następująco:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Ponieważ termin ostateczny jest sprawdzany tylko podczas początkowego rozruchu, te zdarzenia mogą nie być wyświetlane do momentu, gdy upłynie termin kalendarzowy. Po rozpoznaniu terminu ostatecznego nie będą miały żadnych negatywnych efektów na kontrolerze domeny ani większego środowiska.

> [!IMPORTANT]
> Firma Microsoft zaleca, aby wygasły od razu uaktualniony publiczny Agent DC do najnowszej wersji.

Aby łatwo wykryć agentów DC w środowisku, które muszą zostać uaktualnione, należy uruchomić `Get-AzureADPasswordProtectionDCAgent` polecenie cmdlet, na przykład:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

W tym temacie pole SoftwareVersion jest oczywiście właściwością klucza do wyszukania. Możesz również użyć filtrowania programu PowerShell, aby odfiltrować agentów DC, które są już w wymaganej wersji linii bazowej, na przykład:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

W żadnej wersji nie ograniczono czasu na oprogramowanie serwera proxy ochrony hasłem usługi Azure AD. Firma Microsoft w dalszym ciągu zaleca uaktualnienie kontrolerów DC i proxy do najnowszych wersji. `Get-AzureADPasswordProtectionProxy`Polecenie cmdlet może służyć do znajdowania agentów proxy, którzy wymagają uaktualnień, podobnie jak powyżej w przypadku agentów DC.

Zapoznaj się z tematem [Uaktualnianie agenta kontrolera domeny](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) i [uaktualnianie usługi proxy](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) , aby uzyskać więcej szczegółowych informacji na temat określonych procedur uaktualniania.

## <a name="emergency-remediation"></a>Korygowanie awaryjne

Jeśli wystąpi sytuacja, w której usługa agenta kontrolera domeny powoduje problemy, usługa agenta kontrolera domeny może zostać natychmiast wyłączona. Biblioteka DLL filtru haseł agenta DC nadal próbuje wywołać niedziałającą usługę i będzie rejestrować zdarzenia ostrzegawcze (10012, 10013), ale w tym czasie wszystkie hasła przychodzące są akceptowane. Usługę agenta kontrolera domeny można również skonfigurować za pomocą Menedżera kontroli usług systemu Windows z typem uruchamiania "wyłączone" zgodnie z wymaganiami.

Kolejną środkiem naprawczym byłoby ustawienie trybu włączania na wartość nie w portalu ochrony hasłem usługi Azure AD. Po pobraniu zaktualizowanych zasad każda usługa agenta kontrolera domeny przejdzie w tryb quiescent, w którym wszystkie hasła są akceptowane jako-is. Aby uzyskać więcej informacji, zobacz [tryby operacji](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Praw

Jeśli podjęto decyzję o odinstalowaniu oprogramowania ochrony hasłem usługi Azure AD i oczyszczeniu wszystkich powiązanych Stanów z domen i lasów, to zadanie można wykonać, wykonując następujące czynności:

> [!IMPORTANT]
> Ważne jest, aby wykonać te kroki w kolejności. Jeśli jakiekolwiek wystąpienie usługi proxy zostanie uruchomione, spowoduje to ponowne utworzenie obiektu serviceConnectionPoint. Jeśli jakiekolwiek wystąpienie usługi agenta kontrolera domeny zostanie uruchomione, będzie okresowo utworzyć ponownie obiekt serviceConnectionPoint i stan SYSVOL.

1. Odinstaluj oprogramowanie serwera proxy ze wszystkich komputerów. Ten krok nie **wymaga ponownego** uruchomienia.
2. Odinstaluj oprogramowanie agenta kontrolera domeny ze wszystkich kontrolerów domeny. Ten krok **wymaga** ponownego uruchomienia.
3. Ręcznie usuń wszystkie punkty połączenia usługi serwera proxy w każdym kontekście nazewnictwa domen. Lokalizację tych obiektów można odnaleźć za pomocą następującego polecenia Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nie pomijaj gwiazdki ("*") na końcu wartości zmiennej $keywords.

   Obiekty uzyskane za pośrednictwem `Get-ADObject` polecenia mogą być następnie potoku `Remove-ADObject` lub usunięte ręcznie.

4. Ręcznie usuń wszystkie punkty połączenia z agentem DC w każdym kontekście nazewnictwa domen. W zależności od tego, jak szeroko wdrożono oprogramowanie, może istnieć jeden z tych obiektów na kontroler domeny w lesie. Lokalizację tego obiektu można odnaleźć za pomocą następującego polecenia Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Obiekty uzyskane za pośrednictwem `Get-ADObject` polecenia mogą być następnie potoku `Remove-ADObject` lub usunięte ręcznie.

   Nie pomijaj gwiazdki ("*") na końcu wartości zmiennej $keywords.

5. Ręcznie usuń stan konfiguracji poziomu lasu. Stan konfiguracji lasu jest przechowywany w kontenerze w kontekście nazewnictwa konfiguracji Active Directory. Można go odnaleźć i usunąć w następujący sposób:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ręcznie usuń wszystkie stany związane z folderem Sysvol przez ręczne usunięcie następującego folderu i całej jego zawartości:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   W razie potrzeby Ta ścieżka może być również dostępna lokalnie na danym kontrolerze domeny; lokalizacja domyślna będzie wyglądać następująco:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ta ścieżka jest inna, jeśli udział SYSVOL został skonfigurowany w lokalizacji innej niż domyślna.

## <a name="next-steps"></a>Następne kroki

[Często zadawane pytania dotyczące ochrony hasłem w usłudze Azure AD](howto-password-ban-bad-on-premises-faq.md)

Aby uzyskać więcej informacji na temat globalnych i niestandardowych list zakazanych haseł, zobacz artykuł Zablokuj [złe hasła](concept-password-ban-bad.md)
