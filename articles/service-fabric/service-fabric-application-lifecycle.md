---
title: Cykl życia aplikacji w Service Fabric
description: Opisuje tworzenie, wdrażanie, testowanie, uaktualnianie, konserwowanie i usuwanie Service Fabric aplikacji.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: ae0c79cdaafc8fc016d463a01046f0a02121330a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785739"
---
# <a name="service-fabric-application-lifecycle"></a>Service Fabric application lifecycle (Cykl życia aplikacji usługi Service Fabric)
Podobnie jak w przypadku innych platform, aplikacja na platformie Azure Service Fabric zwykle przechodzi przez następujące fazy: projektowanie, programowanie, testowanie, wdrażanie, uaktualnianie, konserwacja i usuwanie. Service Fabric zapewnia obsługę pierwszej klasy dla pełnego cyklu życia aplikacji w chmurze, od projektowania przez wdrażanie, codzienne zarządzanie i konserwację do ostatecznego wycofania. Model usług umożliwia wielu różnym rolom uczestnictwo niezależnie w cyklu życia aplikacji. Ten artykuł zawiera omówienie interfejsów API i ich użycia przez różne role w fazie cyklu życia Service Fabric aplikacji.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Role modelu usług
Role modelu usługi:

* **Deweloper usługi**: opracowuje modularne i ogólne usługi, które mogą być przeznaczenie i używane w wielu aplikacjach tego samego typu lub różnych typów. Na przykład usługa kolejki może służyć do tworzenia aplikacji biletów (pomocy technicznej) lub aplikacji handlu elektronicznego (koszyk).
* **Deweloper aplikacji**: tworzy aplikacje przez integrację kolekcji usług w celu spełnienia określonych wymagań lub scenariuszy. Na przykład witryna handlu elektronicznego może zintegrować usługę "bezstanowe JSON Front-End," "usługa stanowa aukcji" i "usługa stanowa kolejki" w celu utworzenia rozwiązania z licytacją.
* **Administrator aplikacji**: podejmuje decyzje dotyczące konfiguracji aplikacji (wypełniając parametry szablonu konfiguracji), wdrożenie (mapowanie do dostępnych zasobów) i jakość usługi. Na przykład administrator aplikacji decyduje ustawienia regionalne języka (w języku angielskim dla Stany Zjednoczone lub japońskiego dla Japonii, na przykład) aplikacji. Inna wdrożona aplikacja może mieć inne ustawienia.
* **Operator**: wdraża aplikacje na podstawie konfiguracji aplikacji i wymagań określonych przez administratora aplikacji. Na przykład operator inicjuje i wdraża aplikację i zapewnia jej działanie na platformie Azure. Operatory monitorują informacje o kondycji i wydajności aplikacji oraz przechowują infrastrukturę fizyczną zgodnie z wymaganiami.

## <a name="develop"></a>Opracowywanie
1. *Deweloper usługi* opracowuje różne typy usług przy użyciu modelu programowania [Reliable Actors](service-fabric-reliable-actors-introduction.md) lub [Reliable Services](service-fabric-reliable-services-introduction.md) .
2. *Deweloper usługi* deklaratywnie opisuje utworzone typy usług w pliku manifestu usługi składający się z co najmniej jednego kodu, konfiguracji i pakietów danych.
3. *Deweloper aplikacji* kompiluje aplikację przy użyciu różnych typów usług.
4. *Deweloper aplikacji* deklaratywnie opisuje typ aplikacji w manifeście aplikacji, odwołując się do manifestów usług składowych i odpowiednio zastępując i parametryzacja różne ustawienia konfiguracji i wdrażania usług składowych.

Zobacz Wprowadzenie do [Reliable Actors](service-fabric-reliable-actors-get-started.md) i [wprowadzenie do Reliable Services](service-fabric-reliable-services-quick-start.md) na potrzeby przykładów.

