---
title: Konfigurowanie aplikacji Java
description: Dowiedz się, jak skonfigurować aplikacje Java do uruchamiania na Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
keywords: Azure App Service, Web App, Windows, OSS, Java, Tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 0334e259f75440cae25f1e165c0621c85f7c7705
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804007"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji Java dla Azure App Service

Azure App Service umożliwia deweloperom języka Java szybkie tworzenie, wdrażanie i skalowanie aplikacji sieci Web Java SE, Tomcat i JBoss protokołu EAP w w pełni zarządzanej usłudze. Wdrażaj aplikacje z wtyczkami Maven, z poziomu wiersza polecenia lub w edytorach, takich jak IntelliJ, zaćmienie lub Visual Studio Code.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Java korzystających App Service. Jeśli nie korzystasz z Azure App Service, musisz najpierw przeczytać [Przewodnik Szybki Start dla języka Java](quickstart-java.md) . Ogólne pytania dotyczące korzystania z App Service, które nie są specyficzne dla programowania w języku Java, znajdują się w odpowiedzi na [App Service często zadawane pytania](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Za pomocą [wtyczki aplikacji sieci Web platformy Azure dla Maven](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) można wdrożyć pliki War lub jar. Wdrożenie ze popularną środowisk IDE jest również obsługiwane w przypadku [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) lub [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

W przeciwnym razie metoda wdrażania będzie zależeć od typu archiwum:

### <a name="java-se"></a>Java SE

Aby wdrożyć pliki jar w środowisku Java SE, użyj `/api/zipdeploy/` punktu końcowego witryny kudu. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](./deploy-zip.md#rest).

### <a name="tomcat"></a>Tomcat

Aby wdrożyć pliki War do Tomcat, użyj `/api/wardeploy/` punktu końcowego, aby opublikować plik archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](./deploy-zip.md#deploy-war-file).

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

Aby wdrożyć pliki War do JBoss, użyj `/api/wardeploy/` punktu końcowego, aby opublikować plik archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](./deploy-zip.md#deploy-war-file).

Aby wdrożyć pliki. [](deploy-ftp.md)

::: zone-end

Nie należy wdrażać. War lub. jar przy użyciu protokołu FTP. Narzędzie FTP służy do przekazywania skryptów uruchamiania, zależności lub innych plików czasu wykonywania. Nie jest to optymalna opcja wdrażania aplikacji sieci Web.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty o wydajności, wizualizacje ruchu i narzędzia dla kondycji są dostępne dla każdej aplikacji za pomocą Azure Portal. Aby uzyskać więcej informacji, zobacz [Omówienie diagnostyki Azure App Service](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe dzienników w Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów

Wbudowane obrazy języka Java są oparte na systemie operacyjnym [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Użyj `apk` Menedżera pakietów, aby zainstalować narzędzia do rozwiązywania problemów lub polecenia.

::: zone-end

### <a name="flight-recorder"></a>Rejestrator lotu

Wszystkie środowiska uruchomieniowe języka Java na App Service przy użyciu JVMs Azul są dostarczane z rejestratorem lotów Zulu. Służy do rejestrowania zdarzeń JVM, system i Application oraz rozwiązywania problemów z aplikacjami języka Java.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Rejestrowanie czasu

Aby można było rejestrować czas, potrzebny jest Identyfikator PID (identyfikator procesu) aplikacji Java. Aby znaleźć identyfikator PID, Otwórz przeglądarkę dla witryny SCM aplikacji sieci Web pod adresem https://<nazwę witryny>. scm.azurewebsites.net/ProcessExplorer/. Na tej stronie są wyświetlane uruchomione procesy w aplikacji sieci Web. Znajdź w tabeli proces o nazwie "Java" i skopiuj odpowiedni identyfikator PID (identyfikator procesu).

Następnie otwórz **konsolę debugowania** na górnym pasku narzędzi w witrynie SCM i uruchom następujące polecenie. Zamień na `<pid>` Identyfikator procesu, który został wcześniej skopiowany. To polecenie spowoduje uruchomienie 30-sekundowego nagrania profilera aplikacji Java i wygenerowanie pliku o nazwie `timed_recording_example.jfr` w `D:\home` katalogu.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

Użyj protokołu SSH do App Service i uruchom `jcmd` polecenie, aby wyświetlić listę wszystkich uruchomionych procesów języka Java. Oprócz samej jcmd powinna zostać wyświetlona aplikacja Java działająca z IDENTYFIKATORem procesu (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Wykonaj poniższe polecenie, aby rozpocząć rejestrowanie 30-sekundowe JVM. Spowoduje to profilowanie JVM i utworzenie pliku JFR o nazwie *jfr_example. JFR* w katalogu macierzystym. (Zastąp 116 identyfikatorem PID aplikacji Java).

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Podczas 30-sekundowego interwału można sprawdzić, czy nagranie odbywa się przez uruchomienie `jcmd 116 JFR.check` . Spowoduje to wyświetlenie wszystkich nagrań dla danego procesu języka Java.

#### <a name="continuous-recording"></a>Ciągłe nagrywanie

Rejestratora lotów Zulu można użyć do ciągłego profilowania aplikacji Java z minimalnym wpływem na wydajność środowiska uruchomieniowego ([Źródło](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Aby to zrobić, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure w celu utworzenia ustawienia aplikacji o nazwie JAVA_OPTS z niezbędną konfiguracją. Zawartość ustawienia aplikacji JAVA_OPTS są przesyłane do `java` polecenia po uruchomieniu aplikacji.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Po rozpoczęciu rejestrowania można zrzucić bieżące dane rejestrowania w dowolnym momencie za pomocą `JFR.dump` polecenia.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Analizowanie `.jfr` plików

Użyj [FTPS](deploy-ftp.md) , aby pobrać plik JFR na komputer lokalny. Aby przeanalizować plik JFR, Pobierz i zainstaluj [sterowanie misjami Zulu](https://www.azul.com/products/zulu-mission-control/). Aby uzyskać instrukcje na temat sterowania Zulu, zapoznaj się z [dokumentacją Azul](https://docs.azul.com/zmc/) i [instrukcjami dotyczącymi instalacji](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="app-logging"></a>Rejestrowanie aplikacji

::: zone pivot="platform-windows"

Włącz [Rejestrowanie aplikacji](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) za pomocą Azure Portal lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/webapp/log#az-webapp-log-config) w celu skonfigurowania App Service zapisywania strumieni błędów konsoli standardowej i standardowego programu na potrzeby aplikacji w lokalnym systemie plików lub BLOB Storage platformy Azure. Rejestrowanie w lokalnym wystąpieniu systemu plików App Service jest wyłączone 12 godzin od jego skonfigurowania. Jeśli potrzebujesz już przechowywania, skonfiguruj aplikację do zapisywania danych wyjściowych do kontenera magazynu obiektów BLOB. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */Home/LogFiles/Application/* .

::: zone-end
::: zone pivot="platform-linux"

Włącz [Rejestrowanie aplikacji](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) za pomocą Azure Portal lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/webapp/log#az-webapp-log-config) w celu skonfigurowania App Service zapisywania strumieni błędów konsoli standardowej i standardowego programu na potrzeby aplikacji w lokalnym systemie plików lub BLOB Storage platformy Azure. Jeśli potrzebujesz już przechowywania, skonfiguruj aplikację do zapisywania danych wyjściowych do kontenera magazynu obiektów BLOB. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */Home/LogFiles/Application/* .

Rejestrowanie Blob Storage platformy Azure dla App Services opartych na systemie Linux można skonfigurować tylko przy użyciu [Azure monitor (wersja zapoznawcza)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Jeśli aplikacja korzysta z [Logback](https://logback.qos.ch/) lub [Log4J](https://logging.apache.org/log4j) do śledzenia, można przekazać te ślady do przeglądu na platformie Azure Application Insights przy użyciu instrukcji konfiguracyjnych platformy rejestrowania w temacie [Eksplorowanie dzienników śledzenia Java w Application Insights](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Azure App Service dla systemu Linux obsługuje Dostosowywanie i Dostosowywanie pól za pomocą Azure Portal i interfejsu wiersza polecenia. Zapoznaj się z następującymi artykułami dotyczącymi konfiguracji aplikacji sieci Web niezależnej od języka Java:

- [Konfigurowanie ustawień aplikacji](configure-common.md#configure-app-settings)
- [Skonfiguruj domenę niestandardową](app-service-web-tutorial-custom-domain.md)
- [Konfigurowanie powiązań SSL](configure-ssl-bindings.md)
- [Dodawanie sieci CDN](../cdn/cdn-add-to-web-app.md)
- [Skonfiguruj witrynę kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Ustawianie opcji środowiska uruchomieniowego języka Java

Aby ustawić przydzieloną pamięć lub inne opcje środowiska uruchomieniowego JVM, Utwórz [ustawienie aplikacji](configure-common.md#configure-app-settings) o nazwie `JAVA_OPTS` z opcjami. App Service przekazuje to ustawienie jako zmienną środowiskową do środowiska uruchomieniowego Java podczas jego uruchamiania.

W Azure Portal w obszarze **Ustawienia aplikacji** dla aplikacji sieci Web Utwórz nowe ustawienie aplikacji o nazwie, `JAVA_OPTS` które zawiera dodatkowe ustawienia, na przykład `-Xms512m -Xmx1204m` .

Aby skonfigurować ustawienie aplikacji z wtyczki Maven, Dodaj Tagi ustawienia/wartość w sekcji wtyczka platformy Azure. W poniższym przykładzie ustawiono określony minimalny i maksymalny rozmiar sterty Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Deweloperzy korzystający z jednej aplikacji z jednym miejscem wdrożenia w ramach planu App Service mogą korzystać z następujących opcji:

- Wystąpienia B1 i S1: `-Xms1024m -Xmx1024m`
- Wystąpienia B2 i S2: `-Xms3072m -Xmx3072m`
- Wystąpienia B3 i S3: `-Xms6144m -Xmx6144m`

Podczas dostrajania ustawień sterty aplikacji Przejrzyj szczegóły planu App Service i Uwzględnij wiele aplikacji oraz miejsce wdrożenia, aby znaleźć optymalną alokację pamięci.

### <a name="turn-on-web-sockets"></a>Włącz gniazda sieci Web

Włącz obsługę gniazd sieci Web w Azure Portal w **ustawieniach aplikacji** dla aplikacji. Aby ustawienie zaczęło obowiązywać, należy ponownie uruchomić aplikację.

Włącz obsługę funkcji gniazdo sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure z następującym poleceniem:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Następnie uruchom ponownie aplikację:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ustaw domyślne kodowanie znaków

W Azure Portal w obszarze **Ustawienia aplikacji** dla aplikacji sieci Web Utwórz nowe ustawienie aplikacji o nazwie `JAVA_OPTS` z wartością `-Dfile.encoding=UTF-8` .

Alternatywnie można skonfigurować ustawienie aplikacji przy użyciu wtyczki App Service Maven. Dodaj nazwę ustawienia i Tagi wartości w konfiguracji wtyczki:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Pliki JSP przed kompilacją

Aby zwiększyć wydajność aplikacji Tomcat, możesz skompilować pliki JSP przed wdrożeniem do App Service. Możesz użyć [wtyczki Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) dostarczonej przez Apache Sling lub przy użyciu tego [pliku kompilacji ANT](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpieczanie aplikacji

Aplikacje Java działające w App Service mają ten sam zestaw [najlepszych](../security/fundamentals/paas-applications-using-app-services.md) rozwiązań w zakresie zabezpieczeń, co inne aplikacje.

### <a name="authenticate-users-easy-auth"></a>Uwierzytelnianie użytkowników (łatwa autoryzacja)

Skonfiguruj uwierzytelnianie aplikacji w Azure Portal przy użyciu opcji **uwierzytelnianie i autoryzacja** . W tym miejscu możesz włączyć uwierzytelnianie przy użyciu usług Azure Active Directory lub towarzyskich, takich jak Facebook, Google i GitHub. Konfiguracja Azure Portal działa tylko podczas konfigurowania jednego dostawcy uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji App Service do korzystania z Azure Active Directory logowania](configure-authentication-provider-aad.md) i pokrewnych artykułów dla innych dostawców tożsamości. Jeśli musisz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami w artykule [dostosowywanie App Service Authentication](app-service-authentication-how-to.md) .

#### <a name="java-se"></a>Java SE

Deweloperzy rozruchu sprężynowego mogą korzystać z [Azure Active Directory sprężynowego rozruchu Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) do zabezpieczania aplikacji przy użyciu dobrze znanych adnotacji i interfejsów API zabezpieczeń. Pamiętaj, aby zwiększyć maksymalny rozmiar nagłówka w pliku *Application. Properties* . Sugerujemy wartość `16384` .

#### <a name="tomcat"></a>Tomcat

Aplikacja Tomcat może uzyskać dostęp do oświadczeń użytkownika bezpośrednio z serwletu przez rzutowanie obiektu podmiotu zabezpieczeń na obiekt mapy. Obiekt mapy będzie mapować każdy typ oświadczenia do kolekcji oświadczeń dla tego typu. W poniższym kodzie `request` jest wystąpienie `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Teraz można sprawdzić `Map` obiekt pod kątem określonego żądania. Na przykład poniższy fragment kodu iteruje przez wszystkie typy roszczeń i drukuje zawartość każdej kolekcji.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Aby wylogować użytkowników, użyj `/.auth/ext/logout` ścieżki. Aby wykonać inne czynności, zapoznaj się z dokumentacją dotyczącą [korzystania z App Service uwierzytelniania i autoryzacji](./app-service-authentication-how-to.md). Dostępna jest również Oficjalna dokumentacja [interfejsu Tomcat HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) i jego metod. Następujące metody serwletu są również odwodnione na podstawie konfiguracji App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Aby wyłączyć tę funkcję, należy utworzyć ustawienie aplikacji o nazwie `WEBSITE_AUTH_SKIP_PRINCIPAL` z wartością `1` . Aby wyłączyć wszystkie filtry serwletu dodane przez App Service, Utwórz ustawienie o nazwie `WEBSITE_SKIP_FILTERS` z wartością `1` .

### <a name="configure-tlsssl"></a>Konfigurowanie protokołu TLS/SSL

Postępuj zgodnie z instrukcjami w polu [Zabezpiecz niestandardową nazwę DNS z powiązaniem SSL w Azure App Service](configure-ssl-bindings.md) , aby przekazać istniejący certyfikat SSL i powiązać go z nazwą domeny aplikacji. Domyślnie aplikacja będzie nadal zezwalać na połączenia HTTP — wykonaj określone czynności opisane w samouczku, aby wymusić stosowanie protokołów SSL i TLS.

### <a name="use-keyvault-references"></a>Użyj odwołań do magazynu kluczy

[Magazyn kluczy platformy Azure](../key-vault/general/overview.md) oferuje scentralizowane zarządzanie kluczami tajnymi przy użyciu zasad dostępu i historii inspekcji. Wpisy tajne (takie jak hasła lub parametry połączeń) można przechowywać w magazynie kluczy i uzyskiwać do nich dostęp w aplikacjach przy użyciu zmiennych środowiskowych.

Najpierw postępuj zgodnie z instrukcjami dotyczącymi [udzielania dostępu aplikacji do Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i [dokonywania odwołania do magazynu kluczy w ustawieniach aplikacji](app-service-key-vault-references.md#reference-syntax). Można sprawdzić, czy odwołanie jest rozpoznawane jako wpis tajny, drukując zmienną środowiskową podczas zdalnego uzyskiwania dostępu do terminalu App Service.

Aby wstrzyknąć te wpisy tajne w pliku konfiguracji wiosennej lub Tomcat, użyj składni iniekcji zmiennych środowiskowych ( `${MY_ENV_VAR}` ). W przypadku plików konfiguracji wiosennej zapoznaj się z tą dokumentacją w temacie [konfiguracje zewnętrzne](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Korzystanie z magazynu kluczy języka Java

Domyślnie wszystkie certyfikaty publiczne lub prywatne [przekazane do App Service Linux](configure-ssl-certificate.md) zostaną załadowane do odpowiednich magazynów kluczy Java podczas uruchamiania kontenera. Po przekazaniu certyfikatu należy ponownie uruchomić App Service, aby można go było załadować do magazynu kluczy Java. Certyfikaty publiczne są ładowane do magazynu kluczy w programie `$JAVA_HOME/jre/lib/security/cacerts` , a certyfikaty prywatne są przechowywane w usłudze `$JAVA_HOME/lib/security/client.jks` .

Dodatkowa konfiguracja może być niezbędna do szyfrowania połączenia usługi JDBC przy użyciu certyfikatów w magazynie kluczy języka Java. Zapoznaj się z dokumentacją wybranego sterownika JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Inicjowanie magazynu kluczy języka Java

Aby zainicjować `import java.security.KeyStore` obiekt, Załaduj plik magazynu kluczy z hasłem. Domyślnym hasłem dla obu magazynów kluczy jest "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Ręczne ładowanie magazynu kluczy

Certyfikaty można ładować ręcznie do magazynu kluczy. Utwórz ustawienie aplikacji `SKIP_JAVA_KEYSTORE_LOAD` o wartości, `1` aby wyłączyć App Service ładowania certyfikatów do magazynu kluczy automatycznie. Wszystkie certyfikaty publiczne przekazane do App Service za pośrednictwem Azure Portal są przechowywane w obszarze `/var/ssl/certs/` . Certyfikaty prywatne są przechowywane w obszarze `/var/ssl/private/` .

Możesz korzystać z narzędzia klucza Java lub debugować je, [otwierając połączenie SSH](configure-linux-open-ssh-session.md) do App Service i uruchamiając polecenie `keytool` . Listę poleceń można znaleźć w dokumentacji dotyczącej [najważniejszych narzędzi](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Aby uzyskać więcej informacji na temat interfejsu API magazynu kluczy, zapoznaj się z [oficjalną dokumentacją](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

::: zone-end

## <a name="configure-apm-platforms"></a>Konfigurowanie platform APM

W tej sekcji przedstawiono sposób łączenia aplikacji Java wdrożonych w systemie Azure App Service w systemie Linux z platformami NewRelic i AppDynamics Application Performance Monitoring (APM).

### <a name="configure-new-relic"></a>Konfiguruj nowe Relic

::: zone pivot="platform-windows"

1. Utwórz konto NewRelic na [newrelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z NewRelic, będzie on miał nazwę pliku podobną do *newrelic-java-x.x.x.zip*.
3. Skopiuj klucz licencji, który będzie potrzebny do późniejszego skonfigurowania agenta.
4. Użyj protokołu [SSH do wystąpienia App Service](configure-linux-open-ssh-session.md) i Utwórz nowy katalog */Home/site/wwwroot/APM*.
5. Przekaż pliki agenta NewRelic języka Java rozpakowane do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/newrelic*.
6. Zmodyfikuj plik YAML pod adresem */Home/site/wwwroot/APM/newrelic/newrelic.yml* i Zastąp wartość licencji zastępczej własnym kluczem licencji.
7. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.

    - W przypadku aplikacji **Java SE** Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` z wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Dla **Tomcat** Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` z wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Utwórz konto NewRelic na [newrelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z NewRelic, będzie on miał nazwę pliku podobną do *newrelic-java-x.x.x.zip*.
3. Skopiuj klucz licencji, który będzie potrzebny do późniejszego skonfigurowania agenta.
4. Użyj protokołu [SSH do wystąpienia App Service](configure-linux-open-ssh-session.md) i Utwórz nowy katalog */Home/site/wwwroot/APM*.
5. Przekaż pliki agenta NewRelic języka Java rozpakowane do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/newrelic*.
6. Zmodyfikuj plik YAML pod adresem */Home/site/wwwroot/APM/newrelic/newrelic.yml* i Zastąp wartość licencji zastępczej własnym kluczem licencji.
7. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.
   
    - W przypadku aplikacji **Java SE** Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` z wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Dla **Tomcat** Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` z wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Jeśli masz już zmienną środowiskową dla `JAVA_OPTS` lub `CATALINA_OPTS` , Dołącz `-javaagent:/...` opcję do końca bieżącej wartości.

### <a name="configure-appdynamics"></a>Konfigurowanie AppDynamics

::: zone pivot="platform-windows"

1. Utwórz konto AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java z witryny internetowej AppDynamics nazwa pliku będzie podobna do *AppServerAgent-x.x.x.xxxxx.zip*
3. Za pomocą [konsoli kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) Utwórz nowy katalog */Home/site/wwwroot/APM*.
4. Przekaż pliki agenta Java do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/AppDynamics*.
5. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.

   - W przypadku aplikacji **Java SE** Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` z wartością, `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` gdzie `<app-name>` to nazwa App Service.
   - W przypadku aplikacji **Tomcat** Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` z wartością, `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` gdzie `<app-name>` to nazwa App Service.

::: zone-end
::: zone pivot="platform-linux"

1. Utwórz konto AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java z witryny internetowej AppDynamics nazwa pliku będzie podobna do *AppServerAgent-x.x.x.xxxxx.zip*
3. Użyj protokołu [SSH do wystąpienia App Service](configure-linux-open-ssh-session.md) i Utwórz nowy katalog */Home/site/wwwroot/APM*.
4. Przekaż pliki agenta Java do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/AppDynamics*.
5. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.

   - W przypadku aplikacji **Java SE** Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` z wartością, `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` gdzie `<app-name>` to nazwa App Service.
   - W przypadku aplikacji **Tomcat** Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` z wartością, `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` gdzie `<app-name>` to nazwa App Service.

::: zone-end

> [!NOTE]
>  Jeśli masz już zmienną środowiskową dla `JAVA_OPTS` lub `CATALINA_OPTS` , Dołącz `-javaagent:/...` opcję do końca bieżącej wartości.

## <a name="configure-data-sources"></a>Konfigurowanie źródeł danych

### <a name="java-se"></a>Java SE

Aby nawiązać połączenie ze źródłami danych w aplikacjach do rozruchu sprężynowego, zalecamy utworzenie parametrów połączenia i dodanie ich do pliku *Application. Properties* .

1. W sekcji "Konfiguracja" na stronie App Service Ustaw nazwę ciągu, wklej parametry połączenia JDBC w polu wartość i ustaw typ na "niestandardowy". Opcjonalnie możesz ustawić te parametry połączenia jako ustawienie gniazda.

    Te parametry połączenia są dostępne dla naszej aplikacji jako zmienna środowiskowa o nazwie `CUSTOMCONNSTR_<your-string-name>` . Na przykład parametry połączenia utworzone powyżej zostaną nazwane `CUSTOMCONNSTR_exampledb` .

2. W pliku *Application. Properties* odwołując się do tych parametrów połączenia z nazwą zmiennej środowiskowej. W naszym przykładzie będziemy używać następujących sposobów.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Aby uzyskać więcej informacji na temat tego tematu, zobacz [dokumentację dotyczącą rozruchu sprężynowego na stronie dostęp do danych](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) i [konfiguracje zewnętrzne](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Te instrukcje dotyczą wszystkich połączeń z bazą danych. Musisz wypełnić symbole zastępcze nazwą klasy sterownika wybranej bazy danych i plikiem JAR. Dostarczono tabelę z nazwami klas i pobraniami sterowników dla wspólnych baz danych.

| baza danych   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobieranie](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (wybierz pozycję "Platforma niezależna") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobieranie](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Aby skonfigurować Tomcat do korzystania z łączności z bazą danych Java (JDBC) lub interfejsu API trwałości Java (JPA), najpierw Dostosuj `CATALINA_OPTS` zmienną środowiskową, która jest odczytywana przez Tomcat podczas uruchamiania. Ustaw te wartości za pomocą ustawienia aplikacji w [dodatku App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Lub Ustaw zmienne środowiskowe na stronie   >  **Ustawienia aplikacji** konfiguracji w Azure Portal.

Następnie ustal, czy źródło danych powinno być dostępne dla jednej aplikacji, czy dla wszystkich aplikacji uruchomionych na Tomcat serwletu.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Utwórz plik *context.xml* w *META-INF/* katalogu projektu. Utwórz *plik META-INF/* katalog, jeśli nie istnieje.

2. W *context.xml* Dodaj element, `Context` Aby połączyć źródło danych z adresem JNDI. Zastąp `driverClassName` symbol zastępczy nazwą klasy sterownika z powyższej tabeli.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Zaktualizuj *web.xml* aplikacji tak, aby korzystała ze źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Na koniec umieścimy sterownik JARs w ścieżce klasy Tomcat i uruchomisz ponownie App Service. Upewnij się, że pliki sterowników JDBC są dostępne dla Tomcat ClassLoader, umieszczając je w katalogu */Home/Tomcat/lib* . (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do wystąpienia App Service, wykonaj następujące czynności:

1. W [Cloud Shell](https://shell.azure.com)Zainstaluj rozszerzenie webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Połącz się z lokalnym portem tunelowania przy użyciu klienta SFTP i przekaż pliki do folderu */Home/Tomcat/lib* .

Alternatywnie możesz użyć klienta FTP do przekazania sterownika JDBC. Postępuj zgodnie z tymi [instrukcjami w celu uzyskania poświadczeń FTP](deploy-configure-credentials.md).

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Te instrukcje dotyczą wszystkich połączeń z bazą danych. Musisz wypełnić symbole zastępcze nazwą klasy sterownika wybranej bazy danych i plikiem JAR. Dostarczono tabelę z nazwami klas i pobraniami sterowników dla wspólnych baz danych.

| baza danych   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobieranie](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (wybierz pozycję "Platforma niezależna") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobieranie](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Aby skonfigurować Tomcat do korzystania z łączności z bazą danych Java (JDBC) lub interfejsu API trwałości Java (JPA), najpierw Dostosuj `CATALINA_OPTS` zmienną środowiskową, która jest odczytywana przez Tomcat podczas uruchamiania. Ustaw te wartości za pomocą ustawienia aplikacji w [dodatku App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Lub Ustaw zmienne środowiskowe na stronie   >  **Ustawienia aplikacji** konfiguracji w Azure Portal.

Następnie ustal, czy źródło danych powinno być dostępne dla jednej aplikacji, czy dla wszystkich aplikacji uruchomionych na Tomcat serwletu.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Utwórz plik *context.xml* w *META-INF/* katalogu projektu. Utwórz *plik META-INF/* katalog, jeśli nie istnieje.

2. W *context.xml* Dodaj element, `Context` Aby połączyć źródło danych z adresem JNDI. Zastąp `driverClassName` symbol zastępczy nazwą klasy sterownika z powyższej tabeli.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Zaktualizuj *web.xml* aplikacji tak, aby korzystała ze źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Udostępnione zasoby na poziomie serwera

Dodanie udostępnionego źródła danych na poziomie serwera wymaga edytowania server.xml tomcat. Najpierw przekaż [skrypt uruchamiania](faq-app-service-linux.md#built-in-images) i Ustaw ścieżkę do skryptu w   >  **poleceniu uruchamiania** konfiguracji. Można przekazać skrypt uruchamiania przy użyciu [protokołu FTP](deploy-ftp.md).

Skrypt uruchamiania wykona [przekształcenie XSL](https://www.w3schools.com/xml/xsl_intro.asp) do pliku server.xml i wyprowadza wynikowy plik XML do `/usr/local/tomcat/conf/server.xml` . Skrypt uruchamiania powinien instalować libxslt za pośrednictwem APK. Plik XSL i skrypt uruchamiania można przekazać za pośrednictwem protokołu FTP. Poniżej znajduje się przykładowy skrypt uruchamiania.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Poniżej przedstawiono przykładowy plik XSL. Przykładowy plik XSL dodaje nowy węzeł łącznika do server.xml tomcat.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Na koniec Umieść sterownik JARs w ścieżce klasy Tomcat i ponownie uruchom App Service.

1. Upewnij się, że pliki sterowników JDBC są dostępne dla Tomcat ClassLoader, umieszczając je w katalogu */Home/Tomcat/lib* . (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do wystąpienia App Service, wykonaj następujące czynności:

    1. W [Cloud Shell](https://shell.azure.com)Zainstaluj rozszerzenie webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Połącz się z lokalnym portem tunelowania przy użyciu klienta SFTP i przekaż pliki do folderu */Home/Tomcat/lib* .

    Alternatywnie możesz użyć klienta FTP do przekazania sterownika JDBC. Postępuj zgodnie z tymi [instrukcjami w celu uzyskania poświadczeń FTP](deploy-configure-credentials.md).

2. Jeśli utworzono źródło danych na poziomie serwera, uruchom ponownie aplikację App Service Linux. Tomcat zostanie zresetowana `CATALINA_BASE` do programu `/home/tomcat` i użyta zostanie zaktualizowana konfiguracja.

### <a name="jboss-eap"></a>JBoss EAP

Podczas [rejestrowania źródła danych przy użyciu protokołu EAP JBoss](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)należy wykonać trzy podstawowe kroki: przekazywanie sterownika JDBC, Dodawanie sterownika JDBC jako modułu i Rejestrowanie modułu. App Service to bezstanowa usługa hostingu, dlatego polecenia konfiguracyjne służące do dodawania i rejestrowania modułu źródła danych muszą mieć skrypt i być stosowane podczas uruchamiania kontenera.

1. Uzyskaj sterownik JDBC bazy danych. 
2. Utwórz plik definicji modułu XML dla sterownika JDBC. Poniższy przykład jest definicją modułu dla PostgreSQL.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Umieść polecenia interfejsu CLI JBoss w pliku o nazwie `jboss-cli-commands.cli` . Polecenia JBoss muszą dodać moduł i zarejestrować go jako źródło danych. Poniższy przykład przedstawia polecenie JBoss interfejsu wiersza polecenia dla PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Utwórz skrypt uruchomieniowy, `startup_script.sh` który wywołuje polecenia interfejsu CLI JBoss. W poniższym przykładzie pokazano, jak wywołać `jboss-cli-commands.cli` . Później będzie configre App Service do uruchamiania tego skryptu podczas uruchamiania kontenera. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Za pomocą wybranego klienta FTP Przekaż swój sterownik JDBC,, `jboss-cli-commands.cli` `startup_script.sh` i definicję modułu do programu `/site/deployments/tools/` .
2. Skonfiguruj swoją lokację do uruchamiania `startup_script.sh` podczas uruchamiania kontenera. W witrynie Azure Portal przejdź do polecenia **Konfiguracja**  >  **Ogólne ustawienia**  >  **uruchamiania**. Ustaw pole polecenia uruchamiania na `/home/site/deployments/tools/startup_script.sh` . **Zapisz** zmiany.

Aby potwierdzić, że źródło danych zostało dodane do serwera JBoss, Użyj protokołu SSH do webapp i uruchom polecenie `$JBOSS_HOME/bin/jboss-cli.sh --connect` . Po nawiązaniu połączenia z usługą JBoss Uruchom, `/subsystem=datasources:read-resource` Aby wydrukować listę źródeł danych.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Wybieranie wersji środowiska uruchomieniowego Java

App Service umożliwia użytkownikom wybranie wersji głównej JVM, takiej jak Java 8 lub Java 11, a także wersji pomocniczej, takiej jak 1.8.0 _232 lub 11.0.5. Możesz również wybrać opcję automatycznej aktualizacji wersji pomocniczej, gdy staną się dostępne nowe wersje pomocnicze. W większości przypadków Lokacje produkcyjne powinny używać przypiętych mniejszych wersji JVM. Uniemożliwi to unnanticipated przestoju podczas samoaktualizacji wersji pomocniczej.

W przypadku wybrania opcji przypięcia wersji pomocniczej konieczne będzie okresowe Aktualizowanie wersji pomocniczej JVM w lokacji. Aby upewnić się, że aplikacja działa w nowszej wersji pomocniczej, Utwórz miejsce przejściowe i zwiększ wersję pomocniczą w lokacji tymczasowej. Po potwierdzeniu, że aplikacja działa prawidłowo w nowej wersji pomocniczej, można zamienić miejsca przejściowe i produkcyjne.

## <a name="jboss-eap-hardware-options"></a>JBoss opcje sprzętowe protokołu EAP

JBoss EAP jest dostępny tylko w opcjach sprzętowych Premium i izolowanych. Klienci, którzy utworzyli witrynę JBoss EAP w warstwie Bezpłatna, współdzielona, podstawowa lub standardowa w ramach publicznej wersji zapoznawczej, powinny skalować w górę do warstwy Premium lub izolowanej, aby uniknąć nieoczekiwanego zachowania.

## <a name="java-runtime-statement-of-support"></a>Instrukcja obsługi środowiska uruchomieniowego Java

### <a name="jdk-versions-and-maintenance"></a>JDK wersje i konserwacja

Obsługiwany zestaw Java Development Kit (JDK) platformy Azure jest [Zulu](https://www.azul.com/downloads/azure-only/zulu/) udostępniany przez [systemy Azul](https://www.azul.com/). Azul Zulu Enterprise builds of OpenJDK to bezpłatna, wieloplatformowa, przygotowana do produkcji dystrybucja OpenJDK dla platformy Azure i Azure Stackna przez systemy Microsoft i Azul. Zawierają one wszystkie składniki do kompilowania i uruchamiania aplikacji Java SE. Można zainstalować JDK z [instalacji języka Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support).

Aktualizacje wersji głównej zostaną udostępnione za poorednictwem nowych opcji środowiska uruchomieniowego w Azure App Service. Klienci mogą aktualizować te nowsze wersje środowiska Java przez skonfigurowanie wdrożenia App Service i są odpowiedzialne za testowanie i upewnienie się, że główna aktualizacja spełnia ich potrzeby.

Obsługiwane zestawy JDK są automatycznie poprawiane co kwartał w styczniu, kwietniu, lipcu i październiku każdego roku. Aby uzyskać więcej informacji na temat języka Java na platformie Azure, zobacz [ten dokument pomocy technicznej](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Poprawki i poprawki dla głównych luk w zabezpieczeniach zostaną wydane, gdy tylko staną się dostępne w systemach Azul. Usterka "główna" jest definiowana przez podstawowy wynik 9,0 lub wyższy w [systemie oceny typowych luk w zabezpieczeniach NIST, wersja 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8,0 osiągnął [koniec cyklu życia (EOL) od 30 września 2018](https://tomcat.apache.org/tomcat-80-eol.html). Mimo że środowisko uruchomieniowe jest nadal dostępne w Azure App Service, platforma Azure nie będzie stosować aktualizacji zabezpieczeń do tomcat 8,0. Jeśli to możliwe, dokonaj migracji aplikacji do tomcat 8,5 lub 9,0. W Azure App Service są dostępne zarówno Tomcat 8,5, jak i 9,0. Aby uzyskać więcej informacji, zobacz [oficjalną witrynę Tomcat](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Wycofanie i wycofanie

Jeśli obsługiwane środowisko uruchomieniowe języka Java zostanie wycofane, deweloperzy platformy Azure korzystający z środowiska uruchomieniowego, którego to dotyczy, otrzymają powiadomienie o wycofaniu z co najmniej sześciu miesięcy przed wycofaniem środowiska uruchomieniowego.


### <a name="local-development"></a>Programowanie lokalne

Deweloperzy mogą pobrać wersję produkcyjną Azul Zulu Enterprise JDK na potrzeby lokalnego opracowywania z [witryny pobierania programu Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Obsługa programowania

Pomoc techniczna dla platformy [Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna w firmie Microsoft podczas tworzenia aplikacji dla platformy azure lub [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) z [wykwalifikowanym planem pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Następne kroki

Odwiedź centrum [deweloperów platformy Azure dla języka Java](/java/azure/) , aby znaleźć Przewodniki Szybki Start, samouczki i dokumentację języka Java dla platformy Azure.

Ogólne pytania dotyczące korzystania z App Service dla systemu Linux, które nie są specyficzne dla programowania w języku Java, są odpowiedzi na [często zadawane pytania dotyczące systemu App Service Linux](faq-app-service-linux.md).
