---
title: Wersje sieci szkieletowej usług Azure Service
description: Informacje o wersji dla sieci szkieletowej usług Azure. Zawiera informacje na temat najnowszych funkcji i ulepszeń w sieci szkieletowej usług.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729596"
---
# <a name="service-fabric-releases"></a>Wydania sieci szkieletowej usług

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Rozwiązywanie problemów Przewodników</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Śledzenie problemów</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Opcje</a> 
| pomocy technicznej<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Obsługiwane wersje</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kod przykłady</a>

Ten artykuł zawiera więcej informacji na temat najnowszych wersji i aktualizacji środowiska wykonawczego sieci szkieletowej usług i SDK.

## <a name="whats-new-in-service-fabric"></a>Co nowego w sieci szkieletowej usług

### <a name="service-fabric-71"></a>Tkanina serwisowa 7.1
Ze względu na obecny kryzys COVID-19 i biorąc pod uwagę wyzwania stojące przed naszymi klientami, udostępniamy 7.1, ale nie uaktualniamy automatycznie klastrów ustawionych na automatyczne uaktualnienia. Wstrzymujemy automatyczne uaktualnienia do odwołania, aby upewnić się, że klienci mogą stosować uaktualnienia, gdy jest to dla nich najbardziej odpowiednie, aby uniknąć nieoczekiwanych zakłóceń.

Będzie można aktualizować do 7.1 za pośrednictwem [witryny Azure Portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) lub za pośrednictwem [wdrożenia usługi Azure Resource Manager.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)

Klastry sieci szkieletowej usług z włączonymi automatycznymi uaktualnieniami zaczną automatycznie odbierać aktualizację 7.1 po wznowieniu standardowej procedury wdrażania. Przed rozpoczęciem standardowego wdrażania w [witrynie społeczności Service Fabric Tech](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)udostępnimy kolejne ogłoszenie.
Opublikowaliśmy również aktualizacje do końca daty wsparcia dla głównych wydań, począwszy od 6.5 do 7.1 [tutaj](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions). 

