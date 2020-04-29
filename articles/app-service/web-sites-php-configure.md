---
title: Konfigurowanie środowiska uruchomieniowego języka PHP
description: Dowiedz się, jak skonfigurować domyślną instalację PHP lub dodać niestandardową instalację PHP dla Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272478"
---
# <a name="configure-php-in-azure-app-service"></a>Konfigurowanie języka PHP w Azure App Service

## <a name="introduction"></a>Wprowadzenie

W tym przewodniku pokazano, jak skonfigurować wbudowane środowisko uruchomieniowe PHP dla aplikacji sieci Web i aplikacji interfejsu API w [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), udostępnić niestandardowe środowisko uruchomieniowe php i włączyć rozszerzenia. Aby skorzystać z App Service, zarejestruj się w celu uzyskania [bezpłatnej wersji próbnej]. Aby najlepiej wykorzystać ten przewodnik, należy najpierw utworzyć aplikację PHP w App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Instrukcje: Zmienianie wbudowanej wersji języka PHP

Podczas tworzenia aplikacji sieci Web można wybrać wersję języka PHP, która zostanie skonfigurowana. Zobacz [php w App Service](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) , aby uzyskać aktualne informacje o aktualnie obsługiwanych wersjach.

Aby sprawdzić istniejącą wersję środowiska uruchomieniowego aplikacji, można wdrożyć skrypt wywołujący funkcję [phpinfo ()] .

Aby zaktualizować wersję języka PHP, należy wykonać jedną z następujących metod:

### <a name="azure-portal"></a>Azure Portal

