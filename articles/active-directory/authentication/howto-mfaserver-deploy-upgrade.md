---
title: Uaktualnianie serwera usługi Azure MFA — Azure Active Directory
description: Kroki i wskazówki dotyczące uaktualniania Serwer Multi-Factor Authentication platformy Azure do nowszej wersji.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1755404a06d8586968801aa22f2af532da278802
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742327"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgrade to the latest Azure Multi-Factor Authentication Server (Uaktualnianie do najnowszej wersji serwera Azure Multi-Factor Authentication)

Ten artykuł przeprowadzi Cię przez proces uaktualniania serwera usługi Azure Multi-Factor Authentication (MFA) w wersji 6.0 lub nowszej. Jeśli musisz uaktualnić starą wersję agenta PhoneFactor, zapoznaj się z tematem [Uaktualnianie agenta PhoneFactor do usługi Azure serwer Multi-Factor Authentication](howto-mfaserver-deploy-upgrade-pf.md).

Jeśli uaktualniasz program z wersji v6. x lub starszej do wersji 7. x lub nowszej, wszystkie składniki zmieniają się z .NET 2,0 na .NET 4,5. Wszystkie składniki wymagają również Microsoft Visual C++ 2015 redystrybucyjnej aktualizacji 1 lub nowszej. Instalator serwera usługi MFA instaluje zarówno wersje x86, jak i x64 tych składników, jeśli nie są one jeszcze zainstalowane. Jeśli portal użytkowników i usługa sieci Web aplikacji mobilnej działają na oddzielnych serwerach, należy zainstalować te pakiety przed uaktualnieniem tych składników. Najnowszą aktualizację pakietu redystrybucyjnego Microsoft Visual C++ 2015 można znaleźć w [Centrum pobierania Microsoft](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać Multi-Factor Authentication usługi Azure AD opartych na chmurze.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

Kroki uaktualniania w skrócie:

* Uaktualnianie serwerów usługi Azure MFA (podwładnych, a następnie Master)
* Uaktualnianie wystąpień portalu użytkowników
* Uaktualnij wystąpienia kart AD FS

## <a name="upgrade-azure-mfa-server"></a>Uaktualnianie serwera usługi Azure MFA

1. Aby uzyskać najnowszą wersję Instalatora serwera usługi Azure MFA, wykonaj instrukcje podane w temacie [pobieranie serwer Multi-Factor Authentication platformy Azure](howto-mfaserver-deploy.md#download-the-mfa-server) .
2. Utwórz kopię zapasową pliku danych serwera usługi MFA znajdującego się w katalogu C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (przy założeniu domyślnej lokalizacji instalacji) na serwerze głównym usługi MFA.
3. W przypadku uruchamiania wielu serwerów w celu zapewnienia wysokiej dostępności należy zmienić systemy klienckie uwierzytelniane na serwerze usługi MFA, aby zatrzymać wysyłanie ruchu do serwerów, które są uaktualniane. Jeśli używasz modułu równoważenia obciążenia, Usuń podrzędny serwer MFA z modułu równoważenia obciążenia, wykonaj uaktualnienie, a następnie Dodaj serwer z powrotem do farmy.
4. Uruchom Nowy Instalator na każdym serwerze usługi MFA. Najpierw Uaktualnij serwery podrzędne, ponieważ mogą one odczytywać stary plik danych replikowany przez serwer główny.

   > [!NOTE]
   > Podczas uaktualniania serwera należy go usunąć z dowolnego równoważenia obciążenia lub udostępniania ruchu z innymi serwerami usługi MFA.
   >
   > Nie ma potrzeby odinstalowywania bieżącego serwera usługi MFA przed uruchomieniem Instalatora. Instalator przeprowadza uaktualnienie w miejscu. Ścieżka instalacji jest pobierana z rejestru z poprzedniej instalacji, więc jest instalowana w tej samej lokalizacji (np. C:\Program Files\Multi-Factor Authentication Server).
  
5. Jeśli zostanie wyświetlony monit o zainstalowanie pakietu aktualizacji redystrybucyjnej Microsoft Visual C++ 2015, zaakceptuj monit. Instalowane są zarówno wersje x86, jak i x64 pakietu.
6. Jeśli używasz zestawu SDK usługi sieci Web, zostanie wyświetlony monit o zainstalowanie nowego zestawu SDK usługi sieci Web. Podczas instalowania nowego zestawu SDK usługi sieci Web upewnij się, że nazwa katalogu wirtualnego jest zgodna z wcześniej zainstalowanym katalogiem wirtualnym (na przykład MultiFactorAuthWebServiceSdk).
7. Powtórz kroki na wszystkich serwerach podrzędnych. Podnieś poziom jednego ze podwładnych jako nowy wzorzec, a następnie Uaktualnij stary serwer główny.

## <a name="upgrade-the-user-portal"></a>Uaktualnianie portalu użytkowników

Przed przejściem do tej sekcji Ukończ uaktualnienie serwerów usługi MFA.

1. Utwórz kopię zapasową pliku web.config znajdującego się w katalogu wirtualnym lokalizacji instalacji portalu użytkowników (na przykład C:\inetpub\wwwroot\MultiFactorAuth). Jeśli wprowadzono zmiany w motywie domyślnym, należy również utworzyć kopię zapasową folderu App_Themes \Default. Lepiej jest utworzyć kopię folderu domyślnego i utworzyć nową kompozycję niż w celu zmiany motywu domyślnego.
2. Jeśli portal użytkowników jest uruchomiony na tym samym serwerze co inne składniki serwera usługi MFA, w ramach instalacji serwera usługi MFA zostanie wyświetlony komunikat z prośbą o aktualizację portalu użytkowników. Zaakceptuj monit i zainstaluj aktualizację portalu użytkowników. Sprawdź, czy nazwa katalogu wirtualnego jest zgodna z wcześniej zainstalowanym katalogiem wirtualnym (na przykład MultiFactorAuth).
3. Jeśli portal użytkowników znajduje się na własnym serwerze, skopiuj plik MultiFactorAuthenticationUserPortalSetup64.msi z lokalizacji instalacji jednego z serwerów usługi MFA i umieść go na serwerze sieci Web portalu użytkowników. Uruchom instalatora.

   Jeśli wystąpi błąd z informacją, wymagana jest "Microsoft Visual C++ 2015 redystrybucyjnej aktualizacji 1 lub nowszej", Pobierz i zainstaluj najnowszy pakiet aktualizacji z [Centrum pobierania Microsoft](https://www.microsoft.com/download/). Zainstaluj wersje x86 i x64.

4. Po zainstalowaniu zaktualizowanego oprogramowania portalu użytkowników Porównaj web.config kopię zapasową wykonaną w kroku 1 z nowym plikiem web.config. Jeśli w nowym web.config nie ma nowych atrybutów, skopiuj web.config kopii zapasowej do katalogu wirtualnego, aby zastąpić nowy. Kolejną opcją jest skopiowanie/wklejenie wartości appSettings i adres URL zestawu SDK usługi sieci Web z pliku kopii zapasowej do nowej web.config.

Jeśli masz Portal użytkowników na wielu serwerach, należy powtórzyć instalację na wszystkich z nich.

## <a name="upgrade-the-mobile-app-web-service"></a>Uaktualnianie usługi sieci Web aplikacji mobilnej

> [!NOTE]
> W przypadku uaktualniania z wersji serwera usługi Azure MFA starszej niż 8,0 do 8.0 + usługa sieci Web aplikacji mobilnej może zostać odinstalowana po uaktualnieniu

## <a name="upgrade-the-ad-fs-adapters"></a>Uaktualnianie kart AD FS

Przed przejściem do tej sekcji Ukończ uaktualnienie serwerów usługi MFA i portalu użytkowników.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Jeśli uwierzytelnianie wieloskładnikowe działa na różnych serwerach niż AD FS

Te instrukcje mają zastosowanie tylko w przypadku uruchamiania Serwer Multi-Factor Authentication niezależnie od serwerów AD FS. Jeśli obie usługi działają na tych samych serwerach, Pomiń tę sekcję i przejdź do kroku instalacji. 

1. Zapisz kopię pliku MultiFactorAuthenticationAdfsAdapter.config zarejestrowanego w AD FS lub wyeksportuj konfigurację przy użyciu następującego polecenia programu PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]` . Nazwa karty to "element windowsazuremultifactorauthentication" lub "AzureMfaServerAuthentication" w zależności od zainstalowanej wcześniej wersji.
2. Skopiuj następujące pliki z lokalizacji instalacji serwera usługi MFA do serwerów AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Edytuj skrypt Register-MultiFactorAuthenticationAdfsAdapter.ps1, dodając `-ConfigurationFilePath [path]` go do końca `Register-AdfsAuthenticationProvider` polecenia. Zastąp element *[path]* pełną ścieżką do pliku MultiFactorAuthenticationAdfsAdapter.config lub plikiem konfiguracji wyeksportowanym w poprzednim kroku.

   Sprawdź atrybuty w nowym MultiFactorAuthenticationAdfsAdapter.config, aby sprawdzić, czy pasują do starego pliku konfiguracji. Jeśli jakiekolwiek atrybuty zostały dodane lub usunięte w nowej wersji, skopiuj wartości atrybutów ze starego pliku konfiguracji do nowego lub zmodyfikuj stary plik konfiguracji, aby był zgodny.

### <a name="install-new-ad-fs-adapters"></a>Instalowanie nowych kart AD FS

> [!IMPORTANT]
> Użytkownicy nie będą musieli przeprowadzać weryfikacji dwuetapowej podczas kroków 3-8 tej sekcji. Jeśli masz AD FS skonfigurowany w wielu klastrach, możesz usunąć, uaktualnić i przywrócić każdy klaster w farmie niezależnie od innych klastrów, aby uniknąć przestojów.

1. Usuń niektóre AD FS serwery z farmy. Zaktualizuj te serwery, gdy inne nadal działają.
2. Zainstaluj nową kartę AD FS na każdym serwerze usuniętym z farmy AD FS. Jeśli serwer usługi MFA jest zainstalowany na każdym serwerze AD FS, można go zaktualizować za pomocą środowiska administracyjnego serwera usługi MFA. W przeciwnym razie zaktualizuj, uruchamiając MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Jeśli wystąpi błąd z informacją, wymagana jest "Microsoft Visual C++ 2015 redystrybucyjnej aktualizacji 1 lub nowszej", Pobierz i zainstaluj najnowszy pakiet aktualizacji z [Centrum pobierania Microsoft](https://www.microsoft.com/download/). Zainstaluj wersje x86 i x64.

3. Przejdź do pozycji **AD FS**  >  **zasady uwierzytelniania**  >  **Edytuj globalne zasady uwierzytelniania** wieloskładnikowego. Usuń zaznaczenie opcji **element windowsazuremultifactorauthentication** lub **AzureMFAServerAuthentication** (w zależności od zainstalowanej bieżącej wersji).

   Po zakończeniu tego kroku weryfikacja dwuetapowa za poorednictwem serwera usługi MFA nie jest dostępna w tym AD FS klastrze, dopóki nie zostanie ukończony krok 8.

4. Wyrejestruj starszą wersję karty AD FS, uruchamiając skrypt Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell. Upewnij się, że parametr *-name* ("element windowsazuremultifactorauthentication" lub "AzureMFAServerAuthentication") pasuje do nazwy, która została wyświetlona w kroku 3. Dotyczy to wszystkich serwerów w tym samym klastrze AD FS od momentu, gdy istnieje Centralna konfiguracja.
5. Zarejestruj nową kartę AD FS, uruchamiając skrypt Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell. Dotyczy to wszystkich serwerów w tym samym klastrze AD FS od momentu, gdy istnieje Centralna konfiguracja.
6. Uruchom ponownie usługę AD FS na każdym serwerze, który został usunięty z farmy AD FS.
7. Dodaj zaktualizowane serwery z powrotem do farmy AD FS i Usuń inne serwery z farmy.
8. Przejdź do pozycji **AD FS**  >  **zasady uwierzytelniania**  >  **Edytuj globalne zasady uwierzytelniania** wieloskładnikowego. Sprawdź **AzureMfaServerAuthentication**.
9. Powtórz krok 2, aby zaktualizować serwery teraz usunięte z farmy AD FS i ponownie uruchomić usługę AD FS na tych serwerach.
10. Dodaj te serwery z powrotem do farmy AD FS.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z przykładami [zaawansowanych scenariuszy z usługą Azure Multi-Factor Authentication i sieciami VPN innych](howto-mfaserver-nps-vpn.md) firm

* [Synchronizowanie serwera usługi MFA z systemem Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Konfigurowanie uwierzytelniania systemu Windows](howto-mfaserver-windows.md) dla aplikacji
