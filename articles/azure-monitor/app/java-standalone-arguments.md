---
title: Dodawanie Application Insights JVM ARG-Azure Monitor dla języka Java
description: Jak dodać argument JVM, który włącza Application Insights Azure Monitor dla języka Java
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 9e824af23fd83725b426d5860186d3c4b1496e18
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448903"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>Dodawanie Application Insights JVM ARG-Azure Monitor dla języka Java



## <a name="azure-environments"></a>Środowiska platformy Azure

Skonfiguruj [App Services](../../app-service/configure-language-java.md#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Dodaj JVM ARG w `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` dowolnym miejscu `-jar` , na przykład:

```
java -javaagent:path/to/applicationinsights-agent-3.0.3.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Rozruch sprężynowy za pośrednictwem punktu wejścia platformy Docker

Jeśli używasz formularza *exec* , Dodaj parametr `"-javaagent:path/to/applicationinsights-agent-3.0.3.jar"` do listy parametrów w dowolnym miejscu przed `"-jar"` parametrem, na przykład:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.3.jar", "-jar", "<myapp.jar>"]
```

Jeśli używasz formularza *powłoki* , Dodaj JVM ARG w `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` dowolnym miejscu `-jar` , na przykład:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.3.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat zainstalowany za pośrednictwem `apt-get` lub `yum`

Jeśli zainstalowano program Tomcat za pośrednictwem `apt-get` `yum` programu lub, powinien istnieć plik `/etc/tomcat8/tomcat8.conf` .  Dodaj ten wiersz na końcu tego pliku:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.3.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat instalowany za pośrednictwem pobierania i rozpakowania

Jeśli zainstalowano program Tomcat za pośrednictwem pobierania i rozpakować z programu [https://tomcat.apache.org](https://tomcat.apache.org) , powinien istnieć plik `<tomcat>/bin/catalina.sh` .  Utwórz nowy plik w tym samym katalogu o nazwie `<tomcat>/bin/setenv.sh` z następującą zawartością:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.3.jar"
```

Jeśli plik `<tomcat>/bin/setenv.sh` już istnieje, zmodyfikuj go i Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` do `CATALINA_OPTS` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Uruchamianie Tomcat z wiersza polecenia

Znajdź plik `<tomcat>/bin/catalina.bat` .  Utwórz nowy plik w tym samym katalogu o nazwie `<tomcat>/bin/setenv.bat` z następującą zawartością:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.3.jar
```

Cudzysłowy nie są konieczne, ale jeśli chcesz je uwzględnić, odpowiednie umieszczanie jest następujące:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.3.jar"
```

Jeśli plik `<tomcat>/bin/setenv.bat` już istnieje, po prostu zmodyfikuj ten plik i Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` do `CATALINA_OPTS` .

### <a name="running-tomcat-as-a-windows-service"></a>Uruchamianie Tomcat jako usługi systemu Windows

Znajdź plik `<tomcat>/bin/tomcat8w.exe` .  Uruchom ten plik wykonywalny i Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` go do `Java Options` `Java` karty pod kartą.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Serwer autonomiczny

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` do istniejącej `JAVA_OPTS` zmiennej środowiskowej w pliku `JBOSS_HOME/bin/standalone.conf` (Linux) lub `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.3.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Serwer domeny

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` do istniejącego `jvm-options` w `JBOSS_HOME/domain/configuration/host.xml` :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.3.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

W przypadku uruchamiania wielu serwerów zarządzanych na jednym hoście należy dodać `applicationinsights.agent.id` do `system-properties` każdego z nich `server` :

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

Określona `applicationinsights.agent.id` wartość musi być unikatowa. Służy do tworzenia podkatalogu w katalogu ApplicationInsights, ponieważ każdy proces JVM wymaga własnej lokalnej konfiguracji ApplicationInsights i lokalnego pliku dziennika ApplicationInsights. Ponadto, jeśli raportowanie do centralnego modułu zbierającego, `applicationinsights.properties` plik jest współużytkowany przez wiele zarządzanych serwerów, więc określony `applicationinsights.agent.id` jest niezbędny do przesłaniania `agent.id` Ustawienia w tym pliku udostępnionym. `applicationinsights.agent.rollup.id` może być analogicznie określony na serwerze, `system-properties` Jeśli zachodzi potrzeba zastąpienia `agent.rollup.id` Ustawienia na serwerze zarządzanym.


## <a name="jetty-9"></a>Jetty 9

Dodaj te wiersze do `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.3.jar
```


## <a name="payara-5"></a>Payara 5

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` do istniejącego `jvm-options` w `glassfish/domains/domain1/config/domain.xml` :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.3.jar>
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
-javaagent:path/to/applicationinsights-agent-3.0.3.jar
```
Następnie Zapisz i ponownie uruchom serwer aplikacji.


## <a name="openliberty-18"></a>OpenLiberty 18

Utwórz nowy plik `jvm.options` w katalogu serwera (na przykład `<openliberty>/usr/servers/defaultServer` ), a następnie Dodaj następujący wiersz:
```
-javaagent:path/to/applicationinsights-agent-3.0.3.jar
```
