---
title: Aktualizacje obrazu podstawowego — zadania
description: Informacje o obrazach podstawowych dla obrazów kontenerów aplikacji oraz o sposobie wyzwalania przez aktualizację obrazu podstawowego zadania Azure Container Registry.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: df33096830cd7b34a288c38c105aff3610315337
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97707490"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Informacje o aktualizacjach obrazu podstawowego dla zadań ACR

Ten artykuł zawiera ogólne informacje dotyczące aktualizacji obrazu podstawowego aplikacji oraz sposobu, w jaki te aktualizacje mogą wyzwalać zadanie Azure Container Registry.

## <a name="what-are-base-images"></a>Co to są obrazy podstawowe?

Wieloetapowe dockerfile Definiowanie większości obrazów kontenerów określa obraz nadrzędny, na podstawie którego jest oparty obraz, często określany jako *obraz podstawowy*. Obrazy podstawowe przeważnie zawierają system operacyjny, na przykład [Alpine Linux][base-alpine] lub [Windows Nano Server][base-windows], w którym są stosowane pozostałe warstwy kontenera. Mogą również zawierać platformy aplikacji, takie jak [Node.js][base-node] lub [.NET Core][base-dotnet]. Te obrazy podstawowe są zwykle oparte na publicznych obrazach nadrzędnych. Kilka obrazów aplikacji może współużytkować wspólny obraz podstawowy.

Obraz podstawowy jest często aktualizowany przez element utrzymujący obrazu w celu uwzględnienia nowych funkcji lub ulepszeń systemu operacyjnego lub platformy w obrazie. Poprawki zabezpieczeń są kolejną typową przyczyną aktualizacji obrazu podstawowego. Gdy te aktualizacje nadrzędne wystąpią, należy również zaktualizować obrazy podstawowe w celu uwzględnienia poprawki krytycznej. Każdy obraz aplikacji należy również ponownie skompilować, aby uwzględnić te prefiksy nadrzędne teraz zawarte w obrazie podstawowym.

W niektórych przypadkach, takich jak prywatny zespół programistyczny, podstawowy obraz może określać więcej niż system operacyjny lub platformę. Na przykład obraz podstawowy może być obrazem składnika usługi udostępnionej, który musi być śledzony. Członkowie zespołu mogą potrzebować śledzenia tego podstawowego obrazu do testowania lub konieczności regularnego aktualizowania obrazu podczas tworzenia obrazów aplikacji.

## <a name="maintain-copies-of-base-images"></a>Zachowaj kopie obrazów podstawowych

Dla każdej zawartości w rejestrach, która zależy od zawartości podstawowej przechowywanej w rejestrze publicznym, takiej jak usługa Docker Hub, zalecamy skopiowanie zawartości do usługi Azure Container Registry lub innego rejestru prywatnego. Następnie upewnij się, że tworzysz obrazy aplikacji, odwołując się do prywatnych obrazów podstawowych. Azure Container Registry zapewnia możliwość [importowania obrazów](container-registry-import-images.md) w celu łatwego kopiowania zawartości z rejestrów publicznych lub innych rejestrów kontenerów platformy Azure. W następnej sekcji opisano użycie zadań ACR do śledzenia aktualizacji obrazów podstawowych podczas kompilowania aktualizacji aplikacji. Można śledzić aktualizacje obrazu podstawowego we własnych rejestrach kontenerów platformy Azure i opcjonalnie w rejestrach publicznych.

## <a name="track-base-image-updates"></a>Śledź aktualizacje obrazu podstawowego

Usługa ACR Tasks oferuje możliwość automatycznego kompilowania obrazów podczas aktualizacji obrazu podstawowego kontenera. Za pomocą tej możliwości można zachować i zaktualizować kopie publicznych obrazów podstawowych w rejestrach kontenerów platformy Azure, a następnie ponownie skompilować obrazy aplikacji, które są zależne od obrazów podstawowych.

Zadania ACR dynamicznie odnajdują zależności obrazu podstawowego podczas kompilacji obrazu kontenera. W związku z tym może wykryć, kiedy obraz podstawowy obrazu aplikacji zostanie zaktualizowany. Po jednym wstępnie skonfigurowanym zadaniu kompilacji zadania ACR mogą automatycznie odbudować każdy obraz aplikacji, który odwołuje się do obrazu podstawowego. Dzięki temu automatycznemu wykrywaniu i odbudowywaniu ACR zadania oszczędzają czas i nakłady pracy, które są zwykle wymagane do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji, do którego odwołuje się zaktualizowany obraz podstawowy.

## <a name="base-image-locations"></a>Podstawowe lokalizacje obrazu

