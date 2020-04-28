---
title: Rozwiązywanie problemów z lokalną konfiguracją klastra Service Fabric platformy Azure
description: W tym artykule opisano zestaw sugestii dotyczących rozwiązywania problemów z lokalnym klastrem programistycznym
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: ea313adb43f8d91ec9e57dd1d0b8d3447a8075f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75465505"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Troubleshoot your local development cluster setup (Rozwiązywanie problemów z lokalną konfiguracją klastra programowania)
Jeśli wystąpi problem podczas pracy z lokalnym klastrem programistycznym platformy Azure Service Fabric, zapoznaj się z poniższymi sugestiami dotyczącymi potencjalnych rozwiązań.

## <a name="cluster-setup-failures"></a>Błędy konfiguracji klastra
### <a name="cannot-clean-up-service-fabric-logs"></a>Nie można oczyścić dzienników Service Fabric
#### <a name="problem"></a>Problem
Podczas uruchamiania skryptu DevClusterSetup pojawia się następujący błąd:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Rozwiązanie
Zamknij bieżące okno programu PowerShell i Otwórz nowe okno programu PowerShell jako administrator. Teraz można pomyślnie uruchomić skrypt.

## <a name="cluster-connection-failures"></a>Błędy połączenia klastra

### <a name="type-initialization-exception"></a>Wyjątek inicjalizacji typu
#### <a name="problem"></a>Problem
W przypadku nawiązywania połączenia z klastrem w programie PowerShell zostanie wyświetlony komunikat o błędzie TypeInitializationException dla elementu System. Fabric. Common. AppTrace.

#### <a name="solution"></a>Rozwiązanie
Zmienna Path nie została poprawnie ustawiona podczas instalacji. Wyloguj się z systemu Windows i zaloguj się ponownie. Spowoduje to odświeżenie ścieżki.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Połączenie z klastrem kończy się niepowodzeniem z opcją "obiekt jest zamknięty"
#### <a name="problem"></a>Problem
Wywołanie metody Connect-ServiceFabricCluster kończy się niepowodzeniem z powodu błędu:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Rozwiązanie
Zamknij bieżące okno programu PowerShell i Otwórz nowe okno programu PowerShell jako administrator.

### <a name="fabric-connection-denied-exception"></a>Wyjątek odmowy połączenia sieci szkieletowej
#### <a name="problem"></a>Problem
Podczas debugowania z programu Visual Studio pojawia się błąd FabricConnectionDeniedException.

#### <a name="solution"></a>Rozwiązanie
Ten błąd występuje zwykle podczas próby ręcznego uruchomienia procesu hosta usługi.

Upewnij się, że nie masz żadnych projektów usług ustawionych jako projekty startowe w rozwiązaniu. Jako projekty startowe powinny być ustawiane tylko Service Fabric projekty aplikacji.

> [!TIP]
> Jeśli po instalacji klaster lokalny zaczyna działać nieprawidłowo, można zresetować go przy użyciu aplikacji lokalnego paska zadań Menedżera klastra. Spowoduje to usunięcie istniejącego klastra i skonfigurowanie nowego. Zwróć uwagę, że wszystkie wdrożone aplikacje i skojarzone dane są usuwane.
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Informacje o rozwiązywaniu problemów z klastrem przy użyciu raportów kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

