---
title: Azure Service Fabric wydań
description: Informacje o wersji dla usługi Azure Service Fabric. Zawiera informacje o najnowszych funkcjach i ulepszeniach Service Fabric.
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 2a035f531e03dc42e8be4f3dab403406eb7c8f14
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518630"
---
# <a name="service-fabric-releases"></a>Service Fabric wersji

Ten artykuł zawiera więcej informacji na temat najnowszych wersji i aktualizacji środowiska Service Fabric uruchomieniowego i zestawów SDK.

Dostępne są również następujące zasoby:
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Przewodniki dotyczące rozwiązywania problemów</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Śledzenie problemów</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Opcje pomocy technicznej</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Obsługiwane wersje</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Przykłady kodu</a>


## <a name="service-fabric-80"></a>Service Fabric 8.0

Z przyjemnością ogłaszamy, że wersja 8.0 środowiska uruchomieniowego usługi Service Fabric została uruchomiona w różnych regionach świadczenia usługi Azure wraz z aktualizacjami narzędzi i zestawu SDK. Aktualizacje zestawu SDK platformy .NET, zestawu Java SDK Service Fabric runtime są dostępne za pośrednictwem Instalatora platformy sieci Web, pakietów NuGet i repozytoriów Maven.

### <a name="key-announcements"></a>Najważniejsze ogłoszenia

