---
title: Uaktualnianie PhoneFactor do serwera usługi Azure MFA — Azure Active Directory
description: Rozpoczynanie pracy z serwerem Azure MFA podczas uaktualniania ze starszej wersji agenta PhoneFactor.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e7c0399cce45b36a96bedb14d225490d48ee049
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966886"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Uaktualnianie agenta PhoneFactor do serwera Azure Multi-Factor Authentication

Aby uaktualnić agenta PhoneFactor (w wersji 5.x lub starszej) do serwera Azure Multi-Factor Authentication, najpierw odinstaluj agenta PhoneFactor i powiązane z nim składniki. Następnie można zainstalować serwer Multi-Factor Authentication i powiązane z nim składniki.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać opartych na chmurze Multi-Factor Authentication platformy Azure.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="uninstall-the-phonefactor-agent"></a>Odinstalowywanie agenta PhoneFactor

1. Najpierw należy utworzyć kopię zapasową pliku danych PhoneFactor. Domyślna lokalizacja instalacji to C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.

2. Jeśli zainstalowano portal użytkowników:
   1. Przejdź do folderu instalacji i utwórz kopię zapasową pliku web.config. Domyślna lokalizacja instalacji to C:\inetpub\wwwroot\PhoneFactor.

   2. Jeśli do portalu dodano niestandardowe kompozycje, należy wykonać kopię zapasową niestandardowego folderu poniżej katalogu C:\inetpub\wwwroot\PhoneFactor\App_Themes.

   3. Odinstaluj portal użytkowników za pośrednictwem agenta PhoneFactor (opcja dostępna tylko wtedy, gdy jest zainstalowany na tym samym serwerze co agent PhoneFactor) lub za pomocą modułu Programy i funkcje systemu Windows.

3. Jeśli zainstalowana jest usługa sieci Web aplikacji mobilnej:

   1. Przejdź do folderu instalacyjnego i utwórz kopię zapasową pliku web.config. Domyślna lokalizacja instalacji to C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

   2. Odinstaluj usługę sieci Web aplikacji mobilnej za pośrednictwem modułu Programy i funkcje systemu Windows.

4. Jeśli zainstalowano zestaw SDK usługi sieci Web, należy go odinstalować za pomocą agenta PhoneFactor lub za pośrednictwem modułu Programy i funkcje systemu Windows.

5. Odinstaluj agenta PhoneFactor za pośrednictwem modułu Programy i funkcje systemu Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Instalowanie serwera Multi-Factor Authentication

Ścieżka instalacji jest pobierana z rejestru z poprzedniej instalacji agenta PhoneFactor, dlatego instalacji należy dokonać w tej samej lokalizacji (np. C:\Program Files\PhoneFactor). Nowe instalacje mają inne domyślne ścieżki instalacji (np. C:\Program Files\Multi-Factor Authentication Server). Plik danych pozostawiony przez poprzedniego agenta PhoneFactor należy uaktualnić podczas instalacji, aby po zainstalowaniu nowego serwera Multi-Factor Authentication określeni wcześniej użytkownicy i ustawienia nadal były dostępne.

1. Jeśli zostanie wyświetlony monit, uaktywnij serwer Multi-Factor Authentication i upewnij się, że jest przypisany do odpowiedniej grupy replikacji.

2. Jeśli wcześniej zainstalowano zestaw SDK usługi sieci Web, należy zainstalować nowy zestaw SDK usługi sieci Web za pomocą interfejsu użytkownika serwera Multi-Factor Authentication.

   Domyślną nazwą katalogu wirtualnego jest teraz **MultiFactorAuthWebServiceSdk** zamiast **PhoneFactorWebServiceSdk**. Jeśli chcesz użyć poprzedniej nazwy, musisz podczas instalacji zmienić nazwę katalogu wirtualnego. W przeciwnym razie, jeśli zezwolisz procesowi instalacji na użycie nowej nazwy domyślnej, trzeba będzie zmienić adres URL w każdej aplikacji, która odwołuje się do zestawu SDK usługi internetowej, takiej jak portal użytkowników i usługa internetowa aplikacji mobilnej, aby wskazywała na poprawną lokalizację.

3. Jeśli wcześniej zainstalowano portal użytkowników na tym serwerze agenta PhoneFactor, zainstaluj nowy portal użytkowników usługi Multi-Factor Authentication za pomocą interfejsu użytkownika serwera Multi-Factor Authentication.

   Domyślną nazwą katalogu wirtualnego jest teraz **MultiFactorAuth** zamiast **PhoneFactor**. Jeśli chcesz użyć poprzedniej nazwy, musisz podczas instalacji zmienić nazwę katalogu wirtualnego. W przeciwnym razie, jeśli zezwolisz procesowi instalacji na użycie nowej nazwy domyślnej, musisz kliknąć ikonę Portal użytkowników na serwerze Multi-Factor Authentication i zaktualizować adres URL portalu użytkowników na karcie Ustawienia.

4. Jeśli portal użytkowników i/lub usługa sieci Web aplikacji mobilnej zostały wcześniej zainstalowane na innym serwerze z agenta PhoneFactor:

   1. Przejdź do lokalizacji instalacji (np. C:\Program Files\PhoneFactor) i skopiuj jeden lub więcej plików instalacyjnych na inny serwer. Zarówno dla portalu użytkowników, jak i dla usługi sieci Web aplikacji mobilnej istnieją instalatory w wersji 32-bitowej i 64-bitowej. Mają one nazwy MultiFactorAuthenticationUserPortalSetupXX.msi i MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

   2. Aby zainstalować portal użytkowników na serwerze sieci Web, otwórz wiersz polecenia jako administrator i uruchom instalatora MultiFactorAuthenticationUserPortalSetupXX.msi.

      Domyślną nazwą katalogu wirtualnego jest teraz **MultiFactorAuth** zamiast **PhoneFactor**. Jeśli chcesz użyć poprzedniej nazwy, musisz podczas instalacji zmienić nazwę katalogu wirtualnego. W przeciwnym razie, Jeśli zezwolisz instalacji na użycie nowej nazwy domyślnej, należy kliknąć ikonę portalu użytkowników w Serwer Multi-Factor Authentication i zaktualizować adres URL portalu użytkowników na karcie Ustawienia. Istniejący użytkownicy muszą być informowani o nowym adresie URL.

   3. Przejdź do lokalizacji instalacji portalu użytkowników (np. C:\inetpub\wwwroot\MultiFactorAuth) i otwórz do edycji plik web.config. Z oryginalnego pliku web.config, który został umieszczony w kopii zapasowej przed uaktualnieniem, skopiuj wartości z sekcji appSettings i applicationSettings do nowego pliku web.config. Jeśli nowa domyślna nazwa katalogu wirtualnego została zachowana podczas instalacji zestawu SDK usługi sieci Web, zmień adres URL w sekcji applicationSettings, aby wskazywał poprawną lokalizację. Jeśli w poprzednim pliku web.config zostały zmienione inne wartości domyślne, zastosuj te same zmiany w nowym pliku web.config.

> [!NOTE]
> W przypadku uaktualniania z wersji serwera Azure MFA Server starszej niż 8.0 do wersji 8.0 lub nowszej po uaktualnieniu można odinstalować usługę internetową aplikacji mobilnej

## <a name="next-steps"></a>Następne kroki

- [Zainstaluj portal użytkowników](howto-mfaserver-deploy-userportal.md) dla serwera Azure Multi-Factor Authentication.

- [Skonfiguruj uwierzytelnianie systemu Windows](howto-mfaserver-windows.md) dla aplikacji. 
