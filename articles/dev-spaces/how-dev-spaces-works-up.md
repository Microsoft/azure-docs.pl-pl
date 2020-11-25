---
title: Jak działa kod przy użyciu Azure Dev Spaces Works
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Opisuje procesy uruchamiania kodu w usłudze Azure Kubernetes Service przy użyciu Azure Dev Spaces
keywords: azds. YAML, Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014435"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Jak działa kod przy użyciu Azure Dev Spaces Works

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces zapewnia wiele sposobów na szybkie Iterowanie i debugowanie aplikacji Kubernetes oraz współpracę z zespołem w klastrze usługi Azure Kubernetes Service (AKS). Gdy [projekt zostanie przygotowany do uruchomienia w obszarze dev][how-it-works-prep], możesz użyć funkcji miejsca do tworzenia i uruchamiania projektu w klastrze AKS.

W tym artykule opisano, co dzieje się z uruchamianiem kodu w AKS z miejscami dev.

## <a name="run-your-code"></a>Uruchamianie kodu

Aby uruchomić kod w miejscu deweloperskim, wydaj `up` polecenie w tym samym katalogu, w którym znajduje się `azds.yaml` plik:

```cmd
azds up
```

`up`Polecenie przekazuje pliki źródłowe aplikacji i inne artefakty, które są konieczne do kompilowania i uruchamiania projektu w obszarze dev. Z tego miejsca, kontroler w obszarze dev:

1. Tworzy obiekty Kubernetes do wdrożenia aplikacji.
1. Kompiluje kontener dla aplikacji.
1. Wdraża aplikację w obszarze dev.
1. Tworzy publicznie dostępną nazwę DNS dla punktu końcowego aplikacji, jeśli jest skonfigurowana.
1. Używa *portu do przodu* w celu zapewnienia dostępu do punktu końcowego aplikacji przy użyciu programu http://localhost .
1. Przesyła strumieniowo stdout i stderr do narzędzi po stronie klienta.


## <a name="starting-a-service"></a>Uruchamianie usługi

Po uruchomieniu usługi w obszarze dev narzędzia i kontroler po stronie klienta pracują w ramach koordynacji synchronizacji plików źródłowych, tworzenia kontenera i obiektów Kubernetes oraz uruchamiania aplikacji.

Na bardziej szczegółowym poziomie jest to, co się stanie po uruchomieniu `azds up` :

1. [Pliki są synchronizowane][sync-section] z komputera użytkownika z magazynem plików platformy Azure, który jest unikatowy dla klastra AKS użytkownika. Przekazano kod źródłowy, wykres Helm i pliki konfiguracyjne.
1. Kontroler tworzy żądanie uruchomienia nowej sesji. To żądanie zawiera kilka właściwości, w tym unikatowy identyfikator, nazwę przestrzeni, ścieżkę do kodu źródłowego i flagę debugowania.
1. Kontroler zastępuje symbol zastępczy *$ (tag)* na wykresie Helm z UNIKATOWYm identyfikatorem sesji i instaluje wykres Helm dla usługi. Dodanie odwołania do unikatowego identyfikatora sesji do wykresu Helm umożliwia skojarzenie kontenera wdrożonego z klastrem AKS dla tej konkretnej sesji z powrotem do żądania sesji i skojarzonych informacji.
1. Podczas instalacji wykresu Helm serwer przyjęcia elementu webhook Kubernetes dodaje dodatkowe kontenery do aplikacji pod kątem Instrumentacji i dostępu do kodu źródłowego projektu. Kontenery devspaces-proxy i devspaces-proxy-init są dodawane w celu zapewnienia śledzenia HTTP i routingu miejsca. Kontener devspaces-Build został dodany, aby zapewnić mu dostęp do wystąpienia platformy Docker i kodu źródłowego projektu na potrzeby tworzenia kontenera aplikacji.
1. Gdy aplikacja jest uruchomiona, kontener devspaces-Build i devspaces-proxy-init jest używany do kompilowania kontenera aplikacji. Kontenery aplikacji i kontenery devspaces-proxy są następnie uruchamiane.
1. Po rozpoczęciu kontenera aplikacji funkcja po stronie klienta używa funkcji *przekazywania portów* Kubernetes, aby zapewnić dostęp do aplikacji za pośrednictwem protokołu HTTP http://localhost . Ten port przekazujący nawiązuje połączenie komputera deweloperskiego z usługą w obszarze dev.
1. Gdy wszystkie kontenery w obszarze zostały uruchomione, usługa jest uruchomiona. W tym momencie funkcja po stronie klienta zaczyna przesyłać strumieniowo dane śledzenia HTTP, stdout i stderr. Te informacje są wyświetlane przez funkcję po stronie klienta dla dewelopera.

