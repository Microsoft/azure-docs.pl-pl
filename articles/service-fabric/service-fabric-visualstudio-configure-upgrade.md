---
title: Konfigurowanie uaktualnienia aplikacji Service Fabric
description: Dowiedz się, jak skonfigurować ustawienia uaktualniania aplikacji Service Fabric przy użyciu Microsoft Visual Studio.
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 11df0387db07669ebacdba2e3d850e32903da133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573684"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurowanie uaktualnienia aplikacji Service Fabric w programie Visual Studio
Narzędzia Visual Studio Tools for Azure Service Fabric zapewniają obsługę uaktualnienia w przypadku klastrów lokalnych lub zdalnych. Istnieją trzy scenariusze, w których aplikacja ma zostać uaktualniona do nowszej wersji zamiast zastępowania aplikacji podczas testowania i debugowania:

* Podczas uaktualniania nie zostaną utracone dane aplikacji.
* Dostępność pozostaje wysoka, dlatego podczas uaktualniania nie będzie żadnych przerw w działaniu usługi, jeśli jest wystarczająca liczba wystąpień usługi rozmieszczonych w różnych domenach uaktualnienia.
* Testy można uruchamiać względem aplikacji podczas jej uaktualniania.

## <a name="parameters-needed-to-upgrade"></a>Parametry wymagające uaktualnienia
Można wybrać jedną z dwóch typów wdrożenia: Regular lub upgrade. Regularne wdrożenie powoduje wymazanie wcześniejszych informacji o wdrożeniu i danych w klastrze, podczas gdy wdrożenie uaktualnienia zachowuje się. W przypadku uaktualniania aplikacji Service Fabric w programie Visual Studio należy podać parametry uaktualnienia aplikacji i zasady sprawdzania kondycji. Parametry uaktualniania aplikacji ułatwiają kontrolę nad uaktualnieniem, podczas gdy zasady sprawdzania kondycji określają, czy uaktualnienie zakończyło się pomyślnie. Aby uzyskać więcej informacji, zobacz [Service Fabric uaktualniania aplikacji: parametry uaktualnienia](service-fabric-application-upgrade-parameters.md) .

Istnieją trzy tryby uaktualniania: *monitorowane*, *UnmonitoredAuto* i *UnmonitoredManual*.

* Monitorowane uaktualnienie automatyzuje Sprawdzanie kondycji uaktualnienia i aplikacji.
* Uaktualnienie UnmonitoredAuto automatyzuje uaktualnienie, ale pomija kontrolę kondycji aplikacji.
* Po uaktualnieniu UnmonitoredManual należy ręcznie uaktualnić każdą domenę uaktualnienia.

Każdy tryb uaktualniania wymaga różnych zestawów parametrów. Zobacz [Parametry uaktualnienia aplikacji](service-fabric-application-upgrade-parameters.md) , aby dowiedzieć się więcej o dostępnych opcjach uaktualniania.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Uaktualnianie aplikacji Service Fabric w programie Visual Studio
Jeśli używasz narzędzi Service Fabric Visual Studio do uaktualnienia aplikacji Service Fabric, możesz określić proces publikowania jako uaktualnienie, a nie regularne wdrożenie, zaznaczając pole wyboru **Uaktualnij aplikację** .

### <a name="to-configure-the-upgrade-parameters"></a>Aby skonfigurować parametry uaktualnienia
1. Kliknij przycisk **Ustawienia** obok pola wyboru. Zostanie wyświetlone okno dialogowe **edytowanie parametrów uaktualnienia** . Okno dialogowe **edytowanie parametrów uaktualnienia** obsługuje tryby uaktualniania monitorowane, UnmonitoredAuto i UnmonitoredManual.
2. Wybierz tryb uaktualniania, którego chcesz użyć, a następnie Wypełnij siatkę parametrów.

    Każdy parametr ma wartości domyślne. Opcjonalny parametr *DefaultServiceTypeHealthPolicy* pobiera dane wejściowe tabeli skrótów. Oto przykład formatu danych wejściowych tabeli skrótów dla *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* jest kolejnym opcjonalnym parametrem, który pobiera dane wejściowe tabeli skrótów w następującym formacie:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Oto przykład w czasie rzeczywistym:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. W przypadku wybrania trybu uaktualnienia UnmonitoredManual należy ręcznie uruchomić konsolę programu PowerShell, aby kontynuować i zakończyć proces uaktualniania. Zapoznaj się z tematem [Service Fabric uaktualniania aplikacji: Zaawansowane tematy](service-fabric-application-upgrade-advanced.md) , aby dowiedzieć się, jak działa uaktualnienie ręczne.

## <a name="upgrade-an-application-by-using-powershell"></a>Uaktualnianie aplikacji przy użyciu programu PowerShell
Za pomocą poleceń cmdlet programu PowerShell można uaktualnić aplikację Service Fabric. Aby uzyskać szczegółowe informacje, zobacz [Samouczek dotyczący uaktualniania aplikacji Service Fabric](service-fabric-application-upgrade-tutorial.md) i [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Określ zasady sprawdzania kondycji w pliku manifestu aplikacji
Każda usługa w aplikacji Service Fabric może mieć własne parametry zasad dotyczących kondycji, które zastępują wartości domyślne. Można podać te wartości parametrów w pliku manifestu aplikacji.

Poniższy przykład pokazuje, jak zastosować unikatowe zasady sprawdzania kondycji dla każdej usługi w manifeście aplikacji.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uaktualniania aplikacji, zobacz [Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md).
