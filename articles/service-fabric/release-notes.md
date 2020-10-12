---
title: Wersje Service Fabric platformy Azure
description: Informacje o wersji dla Service Fabric platformy Azure. Zawiera informacje na temat najnowszych funkcji i ulepszeń w programie Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 467018e57a0783839cb9495fcc6df89aad9daa8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91280613"
---
# <a name="service-fabric-releases"></a>Wersje Service Fabric

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Przewodniki dotyczące rozwiązywania problemów</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Śledzenie problemów</a> 
- <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Opcje pomocy technicznej</a> 
- <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Obsługiwane wersje</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Przykłady kodu</a>

Ten artykuł zawiera więcej informacji o najnowszych wersjach i aktualizacjach środowiska uruchomieniowego Service Fabric i zestawów SDK.

## <a name="whats-new-in-service-fabric"></a>Co nowego w Service Fabric

### <a name="service-fabric-71"></a>Service Fabric 7,1
Ze względu na bieżące kryzysy COVID-19 i biorąc pod uwagę wyzwania dla naszych klientów, firma Microsoft udostępnia 7,1, ale nie uaktualnia automatycznie klastrów ustawionych w taki sposób, aby otrzymywać automatyczne uaktualnienia. Wstrzymywanie automatycznych uaktualnień do momentu zagwarantowania, że klienci będą mogli zastosować uaktualnienia, gdy jest to najbardziej odpowiednie, aby uniknąć nieoczekiwanych przerw w działaniu.

Będzie można zaktualizować do 7,1 za pośrednictwem przez [Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) lub [wdrożenie Azure Resource Manager](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template).

