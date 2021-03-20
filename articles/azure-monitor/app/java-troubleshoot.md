---
title: Rozwiązywanie problemów Application Insights w projekcie sieci Web w języku Java
description: Przewodnik rozwiązywania problemów — monitorowanie aplikacji Java na żywo za pomocą Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: df1f58418452ecafacddd013ab815ebca5cb8a35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097555"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java-sdk"></a>Rozwiązywanie problemów i narzędzia pytań i odpowiedzi dla Application Insights for Java SDK

> [!IMPORTANT]
> Zalecanym podejściem do monitorowania aplikacji Java jest użycie autoinstrumentacji bez zmiany kodu. Postępuj zgodnie z wytycznymi dla [Application Insights Java 3,0 Agent](./java-in-process-agent.md).

Masz pytania lub problemy z [usługą Azure Application Insights w języku Java][java]? Oto kilka wskazówek.

## <a name="build-errors"></a>Błędy kompilacji
**W przypadku, gdy dodajesz zestaw Application Insights SDK za pośrednictwem Maven lub Gradle, nastąpiło przebudowywanie lub IntelliJ.**

* Jeśli element Dependency `<version>` używa wzorca z symbolami wieloznacznymi (np. (Maven) `<version>[2.0,)</version>` lub (Gradle) `version:'2.+'` ), spróbuj określić określoną wersję zamiast `2.6.2` .

## <a name="no-data"></a>Brak danych
**Dodano Application Insights pomyślnie i uruchomiono moją aplikację, ale w portalu nigdy nie były wyświetlane dane.**

