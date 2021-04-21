---
title: Samouczek — wyzwalanie kompilacji obrazu przez aktualizację prywatnego obrazu podstawowego
description: W tym samouczku skonfigurujesz zadanie Azure Container Registry, aby automatycznie wyzwalać kompilacje obrazu kontenera w chmurze po zaktualizowaniu obrazu podstawowego w innym prywatnym rejestrze kontenerów platformy Azure.
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 27ab7c3fc0f04023c32cfac181d8f8650de23560
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772365"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Samouczek: automatyzowanie kompilacji obrazu kontenera po zaktualizowaniu obrazu podstawowego w innym prywatnym rejestrze kontenerów 

[zadania usługi ACR](container-registry-tasks-overview.md) obsługuje automatyczne kompilacje obrazów podczas aktualizacji obrazu podstawowego [kontenera,](container-registry-tasks-base-images.md)na przykład w przypadku stosowania poprawek systemu operacyjnego lub struktury aplikacji w jednym z obrazów podstawowych. 

Z tego samouczka dowiesz się, jak utworzyć zadanie usługi ACR, które wyzwala kompilację w chmurze, gdy obraz podstawowy kontenera jest wypychany do innego rejestru kontenerów platformy Azure. Możesz również wypróbować samouczek, aby utworzyć zadanie usługi ACR, które wyzwala kompilację obrazu, gdy obraz podstawowy zostanie wypchnięty do tego samego rejestru [kontenerów platformy Azure.](container-registry-tutorial-base-image-update.md)

W tym samouczku:

> [!div class="checklist"]
> * Kompilowanie obrazu podstawowego w rejestrze bazowym
> * Tworzenie zadania kompilacji aplikacji w innym rejestrze w celu śledzenia obrazu podstawowego 
> * Aktualizowanie obrazu podstawowego w celu wyzwolenia zadania obrazu aplikacji
> * Wyświetlanie wyzwolonego zadania
> * Weryfikowanie zaktualizowanego obrazu aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="complete-the-previous-tutorials"></a>Ukończenie poprzednich samouczków

W tym samouczku założono, że środowisko zostało już skonfigurowane i zostały wykonane kroki z dwóch pierwszych samouczków z tej serii, w których:

* Tworzenie rejestru kontenerów platformy Azure
* Tworzenie rozwidlenia przykładowego repozytorium
* Klonowanie przykładowego repozytorium
* Tworzenie osobistego tokenu dostępu usługi GitHub

Jeśli jeszcze tego nie zrobiono, przed przystąpieniem wykonaj następujące samouczki:

