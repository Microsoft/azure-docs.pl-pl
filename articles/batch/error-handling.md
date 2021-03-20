---
title: Obsługa błędów i wykrywanie w Azure Batch
description: Dowiedz się więcej o obsłudze błędów w przepływach pracy usługi Batch z punktu widzenia rozwoju.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85964281"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Obsługa błędów i wykrywanie w Azure Batch

Czasami może się okazać, że konieczna jest obsługa błędów zadań i aplikacji w ramach rozwiązania usługi Batch. W tym artykule omówiono typy błędów i sposoby ich rozwiązywania.

## <a name="error-codes"></a>Kody błędów

Typy ogólne błędów obejmują:

- Błędy sieci w przypadku żądań, które nigdy nie dotarły do partii lub gdy odpowiedź usługi Batch nie dotarła do klienta w czasie.
- Wewnętrzne błędy serwera (odpowiedź HTTP standardowego kodu stanu 5xx).
- Błędy związane z ograniczaniem wydajności, takie jak 429 lub 503 kod stanu odpowiedzi HTTP przy użyciu nagłówka retry-After.
- 4xx błędy, takie jak już istnieje i InvalidOperation. Oznacza to, że zasób nie jest w poprawnym stanie dla przejścia stanu.

Aby uzyskać szczegółowe informacje o określonych kodach błędów, w tym o kodach błędów dla interfejsu API REST, usługi Batch i zadania/planowania, zobacz [stan partii i kody błędów](/rest/api/batchservice/batch-status-and-error-codes).

## <a name="application-failures"></a>Błędy aplikacji

W czasie wykonywania aplikacja może tworzyć diagnostyczne dane wyjściowe, których można użyć do rozwiązywania problemów. Zgodnie z opisem w temacie [pliki i katalogi](files-and-directories.md)usługa Batch zapisuje standardowe dane wyjściowe i standardowe wyjście błędu `stdout.txt` do `stderr.txt` plików w katalogu zadań w węźle obliczeniowym.

Aby pobrać te pliki, można użyć portalu Azure lub jednego z zestawów SDK usługi Batch. Te pliki można pobrać w celu rozwiązania problemów za pomocą elementów [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) i [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask) w bibliotece platformy .NET usługi Batch.

## <a name="task-errors"></a>Błędy zadań

Błędy zadań znajdują się w kilku kategoriach.

### <a name="pre-processing-errors"></a>Błędy przetwarzania wstępnego

Jeśli nie można uruchomić zadania, dla tego zadania zostanie ustawiony błąd przetwarzania wstępnego.  

Błędy przetwarzania wstępnego mogą wystąpić, jeśli pliki zasobów zadania zostały przeniesione, konto magazynu nie jest już dostępne lub Wystąpił inny problem uniemożliwiający pomyślne skopiowanie plików do węzła.

### <a name="file-upload-errors"></a>Błędy przekazywania plików

Jeśli z jakiegoś powodu nie można przekazać plików określonych dla zadania, dla zadania zostanie ustawiony błąd przekazywania pliku.

Błędy przekazywania plików mogą wystąpić, jeśli sygnatura dostępu współdzielonego podana na potrzeby dostępu do usługi Azure Storage jest nieprawidłowa lub nie oferuje uprawnień do zapisu, jeśli konto magazynu jest już niedostępne, lub jeśli wystąpił inny problem uniemożliwiający pomyślne skopiowanie plików z węzła.

### <a name="application-errors"></a>Błędy aplikacji

Proces określony przez wiersz polecenia zadania podrzędnego może również zakończyć się niepowodzeniem. Proces zostaje uznany za nieudany, gdy proces wykonywany w ramach zadania zwróci kod zakończenia różny od zera (zobacz *Kody zakończenia zadania podrzędnego* w następnej sekcji).

W przypadku błędów aplikacji można skonfigurować usługi Batch do automatycznego ponawiania próby wykonania zadania do określonej liczby razy.

