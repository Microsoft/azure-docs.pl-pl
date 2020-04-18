---
title: Zapewnianie funkcji usługi Azure MFA przy użyciu usługi NPS — usługa Azure Active Directory
description: Dodawanie opartych na chmurze funkcji weryfikacji dwuetapowej do istniejącej infrastruktury uwierzytelniania
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc1be4637d56d7205d50ebfc6f7d1d5d22e62edf
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617664"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrowanie istniejącej infrastruktury NPS z usługą Azure Multi-Factor Authentication

Rozszerzenie serwera zasad sieciowych (NPS) dla usługi Azure MFA dodaje funkcje usługi MFA oparte na chmurze do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Dzięki rozszerzeniu NPS możesz dodawać połączenia telefoniczne, wiadomości tekstowe lub weryfikację aplikacji telefonicznej do istniejącego przepływu uwierzytelniania bez konieczności instalowania, konfigurowania i obsługi nowych serwerów. 

To rozszerzenie zostało utworzone dla organizacji, które chcą chronić połączenia sieci VPN bez wdrażania serwera usługi Azure MFA Server. Rozszerzenie SERWERA SIECIOWEGO działa jako karta między usługą RADIUS a chmurową usługą Azure MFA, aby zapewnić drugi czynnik uwierzytelniania dla użytkowników federowanych lub zsynchronizowanych.

Podczas korzystania z rozszerzenia SERWERA SERWERA dla usługi Azure MFA przepływ uwierzytelniania zawiera następujące składniki: 

1. **Serwer NAS/VPN** odbiera żądania od klientów sieci VPN i konwertuje je na żądania RADIUS na serwery NPS. 
2. **Serwer SERWERA NPS** łączy się z usługą Active Directory w celu wykonania uwierzytelniania podstawowego dla żądań RADIUS i po powodzeniu przekazuje żądanie do wszystkich zainstalowanych rozszerzeń.  
3. **Rozszerzenie serwera NPS** wyzwala żądanie usługi Azure MFA dla uwierzytelniania pomocniczego. Gdy rozszerzenie otrzyma odpowiedź, a jeśli wyzwanie usługi MFA zakończy się pomyślnie, kończy żądanie uwierzytelniania, zapewniając serwerowi NPS tokeny zabezpieczające, które zawierają oświadczenie usługi MFA wystawione przez usługę Azure STS.  
4. **Usługa Azure MFA** komunikuje się z usługą Azure Active Directory w celu pobrania szczegółów użytkownika i wykonuje uwierzytelnianie pomocnicze przy użyciu metody weryfikacji skonfigurowanej dla użytkownika.

Na poniższym diagramie przedstawiono ten przepływ żądań uwierzytelniania wysokiego poziomu: 

