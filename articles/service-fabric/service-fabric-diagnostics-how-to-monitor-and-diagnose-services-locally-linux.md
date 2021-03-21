---
title: Debugowanie aplikacji Service Fabric platformy Azure w systemie Linux
description: Dowiedz się, jak monitorować i diagnozować usługi Service Fabric na lokalnym komputerze deweloperskim systemu Linux.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 523cb0d1a8e8f322c1936f1fe52a954399b2acc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88999771"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Monitorowanie i diagnozowanie usług w ramach instalacji lokalnego komputera z systemem Linux


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów, dzięki czemu usługi mogą kontynuować minimalne zakłócenia środowiska użytkownika. Monitorowanie i Diagnostyka mają kluczowe znaczenie w rzeczywistym wdrożonym środowisku produkcyjnym. Zastosowanie podobnego modelu podczas tworzenia usług gwarantuje, że potok diagnostyczny działa po przejściu do środowiska produkcyjnego. Service Fabric ułatwia deweloperom usług implementację diagnostyki, która może bezproblemowo współpracować zarówno z lokalnymi konfiguracjami programistycznymi na jednym komputerze, jak i rzeczywistymi konfiguracjami klastrów produkcyjnych.


## <a name="debugging-service-fabric-java-applications"></a>Debugowanie Service Fabric aplikacji Java

W przypadku aplikacji Java dostępnych jest [wiele platform rejestrowania](https://en.wikipedia.org/wiki/Java_logging_framework) . Ponieważ `java.util.logging` jest domyślną opcją środowiska JRE, jest również używana do [przykładów kodu w usłudze GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). W poniższej dyskusji wyjaśniono, jak skonfigurować `java.util.logging` strukturę.

Za pomocą języka Java. util. rejestrowanie można przekierować Dzienniki aplikacji do pamięci, strumieni danych wyjściowych, plików konsoli lub gniazd. Dla każdej z tych opcji istnieją już domyślne programy obsługi w strukturze. Można utworzyć plik, `app.properties` Aby skonfigurować procedurę obsługi plików dla aplikacji w celu przekierowania wszystkich dzienników do pliku lokalnego.

Poniższy fragment kodu zawiera przykładową konfigurację:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Folder wskazany przez `app.properties` plik musi istnieć. Po `app.properties` utworzeniu pliku należy również zmodyfikować skrypt punktu wejścia `entrypoint.sh` w `<applicationfolder>/<servicePkg>/Code/` folderze, aby ustawić właściwość `java.util.logging.config.file` na `app.properties` plik. Wpis powinien wyglądać podobnie do następującego fragmentu kodu:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ta konfiguracja powoduje, że dzienniki są zbierane w sposób rotacyjny na `/tmp/servicefabric/logs/` . Plik dziennika w tym przypadku ma nazwę mysfapp% u .% g. log, gdzie:
* **% u** jest unikatowym numerem do rozwiązywania konfliktów między jednoczesnymi procesami Java.
* **% g** jest numerem generacji umożliwiającym rozróżnienie między obracanymi dziennikami.

Domyślnie jeśli żadna procedura obsługi nie jest jawnie skonfigurowana, program obsługi konsoli jest zarejestrowany. Jeden może wyświetlać dzienniki w dzienniku systemowym w obszarze/var/log/syslog.

Aby uzyskać więcej informacji, zobacz [przykłady kodu w](https://github.com/Azure-Samples/service-fabric-java-getting-started)serwisie GitHub.


## <a name="debugging-service-fabric-c-applications"></a>Debugowanie Service Fabric aplikacji C#


W systemie Linux dostępne są wiele platform do śledzenia aplikacji CoreCLR. Aby uzyskać więcej informacji, zobacz [rozszerzenia .NET do rejestrowania](https://github.com/dotnet/extensions/tree/master/src/Logging).  Ze względu na to, że element EventSource jest znany deweloperom języka C#, "w tym artykule zawarto śledzenie w przykładach CoreCLR w systemie Linux.

Pierwszym krokiem jest dołączenie system. Diagnostics. Tracing, aby można było napisać dzienniki do pamięci, strumieni wyjściowych lub plików konsoli.  W przypadku rejestrowania przy użyciu elementu EventSource Dodaj następujący projekt do project.jsna:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Możesz użyć niestandardowego odbiornika do nasłuchiwania zdarzenia usługi, a następnie odpowiednio przekierować je do plików śledzenia. Poniższy fragment kodu przedstawia przykładową implementację rejestrowania przy użyciu elementu EventSource i niestandardowego odbiornika:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


W poprzednim fragmencie kodu są wyprowadzane dzienniki do pliku w programie `/tmp/MyServiceLog.txt` . Ta nazwa pliku musi być odpowiednio aktualizowana. W przypadku, gdy chcesz przekierować dzienniki do konsoli programu, użyj następującego fragmentu kodu w dostosowanej klasie odbiornika:

```csharp
public static TextWriter Out = Console.Out;
```

Przykłady w [języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) używają elementu EventSource i niestandardowego odbiornika do rejestrowania zdarzeń do pliku.



## <a name="next-steps"></a>Następne kroki
Ten sam kod śledzenia dodany do aplikacji również współdziała z diagnostyką aplikacji w klastrze platformy Azure. Zapoznaj się z tymi artykułami, które omawiają różne opcje narzędzi i opisują, jak je skonfigurować.
* [Jak zbierać dzienniki przy użyciu Diagnostyka Azure](./service-fabric-diagnostics-event-aggregation-lad.md)
