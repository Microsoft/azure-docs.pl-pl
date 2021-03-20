---
title: Technologie open source — często zadawane pytania
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące technologii open source w programie Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831843"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Technologie open source — często zadawane pytania dotyczące Web Apps na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z technologiami typu "open source" dla [funkcji Web Apps Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Jak mogę włączyć rejestrowanie w języku PHP, aby rozwiązać problemy z PHP?

Aby włączyć rejestrowanie w języku PHP:

1. Zaloguj się do **witryny sieci Web kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. W górnym menu wybierz kolejno polecenia **Debuguj konsolę**  >  **cmd**.
3. Wybierz folder **witryny** .
4. Wybierz folder **wwwroot** .
5. Wybierz **+** ikonę, a następnie wybierz pozycję **nowy plik**.
6. Ustaw nazwę pliku na **.user.ini**.
7. Wybierz ikonę ołówka obok pozycji **.user.ini**.
8. W pliku Dodaj następujący kod: `log_errors=on`
9. Wybierz pozycję **Zapisz**.
10. Wybierz ikonę ołówka obok pozycji **wp-config. php**.
11. Zmień tekst na następujący kod:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. W Azure Portal, w menu aplikacji sieci Web, uruchom ponownie aplikację sieci Web.

Aby uzyskać więcej informacji, zobacz [Włączanie dzienników błędów oprogramowania WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Jak mogę rejestrować błędy aplikacji w języku Python w aplikacjach hostowanych w usłudze App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Jak mogę zmienić wersję Node.js aplikacji hostowanej w App Service?

Aby zmienić wersję aplikacji Node.js, można użyć jednej z następujących opcji:

* W Azure Portal Użyj **ustawień aplikacji**.
  1. W Azure Portal przejdź do aplikacji sieci Web.
  2. W bloku **Ustawienia** wybierz pozycję **Ustawienia aplikacji**.
  3. W obszarze **Ustawienia aplikacji** można uwzględnić WEBSITE_NODE_DEFAULT_VERSION jako klucz i wersję Node.js, która ma być wartością.
  4. Przejdź do **konsoli kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
  5. Aby sprawdzić wersję Node.js, wprowadź następujące polecenie:  
     ```
     node -v
     ```
* Zmodyfikuj plik programu iisnode. yml. Zmiana wersji Node.js w pliku programu iisnode. yml ustawia tylko środowisko uruchomieniowe używane przez programu iisnode. Kudu cmd i inne nadal używają wersji Node.js, która jest ustawiona w **ustawieniach aplikacji** w Azure Portal.

  Aby ręcznie ustawić programu iisnode. yml, Utwórz plik programu iisnode. yml w folderze głównym aplikacji. W pliku Uwzględnij następujący wiersz:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Ustaw plik programu iisnode. yml przy użyciu package.jsna etapie wdrażania kontroli źródła.
  Proces wdrażania kontroli źródła platformy Azure obejmuje następujące kroki:
  1. Przenosi zawartość do aplikacji sieci Web platformy Azure.
  2. Tworzy domyślny skrypt wdrażania, jeśli w folderze głównym aplikacji sieci Web nie ma żadnego pliku (Deploy. cmd,. Deployment Files).
  3. Uruchamia skrypt wdrażania, w którym tworzy plik programu iisnode. yml w przypadku podawania wersji Node.js w package.jsna aparacie plików > `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Plik programu iisnode. yml ma następujący wiersz kodu:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Widzę komunikat "błąd podczas ustanawiania połączenia z bazą danych" w aplikacji WordPress hostowanej w App Service. Jak mogę rozwiązać ten problem?

Jeśli ten błąd jest wyświetlany w aplikacji Azure WordPress, aby włączyć php_errors. log i Debug. log, wykonaj kroki opisane szczegółowo w temacie [Włączanie dzienników błędów oprogramowania WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

Po włączeniu dzienników Odtwórz błąd, a następnie sprawdź dzienniki, aby sprawdzić, czy wykorzystano z połączeń:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Jeśli ten błąd wystąpi w plikach Debug. log lub php_errors. log, aplikacja przekracza liczbę połączeń. Jeśli przechowujesz w usłudze ClearDB, sprawdź liczbę połączeń dostępnych w [planie usługi](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Jak mogę debugować aplikację Node.js hostowaną w App Service?

1.  Przejdź do **konsoli kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` ).
2.  Przejdź do folderu logs aplikacji (D:\home\LogFiles\Application).
3.  W pliku logging_errors.txt Sprawdź zawartość.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Jak mogę zainstalować natywne moduły języka Python w App Service aplikacji sieci Web lub aplikacji interfejsu API?

