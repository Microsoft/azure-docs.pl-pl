---
title: Aktualizacje obrazu podstawowego — zadania
description: Dowiedz się więcej o obrazach podstawowych obrazów kontenerów aplikacji oraz o tym, jak aktualizacja obrazu podstawowego może wyzwolić Azure Container Registry zadania.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: fc501cc1db654c11675e5a4f0d19a5a56b63a165
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781185"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Informacje o aktualizacjach obrazu podstawowego dla zadania usługi ACR

Ten artykuł zawiera podstawowe informacje o aktualizacjach obrazu podstawowego aplikacji oraz o tym, jak te aktualizacje mogą wyzwalać Azure Container Registry zadania.

## <a name="what-are-base-images"></a>Co to są obrazy podstawowe?

Pliki Dockerfile definiujące większość obrazów kontenerów określają obraz nadrzędny, na którym jest oparty obraz, często określany jako *obraz podstawowy*. Obrazy podstawowe przeważnie zawierają system operacyjny, na przykład [Alpine Linux][base-alpine] lub [Windows Nano Server][base-windows], w którym są stosowane pozostałe warstwy kontenera. Mogą również zawierać platformy aplikacji, takie jak [Node.js][base-node] lub [.NET Core][base-dotnet]. Te obrazy podstawowe są zwykle oparte na publicznych obrazach nadrzędnych. Kilka obrazów aplikacji może współużytkować wspólny obraz podstawowy.

Obraz podstawowy jest często aktualizowany przez element utrzymujący obrazu w celu uwzględnienia nowych funkcji lub ulepszeń systemu operacyjnego lub platformy w obrazie. Poprawki zabezpieczeń są kolejną typową przyczyną aktualizacji obrazu podstawowego. W przypadku wystąpienia tych aktualizacji nadrzędnych należy również zaktualizować obrazy podstawowe, aby uwzględnić poprawkę krytyczną. Każdy obraz aplikacji również musi zostać ponownie skonsokowane, aby uwzględnić te poprawki nadrzędne, które są teraz zawarte w obrazie bazowym.

W niektórych przypadkach, takich jak prywatny zespół programistów, obraz podstawowy może określać więcej niż system operacyjny lub platformę. Na przykład obraz podstawowy może być obrazem składnika usługi udostępnionej, który musi być śledzony. Członkowie zespołu mogą potrzebować śledzenia tego obrazu podstawowego na potrzeby testowania lub regularnego aktualizowania obrazu podczas tworzenia obrazów aplikacji.

## <a name="maintain-copies-of-base-images"></a>Obsługa kopii obrazów podstawowych

W przypadku dowolnej zawartości w rejestrach, która zależy od zawartości podstawowej utrzymywanej w rejestrze publicznym, takim jak Docker Hub, zalecamy skopiowanie zawartości do rejestru kontenerów platformy Azure lub innego rejestru prywatnego. Następnie upewnij się, że tworzysz obrazy aplikacji, odwołując się do prywatnych obrazów podstawowych. Azure Container Registry umożliwia [importowanie obrazów](container-registry-import-images.md) w celu łatwego kopiowania zawartości z rejestrów publicznych lub innych rejestrów kontenerów platformy Azure. W następnej sekcji opisano używanie zadania usługi ACR do śledzenia aktualizacji obrazu podstawowego podczas tworzenia aktualizacji aplikacji. Aktualizacje obrazu podstawowego można śledzić we własnych rejestrach kontenerów platformy Azure i opcjonalnie w nadrzędnych rejestrach publicznych.

## <a name="track-base-image-updates"></a>Śledzenie aktualizacji obrazu podstawowego

Usługa ACR Tasks oferuje możliwość automatycznego kompilowania obrazów podczas aktualizacji obrazu podstawowego kontenera. Ta możliwość umożliwia konserwację i aktualizowanie kopii publicznych obrazów podstawowych w rejestrach kontenerów platformy Azure, a następnie ponowne kompilowanie obrazów aplikacji zależnych od obrazów podstawowych.

zadania usługi ACR dynamicznie odnajduje zależności obrazu podstawowego podczas kompilowania obrazu kontenera. Dzięki temu może wykryć, kiedy obraz podstawowy obrazu aplikacji jest aktualizowany. Za pomocą jednego wstępnie skonfigurowanego zadania kompilacji zadania usługi ACR automatycznie odbudować każdy obraz aplikacji, który odwołuje się do obrazu podstawowego. Dzięki temu automatycznemu wykrywaniu i ponownemu zadania usługi ACR oszczędza czas i nakład pracy zwykle wymagany do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji odwołującego się do zaktualizowanego obrazu podstawowego.