![Diagram przepływu uwierzytelniania](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planowanie wdrożenia

Rozszerzenie NPS automatycznie obsługuje nadmiarowość, więc nie potrzebujesz specjalnej konfiguracji.

Można utworzyć dowolną liczbę serwerów NPS obsługujących usługę Azure MFA. Jeśli zainstalujesz wiele serwerów, należy użyć certyfikatu klienta różnicy dla każdego z nich. Utworzenie certyfikatu dla każdego serwera oznacza, że można aktualizować każdy certyfikat indywidualnie i nie martwić się o przestoje na wszystkich serwerach.

Serwery sieci VPN kierują żądania uwierzytelniania, więc muszą być świadome nowych serwerów NPS obsługujących usługę Azure MFA.

## <a name="prerequisites"></a>Wymagania wstępne

Rozszerzenie NPS jest przeznaczone do pracy z istniejącą infrastrukturą. Przed rozpoczęciem upewnij się, że masz następujące wymagania wstępne.

### <a name="licenses"></a>Licencje

Rozszerzenie serwera NPS dla usługi Azure MFA jest dostępne dla klientów z [licencjami na uwierzytelnianie wieloskładnikowe platformy Azure](multi-factor-authentication.md) (dołączone do usługi Azure AD Premium, EMS lub licencji autonomicznej usługi MFA). Licencje oparte na zużyciu dla usługi Azure MFA, takie jak licencje na użytkownika lub uwierzytelniania, nie są zgodne z rozszerzeniem serwera NPS. 

### <a name="software"></a>Oprogramowanie

Windows Server 2008 R2 SP1 lub wyższy.

### <a name="libraries"></a>Biblioteki

Biblioteki te są instalowane automatycznie z rozszerzeniem.

- [Pakiety redystrybucyjne programu Visual C++ dla programu Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Moduł usługi Microsoft Azure Active Directory dla systemu Windows PowerShell w wersji 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Moduł usługi Microsoft Azure Active Directory dla programu Windows PowerShell jest zainstalowany, jeśli nie jest jeszcze obecny, za pomocą skryptu konfiguracji uruchomionego w ramach procesu instalacji. Nie ma potrzeby instalowania tego modułu z wyprzedzeniem, jeśli nie jest jeszcze zainstalowany.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Wszystkie osoby korzystające z rozszerzenia serwera NPS muszą być synchronizowane z usługą Azure Active Directory przy użyciu usługi Azure AD Connect i muszą być zarejestrowane dla usługi MFA.

Po zainstalowaniu rozszerzenia potrzebujesz identyfikatora katalogu i poświadczeń administratora dla dzierżawy usługi Azure AD. Identyfikator katalogu można znaleźć w [witrynie Azure portal](https://portal.azure.com). Zaloguj się jako administrator. Wyszukaj i wybierz **usługę Azure Active Directory,** a następnie wybierz pozycję **Właściwości**. Skopiuj identyfikator GUID w polu **Identyfikator katalogu** i zapisz go. Ten identyfikator GUID jest używany jako identyfikator dzierżawy podczas instalowania rozszerzenia NPS.

![Znajdowanie identyfikatora katalogu we właściwościach usługi Azure Active Directory](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Serwer NPS musi być w stanie komunikować się z następującymi adresami URL za pomocą portów 80 i 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https:\//credentials.azure.com

Ponadto łączność z następującymi adresami URL jest wymagana do ukończenia [konfiguracji karty przy użyciu dostarczonego skryptu programu PowerShell](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

Przed zainstalowaniem rozszerzenia NPS, należy przygotować środowisko do obsługi ruchu uwierzytelniania.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Włączanie roli serwera NPS na serwerze przyłączanym do domeny

Serwer SERWERA NPS łączy się z usługą Azure Active Directory i uwierzytelnia żądania usługi MFA. Wybierz jeden serwer dla tej roli. Zaleca się wybranie serwera, który nie obsługuje żądań z innych usług, ponieważ rozszerzenie NPS zgłasza błędy dla żądań, które nie są radius. Serwer NPS musi być skonfigurowany jako podstawowy i pomocniczy serwer uwierzytelniania dla twojego środowiska; nie może proxy żądań RADIUS do innego serwera.

1. Na serwerze otwórz **Kreatora dodawania ról i funkcji** z menu Szybki start Menedżera serwera.
2. Wybierz **pozycję Instalacja oparta na rolach lub funkcjach** dla swojego typu instalacji.
3. Wybierz rolę serwera **Zasad sieciowych i Usług dostępu.** Może pojawić się okno informujące o wymaganych funkcjach do uruchomienia tej roli.
4. Kontynuuj przez kreatora, aż strona potwierdzenia. Wybierz pozycję **Zainstaluj**.

Teraz, gdy masz serwer wyznaczony dla serwera NPS, należy również skonfigurować ten serwer do obsługi przychodzących żądań RADIUS z rozwiązania sieci VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurowanie rozwiązania sieci VPN do komunikacji z serwerem NPS

W zależności od używanego rozwiązania sieci VPN kroki konfigurowania zasad uwierzytelniania RADIUS są różne. Skonfiguruj tę zasadę tak, aby wskazywała serwer NPS usługi RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronizowanie użytkowników domeny z chmurą

Ten krok może być już ukończony w dzierżawie, ale dobrze jest dokładnie sprawdzić, czy usługa Azure AD Connect niedawno zsynchronizowała bazy danych.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Wybieranie **usługi Azure Active Directory** > **Azure AD Connect**
3. Sprawdź, czy stan synchronizacji jest **włączony** i czy ostatnia synchronizacja była mniej niż godzina temu.

Jeśli chcesz rozpocząć nową rundę synchronizacji, us instrukcje w [synchronizacji usługi Azure AD Connect: Harmonogram](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Określanie metod uwierzytelniania, których użytkownicy mogą używać

Istnieją dwa czynniki, które wpływają na to, które metody uwierzytelniania są dostępne we wdrożeniu rozszerzenia NPS:

1. Algorytm szyfrowania haseł używany między klientem RADIUS (VPN, serwerem Netscaler lub innym) a serwerami NPS.
   - **Pap** obsługuje wszystkie metody uwierzytelniania usługi Azure MFA w chmurze: połączenie telefoniczne, jednokierunkową wiadomość tekstową, powiadomienie aplikacji mobilnej, tokeny sprzętowe OATH i kod weryfikacyjny aplikacji mobilnej.
   - **CHAPV2** i **EAP** obsługują połączenia telefoniczne i powiadomienia o aplikacji mobilnej.

      > [!NOTE]
      > Podczas wdrażania rozszerzenia NPS, użyj tych czynników, aby ocenić, które metody są dostępne dla użytkowników. Jeśli klient radius obsługuje pap, ale środowisko użytkownika klienta nie ma pól wejściowych dla kodu weryfikacyjnego, a następnie połączenia telefonicznego i powiadomienia aplikacji mobilnej są dwie obsługiwane opcje.
      >
      > Ponadto jeśli środowisko użytkownika klienta sieci VPN obsługuje pole wejściowe i skonfigurowano zasady dostępu do sieci — uwierzytelnianie może zakończyć się pomyślnie, jednak żaden z atrybutów RADIUS skonfigurowanych w zasadach sieciowych nie zostanie zastosowany ani do urządzenia dostępu sieciowego, takiego jak serwer RRAS, ani do klienta sieci VPN. W rezultacie klient sieci VPN może mieć większy dostęp niż żądany lub mniej do braku dostępu.
      >

2. Metody wprowadzania, które aplikacja kliencka (VPN, serwer Netscaler lub inne) może obsłużyć. Na przykład, czy klient sieci VPN ma jakieś środki, aby umożliwić użytkownikowi wpisywać kod weryfikacyjny z tekstu lub aplikacji mobilnej?

[Nieobsługiwałeś nieobsługiwał metod uwierzytelniania na](howto-mfa-mfasettings.md#verification-methods) platformie Azure.

### <a name="register-users-for-mfa"></a>Rejestrowanie użytkowników w celu uzyskania usługi MFA

Przed wdrożeniem i użyciem rozszerzenia NPS użytkownicy, którzy są zobowiązani do przeprowadzenia weryfikacji dwuetapowej, muszą być zarejestrowani dla usługi MFA. Więcej natychmiast, aby przetestować rozszerzenie podczas wdrażania, potrzebujesz co najmniej jednego konta testowego, które jest w pełni zarejestrowane dla uwierzytelniania wieloskładnikowego.

Wykonaj następujące kroki, aby uruchomić konto testowe:

1. Zaloguj się [https://aka.ms/mfasetup](https://aka.ms/mfasetup) przy za pomocą konta testowego.
2. Postępuj zgodnie z instrukcjami, aby skonfigurować metodę weryfikacji.
3. [Utwórz zasady dostępu warunkowego,](howto-mfa-getstarted.md#create-conditional-access-policy) aby wymagać uwierzytelniania wieloskładnikowego dla konta testowego.

> [!IMPORTANT]
>
> Upewnij się, że użytkownicy pomyślnie zarejestrowali się w usłudze Azure Multi-Factor Authentication. Jeśli użytkownicy wcześniej zarejestrowali tylko do samodzielnego resetowania hasła (SSPR), *strongAuthenticationMetody* są włączone dla ich konta. Uwierzytelnianie wieloskładnikowe platformy Azure jest wymuszane, gdy *strongauthenticationMetods* jest skonfigurowany, nawet jeśli użytkownik jest zarejestrowany tylko dla samookreślenia.
>
> Można włączyć połączoną rejestrację zabezpieczeń, która konfiguruje jednocześnie wiele funkcji SSPR i uwierzytelnianie wieloskładnikowe platformy Azure. Aby uzyskać więcej informacji, zobacz [Włączanie połączonej rejestracji informacji o zabezpieczeniach w usłudze Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Można również [wymusić na użytkownikach ponowne rejestrowanie metod uwierzytelniania,](howto-mfa-userdevicesettings.md#manage-user-authentication-options) jeśli wcześniej włączyli tylko samookreślenie SSPR.

## <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia NPS

> [!IMPORTANT]
> Zainstaluj rozszerzenie NPS na innym serwerze niż punkt dostępu sieci VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Pobieranie i instalowanie rozszerzenia NPS dla usługi Azure MFA

1. [Pobierz rozszerzenie NPS](https://aka.ms/npsmfa) z Centrum pobierania Microsoft.
2. Skopiuj plik binarny na serwer zasad sieciowych, który chcesz skonfigurować.
3. Uruchom *plik setup.exe* i postępuj zgodnie z instrukcjami instalacji. Jeśli wystąpią błędy, sprawdź, czy dwie biblioteki z sekcji warunek wstępny zostały pomyślnie zainstalowane.

#### <a name="upgrade-the-nps-extension"></a>Uaktualnianie rozszerzenia NPS

Podczas uaktualniania istniejącego rozszerzenia NPS, aby uniknąć ponownego uruchomienia serwera źródłowego, wykonaj następujące kroki:

1. Odinstalowywanie istniejącej wersji
1. Uruchamianie nowego instalatora
1. Ponowne uruchamianie usługi serwera zasad sieciowych (IAS)

### <a name="run-the-powershell-script"></a>Uruchom skrypt programu PowerShell

Instalator tworzy skrypt programu PowerShell w `C:\Program Files\Microsoft\AzureMfa\Config` tej lokalizacji: (gdzie C:\ jest dyskinstalacyjny). Ten skrypt programu PowerShell wykonuje następujące akcje za każdym razem, gdy jest uruchamiany:

- Utwórz certyfikat z podpisem własnym.
- Skojarz klucz publiczny certyfikatu z podmiotem usługi w usłudze Azure AD.
- Przechowuj certyfikat w lokalnym magazynie certyfikatów maszyn.
- Udziel dostępu do klucza prywatnego certyfikatu użytkownikowi sieci.
- Uruchom ponownie serwer NPS.

Jeśli nie chcesz używać własnych certyfikatów (zamiast certyfikatów z podpisem własnym, które generuje skrypt programu PowerShell), uruchom skrypt programu PowerShell, aby zakończyć instalację. Jeśli rozszerzenie zostanie zainstalowane na wielu serwerach, każdy z nich powinien mieć własny certyfikat.

1. Uruchom program Windows PowerShell jako administrator.
2. Zmienianie katalogów.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Uruchom skrypt programu PowerShell utworzony przez instalatora.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Zaloguj się do usługi Azure AD jako administrator.
5. Program PowerShell monituje o identyfikator dzierżawy. Użyj identyfikatora GUID identyfikatora katalogu skopiowanego z witryny Azure portal w sekcji wymagania wstępne.
6. Program PowerShell pokazuje komunikat o powodach po zakończeniu skryptu.  

Powtórz te kroki na wszystkich dodatkowych serwerach NPS, które chcesz skonfigurować do równoważenia obciążenia.

Jeśli poprzedni certyfikat komputera wygasł i wygenerowano nowy certyfikat, należy usunąć wszystkie wygasłe certyfikaty. Wygaśnięcie certyfikatów może spowodować problemy z uruchomieniem rozszerzenia NPS.

> [!NOTE]
> Jeśli używasz własnych certyfikatów zamiast generowania certyfikatów za pomocą skryptu programu PowerShell, upewnij się, że są one zgodne z konwencją nazewnictwa serwera NPS. Nazwa podmiotu musi być **\<CN= TenantID\>,OU=Rozszerzenie NPS firmy Microsoft**. 

### <a name="microsoft-azure-government-additional-steps"></a>Dodatkowe kroki platformy Microsoft Azure dla instytucji rządowych

W przypadku klientów korzystających z chmury azure dla instytucji rządowych na każdym serwerze serwera NPS wymagane są następujące dodatkowe kroki konfiguracji:

1. Otwórz **Edytor rejestru** na serwerze NPS.
1. Przejdź do adresu `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Ustaw następujące wartości kluczy:

    | Klucz rejestru       | Wartość |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Powtórz poprzednie dwa kroki, aby ustawić wartości klucza rejestru dla każdego serwera NPS.
1. Uruchom ponownie usługę NPS dla każdego serwera NPS.

    Aby uzyskać minimalny wpływ, należy wyjąć każdy serwer NPS z rotacji równoważenia obciążenia sieciowego po jednym naraz i poczekać, aż wszystkie połączenia wyczerpują się.

### <a name="certificate-rollover"></a>Przerzucie certyfikatu

W wersji 1.0.1.32 rozszerzenia NPS odczyt wielu certyfikatów jest teraz obsługiwany. Ta funkcja ułatwi aktualizację certyfikatów stopniowych przed ich wygaśnięciem. Jeśli w organizacji jest uruchomiona poprzednia wersja rozszerzenia nps, należy uaktualnić do wersji 1.0.1.32 lub nowszej.

Certyfikaty utworzone `AzureMfaNpsExtnConfigSetup.ps1` przez skrypt są ważne przez 2 lata. Organizacje IT powinny monitorować certyfikaty pod kątem wygaśnięcia. Certyfikaty dla rozszerzenia NPS są umieszczane w magazynie certyfikatów komputera lokalnego w obszarze Osobiste i są wystawiane do identyfikatora dzierżawy dostarczonego do skryptu.

Gdy certyfikat zbliża się do daty wygaśnięcia, należy utworzyć nowy certyfikat, aby go zastąpić.  Ten proces jest realizowany `AzureMfaNpsExtnConfigSetup.ps1` przez uruchomienie ponownie i utrzymanie tego samego identyfikatora dzierżawy po wyświetleniu monitu. Ten proces należy powtórzyć na każdym serwerze NPS w twoim środowisku.

## <a name="configure-your-nps-extension"></a>Konfigurowanie rozszerzenia NPS

Ta sekcja zawiera zagadnienia projektowe i sugestie dotyczące pomyślnych wdrożeń rozszerzeń serwera NPS.

### <a name="configuration-limitations"></a>Ograniczenia konfiguracji

- Rozszerzenie SERWERA NPS dla usługi Azure MFA nie zawiera narzędzi do migracji użytkowników i ustawień z serwera usługi MFA do chmury. Z tego powodu sugerujemy użycie rozszerzenia dla nowych wdrożeń, a nie istniejącego wdrożenia. Jeśli używasz rozszerzenia w istniejącym wdrożeniu, użytkownicy muszą wykonać proof-up ponownie wypełnić swoje szczegóły usługi MFA w chmurze.  
- Rozszerzenie serwera NPS używa nazwy UPN z lokalnego katalogu Active directory do identyfikowania użytkownika usługi Azure MFA do wykonywania pomocniczej uwierzytelniania. Rozszerzenie można skonfigurować tak, aby używało innego identyfikatora, takiego jak alternatywny identyfikator logowania lub niestandardowe pole usługi Active Directory inne niż nazwa UPN. Aby uzyskać więcej informacji, zobacz artykuł [Zaawansowane opcje konfiguracji rozszerzenia NPS dla uwierzytelniania wieloskładnikowego](howto-mfa-nps-extension-advanced.md).
- Nie wszystkie protokoły szyfrowania obsługują wszystkie metody weryfikacji.
   - **PAP** obsługuje połączenie telefoniczne, jednokierunkową wiadomość tekstową, powiadomienie w aplikacji mobilnej i kod weryfikacyjny aplikacji mobilnej
   - **CHAPV2** i **EAP** obsługują połączenia telefoniczne i powiadomienia o aplikacji mobilnej

### <a name="control-radius-clients-that-require-mfa"></a>Sterowanie klientami usługi RADIUS, które wymagają usługi MFA

Po włączeniu usługi MFA dla klienta USŁUGI RADIUS przy użyciu rozszerzenia NPS wszystkie uwierzytelnienia dla tego klienta są wymagane do wykonywania usługi MFA. Jeśli chcesz włączyć usługę MFA dla niektórych klientów radius, ale nie dla innych, możesz skonfigurować dwa serwery NPS i zainstalować rozszerzenie tylko na jednym z nich. Skonfiguruj klientów USŁUGI RADIUS, których potrzebujesz usługi MFA do wysyłania żądań do serwera NPS skonfigurowany z rozszerzeniem, oraz innych klientów RADIUS do serwera NPS, który nie jest skonfigurowany z rozszerzeniem.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Przygotuj się na użytkowników, którzy nie są zarejestrowani w usłudze MFA

Jeśli masz użytkowników, którzy nie są zarejestrowani dla usługi MFA, można określić, co się dzieje, gdy próbują uwierzytelnić. Użyj ustawienia rejestru *REQUIRE_USER_MATCH* w ścieżce rejestru *HKLM\Software\Microsoft\AzureMFA,* aby kontrolować zachowanie funkcji. To ustawienie ma jedną opcję konfiguracji:

| Klucz | Wartość | Domyślne |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | PRAWDA/FAŁSZ | Nie ustawiono (odpowiednik TRUE) |

Celem tego ustawienia jest określenie, co należy zrobić, gdy użytkownik nie jest zarejestrowany dla usługi MFA. Gdy klucz nie istnieje, nie jest ustawiony lub jest ustawiony na WARTOŚĆ TRUE, a użytkownik nie jest zarejestrowany, a następnie rozszerzenie kończy wyzwanie usługi MFA. Gdy klucz jest ustawiony na FALSE, a użytkownik nie jest rejestrowany, uwierzytelnianie przebiega bez wykonywania usługi MFA. Jeśli użytkownik jest zarejestrowany w uwierzytelniania wieloskładnikowego, musi uwierzytelnić się za pomocą usługi MFA, nawet jeśli REQUIRE_USER_MATCH jest ustawiona na FALSE.

Można utworzyć ten klucz i ustawić go na FALSE, gdy użytkownicy są dołączania i nie wszystkie mogą być zarejestrowane dla usługi Azure MFA jeszcze. Jednak ponieważ ustawienie klucza pozwala użytkownikom, którzy nie są zarejestrowani dla usługi MFA, aby zalogować się, należy usunąć ten klucz przed przejściem do produkcji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="nps-extension-health-check-script"></a>Skrypt sprawdzania kondycji rozszerzenia NPS

Poniższy skrypt jest dostępny do wykonywania podstawowych kroków sprawdzania kondycji podczas rozwiązywania problemów z rozszerzeniem NPS.

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Jak sprawdzić, czy certyfikat klienta jest zainstalowany zgodnie z oczekiwaniami?

Poszukaj certyfikatu z podpisem własnym utworzonego przez instalatora w magazynie certyfikatów i sprawdź, czy klucz prywatny ma uprawnienia przyznane usłudze **sieciowej**użytkownika . Cert ma nazwę podmiotu ** \<CN\>tenantid , OU = Rozszerzenie NPS firmy Microsoft**

Certyfikaty z podpisem własnym generowane przez skrypt *AzureMfaNpsExtnConfigSetup.ps1* mają również okres ważności dwóch lat. Podczas sprawdzania, czy certyfikat jest zainstalowany, należy również sprawdzić, czy certyfikat nie wygasł.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Jak sprawdzić, czy mój certyfikat klienta jest skojarzony z moją dzierżawą w usłudze Azure Active Directory?

Otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenia:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Te polecenia drukują wszystkie certyfikaty kojarzące dzierżawę z wystąpieniem rozszerzenia NPS w sesji programu PowerShell. Poszukaj certyfikatu, eksportując certyfikat klienta jako plik X.509(.cer)" zakodowany w bazie podstawowej jako "Base-64 kodowany bez klucza prywatnego" i porównaj go z listą z programu PowerShell.

Następujące polecenie utworzy plik o nazwie "npscertificate" na dysku "C:" w formacie .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Po uruchomieniu tego polecenia przejdź do dysku C, znajdź plik i kliknij go dwukrotnie. Przejdź do szczegółów i przewiń w dół do "odcisk palca", porównaj odcisk palca certyfikatu zainstalowanego na serwerze z tym. Odciski palców certyfikatu powinny być zgodne.

Sygnatury czasowe ważne od i ważne do, które są w formie czytelnej dla człowieka, mogą służyć do filtrowania oczywistych błędów, jeśli polecenie zwraca więcej niż jeden certyfikat.

---

### <a name="why-cannot-i-sign-in"></a>Dlaczego nie mogę się zalogować?

Sprawdź, czy hasło nie wygasło. Rozszerzenie NPS nie obsługuje zmiany haseł w ramach przepływu pracy logowania. Aby uzyskać dalszą pomoc, skontaktuj się z personelem it swojej organizacji.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Dlaczego moje żądania nie popełniają się błędem tokenu ADAL?

Ten błąd może być spowodowany jedną z kilku przyczyn. Wykonaj następujące kroki, aby rozwiązać następujące problemy:

1. Uruchom ponownie serwer NPS.
2. Sprawdź, czy certyfikat klienta jest zainstalowany zgodnie z oczekiwaniami.
3. Sprawdź, czy certyfikat jest skojarzony z dzierżawą w usłudze Azure AD.
4. Upewnij się, że adres `https://login.microsoftonline.com/` jest dostępny z serwera, na którym działa rozszerzenie.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Dlaczego uwierzytelnianie kończy się niepowodzeniem z powodu błędu w dziennikach HTTP stwierdzającego, że użytkownik nie został znaleziony?

Sprawdź, czy usługa AD Connect jest uruchomiona i czy użytkownik jest obecny zarówno w usłudze Windows Active Directory, jak i usłudze Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Dlaczego w dziennikach są widoczne błędy połączenia HTTP, a wszystkie moje uwierzytelnianie nie powiodą się?

Upewnij się, że adres https://adnotifications.windowsazure.com jest osiągalny z serwera, na którym działa rozszerzenie NPS.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Dlaczego uwierzytelnianie nie działa, mimo że istnieje ważny certyfikat?

Jeśli poprzedni certyfikat komputera wygasł i wygenerowano nowy certyfikat, należy usunąć wszystkie wygasłe certyfikaty. Wygaśnięcie certyfikatów może spowodować problemy z uruchomieniem rozszerzenia NPS.

Aby sprawdzić, czy masz ważny certyfikat, sprawdź magazyn certyfikatów lokalnego konta komputera przy użyciu programu MMC i upewnij się, że certyfikat nie został przegłosował datę wygaśnięcia. Aby wygenerować nowo prawidłowy certyfikat, uruchom ponownie kroki w sekcji "[Uruchom skrypt programu PowerShell](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Zarządzanie protokołami TLS/SSL i zestawami szyfrowania

Zaleca się wyłączenie lub usunięcie starszych i słabszych mechanizmów szyfrowania, chyba że jest to wymagane przez organizację. Informacje o tym, jak wykonać to zadanie, można znaleźć w artykule [Managing SSL/TLS Protocols and Cipher Suites for AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) (Zarządzanie protokołami SSL/TLS i zestawami szyfrowania dla usług AD FS).

### <a name="additional-troubleshooting"></a>Dodatkowe rozwiązywanie problemów

Dodatkowe wskazówki dotyczące rozwiązywania problemów i możliwe rozwiązania można znaleźć w artykule [Rozwiązywanie komunikatów o błędach z rozszerzenia NPS dla uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Następne kroki

- [Omówienie i konfiguracja serwera zasad sieciowych w systemie Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Konfigurowanie alternatywnych identyfikatorów logowania lub konfigurowanie listy wyjątków dla adresów IP, które nie powinny przeprowadzać weryfikacji dwuetapowej w [opcjach konfiguracji zaawansowanej dla rozszerzenia NPS dla uwierzytelniania wieloskładnikowego](howto-mfa-nps-extension-advanced.md)

- Dowiedz się, jak zintegrować [bramę pulpitu zdalnego](howto-mfa-nps-extension-rdg.md) i [serwery sieci VPN](howto-mfa-nps-extension-vpn.md) przy użyciu rozszerzenia NPS

- [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)