## <a name="updating-a-running-service"></a>Aktualizowanie uruchomionej usługi

Gdy usługa jest uruchomiona, Azure Dev Spaces ma możliwość aktualizowania tej usługi, jeśli którykolwiek z plików źródłowych projektu zostanie zmieniony. Przestrzenie programistyczne obsługują również aktualizowanie usługi w różny sposób w zależności od typu pliku, który został zmieniony. Istnieją trzy sposoby aktualizowania uruchomionej usługi przez funkcję Spaces dev:

* Bezpośrednia aktualizacja pliku
* Odbudowywanie i ponowne uruchamianie procesu aplikacji wewnątrz kontenera działającej aplikacji
* Ponowne kompilowanie i wdrażanie kontenera aplikacji

![Azure Dev Spaces synchronizacji plików](media/how-dev-spaces-works/file-sync.svg)

Niektóre pliki projektu, które są statyczne elementy zawartości, takie jak pliki HTML, CSS i cshtml, można aktualizować bezpośrednio w kontenerze aplikacji bez konieczności ponownego uruchamiania. W przypadku zmiany statycznego zasobu nowy plik zostanie zsynchronizowany z miejscem deweloperskim, a następnie używany przez uruchomiony kontener.

Zmiany plików, takie jak kod źródłowy lub pliki konfiguracji aplikacji, można zastosować przez ponowne uruchomienie procesu aplikacji w działającym kontenerze. Po zsynchronizowaniu tych plików proces aplikacji jest uruchamiany ponownie w ramach uruchomionego kontenera przy użyciu procesu *devhostagent* . Podczas pierwszego tworzenia kontenera aplikacji kontroler zastępuje polecenie uruchamiania dla aplikacji innym procesem o nazwie *devhostagent*. Rzeczywisty proces aplikacji jest następnie uruchamiany jako proces podrzędny w obszarze *devhostagent*, a jego dane wyjściowe są przekazywane przy użyciu danych wyjściowych *devhostagent*. Proces *devhostagent* jest również częścią funkcji miejsca do magazynowania i może wykonywać polecenia w działającym kontenerze w imieniu miejsc deweloperskich. Po ponownym uruchomieniu *devhostagent*:

* Powoduje zatrzymanie bieżącego procesu lub procesów skojarzonych z aplikacją
* Ponownie kompiluje aplikację
* Uruchamia ponownie proces lub procesy skojarzone z aplikacją

Sposób, w jaki *devhostagent* wykonuje powyższe kroki, jest [skonfigurowany w programie `azds.yaml`][azds-yaml-section].

Aktualizacje plików projektu, takie jak wieloetapowe dockerfile, csproj Files lub jakakolwiek część wykresu Helm, wymagają odbudowania i ponownego wdrożenia kontenera aplikacji. Po zsynchronizowaniu jednego z tych plików z miejscem deweloperskim kontroler uruchamia polecenie [uaktualnienia Helm][helm-upgrade] , a kontener aplikacji jest ponownie kompilowany i wdrażany.

## <a name="file-synchronization"></a>Synchronizacja plików

Przy pierwszym uruchomieniu aplikacji w obszarze deweloperskim są przekazywane wszystkie pliki źródłowe aplikacji. Gdy aplikacja jest uruchomiona i przy późniejszym ponownym uruchomieniu, zostaną przekazane tylko zmienione pliki. Dwa pliki są używane do koordynowania tego procesu: plik po stronie klienta i plik po stronie kontrolera.

Plik po stronie klienta jest przechowywany w katalogu tymczasowym i nosi nazwę na podstawie skrótu katalogu projektu, który jest używany w miejscach programistycznych. Na przykład w systemie Windows istnieje plik, taki jak *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* dla projektu. W systemie Linux plik po stronie klienta jest przechowywany w katalogu */tmp* . Katalog można znaleźć w witrynie macOS, uruchamiając `echo $TMPDIR` polecenie.

Ten plik jest w formacie JSON i zawiera:

* Wpis dla każdego pliku projektu, który jest synchronizowany z przestrzenią dev
* Identyfikator synchronizacji
* Sygnatura czasowa ostatniej operacji synchronizacji

Każdy wpis pliku projektu zawiera ścieżkę do pliku i jego sygnaturę czasową.

Plik po stronie kontrolera jest przechowywany w klastrze AKS. Zawiera identyfikator synchronizacji oraz sygnaturę czasową ostatniej synchronizacji.