## <a name="base-image-locations"></a>Lokalizacje obrazów podstawowych

W przypadku kompilacji obrazów z pliku Dockerfile zadanie usługi ACR wykrywa zależności od obrazów podstawowych w następujących lokalizacjach:

* Ten sam rejestr kontenerów platformy Azure, w którym jest uruchamiane zadanie
* Inny prywatny rejestr kontenerów platformy Azure w tym samym lub innym regionie 
* Publiczne repo w Docker Hub 
* Publiczne repo w Microsoft Container Registry

Jeśli obraz podstawowy określony w instrukcji znajduje się w jednej z tych lokalizacji, zadanie usługi ACR dodaje zaczepienie, aby upewnić się, że obraz jest ponownie budować za każdym razem, gdy jego baza `FROM` jest aktualizowana.

## <a name="base-image-notifications"></a>Powiadomienia obrazu podstawowego

Czas między zaktualizowaniem obrazu podstawowego a wyzwoleniem zadania zależnego zależy od lokalizacji obrazu podstawowego:

* **Obrazy** podstawowe z repozytorium publicznego w programie Docker Hub lub MCR — w przypadku obrazów podstawowych w repozytoriach publicznych zadanie ACR sprawdza aktualizacje obrazów w losowym interwale od 10 do 60 minut. Zadania zależne są odpowiednio uruchamiane.
* **Obrazy podstawowe z rejestru kontenerów** platformy Azure — w przypadku obrazów podstawowych w rejestrach kontenerów platformy Azure zadanie usługi ACR natychmiast wyzwala uruchomienie po zaktualizowania obrazu podstawowego. Obraz podstawowy może być w tym samym acr gdzie zadanie jest uruchamiane lub w innym ACR w dowolnym regionie.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

* **Obrazy podstawowe obrazów aplikacji** — obecnie zadanie usługi ACR śledzi tylko podstawowe aktualizacje obrazów dla obrazów aplikacji *(środowiska uruchomieniowego).* Nie śledzi aktualizacji obrazu podstawowego dla obrazów pośrednich *(buildtime)* używanych w wieloetapowych plikach Dockerfile.  

* **Domyślnie włączone** — podczas tworzenia zadania usługi ACR za pomocą polecenia az  [acr task create][az-acr-task-create] zadanie jest domyślnie włączone dla wyzwalacza przez aktualizację obrazu podstawowego. Oznacza to, że `base-image-trigger-enabled` właściwość jest ustawiona na wartość True. Jeśli chcesz wyłączyć to zachowanie w zadaniu, zaktualizuj właściwość do wartości False. Na przykład uruchom następujące polecenie [az acr task update:][az-acr-task-update]

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* Wyzwalacz **umożliwiający śledzenie** zależności — aby umożliwić zadaniu ACR określenie i śledzenie zależności obrazu kontenera , które obejmują jego obraz podstawowy, należy najpierw wyzwolić zadanie w celu skompilowania obrazu co **najmniej raz.** Na przykład wyzwolić zadanie ręcznie za pomocą [polecenia az acr task run.][az-acr-task-run]

* **Tag stabilny dla obrazu podstawowego** — aby wyzwolić zadanie przy aktualizacji obrazu podstawowego, obraz podstawowy musi mieć tag stabilny, taki jak  `node:9-alpine` . To tagowanie jest typowe dla obrazu podstawowego, który został zaktualizowany o poprawki systemu operacyjnego i struktury do najnowszej stabilnej wersji. Jeśli obraz podstawowy zostanie zaktualizowany przy użyciu nowego tagu wersji, nie wyzwoli zadania. Aby uzyskać więcej informacji na temat tagowania obrazów, zobacz [wskazówki dotyczące najlepszych rozwiązań.](container-registry-image-tag-version.md) 

* **Inne wyzwalacze zadań —** w zadaniu wyzwalane przez aktualizacje obrazu podstawowego można również włączyć wyzwalacze na podstawie zatwierdzenia kodu źródłowego [lub](container-registry-tutorial-build-task.md) [harmonogramu](container-registry-tasks-scheduled.md). Aktualizacja obrazu podstawowego może również wyzwolić [wieloetapowe zadanie](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące samouczki dotyczące scenariuszy automatyzowania kompilacji obrazu aplikacji po zaktualizowaniu obrazu podstawowego:

* [Automatyzowanie kompilacji obrazu kontenera podczas aktualizowania obrazu podstawowego w tym samym rejestrze](container-registry-tutorial-base-image-update.md)

* [Automatyzowanie kompilacji obrazu kontenera podczas aktualizowania obrazu podstawowego w innym rejestrze](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