[Tworzenie obrazów kontenera w chmurze przy użyciu usługi Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatyzowanie kompilacji obrazu kontenera za pomocą usługi Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Oprócz rejestru kontenerów utworzonego na potrzeby poprzednich samouczków należy utworzyć rejestr do przechowywania obrazów podstawowych. Jeśli chcesz, utwórz drugi rejestr w innej lokalizacji niż oryginalny rejestr.

### <a name="configure-the-environment"></a>Konfigurowanie środowiska

Wypełnij te zmienne środowiskowe powłoki przy użyciu wartości odpowiednich dla danego środowiska. Ten krok nie jest ściśle wymagany, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli te zmienne środowiskowe nie zostaną wypełnione, musisz ręcznie zastąpić każdą wartość wszędzie tam, gdzie jest wyświetlana w przykładowych poleceniach.

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Scenariusz aktualizacji obrazu podstawowego

W tym samouczku przedstawiono scenariusz aktualizacji obrazu podstawowego. Ten scenariusz odzwierciedla przepływ pracy projektowania do zarządzania obrazami bazowymi we wspólnym prywatnym rejestrze kontenerów podczas tworzenia obrazów aplikacji w innych rejestrach. Obrazy podstawowe mogą określać typowe systemy operacyjne i struktury używane przez zespół, a nawet typowe składniki usług.

Na przykład deweloperzy, którzy opracowują obrazy aplikacji we własnych rejestrach, mogą uzyskać dostęp do zestawu obrazów podstawowych utrzymywanych we wspólnym rejestrze bazowym. Rejestr podstawowy może być w innym regionie, a nawet zreplikowany geograficznie.

[Przykładowy kod][code-sample] obejmuje dwa pliki Dockerfile: obraz aplikacji i obraz określony w nim jako podstawowy. W poniższych sekcjach utworzysz zadanie usługi ACR, które automatycznie wyzwala kompilację obrazu aplikacji po wypchnięciu nowej wersji obrazu podstawowego do innego rejestru kontenerów platformy Azure.

* [Dockerfile-app][dockerfile-app]: mała aplikacja internetowa Node.js, która renderuje statyczną stronę internetową wyświetlającą wersję środowiska Node.js, na której się opiera. Ciąg wersji jest symulowany: wyświetla zawartość zmiennej środowiskowej `NODE_VERSION`, którą zdefiniowano w obrazie podstawowym.

* [Dockerfile-base][dockerfile-base]: obraz, który plik `Dockerfile-app` określa jako podstawowy. Opiera się ona na obrazie [Node][base-node] i uwzględnia zmienną środowiskową `NODE_VERSION`.

W poniższych sekcjach utworzysz zadanie, zaktualizujesz wartość `NODE_VERSION` w pliku Dockerfile obrazu podstawowego, a następnie użyjesz usługi ACR Tasks do skompilowania obrazu podstawowego. Gdy usługa ACR Tasks wypycha nowy obraz podstawowy do rejestru, następuje automatyczne wyzwolenie kompilacji obrazu aplikacji. Opcjonalnie możesz uruchomić obraz kontenera aplikacji lokalnie, aby zobaczyć inne ciągi wersji we wbudowanych obrazach.

W tym samouczku zadanie usługi ACR skompilowało i wypchnie obraz kontenera aplikacji określony w pliku Dockerfile. zadania usługi ACR można również uruchamiać zadania wieloetapowe [przy](container-registry-tasks-multi-step.md)użyciu pliku YAML w celu zdefiniowania kroków kompilowania, wypychania i opcjonalnie testowania wielu kontenerów.

## <a name="build-the-base-image"></a>Tworzenie obrazu podstawowego

Rozpocznij od skompilowania obrazu podstawowego za pomocą zadania usługi ACR *szybkie zadanie* przy użyciu narzędzia [az acr build.][az-acr-build] Zgodnie z opisem w [pierwszym samouczku](container-registry-tutorial-quick-task.md) z serii, w tym procesie następuje nie tylko kompilacja obrazu, ale także wypchnięcie go do rejestru kontenerów, jeśli kompilacja zakończy się pomyślnie. W tym przykładzie obraz jest wypychany do podstawowego rejestru obrazów.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Tworzenie zadania do śledzenia prywatnego obrazu podstawowego

Następnie utwórz zadanie w rejestrze obrazów aplikacji za pomocą az [acr task create][az-acr-task-create], włączając tożsamość [zarządzaną](container-registry-tasks-authentication-managed-identity.md). Tożsamość zarządzana jest używana w kolejnych krokach, aby zadanie uwierzytelniało się w rejestrze obrazów podstawowych. 

W tym przykładzie użyto tożsamości przypisanej przez system, ale w niektórych scenariuszach można utworzyć i włączyć tożsamość zarządzaną przypisaną przez użytkownika. Aby uzyskać szczegółowe informacje, [zobacz Cross-registry authentication in an ACR task using an Azure-managed identity](container-registry-tasks-cross-registry-authentication.md)(Uwierzytelnianie między rejestrami w zadaniu usługi ACR przy użyciu tożsamości zarządzanej przez platformę Azure).

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

To zadanie jest podobne do zadania utworzonego w poprzednim [samouczku.](container-registry-tutorial-build-task.md) Przesyła ono do usługi ACR Tasks instrukcję wyzwolenia kompilacji obrazu, gdy zatwierdzenia są wypychane do repozytorium określonego przez element `--context`. Plik Dockerfile użyty do skompilowania obrazu w poprzednim samouczku określa publiczny obraz podstawowy ( ), natomiast plik `FROM node:15-alpine` Dockerfile w tym zadaniu, [Dockerfile-app,][dockerfile-app]określa obraz podstawowy w rejestrze obrazów podstawowych:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Ta konfiguracja ułatwia symulowanie poprawki struktury w obrazie bazowym w dalszej części tego samouczka.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Nadaj tożsamości uprawnienia do ściągania do rejestru podstawowego

Aby nadać tożsamości zarządzanej zadania uprawnienia do ściągania obrazów z podstawowego rejestru obrazów, najpierw uruchom [az acr task show,][az-acr-task-show] aby uzyskać identyfikator jednostki usługi tożsamości. Następnie uruchom [az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru podstawowego:

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Przypisz uprawnienia do ściągania tożsamości zarządzanej do rejestru, uruchamiając [az role assignment create][az-role-assignment-create]:

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Dodawanie docelowych poświadczeń rejestru do zadania

Uruchom [az acr task credential add,][az-acr-task-credential-add] aby dodać poświadczenia do zadania. Przekaż parametr , aby wskazać, że tożsamość zarządzana przypisana przez system zadania może `--use-identity [system]` uzyskać dostęp do poświadczeń.

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Użyj [az acr task run,][az-acr-task-run] aby ręcznie wyzwolić zadanie i skompilować obraz aplikacji. Ten krok jest wymagany, aby zadanie śledziło zależność obrazu aplikacji od obrazu podstawowego.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
```

Po ukończeniu zadania zanotuj **identyfikator przebiegu** (na przykład „da6”), jeśli chcesz wykonać poniższy krok opcjonalny.

### <a name="optional-run-application-container-locally"></a>Opcjonalnie: lokalne uruchamianie aplikacji kontenera

Jeśli pracujesz lokalnie (nie w usłudze Cloud Shell) i masz zainstalowaną platformę Docker, uruchom kontener, aby zobaczyć renderowaną aplikację w przeglądarce internetowej przed ponownym skompilowaniem jej obrazu podstawowego. Jeśli używasz usługi Cloud Shell, pomiń tę sekcję (usługa Cloud Shell nie obsługuje poleceń `az acr login` ani `docker run`).

Najpierw uwierzytelnij się w rejestrze kontenerów za pomocą [az acr login:][az-acr-login]

```azurecli
az acr login --name $ACR_NAME
```

Teraz uruchom komputer lokalnie przy użyciu polecenia `docker run`. Zastąp **\<run-id\>** identyfikatorem uruchomienia znalezionym w danych wyjściowych poprzedniego kroku (na przykład "da6"). W tym przykładzie kontener ma `myapp` nazwę i zawiera parametr , który służy do usuwania kontenera po jego `--rm` zatrzymaniu.

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

Jeśli chcesz wykonać następujący krok opcjonalny, uruchamiając nowo skompilowany kontener w celu zobaczenia zaktualizowanego numeru wersji, zanotuj wartość **RUN ID** dla kompilacji wyzwalanej przez aktualizację obrazu (w poprzednich danych wyjściowych jest to "ca13").

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

W tym samouczku przedstawiono sposób konfigurowania zadania w celu automatycznego wyzwalania kompilacji obrazu kontenera po zaktualizowaniu obrazu podstawowego powiązanego z obrazem. Teraz przejdź do następnego samouczka, aby dowiedzieć się, jak wyzwalać zadania zgodnie ze zdefiniowanym harmonogramem.

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
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task]: /cli/azure/acr#az_acr_task
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