1. Przejdź do aplikacji w [Azure Portal](https://portal.azure.com) i przewiń do strony **Konfiguracja** .

2. W obszarze **Konfiguracja**wybierz pozycję **Ustawienia ogólne** i wybierz nową wersję php.

3. Kliknij przycisk **Zapisz** znajdujący się u góry bloku **Ustawienia ogólne** .

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy zainstalować na komputerze interfejs użytkownika [platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

1. Otwórz Terminal i zaloguj się na swoim koncie.

        az login

1. Zaznacz, aby wyświetlić listę obsługiwanych środowisk uruchomieniowych.

        az webapp list-runtimes | grep php

2. Ustaw wersję języka PHP dla aplikacji.

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. Wersja języka PHP jest teraz ustawiona. Można potwierdzić następujące ustawienia:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Instrukcje: Zmienianie wbudowanych konfiguracji PHP

W przypadku każdego wbudowanego środowiska uruchomieniowego PHP można zmienić dowolną z opcji konfiguracji, wykonując następujące kroki. (Aby uzyskać informacje na temat dyrektyw php. ini, zobacz [listę dyrektyw php. ini]).

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Zmiana pliku\_ini\_php, PHP\_ini\_PERDIR, php\_ini\_wszystkie ustawienia konfiguracji

1. Dodaj plik [. User. ini] do katalogu głównego.
1. Dodaj ustawienia konfiguracji do `.user.ini` pliku, używając tej samej składni, która będzie używana w `php.ini` pliku. Jeśli na przykład chcesz włączyć `display_errors` ustawienie i ustawić wartość `upload_max_filesize` 10 mln, `.user.ini` plik będzie zawierał następujący tekst:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Wdróż aplikację.
3. Uruchom ponownie aplikację. (Ponowne uruchomienie jest niezbędne, ponieważ częstotliwość odczytywania `.user.ini` plików przez php jest określana przez `user_ini.cache_ttl` ustawienie, które jest ustawieniem na poziomie systemu i domyślnie 300 sekund (5 minut). Ponowne uruchomienie aplikacji wymusza odczytanie nowych ustawień w `.user.ini` pliku przy użyciu języka PHP.

Alternatywą dla użycia `.user.ini` pliku jest użycie funkcji [ini_set ()] w skryptach w celu ustawienia opcji konfiguracji, które nie są dyrektywami na poziomie systemu.

### <a name="changing-php_ini_system-configuration-settings"></a>Zmiana ustawień\_konfiguracji\_systemu php ini

1. Dodaj ustawienie aplikacji do aplikacji przy użyciu klucza `PHP_INI_SCAN_DIR` i wartości`d:\home\site\ini`
1. Utwórz `settings.ini` plik przy użyciu konsoli Kudu (http://&lt;site-name&gt;. SCM.azurewebsite.NET) w `d:\home\site\ini` katalogu.
1. Dodaj ustawienia konfiguracji do `settings.ini` pliku, używając tej samej składni, która będzie używana w `php.ini` pliku. Na przykład jeśli chcesz wskazać `curl.cainfo` `*.crt` plik i ustawić ustawienie "WinCache. MaxFileSize" na 512 K, `settings.ini` plik będzie zawierać następujący tekst:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Aby ponownie załadować zmiany, uruchom ponownie aplikację.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Instrukcje: Włączanie rozszerzeń w domyślnym środowisku uruchomieniowym PHP

Jak wskazano w poprzedniej sekcji, najlepszym sposobem wyświetlenia domyślnej wersji języka PHP, jej konfiguracji domyślnej i włączonych rozszerzeń jest wdrożenie skryptu wywołującego [phpinfo ()]. Aby włączyć dodatkowe rozszerzenia, wykonaj następujące czynności:

### <a name="configure-via-ini-settings"></a>Konfigurowanie za pomocą ustawień pliku ini

1. Dodaj `ext` katalog do `d:\home\site` katalogu.
1. Umieść `.dll` pliki rozszerzeń w `ext` katalogu (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją języka PHP i są zgodne z VC9 i niebezpiecznym wątkem (nkty przerwania).
1. Dodaj ustawienie aplikacji do aplikacji przy użyciu klucza `PHP_INI_SCAN_DIR` i wartości`d:\home\site\ini`
1. Utwórz `ini` plik w `d:\home\site\ini` nazwie. `extensions.ini`
1. Dodaj ustawienia konfiguracji do `extensions.ini` pliku, używając tej samej składni, która będzie używana w `php.ini` pliku. Na przykład jeśli chcesz włączyć rozszerzenia MongoDB i narzędzia Xdebug, `extensions.ini` plik będzie zawierać następujący tekst:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Uruchom ponownie aplikację, aby załadować zmiany.

### <a name="configure-via-app-setting"></a>Konfiguruj przy użyciu ustawienia aplikacji

1. Dodaj `bin` katalog do katalogu głównego.
2. Umieść `.dll` pliki rozszerzeń w `bin` katalogu (na przykład `php_xdebug.dll`). Upewnij się, że rozszerzenia są zgodne z domyślną wersją języka PHP i są zgodne z VC9 i niebezpiecznym wątkem (nkty przerwania).
3. Wdróż aplikację.
4. Przejdź do aplikacji w Azure Portal i kliknij **konfigurację** znajdującą się poniżej sekcji **Ustawienia** .
5. W bloku **Konfiguracja** wybierz pozycję **Ustawienia aplikacji**.
6. W sekcji **Ustawienia aplikacji** kliknij pozycję **+ nowe ustawienie aplikacji** i Utwórz klucz **PHP_EXTENSIONS** . Wartość tego klucza powinna być ścieżką względem katalogu głównego witryny sieci Web: **bin\your-EXT-File**.
7. Kliknij przycisk **Aktualizuj** u dołu ekranu, a następnie kliknij przycisk **Zapisz** powyżej karty **Ustawienia aplikacji** .

Rozszerzenia Zend są również obsługiwane przy użyciu klucza **PHP_ZENDEXTENSIONS** . Aby włączyć wiele rozszerzeń, należy dodać rozdzieloną przecinkami listę `.dll` plików dla wartości ustawienia aplikacji.

## <a name="how-to-use-a-custom-php-runtime"></a>Instrukcje: korzystanie z niestandardowego środowiska uruchomieniowego PHP

Zamiast domyślnego środowiska uruchomieniowego PHP, App Service może używać środowiska uruchomieniowego PHP, które jest zapewniane do wykonywania skryptów PHP. Środowisko uruchomieniowe, które można udostępnić, można skonfigurować `php.ini` za pomocą pliku, który również jest udostępniany. Aby użyć niestandardowego środowiska uruchomieniowego PHP z App Service, wykonaj następujące czynności.

1. Uzyskaj zgodną z VC9 lub VC11 wersję języka PHP dla systemu Windows. Najnowsze wersje środowiska PHP dla systemu Windows można znaleźć tutaj: [https://windows.php.net/download/]. Starsze wersje można znaleźć w archiwum tutaj: [https://windows.php.net/downloads/releases/archives/].
2. Zmodyfikuj `php.ini` plik dla środowiska uruchomieniowego. Wszystkie ustawienia konfiguracji, które są dyrektywami tylko na poziomie systemu, są ignorowane przez App Service. (Aby uzyskać informacje na temat dyrektyw opartych na poziomie systemu, zobacz [listę dyrektyw php. ini]).
3. Opcjonalnie dodaj rozszerzenia do środowiska uruchomieniowego PHP i włącz je w `php.ini` pliku.
4. Dodaj `bin` katalog do katalogu głównego i umieść w nim katalog zawierający środowisko uruchomieniowe php (na przykład `bin\php`).
5. Wdróż aplikację.
6. Przejdź do aplikacji w Azure Portal i kliknij blok **Konfiguracja** .
8. W bloku **Konfiguracja** wybierz pozycję **mapowania ścieżek**. 
9. Kliknij pozycję **+ Nowa obsługa** i `*.php` Dodaj do pola rozszerzenia i Dodaj ścieżkę do `php-cgi.exe` pliku wykonywalnego w **procesorze skryptów**. W przypadku umieszczenia środowiska uruchomieniowego PHP w `bin` katalogu w katalogu głównym aplikacji ścieżka jest `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Kliknij przycisk **Aktualizuj** w dolnej części, aby zakończyć dodawanie mapowania programu obsługi.
11. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Instrukcje: Włączanie automatyzacji kompozytora na platformie Azure

Domyślnie App Service nie wykonuje żadnych czynności z użyciem pliku Composer. JSON, jeśli istnieje on w projekcie PHP. Jeśli używasz [wdrożenia git](deploy-local-git.md), możesz włączyć przetwarzanie pliku Composer. JSON w trakcie `git push` , włączając rozszerzenie Composer.

> [!NOTE]
> Możesz [głosować na potrzeby obsługi kompozytorów pierwszej klasy w App Service tym miejscu](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. W bloku aplikacji PHP w [Azure Portal](https://portal.azure.com)kliknij przycisk **Narzędzia** > **rozszerzenia**.

    ![Blok ustawień Azure Portal, aby włączyć automatyzację kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Kliknij przycisk **Dodaj**, a następnie kliknij pozycję **układacz**.

    ![Dodaj rozszerzenie Composer, aby włączyć automatyzację kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Kliknij przycisk **OK** , aby zaakceptować postanowienia prawne. Kliknij ponownie przycisk **OK** , aby dodać rozszerzenie.

    Blok **zainstalowane rozszerzenia** pokazuje rozszerzenie układacz.
    ![Zaakceptuj postanowienia prawne, aby włączyć automatyzację kompozytora na platformie Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Teraz w oknie terminalu na komputerze lokalnym wykonaj `git add`, `git commit`i `git push` do aplikacji. Zwróć uwagę, że układacz instaluje zależności zdefiniowane w pliku Composer. JSON.

    ![Wdrożenie narzędzia Git z automatyzacją Composer na platformie Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[bezpłatna wersja próbna]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista dyrektyw języka PHP. ini]: https://www.php.net/manual/en/ini.list.php
[. User. ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set ()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
