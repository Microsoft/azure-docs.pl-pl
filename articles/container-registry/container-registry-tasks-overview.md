---
title: Omówienie zadań usługi ACR
description: Wprowadzenie do ACR zadań, zestaw funkcji w Azure Container Registry, który zapewnia bezpieczną, zautomatyzowaną kompilację, zarządzanie i stosowanie poprawek w chmurze.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: b6df415bd55979ef00f6921321dbc254ef7a7e59
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562858"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatyzowanie kompilacji i konserwacji obrazów kontenerów za pomocą zadań ACR

Kontenery zapewniają nowe poziomy wirtualizacji, izolowanie zależności aplikacji i deweloperów od wymagań dotyczących infrastruktury i działania. Jest to jednak potrzeba, aby zająć się tym, w jaki sposób ta Wirtualizacja aplikacji jest zarządzana i poprawiana przez cykl życia kontenera.

## <a name="what-is-acr-tasks"></a>Co to są zadania ACR?

**ACR Tasks** to zestaw funkcji w ramach Azure Container Registry. Zapewnia to Kompilowanie obrazów kontenerów opartych na chmurze dla [platform](#image-platforms) , w tym Linux, Windows i ARM, i umożliwia automatyzację [poprawek systemu operacyjnego i struktury](#automate-os-and-framework-patching) dla kontenerów platformy Docker. ACR zadania nie tylko rozszerzają cykl programowania "pętla wewnętrzna" do chmury z kompilacjami obrazu kontenera na żądanie, ale również włącza zautomatyzowane kompilacje wyzwalane przez aktualizacje kodu źródłowego, aktualizacje obrazu podstawowego kontenera lub czasomierze. Na przykład w przypadku wyzwalaczy aktualizacji obrazu podstawowego można zautomatyzować przepływ pracy z poprawkami systemu operacyjnego i aplikacji, zachowując bezpieczne środowiska, jednocześnie przestrzegając zasad niezmiennych kontenerów.

## <a name="task-scenarios"></a>Scenariusze zadań

Zadania ACR obsługują kilka scenariuszy, które umożliwiają kompilowanie i konserwowanie obrazów kontenerów i innych artefaktów. Aby uzyskać szczegółowe informacje, zobacz następujące sekcje w tym artykule.

* **[Szybkie zadanie](#quick-task)** — Kompilowanie i wypychanie pojedynczego obrazu kontenera do rejestru kontenerów na żądanie, na platformie Azure bez konieczności instalacji lokalnego aparatu platformy Docker. Zastanów się `docker build` `docker push` w chmurze.
* **Automatycznie wyzwolone zadania** — włączenie co najmniej jednego *wyzwalacza* w celu skompilowania obrazu:
  * **[Wyzwalanie aktualizacji kodu źródłowego](#trigger-task-on-source-code-update)** 
  * **[Wyzwól aktualizację obrazu podstawowego](#automate-os-and-framework-patching)** 
  * **[Wyzwalanie według harmonogramu](#schedule-a-task)** 
* **[Zadanie wieloetapowe](#multi-step-tasks)** — pozwala zwiększyć możliwości tworzenia i wypychania pojedynczego obrazu z ACR zadań z wieloetapowymi przepływami pracy opartymi na wielu kontenerach. 

Każde zadanie ACR ma skojarzony [kontekst kodu źródłowego](#context-locations) — lokalizacja zestawu plików źródłowych służącego do kompilowania obrazu kontenera lub innego artefaktu. Przykładowe konteksty obejmują repozytorium Git lub lokalny system plików.

Zadania mogą również wykorzystywać [zmienne uruchamiania](container-registry-tasks-reference-yaml.md#run-variables), dzięki czemu można ponownie używać definicji zadań i standaryzacji tagów dla obrazów i artefaktów.

## <a name="quick-task"></a>Szybkie zadanie

Cykl programowania w pętli wewnętrznej, proces iteracyjny pisania kodu, kompilowania i testowania aplikacji przed zatwierdzeniem do kontroli źródła, to naprawdę początek zarządzania cyklem życia kontenera.

Przed zatwierdzeniem pierwszego wiersza kodu funkcja [zadanie szybkiego zadania](container-registry-tutorial-quick-task.md) ACR zadania może zapewnić zintegrowane środowisko programistyczne, odciążając kompilacje obrazu kontenera na platformie Azure. Za pomocą szybkich zadań można zweryfikować zautomatyzowane definicje kompilacji i wychwycić potencjalne problemy przed zatwierdzeniem kodu.

Przy użyciu znajomego `docker build` formatu polecenie [AZ ACR Build][az-acr-build] w interfejsie wiersza polecenia platformy Azure przyjmuje [kontekst](#context-locations) (zestaw plików do skompilowania), wysyła do niego zadania ACR i domyślnie wypychanie skompilowanego obrazu do rejestru po zakończeniu.

Aby zapoznać się z wprowadzeniem, zobacz Przewodnik Szybki Start dotyczący [kompilowania i uruchamiania obrazu kontenera](container-registry-quickstart-task-cli.md) w Azure Container Registry.  

Zadania ACR są zaprojektowane jako pierwotny cykl życia kontenera. Na przykład Zintegruj zadania ACR w rozwiązaniu CI/CD. Wykonanie polecenia [AZ login][az-login] z jednostką [usługi][az-login-service-principal]powoduje, że rozwiązanie Ci/CD może wydać polecenie [AZ ACR Build][az-acr-build] , aby uruchomić kompilacje obrazu.

Dowiedz się, jak używać szybkich zadań w pierwszym samouczku ACR Tasks, [tworzyć obrazy kontenerów w chmurze za pomocą zadań Azure Container Registry](container-registry-tutorial-quick-task.md).

> [!TIP]
> Jeśli chcesz skompilować i wypchnąć obraz bezpośrednio z kodu źródłowego bez pliku dockerfile, Azure Container Registry udostępnia polecenie [AZ ACR Pack Build][az-acr-pack-build] (wersja zapoznawcza). To narzędzie służy do kompilowania i wypychania obrazu z kodu źródłowego aplikacji przy użyciu [natywnych Buildpacks w chmurze](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Wyzwalanie zadania na aktualizacji kodu źródłowego

Wyzwalanie kompilacji obrazu kontenera lub zadania wieloetapowego, gdy kod jest zatwierdzany lub żądanie ściągnięcia jest tworzone lub aktualizowane do publicznego lub prywatnego repozytorium Git w usłudze GitHub lub Azure DevOps. Można na przykład skonfigurować zadanie kompilacji za pomocą polecenia interfejsu CLI platformy Azure [AZ ACR Task Create][az-acr-task-create] , określając repozytorium git i opcjonalnie gałąź i pliku dockerfile. Gdy Twój zespół zaktualizuje kod w repozytorium, ACR zadania — utworzony element webhook wyzwala kompilację obrazu kontenera zdefiniowanego w repozytorium. 

Zadania ACR obsługują następujące wyzwalacze podczas ustawiania repozytorium git jako kontekstu zadania:

| Wyzwalacz | Domyślnie włączone |
| ------- | ------------------ |
| Zatwierdzenie | Tak |
| Żądanie ściągnięcia | Nie |

Aby skonfigurować wyzwalacz aktualizacji kodu źródłowego, należy podać zadanie w osobistym tokenie dostępu (źródła), aby ustawić element webhook w publicznym lub prywatnym repozytorium GitHub lub DevOps Azure.

> [!NOTE]
> Obecnie zadania ACR nie obsługują wyzwalaczy żądań zatwierdzenia lub ściągnięcia w ramach repozytoriów Enterprise usługi GitHub.

Dowiedz się, jak wyzwolić kompilacje w ramach zatwierdzenia kodu źródłowego w drugim samouczku ACR zadania, [Automatyzuj kompilacje obrazów kontenerów za pomocą zadań Azure Container Registry](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatyzowanie stosowania poprawek systemu operacyjnego i platformy

Możliwości ACR zadania, aby naprawdę ulepszyć przepływ pracy kompilacji kontenera, pochodzą z możliwości wykrywania aktualizacji *obrazu podstawowego*. Funkcja większości obrazów kontenerów jest obrazem nadrzędnym, na którym jest oparty jeden lub więcej obrazów aplikacji. Obrazy podstawowe zwykle zawierają system operacyjny, a czasami struktury aplikacji. 

Można skonfigurować zadanie ACR, aby śledzić zależność od obrazu podstawowego podczas kompilowania obrazu aplikacji. Po wypchnięciu zaktualizowanego obrazu podstawowego do rejestru lub zaktualizowaniu obrazu podstawowego w repozytorium publicznym, takim jak w usłudze Docker Hub, ACR zadania mogą automatycznie kompilować wszystkie obrazy aplikacji na ich podstawie.
Dzięki temu automatycznemu wykrywaniu i odbudowywaniu ACR zadania oszczędzają czas i nakłady pracy, które są zwykle wymagane do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji, do którego odwołuje się zaktualizowany obraz podstawowy.

Dowiedz się więcej na temat [podstawowych wyzwalaczy aktualizacji obrazu](container-registry-tasks-base-images.md) dla zadań ACR. I Dowiedz się, jak wyzwolić kompilację obrazu, gdy podstawowy obraz jest wypychany do rejestru kontenerów w przewodniku [Automatyzowanie obrazu kontenera, gdy obraz podstawowy zostanie zaktualizowany w usłudze Azure Container Registry](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Planowanie zadania

Opcjonalnie można zaplanować zadanie przez skonfigurowanie co najmniej jednego *wyzwalacza czasomierza* podczas tworzenia lub aktualizowania zadania. Planowanie zadania jest przydatne do uruchamiania obciążeń kontenera zgodnie ze zdefiniowanym harmonogramem lub uruchamiania operacji konserwacji lub testów dla obrazów, które są regularnie wypychane do rejestru. Aby uzyskać szczegółowe informacje, zobacz [Uruchamianie zadania ACR zgodnie ze zdefiniowanym harmonogramem](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Zadania wieloetapowe

Wieloetapowe zadania zapewniają definicje zadań oparte na krokach i wykonywanie w celu tworzenia, testowania i poprawiania obrazów kontenerów w chmurze. Kroki zadania zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md) określają poszczególne operacje kompilacji i wypychania dla obrazów kontenerów lub innych artefaktów. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

Można na przykład utworzyć zadanie wieloetapowe, które automatyzuje następujące czynności:

1. Tworzenie obrazu aplikacji sieci Web
1. Uruchamianie kontenera aplikacji sieci Web
1. Kompilowanie obrazu testowego aplikacji sieci Web
1. Uruchom kontener testów aplikacji sieci Web, który wykonuje testy względem uruchomionego kontenera aplikacji
1. Jeśli testy zachodzą, Kompiluj pakiet archiwum wykresów Helm
1. Wykonaj `helm upgrade` przy użyciu nowego pakietu archiwum wykresów Helm

Zadania wieloetapowe umożliwiają podział kompilowania, uruchamiania i testowania obrazu na bardziej złożone etapy z obsługą zależności między etapami. W przypadku zadań wieloetapowych w zadaniach ACR masz bardziej szczegółową kontrolę nad tworzeniem i testowaniem obrazów oraz przepływami pracy w systemie operacyjnym i architekturą.

Więcej informacji o zadaniach wieloetapowych w [uruchamianiu wieloetapowych zadań kompilacji, testowania i poprawiania w zadaniach ACR](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Lokalizacje kontekstu

W poniższej tabeli przedstawiono przykłady obsługiwanych lokalizacji kontekstu dla zadań ACR:

| Lokalizacja kontekstu | Opis | Przykład |
| ---------------- | ----------- | ------- |
| Lokalny system plików | Pliki znajdujące się w katalogu w lokalnym systemie plików. | `/home/user/projects/myapp` |
| Główna gałąź usługi GitHub | Pliki znajdujące się w głównej (lub innej domyślnej) gałęzi w publicznym lub prywatnym repozytorium GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Gałąź GitHub | Określona gałąź publicznego lub prywatnego repozytorium GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Podfolder usługi GitHub | Pliki znajdujące się w podfolderze w publicznym lub prywatnym repozytorium GitHub. Przykład przedstawia kombinację specyfikacji gałęzi i podfolderu. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Zatwierdzenie w serwisie GitHub | Określone zatwierdzenie w publicznym lub prywatnym repozytorium GitHub. Przykład pokazuje kombinację wartości skrótu zatwierdzenia (SHA) i podfolderu. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Podfolder Azure DevOps | Pliki znajdujące się w podfolderze w publicznym lub prywatnym repozytorium platformy Azure. Przykład przedstawia kombinację specyfikacji gałęzi i podfolderów. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Plik tar zdalnego | Pliki skompresowanego Archiwum na zdalnym serwerze WebServer. | `http://remoteserver/myapp.tar.gz` |
| Artefakt w rejestrze kontenerów | Pliki [artefaktów OCI](container-registry-oci-artifacts.md) w repozytorium rejestru kontenerów. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> W przypadku korzystania z prywatnego repozytorium git jako kontekstu dla zadania należy podać osobisty token dostępu.

## <a name="image-platforms"></a>Platformy obrazów

Domyślnie zadania ACR kompilują obrazy dla systemu operacyjnego Linux i architektury amd64. Określ `--platform` tag do kompilowania obrazów systemu Windows lub dla innych architektur w systemie Linux. Określ system operacyjny i opcjonalnie obsługiwaną architekturę w formacie systemu operacyjnego/architektury (na przykład `--platform Linux/arm` ). W przypadku architektur ARM Opcjonalnie określ wariant w formacie OS/Architecture/Variant (na przykład `--platform Linux/arm64/v8` ):

| System operacyjny | Architektura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Każde uruchomienie zadania generuje dane wyjściowe dziennika, które można sprawdzić, aby określić, czy kroki zadania zostały wykonane pomyślnie. Gdy zadanie zostanie wyzwolone ręcznie, dane wyjściowe dziennika dla uruchomienia zadania są przesyłane strumieniowo do konsoli programu, a także przechowywane do późniejszego pobrania. Gdy zadanie jest automatycznie wyzwalane, na przykład przez zatwierdzenie kodu źródłowego lub aktualizację obrazu podstawowego, dzienniki zadań są przechowywane tylko. Wyświetl dzienniki uruchamiania w Azure Portal lub użyj polecenia [AZ ACR Task Logs](/cli/azure/acr/task#az-acr-task-logs) .

Zobacz więcej informacji na temat [wyświetlania dzienników zadań i zarządzania nimi](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Następne kroki

Gdy wszystko jest gotowe do automatyzowania kompilacji i konserwacji obrazów kontenerów w chmurze, zapoznaj się z [serią samouczka zadań ACR](container-registry-tutorial-quick-task.md).

Opcjonalnie zainstaluj [rozszerzenie Docker dla Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) , aby współpracowało z rejestrami kontenerów platformy Azure. Ściągaj i wypychanie obrazów do usługi Azure Container Registry lub uruchamiaj zadania ACR, w Visual Studio Code.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