Niektóre pakiety mogą nie być instalowane przy użyciu narzędzia PIP na platformie Azure. Pakiet może nie być dostępny w indeksie pakietu języka Python lub może być wymagany kompilator (kompilator nie jest dostępny na komputerze, na którym działa aplikacja sieci Web w App Service). Aby uzyskać informacje dotyczące instalowania modułów macierzystych w App Service aplikacje sieci Web i aplikacje interfejsu API, zobacz [Instalowanie modułów języka Python w programie App Service](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Jak mogę wdrożyć aplikację Django do App Service przy użyciu narzędzia Git i nowej wersji języka Python?

Aby uzyskać informacje na temat instalowania programu Django, zobacz [wdrażanie aplikacji Django do App Service](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python).

## <a name="where-are-the-tomcat-log-files-located"></a>Gdzie znajdują się pliki dziennika Tomcat?

W witrynie Azure Marketplace i wdrożeniach niestandardowych:

* Lokalizacja folderu: D:\home\site\wwwroot\bin\apache-Tomcat-8.0.33\logs
* Interesujące pliki:
    * catalina. *rrrr-mm-dd*. log
    * Menedżer hosta. *rrrr-mm-dd*. log
    * lokalnym. *rrrr-mm-dd*. log
    * Menedżera. *rrrr-mm-dd*. log
    * site_access_log. *rrrr-mm-dd*. log


W przypadku wdrożeń **ustawień aplikacji** portalu:

* Lokalizacja folderu: D:\home\LogFiles
* Interesujące pliki:
    * catalina. *rrrr-mm-dd*. log
    * Menedżer hosta. *rrrr-mm-dd*. log
    * lokalnym. *rrrr-mm-dd*. log
    * Menedżera. *rrrr-mm-dd*. log
    * site_access_log. *rrrr-mm-dd*. log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Jak mogę rozwiązać problemy z błędami połączenia sterownika JDBC?

W dziennikach Tomcat może zostać wyświetlony następujący komunikat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Aby rozwiązać ten problem:

1. Usuń plik JAR sqljdbc* z folderu App/lib.
2. Jeśli używasz niestandardowego serwera sieci Web Tomcat lub Azure Marketplace Tomcat, Skopiuj ten plik jar do folderu Tomcat lib.
3. W przypadku włączania języka Java z poziomu Azure Portal (wybierz pozycję **Java 1,8**  >  **Tomcat Server**) Skopiuj plik JAR sqljdbc. * w folderze, który jest równoległy do aplikacji. Następnie Dodaj następujące ustawienie ścieżki klasy do pliku web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Dlaczego widzę błędy podczas próby skopiowania plików dziennika na żywo?

W przypadku próby skopiowania plików dziennika na żywo dla aplikacji Java (na przykład Tomcat) może zostać wyświetlony następujący błąd FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Komunikat o błędzie może się różnić w zależności od klienta FTP.

Wszystkie aplikacje Java mają ten problem z blokowaniem. Tylko kudu obsługuje pobieranie tego pliku, gdy aplikacja jest uruchomiona.

Zatrzymywanie aplikacji umożliwia dostęp do tych plików za pomocą protokołu FTP.

Innym obejściem jest napisanie Zadania WebJob, które jest uruchamiane zgodnie z harmonogramem, i skopiowanie tych plików do innego katalogu. W przypadku przykładowego projektu zapoznaj się z projektem [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) .

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Gdzie mogę znaleźć pliki dziennika dla Jetty?

W przypadku wdrożeń w witrynie Marketplace i niestandardowych plik dziennika znajduje się w folderze D:\home\site\wwwroot\bin\jetty-Distribution-9.1.2.v20140210\logs. Należy pamiętać, że lokalizacja folderu zależy od używanej wersji programu Jetty. Na przykład ścieżka podana w tym miejscu dotyczy Jetty 9.1.2. Poszukaj jetty_ *YYYY_MM_DD*. stderrout. log.

W przypadku wdrożeń ustawień aplikacji portalu plik dziennika znajduje się w D:\home\LogFiles. Wyszukaj jetty_ *YYYY_MM_DD*. stderrout. log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Czy mogę wysyłać wiadomości e-mail z mojej aplikacji sieci Web platformy Azure?

App Service nie ma wbudowanej funkcji poczty e-mail. Aby uzyskać bardziej przydatne alternatywy do wysyłania wiadomości e-mail z aplikacji, zobacz tę [Stack Overflow dyskusji](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Dlaczego moja witryna WordPress przekierowuje na inny adres URL?

Jeśli ostatnio przeprowadzono migrację do platformy Azure, usługa WordPress może przekierować do starego adresu URL domeny. Jest to spowodowane ustawieniem w bazie danych MySQL.

WordPress kolega + to rozszerzenie witryny platformy Azure, którego można użyć do zaktualizowania adresu URL przekierowania bezpośrednio w bazie danych programu. Aby uzyskać więcej informacji o korzystaniu z usługi WordPress kolega +, zobacz [Narzędzia WordPress i migracja MySQL przy użyciu oprogramowania WordPress kolega +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

Alternatywnie, jeśli wolisz ręcznie zaktualizować adres URL przekierowania przy użyciu zapytań SQL lub PHPMyAdmin, zobacz [WordPress: przekierowywanie do nieprawidłowego adresu URL](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Jak mogę zmienić hasła logowania WordPress?

Jeśli zapomniano hasła logowania WordPress, możesz go zaktualizować za pomocą oprogramowania WordPress kolega +. Aby zresetować hasło, zainstaluj rozszerzenie WordPress kolega + Azure Site, a następnie wykonaj kroki opisane w temacie [Narzędzia WordPress i migracja MySQL przy użyciu oprogramowania WordPress kolega +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nie mogę zalogować się do platformy WordPress. Jak rozwiązać ten problem?

Jeśli przed niedawno zainstalowaniem wtyczki okaże się, że zablokowano, możesz mieć wadliwą wtyczkę. WordPress kolega + to rozszerzenie witryny platformy Azure, które może pomóc w wyłączaniu wtyczek w WordPress. Aby uzyskać więcej informacji, zobacz [Narzędzia WordPress i migracja MySQL przy użyciu oprogramowania WordPress kolega +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Jak mogę zmigrować moją bazę danych WordPress?

Istnieje wiele opcji migrowania bazy danych MySQL połączonej z witryną sieci Web WordPress:

* Deweloperzy: Użyj [wiersza polecenia lub phpMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service)
* Inni niż deweloperzy: korzystanie z oprogramowania [WordPress kolega +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Jak mogę zwiększyć bezpieczeństwo platformy WordPress?

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń w systemie WordPress, zobacz [najlepsze rozwiązania dotyczące zabezpieczeń WordPress na platformie Azure](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Próbuję użyć PHPMyAdmin, a widzę komunikat "odmowa dostępu". Jak rozwiązać ten problem?

Ten problem może wystąpić, jeśli funkcja MySQL w aplikacji nie została jeszcze uruchomiona w tym wystąpieniu App Service. Aby rozwiązać ten problem, spróbuj uzyskać dostęp do witryny sieci Web. Spowoduje to uruchomienie wymaganych procesów, w tym procesu MySQL w aplikacji. Aby sprawdzić, czy MySQL w aplikacji jest uruchomiona, w Eksploratorze procesów upewnij się, że mysqld.exe jest wymieniona w procesach.

Po upewnieniu się, że MySQL w aplikacji jest uruchomiona, spróbuj użyć PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Otrzymuję błąd HTTP 403 podczas próby zaimportowania lub wyeksportowania bazy danych MySQL w aplikacji za pomocą PHPMyadmin. Jak rozwiązać ten problem?

Jeśli używasz starszej wersji programu Chrome, być może występuje znany błąd. Aby rozwiązać ten problem, przeprowadź uaktualnienie do nowszej wersji programu Chrome. Spróbuj również użyć innej przeglądarki, takiej jak Internet Explorer lub Microsoft Edge, gdzie problem nie występuje.