## <a name="what-is-new-in-service-fabric-71"></a>Co nowego jest w sieci szkieletowej 7.1?
Z przyjemnością ogłaszamy kolejną wersję sieci szkieletowej usług. Ta wersja jest ładowana z kluczowych funkcji i ulepszeń. Niektóre z kluczowych funkcji są wyróżnione poniżej:
## <a name="key-announcements"></a>Kluczowe anonse
- **Ogólna dostępność** [ **tożsamości zarządzanych sieci szkieletowej usług dla aplikacji sieci szkieletowej usług**](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**Wsparcie dla Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**Wersja zapoznawcza: Obsługa dysków systemu operacyjnego VMSS Efemeryczny**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**: Dyski efemerycznego systemu operacyjnego są magazynem utworzonym na lokalnej maszynie wirtualnej i nie są zapisywane w zdalnym usłudze Azure Storage. Są one zalecane dla wszystkich typów węzłów sieci szkieletowej usług (podstawowy i pomocniczy), ponieważ w porównaniu do tradycyjnych dysków systemu operacyjnego trwałe, efemeryczne dyski systemu operacyjnego:
      -  Zmniejszanie opóźnienia odczytu/zapisu na dysku systemu operacyjnego
      -  Szybsze resetowanie/ponowne odtwarzanie operacji zarządzania węzłami obrazu
      -  Zmniejsz ogólne koszty (dyski są bezpłatne i nie ponoszą żadnych dodatkowych kosztów magazynowania)
- Obsługa deklaracji [**certyfikatów service endpoint aplikacji sieci szkieletowej według nazwy pospolitej podmiotu**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources).
- [**Obsługa sond kondycji dla konteneryzowanych usług:**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)Obsługa mechanizmu sondowania żywotności dla aplikacji konteneryzowanych. Liveness Probe pomóc ogłosić żywotność konteneryzowanej aplikacji i gdy nie reagują w odpowiednim czasie, spowoduje to ponowne uruchomienie. 
- [**Obsługa pakietów kodu inicjatora**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) dla [kontenerów](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) i aplikacji [wykonywalnych gościa.](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) Umożliwia to wykonywanie pakietów kodu (np. kontenerów) w określonej kolejności w celu zainicjowania pakietu serwisowego.
- **FabricObserver i ClusterObserver** są aplikacjami bezstanowymi, które przechwytują telemetryczne sieci szkieletowej usług związane z różnymi aspektami klastra SF. Obie te aplikacje są gotowe do wdrożenia w klastrach produkcyjnych systemu Windows do przechwytywania zaawansowanej telemetrii z zaimplementowanym wsparciem dla ApplicationInsights, EventSource i LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- działa na wszystkich węzłach, generuje zdarzenia kondycji, emituje dane telemetryczne po osiągnięciu progów użycia zasobów skonfigurowanych przez użytkownika. Ta wersja zawiera kilka ulepszeń w zakresie monitorowania, zarządzania danymi, szczegółów zdarzeń kondycji, ustrukturyzowanych danych telemetrycznych.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - działa w jednym węźle, przechwytuje dane telemetryczne kondycji na poziomie klastra. W tej wersji ClusterObserver monitoruje również stan węzła i emituje dane telemetryczne, gdy węzeł jest wyłączony/wyłączający/wyłączony przez dłuższy niż okres określony przez użytkownika.

### <a name="improve-application-life-cycle-experience"></a>Poprawa cyklu życia aplikacji

- **[Podgląd:Żądanie drenażu:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)** Podczas planowanej konserwacji usługi, takich jak uaktualnienia usługi lub dezaktywacji węzła, chcesz zezwolić usługom bezpiecznie opróżnić połączenia. Ta funkcja dodaje czas trwania opóźnienia zamknięcia wystąpienia w konfiguracji usługi. Podczas planowanych operacji SF usunie adres usługi z odnajdowania, a następnie odczeka ten czas trwania przed zamknięciem usługi.
- **[Automatyczne wykrywanie i równoważenie podklastrów:](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )** Subclustering ma miejsce, gdy usługi z różnymi ograniczeniami umieszczania mają wspólną [metrykę obciążenia.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics) Jeśli obciążenie różnych zestawów węzłów znacznie się różni, Menedżer zasobów klastra sieci szkieletowej usług uważa, że klaster jest niezrównoważony, nawet jeśli ma najlepszą możliwą równowagę z powodu ograniczeń umieszczania. W rezultacie próbuje zrównoważyć klaster, potencjalnie powodując niepotrzebne ruchy usługi (ponieważ "brak równowagi" nie może być znacznie poprawiony). Począwszy od tej wersji, Menedżer zasobów klastra będzie teraz próbował automatycznie wykryć tego rodzaju konfiguracje i zrozumieć, kiedy nierównowaga może być ustalona poprzez ruch, a kiedy zamiast tego należy pozostawić rzeczy w spokoju, ponieważ nie można dokonać znaczącej poprawy.  
- [**Inny koszt przenoszenia dla replik pomocniczych:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)Wprowadziliśmy nową wartość kosztu przeniesienia VeryHigh, która zapewnia dodatkową elastyczność w niektórych scenariuszach, aby określić, czy oddzielny koszt przeniesienia powinien być używany dla replik pomocniczych.
- Włączony mechanizm [**sondowania żywotności**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage ) dla aplikacji konteneryzowanych. Liveness Probe pomóc ogłosić żywotność konteneryzowanej aplikacji i gdy nie reagują w odpowiednim czasie, spowoduje to ponowne uruchomienie.
- [**Bieganie do zakończenia/raz dla usług**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>Ulepszenia sklepu z obrazami
 - Sieć szkieletowa usług 7.1 domyślnie używa **transportu niestandardowego do bezpiecznego transferu plików między węzłami.** Zależność od udziału plików SMB jest usuwana z wersji 7.1. Zabezpieczone udziały plików SMB są nadal istniejące w węzłach, które zawierają replikę usługi Image Store Service dla klienta, aby zrezygnować z domyślnego oraz uaktualnienia i zmiany na starszą wersję.
       
 ### <a name="reliable-collections-improvements"></a>Ulepszenia niezawodnych kolekcji

- [**W pamięci tylko magazyn obsługi usług stanowych przy użyciu niezawodnych kolekcji:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)Volatile Niezawodne kolekcje umożliwia utrwalone dane na dysku dla trwałości przed awariami na dużą skalę, może służyć do obciążeń, takich jak replikowanej pamięci podręcznej, na przykład, gdzie sporadyczne utraty danych mogą być tolerowane. Na podstawie [ograniczeń i ograniczeń volatile niezawodne kolekcje,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)zaleca się to dla obciążeń, które nie wymagają trwałości, dla usług, które obsługują rzadkie okazje utraty kworum.
- [**Wersja zapoznawcza: Eksplorator kopii zapasowych sieci szkieletowej sieci usług:**](https://github.com/microsoft/service-fabric-backup-explorer)Aby ułatwić zarządzanie kopiami zapasowymi niezawodnych kolekcji dla aplikacji stanowych sieci szkieletowej usług, Eksplorator kopii zapasowych sieci szkieletowej usług umożliwia użytkownikom
    - Inspekcja i przegląd zawartości wiarygodnych kolekcji,
    - Aktualizowanie bieżącego stanu do spójnego widoku
    - Tworzenie kopii zapasowej bieżącej migawki niezawodnych kolekcji
    - Naprawianie uszkodzenia danych
                 
### <a name="service-fabric-71-releases"></a>Wersje usługi Fabric 7.1
| Data wydania | Release | Więcej informacji |
|---|---|---|
| 20 kwietnia 2020 r. | [Usługa Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>Tkanina serwisowa 7.0

Usługa Azure Service Fabric 7.0 jest już dostępna! Będzie można zaktualizować do 7.0 za pośrednictwem witryny Azure portal lub za pośrednictwem wdrożenia usługi Azure Resource Manager. Ze względu na opinie klientów na temat wydań w okresie świątecznym nie rozpoczniemy automatycznej aktualizacji klastrów ustawionych na automatyczne uaktualnienia do stycznia.
W styczniu wznowimy standardową procedurę wdrażania, a klastry z włączonymi automatycznymi uaktualnieniami zaczną automatycznie odbierać aktualizację 7.0. Przed rozpoczęciem wdrożenia przedstawimy kolejne ogłoszenie.
Zaktualizujemy również planowane daty premiery, aby wskazać, że bierzemy pod uwagę tę politykę. Poszukaj tutaj aktualizacji naszych przyszłych [harmonogramów wersji.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)
 
Jest to najnowsza wersja sieci szkieletowej usług i jest ładowany z kluczowych funkcji i ulepszeń.

### <a name="key-announcements"></a>Kluczowe anonse
 - [**Obsługa keyvaultreference dla wpisów tajnych aplikacji (Wersja zapoznawcza)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Aplikacje sieci szkieletowej usług, które włączyły [tożsamości zarządzane,](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) mogą teraz bezpośrednio odwoływać się do tajnego adresu URL magazynu kluczy jako zmiennej środowiskowej, parametru aplikacji lub poświadczenia repozytorium kontenera. Sieci szkieletowej usług automatycznie rozwiąże klucz tajny przy użyciu tożsamości zarządzanej aplikacji. 
     
- **Ulepszone bezpieczeństwo uaktualnień dla usług bezstanowych:** Aby zagwarantować dostępność podczas uaktualniania aplikacji, wprowadziliśmy nowe konfiguracje, aby zdefiniować [minimalną liczbę wystąpień dla usług bezstanowych,](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) które mają być uważane za dostępne. Wcześniej wartość ta wynosiła 1 dla wszystkich usług i nie była zmienna. Dzięki tej nowej kontroli bezpieczeństwa dla usług można zapewnić, że usługi zachowują minimalną liczbę wystąpień podczas uaktualniania aplikacji, uaktualnień klastra i innych konserwacji, które opierają się na kontrolach kondycji i bezpieczeństwa sieci szkieletowej usług.
  
- [**Limity zasobów dla usług użytkowników:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)Użytkownicy mogą konfigurować limity zasobów dla usług użytkowników w węźle, aby zapobiec scenariuszom, takim jak wyczerpanie zasobów usług systemu sieci szkieletowej usług sieci szkieletowej usług. 
  
- [**Bardzo wysoki koszt przeniesienia usługi**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) dla typu repliki. Repliki z bardzo wysokim kosztem przeniesienia zostaną przeniesione tylko wtedy, gdy w klastrze występuje naruszenie ograniczeń, którego nie można naprawić w żaden inny sposób. Zobacz dokumenty, aby uzyskać dodatkowe informacje na temat tego, kiedy użycie "bardzo wysoki" koszt przeniesienia jest uzasadnione i dodatkowe uwagi.
  
-  **Dodatkowe kontrole bezpieczeństwa klastra:** W tej wersji wprowadziliśmy konfigurowalną kontrolę bezpieczeństwa kworum węzła źródłowego. Dzięki temu można dostosować, ile węzłów źródłowych muszą być dostępne podczas klastra cyklu życia i scenariuszy zarządzania. Operacje, które miałyby klastra poniżej skonfigurowaną wartość są blokowane. Obecnie wartość domyślna jest zawsze kworum węzłów źródłowych, na przykład, jeśli masz 7 węzłów źródłowych, operacja, która zabierze Cię poniżej 5 węzłów źródłowych będzie domyślnie zablokowany. Dzięki tej zmianie można wprowadzić minimalną bezpieczną wartość 6, która pozwoliłaby na wysunięciu tylko jednego węzła źródłowego naraz.
   
- Dodano obsługę [**zarządzania usługą tworzenia kopii zapasowych i przywracania w Eksploratorze sieci szkieletowej usług**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Umożliwia to następujące działania bezpośrednio z poziomu SFX: odnajdowanie usługi tworzenia kopii zapasowych i przywracania, tworzenie zasad tworzenia kopii zapasowych, włączanie automatycznych kopii zapasowych, wykonywanie kopii zapasowych adhoc, wyzwalanie operacji przywracania i przeglądanie istniejących kopii zapasowych.

- Ogłaszając dostępność [**ReliableCollectionsMissingTypesTool:**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)To narzędzie pomaga sprawdzić, czy typy używane w niezawodnych kolekcjach są zgodne do przodu i do tyłu podczas uaktualniania aplikacji stopniowych. Pomaga to zapobiec awariom uaktualnienia lub utracie danych i uszkodzeniu danych z powodu brakujących lub niezgodnych typów.

- [**Włącz stabilne odczyty w replikach pomocniczych:Odczyty**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)stabilne ograniczą repliki pomocnicze do zwracanych wartości, które zostały przypisane do kworum.

Ponadto ta wersja zawiera inne nowe funkcje, poprawki błędów i ulepszenia obsługi, niezawodności i wydajności. Pełna lista zmian znajduje się w [informacjach o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Wersje usługi Fabric 7.0

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 18 listopada 2019 r. | [Usługa Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 stycznia 2020 r. | [Wersja odświeżania usługi Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 lutego 2020 r. | [Wersja odświeżania usługi Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 marca 2020 r. | [Wersja odświeżania usługi Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Tkanina serwisowa 6.5

Ta wersja zawiera ulepszenia obsługi, niezawodności i wydajności, nowe funkcje, poprawki błędów i ulepszenia ułatwiające zarządzanie klastrem i cyklem życia aplikacji.

> [!IMPORTANT]
> Usługa Fabric 6.5 to ostateczna wersja z obsługą narzędzi sieci szkieletowej usług w programie Visual Studio 2015. Klienci powinni przejść do [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/) w przyszłości.

Oto co nowego w sieci szkieletowej usług 6.5:

- Eksplorator sieci szkieletowej usług zawiera [przeglądarkę magazynu obrazów](service-fabric-visualizing-your-cluster.md#image-store-viewer) do sprawdzania aplikacji przekazanych do magazynu obrazów.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) w wersji [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) zawiera wiele ulepszeń autodiagnostyki. Klienci POA są zalecane, aby przejść do tej wersji.

- [Usługa EventStore jest domyślnie włączona](service-fabric-visualizing-your-cluster.md#event-store) dla klastrów sieci szkieletowej usługi 6.5, chyba że zrezygnowano.

- Dodano [zdarzenia cyklu życia repliki](service-fabric-diagnostics-event-generation-operational.md#replica-events) dla usług stanowych.

- [Lepsza widoczność stanu węzła źródłowego,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)w tym ostrzeżenia na poziomie klastra, jeśli węzeł źródłowy jest w złej kondycji *(W dół,* *Usunięte* lub *Nieznane).*

- Narzędzie do [odzyskiwania po awarii aplikacji sieci szkieletowej](https://github.com/Microsoft/Service-Fabric-AppDRTool) usług umożliwia usługi stanowe sieci szkieletowej usług, aby szybko odzyskać, gdy klaster podstawowy napotka katastrofę. Dane z klastra podstawowego są stale synchronizowane w pomocniczej aplikacji rezerwowej przy użyciu okresowej kopii zapasowej i przywracania.

- Obsługa programu Visual Studio do [publikowania aplikacji .NET Core w klastrach opartych na systemie Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Interfejs wiersza polecenia sieci szkieletowej usług Azure (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) zostanie zainstalowany automatycznie dla sieci szkieletowej usługi 6.5 (i nowszych wersji) podczas uaktualniania lub tworzenia nowego klastra systemu Linux na platformie Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) jest instalowany domyślnie w klastrach MacOS/Linux OneBox.

Aby uzyskać więcej informacji, zobacz [Informacje o wersji sieci szkieletowej usług 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Wersje usługi Fabric 6.5

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 11 czerwca 2019 r. | [Usługa Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 lipca 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 lipca 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 sierpnia 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 października 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Informacje o wersji] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Poprzednie wersje

### <a name="service-fabric-64-releases"></a>Wersje usługi Fabric 6.4

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 30 listopada 2018 r. | [Usługa Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 grudnia 2018 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4 dla klastrów systemu Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 lutego 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marca 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 kwietnia 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maja 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maja 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
