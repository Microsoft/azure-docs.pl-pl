---
title: 'Azure AD Connect: wymagania wstępne i sprzęt | Microsoft Docs'
description: Ten temat zawiera opis wymagań wstępnych i wymagania sprzętowe Azure AD Connect
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
ms.openlocfilehash: 11d5c1bb133f0aea241fbc55f96ab5f8818e5ed6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518117"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Wymagania wstępne dotyczące programu Azure AD Connect
Ten temat zawiera opis wymagań wstępnych i wymagania sprzętowe Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Przed zainstalowaniem Azure AD Connect
Przed zainstalowaniem Azure AD Connect istnieje kilka rzeczy, które są potrzebne.

### <a name="azure-ad"></a>Azure AD
* Dzierżawa usługi Azure AD. Otrzymujesz jedną z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). Aby zarządzać Azure AD Connect, można użyć jednego z następujących portalów:
  * [Azure Portal](https://portal.azure.com).
  * [Portal Office](https://portal.office.com).  
* [Dodaj i sprawdź domenę](../active-directory-domains-add-azure-portal.md) , która ma być używana w usłudze Azure AD. Jeśli na przykład zamierzasz używać contoso.com dla użytkowników, upewnij się, że ta domena została zweryfikowana i nie tylko używasz domyślnej domeny contoso.onmicrosoft.com.
* Dzierżawa usługi Azure AD zezwala na używanie domyślnych obiektów 50 000. Po zweryfikowaniu domeny limit zostaje zwiększony do obiektów 300 tysięcy pozycji. Jeśli potrzebujesz jeszcze więcej obiektów w usłudze Azure AD, musisz otworzyć zgłoszenie do pomocy technicznej, aby limit został jeszcze bardziej zwiększony. Jeśli potrzebujesz więcej niż 500 000 obiektów, potrzebna jest licencja, taka jak Office 365, Azure AD — wersja Premium lub Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Przygotowywanie danych lokalnych
* Użyj [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) , aby zidentyfikować błędy, takie jak duplikaty i problemy z formatowaniem w katalogu przed synchronizacją do usługi Azure AD i pakietu Office 365.
* Zapoznaj [się z opcjonalnymi funkcjami synchronizacji, które można włączyć w usłudze Azure AD](how-to-connect-syncservice-features.md) , i Oceń, które funkcje należy włączyć.

### <a name="on-premises-active-directory"></a>Lokalna usługa Active Directory
* Wersja schematu usługi AD i poziom funkcjonalności lasu muszą być systemu Windows Server 2003 lub nowszego. Kontrolery domeny mogą uruchamiać dowolną wersję, o ile są spełnione wymagania dotyczące poziomu schematu i lasu.
* Jeśli planujesz używać funkcji **zapisywania zwrotnego haseł**, kontrolery domeny muszą być w systemie Windows Server 2008 R2 lub nowszym.
* Kontroler domeny używany przez usługę Azure AD musi być zapisywalny. Korzystanie z kontrolera RODC (tylko do odczytu) nie jest **obsługiwane** i Azure AD Connect nie są zgodne z żadnym przekierowaniami zapisu.
* Nie jest **obsługiwane** używanie lokalnych lasów/domen przy użyciu "kropek" (nazwa zawiera kropkę ".") Nazwy NetBios.
* Zaleca się [włączenie kosza Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Serwer Azure AD Connect
Serwer Azure AD Connect zawiera krytyczne dane tożsamości. Ważne jest, aby dostęp administracyjny do tego serwera był prawidłowo zabezpieczony, zgodnie z wytycznymi opisanymi w temacie [Zabezpieczanie uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Serwer Azure AD Connect musi być traktowany jako składnik warstwy 0, zgodnie z opisem w [Active Directory modelu warstwy administracyjnej](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).  

Aby dowiedzieć się więcej na temat zabezpieczania środowiska Active Directory, zobacz [najlepsze rozwiązania dotyczące zabezpieczania Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Wymagania wstępne instalacji 

- Azure AD Connect musi być zainstalowany w domenie przyłączonej do systemu Windows Server 2012 lub nowszej. 
- Nie można zainstalować Azure AD Connect na małym lub systemie Windows Server Essentials przed 2019 (obsługiwany jest system Windows Server Essentials 2019). Na serwerze musi być używany system Windows Server Standard lub szybszy.  
- Na serwerze Azure AD Connect musi być zainstalowany pełny graficzny interfejs użytkownika. Instalowanie Azure AD Connect w systemie Windows Server Core nie jest obsługiwane. 
- Na serwerze Azure AD Connect nie może być włączona transkrypcja programu PowerShell zasady grupy, jeśli do zarządzania konfiguracją usług ADFS używasz Kreatora Azure AD Connect. Można włączyć transkrypcję programu PowerShell, jeśli używasz Kreatora Azure AD Connect do zarządzania konfiguracją synchronizacji. 
- Jeśli Active Directory Federation Services jest wdrażana, to: 
    - serwery, na których zainstalowano AD FS lub serwer proxy aplikacji sieci Web, muszą być systemu Windows Server 2012 R2 lub nowszego. Zdalne zarządzanie systemem Windows musi być włączone na tych serwerach na potrzeby instalacji zdalnej. 
    - należy skonfigurować certyfikaty TLS/SSL.  Zobacz [Zarządzanie protokołami SSL/TLS i mechanizmami szyfrowania w celu AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) i [zarządzania certyfikatami ssl w programie AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - należy skonfigurować rozpoznawanie nazw. 
- Jeśli administratorzy globalni mają włączoną usługę MFA, adres URL https://secure.aadcdn.microsoftonline-p.com **musi** znajdować się na liście zaufanych witryn. Zostanie wyświetlony monit o dodanie tej witryny do listy zaufanych witryn, gdy zostanie wyświetlony monit dotyczący wyzwania usługi MFA i nie został dodany przed. Możesz użyć programu Internet Explorer, aby dodać go do zaufanych witryn.  

#### <a name="hardening-your-azure-ad-connect-server"></a>Zabezpieczanie serwera Azure AD Connect 
Firma Microsoft zaleca, aby zabezpieczyć serwer Azure AD Connect, aby zmniejszyć obszar ataków zabezpieczeń dla tego krytycznego składnika środowiska IT. Poniższe zalecenia zapewnią uniknięcie pewnych zagrożeń bezpieczeństwa w organizacji.

- Należy traktować Azure AD Connect tak samo jak kontroler domeny i inne zasoby warstwy 0:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material 
- Dostęp administracyjny do serwera Azure AD Connect należy ograniczyć tylko do administratorów domeny lub innych chronionych grup zabezpieczeń.
- Należy utworzyć [dedykowane konto dla wszystkich pracowników z dostępem uprzywilejowanym](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). Administratorzy nie powinni przeglądać sieci Web, sprawdzać ich poczty e-mail ani codziennych zadań produktywności przy użyciu wysoce uprzywilejowanych kont.
- Należy postępować zgodnie ze wskazówkami podanymi w temacie [Zabezpieczanie uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/security/credentials-protection-and-management/how-to-configure-protected-accounts). 
- Należy upewnić się, że każdy komputer ma unikatowe hasło administratora lokalnego. [Rozwiązanie hasła administratora lokalnego (](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) zależnie od) umożliwia skonfigurowanie unikatowych losowych haseł na każdej stacji roboczej i przechowywanie ich w Active Directory (AD) chronionych przez listę kontroli dostępu. Tylko uprawnieni autoryzowani użytkownicy mogą odczytywać lub żądać resetowania haseł konta administratora lokalnego. Możesz uzyskać dostęp do programu na stacjach roboczych i serwerach z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.). Dodatkowe wskazówki dotyczące pracy ze środowiskiem i dostępem uprzywilejowanym można znaleźć w temacie [standardy operacyjne w oparciu o zasadę czystego źródła](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Należy zaimplementować dedykowane [stacje robocze dostępu uprzywilejowanego (dostępem uprzywilejowanym)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) dla wszystkich pracowników z dostępem uprzywilejowanym do systemów informatycznych w organizacji. 
- Należy postępować zgodnie z poniższymi [wskazówkami](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) , aby zmniejszyć obszar ataków Active Directory środowiska.


### <a name="sql-server-used-by-azure-ad-connect"></a>Program SQL Server używany przez program Azure AD Connect
* Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Domyślnie jest zainstalowana SQL Server 2012 Express LocalDB (wersja uproszczona SQL Server Express). SQL Server Express ma limit rozmiaru 10 GB, który umożliwia zarządzanie około 100 000 obiektów. Aby zarządzać większą ilością obiektów katalogu, należy wskazać Kreator instalacji innej instalacji SQL Server. Typ instalacji SQL Server może mieć wpływ na [wydajność Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* W przypadku korzystania z innej instalacji SQL Server są stosowane następujące wymagania:
  * Azure AD Connect obsługuje wszystkie wersje Microsoft SQL Server z 2012 (z najnowszym dodatkiem Service Pack) do SQL Server 2019. Microsoft Azure SQL Database **nie jest obsługiwana** jako baza danych.
  * Musisz użyć sortowania SQL bez uwzględniania wielkości liter. Te sortowania są identyfikowane za pomocą \_ CI_ w ich nazwie. Nie jest **obsługiwane** użycie sortowania z uwzględnieniem wielkości liter, identyfikowane przez \_ cs_ w ich nazwie.
  * Można korzystać tylko z jednego aparatu synchronizacji na wystąpienie bazy danych SQL. Udostępnianie wystąpienia programu SQL Server za pomocą narzędzia synchronizacji FIM/MIM, narzędzia DirSync lub Azure AD Sync **nie jest obsługiwane** .

### <a name="accounts"></a>Konta
* Konto administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD, z którą chcesz przeprowadzić integrację. To konto musi być **kontem szkoły lub organizacji** i nie może być **konto Microsoft**.
* W przypadku korzystania z [ustawień ekspresowych](reference-connect-accounts-permissions.md#express-settings-installation) lub uaktualniania z narzędzia DirSync wymagane jest posiadanie konta administratora przedsiębiorstwa dla Active Directory lokalnego.
* Jeśli używasz ścieżki instalacji ustawień niestandardowych, masz więcej opcji. Aby uzyskać więcej informacji, zobacz [ustawienia instalacji niestandardowej](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Łączność
* Serwer Azure AD Connect wymaga rozpoznawania nazw DNS dla intranetu i Internetu. Serwer DNS musi być w stanie rozpoznawać nazwy zarówno w lokalnym Active Directory, jak i w punktach końcowych usługi Azure AD.
* Jeśli masz zapory w intranecie i musisz otworzyć porty między serwerami Azure AD Connect i kontrolerami domeny, zobacz [Azure AD Connect portów](reference-connect-ports.md) , aby uzyskać więcej informacji.
* Jeśli Twój serwer proxy lub Zapora ogranicza dostęp do adresów URL, należy otworzyć adresy URL opisane w [adresach URL pakietu Office 365 i w zakresach adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) .
  * Jeśli używasz Microsoft Cloud w Niemczech lub w chmurze Microsoft Azure Government, zobacz [zagadnienia dotyczące wystąpień usługi synchronizacji Azure AD Connect](reference-connect-instances.md) dla adresów URL.
* Azure AD Connect (w wersji 1.1.614.0 i After) domyślnie używa protokołu TLS 1,2 do szyfrowania komunikacji między aparatem synchronizacji i usługą Azure AD. Jeśli protokół TLS 1,2 nie jest dostępny w podstawowym systemie operacyjnym, Azure AD Connect stopniowo powraca do starszych protokołów (TLS 1,1 i TLS 1,0).
* W wersjach wcześniejszych niż 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1,0 do szyfrowania komunikacji między aparatem synchronizacji i usługą Azure AD. Aby zmienić protokół TLS 1,2, wykonaj kroki opisane w temacie [Włączanie protokołu tls 1,2 dla Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Jeśli używasz wychodzącego serwera proxy do łączenia się z Internetem, do Kreatora instalacji należy dodać następujące ustawienie w pliku **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** i Azure AD Connect synchronizacji, aby można było łączyć się z Internetem i usługą Azure AD. Ten tekst musi być wprowadzony w dolnej części pliku. W tym kodzie &lt; ProxyAddress &gt; reprezentuje rzeczywisty adres IP lub nazwę hosta serwera proxy.

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

* Jeśli serwer proxy wymaga uwierzytelnienia, [konto usługi](reference-connect-accounts-permissions.md#adsync-service-account) musi znajdować się w domenie i należy użyć ścieżki instalacji dostosowane ustawienia, aby określić [niestandardowe konto usługi](how-to-connect-install-custom.md#install-required-components). Wymagana jest również inna zmiana machine.config. W przypadku tej zmiany w machine.config Kreator instalacji i aparat synchronizacji odpowiadają na żądania uwierzytelniania z serwera proxy. Na wszystkich stronach Kreatora instalacji z wyłączeniem strony **Konfiguracja** są używane poświadczenia zalogowanego użytkownika. Na stronie **Konfigurowanie** na końcu Kreatora instalacji, kontekst jest przełączany do [konta usługi](reference-connect-accounts-permissions.md#adsync-service-account) , które zostało utworzone przez użytkownika. Sekcja machine.config powinna wyglądać następująco.

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

* Gdy Azure AD Connect wysyła żądanie sieci Web do usługi Azure AD w ramach synchronizacji katalogów, odpowiedź usługi Azure AD może potrwać do 5 minut. W przypadku serwerów proxy często istnieje konfiguracja limitu czasu bezczynności połączenia. Upewnij się, że konfiguracja jest równa co najmniej 6 minut.

Aby uzyskać więcej informacji, zobacz MSDN dotyczący [domyślnego elementu proxy](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Aby uzyskać więcej informacji w przypadku problemów z łącznością, zobacz [Rozwiązywanie problemów z łącznością](tshoot-connect-connectivity.md).

### <a name="other"></a>Inne
* Opcjonalne: konto użytkownika testowego, aby zweryfikować synchronizację.

## <a name="component-prerequisites"></a>Wymagania wstępne dotyczące składników
### <a name="powershell-and-net-framework"></a>PowerShell i .NET Framework
Azure AD Connect zależy od programu Microsoft PowerShell i .NET Framework 4.5.1. Wymagana jest ta wersja lub nowsza wersja zainstalowana na serwerze. W zależności od wersji systemu Windows Server wykonaj następujące czynności:

* 2012R2 systemu Windows Server
  * Program Microsoft PowerShell jest instalowany domyślnie. Nie jest wymagana żadna akcja.
  * .NET Framework 4.5.1 i nowsze wersje są oferowane przez Windows Update. Upewnij się, że zainstalowano najnowsze aktualizacje systemu Windows Server w panelu sterowania.
* Windows Server 2012
  * Najnowsza wersja programu Microsoft PowerShell jest dostępna w **systemie Windows Management Framework 4,0**, dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 i nowsze wersje są dostępne w [Centrum pobierania Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Włącz protokół TLS 1,2 dla Azure AD Connect
W wersjach wcześniejszych niż 1.1.614.0 Azure AD Connect domyślnie używa protokołu TLS 1,0 do szyfrowania komunikacji między serwerem aparatu synchronizacji i usługą Azure AD. Można to zmienić, konfigurując aplikacje platformy .NET do używania protokołu TLS 1,2 domyślnie na serwerze programu. Więcej informacji na temat protokołu TLS 1,2 można znaleźć w [poradniku zabezpieczeń firmy Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Upewnij się, że masz zainstalowaną poprawkę .NET 4.5.1 dla danego systemu operacyjnego, zobacz [Poradnik zabezpieczeń firmy Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Ta poprawka lub nowsza wersja została już zainstalowana na serwerze.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319 "schusestrongcrypto we" = DWORD: 00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
