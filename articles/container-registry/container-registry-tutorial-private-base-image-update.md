---
title: Samouczek — Aktualizacja obrazu wyzwalacza według prywatnego obrazu podstawowego
description: W tym samouczku skonfigurujesz zadanie Azure Container Registry, aby automatycznie wyzwalać kompilacje obrazu kontenera w chmurze, gdy zostanie zaktualizowany podstawowy obraz w innym prywatnym rejestrze kontenera platformy Azure.
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: ce06e792fd5a4582e77d18313052ea91a38121a8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920210"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Samouczek: Automatyzowanie kompilowania obrazu kontenera podczas aktualizowania obrazu podstawowego w innym prywatnym rejestrze kontenera 

[Zadania ACR](container-registry-tasks-overview.md) obsługują zautomatyzowane kompilacje obrazu, gdy [obraz podstawowy kontenera jest aktualizowany](container-registry-tasks-base-images.md), na przykład w przypadku zastosowania poprawki do systemu operacyjnego lub aplikacji w jednym z obrazów podstawowych. 

W tym samouczku dowiesz się, jak utworzyć zadanie ACR wyzwalające kompilację w chmurze, gdy podstawowy obraz kontenera jest wypychany do innego rejestru kontenerów platformy Azure. Możesz również skorzystać z samouczka, aby utworzyć zadanie ACR wyzwalające kompilację obrazu, gdy podstawowy obraz jest wypychany do tego [samego rejestru kontenerów platformy Azure](container-registry-tutorial-base-image-update.md).

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie obrazu podstawowego w rejestrze podstawowym
> * Tworzenie zadania tworzenia aplikacji w innym rejestrze w celu śledzenia obrazu podstawowego 
> * Aktualizowanie obrazu podstawowego w celu wyzwolenia zadania obrazu aplikacji
> * Wyświetlanie wyzwolonego zadania
> * Weryfikowanie zaktualizowanego obrazu aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="complete-the-previous-tutorials"></a>Ukończenie poprzednich samouczków

W tym samouczku przyjęto założenie, że Twoje środowisko zostało już skonfigurowane i zostały wykonane kroki z pierwszych dwóch samouczków w serii, w których:

* Tworzenie rejestru kontenerów platformy Azure
* Tworzenie rozwidlenia przykładowego repozytorium
* Klonowanie przykładowego repozytorium
* Tworzenie osobistego tokenu dostępu usługi GitHub

Jeśli jeszcze tego nie zrobiono, przed kontynuowaniem wykonaj następujące samouczki:

