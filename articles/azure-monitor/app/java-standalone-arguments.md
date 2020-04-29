---
title: Monitoruj aplikacje Java działające w dowolnym środowisku — Azure Monitor Application Insights
description: Monitorowanie wydajności aplikacji dla aplikacji Java działających w dowolnym środowisku z autonomicznym Agentem Java bez Instrumentacji aplikacji. Śledzenie rozproszone i mapa aplikacji.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641877"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurowanie JVM args autonomiczny Agent Java dla Azure Monitor Application Insights



## <a name="azure-environments"></a>Środowiska platformy Azure

Skonfiguruj [App Services](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Dodaj JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` w dowolnym miejscu `-jar <myapp.jar>`, na przykład:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Argumenty umieszczone po `-jar <myapp.jar>` są przekazane do aplikacji jako argumenty programu.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat zainstalowany za `apt-get` pośrednictwem lub`yum`

Jeśli zainstalowano program `apt-get` Tomcat `yum`za pośrednictwem programu lub, powinien `/etc/tomcat8/tomcat8.conf`istnieć plik.  Dodaj ten wiersz na końcu tego pliku:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat instalowany za pośrednictwem pobierania i rozpakowania

Jeśli zainstalowano program Tomcat za pośrednictwem [https://tomcat.apache.org](https://tomcat.apache.org)pobierania i rozpakować z programu, `<tomcat>/bin/catalina.sh`powinien istnieć plik.  Utwórz nowy plik w tym samym katalogu o nazwie `<tomcat>/bin/setenv.sh` z następującą zawartością:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Jeśli plik `<tomcat>/bin/setenv.sh` już istnieje, zmodyfikuj go i Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do. `CATALINA_OPTS`


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Uruchamianie Tomcat z wiersza polecenia

Znajdź plik `<tomcat>/bin/catalina.bat`.  Utwórz nowy plik w tym samym katalogu o nazwie `<tomcat>/bin/setenv.bat` z następującą zawartością:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Cudzysłowy nie są konieczne, ale jeśli chcesz je uwzględnić, odpowiednie umieszczanie jest następujące:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Jeśli plik `<tomcat>/bin/setenv.bat` już istnieje, po prostu zmodyfikuj ten plik i Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do `CATALINA_OPTS`.

### <a name="running-tomcat-as-a-windows-service"></a>Uruchamianie Tomcat jako usługi systemu Windows

Znajdź plik `<tomcat>/bin/tomcat8w.exe`.  Uruchom ten plik wykonywalny `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` i Dodaj `Java Options` go do `Java` karty pod kartą.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Serwer autonomiczny

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do istniejącej `JAVA_OPTS` zmiennej środowiskowej w pliku `JBOSS_HOME/bin/standalone.conf` (Linux) lub `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Serwer domeny

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do istniejącego `jvm-options` w `JBOSS_HOME/domain/configuration/host.xml`:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

W przypadku uruchamiania wielu serwerów zarządzanych na jednym hoście należy dodać `applicationinsights.agent.id` do `system-properties` każdego z nich: `server`

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Określona `applicationinsights.agent.id` wartość musi być unikatowa. Służy do tworzenia podkatalogu w katalogu ApplicationInsights, ponieważ każdy proces JVM wymaga własnej lokalnej konfiguracji ApplicationInsights i lokalnego pliku dziennika ApplicationInsights. Ponadto, jeśli raportowanie do centralnego modułu zbierającego, `applicationinsights.properties` plik jest współużytkowany przez wiele zarządzanych serwerów, więc określony `applicationinsights.agent.id` jest niezbędny do przesłaniania `agent.id` ustawienia w tym pliku udostępnionym. `applicationinsights.agent.rollup.id`może być analogicznie określony na serwerze, `system-properties` Jeśli zachodzi potrzeba zastąpienia `agent.rollup.id` ustawienia na serwerze zarządzanym.


## <a name="jetty-9"></a>Jetty 9

Dodaj te wiersze do`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do istniejącego `jvm-options` w `glassfish/domains/domain1/config/domain.xml`:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Otwórz konsolę zarządzania przejdź do pozycji **serwery > WebSphere serwery aplikacji > serwery aplikacji**, wybierz odpowiednie serwery aplikacji i kliknij pozycję: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
W obszarze "ogólne argumenty JVM" Dodaj następujące elementy:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Następnie Zapisz i ponownie uruchom serwer aplikacji.


## <a name="openliberty-18"></a>OpenLiberty 18

Utwórz nowy plik `jvm.options` w katalogu serwera (na przykład `<openliberty>/usr/servers/defaultServer`), a następnie Dodaj następujący wiersz:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