Service Fabric klastrów z włączonymi uaktualnieniami automatycznymi rozpocznie się automatyczne otrzymywanie aktualizacji 7,1 po wznowieniu standardowej procedury wdrażania. Przed rozpoczęciem standardowego wdrożenia w [witrynie społeczności Service Fabric Tech](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Opublikowano również aktualizacje dotyczące końca okresu pomocy technicznej dla głównych wersji, począwszy od 6,5 [do 7,1.](./service-fabric-versions.md#supported-versions) 

## <a name="what-is-new-in-service-fabric-71"></a>Co nowego w Service Fabric 7,1?
Przyjemnością ogłaszamy kolejne wydanie Service Fabric. Ta wersja jest ładowana przy użyciu najważniejszych funkcji i ulepszeń. Poniżej przedstawiono niektóre z kluczowych funkcji:
## <a name="key-announcements"></a>Anonse kluczy
- **Ogólna dostępność** [ **Service Fabric zarządzanych tożsamości dla aplikacji Service Fabric**](./concepts-managed-identity.md)
- [**Obsługa Ubuntu 18,04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Wersja zapoznawcza: obsługa tymczasowych dysków systemu operacyjnego zestawu skalowania maszyn wirtualnych**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: dyski tymczasowe systemu operacyjnego są magazynem utworzonym na lokalnej maszynie wirtualnej i nie zostały zapisane w zdalnej usłudze Azure Storage. Są one zalecane dla wszystkich typów węzłów Service Fabric (podstawowych i pomocniczych), w porównaniu do tradycyjnych stałych dysków systemu operacyjnego, dysków tymczasowych systemu operacyjnego:
      -  Zmniejsz opóźnienie odczytu/zapisu na dysku systemu operacyjnego
      -  Włącz szybsze operacje zarządzania węzłami resetowania/ponownego obrazu
      -  Obniżenie kosztów ogólnych (dyski są bezpłatne i nie wiążą się z dodatkowym kosztem magazynowania)
- Obsługa deklaracji [**certyfikatów punktów końcowych usługi Service Fabric aplikacji według nazwy pospolitej podmiotu**](./service-fabric-service-manifest-resources.md).
- [**Obsługa sond kondycji dla usług kontenerowych**](./probes-codepackage.md): obsługa mechanizmu sondowania na żywo dla aplikacji kontenerowych. Sonda na żywo pomaga w ogłaszaniu na żywo aplikacji kontenera i gdy nie odpowiadają one w odpowiednim czasie, spowoduje to ponowne uruchomienie. 
- [**Obsługa pakietów kodu inicjatora**](./initializer-codepackages.md) dla [kontenerów](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) i aplikacji [wykonywalnych gościa](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) . Umożliwia to wykonywanie pakietów kodu (np. kontenerów) w określonej kolejności, aby przeprowadzić inicjalizację pakietu usług.
- **FabricObserver i ClusterObserver** są aplikacjami bezstanowymi, które przechwytują Service Fabric dane telemetryczne związane z różnymi aspektami klastra SF. Obie te aplikacje są gotowe do wdrożenia w klastrach produkcyjnych systemu Windows w celu przechwytywania bogatej telemetrii z zaimplementowaną obsługą ApplicationInsights, EventSource i LogAnalytics.
    - [**FabricObserver (fo) 2,0**](https://github.com/microsoft/service-fabric-observer)-działa na wszystkich węzłach, generuje zdarzenia dotyczące kondycji, emituje dane telemetryczne po osiągnięciu progów użycia zasobów skonfigurowanych przez użytkownika. Ta wersja zawiera kilka ulepszeń między monitorowaniem, zarządzaniem danymi, szczegółami zdarzeń dotyczących kondycji, strukturalną telemetrię.
     - [**ClusterObserver (co) 1,1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) -działa na jednym węźle, przechwytuje dane telemetryczne kondycji poziomu klastra. W tej wersji ClusterObserver również monitoruje stan węzła i emituje dane telemetryczne, gdy węzeł jest wyłączony/wyłączany/wyłączony przez czas dłuższy niż określony przez użytkownika.

### <a name="improve-application-life-cycle-experience"></a>Ulepsz środowisko cyklu życia aplikacji

- **[Wersja zapoznawcza:](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** w trakcie konserwacji zaplanowanej usługi, takiej jak uaktualnienia usług lub dezaktywacja węzła, chcesz zezwolić usługom na bezpieczne opróżnianie połączeń. Ta funkcja dodaje czas trwania opóźnienia zamknięcia w konfiguracji usługi. Podczas planowanych operacji SF usunie adres usługi z odnajdywania, a następnie poczeka ten czas przed zamknięciem usługi.
- **[Automatyczne wykrywanie i równoważenie klastra](./cluster-resource-manager-subclustering.md)**: klastrowanie występuje, gdy usługi z różnymi ograniczeniami umieszczania mają wspólną [metrykę obciążenia](./service-fabric-cluster-resource-manager-metrics.md). Jeśli obciążenie różnych zestawów węzłów znacznie się różni, klaster Service Fabric Menedżer zasobów uważa, że klaster jest niezrównoważony, nawet jeśli ma najlepsze możliwe saldo z powodu ograniczeń umieszczania. W związku z tym próbuje on ponownie zrównoważyć klaster, potencjalnie powodując niepotrzebne ruchy usługi (ponieważ "niezrównoważone" nie może być znacząco ulepszone). Począwszy od tej wersji, klaster Menedżer zasobów podejmie teraz próbę automatycznego wykrycia tych rodzajów konfiguracji i zrozumienia sytuacji, w których można nawiązać transrównowaga za pomocą przenoszenia, a kiedy zamiast tego należy pozostawać same w sobie, ponieważ nie można wykonać znacznej poprawy.  
- [**Inny koszt przenoszenia dla replik pomocniczych**](./service-fabric-cluster-resource-manager-movement-cost.md): wprowadzono nową VeryHigh kosztu przeniesienia, która zapewnia dodatkową elastyczność w niektórych scenariuszach, aby określić, czy do replik pomocniczych należy używać oddzielnego kosztu przenoszenia.
- Włączony mechanizm [**sondowania na żywo**](./probes-codepackage.md) dla aplikacji kontenerowych. Sonda na żywo pomaga w ogłaszaniu na żywo aplikacji kontenera i gdy nie odpowiadają one w odpowiednim czasie, spowoduje to ponowne uruchomienie.
- [**Uruchom do ukończenia/raz dla usług**](./run-to-completion.md)**

### <a name="image-store-improvements"></a>Udoskonalenia Magazyn obrazów
 - Service Fabric 7,1 używa **niestandardowego transportu do bezpiecznego transferu plików między węzłami**. Zależność od udziału plików SMB jest usuwana z wersji 7,1. Zabezpieczone udziały plików SMB nadal są istniejące w węzłach, które zawierają replikę usługi Magazyn obrazów, aby klient mógł zrezygnować z domyślnych oraz uaktualnić i obniżyć wersję do starej wersji.
       
 ### <a name="reliable-collections-improvements"></a>Udoskonalenia niezawodnych kolekcji

- [**W pamięci tylko magazyn obsługujący usługi stanowe przy użyciu niezawodnych kolekcji**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): niewiarygodne kolekcje nietrwałe umożliwiają utrwalanie danych na dysku w celu zapewnienia trwałości przed awarią w dużej skali. mogą być używane do obciążeń, takich jak replikowana pamięć podręczna, na przykład w przypadku, gdy okazjonalnie utracie danych. W oparciu o [ograniczenia i ograniczenia niezawodnych kolekcji nietrwałej](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)zalecamy to dla obciążeń, które nie wymagają trwałości, dla usług, które obsługują rzadko występujące straty kworum.
- [**Wersja zapoznawcza: Service Fabric Eksplorator kopii zapasowych**](https://github.com/microsoft/service-fabric-backup-explorer): aby ułatwić zarządzanie niezawodnymi kopiami zapasowymi kolekcji dla Service Fabric aplikacji stanowych, Service Fabric Eksplorator kopii zapasowych umożliwia użytkownikom
    - Inspekcja i przeglądanie zawartości niezawodnych kolekcji,
    - Aktualizuj bieżący stan do spójnego widoku
    - Utwórz kopię zapasową bieżącej migawki niezawodnych kolekcji
    - Napraw uszkodzenie danych
                 
### <a name="service-fabric-71-releases"></a>Wersje 7,1 Service Fabric
| Data wydania | Release | Więcej informacji |
|---|---|---|
| 20 kwietnia 2020 | [Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 czerwca 2020 | [Microsoft Azure Service Fabric 7,1, pierwsze odświeżanie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 lipca 2020 | [Microsoft Azure Service Fabric 7,1 drugiego odświeżenia](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 sierpnia 2020 | [Microsoft Azure Service Fabric 7,1 trzecie odświeżanie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 września 2020 | [Microsoft Azure Service Fabric 7,1 czwarte odświeżanie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|

### <a name="service-fabric-70"></a>Service Fabric 7,0

Usługa Azure Service Fabric 7,0 jest teraz dostępna! Będzie można zaktualizować do 7,0 za pośrednictwem Azure Portal lub wdrożenia Azure Resource Manager. Ze względu na Opinie klientów dotyczące wydań w okresie świątecznym nie rozpocznie się automatyczne aktualizowanie klastrów ustawionych w taki sposób, aby otrzymywać automatyczne uaktualnienia do stycznia.
W styczniu wznowimy standardowe procedury i klastry z włączonymi uaktualnieniami automatycznymi umożliwią automatyczne otrzymywanie aktualizacji 7,0. Przed rozpoczęciem wycofywania zajmiemy się innym ogłoszeniem.
Będziemy również aktualizować nasze planowane daty wydania, aby wskazać, że te zasady są uwzględniane. Zobacz tutaj, aby uzyskać aktualizacje dotyczące naszych przyszłych [harmonogramów wydania](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Jest to Najnowsza wersja Service Fabric i została załadowana przy użyciu najważniejszych funkcji i ulepszeń.

### <a name="key-announcements"></a>Anonse kluczy
 - [**Obsługa KeyVaultReference dla wpisów tajnych aplikacji (wersja zapoznawcza)**](./service-fabric-keyvault-references.md): Service Fabric aplikacje, które obsługują [tożsamości zarządzane](./concepts-managed-identity.md) , mogą teraz bezpośrednio odwoływać się do Key Vault tajnego adresu URL jako zmiennej środowiskowej, parametru aplikacji lub poświadczenia repozytorium kontenerów. Service Fabric automatycznie rozwiąże wpis tajny przy użyciu tożsamości zarządzanej aplikacji. 
     
- **Ulepszone zabezpieczenia uaktualnienia dla usług bezstanowych**: w celu zagwarantowania dostępności podczas uaktualniania aplikacji wprowadzono nowe konfiguracje umożliwiające zdefiniowanie [minimalnej liczby wystąpień usług bezstanowych](/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) , które mają być uznawane za dostępne. Wcześniej ta wartość była równa 1 dla wszystkich usług i nie mogła zostać zmieniona. Korzystając z tej nowej kontroli bezpieczeństwa dla poszczególnych usług, można zagwarantować, że usługi zachowują minimalną liczbę wystąpień podczas uaktualniania aplikacji, uaktualniania klastra i innych czynności konserwacyjnych, które opierają się na sprawdzaniu kondycji Service Fabric i bezpieczeństwa.
  
- [**Limity zasobów dla usług użytkowników**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): użytkownicy mogą konfigurować limity zasobów dla usług użytkowników w węźle, aby zapobiec scenariuszom, takim jak wyczerpanie zasobów Service Fabric usług systemu. 
  
- [**Bardzo wysoki koszt przeniesienia usługi**](./service-fabric-cluster-resource-manager-movement-cost.md) dla typu repliki. Repliki o bardzo wysokim koszcie są przenoszone tylko w przypadku naruszenia ograniczenia w klastrze, którego nie można naprawić w żaden inny sposób. Zapoznaj się z dokumentem połączonym, aby uzyskać dodatkowe informacje na temat sytuacji, w których użycie "bardzo wysokie" kosztu przeniesienia jest rozsądne i dodatkowe zagadnienia.
  
-  **Dodatkowe kontrole bezpieczeństwa klastra**: w tej wersji wprowadziliśmy sprawdzanie bezpieczeństwa kworum węzła inicjatora. Pozwala to na dostosowanie liczby węzłów inicjatora, które muszą być dostępne podczas scenariuszy związanych z cyklem życia klastra i zarządzaniem nimi. Operacje przyjmujące klaster poniżej skonfigurowanej wartości są blokowane. Dzisiaj wartość domyślna to zawsze kworum węzłów inicjatora, na przykład jeśli masz 7 węzłów inicjatora, operacja, która mogłaby potrwać poniżej 5 węzłów inicjatora, będzie domyślnie blokowana. W przypadku tej zmiany można wprowadzić minimalną bezpieczną wartość 6, która pozwoli na przekroczenie tylko jednego węzła inicjatora.
   
- Dodano obsługę [**zarządzania kopiami zapasowymi i przywracaniem w programie Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Powoduje to, że następujące działania mogą być możliwe bezpośrednio z poziomu SFX: odnajdywanie usługi tworzenia kopii zapasowych i przywracania, tworzenie zasad tworzenia kopii zapasowych, Włączanie automatycznych kopii zapasowych, wykonywanie kopii zapasowych ad hoc, wyzwalanie operacji przywracania i przeglądanie istniejących kopii zapasowych.

- Zapowiedź dostępności [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): to narzędzie pomaga sprawdzić, czy typy używane w niezawodnych kolekcjach są zgodne z poprzednimi wersjami podczas stopniowego uaktualniania aplikacji. Pozwala to zapobiec błędom uaktualniania lub utracie danych i uszkodzeniu danych ze względu na brak lub niezgodne typy.

- [**Włącz stałe odczyty w replikach pomocniczych**](./service-fabric-reliable-services-configuration.md#configuration-names-1): stabilne odczyty ograniczają repliki pomocnicze, które będą zwracać wartości, które były potwierdzone kworum.

Ponadto ta wersja zawiera inne nowe funkcje, poprawki błędów i usprawnienia, niezawodność i wydajność. Aby uzyskać pełną listę zmian, zapoznaj się z [informacjami o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Wersje 7,0 Service Fabric

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 18 listopada 2019 r. | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 stycznia 2020 | [Wersja odświeżenia systemu Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 lutego 2020 | [Wersja odświeżenia systemu Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 marca 2020 | [Wersja odświeżenia systemu Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Service Fabric 6,5

Ta wersja zawiera ulepszenia dotyczące możliwości obsługi, niezawodności i wydajności, nowe funkcje, poprawki błędów i ulepszenia ułatwiające zarządzanie klastrami i aplikacjami.

> [!IMPORTANT]
> Service Fabric 6,5 to ostateczna wersja z obsługą Service Fabric narzędzi w programie Visual Studio 2015. Klienci są zachęcani do przechodzenia do [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

Oto nowości Nowość w Service Fabric 6,5:

- Service Fabric Explorer zawiera [podgląd magazyn obrazów](service-fabric-visualizing-your-cluster.md#image-store-viewer) do inspekcji aplikacji przekazanych do magazynu obrazów.

- Wersja [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) [aplikacji aranżacji patch (POA)](service-fabric-patch-orchestration-application.md) zawiera wiele ulepszeń samoobsługowych. Aby przejść do tej wersji, zalecamy użycie klientów POA.

- [Usługa EventStore jest domyślnie włączona](service-fabric-visualizing-your-cluster.md#event-store) dla klastrów Service Fabric 6,5, chyba że wybrałeś opcję.

- Dodano [zdarzenia cyklu życia repliki](service-fabric-diagnostics-event-generation-operational.md#replica-events) dla usług stanowych.

- [Lepsza widoczność stanu węzła inicjatora](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), w tym ostrzeżeń na poziomie klastra, jeśli węzeł inicjatora jest w złej kondycji (*w dół*, *usunięty* lub *nieznany*).

- [Service Fabric narzędzie do odzyskiwania po awarii aplikacji](https://github.com/Microsoft/Service-Fabric-AppDRTool) umożliwia Service Fabric usługi stanowe, aby szybko wykonać odzyskiwanie w przypadku wystąpienia awarii w klastrze podstawowym. Dane z klastra podstawowego są stale synchronizowane z pomocniczą aplikacją do rezerwowania przy użyciu okresowych kopii zapasowych i przywracania.

- Program Visual Studio obsługuje [publikowanie aplikacji platformy .NET Core w klastrach opartych na systemie Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Interfejs wiersza polecenia platformy Azure Service Fabric (SFCTL)](./service-fabric-cli.md) zostanie automatycznie zainstalowany dla Service Fabric 6,5 (i nowszych) w przypadku uaktualniania lub tworzenia nowego klastra z systemem Linux na platformie Azure.

- [SFCTL](./service-fabric-cli.md) jest instalowany domyślnie w klastrach jednopunktowy MacOS/Linux.

Aby uzyskać więcej informacji, zobacz [Informacje o wersji Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Wersje 6,5 Service Fabric

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 11 czerwca 2019 r. | [Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 lipca 2019 r. | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 lipca 2019 r. | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 sierpnia 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 października 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Informacje o wersji] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Poprzednie wersje

### <a name="service-fabric-64-releases"></a>Wersje 6,4 Service Fabric

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 30 listopada 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 grudnia 2018 | [Usługa Azure Service Fabric 6,4 Refresh Release dla klastrów systemu Windows](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 lutego 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marca 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 kwietnia 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maja 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maja 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
