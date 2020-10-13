---
title: Uruchamianie i zatrzymywanie węzłów klastra
description: Dowiedz się, jak używać iniekcji błędów do testowania aplikacji Service Fabric przez uruchamianie i zatrzymywanie węzłów klastra.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021259"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Zastępowanie węzła początkowego i Zatrzymaj interfejsy API węzła przy użyciu interfejsu API przejścia węzła

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Co robią interfejsy API Zatrzymaj węzeł i węzeł uruchamiania?

Interfejs API zatrzymania węzła (Managed: [StopNodeAsync ()][stopnode], PowerShell: [stop-ServiceFabricNode][stopnodeps]) zatrzymuje węzeł Service Fabric.  Węzeł Service Fabric to proces, a nie maszyna wirtualna lub maszyna — maszyna wirtualna lub maszyna nadal będzie działać.  W pozostałej części dokumentu "Node" będzie oznaczać Service Fabric węzła.  Zatrzymywanie węzła powoduje zatrzymanie w stanie *zatrzymania* , w którym nie jest elementem członkowskim klastra i nie może hostować usług, co symuluje węzeł *w dół* .  Jest to przydatne w przypadku wstrzykiwania błędów do systemu w celu przetestowania aplikacji.  Interfejs API uruchamiania węzła (Managed: [StartNodeAsync ()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) Odwraca działanie interfejsu API zatrzymania węzła, co powoduje przywrócenie stanu węzła do normalnego.

## <a name="why-are-we-replacing-these"></a>Dlaczego zamieniamy te?

Jak opisano wcześniej, *zatrzymany* węzeł Service Fabric jest węzłem celowym przeznaczonym do użycia interfejsu API zatrzymania węzła.  Węzeł *w dół* jest węzłem, który nie działa z innych przyczyn (na przykład maszyna wirtualna lub maszyna jest wyłączona).  W przypadku interfejsu API Zatrzymaj węzeł System nie ujawnia informacji do rozróżnienia między *zatrzymanymi* węzłami i węzłami *w dół* .

Ponadto niektóre błędy zwrócone przez te interfejsy API nie są tak, jak to możliwe.  Na przykład wywołanie interfejsu API Zatrzymaj węzeł na już *zatrzymanym* węźle zwróci błąd *InvalidAddress*.  To środowisko może być udoskonalone.

Ponadto czas, przez jaki węzeł jest zatrzymany, ma wartość "nieskończone" do momentu wywołania interfejsu API uruchamiania węzła.  Znaleźliśmy, że może to spowodować problemy i może być podatna na błędy.  Na przykład pojawiły się problemy, w których użytkownik wywołał interfejs API Zatrzymaj węzeł w węźle, a następnie go zapomniano.  Później nie było jasne, jeśli węzeł został *wyłączony* lub *zatrzymany*.


## <a name="introducing-the-node-transition-apis"></a>Wprowadzenie do interfejsów API przejścia węzła

Te problemy zostały opisane powyżej w nowym zestawie interfejsów API.  Nowy interfejs API przejścia węzła (zarządzany: [StartNodeTransitionAsync ()][snt]) może służyć do przechodzenia do stanu *zatrzymanego* węzła Service Fabric lub przechodzenia z stanu *zatrzymanego* do normalnego.  Należy pamiętać, że "Start" w nazwie interfejsu API nie odwołuje się do uruchamiania węzła.  Odnosi się do rozpoczęcia operacji asynchronicznej wykonywanej przez system w celu przejścia węzła do stanu *zatrzymania* lub uruchomienia.

**Użycie**

Jeśli interfejs API przejścia węzła nie zgłasza wyjątku po wywołaniu, system zaakceptował operację asynchroniczną i wykona ją.  Pomyślne wywołanie nie oznacza, że operacja została jeszcze zakończona.  Aby uzyskać informacje o bieżącym stanie operacji, Wywołaj interfejs API postępu przejścia węzła (zarządzany: [GetNodeTransitionProgressAsync ()][gntp]) z identyfikatorem GUID używanym podczas wywoływania interfejsu API przejścia węzła dla tej operacji.  Interfejs API postępu przejścia węzła zwraca obiekt NodeTransitionProgress.  Właściwość stanu tego obiektu określa bieżący stan operacji.  Jeśli stan to "uruchomiona", wykonywana jest operacja.  Jeśli zostanie ukończona, operacja zakończyła się bez błędu.  Jeśli jest to błąd, wystąpił problem podczas wykonywania operacji.  Właściwość wyjątku właściwości wyniku wskazuje, co to jest problem.  Zobacz https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate , aby uzyskać więcej informacji na temat właściwości State oraz poniższej sekcji "Przykładowe użycie" dla przykładów kodu.


**Rozróżnianie między zatrzymanym węzłem a węzłem w dół** Jeśli węzeł zostanie *zatrzymany* przy użyciu interfejsu API przejścia węzła, dane wyjściowe zapytania węzła (zarządzane: [GetNodeListAsync ()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) będą pokazywały, że ten węzeł ma właściwość *iszatrzymania* o wartości true.  Należy zauważyć, że różni się od wartości właściwości *NodeStatus* , która zostanie wyświetlona *.*  Jeśli właściwość *NodeStatus* ma wartość " *Down*", ale *iszatrzymana* jest false, węzeł nie został zatrzymany przy użyciu interfejsu API przejścia węzła i *nie jest z innego powodu.*  Jeśli właściwość *Iszatrzymana* ma wartość true, a właściwość *NodeStatus* nie *działa, to*została zatrzymana przy użyciu interfejsu API przejścia węzła.

Uruchomienie *zatrzymanego* węzła przy użyciu interfejsu API przejścia węzła zwróci go, aby ponownie działać jako normalny element członkowski klastra.  W danych wyjściowych interfejsu API zapytania węzła zostanie wyświetlony komunikat *Iszatrzymany* jako FAŁSZ i *NodeStatus* jako coś, który nie jest wyłączony (na przykład w górę).


**Ograniczony czas trwania** W przypadku korzystania z interfejsu API przejścia węzła do zatrzymania węzła jeden z wymaganych parametrów, *stopNodeDurationInSeconds*, reprezentuje czas (w sekundach) *zatrzymania*węzła.  Ta wartość musi mieścić się w dozwolonym zakresie, który ma wartość minimalną 600 i maksymalnie 14400.  Po upływie tego czasu węzeł zostanie automatycznie uruchomiony ponownie w stanie up.  Przykład użycia można znaleźć w przykładzie 1 poniżej.

> [!WARNING]
> Unikaj mieszania interfejsów API przejścia węzła i węzła Zatrzymaj i interfejsów API węzła uruchomieniowego.  Zalecenie polega na użyciu tylko interfejsu API przejścia węzła.  >, jeśli węzeł został już zatrzymany przy użyciu interfejsu API Zatrzymaj węzła, powinien zostać uruchomiony przy użyciu interfejsu API uruchamiania węzła najpierw przed użyciem interfejsów API przejścia węzła >.

> [!WARNING]
> Nie można jednocześnie wykonać wielu wywołań interfejsów API przejścia węzła w tym samym węźle.  W takiej sytuacji interfejs API przechodzenia węzłów będzie > zgłaszać element Fabricexception z wartością właściwości ErrorCode równą NodeTransitionInProgress.  Gdy przejście węzła w określonym węźle > zostało uruchomione, należy poczekać, aż operacja osiągnie stan terminalu (ukończono, błąd lub ForceCancelled) przed rozpoczęciem > nowym przeniesieniu w tym samym węźle.  Równoległe wywołania przejścia węzła w różnych węzłach są dozwolone.


#### <a name="sample-usage"></a>Przykładowe użycie


**Przykład 1** — Poniższy przykład używa interfejsu API przejścia węzła, aby zatrzymać węzeł.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Przykład 2** — następujący przykład uruchamia *zatrzymany* węzeł.  Używa ona niektórych metod pomocnika z pierwszego przykładu.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Przykład 3** — następujący przykład pokazuje nieprawidłowe użycie.  To użycie jest nieprawidłowe, ponieważ wartość *stopDurationInSeconds* jest większa niż dozwolony zakres.  Ponieważ StartNodeTransitionAsync () zakończy się niepowodzeniem z powodu błędu krytycznego, operacja nie została zaakceptowana, a interfejs API postępu nie powinien zostać wywołany.  W tym przykładzie zastosowano niektóre metody pomocnika z pierwszego przykładu.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Przykład 4** — Poniższy przykład pokazuje informacje o błędzie, które zostaną zwrócone z interfejsu API postępu przejścia węzła, gdy operacja zainicjowana przez interfejs API przejścia węzła zostanie zaakceptowana, ale zostanie uruchomiona później podczas wykonywania.  W przypadku nie jest to możliwe, ponieważ interfejs API przejścia węzła próbuje uruchomić węzeł, który nie istnieje.  W tym przykładzie zastosowano niektóre metody pomocnika z pierwszego przykładu.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
