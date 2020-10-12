---
title: Historia wydania agenta ochrony haseł — Azure Active Directory
description: Dokumenty wersja wydania i zachowanie historia zmian zachowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74847853"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historia wersji agenta ochrony haseł usługi Azure AD

## <a name="121250"></a>1.2.125.0

Data wydania: 3/22/2019

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
  * Środowisko .NET 4,7 powinno być już zainstalowane w w pełni zaktualizowanym systemie Windows Server. Jeśli tak nie jest, Pobierz i uruchom Instalatora, który znajduje się w [instalatorze offline .NET Framework 4,7 dla systemu Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * W systemach Server Core może być konieczne przekazanie flagi/q do Instalatora .NET 4,7 w celu pomyślnego przeprowadzenia.
* Usługa serwera proxy obsługuje teraz automatyczne uaktualnianie. Automatyczne uaktualnianie Microsoft Azure AD używa usługi Aktualizator Connect Agent, która jest zainstalowana równolegle z usługą proxy. Automatyczne uaktualnianie jest domyślnie włączone.
* Automatyczne uaktualnianie można włączyć lub wyłączyć za pomocą polecenia cmdlet Set-AzureADPasswordProtectionProxyConfiguration. Bieżące ustawienie można zbadać przy użyciu polecenia cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* Nazwa pliku binarnego usługi dla usługi agenta kontrolera domeny została zmieniona na AzureADPasswordProtectionDCAgent.exe.
* Nazwa pliku binarnego usługi dla usługi serwera proxy została zmieniona na AzureADPasswordProtectionProxy.exe. W przypadku korzystania z zapory innej firmy może być konieczne zmodyfikowanie reguł zapory.
  * Uwaga: Jeśli plik konfiguracyjny serwera proxy HTTP był używany w poprzedniej instalacji serwera proxy, należy zmienić jego nazwę (z *proxyservice.exe.config* na *AzureADPasswordProtectionProxy.exe.config*) po tym uaktualnieniu.
* Wszystkie testy funkcji ograniczone przez czas zostały usunięte z agenta kontrolera domeny.
* Poprawki i ulepszenia rejestrowania drobnych usterek.

## <a name="12650"></a>1.2.65.0

Data wydania: 2/1/2019

Wprowadzane

* Agent i serwer proxy usługi DC są teraz obsługiwane w trybie Server Core. Wymagania systemu operacyjnego Mininimum nie zostały zmienione przed: system Windows Server 2012 dla agentów DC i system Windows Server 2012 R2 dla serwerów proxy.
* Polecenia cmdlet Register-AzureADPasswordProtectionProxy i Register-AzureADPasswordProtectionForest obsługują teraz tryby uwierzytelniania platformy Azure oparte na kodzie na urządzeniu.
* Get-AzureADPasswordProtectionDCAgent polecenie cmdlet zignoruje zniekształcona i/lub nieprawidłowe punkty połączenia usługi. Pozwala to rozwiązać usterkę, w której kontrolery domeny czasami będą widoczne w danych wyjściowych wiele razy.
* Get-AzureADPasswordProtectionSummaryReport polecenie cmdlet zignoruje zniekształcona i/lub nieprawidłowe punkty połączenia usługi. Pozwala to rozwiązać usterkę, w której kontrolery domeny czasami będą widoczne w danych wyjściowych wiele razy.
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
* Aktualizacje algorytmu walidacji hasła: Lista globalna wykluczonych haseł i lista wykluczonych haseł specyficznych dla klienta (jeśli zostały skonfigurowane) są łączone przed walidacjami haseł. Dane hasło mogą teraz zostać odrzucone (tylko Niepowodzenie lub tylko Inspekcja), jeśli zawiera tokeny zarówno z listy globalnej, jak i dla danego klienta. Dokumentacja dziennika zdarzeń została zaktualizowana w celu odzwierciedlenia tego; Zobacz [monitorowanie ochrony hasłem usługi Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Poprawki wydajności i niezawodności
* Ulepszone rejestrowanie

> [!WARNING]
> Funkcja ograniczona czasowo: usługa agenta kontrolera domeny w tej wersji (1.2.65.0) nie będzie przetwarzać żądań weryfikacji haseł od 1 września 2019.  Usługi agenta kontrolera domeny we wcześniejszych wersjach (patrz lista poniżej) przestaną być przetwarzane od 1 lipca 2019. Usługa agenta kontrolera domeny we wszystkich wersjach będzie rejestrować zdarzenia 10021 w dzienniku zdarzeń administratora w dwóch miesiącach poprzedzających te terminy. Wszystkie ograniczenia czasowe zostaną usunięte w nadchodzącym wydaniu. Usługa agenta proxy nie jest ograniczona czasowo w żadnej wersji, ale nadal powinna zostać uaktualniona do najnowszej wersji, aby móc korzystać ze wszystkich kolejnych poprawek usterek i innych ulepszeń.

## <a name="12250"></a>1.2.25.0

Data wydania: 11/01/2018

Prefix

* Agent i serwer proxy usługi DC nie powinien już kończyć się niepowodzeniem z powodu błędów zaufania certyfikatów.
* Agent i serwer proxy usługi DC mają dodatkowe poprawki dla maszyn zgodnych ze standardem FIPS.
* Usługa proxy będzie teraz działała prawidłowo w środowisku sieciowym obsługującym protokół TLS 1,2.
* Niewielkie poprawki wydajności i niezawodności
* Ulepszone rejestrowanie

Wprowadzane

* Minimalny wymagany poziom systemu operacyjnego dla usługi proxy to teraz Windows Server 2012 R2. Minimalny wymagany poziom systemu operacyjnego dla usługi agenta DC pozostaje w systemie Windows Server 2012.
* Usługa serwera proxy wymaga teraz programu .NET w wersji 4.6.2.
* Algorytm walidacji hasła używa rozszerzonej tabeli normalizacji znaków. Może to spowodować odrzucenie haseł, które zostały zaakceptowane we wcześniejszych wersjach.

## <a name="12100"></a>1.2.10.0

Data wydania: 8/17/2018

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

Data wydania: 6/15/2018

Początkowa wersja publicznej wersji zapoznawczej

## <a name="next-steps"></a>Następne kroki

[Wdróż ochronę hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md)