## <a name="deploy"></a>Wdróż
1. Autorzy *aplikacji* , który ma zostać wdrożony dla konkretnej aplikacji do wdrożenia w klastrze Service Fabric przez określenie odpowiednich parametrów elementu **ApplicationType** w manifeście aplikacji.
2. *Operator* przekazuje pakiet aplikacji do magazynu obrazów klastra przy użyciu [metody **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [polecenia cmdlet **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Pakiet aplikacji zawiera manifest aplikacji i kolekcję pakietów usług. Service Fabric wdraża aplikacje z pakietu aplikacji przechowywanego w magazynie obrazów, który może być magazynem obiektów blob platformy Azure lub usługą systemową Service Fabric.
3. Następnie *operator* Inicjuje obsługę typu aplikacji w klastrze docelowym z przekazanego pakietu aplikacji przy użyciu [metody **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **register-ServiceFabricApplicationType**](/powershell/module/servicefabric/register-servicefabricapplicationtype)i [operacji REST **aplikacji**](/rest/api/servicefabric/provision-an-application).
4. Po zainicjowaniu obsługi aplikacji *operator* uruchamia aplikację za pomocą parametrów dostarczonych przez *administratora aplikacji* przy użyciu [metody **CreateApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **New-ServiceFabricApplication**](/powershell/module/servicefabric/new-servicefabricapplication)lub [operacji REST **tworzenia aplikacji**](/rest/api/servicefabric/create-an-application).
5. Po wdrożeniu aplikacji *operator* używa [metody **CreateServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [polecenia cmdlet **New-ServiceFabricService**](/powershell/module/servicefabric/new-servicefabricservice)lub [operacji REST **usługi Create Service**](/rest/api/servicefabric/create-a-service) , aby utworzyć nowe wystąpienia usługi dla aplikacji na podstawie dostępnych typów usług.
6. Aplikacja działa teraz w klastrze Service Fabric.

Zobacz temat [wdrażanie aplikacji](service-fabric-deploy-remove-applications.md) na przykład.

## <a name="test"></a>Testowanie
1. Po wdrożeniu w lokalnym klastrze projektowym lub w klastrze testowym *Deweloper usługi* uruchamia wbudowany Scenariusz testowania trybu failover przy użyciu klas [**FailoverTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) i [**FailoverTestScenario**](/dotnet/api/system.fabric.testability.scenario.failovertestscenario) lub [polecenia cmdlet **Invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario). Scenariusz testowania trybu failover uruchamia określoną usługę za pomocą ważnych przejść i trybu failover, aby upewnić się, że jest ona nadal dostępna i działa.
2. *Deweloper usługi* następnie uruchamia wbudowany Scenariusz testowania chaos przy użyciu klas [**ChaosTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) i [**ChaosTestScenario**](/dotnet/api/system.fabric.testability.scenario.chaostestscenario) lub [polecenia cmdlet **Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario). Scenariusz testu chaos losowo wywołuje wiele błędów węzła, pakietu kodu i repliki w klastrze.
3. *Deweloper usług* [testuje komunikację między](service-fabric-testability-scenarios-service-communication.md) usługą, tworząc scenariusze testów, które przenoszą repliki podstawowe wokół klastra.

Aby uzyskać więcej informacji [, zobacz Wprowadzenie do usługi błędów analizy](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Uaktualnienie
1. *Deweloper usługi* aktualizuje usługi składowe wystąpienia aplikacji i/lub naprawia błędy i udostępnia nową wersję manifestu usługi.
2. *Deweloper aplikacji* zastępuje i parameterizes konfigurację oraz ustawienia wdrożenia spójnych usług i udostępnia nową wersję manifestu aplikacji. Deweloper aplikacji wprowadza nowe wersje manifestów usługi do aplikacji i udostępnia nową wersję typu aplikacji w zaktualizowanym pakiecie aplikacji.
3. *Administrator aplikacji* obejmuje nową wersję typu aplikacji w aplikacji docelowej przez zaktualizowanie odpowiednich parametrów.
4. *Operator* przekazuje zaktualizowany pakiet aplikacji do magazynu obrazów klastra przy użyciu [metody **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [polecenia cmdlet **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Pakiet aplikacji zawiera manifest aplikacji i kolekcję pakietów usług.
5. *Operator* Inicjuje nową wersję aplikacji w klastrze docelowym przy użyciu [metody **ProvisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **register-ServiceFabricApplicationType**](/powershell/module/servicefabric/register-servicefabricapplicationtype)lub w ramach [operacji REST **aplikacji**](/rest/api/servicefabric/provision-an-application).
6. *Operator* uaktualnia aplikację docelową do nowej wersji przy użyciu metody [ **UpgradeApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Start-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/start-servicefabricapplicationupgrade)lub [operacji REST **uaktualniania aplikacji**](/rest/api/servicefabric/upgrade-an-application).
7. *Operator* sprawdza postęp uaktualniania przy użyciu [metody **GetApplicationUpgradeProgressAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Get-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/get-servicefabricapplicationupgrade)lub [operacji REST **uaktualniania aplikacji**](/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. W razie potrzeby *operator* modyfikuje i ponownie stosuje parametry bieżącego uaktualnienia aplikacji przy użyciu [metody **UpdateApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Update-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)lub [operacji **aktualizacji REST uaktualniania aplikacji**](/rest/api/servicefabric/update-an-application-upgrade).
9. W razie potrzeby *operator* wycofuje bieżące uaktualnienie aplikacji, korzystając z metody [ **RollbackApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Start-ServiceFabricApplicationRollback**](/powershell/module/servicefabric/start-servicefabricapplicationrollback)lub [operacji **wycofywania uaktualniania aplikacji**](/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric uaktualnia aplikację docelową działającą w klastrze bez utraty dostępności żadnej z jej usług składowych.

Przykłady można znaleźć w [samouczku dotyczącym uaktualniania aplikacji](service-fabric-application-upgrade-tutorial.md) .

## <a name="maintain"></a>Obsługa
1. W przypadku uaktualnień i poprawek systemu operacyjnego Service Fabric interfejsów z infrastrukturą platformy Azure w celu zagwarantowania dostępności wszystkich aplikacji uruchomionych w klastrze.
2. W przypadku uaktualnień i poprawek na platformie Service Fabric Service Fabric uaktualniane bez utraty dostępności żadnej aplikacji uruchomionej w klastrze.
3. *Administrator aplikacji* zatwierdza Dodawanie lub usuwanie węzłów z klastra po analizie historycznych danych użycia pojemności i prognozowanie przyszłych zapotrzebowań.
4. *Operator* dodaje i usuwa węzły określone przez *administratora aplikacji*.
5. Gdy nowe węzły są dodawane do lub istniejące węzły są usuwane z klastra, Service Fabric automatycznie równoważyć obciążenie uruchomionych aplikacji we wszystkich węzłach w klastrze, aby osiągnąć optymalną wydajność.

## <a name="remove"></a>Usuń
1. *Operator* może usunąć określone wystąpienie uruchomionej usługi w klastrze bez usuwania całej aplikacji przy użyciu [metody **DeleteServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [polecenia cmdlet **Remove-ServiceFabricService**](/powershell/module/servicefabric/remove-servicefabricservice)ani [operacji **usuwania REST usługi**](/rest/api/servicefabric/delete-a-service).  
2. *Operator* może również usunąć wystąpienie aplikacji i wszystkie jego usługi przy użyciu [metody **DeleteApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Remove-ServiceFabricApplication**](/powershell/module/servicefabric/remove-servicefabricapplication)lub [operacji **usuwania REST aplikacji**](/rest/api/servicefabric/delete-an-application).
3. Po zatrzymaniu aplikacji i usług *operator* może anulować obsługę administracyjną typu aplikacji przy użyciu [metody **UnprovisionApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [polecenia cmdlet **Unregister-ServiceFabricApplicationType**](/powershell/module/servicefabric/unregister-servicefabricapplicationtype)lub cofnięcia aprowizacji [ **aplikacji** REST](/rest/api/servicefabric/unprovision-an-application). Cofnięcie aprowizacji typu aplikacji nie powoduje usunięcia pakietu aplikacji z magazynu ImageStore. Pakiet aplikacji należy usunąć ręcznie.
4. *Operator* usuwa pakiet aplikacji z magazynu ImageStore za pomocą [metody **RemoveApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) lub [polecenia cmdlet **Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage).

Zobacz temat [wdrażanie aplikacji](service-fabric-deploy-remove-applications.md) na przykład.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat opracowywania i testowania Service Fabric aplikacji i usług oraz zarządzania nimi, zobacz:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Uaktualnienie aplikacji](service-fabric-application-upgrade.md)
* [Przegląd testowania](service-fabric-testability-overview.md)
