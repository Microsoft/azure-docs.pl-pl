---
title: Konfigurowanie aplikacji Java
description: Dowiedz się, jak skonfigurować aplikacje Java do uruchamiania na Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
keywords: azure app service, web app, windows, oss, java, tomcat, jboss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 134ac04c4f6fb5f0e38a868adc735fc816fbc875
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829516"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji Java dla Azure App Service

Azure App Service umożliwia deweloperom języka Java szybkie tworzenie, wdrażanie i skalowanie aplikacji internetowych Java SE, Tomcat i JBoss EAP w w pełni zarządzanej usłudze. Wdrażanie aplikacji za pomocą wtyczek Maven, z wiersza polecenia lub w edytorach, takich jak IntelliJ, Eclipse lub Visual Studio Code.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla deweloperów języka Java korzystających z App Service. Jeśli nigdy wcześniej nie używaliśmy Azure App Service, najpierw zapoznaj się z przewodnikem Szybki start [języka Java.](quickstart-java.md) Odpowiedzi na ogólne pytania dotyczące App Service, które nie są specyficzne dla programowania w języku Java, znajdują się w te App Service FAQ ( Często [zadawane pytania).](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Wtyczka [aplikacji internetowej platformy Azure dla narzędzia Maven](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) umożliwia wdrażanie plików war lub jar. Wdrażanie za pomocą popularnych esejów jest również obsługiwane za pomocą [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) lub [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

W przeciwnym razie metoda wdrażania będzie zależeć od typu archiwum:

### <a name="java-se"></a>Java SE

Aby wdrożyć pliki jar w języku Java SE, użyj `/api/zipdeploy/` punktu końcowego witryny Kudu. Aby uzyskać więcej informacji na temat tego interfejsu API, zapoznaj się z [tą dokumentacją.](./deploy-zip.md#rest) 

> [!NOTE]
>  Aplikacja jar musi mieć nazwę , `app.jar` aby App Service ją identyfikować i uruchamiać. Wtyczka Maven (wymieniona powyżej) automatycznie zmieni nazwę aplikacji podczas wdrażania. Jeśli nie chcesz zmieniać nazwy pliku JAR na *app.jar,* możesz przekazać skrypt powłoki za pomocą polecenia , aby uruchomić aplikację jar. Wklej ścieżkę bezwzględną do tego skryptu w polu [tekstowym Plik](faq-app-service-linux.md#built-in-images) startowy w sekcji Konfiguracja portalu. Skrypt startowy nie jest uruchamiany z katalogu, w którym został umieszczony. W związku z tym zawsze należy używać ścieżek bezwzględnych do odwoływania się do plików w skrypcie startowym (na przykład: `java -jar /home/myapp/myapp.jar`).

### <a name="tomcat"></a>Tomcat

Aby wdrożyć pliki war w programie Tomcat, użyj punktu `/api/wardeploy/` końcowego, aby opublikować plik archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zapoznaj się z [tą dokumentacją.](./deploy-zip.md#deploy-war-file)

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

Aby wdrożyć pliki war w pliku JBoss, użyj punktu `/api/wardeploy/` końcowego do postu pliku archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zapoznaj się z [tą dokumentacją.](./deploy-zip.md#deploy-war-file)

Aby wdrożyć pliki .ear, [użyj protokołu FTP](deploy-ftp.md).

::: zone-end

Nie wdrażaj pliku war lub jar przy użyciu protokołu FTP. Narzędzie FTP jest przeznaczone do przekazywania skryptów uruchamiania, zależności lub innych plików środowiska uruchomieniowego. Nie jest to optymalny wybór w przypadku wdrażania aplikacji internetowych.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty wydajności, wizualizacje ruchu i sprawdzanie kondycji są dostępne dla każdej aplikacji za pośrednictwem Azure Portal. Aby uzyskać więcej informacji, [zobacz Azure App Service omówienie diagnostyki.](overview-diagnostics.md)

### <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Aby uzyskać więcej informacji, zobacz [Stream logs in Cloud Shell (Przesyłanie](troubleshoot-diagnostic-logs.md#in-cloud-shell)strumieniowe dzienników w Cloud Shell ).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów

Wbudowane obrazy Języka Java są oparte na systemie [operacyjnym Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Użyj menedżera `apk` pakietów, aby zainstalować narzędzia do rozwiązywania problemów lub polecenia.

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

Wszystkie środowiska uruchomieniowe Java na App Service przy użyciu środowiska Azul JVM są wyposażone w narzędzia Zulu Flight Recorder. Umożliwia to rejestrowanie zdarzeń JVM, systemowych i aplikacji oraz rozwiązywanie problemów w aplikacjach Java.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Rejestrowanie z czasem

Do rejestrowania w czasie potrzebny jest identyfikator PID (identyfikator procesu) aplikacji Java. Aby znaleźć identyfikator PID, otwórz w przeglądarce witrynę SCM aplikacji internetowej pod adresem https://<your-site-name>.scm.azurewebsites.net/ProcessExplorer/. Na tej stronie przedstawiono uruchomione procesy w aplikacji internetowej. Znajdź w tabeli proces o nazwie "java" i skopiuj odpowiedni identyfikator PID (identyfikator procesu).

Następnie otwórz **konsolę debugowania** na górnym pasku narzędzi witryny SCM i uruchom następujące polecenie. Zastąp `<pid>` identyfikatorem procesu skopiowanym wcześniej. To polecenie spowoduje uruchomienie 30-sekundowego rejestrowania profilera aplikacji Java i wygenerowanie pliku o `timed_recording_example.jfr` nazwie w `D:\home` katalogu .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

Na komputerze za pomocą App Service SSH i uruchom polecenie , `jcmd` aby wyświetlić listę wszystkich uruchomionych procesów Java. Oprócz samego jcmd powinna zostać uruchomiona aplikacja Java z identyfikatorem procesu (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Wykonaj poniższe polecenie, aby rozpocząć 30-sekundowe rejestrowanie JVM. Spowoduje to profilowanie JVM i utworzenie pliku JFR o *nazwie jfr_example.jfr* w katalogu macierzystym. (Zastąp 116 identyfikatorem PID aplikacji Java).

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

W 30-sekundowym interwale możesz sprawdzić, czy rejestrowanie odbywa się, uruchamiając . `jcmd 116 JFR.check` Spowoduje to pokazanie wszystkich nagrań dla danego procesu Java.

#### <a name="continuous-recording"></a>Ciągłe rejestrowanie

Za pomocą programu Zulu Flight Recorder można stale profilować aplikację Java przy minimalnym wpływie na wydajność środowiska uruchomieniowego[(źródło).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) W tym celu uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby utworzyć ustawienie aplikacji o JAVA_OPTS z niezbędną konfiguracją. Zawartość ustawienia JAVA_OPTS aplikacji jest przekazywana do `java` polecenia podczas rozpoczynania pracy aplikacji.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Po zakończeniu rejestrowania możesz zrzucić bieżące dane rejestrowania w dowolnym momencie za pomocą `JFR.dump` polecenia .

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Analizowanie `.jfr` plików

Użyj [protokołu FTPS,](deploy-ftp.md) aby pobrać plik JFR na komputer lokalny. Aby przeanalizować plik JFR, pobierz i zainstaluj [aplikację Zulu Mission Control.](https://www.azul.com/products/zulu-mission-control/) Aby uzyskać instrukcje dotyczące aplikacji Zulu Mission Control, zobacz dokumentację [azul](https://docs.azul.com/zmc/) i [instrukcje instalacji](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="app-logging"></a>Rejestrowanie aplikacji

::: zone pivot="platform-windows"

Włącz [rejestrowanie](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure Portal lub platformy [Azure App Service](/cli/azure/webapp/log#az_webapp_log_config) aby skonfigurować usługę App Service do zapisu standardowych strumieni błędów konsoli i konsoli aplikacji w lokalnym systemie plików lub Azure Blob Storage. Rejestrowanie do lokalnego App Service systemu plików jest wyłączone 12 godzin po jego skonfigurowaniu. Jeśli potrzebujesz dłuższego okresu przechowywania, skonfiguruj aplikację do zapisu danych wyjściowych w kontenerze usługi Blob Storage. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */home/LogFiles/Application/.*

::: zone-end
::: zone pivot="platform-linux"

Włącz [rejestrowanie](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure Portal lub platformy [Azure App Service](/cli/azure/webapp/log#az_webapp_log_config) aby skonfigurować usługę App Service do zapisu standardowych strumieni błędów konsoli i konsoli aplikacji w lokalnym systemie plików lub Azure Blob Storage. Jeśli potrzebujesz dłuższego okresu przechowywania, skonfiguruj aplikację do zapisu danych wyjściowych w kontenerze usługi Blob Storage. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */home/LogFiles/Application/.*

Azure Blob Storage w przypadku aplikacji opartych na systemie Linux App Services można skonfigurować tylko przy użyciu Azure Monitor [(wersja zapoznawcza)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Jeśli aplikacja używa funkcji [Logback](https://logback.qos.ch/) lub [Log4j](https://logging.apache.org/log4j) do śledzenia, możesz przesyłać te ślady do przeglądu do usługi Azure Application Insights przy użyciu instrukcji konfiguracji struktury rejestrowania w tesłudze Explore Java trace logs in Application Insights (Eksplorowanie dzienników śledzenia języka Java w usłudze [Application Insights).](../azure-monitor/app/java-trace-logs.md)

## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Azure App Service dla systemu Linux obsługuje dostrajanie i dostosowywanie za pomocą interfejsu Azure Portal wiersza polecenia. Zapoznaj się z następującymi artykułami, aby zapoznać się z konfiguracją aplikacji internetowej, która nie jest specyficzna dla języka Java:

- [Konfigurowanie ustawień aplikacji](configure-common.md#configure-app-settings)
- [Konfigurowanie domeny niestandardowej](app-service-web-tutorial-custom-domain.md)
- [Konfigurowanie powiązań SSL](configure-ssl-bindings.md)
- [Dodawanie sieci CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurowanie witryny Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Ustawianie opcji środowiska uruchomieniowego Java

Aby ustawić przydzieloną pamięć lub inne opcje środowiska uruchomieniowego JVM, utwórz ustawienie aplikacji [o](configure-common.md#configure-app-settings) nazwie `JAVA_OPTS` z opcjami. App Service przekazuje to ustawienie jako zmienną środowiskową do środowiska uruchomieniowego Java podczas jego uruchamiania.

W witrynie Azure Portal w obszarze **Ustawienia** aplikacji dla aplikacji internetowej utwórz nowe ustawienie aplikacji o nazwie , które zawiera `JAVA_OPTS` dodatkowe ustawienia, takie jak `-Xms512m -Xmx1204m` .

Aby skonfigurować ustawienie aplikacji z wtyczki Maven, dodaj tagi ustawienia/wartości w sekcji wtyczki platformy Azure. W poniższym przykładzie określono określony minimalny i maksymalny rozmiar sterty Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Deweloperzy, którzy uruchamiają pojedynczą aplikację z jednym App Service wdrożenia w planie wdrożenia, mogą korzystać z następujących opcji:

- Wystąpienia B1 i S1: `-Xms1024m -Xmx1024m`
- Wystąpienia B2 i S2: `-Xms3072m -Xmx3072m`
- Wystąpienia B3 i S3: `-Xms6144m -Xmx6144m`

Podczas dostrajania ustawień sterty aplikacji przejrzyj szczegóły planu App Service i weź pod uwagę wiele aplikacji oraz miejsca wdrożenia, które muszą znaleźć optymalną alokację pamięci.

### <a name="turn-on-web-sockets"></a>Włączanie gniazd internetowych

Włącz obsługę gniazd internetowych w Azure Portal w **ustawieniach** aplikacji. Aby ustawienie zaczęło obowiązywać, należy ponownie uruchomić aplikację.

Włącz obsługę gniazd internetowych przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą następującego polecenia:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Następnie uruchom ponownie aplikację:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ustawianie domyślnego kodowania znaków

W witrynie Azure Portal w **obszarze Ustawienia** aplikacji dla aplikacji internetowej utwórz nowe ustawienie aplikacji o nazwie z `JAVA_OPTS` wartością `-Dfile.encoding=UTF-8` .

Alternatywnie możesz skonfigurować ustawienie aplikacji przy użyciu wtyczki App Service Maven. Dodaj nazwę ustawienia i tagi wartości w konfiguracji wtyczki:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Wstępne kompilowanie plików JSP

Aby zwiększyć wydajność aplikacji Tomcat, można skompilować pliki JSP przed wdrożeniem w App Service. Możesz użyć wtyczki [Maven dostarczonej](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) przez apache Sling lub tego pliku [kompilacji Ant.](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)

## <a name="secure-applications"></a>Zabezpieczanie aplikacji

Aplikacje Java działające w App Service mają ten sam zestaw najlepszych rozwiązań w zakresie [zabezpieczeń co](../security/fundamentals/paas-applications-using-app-services.md) inne aplikacje.

### <a name="authenticate-users-easy-auth"></a>Uwierzytelnianie użytkowników (easy auth)

Skonfiguruj uwierzytelnianie aplikacji w Azure Portal z **opcją Uwierzytelnianie i** autoryzacja. W tym miejscu możesz włączyć uwierzytelnianie przy użyciu Azure Active Directory logowania w sieciach społecznościowych, takich jak Facebook, Google lub GitHub. Azure Portal działa tylko podczas konfigurowania jednego dostawcy uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Configure your App Service app to use Azure Active Directory login](configure-authentication-provider-aad.md) and the related articles for other identity providers (Konfigurowanie aplikacji do Azure Active Directory logowania i powiązane artykuły dotyczące innych dostawców tożsamości. Jeśli musisz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami w artykule [dostosowywanie App Service uwierzytelniania.](app-service-authentication-how-to.md)

#### <a name="java-se"></a>Java SE

Spring Boot deweloperzy mogą używać początkowego [Azure Active Directory Spring Boot do](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) zabezpieczania aplikacji przy użyciu znanych adnotacji i interfejsów API spring security. Pamiętaj, aby zwiększyć maksymalny rozmiar nagłówka w pliku *application.properties.* Sugerujemy wartość `16384` .

#### <a name="tomcat"></a>Tomcat

Aplikacja Tomcat może uzyskać dostęp do oświadczeń użytkownika bezpośrednio z serwletu, rzutując obiekt Principal na obiekt Map. Obiekt Map mapuje każdy typ oświadczenia na kolekcję oświadczeń dla tego typu. W poniższym kodzie jest `request` wystąpieniem klasy `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Teraz możesz sprawdzić obiekt `Map` pod celu określonego oświadczenia. Na przykład poniższy fragment kodu iteruje po wszystkich typach oświadczenia i drukuje zawartość każdej kolekcji.

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

Aby wylogować użytkowników, użyj `/.auth/ext/logout` ścieżki . Aby wykonać inne akcje, zapoznaj się z dokumentacją dotyczącą użycia uwierzytelniania [App Service autoryzacji.](./app-service-authentication-how-to.md) Istnieje również oficjalna dokumentacja interfejsu [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) serwera Tomcat i jego metod. Następujące metody serwletu są również oparte na konfiguracji App Service serwletu:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Aby wyłączyć tę funkcję, utwórz ustawienie aplikacji o nazwie `WEBSITE_AUTH_SKIP_PRINCIPAL` o wartości `1` . Aby wyłączyć wszystkie filtry serwletu dodane przez App Service, utwórz ustawienie o nazwie `WEBSITE_SKIP_FILTERS` z wartością `1` .

### <a name="configure-tlsssl"></a>Konfigurowanie protokołu TLS/SSL

Postępuj zgodnie z instrukcjami w te tematu Secure a custom DNS name with an SSL binding in Azure App Service (Zabezpieczanie niestandardowej nazwy DNS za pomocą powiązania SSL na platformie [Azure App Service),](configure-ssl-bindings.md) aby przekazać istniejący certyfikat SSL i powiązać go z nazwą domeny aplikacji. Domyślnie aplikacja będzie nadal zezwalać na połączenia HTTP — wykonaj kroki opisane w samouczku, aby wymusić protokoły SSL i TLS.

### <a name="use-keyvault-references"></a>Używanie odwołań do funkcji KeyVault

[Usługa Azure KeyVault zapewnia](../key-vault/general/overview.md) scentralizowane zarządzanie kluczami tajnymi z zasadami dostępu i historią inspekcji. Wpisy tajne (takie jak hasła lub parametry połączenia) można przechowywać w magazynie kluczy i uzyskać dostęp do tych wpisów tajnych w aplikacji za pomocą zmiennych środowiskowych.

Najpierw postępuj zgodnie z instrukcjami dotyczącymi przyznawania aplikacji dostępu do usługi [Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i tworzenia odwołania do klucza [KeyVault](app-service-key-vault-references.md#reference-syntax)dla twojego klucza tajnego w ustawieniu aplikacji . Możesz sprawdzić, czy odwołanie jest rozpoznawczy jako klucz tajny, drukując zmienną środowiskową podczas zdalnego uzyskiwania dostępu do App Service terminalu.

Aby wstrzyknąć te wpisy tajne do pliku konfiguracji środowiska Spring lub Tomcat, użyj składni iniekcji zmiennej środowiskowej ( `${MY_ENV_VAR}` ). W przypadku plików konfiguracji spring zapoznaj się z tą dokumentacją dotyczącą [konfiguracji zewnętrznych.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Korzystanie z magazynu kluczy języka Java

Domyślnie wszystkie certyfikaty publiczne lub prywatne przekazane do systemu [App Service Linux](configure-ssl-certificate.md) zostaną załadowane do odpowiednich magazynów kluczy Java podczas uruchamiania kontenera. Po przesłaniu certyfikatu należy ponownie uruchomić App Service, aby został załadowany do magazynu kluczy Java. Certyfikaty publiczne są ładowane do magazynu kluczy w programie `$JAVA_HOME/jre/lib/security/cacerts` , a certyfikaty prywatne są przechowywane w programie `$JAVA_HOME/lib/security/client.jks` .

Do szyfrowania połączenia JDBC przy użyciu certyfikatów w magazynie kluczy Java może być konieczna dodatkowa konfiguracja. Zapoznaj się z dokumentacją wybranego sterownika JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Inicjowanie magazynu kluczy Java

Aby zainicjować `import java.security.KeyStore` obiekt , załaduj plik magazynu kluczy z hasłem. Domyślne hasło dla obu magazynów kluczy to "changeit".

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

Certyfikaty można załadować ręcznie do magazynu kluczy. Utwórz ustawienie aplikacji o wartości , aby App Service automatycznie ładować certyfikaty do `SKIP_JAVA_KEYSTORE_LOAD` `1` magazynu kluczy. Wszystkie certyfikaty publiczne przekazane do usługi App Service za pośrednictwem Azure Portal są przechowywane w obszarze `/var/ssl/certs/` . Certyfikaty prywatne są przechowywane w obszarze `/var/ssl/private/` .

Możesz wchodzić w interakcje z narzędziem Java Key Tool lub debugować je, otwierając połączenie [SSH](configure-linux-open-ssh-session.md) z App Service i uruchamiając polecenie `keytool` . Listę [poleceń można znaleźć w](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) dokumentacji narzędzia Key Tool. Aby uzyskać więcej informacji na temat interfejsu API magazynu kluczy, zapoznaj się z oficjalną [dokumentacją](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

::: zone-end

## <a name="configure-apm-platforms"></a>Konfigurowanie platform APM

W tej sekcji przedstawiono sposób łączenia aplikacji Java wdrożonych na platformach Azure App Service dla systemu Linux z platformami monitorowania wydajności aplikacji NewRelic i AppDynamics (APM).

### <a name="configure-new-relic"></a>Konfigurowanie New Relic

::: zone pivot="platform-windows"

1. Tworzenie konta NewRelic w [NewRelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z biblioteki NewRelic. Będzie on miał nazwę pliku podobną *donewrelic-java-x.x.x.zip*.
3. Skopiuj klucz licencji. Będzie on potrzebny do późniejszego skonfigurowania agenta.
4. [SSH do App Service i](configure-linux-open-ssh-session.md) utwórz nowy katalog */home/site/wwwroot/apm.*
5. Przekaż rozpakowane pliki agenta Java NewRelic do katalogu w katalogu */home/site/wwwroot/apm.* Pliki agenta powinny być w folderze */home/site/wwwroot/apm/newrelic.*
6. Zmodyfikuj plik YAML pod adresem */home/site/wwwroot/apm/newrelic/newrelic.yml* i zastąp symbol zastępczy wartości licencji własnym kluczem licencji.
7. W Azure Portal przejdź do aplikacji w App Service i utwórz nowe ustawienie aplikacji.

    - W **przypadku aplikacji Java SE** utwórz zmienną środowiskową o nazwie z `JAVA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Dla **środowiska Tomcat** utwórz zmienną środowiskową o `CATALINA_OPTS` nazwie z wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Tworzenie konta NewRelic w [NewRelic.com](https://newrelic.com/signup)
2. Pobierz agenta języka Java z biblioteki NewRelic. Będzie on miał nazwę pliku podobną *donewrelic-java-x.x.x.zip*.
3. Skopiuj klucz licencji. Będzie on potrzebny do późniejszego skonfigurowania agenta.
4. [SSH do App Service i](configure-linux-open-ssh-session.md) utwórz nowy katalog */home/site/wwwroot/apm.*
5. Przekaż rozpakowane pliki agenta Java NewRelic do katalogu w katalogu */home/site/wwwroot/apm.* Pliki agenta powinny być w folderze */home/site/wwwroot/apm/newrelic.*
6. Zmodyfikuj plik YAML pod adresem */home/site/wwwroot/apm/newrelic/newrelic.yml* i zastąp symbol zastępczy wartości licencji własnym kluczem licencji.
7. W Azure Portal przejdź do aplikacji w App Service i utwórz nowe ustawienie aplikacji.
   
    - W **przypadku aplikacji Java SE** utwórz zmienną środowiskową o nazwie z `JAVA_OPTS` wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Dla **środowiska Tomcat** utwórz zmienną środowiskową o `CATALINA_OPTS` nazwie z wartością `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Jeśli masz już zmienną środowiskową dla lub , dołącz opcję na `JAVA_OPTS` `CATALINA_OPTS` końcu `-javaagent:/...` bieżącej wartości.

### <a name="configure-appdynamics"></a>Konfigurowanie appdynamics

::: zone pivot="platform-windows"

1. Tworzenie konta AppDynamics w [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java z witryny internetowej AppDynamics. Nazwa pliku będzie podobna do *AppServerAgent-x.x.x.xxxxx.zip*
3. Użyj konsoli [Kudu,](https://github.com/projectkudu/kudu/wiki/Kudu-console) aby utworzyć nowy katalog */home/site/wwwroot/apm.*
4. Przekaż pliki agenta Java do katalogu w katalogu */home/site/wwwroot/apm.* Pliki agenta powinny być w folderze */home/site/wwwroot/apm/appdynamics.*
5. W Azure Portal przejdź do aplikacji w App Service i utwórz nowe ustawienie aplikacji.

   - W **przypadku aplikacji Java SE** utwórz zmienną środowiskową o nazwie z wartością , gdzie to App Service nazwa `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` użytkownika.
   - W **przypadku aplikacji Tomcat** utwórz zmienną środowiskową o nazwie z `CATALINA_OPTS` wartością , gdzie to App Service nazwa `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` użytkownika.

::: zone-end
::: zone pivot="platform-linux"

1. Tworzenie konta AppDynamics na stronie [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java z witryny internetowej AppDynamics. Nazwa pliku będzie podobna do *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH do App Service i](configure-linux-open-ssh-session.md) utwórz nowy katalog */home/site/wwwroot/apm.*
4. Przekaż pliki agenta Java do katalogu w katalogu */home/site/wwwroot/apm.* Pliki agenta powinny być w folderze */home/site/wwwroot/apm/appdynamics.*
5. W Azure Portal przejdź do aplikacji w App Service i utwórz nowe ustawienie aplikacji.

   - W **przypadku aplikacji Java SE** utwórz zmienną środowiskową o nazwie z wartością , gdzie to App Service nazwa `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` użytkownika.
   - W **przypadku aplikacji Tomcat** utwórz zmienną środowiskową o nazwie z `CATALINA_OPTS` wartością , gdzie to App Service nazwa `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` użytkownika.

::: zone-end

> [!NOTE]
>  Jeśli masz już zmienną środowiskową dla lub , dołącz opcję na `JAVA_OPTS` `CATALINA_OPTS` końcu `-javaagent:/...` bieżącej wartości.

## <a name="configure-data-sources"></a>Konfigurowanie źródeł danych

### <a name="java-se"></a>Java SE

Aby połączyć się ze źródłami danych Spring Boot aplikacji, sugerujemy utworzenie ciągów połączenia i wstrzyknięcie ich do *pliku application.properties.*

1. W sekcji "Konfiguracja" na stronie App Service ustaw nazwę ciągu, wklej ciąg połączenia JDBC w polu wartości i ustaw typ na "Niestandardowy". Opcjonalnie możesz ustawić te ciągi połączenia jako ustawienie miejsca.

    Te ciągi połączenia są dostępne dla naszej aplikacji jako zmienna środowiskowa o nazwie `CUSTOMCONNSTR_<your-string-name>` . Na przykład utworzone powyżej parametrów połączenia będą mieć nazwę `CUSTOMCONNSTR_exampledb` .

2. W pliku *application.properties* odwołuj się do tych parametrów połączenia przy użyciu nazwy zmiennej środowiskowej . W naszym przykładzie użyjemy następującego.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Więcej informacji na [ten Spring Boot można](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) [](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) znaleźć w dokumentacji dotyczącej dostępu do danych i konfiguracji zewnętrznych.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Te instrukcje dotyczą wszystkich połączeń z bazą danych. Należy wypełnić symbole zastępcze nazwą klasy sterownika wybranej bazy danych i plikiem JAR. Udostępniona tabela zawiera nazwy klas i pliki do pobrania sterowników dla typowych baz danych.

| baza danych   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobieranie](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (wybierz pozycję "Niezależna od platformy") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobieranie](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Aby skonfigurować serwer Tomcat do używania łączności z bazą danych Java (JDBC) lub interfejsu API trwałości języka Java (JPA), najpierw dostosuj zmienną środowiskową odczytywaną przez program `CATALINA_OPTS` Tomcat podczas uruchamiania. Ustaw te wartości za pomocą ustawienia aplikacji w App Service [Maven:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Możesz też ustawić zmienne środowiskowe na stronie **Ustawienia**  >  **aplikacji konfiguracji** w Azure Portal.

Następnie określ, czy źródło danych powinno być dostępne dla jednej aplikacji, czy dla wszystkich aplikacji uruchomionych w serwletie Tomcat.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Utwórz *plikcontext.xml* w katalogu *META-INF/* projektu. Utwórz *katalog META-INF/,* jeśli nie istnieje.

2. W *context.xml* dodaj element , `Context` aby połączyć źródło danych z adresem JNDI. Zastąp symbol `driverClassName` zastępczy nazwą klasy sterownika z powyższej tabeli.

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

3. Zaktualizuj ustawienia *aplikacjiweb.xml* aby używać źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Udostępnione zasoby na poziomie serwera

Instalacje programu Tomcat App Service systemie Windows istnieją w przestrzeni udostępnionej w planie App Service. Nie można bezpośrednio zmodyfikować instalacji serwera Tomcat na potrzeby konfiguracji całego serwera. Aby wprowadzić zmiany konfiguracji na poziomie serwera w instalacji serwera Tomcat, należy skopiować serwer Tomcat do folderu lokalnego, w którym można modyfikować konfigurację serwera Tomcat. 

##### <a name="automate-creating-custom-tomcat-on-app-start"></a>Automatyzowanie tworzenia niestandardowego programu Tomcat podczas uruchamiania aplikacji

Skrypt uruchamiania umożliwia wykonywanie akcji przed uruchomieniem aplikacji internetowej. Skrypt startowy do dostosowywania programu Tomcat musi wykonać następujące czynności:

1. Sprawdź, czy serwer Tomcat został już skopiowany i skonfigurowany lokalnie. Jeśli tak było, skrypt uruchamiania może zakończyć się w tym miejscu.
2. Skopiuj lokalnie program Tomcat.
3. Wprowadzić wymagane zmiany konfiguracji.
4. Wskaż, że konfiguracja została pomyślnie ukończona.

Oto skrypt programu PowerShell, który kończy następujące kroki:

```powershell
    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    {... customization ...}

    # Mark that the operation was a success
    New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
```

##### <a name="transforms"></a>Przekształcenia

Częstym zastosowaniem dostosowywania wersji serwera Tomcat jest modyfikowanie `server.xml` plików konfiguracji , lub `context.xml` `web.xml` Tomcat. App Service już modyfikuje te pliki w celu zapewnienia funkcji platformy. Aby nadal korzystać z tych funkcji, ważne jest zachowanie zawartości tych plików podczas ich zmiany. W tym celu zalecamy użycie przekształcenia [XSL (XSLT).](https://www.w3schools.com/xml/xsl_intro.asp) Użyj transformacji XSL, aby wprowadzić zmiany w plikach XML przy zachowaniu oryginalnej zawartości pliku.

###### <a name="example-xslt-file"></a>Przykładowy plik XSLT

To przykładowe przekształcenie dodaje nowy węzeł łącznika do `server.xml` . Zwróć uwagę *na przekształcenie tożsamości*, które zachowuje oryginalną zawartość pliku.

```xml
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="xml" indent="yes"/>
  
    <!-- Identity transform: this ensures that the original contents of the file are included in the new file -->
    <!-- Ensure that your transform files include this block -->
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

###### <a name="function-for-xsl-transform"></a>Funkcja transformacji XSL

Program PowerShell ma wbudowane narzędzia do przekształcania plików XML przy użyciu przekształceń XSL. Poniższy skrypt jest przykładową funkcją, która umożliwia wykonanie `startup.ps1` przekształcenia:

```powershell
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);

        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
```

##### <a name="app-settings"></a>Ustawienia aplikacji

Platforma musi również wiedzieć, gdzie jest zainstalowana niestandardowa wersja programu Tomcat. Lokalizację instalacji można ustawić w ustawieniu `CATALINA_BASE` aplikacji.

Aby zmienić to ustawienie, możesz użyć interfejsu wiersza polecenia platformy Azure:

```powershell
    az webapp config appsettings set -g $MyResourceGroup -n $MyUniqueApp --settings CATALINA_BASE="%LOCAL_EXPANDED%\tomcat"
```

Możesz też ręcznie zmienić ustawienie w Azure Portal:

1. Przejdź do **opcji Ustawienia**  >  **Ustawienia Ustawienia**  >  **Aplikacji.**
1. Wybierz **pozycję Nowe ustawienie aplikacji.**
1. Użyj tych wartości, aby utworzyć ustawienie:
   1. **Nazwa:**`CATALINA_BASE`
   1. **Wartość**: `"%LOCAL_EXPANDED%\tomcat"`

##### <a name="example-startupps1"></a>Przykładowy startup.ps1

Poniższy przykładowy skrypt kopiuje niestandardowy katalog Tomcat do folderu lokalnego, wykonuje przekształcenie XSL i wskazuje, że przekształcenie powiodło się:

```powershell
    # Locations of xml and xsl files
    $target_xml="$LOCAL_EXPANDED\tomcat\conf\server.xml"
    $target_xsl="$HOME\site\server.xsl"

    # Define the transform function
    # Useful if transforming multiple files
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);
        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }

    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml

    # Mark that the operation was a success if successful
    if($success){
        New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
    }
```

#### <a name="finalize-configuration"></a>Finalizuj konfigurację

Na koniec umieśćmy ścieżki JAR sterownika w ścieżkach klas serwera Tomcat i ponownie uruchomimy App Service. Upewnij się, że pliki sterownika JDBC są dostępne dla klasyfikatora Tomcat, umieszczając je w *katalogu /home/tomcat/lib.* (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do App Service, wykonaj następujące kroki:

1. W [Cloud Shell](https://shell.azure.com)zainstaluj rozszerzenie aplikacji internetowej:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Połącz się z lokalnym portem tunelowania przy użyciu klienta SFTP i przekaż pliki do *folderu /home/tomcat/lib.*

Alternatywnie można przekazać sterownik JDBC za pomocą klienta FTP. Postępuj zgodnie z [tymi instrukcjami, aby uzyskać poświadczenia FTP.](deploy-configure-credentials.md)

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Te instrukcje dotyczą wszystkich połączeń z bazą danych. Należy wypełnić symbole zastępcze nazwą klasy sterowników wybranej bazy danych i plikiem JAR. Udostępniona tabela zawiera nazwy klas i pliki do pobrania sterowników dla typowych baz danych.

| baza danych   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobieranie](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (wybierz pozycję "Niezależnie od platformy") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobieranie](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Aby skonfigurować serwer Tomcat do korzystania z łączności bazy danych Java (JDBC) lub interfejsu API trwałości języka Java (JPA), najpierw dostosuj zmienną środowiskową odczytywaną przez `CATALINA_OPTS` program Tomcat podczas uruchamiania. Ustaw te wartości za pomocą ustawienia aplikacji w App Service [Maven:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Możesz też ustawić zmienne środowiskowe na stronie **Ustawienia**  >  **aplikacji konfiguracji** w Azure Portal.

Następnie określ, czy źródło danych powinno być dostępne dla jednej aplikacji, czy dla wszystkich aplikacji uruchomionych w serwletie Tomcat.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Utwórz *context.xml* w katalogu *META-INF/* projektu. Utwórz *katalog META-INF/,* jeśli nie istnieje.

2. W *context.xml* dodaj element `Context` , aby połączyć źródło danych z adresem JNDI. Zastąp symbol `driverClassName` zastępczy nazwą klasy sterownika z powyższej tabeli.

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

3. Zaktualizuj ustawienia aplikacji *web.xml,* aby używać źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Udostępnione zasoby na poziomie serwera

Dodanie udostępnionego źródła danych na poziomie serwera będzie wymagało edytowania server.xml. Najpierw przekaż skrypt [startowy i](faq-app-service-linux.md#built-in-images) ustaw ścieżkę do skryptu w **poleceniu**  >  **uruchamiania konfiguracji**. Skrypt uruchamiania można przekazać przy użyciu [protokołu FTP.](deploy-ftp.md)

Skrypt startowy przekształci plik [xsl](https://www.w3schools.com/xml/xsl_intro.asp) w plik server.xml i wyprowadzi wynikowy plik XML do pliku `/usr/local/tomcat/conf/server.xml` . Skrypt uruchamiania powinien zainstalować bibliotekę libxslt za pośrednictwem pakietu apk. Plik xsl i skrypt uruchamiania można przekazać za pośrednictwem protokołu FTP. Poniżej znajduje się przykładowy skrypt uruchamiania.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Poniżej przedstawiono przykładowy plik xsl. Przykładowy plik xsl dodaje nowy węzeł łącznika do server.xml Tomcat.

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

#### <a name="finalize-configuration"></a>Finalizuj konfigurację

Na koniec umieść ścieżki JAR sterowników w ścieżkach klas serwera Tomcat i uruchom ponownie App Service.

1. Upewnij się, że pliki sterownika JDBC są dostępne dla klasyfikatora Tomcat, umieszczając je w *katalogu /home/tomcat/lib.* (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do App Service, wykonaj następujące kroki:

    1. W [Cloud Shell](https://shell.azure.com)zainstaluj rozszerzenie aplikacji internetowej:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Połącz się z lokalnym portem tunelowania przy użyciu klienta SFTP i przekaż pliki do *folderu /home/tomcat/lib.*

    Alternatywnie można przekazać sterownik JDBC za pomocą klienta FTP. Postępuj zgodnie z [tymi instrukcjami, aby uzyskać poświadczenia FTP.](deploy-configure-credentials.md)

2. Jeśli utworzono źródło danych na poziomie serwera, uruchom ponownie aplikację App Service Linux. Program Tomcat zostanie `CATALINA_BASE` zresetowany do `/home/tomcat` zaktualizowanej konfiguracji i użyje jej.

### <a name="jboss-eap"></a>JBoss EAP

Podczas rejestrowania źródła danych za pomocą protokołu [EAP JBoss](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)istnieją trzy podstawowe kroki: przekazywanie sterownika JDBC, dodawanie sterownika JDBC jako modułu i rejestrowanie modułu. App Service to bez stanowa usługa hostingu, więc polecenia konfiguracji służące do dodawania i rejestrowania modułu źródła danych muszą być skryptowane i stosowane podczas uruchamiania kontenera.

1. Uzyskaj sterownik JDBC bazy danych. 
2. Utwórz plik definicji modułu XML dla sterownika JDBC. Poniższy przykład to definicja modułu dla postgreSQL.

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

1. Umieść polecenia interfejsu wiersza polecenia JBoss w pliku o nazwie `jboss-cli-commands.cli` . Polecenia JBoss muszą dodać moduł i zarejestrować go jako źródło danych. W poniższym przykładzie przedstawiono polecenia interfejsu wiersza polecenia JBoss dla programu PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Utwórz skrypt startowy, który wywołuje polecenia interfejsu wiersza `startup_script.sh` polecenia JBoss. W poniższym przykładzie pokazano, jak wywołać `jboss-cli-commands.cli` . Później przekonfiguruj App Service uruchamiania tego skryptu po uruchomieniu kontenera. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Za pomocą wybranego klienta FTP przekaż sterownik JDBC, `jboss-cli-commands.cli` `startup_script.sh` , i definicję modułu do pliku `/site/deployments/tools/` .
2. Skonfiguruj lokację do uruchamiania `startup_script.sh` po uruchomieniu kontenera. W witrynie Azure Portal przejdź do **opcji Konfiguracja** Ustawienia  >  **ogólne Polecenie**  >  **uruchamiania.** Ustaw pole polecenia uruchamiania na `/home/site/deployments/tools/startup_script.sh` wartość . **Zapisz** zmiany.

Aby potwierdzić, że źródło danych zostało dodane do serwera JBoss, ssh do aplikacji internetowej i `$JBOSS_HOME/bin/jboss-cli.sh --connect` uruchom . Po na połączeniu z JBoss uruchom program , aby `/subsystem=datasources:read-resource` wydrukować listę źródeł danych.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Wybieranie wersji środowiska uruchomieniowego Java

App Service pozwala użytkownikom wybrać wersję główną środowiska JVM, taką jak Java 8 lub Java 11, a także wersję pomocniczą, taką jak 1.8.0_232 lub 11.0.5. Możesz również wybrać opcję automatycznego aktualizowania wersji pomocniczej w przypadku, gdy nowe wersje pomocnicze staną się dostępne. W większości przypadków lokacje produkcyjne powinny używać przypiętych wersji pomocniczej JVM. Zapobiegnie to nieoczekiwanym omdniom podczas automatycznej aktualizacji wersji pomocniczej.

Jeśli zdecydujesz się przypiąć wersję pomocniczą, musisz okresowo aktualizować wersję pomocniczą JVM w witrynie. Aby upewnić się, że aplikacja działa w nowszej wersji pomocniczej, utwórz miejsce przejściowe i zwiększaj wersję pomocniczą w lokacji przejściowej. Po potwierdzeniu, że aplikacja działa poprawnie w nowej wersji pomocniczej, możesz zamienić miejsca przejściowe i produkcyjne.

## <a name="jboss-eap-hardware-options"></a>Opcje sprzętu JBoss EAP

Oprogramowanie JBoss EAP jest dostępne tylko w opcjach sprzętu Premium i Izolowany. Klienci, którzy podczas publicznej wersji zapoznawczej tworzyli witrynę JBoss EAP w warstwie Bezpłatna, Współdzielona, Podstawowa lub Standardowa, powinni skalować w górę do warstwy sprzętu Premium lub Izolowana, aby uniknąć nieoczekiwanego zachowania.

## <a name="java-runtime-statement-of-support"></a>Instrukcja obsługi środowiska uruchomieniowego języka Java

### <a name="jdk-versions-and-maintenance"></a>Wersje i konserwacja JDK

Zestaw Java Development Kit (JDK) obsługiwany przez platformę Azure to [zestaw Zulu](https://www.azul.com/downloads/azure-only/zulu/) dostarczany za [pośrednictwem firmy Azul Systems.](https://www.azul.com/) Kompilacje OpenJDK Azul Zulu Enterprise to bezpłatne, wieloplatformowe, gotowe do użycia w środowisku produkcyjnym dystrybucje openJDK dla platformy Azure i Azure Stack przez firmy Microsoft i Azul Systems. Zawierają one wszystkie składniki do kompilowania i uruchamiania aplikacji Java SE. Zestaw JDK można zainstalować z instalacji [zestawu Java JDK.](/azure/developer/java/fundamentals/java-jdk-long-term-support)

Aktualizacje wersji głównych będą udostępniane za pośrednictwem nowych opcji środowiska uruchomieniowego w Azure App Service. Klienci aktualizują do tych nowszej wersji języka Java, konfigurując wdrożenie App Service i są odpowiedzialni za testowanie oraz zapewnianie, że główna aktualizacja spełnia ich potrzeby.

Obsługiwane JDK są automatycznie poprawiane kwartalnie w styczniu, kwietniu, lipcu i październiku każdego roku. Aby uzyskać więcej informacji na temat języka Java na platformie Azure, zobacz [ten dokument pomocy technicznej](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Poprawki i poprawki dotyczące głównych luk w zabezpieczeniach zostaną wydane natychmiast po ich staną się dostępne w systemie Azul Systems. "Główna" luka w zabezpieczeniach jest definiowana przez podstawową ocenę 9.0 lub wyższą w systemie [NIST Common Vulnerability Scoring System w wersji 2.](https://nvd.nist.gov/vuln-metrics/cvss)

Program Tomcat 8.0 osiągnął koniec cyklu życia [(EOL) od 30 września 2018 r.](https://tomcat.apache.org/tomcat-80-eol.html). Chociaż środowisko uruchomieniowe jest nadal dostępne Azure App Service, platforma Azure nie zastosuje aktualizacji zabezpieczeń do programu Tomcat 8.0. Jeśli to możliwe, przemigruj aplikacje do tomcat 8.5 lub 9.0. Zarówno tomcat 8.5, jak i 9.0 są dostępne na Azure App Service. Aby uzyskać więcej informacji, zobacz oficjalną [witrynę tomcat.](https://tomcat.apache.org/whichversion.html) 

### <a name="deprecation-and-retirement"></a>Wycofanie i wycofanie

Jeśli obsługiwane środowisko uruchomieniowe Java zostanie wycofane, deweloperzy platformy Azure korzystający z danego środowiska uruchomieniowego będą o wycofaniu co najmniej sześć miesięcy przed jego wycofaniu.


### <a name="local-development"></a>Programowanie lokalne

Deweloperzy mogą pobrać wersję produkcyjną azul Zulu Enterprise JDK do lokalnego tworzenia oprogramowania z witryny [pobierania azul.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Pomoc techniczna dla programistów

Pomoc techniczna dla obsługiwanego przez platformę Azure rozwiązania [Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna za pośrednictwem firmy Microsoft podczas opracowywania dla platformy Azure lub Azure Stack [z](https://azure.microsoft.com/overview/azure-stack/) kwalifikowanym [planem pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Następne kroki

Odwiedź Centrum [deweloperów platformy Azure dla języka Java,](/java/azure/) aby znaleźć przewodniki Szybki start, samouczki i dokumentację języka Java.

Odpowiedzi na ogólne pytania App Service dla systemu Linux, które nie są specyficzne dla programowania w języku Java, znajdują się w często zadawanych pytaniach dotyczących App Service [Linux.](faq-app-service-linux.md)