- **Ogólna dostępność** pomocy technicznej dla programu .NET 5 dla systemu Windows
- **Ogólna dostępność** [bez stanowych typów węzłów](https://docs.microsoft.com/azure/service-fabric/service-fabric-stateless-node-types)
- Możliwość przenoszenia bez stanowych wystąpień usługi
- Możliwość dodawania sparametryzowanego ładunku DefaultLoad w manifeście aplikacji
- W przypadku uaktualnień repliki pojedynczej — możliwość definicji niektórych ustawień na poziomie klastra na poziomie aplikacji
- Możliwość inteligentnego umieszczania na podstawie tagów węzłów
- Możliwość zdefiniowania progu procentowego węzłów w złej kondycji, które mają wpływ na kondycję klastra
- Możliwość wykonywania zapytań o usługi ładowane na najwyższym poziomie
- Możliwość dodania nowego interwału dla nowych kodów błędów
- Możliwość oznaczania wystąpienia usługi jako ukończonego
- Obsługa modelu wdrażania opartego na falach dla automatycznych uaktualnień
- Dodano sondę gotowości dla konteneryzowanych aplikacji
- Domyślnie włącz wartość True dla wartości UseSeparateSecondaryMoveCost
- Rozwiązano problem z menedżerem stanu w celu zwolnienia odwołania tak szybko, jak to było bezpieczne
- Blokuj usuwanie centralnej usługi wpisów tajnych podczas przechowywania wpisów tajnych użytkowników


### <a name="service-fabric-80-releases"></a>Service Fabric wersji 8.0
| Data wydania | Release | Więcej informacji |
|---|---|---|
| 8 kwietnia 2021 r. | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|


## <a name="previous-versions"></a>Poprzednie wersje

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>Najważniejsze ogłoszenia

- **Wersja** [**zapoznawcza: Service Fabric zarządzane są**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) teraz w publicznej wersji zapoznawczej. Service Fabric klastry zarządzane mają na celu uproszczenie wdrażania klastra i zarządzania nimi przez hermetyzowanie podstawowych zasobów, które Service Fabric klastra w pojedynczy zasób usługi ARM. Aby uzyskać więcej informacji, zobacz [Service Fabric omówienie klastra zarządzanego.](./overview-managed-cluster.md)
- **Wersja** zapoznawcza: [**obsługa usług bez stanowych**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) z liczbą wystąpień większą niż liczba węzłów jest teraz w publicznej wersji zapoznawczej. Zasady umieszczania umożliwiają tworzenie wielu bez stanowych wystąpień partycji w węźle.
- [**Serwer FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) jest teraz dostępny.
    - Teraz możesz uruchomić serwer FabricObserver w klastrach z systemami Linux i Windows.
    - Teraz można tworzyć wtyczki niestandardowego obserwatora. Aby uzyskać szczegółowe informacje [](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) i kod, zobacz [wtyczkę readme](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) i przykładowy projekt wtyczki.
    - Teraz możesz zmienić dowolne ustawienie obserwatora za pomocą uaktualnienia parametrów aplikacji. Oznacza to, że nie trzeba już ponownie wdaj się w celu zmodyfikowania określonych ustawień obserwatora. Zobacz [przykład](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Obsługa obrazów kontenerów OneBox systemu Ubuntu 18.04.**](https://hub.docker.com/_/microsoft-service-fabric-onebox)
- **Wersja zapoznawcza:** [keyvault reference for Service Fabric applications supports ONLY versioned secrets (Informacje referencyjne dotyczące programu KeyVault dla aplikacji **Service Fabric obsługują **TYLKO wersjone wpisy tajne.** Wpisy tajne bez wersji nie są obsługiwane.**](./service-fabric-keyvault-references.md)
- Zestaw SDK usługi SF wymaga najnowszej wersji programu VS 2019 w wersji 16.7.6 lub 16.8 (wersja zapoznawcza 4), aby można było tworzyć nowe projekty .NET Framework bez stanowych/stanowych/aktorów. Jeśli nie masz najnowszej aktualizacji programu VS, po utworzeniu projektu usługi użyj menedżera pakietów do zainstalowania pakietu Microsoft.ServiceFabric.Services (wersja 4.2.x) dla projektów stanowych/bez stanowych oraz Microsoft.ServiceFabric.Actors (wersja 4.2.x) dla projektów aktorów z programu nuget.org.
- **RunToCompletion:** Service Fabric obsługuje koncepcję uruchamiania do ukończenia dla plików wykonywalnych gościa. Po zakończeniu pracy repliki w tej aktualizacji zasoby klastra przydzielone do tej repliki zostaną zwolnione.
- [**Rozszerzono obsługę ładu zasobów:**](./service-fabric-resource-governance.md)zezwalanie na specyfikacje żądań i limitów dla zasobów procesora i pamięci.

#### <a name="service-fabric-72-releases"></a>Service Fabric wersji 7.2
| Data wydania | Release | Więcej informacji |
|---|---|---|
| 21 października 2020 r. | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 listopada 2020 r. | [Azure Service Fabric 7.2 Second Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 listopada 2020 r.  | Azure Service Fabric 7.2 Third Refresh Release | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 grudnia 2020 r. | [Czwarta wersja odświeżania usługi Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 stycznia 2021 r. | [Azure Service Fabric 7.2 Piąta wersja odświeżania](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 lutego 2021 r. | [Azure Service Fabric 7.2 Szósta wersja odświeżania](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10 marca 2021 r. | [Azure Service Fabric 7.2 Wydanie z odświeżeniami czasowego](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1

Z powodu obecnego kryzysu związanego z covid-19 i biorąc pod uwagę wyzwania stojące przed naszymi klientami, 7.1 jest dostępne, ale nie będziemy automatycznie uaktualniać klastrów ustawionych w celu otrzymywania automatycznych uaktualnień. Ws używamy automatycznych uaktualnień do momentu powiadomienia, aby zapewnić, że klienci będą w stanie stosować uaktualnienia, gdy będą dla nich najbardziej odpowiednie, aby uniknąć nieoczekiwanych zakłóceń.

Aktualizację do wersji 7.1 będzie [](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal) można zaktualizować za pośrednictwem Azure Portal lub za [pośrednictwem Azure Resource Manager wdrożenia.](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)

Service Fabric z włączonymi automatycznymi uaktualnieniami zaczną automatycznie otrzymywać aktualizację 7.1 po wznowieniu standardowej procedury. Przed rozpoczęciem standardowego wejdą w życie kolejne ogłoszenie w [witrynie społeczności technicznej](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)Service Fabric Tech Community.
Opublikowaliśmy również aktualizacje do końca okresu wsparcia technicznego dla wersji głównych od wersji od 6.5 do 7.1 [tutaj.](./service-fabric-versions.md) 

#### <a name="key-announcements"></a>Najważniejsze ogłoszenia

- **Ogólna** dostępność [ **Service Fabric tożsamości zarządzanych dla Service Fabric aplikacji**](./concepts-managed-identity.md)
- [**Obsługa systemu Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Wersja zapoznawcza:**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)Zestaw skalowania maszyn wirtualnych Obsługa efemeralnych dysków systemu operacyjnego **: Efemeralne dyski systemu operacyjnego są magazynami utworzonymi na lokalnej maszynie wirtualnej, a nie zapisanymi w zdalnej usłudze Azure Storage. Są one zalecane dla wszystkich Service Fabric typów węzłów (podstawowy i pomocniczy), ponieważ w porównaniu z tradycyjnymi trwałymi dyskami systemu operacyjnego efemeralne dyski systemu operacyjnego:
      -  Zmniejszenie opóźnienia odczytu/zapisu na dysku systemu operacyjnego
      -  Szybsze resetowanie/ponowne obrazowanie operacji zarządzania węzłami
      -  Zmniejsz ogólne koszty (dyski są bezpłatne i nie są naliczane żadne dodatkowe koszty magazynowania)
- Obsługa deklaracji certyfikatów punktu końcowego usługi [**dla aplikacji Service Fabric przez nazwę pospolitą podmiotu**](./service-fabric-service-manifest-resources.md).
- [**Obsługa sond kondycji dla usług konteneryzowanych:**](./probes-codepackage.md)obsługa mechanizmu sondy liveness dla konteneryzowanych aplikacji. Sonda liveness pomaga poinformować o żywo konteneryzowanej aplikacji, a jeśli nie odpowie w terminowy sposób, spowoduje to ponowne uruchomienie. 
- [**Obsługa pakietów kodu inicjatora**](./initializer-codepackages.md) dla [kontenerów](./service-fabric-containers-overview.md) i [aplikacji wykonywalnych gościa.](./service-fabric-guest-executables-introduction.md) Umożliwia to wykonywanie pakietów kodu (np. kontenerów) w określonej kolejności w celu inicjowania pakietu usługi.
- **FabricObserver i ClusterObserver** to bez stanowe aplikacje, które Service Fabric dane telemetryczne związane z różnymi aspektami klastra sf. Obie te aplikacje są gotowe do wdrożenia w klastrach produkcyjnych systemu Windows w celu przechwytywania rozbudowanych danych telemetrycznych z zaimplementowaną obsługą usług ApplicationInsights, EventSource i LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)— działa we wszystkich węzłach, generuje zdarzenia kondycji, emituje telemetrię po osiągnięciu progów użycia zasobów skonfigurowanych przez użytkownika. Ta wersja zawiera kilka ulepszeń dotyczących monitorowania, zarządzania danymi, szczegółów zdarzeń kondycji i telemetrii strukturalnej.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) — działa w jednym węźle i przechwytuje dane telemetryczne kondycji na poziomie klastra. W tej wersji clusterObserver monitoruje również stan węzła i emituje dane telemetryczne, gdy węzeł jest wyłączony/wyłączony przez czas dłuższy niż określony przez użytkownika.

#### <a name="improve-application-life-cycle-experience"></a>Ulepszanie obsługi cyklu życia aplikacji

- **[Wersja zapoznawcza:opróżnianie](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** żądań: podczas planowanej konserwacji usługi, takiej jak uaktualnienia usług lub dezaktywacja węzłów, chcesz zezwolić usługom na bezpiecznie opróżnianie połączeń. Ta funkcja dodaje czas trwania opóźnienia zamknięcia wystąpienia w konfiguracji usługi. Podczas planowanych operacji sf usunie adres usługi z odnajdywania, a następnie zaczeka ten czas przed zamknięciem usługi.
- **[Automatyczne wykrywanie i równoważenie](./cluster-resource-manager-subclustering.md)** podklasterów: podklasterowanie ma miejsce, gdy usługi z różnymi ograniczeniami umieszczania mają wspólną [metrykę obciążenia](./service-fabric-cluster-resource-manager-metrics.md). Jeśli obciążenie różnych zestawów węzłów różni się znacznie, Service Fabric Menedżer zasobów klastra uważa, że klaster jest niezrównoważony, nawet jeśli ma najlepszą możliwą równowagę ze względu na ograniczenia umieszczania. W związku z tym próbuje ponownie zrównoważyć klaster, potencjalnie powodując niepotrzebne przepływy usług (ponieważ nie można znacząco poprawić "dysproporcji"). Począwszy od tej wersji, Menedżer zasobów klastra teraz spróbuje automatycznie wykryć tego rodzaju konfiguracje i zrozumieć, kiedy dysproporcja może zostać usunięta przez ruch, a kiedy zamiast tego powinna pozostawić wszystko bez zmian, ponieważ nie można znacząco poprawić.  
- [**Różne koszty przenoszenia**](./service-fabric-cluster-resource-manager-movement-cost.md)replik pomocniczych: wprowadziliśmy nową wartość kosztu przenoszenia VeryHigh, która zapewnia dodatkową elastyczność w niektórych scenariuszach w celu zdefiniowania, czy dla replik pomocniczych należy użyć oddzielnego kosztu przenoszenia.
- Włączono [**mechanizm sondy żywotności**](./probes-codepackage.md) dla konteneryzowanych aplikacji. Sonda liveness pomaga poinformować o żywo konteneryzowanej aplikacji, a jeśli nie odpowie w terminowy sposób, spowoduje to ponowne uruchomienie.
- [**Uruchamianie do ukończenia/jeden raz dla usług**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Magazyn obrazów ulepszenia
 - Service Fabric 7.1 domyślnie używa transportu niestandardowego do zabezpieczania **transferu plików między węzłami.** Zależność od udziału plików SMB jest usuwana z wersji 7.1. Zabezpieczone udziały plików SMB nadal istnieją w węzłach, które zawierają replikę usługi Magazyn obrazów Service, co oznacza, że klient zrezygnuje z domyślnego i uaktualnienia do starszej wersji.
       
 #### <a name="reliable-collections-improvements"></a>Ulepszenia funkcji Reliable Collections

- [](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)W pamięci przechowuj tylko obsługę usług stanowych przy użyciu niezawodnych kolekcji: Niezawodne kolekcje nietrwałe umożliwiają utrwalanie danych na dysku w celu zapewnienia trwałości w przypadku awarii na dużą skalę. Można ich używać na przykład w przypadku obciążeń, takich jak replikowana pamięć podręczna, na przykład gdy można tolerować sporadyczne utraty danych. W oparciu o ograniczenia i ograniczenia funkcji [Volatile Reliable Collections](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)zalecamy użycie tej funkcji w przypadku obciążeń, które nie wymagają trwałości, w przypadku usług, które obsługują rzadkie przypadki utraty kworum.
- [**Wersja zapoznawcza: Service Fabric Eksplorator usługi Backup:**](https://github.com/microsoft/service-fabric-backup-explorer)aby ułatwić zarządzanie kopiami zapasowymi kolekcji Reliable Collections Service Fabric aplikacji stanowych, Service Fabric Eksplorator usługi Backup użytkownikom
    - Inspekcja i przeglądanie zawartości kolekcji Reliable Collections,
    - Aktualizowanie bieżącego stanu do spójnego widoku
    - Tworzenie kopii zapasowej bieżącej migawki kolekcji Reliable Collections
    - Naprawianie uszkodzenia danych
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric wersji 7.1
| Data wydania | Release | Więcej informacji |
|---|---|---|
| 20 kwietnia 2020 r. | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 czerwca 2020 r. | [Microsoft Azure Service Fabric 7.1 Pierwsze odświeżenie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 lipca 2020 r. | [Microsoft Azure Service Fabric 7,1 sekundy odświeżania](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 sierpnia 2020 r. | [Microsoft Azure Service Fabric 7.1 Trzecie odświeżanie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 września 2020 r. | [Microsoft Azure Service Fabric 7.1 Czwarte odświeżanie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 października 2020 r. | Microsoft Azure Service Fabric 7.1 Szóste odświeżenie | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 listopada 2020 r. | Microsoft Azure Service Fabric 7.1 Ósme odświeżanie | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Usługa Azure Service Fabric 7.0 jest teraz dostępna! Aktualizację do wersji 7.0 będzie można zaktualizować za pośrednictwem Azure Portal lub za pośrednictwem Azure Resource Manager wdrożenia. Ze względu na opinie klientów dotyczące wydań w okresie świątecznym nie rozpoczniemy automatycznego aktualizowania klastrów ustawionych na otrzymywanie automatycznych uaktualnień do stycznia.
W styczniu wznowimy standardową procedurę wycofywania, a klastry z włączonymi automatycznymi uaktualnieniami zaczną automatycznie otrzymywać aktualizację 7.0. Przed rozpoczęciem tego wycofywania udostępnimy kolejne zawiadomienie.
Zaktualizujemy również planowane daty wydania, aby wskazać, że te zasady zostaną uwzględnione. Poszukaj tutaj aktualizacji naszych przyszłych [harmonogramów wydań.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)

#### <a name="key-announcements"></a>Najważniejsze ogłoszenia
 - [**Obsługa kluczy KeyVaultReference**](./service-fabric-keyvault-references.md)dla wpisów tajnych aplikacji [](./concepts-managed-identity.md) (wersja zapoznawcza): aplikacje usługi Service Fabric, które włączyły tożsamości zarządzane, mogą teraz bezpośrednio odwoływać się do adresu URL wpisów tajnych usługi Key Vault jako zmiennej środowiskowej, parametru aplikacji lub poświadczeń repozytorium kontenera. Service Fabric automatycznie rozpoznać klucz tajny przy użyciu tożsamości zarządzanej aplikacji. 
     
- **Lepsze bezpieczeństwo uaktualniania** usług bez stanowych: Aby zagwarantować dostępność podczas uaktualniania [](/dotnet/api/system.fabric.description.statelessservicedescription) aplikacji, wprowadziliśmy nowe konfiguracje definiujące minimalną liczbę wystąpień usług bez stanowych, które mają być uznawane za dostępne. Wcześniej ta wartość miała wartość 1 dla wszystkich usług i nie można było jej zmienić. Dzięki nowej kontroli bezpieczeństwa dla usługi możesz upewnić się, że usługi zachowują minimalną liczbę up wystąpień podczas uaktualnień aplikacji, uaktualnień klastra i innych czynności konserwacyjnych, które są przeprowadzane na podstawie kontroli kondycji i bezpieczeństwa usługi Service Fabric.
  
- [**Limity zasobów dla usług użytkownika:**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)użytkownicy mogą skonfigurować limity zasobów dla usług użytkownika w węźle, aby zapobiec scenariuszom, takim jak wyczerpanie zasobów Service Fabric usług systemowych. 
  
- [**Bardzo duży koszt przenoszenia usługi**](./service-fabric-cluster-resource-manager-movement-cost.md) dla typu repliki. Repliki z bardzo wysokim kosztem przenoszenia zostaną przeniesione tylko w przypadku naruszenia ograniczeń w klastrze, których nie można naprawić w jakikolwiek inny sposób. Zapoznaj się z połączonym dokumentem, aby uzyskać dodatkowe informacje na temat tego, kiedy użycie "bardzo wysokiego" kosztu przeniesienia jest uzasadnione i w celu uwzględnienia dodatkowych kwestii.
  
-  **Dodatkowe kontrole bezpieczeństwa klastra:** w tej wersji wprowadziliśmy konfigurowalny test zabezpieczeń kworum węzła iniekcjatora. Dzięki temu można dostosować, ile węzłów iniekcjonowych musi być dostępnych podczas cyklu życia klastra i scenariuszy zarządzania. Operacje, które byłyby wykonywane w klastrze poniżej skonfigurowanej wartości, są blokowane. Obecnie wartością domyślną jest zawsze kworum węzłów iniekcjonowania, na przykład jeśli masz 7 węzłów iniekcjonowania, operacja, która będzie wymagać mniej niż 5 węzłów iniekcjonowania, zostanie domyślnie zablokowana. Dzięki tej zmianie można wprowadzić minimalną bezpieczną wartość 6, co pozwoliłoby na nie tylko jeden węzeł iniekcyjny jednocześnie.
   
- Dodano obsługę [**zarządzania usługą tworzenia kopii zapasowych i przywracania w programie Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Umożliwia to wykonywanie następujących działań bezpośrednio z poziomu SFX: odnajdywanie usługi tworzenia kopii zapasowych i przywracania, tworzenie zasad tworzenia kopii zapasowych, włączanie automatycznych kopii zapasowych, wykonywanie kopii zapasowych adhoc, wyzwalanie operacji przywracania i przeglądanie istniejących kopii zapasowych.

- Announcing availability of [**the ReliableCollectionsMissingTypesTool:**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)To narzędzie pomaga sprawdzić, czy typy używane w niezawodnych kolekcjach są zgodne do przodu i wstecz podczas stopniowego uaktualniania aplikacji. Pomaga to zapobiec niepowodzeniu uaktualniania lub utracie danych i ich uszkodzeniem z powodu brakujących lub niezgodnych typów.

- [**Włącz stabilne odczyty na replikach**](./service-fabric-reliable-services-configuration.md#configuration-names-1)pomocniczych: Stabilne odczyty ograniczą repliki pomocnicze do zwracanych wartości, które zostały skonsumowane w kworum.

Ponadto ta wersja zawiera inne nowe funkcje, poprawki błędów oraz ulepszenia możliwości obsługi, niezawodności i wydajności. Pełną listę zmian można znaleźć w informacjach [o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Service Fabric wersji 7.0

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 18 listopada 2019 r. | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 stycznia 2020 r. | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 lutego 2020 r. | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 marca 2020 r. | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 maja 2020 r. | [Azure Service Fabric 7.0 Szósta wersja odświeżania](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 października 2020 r. | Azure Service Fabric 7.09999 Refresh Release | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Ta wersja obejmuje ulepszenia obsługi, niezawodności i wydajności, nowe funkcje, poprawki błędów i ulepszenia ułatwiające zarządzanie cyklem życia klastra i aplikacji.

> [!IMPORTANT]
> Service Fabric 6.5 to ostateczna wersja z obsługą narzędzi Service Fabric w wersji Visual Studio 2015. Klientom zaleca się przejście do Visual Studio [2019](https://visualstudio.microsoft.com/vs/) r.

Co nowego w programie Service Fabric 6.5:

- Service Fabric Explorer zawiera przeglądarkę [Magazyn obrazów do](service-fabric-visualizing-your-cluster.md#image-store-viewer) sprawdzania aplikacji przekazanych do magazynu obrazów.

- [Aplikacja Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) [w wersji 1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) zawiera wiele ulepszeń samodzielnej diagnostyki. Klientom usługi POA zaleca się przejście do tej wersji.

- [Usługa EventStore jest domyślnie włączona](service-fabric-visualizing-your-cluster.md#event-store) dla Service Fabric 6.5, chyba że zrezygnujesz.

- Dodano [zdarzenia cyklu życia repliki](service-fabric-diagnostics-event-generation-operational.md#replica-events) dla usług stanowych.

- [Lepsza widoczność stanu węzła iniekcjowego,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)w tym ostrzeżeń na poziomie klastra, jeśli węzeł iniekcyjny jest w złej kondycji *(w* dół, *usunięty* lub *nieznany).*

- [Service Fabric odzyskiwania po awarii aplikacji](https://github.com/Microsoft/Service-Fabric-AppDRTool) Service Fabric stanowe mogą szybko odzyskać dane, gdy klaster podstawowy napotka awarię. Dane z klastra podstawowego są stale synchronizowane w pomocniczej aplikacji rezerwowej przy użyciu okresowych kopii zapasowych i przywracania.

- Visual Studio publikowania [aplikacji .NET Core w klastrach](service-fabric-how-to-publish-linux-app-vs.md)opartych na systemie Linux.

- Interfejs wiersza polecenia usługi [Azure Service Fabric (SFCTL)](./service-fabric-cli.md) zostanie zainstalowany automatycznie dla wersji Service Fabric 6.5 (i nowszych) podczas uaktualniania lub tworzenia nowego klastra systemu Linux na platformie Azure.

- [SFCTL](./service-fabric-cli.md) jest instalowany domyślnie w klastrach OneBox systemu MacOS/Linux.

Aby uzyskać więcej informacji, zobacz [Service Fabric 6.5 Release Notes (Informacje o wersji 6.5).](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)

#### <a name="service-fabric-65-releases"></a>Service Fabric wersji 6.5

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 11 czerwca 2019 r. | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 lipca 2019 r. | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 lipca 2019 r. | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 sierpnia 2019 r. | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Informacje o wersji](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 października 2019 r. | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Informacje o wersji] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric wersji 6.4

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 30 listopada 2018 r. | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 grudnia 2018 r. | [Azure Service Fabric 6.4 Refresh Release for Windows clusters (Wersja odświeżania usługi Azure Service Fabric 6.4 dla klastrów systemu Windows)](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 lutego 2019 r. | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marca 2019 r. | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 kwietnia 2019 r. | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maja 2019 r. | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maja 2019 r. | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