Synchronizacja występuje, gdy sygnatury czasowe synchronizacji nie są zgodne między plikami po stronie klienta i po stronie kontrolera. Podczas synchronizacji narzędzie po stronie klienta wykonuje iterację nad wpisami plików w pliku po stronie klienta. Jeśli sygnatura czasowa pliku jest po sygnaturze czasowej synchronizacji, ten plik jest synchronizowany do obszaru dev. Po zakończeniu synchronizacji sygnatury czasowe synchronizacji są aktualizowane zarówno po stronie klienta, jak i na plikach po stronie kontrolera.

Wszystkie pliki projektu są synchronizowane, jeśli plik po stronie klienta nie istnieje. Takie zachowanie umożliwia wymuszenie pełnej synchronizacji przez usunięcie pliku po stronie klienta.

## <a name="how-running-your-code-is-configured"></a>Jak jest konfigurowany kod

Azure Dev Spaces używa `azds.yaml` pliku do zainstalowania i skonfigurowania usługi. Kontroler używa `install` właściwości w `azds.yaml` pliku do zainstalowania wykresu Helm i utworzenia obiektów Kubernetes:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Domyślnie `prep` polecenie spowoduje wygenerowanie wykresu Helm. Ustawia również właściwość *install. Chart* na katalog wykresu Helm. Jeśli chcesz użyć wykresu Helm w innej lokalizacji, możesz zaktualizować tę właściwość, aby użyć tej lokalizacji.

Podczas instalowania wykresów Helm Azure Dev Spaces zapewnia sposób przesłaniania wartości na wykresie Helm. Wartości domyślne dla wykresu Helm są w `charts/APP_NAME/values.yaml` .

Za pomocą właściwości *install. Values* można wyświetlić jeden lub więcej plików, które definiują wartości, które mają zostać zastąpione na wykresie Helm. Na przykład w przypadku wybrania nazwy hosta lub bazy danych, która jest używana w specjalnym czasie podczas uruchamiania aplikacji w obszarze dev, można użyć tej funkcji przesłonięcia. Możesz również dodać *?* na końcu dowolnych nazw plików, aby ustawić je jako opcjonalne.

Właściwość *install. Set* umożliwia skonfigurowanie co najmniej jednej wartości, która ma zostać zastąpiona na wykresie Helm. Wszystkie wartości skonfigurowane w ramach *instalacji. Set* przesłonią wartości skonfigurowane w plikach wymienionych w pliku *install. Values*. Właściwości w obszarze *install. Set* są zależne od wartości na wykresie Helm i mogą się różnić w zależności od wygenerowanego wykresu Helm.

W powyższym przykładzie właściwość *install. Set. replicaCount* informuje kontroler, ile wystąpień aplikacji ma działać w miejscu dev. W zależności od danego scenariusza można zwiększyć tę wartość, ale będzie to miało wpływ na dołączenie debugera do poziomu aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł dotyczący rozwiązywania problemów][troubleshooting].

Na wygenerowanym wykresie Helm obraz kontenera jest ustawiany na *{{. Values. Image. Repository}}: {{. Values. Image. tag}}*. `azds.yaml`Plik definiuje domyślnie Właściwość *install. Set. Image. tag* jako *$ (tag)* , która jest używana jako wartość dla *{{. Values. Image. tag}}*. Ustawiając właściwość *install. Set. Image. tag* w ten sposób, umożliwia ona oznakowanie obrazu kontenera dla aplikacji w sposób odrębny podczas uruchamiania Azure dev Spaces. W tym konkretnym przypadku obraz jest otagowany jako *\<value from image.repository> : $ (tag)*. Należy użyć zmiennej *$ (tag)* jako wartości   *install. Set. Image. tag* , aby funkcja Spaces dev rozpoznaje i ZLOKALIZOWAĆ kontener w klastrze AKS.

W powyższym przykładzie `azds.yaml` definiuje *install. Set.* Transfers. Hosts. Właściwość *install. Set. Ingress. hosts* definiuje format nazwy hosta dla publicznych punktów końcowych. Ta właściwość używa również wartości *$ (spacePrefix)*, *$ (rootSpacePrefix)* i *$ (hostSuffix)*, które są wartościami dostarczonymi przez kontroler.

*$ (SpacePrefix)* to nazwa podrzędnego miejsca dev, która przyjmuje postać *spacename. s*. *$ (RootSpacePrefix)* to nazwa przestrzeni nadrzędnej. Na przykład jeśli *azureuser* jest obszarem podrzędnym *domyślnym*, wartość *$ (rootSpacePrefix)* jest *Domyślna* , a wartość *$ (spacePrefix)* to *azureuser. s*. Jeśli przestrzeń nie jest przestrzenią podrzędną, *$ (spacePrefix)* jest pusty. Na przykład, jeśli *domyślne* miejsce nie ma miejsca nadrzędnego, wartość *$ (rootSpacePrefix)* jest *Domyślna* i wartość *$ (spacePrefix)* jest pusta. *$ (HostSuffix)* to sufiks DNS wskazujący Azure dev Spaces kontroler transferu danych przychodzących, który działa w klastrze AKS. Ten sufiks DNS odpowiada wpisowi DNS z symbolem wieloznacznym, na przykład *\* . RANDOM_VALUE. EUS. azds. IO*, który został utworzony, gdy kontroler Azure dev Spaces został dodany do klastra AKS.

