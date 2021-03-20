---
title: Historia wydania agenta ochrony haseł — Azure Active Directory
description: Dokumenty wersja wydania i zachowanie historia zmian zachowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ad7199360ca0acc8674f7a4e34bd206f8b335f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648770"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historia wersji agenta ochrony haseł usługi Azure AD

## <a name="121720"></a>1.2.172.0

Data wydania: 22 lutego 2021

To prawie dwa lata od momentu wydania wersji zaogólnodostępnej lokalnych agentów ochrony hasła usługi Azure AD. Dostępna jest nowa aktualizacja — Zobacz poniższe opisy zmian. Dziękujemy za wszystkich użytkowników, którzy otrzymali nam opinię na temat produktu. 

* Agent kontrolera domeny i oprogramowanie agenta serwera proxy wymagają teraz zainstalowania programu .NET 4.7.2.
  * Jeśli program .NET 4.7.2 nie jest jeszcze zainstalowany, Pobierz i uruchom Instalatora, który znajduje [się w instalatorze offline usługi .NET Framework 4.7.2 dla systemu Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Moduł AzureADPasswordProtection PowerShell jest teraz także instalowany przez oprogramowanie agenta kontrolera domeny.
* Dodano dwa nowe polecenia cmdlet programu PowerShell powiązane z kondycją: Test-AzureADPasswordProtectionDCAgent i test-AzureADPasswordProtectionProxy.
* Biblioteka DLL filtru haseł agenta AzureADPasswordProtection kontrolera domeny zostanie teraz załadowana i uruchomiona na maszynach, na których lsass.exe jest skonfigurowany do uruchamiania w trybie PPL.
* Poprawka błędu algorytmu hasła, w przypadku którego dozwolone są zabronione hasła mniej niż pięć znaków, które mają być niepoprawnie akceptowane.
  * Ta usterka ma zastosowanie tylko wtedy, gdy skonfigurowano zasady minimalnej długości hasła dla lokalnego usługi AD, aby zezwalało na używanie mniej niż pięciu znaków haseł w pierwszym miejscu.
* Inne poprawki drobnej usterki.

Nowe Instalatory automatycznie uaktualniają starsze wersje oprogramowania. Jeśli na pojedynczym komputerze zainstalowano zarówno agenta kontrolera domeny, jak i oprogramowanie serwera proxy (zalecane tylko w przypadku środowisk testowych), należy przeprowadzić uaktualnienie jednocześnie.

Jest on obsługiwany do uruchamiania starszych i nowszych wersji agenta i oprogramowania serwera proxy w domenie lub lesie, chociaż zalecamy uaktualnienie wszystkich agentów do najnowszej wersji. Obsługiwane jest dowolne porządkowanie uaktualnień agentów — nowi agenci DC mogą komunikować się za poorednictwem starszych agentów proxy, a starsi agenci kontrolera domeny mogą komunikować się za poorednictwem nowszych agentów proxy.

## <a name="121250"></a>1.2.125.0

Data wydania: 22 marca 2019

* Napraw drobne błędy pisowni w komunikatach dziennika zdarzeń
* Aktualizuj umowę EULA do końcowej wersji ogólnej dostępności

> [!NOTE]
> Kompilacja 1.2.125.0 to ogólna kompilacja dostępności. Podziękowanie do każdego z nich otrzymasz opinię na temat produktu!

## <a name="121160"></a>1.2.116.0

Data wydania: 3/13/2019

* Polecenia cmdlet Get-AzureADPasswordProtectionProxy i Get-AzureADPasswordProtectionDCAgent teraz raportują wersję oprogramowania i bieżącą dzierżawę platformy Azure z następującymi ograniczeniami:
  * Wersja oprogramowania i dane dzierżawy platformy Azure są dostępne tylko dla agentów DC i serwerów proxy z systemem w wersji 1.2.116.0 lub nowszej.
  * Dane dzierżawy platformy Azure mogą nie zostać zgłoszone, dopóki nie nastąpiła ponowna rejestracja (lub odnowienie) serwera proxy lub lasu.
* Usługa serwera proxy wymaga teraz zainstalowania programu .NET 4,7.
  * Jeśli program .NET 4,7 nie jest jeszcze zainstalowany, Pobierz i uruchom Instalatora, który znajduje [się w instalatorze offline programu .NET Framework 4,7 dla systemu Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * W systemach Server Core może być konieczne przekazanie flagi/q do Instalatora programu .NET 4,7 w celu uzyskania sukcesu.
* Usługa serwera proxy obsługuje teraz automatyczne uaktualnianie. Automatyczne uaktualnianie używa usługi Microsoft Azure AD Connect Agent Aktualizator, która jest instalowana obok usługi proxy. Automatyczne uaktualnianie jest domyślnie włączone.
* Automatyczne uaktualnianie można włączyć lub wyłączyć za pomocą polecenia cmdlet Set-AzureADPasswordProtectionProxyConfiguration. Bieżące ustawienie można zbadać przy użyciu polecenia cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* Nazwa pliku binarnego usługi dla usługi agenta kontrolera domeny została zmieniona na AzureADPasswordProtectionDCAgent.exe.
* Nazwa pliku binarnego usługi dla usługi serwera proxy została zmieniona na AzureADPasswordProtectionProxy.exe. W przypadku korzystania z zapory innej firmy może być konieczne zmodyfikowanie reguł zapory.
  * Uwaga: Jeśli plik konfiguracyjny serwera proxy HTTP był używany w poprzedniej instalacji serwera proxy, należy zmienić jego nazwę (z *proxyservice.exe.config* na *AzureADPasswordProtectionProxy.exe.config*) po tym uaktualnieniu.
* Wszystkie testy funkcji ograniczone przez czas zostały usunięte z agenta kontrolera domeny.
* Poprawki i ulepszenia rejestrowania drobnych usterek.

## <a name="12650"></a>1.2.65.0

Data wydania: 1 lutego 2019

Wprowadzane

* Agent i serwer proxy usługi DC są teraz obsługiwane w trybie Server Core. Wymagania systemu operacyjnego Mininimum nie zostały zmienione przed: system Windows Server 2012 dla agentów DC i system Windows Server 2012 R2 dla serwerów proxy.
* Polecenia cmdlet Register-AzureADPasswordProtectionProxy i Register-AzureADPasswordProtectionForest obsługują teraz tryby uwierzytelniania platformy Azure oparte na kodzie na urządzeniu.
* Get-AzureADPasswordProtectionDCAgent polecenie cmdlet zignoruje zniekształcona i/lub nieprawidłowe punkty połączenia usługi. Ta zmiana eliminuje usterkę, w której kontrolery domeny czasami będą widoczne w danych wyjściowych wiele razy.
* Get-AzureADPasswordProtectionSummaryReport polecenie cmdlet zignoruje zniekształcona i/lub nieprawidłowe punkty połączenia usługi. Ta zmiana eliminuje usterkę, w której kontrolery domeny czasami będą widoczne w danych wyjściowych wiele razy.
* Moduł serwera proxy programu PowerShell jest teraz zarejestrowany w usłudze%ProgramFiles%\WindowsPowerShell\Modules. Zmienna środowiskowa PSModulePath komputera nie jest już modyfikowana.
* Dodano nowe polecenie cmdlet Get-AzureADPasswordProtectionProxy w celu ułatwienia odnajdywania zarejestrowanych serwerów proxy w lesie lub domenie.
* Agent DC używa nowego folderu w udziale Sysvol do replikowania zasad haseł i innych plików.

   Stara Lokalizacja folderu:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Lokalizacja nowego folderu:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Ta zmiana została wprowadzona w celu uniknięcia fałszywych "oddzielonych obiektów zasad grupy").

   > [!NOTE]
   > Migracja ani udostępnianie danych nie będą wykonywane między starym folderem a nowym folderem. Starsze wersje agentów DC będą nadal używać starej lokalizacji do momentu uaktualnienia do tej wersji lub nowszej. Gdy wszyscy agenci kontrolera domeny mają uruchomioną wersję 1.2.65.0 lub nowszą, stary folder SYSVOL może zostać ręcznie usunięty.

* Agent kontrolera domeny i usługa serwera proxy będą teraz wykrywać i usuwać zniekształcona kopie odpowiednich punktów połączenia usługi.
* Każdy agent DC będzie okresowo usuwać zniekształcona i przestarzałe punkty połączenia usługi w swojej domenie dla obu punktów połączenia z agentem i serwerem proxy. Punkty połączenia z agentem i serwerem proxy są uważane za przestarzałe, jeśli sygnatura czasowa pulsu jest starsza niż siedem dni.
* Agent DC będzie teraz odnawiać certyfikat lasu zgodnie z wymaganiami.
* Usługa proxy będzie teraz odnawiać certyfikat serwera proxy zgodnie z wymaganiami.
* Aktualizacje algorytmu walidacji hasła: Lista globalna wykluczonych haseł i lista wykluczonych haseł specyficznych dla klienta (jeśli zostały skonfigurowane) są łączone przed walidacjami haseł. Dane hasło mogą teraz zostać odrzucone (tylko Niepowodzenie lub tylko Inspekcja), jeśli zawiera tokeny zarówno z listy globalnej, jak i dla danego klienta. Dokumentacja dziennika zdarzeń została zaktualizowana w celu odzwierciedlenia tego; Zobacz [monitorowanie ochrony hasłem w usłudze Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Poprawki wydajności i niezawodności
* Ulepszone rejestrowanie

> [!WARNING]
> Funkcja ograniczona czasowo: usługa agenta kontrolera domeny w tej wersji (1.2.65.0) nie będzie przetwarzać żądań weryfikacji haseł od 1 września 2019.  Usługi agenta kontrolera domeny we wcześniejszych wersjach (patrz lista poniżej) przestaną być przetwarzane od 1 lipca 2019. Usługa agenta kontrolera domeny we wszystkich wersjach będzie rejestrować zdarzenia 10021 w dzienniku zdarzeń administratora w dwóch miesiącach poprzedzających te terminy. Wszystkie ograniczenia czasowe zostaną usunięte w nadchodzącym wydaniu. Usługa agenta proxy nie jest ograniczona czasowo w żadnej wersji, ale nadal powinna zostać uaktualniona do najnowszej wersji, aby móc korzystać ze wszystkich kolejnych poprawek usterek i innych ulepszeń.

## <a name="12250"></a>1.2.25.0

Data wydania: 1 listopada 2018

Prefix

* Agent i serwer proxy usługi DC nie powinien już kończyć się niepowodzeniem z powodu błędów zaufania certyfikatów.
* Agent i serwer proxy usługi DC mają poprawki dla maszyn zgodnych ze standardem FIPS.
* Usługa proxy będzie teraz działała prawidłowo w środowisku sieciowym obsługującym protokół TLS 1,2.
* Niewielkie poprawki wydajności i niezawodności
* Ulepszone rejestrowanie

Wprowadzane

* Minimalny wymagany poziom systemu operacyjnego dla usługi proxy to teraz Windows Server 2012 R2. Minimalny wymagany poziom systemu operacyjnego dla usługi agenta DC pozostaje w systemie Windows Server 2012.
* Usługa serwera proxy wymaga teraz programu .NET w wersji 4.6.2.
* Algorytm walidacji hasła używa rozszerzonej tabeli normalizacji znaków. Ta zmiana może spowodować odrzucenie haseł, które zostały zaakceptowane we wcześniejszych wersjach.

## <a name="12100"></a>1.2.10.0

Data wydania: 17 sierpnia 2018

Prefix

* Register-AzureADPasswordProtectionProxy i Register-AzureADPasswordProtectionForest obsługują teraz uwierzytelnianie wieloskładnikowe
* Aby uniknąć błędów szyfrowania, Register-AzureADPasswordProtectionProxy wymaga kontrolera domeny WS2012 lub nowszego w domenie.
* Usługa agenta kontrolera domeny jest bardziej niezawodna na żądanie nowych zasad haseł z platformy Azure przy uruchamianiu.
* Usługa agenta kontrolera domeny będzie żądać nowych zasad haseł od platformy Azure co godzinę, jeśli będzie to konieczne, ale teraz zostanie to zrobione w losowo wybranym czasie rozpoczęcia.
* Usługa agenta DC nie będzie już powodowała nieograniczonego opóźnienia w nowym anonsie kontrolera domeny, gdy jest zainstalowany na serwerze przed jego podwyższeniem poziomu jako repliki.
* Usługa agenta kontrolera domeny będzie teraz przestrzegać ustawienia konfiguracji "Włącz ochronę hasłem w systemie Windows Server Active Directory"
* Zarówno Instalator agenta kontrolera domeny, jak i serwer proxy programu obsługują uaktualnienie w miejscu podczas uaktualniania do przyszłych wersji.

> [!WARNING]
> Uaktualnienie w miejscu z wersji 1.1.10.3 nie jest obsługiwane i spowoduje błąd instalacji. Aby uaktualnić do wersji 1.2.10 lub nowszej, należy najpierw całkowicie odinstalować agenta kontrolera domeny i oprogramowanie usługi serwera proxy, a następnie zainstalować nową wersję od podstaw. Wymagana jest ponowna rejestracja usługi proxy ochrony hasłem w usłudze Azure AD.  Ponowne zarejestrowanie lasu nie jest wymagane.

> [!NOTE]
> Uaktualnienia w miejscu oprogramowania agenta kontrolera domeny będą wymagać ponownego uruchomienia komputera.

* Agent DC i usługa serwera proxy obsługują teraz działanie na serwerze skonfigurowanym do korzystania tylko z algorytmów zgodnych ze standardem FIPS.
* Niewielkie poprawki wydajności i niezawodności
* Ulepszone rejestrowanie

## <a name="11103"></a>1.1.10.3

Data wydania: 15 czerwca 2018

Początkowa wersja publicznej wersji zapoznawczej

## <a name="next-steps"></a>Następne kroki

[Wdróż ochronę hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md)