### <a name="constraint-errors"></a>Błędy ograniczenia

Można ustawić ograniczenie określające maksymalny czas trwania zadania, *maxWallClockTime*. Może to być przydatne do zakończenia zadań, które nie mogą kontynuować działania.

Po przekroczeniu maksymalnego czasu zadanie podrzędne jest oznaczane jako *ukończone*, ale kod zakończenia ma wartość `0xC000013A`, a pole *schedulingError* jest oznaczane jako `{ category:"ServerError", code="TaskEnded"}`.

## <a name="task-exit-codes"></a>Kody zakończenia zadania podrzędnego

Jak wspomniano wcześniej, zadanie podrzędne jest oznaczone jako nieudane przez usługę Batch, jeśli proces wykonywany przez zadanie podrzędne zwraca kod zakończenia różny od zera. Gdy zadanie podrzędne wykonuje proces, usługa Batch wypełnia właściwości kodu zakończenia zadania podrzędnego przy użyciu zwracanego kodu procesu.

Należy pamiętać, że kod zakończenia zadania podrzędnego nie jest określany przez usługę Batch. Kod zakończenia zadania podrzędnego jest określany przez sam proces lub system operacyjny, w którym proces jest wykonywany.

## <a name="task-failures-or-interruptions"></a>Błędy lub przerwy zadania

Od czasu do czasu zadania podrzędne mogą zakończyć się niepowodzeniem lub zostać przerwane. Sama aplikacja zadania może się nie powieść, węzeł, na którym uruchomiono zadanie, może zostać ponownie uruchomiony lub węzeł może zostać usunięty z puli podczas operacji zmiany rozmiaru (Jeśli zasady cofania alokacji puli są ustawione na natychmiastowe usuwanie węzłów bez oczekiwania na zakończenie zadań). We wszystkich przypadkach zadanie podrzędne może zostać automatycznie ponownie umieszczone w kolejce przy użyciu usługi Batch do wykonania w innym węźle.

Istnieje również możliwość, że sporadyczny problem może przestać odpowiadać lub trwać zbyt długo. Można ustawić maksymalny interwał wykonywania zadania podrzędnego. Po jego przekroczeniu usługa Batch przerywa działanie aplikacji zadania podrzędnego.

## <a name="connect-to-compute-nodes"></a>Łączenie z węzłami obliczeniowymi

