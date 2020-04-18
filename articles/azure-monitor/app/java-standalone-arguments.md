---
title: Monitorowanie aplikacji Java uruchomionych w dowolnym środowisku — usługa Azure Monitor Application Insights
description: Monitorowanie wydajności aplikacji java działających w dowolnym środowisku za pomocą autonomicznego agenta Java bez instrumentowania aplikacji. Rozproszone śledzenie i mapa aplikacji.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641877"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurowanie agenta autonomicznego JVM args Java dla usługi Azure Monitor Application Insights



## <a name="azure-environments"></a>Środowiska platformy Azure

Konfigurowanie [usług aplikacji](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Dodaj arg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` JVM `-jar <myapp.jar>`gdzieś przed , na przykład:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args umieszczone `-jar <myapp.jar>` po są przekazywane do aplikacji jako args programu.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat zainstalowany `apt-get` przez lub`yum`

Jeśli zainstalowałeś Tomcat przez `apt-get` lub `yum`, `/etc/tomcat8/tomcat8.conf`to powinieneś mieć plik .  Dodaj ten wiersz na końcu tego pliku:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat zainstalowany przez pobieranie i rozpakować

Jeśli zainstalowałeś Tomcat przez pobieranie [https://tomcat.apache.org](https://tomcat.apache.org)i rozpakować z `<tomcat>/bin/catalina.sh`, to powinieneś mieć plik .  Utwórz nowy plik w tym `<tomcat>/bin/setenv.sh` samym katalogu o nazwie o następującej zawartości:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Jeśli plik `<tomcat>/bin/setenv.sh` już istnieje, zmodyfikuj ten plik i dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` go do `CATALINA_OPTS`pliku .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Uruchamianie tomcat z wiersza polecenia

Zlokalizuj plik `<tomcat>/bin/catalina.bat`.  Utwórz nowy plik w tym `<tomcat>/bin/setenv.bat` samym katalogu o nazwie o następującej zawartości:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Cytaty nie są konieczne, ale jeśli chcesz je uwzględnić, właściwe umieszczenie jest:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Jeśli plik `<tomcat>/bin/setenv.bat` już istnieje, po prostu `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` zmodyfikuj ten plik i dodaj go do `CATALINA_OPTS`.

### <a name="running-tomcat-as-a-windows-service"></a>Uruchamianie programu Tomcat jako usługi systemu Windows

Zlokalizuj plik `<tomcat>/bin/tomcat8w.exe`.  Uruchom ten plik `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` wykonywalny i dodaj do `Java Options` `Java` karty.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Samodzielny serwer

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do `JAVA_OPTS` istniejącej zmiennej `JBOSS_HOME/bin/standalone.conf` środowiskowej `JBOSS_HOME/bin/standalone.conf.bat` w pliku (Linux) lub (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Serwer domeny

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do `jvm-options` istniejącego w `JBOSS_HOME/domain/configuration/host.xml`:

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

Jeśli na jednym hoście jest uruchomionych wiele serwerów zarządzanych, należy dodać `applicationinsights.agent.id` do `system-properties` każdego: `server`

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

Określona `applicationinsights.agent.id` wartość musi być unikatowa. Służy do tworzenia podkatalogu w katalogu applicationinsights, ponieważ każdy proces JVM wymaga własnego lokalnego pliku dziennika applicationinsights i local applicationinsights. Ponadto w przypadku raportowania do `applicationinsights.properties` modułu zbierającego centralnego plik jest `applicationinsights.agent.id` współużytkowane przez `agent.id` wiele serwerów zarządzanych, a więc określony jest potrzebny do zastąpienia ustawienia w tym pliku udostępnionym. `applicationinsights.agent.rollup.id`można podobnie określić w serwerze, `system-properties` jeśli trzeba zastąpić `agent.rollup.id` ustawienie dla serwera zarządzanego.


## <a name="jetty-9"></a>Molo 9

Dodaj te wiersze do`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5 (własna)

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do `jvm-options` istniejącego w `glassfish/domains/domain1/config/domain.xml`:

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

## <a name="websphere-8"></a>Sfera internetowa 8

Otwórz Konsolę zarządzania przejdź do **serwerów > serwerów aplikacji WebSphere > serwerów aplikacji**, wybierz odpowiednie serwery aplikacji i kliknij na: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
W "Ogólne argumenty JVM" dodaj następujące elementy:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Następnie zapisz i uruchom ponownie serwer aplikacji.


## <a name="openliberty-18"></a>OtwórzLibertie 18

Utwórz nowy `jvm.options` plik w katalogu serwera `<openliberty>/usr/servers/defaultServer`(na przykład ) i dodaj ten wiersz:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
