---
title: 'Testowanie: komunikacja z usługą'
description: Komunikacja między usługami jest krytycznym punktem integracji aplikacji Service Fabric. W tym artykule omówiono zagadnienia dotyczące projektowania i techniki testowania.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575928"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric scenariusze testowania: komunikacja z usługą
Mikrousługi i zorientowane na usługi urządzenia w sposób naturalny na platformie Azure Service Fabric. W tych typach architektur rozproszonych aplikacje mikrousług są zwykle składają się z wielu usług, które muszą komunikować się ze sobą. Nawet w najprostszym przypadku zwykle istnieje co najmniej bezstanowa usługa sieci Web i usługa magazynu danych stanowych, która musi komunikować się.

Komunikacja między usługami jest podstawowym punktem integracji aplikacji, ponieważ każda usługa uwidacznia Zdalny interfejs API do innych usług. Praca z zestawem granic interfejsu API, które obejmują wykonywanie operacji we/wy, zazwyczaj wymaga pewnej staranności, z dobrą ilością testów i weryfikacji.

Istnieją liczne kwestie, które należy wziąć pod uwagę w przypadku przewodowego łączenia tych usług w systemie rozproszonym:

* *Protokół transportowy*. Czy używasz protokołu HTTP w celu zwiększenia współdziałania lub niestandardowego protokołu binarnego w celu zapewnienia maksymalnej przepływności?
* *Obsługa błędów*. Jak będą obsługiwane błędy trwałe i przejściowe? Co się stanie, gdy usługa zostanie przeniesiona do innego węzła?
* *Limity czasu i czas oczekiwania*. W jaki sposób każda warstwa usług w aplikacjach wielowarstwowych będzie obsługiwać opóźnienia przez stos i do użytkownika?

Bez względu na to, czy używasz jednego z wbudowanych składników komunikacji usług udostępnianych przez Service Fabric lub tworzysz własne, testowanie interakcji między usługami ma kluczowe znaczenie dla zapewnienia odporności aplikacji.

## <a name="prepare-for-services-to-move"></a>Przygotowywanie do przechodzenia do usług
Wystąpienia usługi mogą poruszać się w czasie. Jest to szczególnie ważne, gdy są skonfigurowane przy użyciu metryk obciążenia dla optymalnego równoważenia zasobów dopasowanego do niestandardowego. Service Fabric przenosi wystąpienia usługi, aby zmaksymalizować ich dostępność nawet podczas uaktualniania, przełączania do trybu failover, skalowania w poziomie i innych sytuacji, w których występuje w okresie istnienia systemu rozproszonego.

Gdy usługi są przenoszone w klastrze, klienci i inne usługi powinny być przygotowane do obsługi dwóch scenariuszy podczas komunikowania się z usługą:

* Wystąpienie usługi lub replika partycji zostały przeniesione od czasu ostatniego przeprowadzenia rozmowy. Jest to normalna część cyklu życia usługi, która powinna wystąpić w okresie istnienia aplikacji.
* Trwa przechodzenie wystąpienia usługi lub repliki partycji. Mimo że przełączanie w tryb failover usługi z jednego węzła do drugiego odbywa się bardzo szybko w Service Fabric, może wystąpić opóźnienie dostępności, jeśli składnik komunikacyjny usługi jest wolny od początku.

Obsługa tych scenariuszy jest niezwykle ważna dla sprawnego systemu. W tym celu należy pamiętać, że:

* Każda usługa, która może być połączona, ma *adres* , na którym nasłuchuje (na przykład http lub WebSockets). Gdy wystąpienie usługi lub partycja zostanie przeniesiona, jego punkt końcowy adresu zmieni się. (Przechodzi do innego węzła przy użyciu innego adresu IP). Jeśli używasz wbudowanych składników komunikacji, będą one obsługiwać ponowne rozpoznawanie adresów usługi.
* Może wystąpić tymczasowy wzrost opóźnienia usługi, ponieważ wystąpienie usługi ponownie uruchomi swój odbiornik. Jest to zależne od tego, jak szybko usługa otwiera odbiornik po przeniesieniu wystąpienia usługi.
* Wszystkie istniejące połączenia należy zamknąć i ponownie otworzyć po otwarciu usługi w nowym węźle. Bezpieczne zamknięcie lub ponowne uruchomienie węzła pozwala na bezpieczne wyłączenie istniejących połączeń.

### <a name="test-it-move-service-instances"></a>Przetestuj: Przenieś wystąpienia usługi
Za pomocą narzędzi do testowania Service Fabric, można utworzyć scenariusz testu, aby przetestować te sytuacje na różne sposoby:

1. Przenieś replikę podstawową usługi stanowej.
   
    Replikę podstawową partycji usługi stanowej można przenieść z dowolnej liczby przyczyn. Użyj tego elementu, aby określić podstawową replikę konkretnej partycji, aby zobaczyć, w jaki sposób usługi reagują na przenoszenie w bardzo kontrolowany sposób.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zatrzymaj węzeł.
   
    Gdy węzeł zostanie zatrzymany, Service Fabric przenosi wszystkie wystąpienia usługi lub partycje, które znajdowały się w tym węźle, do jednego z innych dostępnych węzłów w klastrze. Służy do testowania sytuacji, w której węzeł zostanie utracony z klastra, a wszystkie wystąpienia usługi i repliki w tym węźle muszą zostać przeniesione.
   
    Węzeł można zatrzymać za pomocą polecenia cmdlet **stop-ServiceFabricNode** programu PowerShell:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Obsługa dostępności usługi
Jako Platforma Service Fabric zaprojektowano w celu zapewnienia wysokiej dostępności usług. Jednak w skrajnych przypadkach problemy związane z infrastrukturą mogą nadal spowodować niedostępność. Ważne jest, aby przetestować te scenariusze.

Usługi stanowe używają systemu opartego na kworum, aby replikować stan w celu zapewnienia wysokiej dostępności. Oznacza to, że kworum replik musi być dostępne do wykonywania operacji zapisu. W rzadkich przypadkach, na przykład w przypadku rozległego błędu sprzętowego, kworum replik może nie być dostępne. W takich przypadkach nie będzie można wykonywać operacji zapisu, ale nadal będzie można wykonywać operacje odczytu.

### <a name="test-it-write-operation-unavailability"></a>Testowanie: niedostępność operacji zapisu
Korzystając z narzędzi do testowania w Service Fabric, można wstrzyknąć błąd, który wywołuje utratę kworum jako test. Chociaż taki scenariusz jest rzadki, ważne jest, aby klienci i usługi, które są zależne od usługi stanowej są przygotowane do obsługi sytuacji, gdy nie mogą wprowadzać do niej żądań zapisu. Należy również pamiętać, że sama usługa stanowa jest świadoma tej możliwości i może bezpiecznie przekazać ją do wywoływania.

Za pomocą polecenia cmdlet **Invoke-ServiceFabricPartitionQuorumLoss** programu PowerShell można wywołać utratę kworum:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

W tym przykładzie ustawimy, `QuorumLossMode` Aby `QuorumReplicas` wskazać, że chcemy wymusić utratę kworum bez wyłączania wszystkich replik. W ten sposób operacje odczytu są nadal możliwe. W celu przetestowania scenariusza, w którym cała partycja jest niedostępna, można ustawić ten przełącznik `AllReplicas` .

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o akcjach testowania](service-fabric-testability-actions.md)

[Dowiedz się więcej na temat scenariuszy testowania](service-fabric-testability-scenarios.md)

