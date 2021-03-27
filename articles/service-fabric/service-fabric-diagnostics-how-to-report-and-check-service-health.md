---
title: Raportowanie i sprawdzanie kondycji za pomocą usługi Azure Service Fabric
description: Dowiedz się, jak wysyłać raporty dotyczące kondycji z kodu usługi i jak sprawdzać kondycję usługi przy użyciu narzędzi do monitorowania kondycji udostępnianych przez usługę Azure Service Fabric.
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: dc15ea7f062d3912c297050ffac755990fdd3b7a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625946"
---
# <a name="report-and-check-service-health"></a>Tworzenie raportów i sprawdzanie kondycji usług
Gdy usługi napotykają problemy, zdolność do reagowania na zdarzenia i przestoje zależy od możliwości szybkiego wykrywania problemów. W przypadku zgłaszania problemów i błędów do programu Azure Service Fabric Health Manager z kodu usługi można użyć standardowych narzędzi do monitorowania kondycji, które Service Fabric zapewniają, aby sprawdzić stan kondycji.

Istnieją trzy sposoby raportowania kondycji usługi:

* Użyj [partycji](/dotnet/api/system.fabric.istatefulservicepartition) lub obiektów [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) .  
  Za pomocą `Partition` obiektów i można `CodePackageActivationContext` raportować kondycję elementów, które są częścią bieżącego kontekstu. Na przykład kod, który jest uruchamiany jako część repliki, może zgłosić kondycję tylko dla tej repliki, partycji, do której należy, i aplikacji, z którą jest częścią.
* Użyj polecenia `FabricClient`.   
  Programu można użyć `FabricClient` do raportowania kondycji kodu usługi, Jeśli klaster nie jest [zabezpieczony](service-fabric-cluster-security.md) lub jeśli usługa jest uruchomiona z uprawnieniami administratora. Większość scenariuszy rzeczywistych nie korzysta z klastrów niezabezpieczonych lub zapewniają uprawnienia administratora. Za pomocą programu `FabricClient` można raportować kondycję wszystkich jednostek, które są częścią klastra. W idealnym przypadku kod usługi powinien jednak wysyłać tylko raporty powiązane ze swoimi kondycjami.
* Użyj interfejsów API REST na poziomie klastra, aplikacji, wdrożonej aplikacji, usługi, pakietu usług, partycji, repliki lub poziomu węzła. Ta wartość może służyć do raportowania kondycji z kontenera.

W tym artykule przedstawiono przykład, który raportuje kondycję z kodu usługi. W przykładzie pokazano również, jak narzędzia dostarczone przez Service Fabric mogą służyć do sprawdzania stanu kondycji. Ten artykuł ma na celu szybkie wprowadzenie do możliwości monitorowania kondycji Service Fabric. Aby uzyskać bardziej szczegółowe informacje, możesz zapoznać się z serią szczegółowych artykułów dotyczących kondycji, które zaczynają się od linku na końcu tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Musisz mieć zainstalowane następujące elementy:

* Visual Studio 2015 lub Visual Studio 2019
* Zestaw SDK Service Fabric

## <a name="to-create-a-local-secure-dev-cluster"></a>Aby utworzyć lokalny bezpieczny klaster programistyczny
* Otwórz program PowerShell z uprawnieniami administratora i uruchom następujące polecenia:

![Polecenia, które pokazują, jak utworzyć bezpieczny klaster deweloperów](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Aby wdrożyć aplikację i sprawdzić jej kondycję
1. Otwórz program Visual Studio jako administrator.
1. Utwórz projekt przy użyciu szablonu **usługi stanowej** .
   
    ![Tworzenie Service Fabric aplikacji z usługą stanową](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Naciśnij klawisz **F5** , aby uruchomić aplikację w trybie debugowania. Aplikacja jest wdrażana w klastrze lokalnym.
1. Po uruchomieniu aplikacji kliknij prawym przyciskiem myszy ikonę Menedżera klastra lokalnego w obszarze powiadomień i wybierz polecenie **Zarządzaj klastrem lokalnym** z menu skrótów, aby otworzyć Service Fabric Explorer.
   
    ![Otwórz Service Fabric Explorer z obszaru powiadomień](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Kondycja aplikacji powinna być wyświetlana w tym obrazie. W tej chwili aplikacja powinna działać w dobrej kondycji bez błędów.
   
    ![Dobra kondycja w Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Kondycję można także sprawdzić przy użyciu programu PowerShell. Możesz użyć ```Get-ServiceFabricApplicationHealth``` , aby sprawdzić kondycję aplikacji, i można użyć, ```Get-ServiceFabricServiceHealth``` Aby sprawdzić kondycję usługi. Raport kondycji dla tej samej aplikacji w programie PowerShell znajduje się na tym obrazie.
   
    ![Aplikacja w dobrej kondycji w programie PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Aby dodać niestandardowe zdarzenia dotyczące kondycji do kodu usługi
Szablony projektów Service Fabric w programie Visual Studio zawierają przykładowy kod. Poniższe kroki pokazują, jak można raportować niestandardowe zdarzenia dotyczące kondycji z kodu usługi. Takie raporty są automatycznie wyświetlane w standardowych narzędziach do monitorowania kondycji, które zapewnia Service Fabric, takich jak Service Fabric Explorer, Azure Portal Health View i PowerShell.

1. Otwórz ponownie aplikację utworzoną wcześniej w programie Visual Studio lub Utwórz nową aplikację przy użyciu szablonu **usługi stanowej** programu Visual Studio.
1. Otwórz plik Stateful1. cs i Znajdź `myDictionary.TryGetValueAsync` wywołanie w `RunAsync` metodzie. Można zobaczyć, że ta metoda zwraca `result` , który przechowuje bieżącą wartość licznika, ponieważ logika klucza w tej aplikacji ma mieć uruchomioną liczbę. Jeśli ta aplikacja była aplikacją rzeczywistą, a w przypadku braku wyniku nie można oflagować tego zdarzenia.
1. Aby zgłosić zdarzenie kondycji, gdy brak wyniku reprezentuje błąd, należy dodać następujące kroki.
   
    a. Dodaj `System.Fabric.Health` przestrzeń nazw do pliku Stateful1. cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Dodaj następujący kod po `myDictionary.TryGetValueAsync` wywołaniu
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Raport o kondycji repliki jest raportowany przez usługę stanową. `HealthInformation`Parametr przechowuje informacje o raportowanym problemie kondycji.
   
    Jeśli utworzono usługę bezstanową, użyj poniższego kodu
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Jeśli usługa jest uruchomiona z uprawnieniami administratora lub Jeśli klaster nie jest [zabezpieczony](service-fabric-cluster-security.md), możesz również użyć `FabricClient` programu do raportowania kondycji, jak pokazano w poniższych krokach.  
   
    a. Utwórz `FabricClient` wystąpienie po `var myDictionary` deklaracji.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Dodaj następujący kod po `myDictionary.TryGetValueAsync` wywołaniu.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Symulujemy ten błąd i zobaczysz go w narzędziach do monitorowania kondycji. Aby symulować błąd, Skomentuj pierwszy wiersz w kodzie raportowania kondycji, który został dodany wcześniej. Po zapisaniu komentarza w pierwszym wierszu kod będzie wyglądał jak w poniższym przykładzie.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Ten kod uruchamia Raport kondycji przy każdym uruchomieniu `RunAsync` . Po wprowadzeniu zmiany naciśnij klawisz **F5** , aby uruchomić aplikację.
1. Po uruchomieniu aplikacji otwórz Service Fabric Explorer, aby sprawdzić kondycję aplikacji. Tym razem Service Fabric Explorer pokazuje, że aplikacja jest w złej kondycji. Aplikacja jest wyświetlana jako zła kondycja, ponieważ wystąpił błąd zgłoszony przez dodany wcześniej kod.
   
    ![Aplikacja w złej kondycji w Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. W przypadku wybrania repliki podstawowej w widoku drzewa Service Fabric Explorer zostanie wyświetlony **stan kondycji** wskazujący błąd. Service Fabric Explorer również Wyświetla szczegóły raportu kondycji, które zostały dodane do `HealthInformation` parametru w kodzie. Te same raporty kondycji można zobaczyć w programie PowerShell i w Azure Portal.
   
    ![Kondycja repliki w Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ten raport pozostaje w Menedżerze kondycji, dopóki nie zostanie zastąpiony przez inny raport lub dopóki ta replika nie zostanie usunięta. Ponieważ nie została ustawiona `TimeToLive` dla tego raportu kondycji w `HealthInformation` obiekcie, raport nigdy nie wygaśnie.

Zalecamy, aby kondycja była raportowana na najbardziej szczegółowym poziomie, który jest w tym przypadku repliką. Możesz również zgłosić kondycję `Partition` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Aby zgłosić kondycję w systemach `Application` , `DeployedApplication` , i `DeployedServicePackage` , użyj  `CodePackageActivationContext` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Następne kroki
* [Głębokie szczegółowe na Service Fabric kondycji](service-fabric-health-introduction.md)
* [Interfejs API REST dla kondycji usługi raportowania](/rest/api/servicefabric/report-the-health-of-a-service)
* [Interfejs API REST dla kondycji aplikacji raportowania](/rest/api/servicefabric/report-the-health-of-an-application)
