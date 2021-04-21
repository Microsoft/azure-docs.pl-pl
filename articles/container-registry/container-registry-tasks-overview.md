---
title: Omówienie zadań usługi ACR
description: Wprowadzenie do zadania usługi ACR, zestawu funkcji w usłudze Azure Container Registry, które zapewniają bezpieczne, zautomatyzowane kompilowanie obrazu kontenera, zarządzanie i stosowanie poprawek w chmurze.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: a42a2bfcdc1621689421940c4db2fcf4f5e64b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781005"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatyzowanie kompilacji i konserwacji obrazu kontenera za pomocą zadania usługi ACR

Kontenery zapewniają nowe poziomy wirtualizacji, izolując zależności aplikacji i deweloperów od wymagań dotyczących infrastruktury i działania. Pozostaje jednak potrzeba rozwiązania tego, jak ta wirtualizacja aplikacji jest zarządzana i poprawiana w całym cyklu życia kontenera.

## <a name="what-is-acr-tasks"></a>Co to jest zadania usługi ACR?

**zadania usługi ACR** to zestaw funkcji w Azure Container Registry. Zapewnia ona oparte na chmurze tworzenie obrazów kontenerów dla platform, w tym Linux, Windows i ARM, oraz może automatyzować stosowanie poprawek systemu operacyjnego i struktury dla kontenerów platformy Docker. [](#image-platforms) [](#automate-os-and-framework-patching) zadania usługi ACR nie tylko rozszerza cykl tworzenia "pętli wewnętrznej" na chmurę przy użyciu kompilacji obrazu kontenera na żądanie, ale także umożliwia automatyczne kompilacje wyzwalane przez aktualizacje kodu źródłowego, aktualizacje obrazu podstawowego kontenera lub czasomierze. Na przykład przy użyciu wyzwalaczy aktualizacji obrazu podstawowego można zautomatyzować przepływ pracy poprawiania systemu operacyjnego i struktury aplikacji, zachowując bezpieczne środowiska przy zachowaniu zasad kontenerów niezmiennych.

## <a name="task-scenarios"></a>Scenariusze zadań

zadania usługi ACR obsługuje kilka scenariuszy tworzenia i obsługi obrazów kontenerów i innych artefaktów. Szczegółowe informacje można znaleźć w poniższych sekcjach tego artykułu.

* **[Szybkie zadanie](#quick-task)** — Kompilowanie i wypychanie pojedynczego obrazu kontenera do rejestru kontenerów na żądanie na platformie Azure bez konieczności instalowania lokalnego aparatu platformy Docker. Pomyśl `docker build` , `docker push` w chmurze.
* **Automatycznie wyzwalane zadania —** włącz co najmniej jeden *wyzwalacz w* celu skompilowania obrazu:
  * **[Wyzwalacz po aktualizacji kodu źródłowego](#trigger-task-on-source-code-update)** 
  * **[Wyzwalanie przy aktualizacji obrazu podstawowego](#automate-os-and-framework-patching)** 
  * **[Wyzwalanie zgodnie z harmonogramem](#schedule-a-task)** 
* **[Zadanie wieloetapowe](#multi-step-tasks)** — rozszerzanie możliwości kompilowania i wypychania pojedynczego obrazu zadania usługi ACR wieloetapowych przepływów pracy opartych na wielu kontenerach. 

Każde zadanie usługi ACR ma skojarzony kontekst kodu [źródłowego](#context-locations) — lokalizację zestawu plików źródłowych używanych do kompilowania obrazu kontenera lub innego artefaktu. Przykładowe konteksty obejmują repozytorium Git lub lokalny system plików.

Zadania mogą również korzystać ze [zmiennych](container-registry-tasks-reference-yaml.md#run-variables)uruchamiania , dzięki czemu można ponownie używać definicji zadań i standaryzować tagi dla obrazów i artefaktów.

## <a name="quick-task"></a>Szybkie zadanie

Cykl tworzenia pętli wewnętrznej, iteracyjny proces pisania kodu, budowania i testowania aplikacji przed zatwierdzeniem do kontroli źródła, to tak naprawdę początek zarządzania cyklem życia kontenera.

Przed zatwierdzeniem pierwszego wiersza kodu funkcja szybkiego [](container-registry-tutorial-quick-task.md) zadania usługi ACR może zapewnić zintegrowane środowisko programowe przez odciążenie kompilacji obrazu kontenera na platformę Azure. Za pomocą szybkich zadań można zweryfikować definicje zautomatyzowanej kompilacji i wychwytują potencjalne problemy przed zatwierdzeniem kodu.

Przy użyciu znanego formatu polecenie az acr build w interfejsie wiersza polecenia platformy Azure przyjmuje kontekst (zestaw plików do skompilowania), wysyła go do usługi zadania usługi ACR i domyślnie wypycha skompilowany obraz do swojego rejestru po `docker build` zakończeniu. [][az-acr-build] [](#context-locations)

Aby uzyskać wprowadzenie, zobacz przewodnik Szybki start, aby [skompilować i uruchomić obraz kontenera w](container-registry-quickstart-task-cli.md) Azure Container Registry.  

zadania usługi ACR jest zaprojektowany jako prymityw cyklu życia kontenera. Na przykład zintegruj zadania usługi ACR z rozwiązaniem ci/CD. Wykonując polecenie [az login z][az-login] jednostką usługi, rozwiązanie ci/CD może następnie wytworzyć polecenia az [acr build,][az-acr-build] aby rozpoczynać kompilacje obrazów. [][az-login-service-principal]

Dowiedz się, jak używać szybkich zadań w pierwszym samouczku zadania usługi ACR, Build container images in the cloud with zadania usługi Azure Container Registry (Tworzenie obrazów kontenerów w chmurze za [pomocą zadania usługi Azure Container Registry).](container-registry-tutorial-quick-task.md)

> [!TIP]
> Jeśli chcesz skompilować i wypchnąć obraz bezpośrednio z kodu źródłowego bez pliku Dockerfile, Azure Container Registry polecenie [az acr pack build][az-acr-pack-build] (wersja zapoznawcza). To narzędzie tworzy i wypycha obraz z kodu źródłowego aplikacji przy użyciu pakietów [kompilacji natywnych dla chmury.](https://buildpacks.io/)

## <a name="trigger-task-on-source-code-update"></a>Wyzwalanie zadania przy aktualizacji kodu źródłowego

Wyzwalanie kompilacji obrazu kontenera lub zadania wieloetapowego w przypadku zatwierdzona kodu lub wykonania lub zaktualizowania żądania ściągnnięcia do publicznego lub prywatnego repozytorium Git w usłudze GitHub lub Azure DevOps. Na przykład skonfiguruj zadanie kompilacji za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az acr task create,][az-acr-task-create] określając repozytorium Git oraz opcjonalnie gałąź i plik Dockerfile. Gdy zespół aktualizuje kod w repozytorium, utworzony zadania usługi ACR webhook wyzwala kompilację obrazu kontenera zdefiniowanego w repozytorium. 

zadania usługi ACR obsługuje następujące wyzwalacze po skonfigurowaniu repozytorium Git jako kontekstu zadania:

| Wyzwalacz | Domyślnie włączone |
| ------- | ------------------ |
| Zatwierdzenie | Tak |
| Żądanie ściągnięcia | Nie |

Aby skonfigurować wyzwalacz aktualizacji kodu źródłowego, należy podać zadaniu osobisty token dostępu , aby ustawić ten obiekt webhook w publicznym lub prywatnym repozytorium GitHub lub Azure DevOps repozytorium.

> [!NOTE]
> Obecnie zadania usługi ACR nie obsługuje wyzwalaczy zatwierdzeń ani żądań ściągnięć w GitHub Enterprise żądaniami.

Dowiedz się, jak wyzwalać kompilacje po zatwierdzeniu kodu źródłowego, zadania usługi ACR samouczku Automatyzowanie kompilacji obrazu kontenera za [pomocą zadania usługi Azure Container Registry.](container-registry-tutorial-build-task.md)

## <a name="automate-os-and-framework-patching"></a>Automatyzowanie poprawek systemu operacyjnego i struktury

Możliwość zadania usługi ACR przepływu pracy kompilacji kontenera wynika z możliwości wykrywania aktualizacji obrazu *podstawowego.* Funkcja większości obrazów kontenerów, obraz podstawowy jest obrazem nadrzędnym, na którym bazuje co najmniej jeden obraz aplikacji. Obrazy podstawowe zwykle zawierają system operacyjny, a czasami struktury aplikacji. 

Zadanie ACR można skonfigurować w celu śledzenia zależności od obrazu podstawowego podczas kompilowania obrazu aplikacji. Gdy zaktualizowany obraz podstawowy jest wypychany do rejestru lub obraz podstawowy jest aktualizowany w publicznym repo, takim jak w programie Docker Hub, program zadania usługi ACR automatycznie skompilować dowolne obrazy aplikacji na jego podstawie.
Dzięki temu automatycznemu wykrywaniu i ponownemu kompilowaniu zadania usługi ACR zaoszczędzić czas i nakład pracy zwykle wymagany do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji odwołującego się do zaktualizowanego obrazu podstawowego.

Dowiedz się więcej o [wyzwalaczach aktualizacji obrazu podstawowego](container-registry-tasks-base-images.md) dla zadania usługi ACR. Dowiedz się, jak wyzwolić kompilację obrazu, gdy obraz podstawowy zostanie wypchnięty do rejestru kontenerów, korzystając z samouczka Automatyzowanie kompilacji obrazu kontenera po zaktualizowaniu obrazu podstawowego w rejestrze [kontenerów platformy Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Planowanie zadania

Opcjonalnie zaplanuj zadanie przez skonfigurowanie co najmniej jednego wyzwalacza *czasomierza* podczas jego tworzenia lub aktualizowania. Planowanie zadania jest przydatne w przypadku uruchamiania obciążeń kontenerów zgodnie ze zdefiniowanym harmonogramem lub uruchamiania operacji konserwacji lub testów obrazów wypychanych regularnie do rejestru. Aby uzyskać szczegółowe informacje, [zobacz Uruchamianie zadania ACR zgodnie ze zdefiniowanym harmonogramem](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Zadania wieloetapowe

Zadania wieloetapowe zapewniają oparte na krokach definicje zadań i ich wykonywanie w celu tworzenia, testowania i poprawiania obrazów kontenerów w chmurze. Kroki zadania zdefiniowane w pliku [YAML określają](container-registry-tasks-reference-yaml.md) pojedyncze operacje kompilacji i wypychania dla obrazów kontenerów lub innych artefaktów. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

Można na przykład utworzyć zadanie wieloetapowe, które automatyzuje następujące czynności:

1. Tworzenie obrazu aplikacji internetowej
1. Uruchamianie kontenera aplikacji internetowej
1. Tworzenie obrazu testowego aplikacji internetowej
1. Uruchamianie kontenera testowego aplikacji internetowej, który wykonuje testy względem uruchomionego kontenera aplikacji
1. Jeśli testy przebiegną, skompilowanie pakietu archiwum helm chart
1. Wykonywanie operacji `helm upgrade` przy użyciu nowego pakietu archiwum helm chart

Zadania wieloetapowe umożliwiają podzielenie budynku, uruchomienia i testowania obrazu na bardziej skonfigurowalne kroki z obsługą zależności między krokami. Dzięki wieloetapowym zadania usługi ACR zapewniasz bardziej szczegółową kontrolę nad przepływami pracy tworzenia, testowania i poprawiania struktury oraz tworzenia i testowania obrazów.

Dowiedz się więcej o zadaniach wieloetapowych w [teście Run multi-step build, test, and patch tasks in zadania usługi ACR](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Lokalizacje kontekstowe

W poniższej tabeli przedstawiono przykłady obsługiwanych lokalizacji kontekstu dla zadania usługi ACR:

| Lokalizacja kontekstu | Opis | Przykład |
| ---------------- | ----------- | ------- |
| Lokalny system plików | Pliki w katalogu w lokalnym systemie plików. | `/home/user/projects/myapp` |
| Gałąź główna usługi GitHub | Pliki w głównej (lub innej domyślnej) gałęzi publicznego lub prywatnego repozytorium GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Gałąź GitHub | Konkludna gałąź publicznego lub prywatnego repozytorium GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Podfolder usługi GitHub | Pliki w podfolderze w publicznym lub prywatnym repozytorium GitHub. Przykład przedstawia kombinację specyfikacji gałęzi i podfolderu. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Zatwierdzenie w usłudze GitHub | Określone zatwierdzenie w publicznym lub prywatnym repozytorium GitHub. Przykład przedstawia kombinację specyfikacji skrótu zatwierdzenia (SHA) i podfolderu. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps podfolder | Pliki w podfolderze w publicznym lub prywatnym repocie platformy Azure. Przykład przedstawia kombinację specyfikacji gałęzi i podfolderu. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Zdalny tarball | Pliki w skompresowanym archiwum na zdalnym serwerze sieci Web. | `http://remoteserver/myapp.tar.gz` |
| Artefakt w rejestrze kontenerów | [Pliki artefaktów OCI](container-registry-oci-artifacts.md) w repozytorium rejestru kontenerów. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> W przypadku używania prywatnego repozytorium Git jako kontekstu zadania należy podać osobisty token dostępu.

## <a name="image-platforms"></a>Platformy obrazów

Domyślnie program zadania usługi ACR obrazy dla systemu operacyjnego Linux i architektury amd64. Określ `--platform` tag do kompilowania obrazów systemu Windows lub obrazów systemu Linux dla innych architektur. Określ system operacyjny i opcjonalnie obsługiwaną architekturę w formacie system operacyjny/architektura (na przykład `--platform Linux/arm` ). W przypadku architektur ARM opcjonalnie określ wariant w formacie system operacyjny/architektura/wariant (na przykład `--platform Linux/arm64/v8` ):

| System operacyjny | Architektura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Każdy przebieg zadania generuje dane wyjściowe dziennika, które można sprawdzić, aby ustalić, czy kroki zadania zostały pomyślnie uruchomione. Po ręcznym wyzwoleniu zadania dane wyjściowe dziennika dla uruchomienia zadania są przesyłane strumieniowo do konsoli i przechowywane w celu późniejszego pobrania. Gdy zadanie jest wyzwalane automatycznie, na przykład przez zatwierdzenie kodu źródłowego lub aktualizację obrazu podstawowego, dzienniki zadań są przechowywane tylko. Wyświetl dzienniki uruchamiania w Azure Portal lub użyj [polecenia az acr task logs.](/cli/azure/acr/task#az_acr_task_logs)

Zobacz więcej informacji na [temat wyświetlania dzienników zadań i zarządzania nimi.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>Następne kroki

Gdy wszystko będzie gotowe do zautomatyzowania kompilacji i konserwacji obrazu kontenera w chmurze, zapoznaj się z [zadania usługi ACR samouczka.](container-registry-tutorial-quick-task.md)

Opcjonalnie zainstaluj rozszerzenie [docker for Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie konta platformy [Azure,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) aby współpracować z rejestrami kontenerów platformy Azure. Ściąganie i wypychanie obrazów do rejestru kontenerów platformy Azure lub uruchamianie zadania usługi ACR w Visual Studio Code.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