[Tworzenie obrazów kontenera w chmurze przy użyciu usługi Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatyzowanie kompilacji obrazu kontenera za pomocą usługi Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Oprócz rejestru kontenerów utworzonego dla poprzednich samouczków należy utworzyć rejestr do przechowywania obrazów podstawowych. Jeśli chcesz, utwórz drugi rejestr w innej lokalizacji niż oryginalny rejestr.

### <a name="configure-the-environment"></a>Konfigurowanie środowiska

Wypełnij te zmienne środowiskowe powłoki przy użyciu wartości odpowiednich dla danego środowiska. Ten krok nie jest ściśle wymagany, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli te zmienne środowiskowe nie zostaną wypełnione, należy ręcznie zastąpić każdą wartość w dowolnym miejscu w przykładowych poleceniach.

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Scenariusz aktualizacji obrazu podstawowego

W tym samouczku przedstawiono scenariusz aktualizacji obrazu podstawowego. Ten scenariusz odzwierciedla przepływ pracy deweloperskiej służący do zarządzania obrazami podstawowymi w typowym, prywatnym rejestrze kontenera podczas tworzenia obrazów aplikacji w innych rejestrach. Obrazy podstawowe mogą określać typowe systemy operacyjne i struktury używane przez zespół, a nawet wspólne składniki usługi.

Na przykład deweloperzy, którzy opracowują obrazy aplikacji we własnych rejestrach, mogą uzyskać dostęp do zestawu obrazów podstawowych przechowywanych w typowym rejestrze podstawowym. Rejestr podstawowy może znajdować się w innym regionie lub nawet z replikacją geograficzną.

[Przykładowy kod][code-sample] obejmuje dwa pliki Dockerfile: obraz aplikacji i obraz określony w nim jako podstawowy. W poniższych sekcjach utworzysz zadanie ACR, które automatycznie wyzwala kompilację obrazu aplikacji, gdy nowa wersja obrazu podstawowego jest wypychana do innego rejestru kontenerów platformy Azure.

* [Dockerfile-app][dockerfile-app]: mała aplikacja internetowa Node.js, która renderuje statyczną stronę internetową wyświetlającą wersję środowiska Node.js, na której się opiera. Ciąg wersji jest symulowany: wyświetla zawartość zmiennej środowiskowej `NODE_VERSION`, którą zdefiniowano w obrazie podstawowym.

* [Dockerfile-base][dockerfile-base]: obraz, który plik `Dockerfile-app` określa jako podstawowy. Opiera się ona na obrazie [Node][base-node] i uwzględnia zmienną środowiskową `NODE_VERSION`.

W poniższych sekcjach utworzysz zadanie, zaktualizujesz wartość `NODE_VERSION` w pliku Dockerfile obrazu podstawowego, a następnie użyjesz usługi ACR Tasks do skompilowania obrazu podstawowego. Gdy usługa ACR Tasks wypycha nowy obraz podstawowy do rejestru, następuje automatyczne wyzwolenie kompilacji obrazu aplikacji. Opcjonalnie możesz uruchomić obraz kontenera aplikacji lokalnie, aby zobaczyć inne ciągi wersji we wbudowanych obrazach.

W tym samouczku zadanie ACR kompiluje i wypycha obraz kontenera aplikacji określony w pliku dockerfile. Zadania ACR mogą również uruchamiać [zadania wieloetapowe](container-registry-tasks-multi-step.md), używając pliku YAML do definiowania kroków do kompilowania, wypychania i opcjonalnego testowania wielu kontenerów.

## <a name="build-the-base-image"></a>Tworzenie obrazu podstawowego

Zacznij od utworzenia obrazu podstawowego z *ACR zadaniami, używając* polecenia [AZ ACR Build][az-acr-build]. Zgodnie z opisem w [pierwszym samouczku](container-registry-tutorial-quick-task.md) z serii, w tym procesie następuje nie tylko kompilacja obrazu, ale także wypchnięcie go do rejestru kontenerów, jeśli kompilacja zakończy się pomyślnie. W tym przykładzie obraz jest wypychany do rejestru podstawowego obrazu.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Utwórz zadanie śledzenia obrazu prywatnego

Następnie utwórz zadanie w rejestrze obrazu aplikacji za pomocą [AZ ACR Task Create][az-acr-task-create], włączając [tożsamość zarządzaną](container-registry-tasks-authentication-managed-identity.md). Tożsamość zarządzana jest używana w kolejnych krokach, aby zadanie było uwierzytelniane przy użyciu podstawowego rejestru obrazów. 

W tym przykładzie użyto tożsamości przypisanej do systemu, ale można utworzyć i włączyć tożsamość zarządzaną przypisaną przez użytkownika dla niektórych scenariuszy. Aby uzyskać szczegółowe informacje, zobacz [uwierzytelnianie między rejestrami w ACR zadania przy użyciu tożsamości zarządzanej przez platformę Azure](container-registry-tasks-cross-registry-authentication.md).

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample2 \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```

To zadanie jest podobne do zadania utworzonego w [poprzednim samouczku](container-registry-tutorial-build-task.md). Przesyła ono do usługi ACR Tasks instrukcję wyzwolenia kompilacji obrazu, gdy zatwierdzenia są wypychane do repozytorium określonego przez element `--context`. Podczas gdy pliku dockerfile używany do kompilowania obrazu w poprzednim samouczku określa publiczny obraz podstawowy ( `FROM node:15-alpine` ), pliku dockerfile w tym zadaniu, [pliku dockerfile-App][dockerfile-app], określa podstawowy obraz w rejestrze podstawowym obrazu:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Ta konfiguracja ułatwia symulowanie stosowania poprawek struktury w obrazie podstawowym w dalszej części tego samouczka.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Przyznaj uprawnienia do ściągania tożsamości do rejestru podstawowego

Aby przyznać zarządzanym przez zadanie uprawnienia tożsamości do ściągania obrazów z rejestru podstawowego obrazu, najpierw uruchom [AZ ACR Task show][az-acr-task-show] , aby uzyskać identyfikator jednostki usługi tożsamości. Następnie uruchom polecenie [AZ ACR show][az-acr-show] , aby uzyskać identyfikator zasobu rejestru podstawowego:

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Aby przypisać uprawnienia do ściągania tożsamości zarządzanej do rejestru, należy uruchomić [AZ role przypisanie Create][az-role-assignment-create]:

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Dodawanie docelowych poświadczeń rejestru do zadania

Uruchom [AZ ACR Task Credential Add][az-acr-task-credential-add] , aby dodać poświadczenia do zadania. Przekaż `--use-identity [system]` parametr, aby wskazać, że tożsamość zarządzana przypisana przez system do zadania ma dostęp do poświadczeń.

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Użyj [AZ ACR Task Run][az-acr-task-run] , aby ręcznie wyzwolić zadanie i utworzyć obraz aplikacji. Ten krok jest zbędny, aby zadanie śledzi zależność obrazu aplikacji na obrazie podstawowym.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
```

Po ukończeniu zadania zanotuj **identyfikator przebiegu** (na przykład „da6”), jeśli chcesz wykonać poniższy krok opcjonalny.

### <a name="optional-run-application-container-locally"></a>Opcjonalnie: lokalne uruchamianie aplikacji kontenera

Jeśli pracujesz lokalnie (nie w usłudze Cloud Shell) i masz zainstalowaną platformę Docker, uruchom kontener, aby zobaczyć renderowaną aplikację w przeglądarce internetowej przed ponownym skompilowaniem jej obrazu podstawowego. Jeśli używasz usługi Cloud Shell, pomiń tę sekcję (usługa Cloud Shell nie obsługuje poleceń `az acr login` ani `docker run`).

Najpierw należy uwierzytelnić się w rejestrze kontenerów za pomocą [AZ ACR login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Teraz uruchom komputer lokalnie przy użyciu polecenia `docker run`. Zamień na **\<run-id\>** Identyfikator przebiegu znaleziony w danych wyjściowych z poprzedniego kroku (na przykład "DA6"). Ten przykład określa nazwę kontenera `myapp` i zawiera `--rm` parametr do usuwania kontenera po jego zatrzymaniu.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Przejdź do elementu `http://localhost:8080` w przeglądarce. Powinien zostać wyświetlony numer wersji środowiska Node.js renderowany na stronie internetowej, podobny do następującego. W kolejnym kroku wersja zostanie zwiększona przez dodanie „a” do ciągu wersji.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Zrzut ekranu przedstawiający przykładową aplikację w przeglądarce":::

Aby zatrzymać i usunąć kontener, uruchom następujące polecenie:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Tworzenie listy kompilacji

Następnie utwórz listę przebiegów zadań, które wykonała usługa ACR Tasks dla rejestru, używając polecenia [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Jeśli ukończono poprzedni samouczek (a rejestr nie został usunięty), powinny pojawić się dane wyjściowe podobne do poniższych. Zanotuj liczbę przebiegów zadań i najnowszą wartość RUN ID (identyfikator przebiegu), aby porównać dane wyjściowe po zaktualizowaniu obrazu podstawowego w następnej sekcji.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

UN ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

## <a name="update-the-base-image"></a>Aktualizowanie obrazu podstawowego

W tym miejscu przeprowadzisz symulację poprawki platformy w obrazie podstawowym. Edytuj plik **Dockerfile-base** i dodaj „a” po numerze wersji zdefiniowanym w elemencie `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 15.2.1a
```

Uruchom szybkie zadanie w celu skompilowania zmodyfikowanego obrazu podstawowego. Zanotuj **identyfikator przebiegu** w danych wyjściowych.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

Gdy kompilacja zostanie ukończona i usługa ACR Tasks wypchnie nowy obraz podstawowy do rejestru, następuje wyzwolenie kompilacji obrazu aplikacji. Może upłynąć kilka minut, zanim utworzone wcześniej zadanie wyzwoli kompilację obrazu aplikacji, ponieważ musi wykryć nowo utworzony i wypchnięty obraz podstawowy.

## <a name="list-updated-build"></a>Utworzenie listy zaktualizowanej kompilacji

Teraz, gdy obraz podstawowy został skompilowany, ponownie utwórz listę przebiegów zadań w celu porównania ze starszą listą. Jeśli na początku dane wyjściowe nie różnią się, okresowo uruchamiaj polecenie, aby zobaczyć nowy przebieg zadania na liście.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Dane wyjściowe będą podobne do następujących. Element TRIGGER (wyzwalacz) dla ostatnio wykonanej kompilacji powinien mieć wartość „Aktualizacja obrazu”, wskazując, że zadanie zostało uruchomione przez funkcję szybkiego zadania obrazu podstawowego.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca13      baseexample2    linux       Succeeded  Image Update  2020-11-21T00:06:00Z  00:00:43
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

Jeśli chcesz wykonać następujący opcjonalny krok uruchamiania nowo skompilowanego kontenera, aby wyświetlić zaktualizowany numer wersji, zwróć uwagę na wartość **identyfikatora uruchomienia** dla kompilacji wyzwalanej przez aktualizację dla obrazu (w poprzednich danych wyjściowych jest to "ca13").

### <a name="optional-run-newly-built-image"></a>Opcjonalnie: uruchamianie nowo utworzonego obrazu

Jeśli pracujesz lokalnie (nie w usłudze Cloud Shell) i masz zainstalowaną platformę Docker, uruchom nowy obraz aplikacji po ukończeniu kompilacji. Zastąp element `<run-id>` identyfikatorem przebiegu RUN ID uzyskanym w poprzednim kroku. Jeśli używasz usługi Cloud Shell, pomiń tę sekcję (usługa Cloud Shell nie obsługuje polecenia `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Przejdź do elementu http://localhost:8081 w przeglądarce. Powinien zostać wyświetlony zaktualizowany numer wersji środowiska Node.js (z literą „a”) na stronie internetowej:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Zrzut ekranu przedstawiający zaktualizowaną przykładową aplikację w przeglądarce":::

Pamiętaj, że obraz **podstawowy** został zaktualizowany za pomocą nowego numeru wersji, ale ostatnio skompilowany obraz **aplikacji** wyświetla nową wersję. Usługa ACR Tasks pobrała zmianę do obrazu podstawowego i automatycznie ponownie skompilowała obraz aplikacji.

Aby zatrzymać i usunąć kontener, uruchom następujące polecenie:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania zadania w celu automatycznego wyzwalania kompilacji obrazu kontenera po zaktualizowaniu obrazu podstawowego powiązanego z obrazem. Teraz przejdź do następnego samouczka, aby dowiedzieć się, jak wyzwolić zadania zgodnie ze zdefiniowanym harmonogramem.

> [!div class="nextstepaction"]
> [Uruchamianie zadania zgodnie z harmonogramem](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