W przypadku kompilacji obrazu z pliku dockerfile, zadanie ACR wykrywa zależności od obrazów podstawowych w następujących lokalizacjach:

* Ten sam rejestr kontenerów platformy Azure, w którym uruchomiono zadanie
* Inny prywatny rejestr kontenerów platformy Azure w tym samym lub innym regionie 
* Repozytorium publiczne w usłudze Docker Hub 
* Repozytorium publiczne w programie Microsoft Container Registry

Jeśli obraz podstawowy określony w `FROM` instrukcji znajduje się w jednej z tych lokalizacji, zadanie ACR dodaje punkt zaczepienia, aby upewnić się, że obraz został odbudowany w dowolnym momencie, gdy jego baza jest aktualizowana.

## <a name="base-image-notifications"></a>Powiadomienia obrazu podstawowego

Czas między aktualizacją obrazu podstawowego a wyzwalaniem zależnego zadania zależy od lokalizacji obrazu podstawowego:

* **Obrazy podstawowe z repozytorium publicznego w usłudze Docker Hub lub MCR** — dla obrazów podstawowych w repozytoriach publicznych, zadanie ACR sprawdza dostępność aktualizacji obrazów w losowym interwale wynoszącym od 10 do 60 minut. Zadania zależne są uruchamiane odpowiednio.
* **Obrazy podstawowe z usługi Azure Container Registry** — w przypadku obrazów bazowych w rejestrach kontenerów platformy Azure, zadanie ACR natychmiast wyzwala uruchomienie, gdy jego obraz podstawowy zostanie zaktualizowany. Obraz podstawowy może znajdować się w tym samym ACR, w którym zadanie działa lub w innym ACR w dowolnym regionie.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

* **Obrazy podstawowe dla obrazów aplikacji** — obecnie zadanie ACR śledzi jedynie podstawowe aktualizacje obrazu dla obrazów aplikacji (*środowiska uruchomieniowego*). Nie są śledzone aktualizacje obrazu podstawowego dla obrazów pośrednich (*buildtime*) używanych w wieloetapowym wieloetapowe dockerfile.  

* **Domyślnie włączone** — podczas tworzenia zadania ACR za pomocą polecenia [AZ ACR Task Create][az-acr-task-create] Domyślnie zadanie jest *włączone* dla wyzwalacza przez podstawową aktualizację obrazu. Oznacza to, że `base-image-trigger-enabled` Właściwość jest ustawiona na wartość true. Jeśli chcesz wyłączyć to zachowanie w zadaniu, zaktualizuj właściwość do wartości false. Na przykład uruchom następujące polecenie [AZ ACR Task Update][az-acr-task-update] :

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Wyzwalacz do śledzenia zależności** — aby włączyć zadanie ACR w celu ustalenia i śledzenia zależności obrazu kontenera — w tym obrazu podstawowego — należy najpierw wyzwolić zadanie, aby utworzyć obraz **co najmniej raz**. Na przykład Wyzwól zadanie ręcznie przy użyciu polecenia [AZ ACR Task Run][az-acr-task-run] .

* **Stabilny tag obrazu podstawowego** — aby wyzwolić zadanie w ramach aktualizacji obrazu podstawowego, podstawowy obraz musi mieć *stabilny* tag, taki jak `node:9-alpine` . Ten znakowanie jest typowe w przypadku obrazu podstawowego, który jest aktualizowany przy użyciu poprawek systemu operacyjnego i wersji Framework do najnowszej stabilnego wydania. Jeśli obraz podstawowy został zaktualizowany przy użyciu nowego tagu wersji, zadanie nie zostanie wyzwolone. Aby uzyskać więcej informacji na temat znakowania obrazu, zobacz [wskazówki dotyczące najlepszych](container-registry-image-tag-version.md)rozwiązań. 

* **Inne Wyzwalacze zadań** — w ramach zadania wyzwalanego przez aktualizacje obrazu podstawowego można także włączyć wyzwalacze na podstawie [zatwierdzenia kodu źródłowego](container-registry-tutorial-build-task.md) lub [harmonogramu](container-registry-tasks-scheduled.md). Aktualizacja obrazu podstawowego może również wyzwolić [zadanie wieloetapowe](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższymi samouczkami dotyczącymi scenariuszy automatyzowania kompilacji obrazów aplikacji po zaktualizowaniu obrazu podstawowego:

* [Automatyzuj kompilacje obrazu kontenera, gdy podstawowy obraz zostanie zaktualizowany w tym samym rejestrze](container-registry-tutorial-base-image-update.md)

* [Automatyzowanie tworzenia obrazu kontenera podczas aktualizowania obrazu podstawowego w innym rejestrze](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
