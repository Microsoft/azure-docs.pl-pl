---
title: Konfigurowanie aplikacji Java dla systemu Linux
description: Dowiedz się, jak skonfigurować wstępnie utworzony kontener Java dla aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
keywords: usługa aplikacji azure, aplikacja internetowa, Linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: f4f6de807628704051cdddf74bcefbed678f8fcd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457896"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji Java systemu Linux dla usługi Azure App Service

Usługa Azure App Service w systemie Linux umożliwia deweloperom oprogramowania Java szybkie tworzenie, wdrażanie i skalowanie ich aplikacji sieci Web spakowanych w programie Tomcat lub Java Standard Edition (SE) w w pełni zarządzanej usłudze opartej na systemie Linux. Wdrażanie aplikacji z wtyczkami Maven z wiersza polecenia lub w edytorach, takich jak IntelliJ, Eclipse lub Visual Studio Code.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla deweloperów oprogramowania Java, którzy używają wbudowanego kontenera systemu Linux w usłudze App Service. Jeśli nigdy nie korzystałeś z usługi Azure App Service, postępuj zgodnie z [przewodnikiem Java Szybki start](quickstart-java.md).

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Za pomocą [wtyczki Maven dla usługi Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) można wdrożyć pliki .jar i .war. Wdrażanie z popularnymi środowiskami IDE jest również obsługiwane przez [zestaw narzędzi Azure dla intellij](/java/azure/intellij/azure-toolkit-for-intellij) lub zestaw narzędzi platformy Azure dla programu [Eclipse.](/java/azure/eclipse/azure-toolkit-for-eclipse)

W przeciwnym razie metoda wdrażania będzie zależeć od typu archiwum:

- Aby wdrożyć pliki .war w `/api/wardeploy/` tomcat, użyj punktu końcowego, aby opublikować plik archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Aby wdrożyć pliki .jar na obrazach `/api/zipdeploy/` Java SE, użyj punktu końcowego witryny Kudu. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nie należy wdrażać pliku .war lub .jar za pomocą protokołu FTP. Narzędzie FTP jest przeznaczone do przekazywania skryptów startowych, zależności lub innych plików środowiska uruchomieniowego. Nie jest to optymalny wybór do wdrażania aplikacji sieci web.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty wydajności, wizualizacje ruchu i sprawdzanie kondycji są dostępne dla każdej aplikacji za pośrednictwem witryny Azure portal. Aby uzyskać więcej informacji, zobacz [omówienie diagnostyki usługi Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji, zobacz [Dzienniki strumienia w usłudze Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Rejestrowanie aplikacji

Włącz [rejestrowanie aplikacji](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) za pośrednictwem witryny Azure portal lub [interfejsu wiersza polecenia platformy Azure,](/cli/azure/webapp/log#az-webapp-log-config) aby skonfigurować usługę App Service do zapisywania standardowych danych wyjściowych konsoli aplikacji i standardowych strumieni błędów konsoli do lokalnego systemu plików lub usługi Azure Blob Storage. Rejestrowanie do lokalnego wystąpienia systemu plików usługi App Service jest wyłączone 12 godzin po jego skonfigurowaniu. Jeśli potrzebujesz dłuższego przechowywania, skonfiguruj aplikację do zapisu danych wyjściowych w kontenerze magazynu obiektów Blob. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */home/LogFiles/Application/.*

Jeśli aplikacja używa [Logback](https://logback.qos.ch/) lub [Log4j](https://logging.apache.org/log4j) do śledzenia, można przesłać te ślady do przeglądu w usłudze Azure Application Insights przy użyciu instrukcji konfiguracji struktury rejestrowania w [eksploruj dzienniki śledzenia Java w usłudze Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów

Wbudowane obrazy Java są oparte na systemie operacyjnym [Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Użyj `apk` Menedżera pakietów, aby zainstalować wszystkie narzędzia do rozwiązywania problemów lub polecenia.

### <a name="flight-recorder"></a>Rejestrator lotu

Wszystkie obrazy Java Linuksa w usłudze App Service mają zainstalowany rejestrator lotu Zulu, dzięki czemu można łatwo połączyć się z JVM i rozpocząć nagrywanie profilera lub wygenerować zrzut sterty.

#### <a name="timed-recording"></a>Nagrywanie z czasem

Aby rozpocząć, SSH do usługi App `jcmd` Service i uruchom polecenie, aby wyświetlić listę wszystkich uruchomionych procesów Java. Oprócz samego jcmd, powinieneś zobaczyć aplikację Java działa z numerem identyfikatora procesu (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Wykonaj poniższe polecenie, aby rozpocząć 30-sekundowe nagrywanie JVM. Spowoduje to profil JVM i utworzenie pliku JFR o nazwie *jfr_example.jfr* w katalogu macierzystym. (Zastąp 116 pid aplikacji Java).

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

W ciągu 30-sekundowego interwału można sprawdzić, `jcmd 116 JFR.check`czy nagrywanie odbywa się, uruchamiając program . Spowoduje to wyświetlenie wszystkich nagrań dla danego procesu Java.

#### <a name="continuous-recording"></a>Nagrywanie ciągłe

Rejestrator lotu Zulu umożliwia ciągłe profilanie aplikacji Java przy minimalnym wpływie na wydajność środowiska uruchomieniowego[(źródło).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Aby to zrobić, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby utworzyć ustawienie aplikacji o nazwie JAVA_OPTS z niezbędną konfiguracją. Zawartość ustawienia aplikacji JAVA_OPTS są przekazywane do `java` polecenia po uruchomieniu aplikacji.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Po rozpoczęciu nagrywania można w dowolnym momencie zrzucić bieżące dane nagrywania za pomocą `JFR.dump` polecenia.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Aby uzyskać więcej informacji, zobacz [numer polecenia Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analizowanie nagrań

Użyj [protokołu FTPS,](../deploy-ftp.md) aby pobrać plik JFR na komputer lokalny. Aby przeanalizować plik JFR, pobierz i zainstaluj [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Instrukcje dotyczące zulu mission control można znaleźć w [dokumentacji Azul](https://docs.azul.com/zmc/) i [instrukcji instalacji.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Usługa Azure App Service dla systemu Linux obsługuje dostrajanie i dostosowywanie po wyjęciu z pudełka za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia. Przejrzyj następujące artykuły dotyczące konfiguracji aplikacji sieci Web niespecyfizacyjnej w języku Java:

- [Konfigurowanie ustawień aplikacji](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Konfigurowanie domeny niestandardowej](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurowanie powiązań SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Dodawanie sieci CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurowanie witryny Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ustawianie opcji środowiska wykonawczego języka Java

Aby ustawić przydzieloną pamięć lub inne opcje środowiska uruchomieniowego JVM w środowiskach `JAVA_OPTS` Tomcat i Java SE, należy utworzyć ustawienie [aplikacji](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) o nazwie z opcjami. Usługa App Service Linux przekazuje to ustawienie jako zmienną środowiskową do środowiska wykonawczego Java po uruchomieniu.

W witrynie Azure portal w obszarze **Ustawienia aplikacji** dla `JAVA_OPTS` aplikacji sieci web utwórz `-Xms512m -Xmx1204m`nowe ustawienie aplikacji o nazwie, które zawiera dodatkowe ustawienia, takie jak .

Aby skonfigurować ustawienie aplikacji z wtyczki Maven, dodaj tagi ustawień/wartości w sekcji wtyczki platformy Azure. W poniższym przykładzie określono określony minimalny i maksymalny rozmiar sterty języka Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Deweloperzy z jedną aplikacją z jednym miejscem wdrożenia w planie usługi App Service mogą korzystać z następujących opcji:

- Wystąpienia B1 i S1:`-Xms1024m -Xmx1024m`
- Wystąpienia B2 i S2:`-Xms3072m -Xmx3072m`
- Wystąpienia B3 i S3:`-Xms6144m -Xmx6144m`

Podczas dostrajania ustawień sterty aplikacji, przejrzyj szczegóły planu usługi App Service i weź pod uwagę wiele aplikacji i gniazda wdrożenia musi znaleźć optymalną alokację pamięci.

Jeśli wdrażasz aplikację JAR, powinna ona mieć nazwę *app.jar,* aby wbudowany obraz mógł poprawnie zidentyfikować aplikację. (Wtyczka Maven automatycznie zmienia nazwę. Jeśli nie chcesz zmieniać nazwy jar na *app.jar*, możesz przesłać skrypt powłoki z poleceniem uruchomienia JAR. Następnie wklej pełną ścieżkę do tego skryptu w polu tekstowym [Plik startowy](app-service-linux-faq.md#built-in-images) w sekcji Konfiguracja w portalu. Skrypt startowy nie jest uruchamiany z katalogu, w którym został umieszczony. W związku z tym zawsze należy używać ścieżek bezwzględnych do odwoływania się do plików w skrypcie startowym (na przykład: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Włączanie gniazd internetowych

Włącz obsługę gniazd sieci web w witrynie Azure portal w **ustawieniach aplikacji** dla aplikacji. Aby to ustawienie zostało zastosowane, musisz ponownie uruchomić aplikację.

Włącz obsługę gniazd sieci web przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą następującego polecenia:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Następnie uruchom ponownie aplikację:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ustawianie domyślnego kodowania znaków

W witrynie Azure portal w obszarze **Ustawienia aplikacji** dla `JAVA_OPTS` aplikacji `-Dfile.encoding=UTF-8`sieci web utwórz nowe ustawienie aplikacji o nazwie o nazwie .

Alternatywnie można skonfigurować ustawienie aplikacji za pomocą wtyczki Maven usługi aplikacji. Dodaj nazwę ustawienia i tagi wartości w konfiguracji wtyczki:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Dostosowywanie limitu czasu uruchamiania

Jeśli aplikacja Java jest szczególnie duża, należy zwiększyć limit czasu uruchamiania. Aby to zrobić, należy `WEBSITES_CONTAINER_START_TIME_LIMIT` utworzyć ustawienie aplikacji i ustawić go na liczbę sekund, że usługa App Service należy czekać przed przesuniem czasu. Maksymalna wartość `1800` to sekundy.

### <a name="pre-compile-jsp-files"></a>Wstępne kompilowanie plików JSP

Aby zwiększyć wydajność aplikacji Tomcat, można skompilować pliki JSP przed wdrożeniem w usłudze App Service. Możesz użyć [wtyczki Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) dostarczonej przez Apache Sling, lub za pomocą tego [pliku ant kompilacji](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpieczanie aplikacji

Aplikacje Java działające w usłudze App Service dla systemu Linux mają ten sam zestaw [najlepszych rozwiązań w zakresie zabezpieczeń,](/azure/security/security-paas-applications-using-app-services) co inne aplikacje.

### <a name="authenticate-users-easy-auth"></a>Uwierzytelnij użytkowników (Easy Auth)

Skonfiguruj uwierzytelnianie aplikacji w witrynie Azure portal z opcją **Uwierzytelnianie i autoryzacja.** W tym miejscu można włączyć uwierzytelnianie przy użyciu usługi Azure Active Directory lub logowania społecznościowe, takie jak Facebook, Google lub GitHub. Konfiguracja portalu Azure działa tylko podczas konfigurowania jednego dostawcy uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji usługi App Service do używania logowania usługi Azure Active Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) i powiązanych artykułów dla innych dostawców tożsamości. Jeśli chcesz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami w [artykule dostosowywania uwierzytelniania usługi App Service.](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json)

#### <a name="tomcat"></a>Tomcat

Aplikacja Tomcat może uzyskać dostęp do oświadczeń użytkownika bezpośrednio z serwletu, rzucając Principal obiektu do Map obiektu. Map obiektu mapuje każdy typ oświadczenia do kolekcji oświadczeń dla tego typu. W poniższym `request` kodzie znajduje `HttpServletRequest`się wystąpienie pliku .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Teraz możesz sprawdzić `Map` obiekt pod kątem określonego oświadczenia. Na przykład poniższy fragment kodu iteruje przez wszystkie typy oświadczeń i drukuje zawartość każdej kolekcji.

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

Aby wylogować `/.auth/ext/logout` użytkowników, użyj ścieżki. Aby wykonać inne akcje, zapoznaj się z dokumentacją dotyczącą [uwierzytelniania i autoryzacji usługi app service.](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) Istnieje również oficjalna dokumentacja na interfejsie Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) i jego metod. Następujące metody serwletu są również nawilżane na podstawie konfiguracji usługi App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Aby wyłączyć tę funkcję, należy `WEBSITE_AUTH_SKIP_PRINCIPAL` utworzyć ustawienie `1`aplikacji o nazwie o wartości . Aby wyłączyć wszystkie filtry serwletów dodane przez `WEBSITE_SKIP_FILTERS` usługę App `1`Service, utwórz ustawienie o nazwie o wartości .

#### <a name="spring-boot"></a>Spring Boot

Deweloperzy spring boot mogą używać [przystawki spring boot usługi Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) do zabezpieczania aplikacji przy użyciu znanych adnotacji zabezpieczeń sprężynowych i interfejsów API. Pamiętaj, aby zwiększyć maksymalny rozmiar nagłówka w pliku *application.properties.* Sugerujemy wartość `16384`.

### <a name="configure-tlsssl"></a>Konfigurowanie protokołu TLS/SSL

Postępuj zgodnie z instrukcjami w [Secure niestandardowej nazwy DNS z powiązaniem SSL w usłudze Azure App Service,](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) aby przekazać istniejący certyfikat SSL i powiązać go z nazwą domeny aplikacji. Domyślnie aplikacja będzie nadal zezwalać na połączenia HTTP-postępuj zgodnie z określonymi krokami w samouczku, aby wymusić SSL i TLS.

### <a name="use-keyvault-references"></a>Korzystanie z odwołań do keyvault

[Usługa Azure KeyVault](../../key-vault/general/overview.md) zapewnia scentralizowane zarządzanie tajne z zasadami dostępu i historią inspekcji. Wpisy tajne (takie jak hasła lub parametry połączenia) można przechowywać w funkcji KeyVault i uzyskiwać dostęp do tych wpisów tajnych w aplikacji za pośrednictwem zmiennych środowiskowych.

Najpierw postępuj zgodnie z instrukcjami [dotyczącymi udzielania aplikacji dostępu do usługi Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i [odwoływania się do klucza keyvault do klucza tajnego w ustawieniach aplikacji.](../app-service-key-vault-references.md#reference-syntax) Można sprawdzić, czy odwołanie jest rozpoznawane jako klucz tajny, drukując zmienną środowiskową podczas zdalnego uzyskiwania dostępu do terminalu usługi app service.

Aby wstrzyknąć te wpisy tajne w pliku konfiguracyjnym Spring lub Tomcat, należy użyć składni iniekcji zmiennej środowiskowej (`${MY_ENV_VAR}`). W przypadku plików konfiguracyjnych Sprężyn, zapoznaj się z niniejszą dokumentacją dotyczącą [konfiguracji zewnętrznych](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Korzystanie ze sklepu z kluczami Java

Domyślnie wszystkie certyfikaty publiczne lub prywatne [przesłane do usługi App Service Linux](../configure-ssl-certificate.md) zostaną załadowane do odpowiednich magazynów kluczy Java podczas uruchamiania kontenera. Po przesłaniu certyfikatu należy ponownie uruchomić usługę App Service, aby została załadowana do sklepu Java Key Store. Certyfikaty publiczne są ładowane do magazynu kluczy, `$JAVA_HOME/jre/lib/security/cacerts`a certyfikaty prywatne są przechowywane w `$JAVA_HOME/lib/security/client.jks`pliku .

Dodatkowa konfiguracja może być konieczna do szyfrowania połączenia JDBC za pomocą certyfikatów w Magazynie kluczy Java. Zapoznaj się z dokumentacją wybranego sterownika JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [Mongodb](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicjowanie sklepu z kluczami Java

Aby zainicjować `import java.security.KeyStore` obiekt, załaduj plik magazynu kluczy z hasłem. Domyślnym hasłem dla obu magazynów kluczy jest "changeit".

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

Certyfikaty można ładować ręcznie do magazynu kluczy. Utwórz ustawienie `SKIP_JAVA_KEYSTORE_LOAD`aplikacji, z `1` wartością wyłączenia usługi App Service z automatycznego ładowania certyfikatów do magazynu kluczy. Wszystkie certyfikaty publiczne przesłane do usługi App `/var/ssl/certs/`Service za pośrednictwem witryny Azure portal są przechowywane w obszarze . Certyfikaty prywatne `/var/ssl/private/`są przechowywane w obszarze .

Narzędzie klucza Java można wchodzić w interakcje lub debugować, otwierając `keytool`połączenie [SSH z usługą](app-service-linux-ssh-support.md) App Service i uruchamiając polecenie . Lista poleceń znajduje się w [dokumentacji narzędzia klucza.](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) Aby uzyskać więcej informacji na temat interfejsu API KeyStore, zapoznaj się [z oficjalną dokumentacją](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Konfigurowanie platform APM

W tej sekcji pokazano, jak połączyć aplikacje Java wdrożone w usłudze Azure App Service w systemie Linux za pomocą platform monitorowania wydajności aplikacji NewRelic i AppDynamics (APM).

### <a name="configure-new-relic"></a>Konfigurowanie nowej relikwii

1. Utwórz konto NewRelic w [NewRelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z NewRelic, będzie miał nazwę pliku podobną do *newrelic-java-x.x.x.zip*.
3. Skopiuj klucz licencyjny, będziesz go potrzebować, aby skonfigurować agenta później.
4. [SSH do wystąpienia usługi App Service](app-service-linux-ssh-support.md) i utworzyć nowy katalog */home/site/wwwroot/apm*.
5. Prześlij rozpakowane pliki agenta NewRelic Java do katalogu pod */home/site/wwwroot/apm*. Pliki dla twojego agenta powinny znajdować się w */home/site/wwwroot/apm/newrelic*.
6. Zmodyfikuj plik YAML w *pliku /home/site/wwwroot/apm/newrelic/newrelic.yml* i zastąp wartość licencji zastępczej własnym kluczem licencyjnym.
7. W witrynie Azure portal przejdź do aplikacji w usłudze App Service i utwórz nowe ustawienie aplikacji.
    - Jeśli aplikacja korzysta z **oprogramowania Java SE,** utwórz zmienną środowiskową o nazwie `JAVA_OPTS` o wartości `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Jeśli używasz **Tomcat,** utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` o wartości `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Konfigurowanie aplikacji AppDynamics

1. Utwórz konto AppDynamics w [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java ze strony AppDynamics, nazwa pliku będzie podobna do *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH do wystąpienia usługi App Service](app-service-linux-ssh-support.md) i utworzyć nowy katalog */home/site/wwwroot/apm*.
4. Prześlij pliki agenta Java do katalogu w obszarze */home/site/wwwroot/apm*. Pliki dla twojego agenta powinny znajdować się w */home/site/wwwroot/apm/appdynamics*.
5. W witrynie Azure portal przejdź do aplikacji w usłudze App Service i utwórz nowe ustawienie aplikacji.
    - Jeśli używasz **Java SE,** utwórz `JAVA_OPTS` zmienną `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` środowiskową o nazwie o wartości, w której `<app-name>` jest nazwa usługi App Service.
    - Jeśli używasz **Tomcat,** utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` o wartości, `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` w której `<app-name>` jest nazwa usługi App Service.

> [!NOTE]
> Jeśli masz już zmienną `JAVA_OPTS` `CATALINA_OPTS`środowiskową dla `-javaagent:/...` lub , dołącz opcję na końcu bieżącej wartości.

## <a name="configure-jar-applications"></a>Konfigurowanie aplikacji JAR

### <a name="starting-jar-apps"></a>Uruchamianie aplikacji JAR

Domyślnie usługa App Service oczekuje, że aplikacja JAR będzie nazywana *app.jar*. Jeśli ma tę nazwę, zostanie uruchomiony automatycznie. Dla użytkowników Maven można ustawić nazwę `<finalName>app</finalName>` JAR, `<build>` włączając ją w sekcję *pom.xml*. [To samo można zrobić w Gradle,](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) ustawiając `archiveFileName` właściwość.

Jeśli chcesz użyć innej nazwy dla jar, należy również podać [polecenie uruchamiania,](app-service-linux-faq.md#built-in-images) które wykonuje plik JAR. Na przykład `java -jar my-jar-app.jar`. Wartość polecenia uruchamiania można ustawić w portalu, w obszarze Konfiguracja > ustawienia ogólne `STARTUP_COMMAND`lub przy o nazwie Ustawienie aplikacji .

### <a name="server-port"></a>Port serwera

Usługa App Service Linux kieruje przychodzące żądania do portu 80, więc aplikacja powinna nasłuchiwać również na porcie 80. Można to zrobić w konfiguracji aplikacji (na przykład plik *application.properties* programu Spring) lub `java -jar spring-app.jar --server.port=80`w poleceniu uruchamiania (na przykład ). Zapoznaj się z następującą dokumentacją dla typowych struktur Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [IskraJava](http://sparkjava.com/documentation#embedded-web-server)
- [Mikronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Struktura gry](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx ( Vertx )](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Kwarkus ( Quarkus )](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Źródła danych

### <a name="tomcat"></a>Tomcat

Te instrukcje dotyczą wszystkich połączeń z bazą danych. Należy wypełnić symbole zastępcze nazwą klasy sterownika wybranej bazy danych i plikiem JAR. Podana jest tabela z nazwami klas i pobieraniem sterowników dla typowych baz danych.

| baza danych   | Nazwa klasy kierowcy                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobierz](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (wybierz "Platform Independent") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobierz](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Aby skonfigurować firmę Tomcat do używania interfejsu Java Database Connectivity (JDBC) `CATALINA_OPTS` lub interfejsu API Java Persistence (JPA), należy najpierw dostosować zmienną środowiskową odczytywany przez firmę Tomcat podczas uruchamiania. Ustaw te wartości za pomocą ustawienia aplikacji w [wtyczce Maven usługi aplikacji:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Możesz też ustawić zmienne środowiskowe na stronie**Ustawienia aplikacji** **konfiguracji** > w witrynie Azure portal.

Następnie należy określić, czy źródło danych powinno być dostępne dla jednej aplikacji lub dla wszystkich aplikacji uruchomionych na serwletu Tomcat.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Utwórz plik *context.xml* w katalogu *META-INF/* projektu. Utwórz katalog *META-INF/,* jeśli nie istnieje.

2. W *pliku context.xml*dodaj `Context` element, aby połączyć źródło danych z adresem JNDI. Zastąp symbol zastępczy `driverClassName` nazwą klasy kierowcy z powyższej tabeli.

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

3. Zaktualizuj *plik web.xml* aplikacji, aby używał źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Zasoby na poziomie serwera udostępnionego

Dodanie udostępnionego źródła danych na poziomie serwera będzie wymagało edycji pliku Tomcat server.xml. Najpierw przekaż [skrypt startowy](app-service-linux-faq.md#built-in-images) i ustaw ścieżkę do skryptu w **configuration startup** > **command**. Skrypt startowy można przesłać za pomocą [protokołu FTP](../deploy-ftp.md).

Skrypt startowy spowoduje [przekształcenie xsl](https://www.w3schools.com/xml/xsl_intro.asp) do pliku server.xml i `/usr/local/tomcat/conf/server.xml`wysunie wynikowy plik xml do pliku . Skrypt startowy powinien zainstalować libxslt za pośrednictwem apk. Twój plik xsl i skrypt startowy można przesłać za pośrednictwem FTP. Poniżej znajduje się przykładowy skrypt startowy.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Przykładowy plik xsl znajduje się poniżej. Przykładowy plik xsl dodaje nowy węzeł łącznika do pliku Tomcat server.xml.

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

Na koniec umieść jar sterowników w ścieżce klasy Tomcat i uruchom ponownie usługę App Service.

1. Upewnij się, że pliki sterowników JDBC są dostępne dla classloader Tomcat, umieszczając je w *katalogu /home/tomcat/lib.* (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do wystąpienia usługi App Service, wykonaj następujące czynności:

    1. W [aplikacji Cloud Shell](https://shell.azure.com)zainstaluj rozszerzenie aplikacji webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do usługi App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Połącz się z lokalnym portem tunelowania za pomocą klienta SFTP i prześlij pliki do folderu */home/tomcat/lib.*

    Alternatywnie można użyć klienta FTP, aby przekazać sterownik JDBC. Postępuj zgodnie z tymi [instrukcjami, aby uzyskać poświadczenia FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Jeśli utworzono źródło danych na poziomie serwera, uruchom ponownie aplikację App Service Linux. Tomcat zresetuje `CATALINA_BASE` i `/home/tomcat` użyje zaktualizowanej konfiguracji.

### <a name="spring-boot"></a>Spring Boot

Aby połączyć się ze źródłami danych w aplikacjach Spring Boot, zalecamy utworzenie ciągów połączeń i wstrzyk wstrzyk w pliku *application.properties.*

1. W sekcji "Konfiguracja" na stronie usługi app service ustaw nazwę ciągu, wklej ciąg połączenia JDBC do pola wartości i ustaw typ na "Niestandardowy". Opcjonalnie można ustawić ten ciąg połączenia jako ustawienie gniazda.

    Ten ciąg połączenia jest dostępny dla naszej `CUSTOMCONNSTR_<your-string-name>`aplikacji jako zmienna środowiskowa o nazwie . Na przykład parametry połączenia, które utworzyliśmy powyżej, będą nazwane `CUSTOMCONNSTR_exampledb`.

2. W pliku *application.properties* odwołaj się do tego ciągu połączenia o nazwie zmiennej środowiskowej. W naszym przykładzie użyjemy następujących.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Aby uzyskać więcej informacji na ten temat, zapoznaj się [z dokumentacją wiosennego rozruchu dotyczącą dostępu do danych](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) i konfiguracji [zewnętrznych.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Używanie redis jako pamięci podręcznej sesji z tomcat

Program Tomcat można skonfigurować do używania zewnętrznego magazynu sesji, takiego jak [Azure Cache for Redis.](/azure/azure-cache-for-redis/) Dzięki temu można zachować stan sesji użytkownika (na przykład dane koszyka) po przeniesieniu użytkownika do innego wystąpienia aplikacji, na przykład podczas skalowania automatycznego, ponownego uruchamiania lub pracy awaryjnej występuje.

Aby użyć Tomcat z Redis, należy skonfigurować aplikację do korzystania z [implementacji PersistentManager.](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) Następujące kroki wyjaśniają ten proces przy użyciu [menedżera sesji kluczowej: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) jako przykład.

1. Otwórz terminal Bash `<variable>=<value>` i użyj, aby ustawić każdą z następujących zmiennych środowiskowych.

    | Zmienna                 | Wartość                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Nazwa grupy zasobów zawierającej wystąpienie usługi App Service.       |
    | WEBAPP_NAME              | Nazwa wystąpienia usługi App Service.                                     |
    | WEBAPP_PLAN_NAME         | Nazwa planu usługi aplikacji.                                         |
    | REGION                   | Nazwa regionu, w którym znajduje się aplikacja.                           |
    | REDIS_CACHE_NAME         | Nazwa pamięci podręcznej azure dla wystąpienia Redis.                           |
    | REDIS_PORT               | Port SSL, na który nasłuchuje pamięć podręczna Redis.                             |
    | REDIS_PASSWORD           | Podstawowy klucz dostępu dla wystąpienia.                                  |
    | REDIS_SESSION_KEY_PREFIX | Wartość określona w celu zidentyfikowania kluczy sesji pochodzących z aplikacji. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Informacje o nazwie, porcie i kluczu dostępu można znaleźć w witrynie Azure Portal, w sekcji **Właściwości** lub **Klucze dostępu** wystąpienia usługi.

2. Utwórz lub zaktualizuj plik *src/main/webapp/META-INF/context.xml* aplikacji o następującą zawartość:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Ten plik określa i konfiguruje implementację menedżera sesji dla aplikacji. Używa zmiennych środowiskowych ustawionych w poprzednim kroku, aby zachować informacje o koncie z plików źródłowych.

3. Za pomocą protokołu FTP można przekazać plik JAR menedżera sesji do wystąpienia usługi App Service, umieszczając go w katalogu */home/tomcat/lib.* Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji w usłudze Azure App Service przy użyciu protokołu FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Wyłącz [plik cookie koligacji sesji](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) dla wystąpienia usługi App Service. Można to zrobić w witrynie Azure portal, przechodząc do aplikacji, a następnie ustawiając **ustawienia konfiguracyjne > ogólne > koligacji ARR** na **Wyłączone**. Alternatywnie można użyć następującego polecenia:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Domyślnie usługa App Service będzie używać plików cookie koligacji sesji, aby upewnić się, że żądania klientów z istniejącymi sesjami są kierowane do tego samego wystąpienia aplikacji. To domyślne zachowanie nie wymaga konfiguracji, ale nie może zachować stanu sesji użytkownika po ponownym uruchomieniu wystąpienia aplikacji lub przekierowaniu ruchu do innego wystąpienia. Po [wyłączeniu istniejącej konfiguracji koligacji wystąpienia ARR,](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) aby wyłączyć routing oparty na plikach cookie sesji, zezwalasz skonfigurowanego magazynu sesji na działanie bez zakłóceń.

5. Przejdź do sekcji **Właściwości** wystąpienia usługi App Service i znajdź **dodatkowe wychodzące adresy IP**. Reprezentują one wszystkie możliwe wychodzące adresy IP dla aplikacji. Skopiuj je do użycia w następnym kroku.

6. Dla każdego adresu IP utwórz regułę zapory w pamięci podręcznej platformy Azure dla wystąpienia Redis. Można to zrobić w witrynie Azure portal z sekcji **Zapora** wystąpienia Redis. Podaj unikatową nazwę dla każdej reguły i ustaw wartości **początkowego adresu IP** i **końcowego adresu IP** na ten sam adres IP.

7. Przejdź do sekcji **Ustawienia zaawansowane** w instancji Redis i ustaw **zezwalaj na dostęp tylko za pośrednictwem protokołu SSL** na **Nie**. Dzięki temu wystąpienie usługi App Service do komunikowania się z pamięci podręcznej Redis za pośrednictwem infrastruktury platformy Azure.

8. Zaktualizuj konfigurację `azure-webapp-maven-plugin` w pliku *pom.xml* aplikacji, aby zapoznać się z informacjami o koncie Redis. Ten plik używa wcześniej ustawionych zmiennych środowiskowych w celu przechowywania informacji o koncie poza plikami źródłowymi.

    W razie potrzeby zmień `1.7.0` na bieżącą wersję [wtyczki Maven dla usługi Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Przebuduj i ponownie wdrożyć aplikację.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Aplikacja będzie teraz używać pamięci podręcznej Redis do zarządzania sesjami.

Przykład, którego można użyć do przetestowania tych instrukcji, zobacz repozytorium [scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) w usłudze GitHub.

## <a name="docker-containers"></a>Kontenerów Docker

Aby korzystać z obsługiwanej przez platformę Azure narzędzia Zulu JDK w kontenerach, należy pobrać i używać wstępnie utworzonych obrazów `Dockerfile` zgodnie z dokumentami na [obsługiwanej stronie pobierania Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/) lub skorzystaj z przykładów z [repozytorium Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Deklaracja poparcia

### <a name="runtime-availability"></a>Dostępność środowiska uruchomieniowego

Usługa App Service dla systemu Linux obsługuje dwa środowiska wykonawcze dla zarządzanego hostingu aplikacji internetowych Java:

- [Kontener serwletu Tomcat](https://tomcat.apache.org/) do uruchamiania aplikacji spakowanych jako pliki archiwum internetowego (WAR). Obsługiwane wersje to 8.5 i 9.0.
- Środowisko wykonawcze Java SE do uruchamiania aplikacji spakowanych jako pliki archiwum Java (JAR). Obsługiwane wersje to Java 8 i 11.

### <a name="jdk-versions-and-maintenance"></a>Wersje JDK i konserwacja

Kompilacje OpenJDK firmy Azul Zulu Enterprise to bezpłatna, wieloplatformowa, gotowa do produkcji dystrybucja OpenJDK dla platformy Azure i azure stack wspierana przez firmy Microsoft i Azul Systems. Zawierają one wszystkie składniki do kompilowania i uruchamiania aplikacji Java SE. Zestaw JDK można zainstalować z [instalacji Java JDK](https://aka.ms/azure-jdks).

Obsługiwane JDK są automatycznie poprawiane co kwartał w styczniu, kwietniu, lipcu i październiku każdego roku.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Poprawki i poprawki dotyczące poważnych luk w zabezpieczeniach zostaną wydane, gdy tylko staną się dostępne w Azul Systems. "Poważna" luka w zabezpieczeniach jest definiowana przez wynik podstawowy 9.0 lub wyższy w [systemie NIST Common Vulnerability Scoring System w wersji 2.](https://nvd.nist.gov/cvss.cfm)

### <a name="deprecation-and-retirement"></a>Wycofywanie i wycofywanie

Jeśli obsługiwane środowisko uruchomieniowe java zostanie wycofane, deweloperzy platformy Azure przy użyciu środowiska wykonawczego, którego dotyczy problem, otrzymają powiadomienie o wycofaniu się co najmniej sześć miesięcy przed wycofaniem środowiska wykonawczego.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

Odwiedź centrum [azure dla deweloperów oprogramowania Java,](/java/azure/) aby znaleźć szybki start platformy Azure, samouczki i dokumentację referencyjną języka Java.

Na ogólne pytania dotyczące korzystania z usługi App Service dla systemu Linux, które nie są specyficzne dla rozwoju oprogramowania Java, można odpowiedzieć w [często zadawanych pytaniach dotyczących usługi App Service linux.](app-service-linux-faq.md)