* Poczekaj chwilę, a następnie kliknij przycisk Odśwież. Wykresy są odświeżane okresowo, ale można również odświeżać je ręcznie. Interwał odświeżania zależy od zakresu czasu wykresu.
* Sprawdź, czy masz zdefiniowany klucz Instrumentacji w pliku ApplicationInsights.xml (w folderze Resources w projekcie) lub skonfigurowany jako zmienna środowiskowa.
* Sprawdź, czy `<DisableTelemetry>true</DisableTelemetry>` w pliku XML nie ma węzła.
* W zaporze może być konieczne otwarcie portów TCP 80 i 443 dla ruchu wychodzącego do dc.services.visualstudio.com. Zapoznaj się z [pełną listą wyjątków zapory](./ip-addresses.md)
* Na tablicy startowej Microsoft Azure Zapoznaj się z mapą stanu usługi. Jeśli istnieją jakieś wskazania alertów, poczekaj, aż powróci do programu OK, a następnie zamknij i ponownie otwórz blok aplikacji Application Insights.
* [Włącz rejestrowanie](#debug-data-from-the-sdk) , dodając `<SDKLogger />` element w węźle głównym w pliku ApplicationInsights.xml (w folderze zasoby w projekcie) i sprawdź wpisy z systemem AI: info/warn/Error dla wszystkich podejrzanych dzienników. 
* Upewnij się, że prawidłowy plik ApplicationInsights.xml został pomyślnie załadowany przez zestaw SDK języka Java, przeglądając komunikaty wyjściowe konsoli dla "plik konfiguracyjny został pomyślnie znaleziony".
* Jeśli nie można odnaleźć pliku konfiguracji, sprawdź komunikaty wyjściowe, aby zobaczyć, gdzie jest wyszukiwany plik konfiguracji, i upewnij się, że ApplicationInsights.xml znajduje się w jednej z tych lokalizacji wyszukiwania. Zgodnie z zasadą można umieścić plik konfiguracji w sąsiedztwie Application Insights JARs zestawu SDK. Na przykład: w Tomcat, oznacza to folder WEB-INF/Classes. Podczas opracowywania można umieścić ApplicationInsights.xml w folderze Resources projektu sieci Web.
* Sprawdź również na [stronie problemy](https://github.com/microsoft/ApplicationInsights-Java/issues) z usługą GitHub znane problemy związane z zestawem SDK.
* Aby uniknąć problemów z konfliktami wersji, należy użyć tej samej wersji Application Insights Core, Web, Agent i rejestrowania.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Użyto, aby wyświetlić dane, ale zostało ono zatrzymane
* Czy osiągnięto miesięczny limit liczby punktów danych? Otwórz przystawkę ustawienia/przydział i Cennik, aby się dowiedzieć. Jeśli tak, możesz uaktualnić plan lub uregulować dodatkową pojemność. Zobacz [schemat cen](https://azure.microsoft.com/pricing/details/application-insights/).
* Czy ostatnio uaktualniono zestaw SDK? Upewnij się, że w katalogu projektu znajdują się tylko unikatowe Jars zestawu SDK. Nie powinny istnieć dwie różne wersje zestawu SDK.
* Czy oglądasz prawidłowy zasób AI? Dopasuj iKey aplikacji do zasobu, w którym oczekujesz danych telemetrycznych. Powinny być takie same.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nie widzę wszystkich danych, których oczekuje
* Otwórz stronę użycie i Szacowana cena i sprawdź, czy [próbkowanie](./sampling.md) jest w działaniu. (100% transmisja oznacza, że próbkowanie nie jest w trakcie operacji). Usługę Application Insights można ustawić tak, aby akceptowała tylko część telemetrii, która dociera do aplikacji. Dzięki temu można utrzymywać w ramach miesięcznego przydziału danych telemetrycznych.
* Czy włączono próbkowanie zestawu SDK? Jeśli tak, dane będą próbkowane według stawki określonej dla wszystkich odpowiednich typów.
* Czy używasz starszej wersji zestawu Java SDK? Począwszy od wersji 2.0.1, wprowadziliśmy mechanizm odporności na uszkodzenia do obsługi sporadycznych awarii sieci i zaplecza, jak również trwałości danych na dyskach lokalnych.
* Czy masz ograniczone ograniczenia z powodu nadmiernej liczby telemetrii? Jeśli włączysz rejestrowanie informacji, zostanie wyświetlony komunikat dziennika "aplikacja jest ograniczona". Bieżący limit to 32-elementowych elementów telemetrycznych/sekundę.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agent języka Java nie może przechwycić danych zależności
* Czy skonfigurowano agenta Java przez [skonfigurowanie agenta Java](java-agent.md) ?
* Upewnij się, że zarówno plik JAR agenta Java, jak i AI-Agent.xml są umieszczane w tym samym folderze.
* Upewnij się, że zależność, którą próbujesz zbierać, jest obsługiwana przez funkcję Autocollection. Obecnie obsługujemy tylko usługę MySQL, MsSQL, Oracle DB i pamięć podręczną Azure dla kolekcji zależności Redis.

## <a name="no-usage-data"></a>Brak danych użycia
**Widzę dane dotyczące żądań i czasów odpowiedzi, ale nie wyświetlaj strony, przeglądarki ani danych użytkownika.**

Pomyślnie skonfigurowano aplikację w celu wysyłania danych telemetrycznych z serwera. Teraz następnym krokiem jest [skonfigurowanie stron sieci Web w celu wysyłania danych telemetrycznych z przeglądarki sieci Web][usage].

Alternatywnie, jeśli klient jest aplikacją na [telefonie lub innym urządzeniu][platforms], można wysłać dane telemetryczne z tego miejsca.

Użyj tego samego klucza Instrumentacji w celu skonfigurowania telemetrii klienta i serwera. Dane będą wyświetlane w tym samym Application Insights zasobów i będzie można skorelować zdarzenia z klienta i serwera.

## <a name="disabling-telemetry"></a>Wyłączanie telemetrii
**Jak mogę wyłączyć zbieranie danych telemetrycznych?**

W kodzie:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Lub**

Aktualizowanie ApplicationInsights.xml (w folderze zasoby w projekcie). Dodaj następujące elementy w węźle głównym:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Przy użyciu metody XML, należy ponownie uruchomić aplikację po zmianie wartości.

## <a name="changing-the-target"></a>Zmiana elementu docelowego
**Jak mogę zmienić zasób platformy Azure, do którego mój projekt wysyła dane?**

* [Pobierz klucz Instrumentacji nowego zasobu.][java]
* Jeśli dodano Application Insights do projektu przy użyciu Azure Toolkit for Eclipse, kliknij prawym przyciskiem myszy projekt sieci Web, wybierz pozycję **Azure**, **Skonfiguruj Application Insights** i Zmień klucz.
* Jeśli klucz Instrumentacji został skonfigurowany jako zmienna środowiskowa, zaktualizuj wartość zmiennej środowiskowej przy użyciu nowego iKey.
* W przeciwnym razie zaktualizuj klucz w ApplicationInsights.xml w folderze Resources w projekcie.

## <a name="debug-data-from-the-sdk"></a>Debuguj dane z zestawu SDK

**Jak mogę się dowiedzieć, co robi zestaw SDK?**

Aby uzyskać więcej informacji na temat tego, co dzieje się w interfejsie API, Dodaj `<SDKLogger/>` w węźle głównym pliku konfiguracji ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Możesz również nakazać Rejestratorowi wyjście do pliku:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Rozruch z sprężyną Starter

Aby włączyć rejestrowanie zestawu SDK za pomocą aplikacji do rozruchu sprężynowego przy użyciu Application Insights sprężynowego rozruchu Starter, Dodaj następujący `application.properties` plik do pliku:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

lub, aby drukować do błędu standardowego:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agent środowiska Java

Aby włączyć rejestrowanie agenta JVM, zaktualizuj [ plikAI-Agent.xml](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Właściwości wiersza polecenia języka Java
_Od wersji 2.4.0_

Aby włączyć rejestrowanie przy użyciu opcji wiersza polecenia, bez zmiany plików konfiguracji:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

lub, aby drukować do błędu standardowego:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Ekran startowy platformy Azure
**Przeglądam [Azure Portal](https://portal.azure.com). Czy mapa poinformuje mnie coś o mojej aplikacji?**

Nie, pokazuje kondycję serwerów platformy Azure na całym świecie.

*Jak znaleźć dane na temat mojej aplikacji z tablicy startowej platformy Azure (ekran główny)?*

Przy założeniu, że [aplikacja zostanie skonfigurowana do Application Insights][java], kliknij przycisk Przeglądaj, wybierz pozycję Application Insights, a następnie wybierz zasób aplikacji utworzony dla aplikacji. Aby szybciej korzystać z przyszłości, możesz przypiąć aplikację do tablicy startowej.

## <a name="intranet-servers"></a>Serwery intranetowe
**Czy mogę monitorować serwer w intranecie?**

Tak, pod warunkiem, że serwer może wysyłać dane telemetryczne do portalu Application Insights za pomocą publicznego Internetu.

Może być konieczne [otwarcie niektórych portów wychodzących w zaporze serwera](./ip-addresses.md#outgoing-ports) , aby umożliwić zestawowi SDK wysyłanie danych do portalu.

## <a name="data-retention"></a>Przechowywanie danych
**Jak długo dane są przechowywane w portalu? Czy jest zabezpieczony?**

Zobacz [przechowywanie i prywatność danych][data].

## <a name="debug-logging"></a>Rejestrowanie debugowania
Application Insights używa `org.apache.http` . Jest to przeniesiono w Application Insights rdzeń Jars w przestrzeni nazw `com.microsoft.applicationinsights.core.dependencies.http` . Dzięki temu Application Insights obsługiwać scenariusze, w których różne wersje tych samych `org.apache.http` istnieją w jednej bazie kodu.

>[!NOTE]
>Jeśli włączysz rejestrowanie na poziomie debugowania dla wszystkich przestrzeni nazw w aplikacji, zostaną one uznane przez wszystkie wykonywane moduły, w tym `org.apache.http` nazwa zmieniona jako `com.microsoft.applicationinsights.core.dependencies.http` . Application Insights nie będzie w stanie zastosować filtrowania dla tych wywołań, ponieważ jest ono wykonywane przez bibliotekę Apache. Rejestrowanie na poziomie debugowania daje znaczną ilość danych dzienników i nie jest zalecane w przypadku wystąpień produkcyjnych na żywo.

## <a name="next-steps"></a>Następne kroki
**Skonfiguruję Application Insights dla aplikacji serwera Java. Co jeszcze można zrobić?**

* [Monitoruj dostępność stron sieci Web][availability]
* [Monitorowanie użycia stron sieci Web][usage]
* [Śledzenie użycia i diagnozowanie problemów w aplikacjach urządzeń][platforms]
* [Napisz kod umożliwiający śledzenie użycia aplikacji][track]
* [Przechwyć dzienniki diagnostyczne][javalogs]

## <a name="get-help"></a>Uzyskaj pomoc
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Zadawanie problemu w usłudze GitHub](https://github.com/microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[data]: ./data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ./platforms.md
[track]: ./api-custom-events-metrics.md
[usage]: javascript.md

