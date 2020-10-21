---
title: 'Azure AD Connect: wymagania wstępne i sprzęt | Microsoft Docs'
description: W tym artykule opisano warunki wstępne i wymagania sprzętowe Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca2190079cb97e37318bd1c6a32dfb2b9b309a8d
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276943"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Wymagania wstępne dotyczące programu Azure AD Connect
W tym artykule opisano wymagania wstępne i wymagania sprzętowe dotyczące programu Azure Active Directory (Azure AD) Connect.

## <a name="before-you-install-azure-ad-connect"></a>Przed zainstalowaniem Azure AD Connect
Przed zainstalowaniem Azure AD Connect istnieje kilka rzeczy, które są potrzebne.

### <a name="azure-ad"></a>Azure AD
* Potrzebna jest dzierżawa usługi Azure AD. Otrzymujesz jedną z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). Aby zarządzać Azure AD Connect, można użyć jednego z następujących portalów:
  * [Azure Portal](https://portal.azure.com).
  * [Portal Office](https://portal.office.com).
* [Dodaj i sprawdź domenę](../fundamentals/add-custom-domain.md) , która ma być używana w usłudze Azure AD. Jeśli na przykład zamierzasz używać contoso.com dla użytkowników, upewnij się, że ta domena została zweryfikowana i nie używasz tylko domyślnej domeny contoso.onmicrosoft.com.
* Dzierżawa usługi Azure AD zezwala domyślnie na 50 000 obiektów. Po zweryfikowaniu domeny limit rośnie do 300 000 obiektów. Jeśli potrzebujesz jeszcze więcej obiektów w usłudze Azure AD, otwórz sprawę pomocy technicznej, aby limit został jeszcze bardziej zwiększony. Jeśli potrzebujesz więcej niż 500 000 obiektów, potrzebujesz licencji, takiej jak Microsoft 365, Azure AD — wersja Premium lub Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Przygotowywanie danych lokalnych
* Użyj [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) , aby zidentyfikować błędy, takie jak duplikaty i problemy z formatowaniem w katalogu przed synchronizacją do usługi Azure AD i Microsoft 365.
* Zapoznaj [się z opcjonalnymi funkcjami synchronizacji, które można włączyć w usłudze Azure AD](how-to-connect-syncservice-features.md), i Oceń, które funkcje należy włączyć.

### <a name="on-premises-active-directory"></a>Lokalna usługa Active Directory
* Wersja schematu Active Directory i poziom funkcjonalności lasu muszą być systemu Windows Server 2003 lub nowszego. Kontrolery domeny mogą uruchamiać dowolną wersję, o ile są spełnione wymagania dotyczące wersji schematu i poziomu lasu.
* Jeśli planujesz używać funkcji *zapisywania zwrotnego haseł*, kontrolery domeny muszą być w systemie Windows Server 2008 R2 lub nowszym.
* Kontroler domeny używany przez usługę Azure AD musi być zapisywalny. Korzystanie z kontrolera domeny tylko do odczytu (RODC) *nie jest obsługiwane*, a Azure AD Connect nie jest zgodna z przekierowaniami zapisu.
* Używanie lokalnych lasów lub domen przy użyciu "kropkowane" (nazwa zawiera kropkę ".") Nazwy NetBIOS *nie są obsługiwane*.
* Zalecamy [włączenie kosza Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>Zasady wykonywania programu PowerShell
Azure Active Directory Connect uruchamia podpisane skrypty programu PowerShell w ramach instalacji. Upewnij się, że zasady wykonywania programu PowerShell umożliwią uruchamianie skryptów.

Zalecane zasady wykonywania podczas instalacji to "RemoteSigned".

Aby uzyskać więcej informacji na temat ustawiania zasad wykonywania programu PowerShell, zobacz [Set-executionpolicy](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7).


### <a name="azure-ad-connect-server"></a>Serwer Azure AD Connect
Serwer Azure AD Connect zawiera krytyczne dane tożsamości. Ważne jest, aby dostęp administracyjny do tego serwera był prawidłowo zabezpieczony. Postępuj zgodnie z wytycznymi w temacie [Zabezpieczanie uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Serwer Azure AD Connect musi być traktowany jako składnik warstwy 0, zgodnie z opisem w [Active Directory modelu warstwy administracyjnej](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 

Aby dowiedzieć się więcej na temat zabezpieczania środowiska Active Directory, zobacz [najlepsze rozwiązania dotyczące zabezpieczania Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Wymagania wstępne instalacji

- Azure AD Connect musi być zainstalowana w systemie Windows Server 2012 lub nowszym przyłączonym do domeny. 
- Nie można zainstalować Azure AD Connect na małym lub systemie Windows Server Essentials przed 2019 (obsługiwany jest system Windows Server Essentials 2019). Na serwerze musi być używany system Windows Server Standard lub szybszy. 
- Na serwerze Azure AD Connect musi być zainstalowany pełny graficzny interfejs użytkownika. Instalowanie Azure AD Connect w systemie Windows Server Core nie jest obsługiwane. 
- Aby Azure AD Connect można było zarządzać konfiguracją Active Directory Federation Services (AD FS), na serwerze Azure AD Connect nie może być zasady grupy włączona funkcja programu PowerShell. Można włączyć transkrypcję programu PowerShell, jeśli do zarządzania konfiguracją synchronizacji używasz Kreatora Azure AD Connect. 
- Jeśli AD FS jest wdrażana: 
    - Serwery, na których zainstalowano AD FS lub serwer proxy aplikacji sieci Web, muszą być systemu Windows Server 2012 R2 lub nowszego. Zdalne zarządzanie systemem Windows musi być włączone na tych serwerach na potrzeby instalacji zdalnej. 
    - Należy skonfigurować certyfikaty TLS/SSL. Aby uzyskać więcej informacji, zobacz [Zarządzanie protokołami SSL/TLS i mechanizmami szyfrowania w celu AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) i [zarządzania certyfikatami ssl w programie AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Należy skonfigurować rozpoznawanie nazw. 
- Jeśli administratorzy globalni mają włączoną usługę MFA, adres URL https://secure.aadcdn.microsoftonline-p.com *musi* znajdować się na liście zaufanych witryn. Zostanie wyświetlony monit o dodanie tej witryny do listy zaufanych witryn, gdy zostanie wyświetlony monit dotyczący wyzwania usługi MFA i nie został on dodany wcześniej. Możesz użyć programu Internet Explorer, aby dodać go do zaufanych witryn.

#### <a name="harden-your-azure-ad-connect-server"></a>Ograniczanie funkcjonalności serwera Azure AD Connect 
Zaleca się, aby zabezpieczyć serwer Azure AD Connect, aby zmniejszyć obszar ataków zabezpieczeń dla tego krytycznego składnika środowiska IT. Poniższe zalecenia ułatwią uniknięcie pewnych zagrożeń bezpieczeństwa w organizacji.

- Traktuj Azure AD Connect tak samo jak kontroler domeny i inne zasoby warstwy 0. Aby uzyskać więcej informacji, zobacz [Active Directory model warstwy administracyjnej](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Ogranicz dostęp administracyjny do serwera Azure AD Connect tylko do administratorów domeny lub innych chronionych grup zabezpieczeń.
- Utwórz [dedykowane konto dla wszystkich pracowników z dostępem uprzywilejowanym](/windows-server/identity/securing-privileged-access/securing-privileged-access). Administratorzy nie powinni przeglądać sieci Web, sprawdzać ich pocztą e-mail ani codziennych zadań produkcyjnych przy użyciu wysoce uprzywilejowanych kont.
- Postępuj zgodnie ze wskazówkami podanymi w temacie [Zabezpieczanie uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Odmów używania uwierzytelniania NTLM z serwerem AADConnect. Oto kilka sposobów, aby to zrobić: [ograniczenia uwierzytelniania NTLM na serwerze AADConnect](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) i [ograniczania uwierzytelniania NTLM w domenie](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Upewnij się, że każdy komputer ma unikatowe hasło administratora lokalnego. Aby uzyskać więcej informacji, zobacz [rozwiązanie hasła administratora lokalnego (](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) zależnie od) umożliwia skonfigurowanie unikatowych losowych haseł na każdej stacji roboczej i przechowywanie ich w Active Directory chronionych przez listę kontroli dostępu. Tylko uprawnieni autoryzowani użytkownicy mogą odczytywać lub żądać resetowania haseł konta administratora lokalnego. Możesz uzyskać dostęp do programu na stacjach roboczych i serwerach z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.). Dodatkowe wskazówki dotyczące działania środowiska z innymi i stacjami roboczymi dostępu uprzywilejowanego (dostępem uprzywilejowanym) można znaleźć w temacie [standardy operacyjne na podstawie zasad czystego źródła](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Zaimplementuj dedykowane [stacje robocze dostępu uprzywilejowanego](/windows-server/identity/securing-privileged-access/privileged-access-workstations) dla wszystkich pracowników z dostępem uprzywilejowanym do systemów informatycznych w organizacji. 
- Postępuj zgodnie z tymi [dodatkowymi wskazówkami](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) , aby zmniejszyć obszar ataków na środowisko Active Directory.


### <a name="sql-server-used-by-azure-ad-connect"></a>Program SQL Server używany przez program Azure AD Connect
* Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Domyślnie jest zainstalowana SQL Server 2012 Express LocalDB (wersja uproszczona SQL Server Express). SQL Server Express ma limit rozmiaru 10 GB, który umożliwia zarządzanie około 100 000 obiektów. Jeśli potrzebujesz zarządzać większą ilością obiektów katalogu, wskaż Kreatora instalacji inną instalację SQL Server. Typ instalacji SQL Server może mieć wpływ na [wydajność Azure AD Connect](./plan-connect-performance-factors.md#sql-database-factors).
* W przypadku korzystania z innej instalacji SQL Server są stosowane następujące wymagania:
  * Azure AD Connect obsługuje wszystkie wersje SQL Server z 2012 (z najnowszym dodatkiem Service Pack) do SQL Server 2019. Azure SQL Database *nie jest obsługiwana* jako baza danych.
  * Musisz użyć sortowania SQL bez uwzględniania wielkości liter. Te sortowania są identyfikowane za pomocą \_ CI_ w ich nazwie. Użycie sortowania uwzględniającego wielkość liter identyfikowane przez \_ cs_ w ich nazwie *nie jest obsługiwane*.
  * Można mieć tylko jeden aparat synchronizacji dla każdego wystąpienia SQL. Udostępnianie wystąpienia programu SQL Server za pomocą narzędzia synchronizacji FIM/MIM, narzędzia DirSync lub Azure AD Sync *nie jest obsługiwane*.

### <a name="accounts"></a>Konta
* Musisz mieć konto administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD, z którą chcesz przeprowadzić integrację. To konto musi być *kontem szkoły lub organizacji* i nie może być *konto Microsoft*.
* W przypadku korzystania z [ustawień ekspresowych](reference-connect-accounts-permissions.md#express-settings-installation) lub uaktualniania z narzędzia DirSync wymagane jest posiadanie konta administratora przedsiębiorstwa dla Active Directory lokalnego.
* Jeśli używasz ścieżki instalacji ustawień niestandardowych, masz więcej opcji. Aby uzyskać więcej informacji, zobacz [ustawienia instalacji niestandardowej](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Łączność
* Serwer Azure AD Connect wymaga rozpoznawania nazw DNS dla intranetu i Internetu. Serwer DNS musi być w stanie rozpoznawać nazwy zarówno w lokalnym Active Directory, jak i w punktach końcowych usługi Azure AD.
* Jeśli masz zapory w intranecie i musisz otworzyć porty między serwerami Azure AD Connect i kontrolerami domeny, zobacz [Azure AD Connect portów](reference-connect-ports.md) , aby uzyskać więcej informacji.
* Jeśli Twój serwer proxy lub Zapora ogranicza dostęp do adresów URL, należy otworzyć adresy URL opisane w [adresach URL pakietu Office 365 i w zakresach adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) .
  * Jeśli używasz chmury firmy Microsoft w Niemczech lub Microsoft Azure Government w chmurze, zobacz [zagadnienia dotyczące wystąpień usługi synchronizacji Azure AD Connect](reference-connect-instances.md) dla adresów URL.
* Azure AD Connect (w wersji 1.1.614.0 i After) domyślnie używa protokołu TLS 1,2 do szyfrowania komunikacji między aparatem synchronizacji i usługą Azure AD. Jeśli protokół TLS 1,2 nie jest dostępny w podstawowym systemie operacyjnym, Azure AD Connect stopniowo powraca do starszych protokołów (TLS 1,1 i TLS 1,0).
* W wersjach wcześniejszych niż 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1,0 do szyfrowania komunikacji między aparatem synchronizacji i usługą Azure AD. Aby zmienić protokół TLS 1,2, wykonaj kroki opisane w temacie [Włączanie protokołu tls 1,2 dla Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Jeśli używasz wychodzącego serwera proxy do łączenia się z Internetem, do Kreatora instalacji należy dodać następujące ustawienie w pliku **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** i Azure AD Connect synchronizacji, aby można było łączyć się z Internetem i usługą Azure AD. Ten tekst musi być wprowadzony w dolnej części pliku. W tym kodzie * &lt; ProxyAddress &gt; * reprezentuje rzeczywisty adres IP lub nazwę hosta serwera proxy.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Jeśli serwer proxy wymaga uwierzytelnienia, [konto usługi](reference-connect-accounts-permissions.md#adsync-service-account) musi znajdować się w domenie. Użyj ścieżki instalacji dostosowane ustawienia, aby określić [niestandardowe konto usługi](how-to-connect-install-custom.md#install-required-components). Wymagana jest również inna zmiana machine.config. W przypadku tej zmiany w machine.config Kreator instalacji i aparat synchronizacji odpowiadają na żądania uwierzytelniania z serwera proxy. Na wszystkich stronach Kreatora instalacji z wyłączeniem strony **Konfiguracja** są używane poświadczenia zalogowanego użytkownika. Na stronie **Konfigurowanie** na końcu Kreatora instalacji kontekst jest przełączany do utworzonego [konta usługi](reference-connect-accounts-permissions.md#adsync-service-account) . Sekcja machine.config powinna wyglądać następująco:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Jeśli konfiguracja serwera proxy jest wykonywana w istniejącej konfiguracji, należy ponownie uruchomić **usługę synchronizacji Microsoft Azure AD** , aby Azure AD Connect odczytać konfigurację serwera proxy i zaktualizować zachowanie. 
* Gdy Azure AD Connect wysyła żądanie sieci Web do usługi Azure AD w ramach synchronizacji katalogów, odpowiedź usługi Azure AD może potrwać do 5 minut. W przypadku serwerów proxy często istnieje możliwość skonfigurowania limitu czasu bezczynności połączenia. Upewnij się, że konfiguracja jest równa co najmniej 6 minut.

Aby uzyskać więcej informacji, zobacz MSDN dotyczący [domyślnego elementu proxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
Aby uzyskać więcej informacji w przypadku problemów z łącznością, zobacz [Rozwiązywanie problemów z łącznością](tshoot-connect-connectivity.md).

### <a name="other"></a>Inne
Opcjonalne: Użyj konta użytkownika testowego, aby zweryfikować synchronizację.

## <a name="component-prerequisites"></a>Wymagania wstępne dotyczące składników
### <a name="powershell-and-net-framework"></a>PowerShell i .NET Framework
Azure AD Connect zależy od programu Microsoft PowerShell i .NET Framework 4.5.1. Wymagana jest ta wersja lub nowsza wersja zainstalowana na serwerze. W zależności od wersji systemu Windows Server wykonaj następujące czynności:

* Windows Server 2012 z dodatkiem R2
  * Program Microsoft PowerShell jest instalowany domyślnie. Nie jest wymagana żadna akcja.
  * .NET Framework 4.5.1 i nowsze wersje są oferowane przez Windows Update. Upewnij się, że zainstalowano najnowsze aktualizacje systemu Windows Server w panelu sterowania.
* Windows Server 2012
  * Najnowsza wersja programu Microsoft PowerShell jest dostępna w systemie Windows Management Framework 4,0, dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 i nowsze wersje są dostępne w [Centrum pobierania Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Włącz protokół TLS 1,2 dla Azure AD Connect
W wersjach wcześniejszych niż 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1,0 do szyfrowania komunikacji między serwerem aparatu synchronizacji i usługą Azure AD. Aplikacje platformy .NET można skonfigurować tak, aby domyślnie używały protokołu TLS 1,2 na serwerze. Aby uzyskać więcej informacji na temat protokołu TLS 1,2, zobacz [Poradnik zabezpieczeń firmy Microsoft 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Upewnij się, że masz zainstalowaną poprawkę .NET 4.5.1 dla danego systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Poradnik zabezpieczeń firmy Microsoft 2960358](/security-updates/SecurityAdvisories/2015/2960358). Ta poprawka lub nowsza wersja została już zainstalowana na serwerze.

1. Dla wszystkich systemów operacyjnych Ustaw ten klucz rejestru i uruchom ponownie serwer.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Jeśli chcesz również włączyć protokół TLS 1,2 między serwerem aparatu synchronizacji i SQL Server zdalnym, upewnij się, że wymagane wersje są zainstalowane do [obsługi protokołu TLS 1,2 dla Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Wymagania wstępne dotyczące instalacji i konfiguracji federacji
### <a name="windows-remote-management"></a>Windows Remote Management
W przypadku używania Azure AD Connect do wdrażania AD FS lub serwera proxy aplikacji sieci Web (WAP) należy sprawdzić następujące wymagania:

* Jeśli serwer docelowy jest przyłączony do domeny, upewnij się, że zdalne zarządzanie systemem Windows jest włączone.
  * W oknie wiersza polecenia programu PowerShell z podwyższonym poziomem uprawnień Użyj polecenia `Enable-PSRemoting –force` .
* Jeśli serwer docelowy jest maszyną WAP, która nie jest przyłączona do domeny, istnieje kilka dodatkowych wymagań:
  * Na maszynie docelowej (komputer WAP):
    * Upewnij się, że usługa Windows Remote Management/WS-Management (WinRM) jest uruchomiona za pośrednictwem przystawki usługi.
    * W oknie wiersza polecenia programu PowerShell z podwyższonym poziomem uprawnień Użyj polecenia `Enable-PSRemoting –force` .
  * Na komputerze, na którym uruchomiony jest Kreator (Jeśli komputer docelowy nie jest przyłączony do domeny lub jest domeną niezaufaną):
    * W oknie wiersza polecenia programu PowerShell z podwyższonym poziomem uprawnień Użyj polecenia `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * W Menedżerze serwera:
      * Dodaj hosta WAP w strefie DMZ do puli maszyn. W Menedżerze serwera wybierz pozycję **Zarządzaj**  >  **Dodaj serwery**, a następnie użyj karty **DNS** .
      * Na karcie **Menedżer serwera wszystkie serwery** kliknij prawym przyciskiem myszy serwer WAP i wybierz polecenie **Zarządzaj jako**. Wprowadź poświadczenia lokalne (nie domeny) dla komputera WAP.
      * Aby sprawdzić poprawność łączności zdalnego programu PowerShell, na karcie **Menedżer serwera wszystkie serwery** kliknij prawym przyciskiem myszy serwer WAP i wybierz pozycję **Windows PowerShell**. Zdalna sesja programu PowerShell powinna zostać otwarta w celu zapewnienia, że można nawiązać zdalne sesje programu PowerShell.

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatu TLS/SSL
* Zalecamy używanie tego samego certyfikatu TLS/SSL między wszystkimi węzłami farmy AD FS i wszystkimi serwerami proxy aplikacji sieci Web.
* Certyfikat musi być certyfikatem x509.
* Możesz użyć certyfikatu z podpisem własnym na serwerach federacyjnych w środowisku laboratorium testowego. W środowisku produkcyjnym zalecamy uzyskanie certyfikatu z publicznego urzędu certyfikacji.
  * Jeśli używasz certyfikatu, który nie jest publicznie zaufany, upewnij się, że certyfikat zainstalowany na każdym serwerze proxy aplikacji sieci Web jest zaufany na serwerze lokalnym i na wszystkich serwerach federacyjnych.
* Tożsamość certyfikatu musi być zgodna z nazwą usługi federacyjnej (na przykład sts.contoso.com).
  * Tożsamość jest rozszerzeniem alternatywnej nazwy podmiotu (SAN) typu dNSName lub, jeśli nie ma żadnych wpisów sieci SAN, nazwa podmiotu zostanie określona jako nazwa pospolita.
  * W certyfikacie można podać wiele wpisów sieci SAN, ponieważ jeden z nich jest zgodny z nazwą usługi federacyjnej.
  * Jeśli planujesz używać Workplace Join, wymagana jest dodatkowa sieć SAN z wartością **enterpriseregistration.** a następnie sufiks głównej nazwy użytkownika (UPN) organizacji, na przykład enterpriseregistration.contoso.com.
* Certyfikaty oparte na kluczach nowej generacji interfejsu CryptoAPI (CNG) i dostawcy magazynu kluczy (KSP) nie są obsługiwane. W związku z tym należy użyć certyfikatu opartego na dostawcy usług kryptograficznych (CSP), a nie na dostawcy magazynu kluczy.
* Obsługiwane są certyfikaty z użyciem symboli wieloznacznych.

### <a name="name-resolution-for-federation-servers"></a>Rozpoznawanie nazw dla serwerów federacyjnych
* Skonfiguruj rekordy DNS dla nazwy AD FS (na przykład sts.contoso.com) dla intranetu (wewnętrznego serwera DNS) i ekstranetu (publiczna usługa DNS za pośrednictwem rejestratora domen). W przypadku rekordu DNS intranet upewnij się, że używasz rekordów, a nie rekordów CNAME. Aby uwierzytelnianie systemu Windows działało prawidłowo z komputera przyłączonego do domeny, wymagane jest użycie rekordów.
* Jeśli wdrażasz więcej niż jeden serwer AD FS lub serwera proxy aplikacji sieci Web, upewnij się, że skonfigurowano moduł równoważenia obciążenia i że rekordy DNS dla nazwy AD FS (na przykład sts.contoso.com) wskazują usługę równoważenia obciążenia.
* Aby zintegrowane uwierzytelnianie systemu Windows działało w przypadku aplikacji przeglądarki przy użyciu programu Internet Explorer w intranecie, należy się upewnić, że nazwa AD FS (na przykład sts.contoso.com) jest dodawana do strefy intranetowej w programie Internet Explorer. To wymaganie można kontrolować za pośrednictwem zasady grupy i wdrażane na wszystkich komputerach przyłączonych do domeny.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect składniki pomocnicze
Azure AD Connect instaluje następujące składniki na serwerze, na którym zainstalowano Azure AD Connect. Ta lista dotyczy podstawowej instalacji ekspresowej. Jeśli zdecydujesz się na użycie innego SQL Server na stronie **Instalowanie usług synchronizacji** , program SQL Express LocalDB nie jest zainstalowany lokalnie.

* Azure AD Connect Health
* Narzędzia wiersza polecenia Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Klient natywny Microsoft SQL Server 2012
* Pakiet redystrybucji Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Wymagania sprzętowe Azure AD Connect
W poniższej tabeli przedstawiono minimalne wymagania dotyczące komputera synchronizacji Azure AD Connect.

| Liczba obiektów w Active Directory | Procesor CPU | Memory (Pamięć) | Rozmiar dysku twardego |
| --- | --- | --- | --- |
| Mniej niż 10 000 |1,6 GHz |4 GB |70 GB |
| 10000 – 50000 |1,6 GHz |4 GB |70 GB |
| 50 000 – 100000 |1,6 GHz |16 GB |100 GB |
| W przypadku 100 000 lub więcej obiektów wymagana jest pełna wersja SQL Server | | | |
| 100000 — 300000 |1,6 GHz |32 GB |300 GB |
| 300000 – 600 000 |1,6 GHz |32 GB |450 GB |
| Więcej niż 600 000 |1,6 GHz |32 GB |500 GB |

Minimalne wymagania dotyczące komputerów z systemami AD FS lub serwerami proxy aplikacji sieci Web:

* Procesor CPU: dwurdzeniowy 1,6 GHz lub szybszy
* Pamięć: 2 GB lub więcej
* Maszyna wirtualna platformy Azure: Konfiguracja a2 lub nowsza

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).