Aby przeprowadzić dodatkowe debugowanie i rozwiązywanie problemów, można zalogować się zdalnie do węzła obliczeniowego. W portalu Azure można pobrać plik protokołu RDP w przypadku węzłów systemu Windows oraz uzyskać informacje o połączeniu z protokołem SSH w przypadku węzłów systemu Linux. Można to zrobić również przy użyciu interfejsów API usługi Batch, takich jak program [Batch .NET](/dotnet/api/microsoft.azure.batch.computenode) lub [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Aby połączyć się z węzłem za pośrednictwem protokołu RDP lub SSH, musisz najpierw utworzyć użytkownika w węźle. W tym celu możesz użyć witryny Azure Portal, [dodać konto użytkownika do węzła](/rest/api/batchservice/computenode/adduser) za pomocą interfejsu API REST usługi Batch, wywołać metodę [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) na platformie .NET usługi Batch lub wywołać metodę [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) w module języka Python usługi Batch.

Jeśli chcesz ograniczyć lub wyłączyć dostęp do węzłów obliczeniowych za pośrednictwem protokołu RDP lub SSH, zobacz [Configure or disable remote access to compute nodes in an Azure Batch pool (Konfigurowanie lub wyłączanie dostępu zdalnego do węzłów obliczeniowych w puli usługi Azure Batch)](pool-endpoint-configuration.md).

## <a name="troubleshoot-problem-nodes"></a>Rozwiązywanie problemów z węzłami problemu

W sytuacjach, w których niektóre z zadań kończą się niepowodzeniem, aplikacja kliencka lub usługa Batch mogą badać metadane nieudanych zadań w celu identyfikacji nieprawidłowo funkcjonującego węzła. Każdemu węzłowi w puli zostaje nadany unikatowy identyfikator, a węzeł, w którym jest uruchomione zadanie, jest dołączany do metadanych zadania. Po zidentyfikowaniu problemu dotyczącego węzła można wykonać kilka powiązanych czynności:

- **Ponowne uruchamianie węzła** ([rest](/rest/api/batchservice/computenode/reboot)  |  [.NET](/dotnet/api/microsoft.azure.batch.computenode.reboot))

    Ponowne uruchomienie węzła może czasami usunąć ukryte problemy, takie jak zablokowane procesy lub procesy, które uległy awarii. Jeśli pula używa zadania uruchamiania, a zadanie zawiera zadanie przygotowania zadania, zostaną one wykonane po ponownym uruchomieniu węzła.
- **Odtwórz węzeł z obrazu** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Spowoduje to ponowne zainstalowanie systemu operacyjnego w węźle. Podobnie jak w przypadku ponownego rozruchu węzła zadania uruchamiania i zadania przygotowania zadania są uruchamiane ponownie po odtworzeniu węzła z obrazu.
- **Usuń węzeł z puli** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    Czasami konieczne jest całkowite usunięcie węzła z puli.
- **Wyłącz planowanie zadań podrzędnych w węźle** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    Ta czynność przełącza węzeł w tryb „offline”, aby nie zostały do niego przypisane żadne dalsze zadania podrzędne, ale pozwala na dalsze działanie węzła i jego obecność w puli. Dzięki temu można dalej badać przyczyny błędów bez utraty danych nieudanego zadania, podczas gdy węzeł nie powoduje dodatkowych błędów zadania. Można na przykład wyłączyć planowanie zadań podrzędnych w węźle, a następnie zalogować się zdalnie, aby sprawdzić dzienniki zdarzeń węzła lub wykonać inne operacje związane z rozwiązywaniem problemów. Po zakończeniu badania można przenieść węzeł z powrotem do trybu online, włączając planowanie zadań ([rest](/rest/api/batchservice/computenode/enablescheduling)  |  [.NET](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)lub wykonując jedną z innych akcji omówionych wcześniej poniżej.

> [!IMPORTANT]
> Dzięki opisanym powyżej akcjom youc można określić, jak zadania aktualnie uruchomione w węźle są obsługiwane podczas wykonywania akcji. Na przykład po wyłączeniu planowania zadań podrzędnych w węźle za pomocą biblioteki klienta platformy .NET w usłudze Batch można określić wartość wyliczeniową [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption), aby wskazać, czy należy **przerwać** uruchomione zadania podrzędne, **umieścić je ponownie w kolejce** do planowania w innych węzłach lub zezwolić na ukończenie uruchomionych zadań przed wykonaniem akcji (**TaskCompletion**).

## <a name="retry-after-errors"></a>Ponów próbę po wystąpieniu błędów

Interfejsy API partii będą powiadamiane o wystąpieniu błędu. Wszystkie te możliwości są ponawiane i wszystkie obejmują globalną procedurę obsługi ponowień. Najlepszym rozwiązaniem jest użycie tego wbudowanego mechanizmu.

Po awarii należy poczekać chwilę (kilka sekund między ponownymi próbami) przed ponowną próbą. W przypadku ponawiania próby zbyt częste lub zbyt szybko, procedura obsługi ponowień będzie ograniczać.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [sprawdzać występowanie błędów puli i węzłów](batch-pool-node-error-checking.md).
- Dowiedz się [, jak sprawdzać występowanie błędów zadań i zadań](batch-job-task-error-checking.md).
- Przejrzyj listę [Stanów partii i kodów błędów](/rest/api/batchservice/batch-status-and-error-codes).