W powyższym `azds.yaml` pliku można także zaktualizować *install. Set.* Transfers. hosts, aby zmienić nazwę hosta aplikacji. Na przykład jeśli chcesz uprościć nazwę hosta aplikacji z *$ (spacePrefix) $ (rootSpacePrefix) webfrontonu $ (hostSuffix)* do *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix)*.

Aby skompilować kontener dla aplikacji, kontroler używa poniższych sekcji w `azds.yaml` pliku konfiguracji:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Kontroler używa pliku dockerfile do kompilowania i uruchamiania aplikacji.

Właściwość *Build. Context* zawiera listę katalogów, w których istnieje wieloetapowe dockerfile. Właściwość *build.dockerfile* definiuje nazwę pliku dockerfile do tworzenia wersji produkcyjnej aplikacji. Właściwość *configurations.develop.build.dockerfile* konfiguruje nazwę pliku dockerfile dla deweloperskiej wersji aplikacji.

Posiadanie różnych wieloetapowe dockerfile na potrzeby programowania i produkcji pozwala na włączenie pewnych rzeczy podczas opracowywania i wyłączenie tych elementów dla wdrożeń produkcyjnych. Na przykład można włączyć debugowanie lub więcej pełnych rejestrowania podczas opracowywania i wyłączyć w środowisku produkcyjnym. Możesz również zaktualizować te właściwości, jeśli wieloetapowe dockerfile są nazwane inaczej lub znajdują się w innej lokalizacji.

Aby ułatwić szybkie Iterowanie podczas opracowywania, Azure Dev Spaces zsynchronizuje zmiany z projektu lokalnego i przyrostowo aktualizuje aplikację. Poniższa sekcja w `azds.yaml` pliku konfiguracyjnym służy do konfigurowania synchronizacji i aktualizacji:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Pliki i katalogi, które będą synchronizowane ze zmianami, są wymienione w właściwości *Configurations. opracowywać. Container. Sync* . Te katalogi są wstępnie synchronizowane po uruchomieniu `up` polecenia, jak również w przypadku wykrycia zmian. Jeśli istnieją dodatkowe lub różne katalogi, które chcesz synchronizować z obszarem deweloperskim, możesz zmienić tę właściwość.

Właściwość *Configuration. Development. Container. ITER. buildCommands* określa sposób kompilowania aplikacji w scenariuszu programistycznym. Właściwość *Configurations. Development. Container. Command* udostępnia polecenie uruchamiania aplikacji w scenariuszu programistycznym. Możesz chcieć zaktualizować jedną z tych właściwości, jeśli istnieją dodatkowe flagi kompilacji lub lub parametry środowiska uruchomieniowego, które mają być używane podczas opracowywania.

*Konfiguracja. programowanie. Container. ITER. processesToKill* wyświetla listę procesów, które mają zostać zabicia, aby zatrzymać aplikację. Możesz chcieć zaktualizować tę właściwość, jeśli chcesz zmienić zachowanie ponownego uruchamiania aplikacji podczas opracowywania. Na przykład, jeśli zaktualizowano *konfiguracje. programowanie. Container. ITER. buildCommands* lub *Configurations. programowanie. Container. Command* właściwości aby zmienić sposób tworzenia lub uruchamiania aplikacji, może zajść potrzeba zmiany procesów, które są zatrzymane.

Podczas przygotowywania kodu za pomocą `azds prep` polecenia możesz dodać `--enable-ingress` flagę. Dodanie `--enable-ingress` flagi powoduje utworzenie publicznie dostępnego adresu URL dla aplikacji. Jeśli pominięto tę flagę, aplikacja jest dostępna tylko w ramach klastra lub przy użyciu tunelu localhost. Po uruchomieniu `azds prep` polecenia można zmienić to ustawienie, modyfikując właściwość Transfered *. Enabled* w `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sieci i sposobu, w jaki są kierowane żądania do Azure Dev Spaces zobacz [jak działa Routing z Azure dev Spaces][how-it-works-routing].

Aby dowiedzieć się więcej na temat szybkiej iteracji i programowania za pomocą Kubernetes, zobacz, [jak mostek Kubernetes działa][how-it-works-bridge-to-kubernetes] i [w jaki sposób zdalne debugowanie kodu za pomocą Azure dev Spaces działa][how-it-works-remote-debugging].


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md