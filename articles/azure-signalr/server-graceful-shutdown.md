---
title: Bezproblemowo Zatrzymaj serwer aplikacji.
description: Ten artykuł zawiera informacje dotyczące bezpiecznego zamykania serwera aplikacji sygnalizującego
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98201675"
---
# <a name="server-graceful-shutdown"></a>Bezpieczne zamykanie serwera
Usługa sygnalizująca Microsoft Azure zapewnia dwa tryby bezpiecznego zamykania serwera. 

Główną zaletą korzystania z tej funkcji jest uniemożliwienie klientom nieoczekiwanej przerwy w nawiązywaniu połączeń. 

Zamiast tego możesz poczekać, aż połączenia klienckie zamknął się w ramach logiki biznesowej, lub nawet Przeprowadź migrację połączenia klienta na inny serwer bez utraty danych. 

## <a name="how-it-works"></a>Jak to działa

Ogólnie rzecz biorąc, będzie można wykonać cztery etapy procesu bezpiecznego zamykania:

1. **Ustawianie serwera w tryb offline**

    Oznacza to, że żadne połączenia klienckie nie będą kierowane do tego serwera.

2. **`OnShutdown`Punkty zaczepienia wyzwalacza**

    Punkty zaczepienia zamknięcia można zarejestrować dla każdego centrum, którego właścicielem jest serwer.
    Zostaną one wywołane w odniesieniu do zarejestrowanej kolejności bezpośrednio po otrzymaniu odpowiedzi **FINACK** z naszej usługi Azure sygnalizującej, co oznacza, że ten serwer został ustawiony w trybie offline w usłudze Azure Signal Service.

    Na tym etapie można emitować komunikaty lub wykonywać pewne zadania czyszczenia, po wykonaniu wszystkich punktów zaczepienia zamknięcia przejdziemy do kolejnego etapu.

3. **Poczekaj na zakończenie wszystkich połączeń klienta** zależnie od wybranego trybu, ale może to być:

    **Tryb ustawiony na WaitForClientsToClose**

    Usługa Azure Signal Service będzie utrzymywać istniejących klientów.

    Może być konieczne zaprojektowanie metody, na przykład emisja komunikatu zamykającego do wszystkich klientów, a następnie umożliwienie klientom podjęcia decyzji o tym, kiedy zamknąć/ponownie nawiązać połączenie.

    Przeczytaj [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) na potrzeby przykładowego użycia, które emitują komunikat "Exit", aby wyzwolić zamknięcie klienta w elemencie Hook zamknięcia.

    **Tryb ustawiony na MigrateClients**

    Usługa Azure sygnalizująca podejmie próbę przekierowania połączenia klienta na tym serwerze na inny prawidłowy serwer. 
    
    W tym scenariuszu `OnConnectedAsync` i `OnDisconnectedAsync` zostanie wyzwolone na nowym serwerze i starym serwerze odpowiednio z `IConnectionMigrationFeature` zestawem w `HttpContext` , który może służyć do określenia, czy połączenie z klientem zostało migrowane — w ramach migracji. Może to być przydatne szczególnie w przypadku scenariuszy stanowych.

    Po dostarczeniu bieżącego komunikatu połączenie z klientem zostanie natychmiast zmigrowane, co oznacza, że następny komunikat zostanie rozesłany do nowego serwera.

4. **Zatrzymaj połączenia serwera**

    Po zamknięciu/przeprowadzeniu migracji wszystkich połączeń klienta lub przekroczeniu limitu czasu (domyślnie 30 s)

    Zestaw SDK serwera sygnałów będzie kontynuował proces zamykania na tym etapie i zamknąć wszystkie połączenia z serwerem.

    Połączenia z klientami będą nadal porzucane, jeśli nie można jej zamknąć/zmigrować. Na przykład żaden odpowiedni serwer docelowy/bieżący komunikat klient-serwer nie został ukończony.

## <a name="sample-codes"></a>Kody przykładowe.

Dodaj następujące opcje, gdy `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>Skonfiguruj `OnConnected` i `OnDisconnected` podczas ustawiania trybu bezpiecznego zamykania do programu `MigrateClients` .

Wprowadziliśmy "IConnectionMigrationFeature", aby wskazać, czy połączenie zostało zmigrowane/wychodzące.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```