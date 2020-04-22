---
title: Konfigurowanie aplikacji Java systemu Windows
description: Dowiedz się, jak skonfigurować aplikacje Java do uruchamiania w wystąpieniach maszyn wirtualnych systemu Windows w usłudze Azure App Service. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
keywords: usługa aplikacji azure, aplikacja internetowa, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: cebe5564767ee345c1aea96b6ac54b9398c3e9a3
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733028"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji Java systemu Windows dla usługi Azure App Service

Usługa Azure App Service umożliwia deweloperom oprogramowania Java szybkie tworzenie, wdrażanie i skalowanie aplikacji sieci Web firmy Tomcat w pełni zarządzanej usłudze systemu Windows. Wdrażanie aplikacji z wtyczkami Maven z wiersza polecenia lub w edytorach, takich jak IntelliJ, Eclipse lub Visual Studio Code.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla deweloperów java korzystających z usługi App Service. Jeśli nigdy nie korzystałeś z usługi Azure App Service, należy najpierw przeczytać przewodnik [Szybki start języka Java.](app-service-web-get-started-java.md) Odpowiedzi na ogólne pytania dotyczące korzystania z usługi App Service, które nie są specyficzne dla rozwoju języka Java, są odpowiedzi w [często zadawanych pytaniach dotyczących usługi App Service systemu Windows.](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Za pomocą [wtyczki aplikacji Azure Web App dla maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) można wdrożyć pliki .war. Wdrażanie z popularnymi środowiskami IDE jest również obsługiwane przez [zestaw narzędzi Azure dla intellij](/java/azure/intellij/azure-toolkit-for-intellij) lub zestaw narzędzi platformy Azure dla programu [Eclipse.](/java/azure/eclipse/azure-toolkit-for-eclipse)

W przeciwnym razie metoda wdrażania będzie zależeć od typu archiwum:

- Aby wdrożyć pliki .war w `/api/wardeploy/` tomcat, użyj punktu końcowego, aby opublikować plik archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Aby wdrożyć pliki jar w języku `/api/zipdeploy/` Java SE, użyj punktu końcowego witryny Kudu. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nie wdrażaj pliku .war przy użyciu protokołu FTP. Narzędzie FTP jest przeznaczone do przekazywania skryptów startowych, zależności lub innych plików środowiska uruchomieniowego. Nie jest to optymalny wybór do wdrażania aplikacji sieci web.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty wydajności, wizualizacje ruchu i sprawdzanie kondycji są dostępne dla każdej aplikacji za pośrednictwem witryny Azure portal. Aby uzyskać więcej informacji, zobacz [omówienie diagnostyki usługi Azure App Service](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Korzystanie z rejestratora lotu

Wszystkie środowiska wykonawcze Java w usłudze App Service za pomocą Azul JVM są wyposażone w rejestrator lotu Zulu. Służy do rejestrowania zdarzeń JVM, systemowych i java poziomie do monitorowania zachowania i rozwiązywania problemów w aplikacjach Java.

Aby wykonać nagranie z czasem, potrzebny jest identyfikator PID (Process ID) aplikacji Java. Aby znaleźć identyfikator PID, otwórz przeglądarkę w witrynie SCM aplikacji sieci web pod adresem https://<nazwą witryny>.scm.azurewebsites.net/ProcessExplorer/. Ta strona pokazuje uruchomione procesy w aplikacji sieci web. Znajdź proces o nazwie "java" w tabeli i skopiuj odpowiedni identyfikator PID (Process ID).

Następnie otwórz **konsolę debugowania** w górnym pasku narzędzi witryny SCM i uruchom następujące polecenie. Zamień `<pid>` identyfikator procesu skopiowany wcześniej. To polecenie rozpocznie 30-sekundowe nagrywanie profilera aplikacji `timed_recording_example.jfr` Java `D:\home` i wygeneruje plik o nazwie w katalogu.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Aby uzyskać więcej informacji, zobacz [numer polecenia Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analizowanie `.jfr` plików

Użyj [protokołu FTPS,](deploy-ftp.md) aby pobrać plik JFR na komputer lokalny. Aby przeanalizować plik JFR, pobierz i zainstaluj [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Instrukcje dotyczące zulu mission control można znaleźć w [dokumentacji Azul](https://docs.azul.com/zmc/) i [instrukcji instalacji.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji, zobacz [Dzienniki strumienia w usłudze Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Rejestrowanie aplikacji

Włącz [rejestrowanie aplikacji](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) za pośrednictwem witryny Azure portal lub [interfejsu wiersza polecenia platformy Azure,](/cli/azure/webapp/log#az-webapp-log-config) aby skonfigurować usługę App Service do zapisywania standardowych danych wyjściowych konsoli aplikacji i standardowych strumieni błędów konsoli do lokalnego systemu plików lub usługi Azure Blob Storage. Rejestrowanie do lokalnego wystąpienia systemu plików usługi App Service jest wyłączone 12 godzin po jego skonfigurowaniu. Jeśli potrzebujesz dłuższego przechowywania, skonfiguruj aplikację do zapisu danych wyjściowych w kontenerze magazynu obiektów Blob. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */LogFiles/Application/.*

Jeśli aplikacja używa [Logback](https://logback.qos.ch/) lub [Log4j](https://logging.apache.org/log4j) do śledzenia, można przesłać te ślady do przeglądu w usłudze Azure Application Insights przy użyciu instrukcji konfiguracji struktury rejestrowania w [eksploruj dzienniki śledzenia Java w usłudze Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Usługa Azure App Service obsługuje dostrajanie i dostosowywanie po wyjęciu z pól wyboru za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia. Przejrzyj następujące artykuły dotyczące konfiguracji aplikacji sieci Web niespecyfizacyjnej w języku Java:

- [Konfigurowanie ustawień aplikacji](configure-common.md#configure-app-settings)
- [Konfigurowanie domeny niestandardowej](app-service-web-tutorial-custom-domain.md)
- [Konfigurowanie powiązań TLS](configure-ssl-bindings.md)
- [Dodawanie sieci CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurowanie witryny Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Ustawianie opcji środowiska wykonawczego języka Java

Aby ustawić przydzieloną pamięć lub inne opcje środowiska uruchomieniowego JVM, utwórz [ustawienie aplikacji](configure-common.md#configure-app-settings) o nazwie `JAVA_OPTS` z opcjami. Usługa App Service przekazuje to ustawienie jako zmienną środowiskową do środowiska wykonawczego java po uruchomieniu.

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

### <a name="pre-compile-jsp-files"></a>Wstępne kompilowanie plików JSP

Aby zwiększyć wydajność aplikacji Tomcat, można skompilować pliki JSP przed wdrożeniem w usłudze App Service. Możesz użyć [wtyczki Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) dostarczonej przez Apache Sling, lub za pomocą tego [pliku ant kompilacji](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpieczanie aplikacji

Aplikacje Java działające w usłudze App Service mają ten sam zestaw [najlepszych rozwiązań w zakresie zabezpieczeń,](/azure/security/security-paas-applications-using-app-services) co inne aplikacje.

### <a name="authenticate-users-easy-auth"></a>Uwierzytelnij użytkowników (Easy Auth)

Skonfiguruj uwierzytelnianie aplikacji w witrynie Azure portal z opcją **Uwierzytelnianie i autoryzacja.** W tym miejscu można włączyć uwierzytelnianie przy użyciu usługi Azure Active Directory lub logowania społecznościowe, takie jak Facebook, Google lub GitHub. Konfiguracja portalu Azure działa tylko podczas konfigurowania jednego dostawcy uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji usługi App Service do używania logowania usługi Azure Active Directory](configure-authentication-provider-aad.md) i powiązanych artykułów dla innych dostawców tożsamości. Jeśli chcesz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami w [artykule dostosowywania uwierzytelniania usługi App Service.](app-service-authentication-how-to.md)

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

### <a name="configure-tlsssl"></a>Konfigurowanie protokołu TLS/SSL

Postępuj zgodnie z instrukcjami w [Secure niestandardowej nazwy DNS z powiązaniem TLS w usłudze Azure App Service,](configure-ssl-bindings.md) aby przekazać istniejący certyfikat TLS/SSL i powiązać go z nazwą domeny aplikacji. Domyślnie aplikacja będzie nadal zezwalać na połączenia HTTP-postępuj zgodnie z określonymi krokami w samouczku, aby wymusić SSL i TLS.

### <a name="use-keyvault-references"></a>Korzystanie z odwołań do keyvault

[Usługa Azure KeyVault](../key-vault/general/overview.md) zapewnia scentralizowane zarządzanie tajne z zasadami dostępu i historią inspekcji. Wpisy tajne (takie jak hasła lub parametry połączenia) można przechowywać w funkcji KeyVault i uzyskiwać dostęp do tych wpisów tajnych w aplikacji za pośrednictwem zmiennych środowiskowych.

Najpierw postępuj zgodnie z instrukcjami [dotyczącymi udzielania aplikacji dostępu do usługi Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i [odwoływania się do klucza keyvault do klucza tajnego w ustawieniach aplikacji.](app-service-key-vault-references.md#reference-syntax) Można sprawdzić, czy odwołanie jest rozpoznawane jako klucz tajny, drukując zmienną środowiskową podczas zdalnego uzyskiwania dostępu do terminalu usługi app service.

Aby wstrzyknąć te wpisy tajne w pliku konfiguracyjnym Spring lub Tomcat, należy użyć składni iniekcji zmiennej środowiskowej (`${MY_ENV_VAR}`). W przypadku plików konfiguracyjnych Sprężyn, zapoznaj się z niniejszą dokumentacją dotyczącą [konfiguracji zewnętrznych](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Konfigurowanie platform APM

W tej sekcji pokazano, jak połączyć aplikacje Java wdrożone w usłudze Azure App Service w systemie Linux za pomocą platform monitorowania wydajności aplikacji NewRelic i AppDynamics (APM).

### <a name="configure-new-relic"></a>Konfigurowanie nowej relikwii

1. Utwórz nowe konto reliktowe w [NewRelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z NewRelic, będzie miał nazwę pliku podobną do *newrelic-java-x.x.x.zip*.
3. Skopiuj klucz licencyjny, będziesz go potrzebować, aby skonfigurować agenta później.
4. Użyj [konsoli Kudu,](https://github.com/projectkudu/kudu/wiki/Kudu-console) aby utworzyć nowy katalog */home/site/wwwroot/apm*.
5. Prześlij rozpakowane nowe pliki agenta Java Relikt do katalogu pod */home/site/wwwroot/apm*. Pliki dla twojego agenta powinny znajdować się w */home/site/wwwroot/apm/newrelic*.
6. Zmodyfikuj plik YAML w *pliku /home/site/wwwroot/apm/newrelic/newrelic.yml* i zastąp wartość licencji zastępczej własnym kluczem licencyjnym.
7. W witrynie Azure portal przejdź do aplikacji w usłudze App Service i utwórz nowe ustawienie aplikacji.
    - Jeśli aplikacja korzysta z **oprogramowania Java SE,** utwórz zmienną środowiskową o nazwie `JAVA_OPTS` o wartości `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Jeśli używasz **Tomcat,** utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` o wartości `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Konfigurowanie aplikacji AppDynamics

1. Utwórz konto AppDynamics w [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java ze strony AppDynamics, nazwa pliku będzie podobna do *AppServerAgent-x.x.x.xxxxx.zip*
3. Użyj [konsoli Kudu,](https://github.com/projectkudu/kudu/wiki/Kudu-console) aby utworzyć nowy katalog */home/site/wwwroot/apm*.
4. Prześlij pliki agenta Java do katalogu w obszarze */home/site/wwwroot/apm*. Pliki dla twojego agenta powinny znajdować się w */home/site/wwwroot/apm/appdynamics*.
5. W witrynie Azure portal przejdź do aplikacji w usłudze App Service i utwórz nowe ustawienie aplikacji.
    - Jeśli używasz **Java SE,** utwórz `JAVA_OPTS` zmienną `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` środowiskową o nazwie o wartości, w której `<app-name>` jest nazwa usługi App Service.
    - Jeśli używasz **Tomcat,** utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` o wartości, `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` w której `<app-name>` jest nazwa usługi App Service.

>  Jeśli masz już zmienną `JAVA_OPTS` `CATALINA_OPTS`środowiskową dla `-javaagent:/...` lub , dołącz opcję na końcu bieżącej wartości.

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

#### <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Na koniec umieścimy sterownik JARs w tomcat classpath i uruchom ponownie usługę App Service. Upewnij się, że pliki sterowników JDBC są dostępne dla classloader Tomcat, umieszczając je w *katalogu /home/tomcat/lib.* (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do wystąpienia usługi App Service, wykonaj następujące czynności:

1. W [aplikacji Cloud Shell](https://shell.azure.com)zainstaluj rozszerzenie aplikacji webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do usługi App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Połącz się z lokalnym portem tunelowania za pomocą klienta SFTP i prześlij pliki do folderu */home/tomcat/lib.*

Alternatywnie można użyć klienta FTP, aby przekazać sterownik JDBC. Postępuj zgodnie z tymi [instrukcjami, aby uzyskać poświadczenia FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Konfigurowanie tomcat

Aby edytować pliki `server.xml` tomcat lub inne pliki konfiguracyjne, najpierw zanotuj wersję główną Tomcat w portalu.

1. Znajdź katalog domowy Tomcat dla swojej `env` wersji, uruchamiając polecenie. Wyszukaj zmienną `AZURE_TOMCAT`środowiskową, która zaczyna się od wersji głównej i dopasowuje do tej głównej wersji. Na przykład `AZURE_TOMCAT85_HOME` wskazuje katalog Tomcat dla Tomcat 8.5.
1. Po zidentyfikowaniu katalogu macierzystego Tomcat dla swojej wersji skopiuj katalog konfiguracyjny do programu `D:\home`. Na przykład, `AZURE_TOMCAT85_HOME` jeśli miał `D:\Program Files (x86)\apache-tomcat-8.5.37`wartość , nowa ścieżka skopiowanego `D:\home\apache-tomcat-8.5.37`katalogu będzie .

Na koniec uruchom ponownie usługę App Service. Wdrożenia powinny przejść `D:\home\site\wwwroot\webapps` do tak jak poprzednio.

## <a name="configure-java-se"></a>Konfigurowanie oprogramowania Java SE

Podczas uruchamiania pliku . JAR aplikacji na Java SE w systemie Windows, `server.port` jest przekazywana jako opcja wiersza polecenia, jak aplikacja uruchamia. Można ręcznie rozpoznać port HTTP ze `HTTP_PLATFORM_PORT`zmiennej środowiskowej, . Wartość tej zmiennej środowiskowej będzie portEM HTTP, na który aplikacja powinna nasłuchiwała. 

## <a name="java-runtime-statement-of-support"></a>Instrukcja obsługi środowiska wykonawczego java

### <a name="jdk-versions-and-maintenance"></a>Wersje JDK i konserwacja

Obsługiwany przez platformę Azure zestaw Java Development Kit (JDK) jest [Zulu](https://www.azul.com/downloads/azure-only/zulu/) dostarczane przez [Azul Systems](https://www.azul.com/).

Główne aktualizacje wersji zostaną dostarczone za pośrednictwem nowych opcji środowiska uruchomieniowego w usłudze Azure App Service dla systemu Windows. Klienci aktualizują te nowsze wersje oprogramowania Java, konfigurując wdrożenie usługi App Service i są odpowiedzialni za testowanie i zapewnianie, że główna aktualizacja spełnia ich potrzeby.

Obsługiwane JDK są automatycznie poprawiane co kwartał w styczniu, kwietniu, lipcu i październiku każdego roku. Aby uzyskać więcej informacji na temat oprogramowania Java na platformie Azure, zobacz [ten dokument pomocy technicznej](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Poprawki i poprawki dotyczące poważnych luk w zabezpieczeniach zostaną wydane, gdy tylko staną się dostępne w Azul Systems. "Poważna" luka w zabezpieczeniach jest definiowana przez wynik podstawowy 9.0 lub wyższy w [systemie NIST Common Vulnerability Scoring System w wersji 2.](https://nvd.nist.gov/cvss.cfm)

Tomcat 8.0 osiągnął [koniec życia (EOL) na 30 września 2018](https://tomcat.apache.org/tomcat-80-eol.html). Środowisko wykonawcze jest nadal avialable w usłudze Azure App Service, platforma Azure nie będzie stosować aktualizacje zabezpieczeń do tomcat 8.0. Jeśli to możliwe, należy przeprowadzić migrację aplikacji do urządzenia Tomcat 8.5 lub 9.0. Zarówno Tomcat 8.5 i 9.0 są dostępne w usłudze Azure App Service. Więcej informacji można znaleźć na [oficjalnej stronie Tomcat.](https://tomcat.apache.org/whichversion.html) 

### <a name="deprecation-and-retirement"></a>Wycofywanie i wycofywanie

Jeśli obsługiwane środowisko uruchomieniowe java zostanie wycofane, deweloperzy platformy Azure przy użyciu środowiska wykonawczego, którego dotyczy problem, otrzymają powiadomienie o wycofaniu się co najmniej sześć miesięcy przed wycofaniem środowiska wykonawczego.

### <a name="local-development"></a>Programowanie lokalne

Deweloperzy mogą pobrać Wydanie Produkcyjne Azul Zulu Enterprise JDK dla rozwoju lokalnego z [witryny pobierania Azul.Developers](https://www.azul.com/downloads/azure-only/zulu/)can download the Production Edition of Azul Zulu Enterprise JDK for local development from Azul's download site .

### <a name="development-support"></a>Wsparcie rozwoju

Pomoc techniczna dla [usługi Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna za pośrednictwem firmy Microsoft podczas opracowywania usługi Azure lub [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/) z [kwalifikowanym planem pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego

Deweloperzy mogą [otworzyć problem](/azure/azure-portal/supportability/how-to-create-azure-support-request) z zestawami JDK Azul Zulu za pośrednictwem pomocy technicznej platformy Azure, jeśli mają [plan kwalifikowanej pomocy technicznej.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Następne kroki

Ten temat zawiera instrukcję java runtime obsługi usługi Azure App Service w systemie Windows.

- Aby dowiedzieć się więcej o hostowanie aplikacji sieci Web za pomocą usługi Azure App Service, zobacz [Omówienie usługi App Service](overview.md).
- Aby uzyskać informacje o oprogramowaniu Java na platformie Azure development, zobacz [Azure for Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
