---
title: Przepływ pracy zadania do zarządzania zawartością rejestru publicznego
description: Utwórz przepływ pracy automatycznego Azure Container Registry zadań, aby śledzić i korzystać z publicznej zawartości obrazów w prywatnym rejestrze kontenerów platformy Azure.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349286"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Jak używać i konserwować publiczną zawartość za pomocą zadań Azure Container Registry

Ten artykuł zawiera przykładowy przepływ pracy w Azure Container Registry ułatwiający zarządzanie wykorzystywaniem i konserwowaniem zawartości publicznej:

1. Importuj lokalne kopie zależnych obrazów publicznych.
1. Weryfikuj Obrazy publiczne poprzez skanowanie zabezpieczeń i testowanie funkcjonalne.
1. Podnieś poziom obrazów do prywatnych rejestrów do użytku wewnętrznego.
1. Wyzwalaj aktualizacje obrazu podstawowego dla aplikacji zależnych od zawartości publicznej.
1. Użyj [Azure Container Registry zadań](container-registry-tasks-overview.md) , aby zautomatyzować ten przepływ pracy.

Ten przepływ pracy jest podsumowywany na poniższym obrazie:

![Używanie publicznego przepływu pracy zawartości](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

Przepływ pracy warunkowego importu pomaga zarządzać zależnościami w organizacji względem zarządzanych przez zewnętrznie artefaktów — na przykład obrazy pochodzące z publicznych rejestrów, w tym usługi [Docker Hub][docker-hub], [GCR][gcr], [nabrzeż][quay], [GitHub Container Registry][ghcr], [Microsoft Container Registry][mcr], a nawet inne [rejestry kontenerów platformy Azure][acr]. 

Aby uzyskać ogólne informacje o ryzyku wprowadzonym przez zależności od publicznej zawartości i sposobach ich rozwiązywania przy użyciu Azure Container Registry, zobacz [wpis w blogu OCI wykorzystujące zawartość publiczną][oci-consuming-public-content] i [Zarządzaj publiczną zawartością przy użyciu Azure Container Registry](buffer-gate-public-content.md).

Aby ukończyć ten Instruktaż, można użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Zalecany jest interfejs wiersza polecenia platformy Azure w wersji 2,10 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-cli].

## <a name="scenario-overview"></a>Omówienie scenariusza

![Importuj składniki przepływu pracy](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Ten Instruktaż konfiguruje:

1. Trzy **rejestry kontenerów**, reprezentujące:
   * Symulowane [centrum Docker][docker-hub] ( `publicregistry` ) umożliwiające obsługę zmiany obrazu podstawowego
   * Rejestr zespołu ( `contoso` ) do udostępniania obrazów prywatnych
   * Udostępniony rejestr firmy/zespołu ( `baseartifacts` ) dla zaimportowanej publicznej zawartości
1. **Zadanie ACR** w każdym rejestrze. Zadania:  
   1. Tworzenie symulowanego publicznego `node` obrazu
   1. Zaimportuj i sprawdź poprawność `node` obrazu do rejestru udostępnionego firmy/zespołu
   1. Kompilowanie i wdrażanie `hello-world` obrazu
1. **Definicje zadań ACR**, w tym konfiguracje dla:
1. Kolekcja **poświadczeń rejestru**, które są wskaźnikami do magazynu kluczy
1. Kolekcja wpisów **tajnych** dostępnych w ramach `acr-task.yaml` , które są wskaźnikami do magazynu kluczy
1. Kolekcja **skonfigurowanych wartości** używanych w ramach `acr-task.yaml`
1. **Magazyn kluczy platformy Azure** do zabezpieczenia wszystkich wpisów tajnych
1. **Wystąpienie kontenera platformy Azure**, które hostuje `hello-world` aplikację kompilacji

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe kroki służą do konfigurowania wartości dla zasobów utworzonych i używanych w przewodniku.

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Skonfiguruj zmienne unikatowe dla danego środowiska. Przestrzegamy najlepszych rozwiązań dotyczących umieszczania zasobów z trwałą zawartością we własnej grupie zasobów, aby zminimalizować Przypadkowe usunięcie. Można je jednak umieścić w pojedynczej grupie zasobów, jeśli jest to konieczne.

Przykłady w tym artykule są sformatowane dla powłoki bash.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Repozytoria i tokeny usługi git

Aby symulować środowisko, rozwidlenie każdego z następujących repozytoriów Git w repozytoriach, którymi można zarządzać. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Następnie zaktualizuj następujące zmienne dla repozytoriów rozwidlenia.

`:main`Dołączenie na końcu adresów URL usługi git reprezentuje domyślną gałąź repozytorium.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

Do ACR zadań do klonowania i ustanawiania elementów webhook usługi git potrzebny jest [token dostępu usługi GitHub][git-token] . Aby uzyskać instrukcje dotyczące tworzenia tokenu z uprawnieniami wymaganymi do repozytorium prywatnego, zobacz [Tworzenie tokenu dostępu usługi GitHub](container-registry-tutorial-build-task.md#create-a-github-personal-access-token). 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Poświadczenia centrum platformy Docker  
Aby uniknąć ograniczania i żądania tożsamości podczas ściągania obrazów z usługi Docker Hub, Utwórz [token usługi Docker Hub][docker-hub-tokens]. Następnie ustaw następujące zmienne środowiskowe:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Tworzenie rejestrów

Korzystając z poleceń interfejsu wiersza polecenia platformy Azure, Utwórz trzy rejestry kontenerów warstwy Premium, z których każda jest w jego własnej grupie zasobów:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Utwórz magazyn kluczy i ustaw wpisy tajne

Utwórz magazyn kluczy:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Ustaw nazwę użytkownika i token centrum platformy Docker w magazynie kluczy:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Ustawianie i weryfikowanie źródła Git w magazynie kluczy:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Tworzenie grupy zasobów dla wystąpienia kontenera platformy Azure

Ta grupa zasobów jest używana w późniejszym zadaniu podczas wdrażania `hello-world` obrazu.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Tworzenie publicznego `node` obrazu podstawowego

Aby zasymulować `node` obraz w usłudze Docker Hub, Utwórz [zadanie ACR][acr-task] , aby skompilować i zachować publiczny obraz. Ta konfiguracja umożliwia symulowanie zmian przez `node` obsłudze obrazu.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Aby uniknąć ograniczania przepustowości platformy Docker, Dodaj do zadania [poświadczenia narzędzia Docker Hub][docker-hub-tokens] . Polecenie [poświadczenia zadania ACR][acr-task-credentials] może służyć do przekazywania poświadczeń platformy Docker do dowolnych rejestrów, w tym do koncentratora platformy Docker.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Przyznaj zadanie dostępu do odczytu wartości z magazynu kluczy:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[Zadania mogą być wyzwalane][acr-task-triggers] przez zatwierdzenia git, aktualizacje obrazu podstawowego, czasomierze lub ręczne uruchomienia. 

Uruchom zadanie ręcznie w celu wygenerowania `node` obrazu:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Wyświetl obraz w symulowanym rejestrze publicznym:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Tworzenie `hello-world` obrazu

Na podstawie symulowanego publicznego `node` obrazu należy utworzyć `hello-world` obraz.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Utwórz token dla dostępu ściągania do symulowanego rejestru publicznego

Utwórz [token dostępu][acr-tokens] do symulowanego rejestru publicznego, do którego należy zakres `pull` . Następnie ustaw go w magazynie kluczy:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Utwórz token dla dostępu ściągania przez Azure Container Instances

Utwórz [token dostępu][acr-tokens] do rejestru, w którym znajduje się `hello-world` obraz, w zakresie ściągania. Następnie ustaw go w magazynie kluczy:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Utwórz zadanie do kompilowania i konserwowania `hello-world` obrazu

Następujące polecenie tworzy zadanie z definicji w `acr-tasks.yaml` `hello-world` repozytorium. Kroki zadania umożliwiają skompilowanie `hello-world` obrazu, a następnie wdrożenie go w celu Azure Container Instances. Grupa zasobów dla Azure Container Instances została utworzona w poprzedniej sekcji. Wywoływanie `az container create` zadania z tylko różnicą w `image:tag` , zadanie jest wdrażane w tym samym wystąpieniu w tym instruktażu.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Dodaj poświadczenia do zadania dla symulowanego rejestru publicznego:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Przyznaj zadanie dostępu do odczytu wartości z magazynu kluczy:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Przyznaj zadanie dostępu do tworzenia Azure Container Instances i zarządzania nim, przyznając dostęp do grupy zasobów:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

Po utworzeniu i skonfigurowaniu zadania uruchom zadanie w celu skompilowania i wdrożenia `hello-world` obrazu:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Po utworzeniu należy uzyskać adres IP kontenera obsługującego `hello-world` obraz.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

W przeglądarce przejdź do adresu IP, aby wyświetlić uruchomioną aplikację.

## <a name="update-the-base-image-with-a-questionable-change"></a>Aktualizowanie obrazu podstawowego za pomocą zmiany "wątpliwe"

Ta sekcja symuluje zmianę obrazu podstawowego, który może spowodować problemy w środowisku.

1. Otwórz `Dockerfile` w repozytorium rozwidlenia `base-image-node` .
1. Zmień na, aby `BACKGROUND_COLOR` `Orange` symulować zmianę.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Zatwierdź zmiany i obejrzyj ACR zadania, aby automatycznie rozpocząć Kompilowanie.

Obejrzyj zadanie, aby rozpocząć wykonywanie:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Powinien być widoczny stan `Succeeded` na podstawie wyzwalacza `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

**Naciśnij klawisze CTRL + C** , aby zamknąć polecenie czujki, a następnie Wyświetl dzienniki dla ostatniego przebiegu:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Po `node` zakończeniu obrazu `watch` ACR zadania automatycznego rozpoczęcia tworzenia `hello-world` obrazu:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Powinien być widoczny stan `Succeeded` na podstawie wyzwalacza `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

**Naciśnij klawisze CTRL + C** , aby zamknąć polecenie czujki, a następnie Wyświetl dzienniki dla ostatniego przebiegu:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Po zakończeniu Pobierz adres IP witryny obsługującej zaktualizowany `hello-world` obraz:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

W przeglądarce przejdź do witryny, która powinna mieć kolor tła pomarańczowy (wątpliwy).

### <a name="checking-in"></a>Ewidencjonowanie

W tym momencie utworzysz `hello-world` obraz, który jest automatycznie tworzony na zatwierdzeniach i zmianach w narzędziu git `node` . W tym przykładzie zadanie kompiluje się względem obrazu podstawowego w Azure Container Registry, ale można użyć dowolnego obsługiwanego rejestru.

Aktualizacja obrazu podstawowego automatycznie wyzwala ponowne uruchomienie zadania, gdy `node` obraz zostanie zaktualizowany. Jak widać tutaj, nie wszystkie aktualizacje są niepożądane.

## <a name="gated-imports-of-public-content"></a>Warunkowe Importowanie zawartości publicznej

Aby zapobiec wprowadzeniu zmian w strumieniach o krytycznym znaczeniu, można dodać skanowanie zabezpieczeń i testy funkcjonalne.

W tej sekcji utworzysz zadanie ACR:

* Kompilowanie obrazu testowego
* Uruchom skrypt testu funkcjonalnego `./test.sh` względem obrazu testu
* Jeśli obraz zakończył się pomyślnie, zaimportuj obraz publiczny do rejestru **baseimages**

### <a name="add-automation-testing"></a>Dodaj testowanie automatyzacji

Do bramy każdej zawartości z dowolnego strumienia jest implementowane automatyczne testowanie. W tym przykładzie `test.sh` jest dostępny program, który sprawdza, czy `$BACKGROUND_COLOR` . Jeśli test nie powiedzie się `EXIT_CODE` , `1` zostanie zwrócony element, który powoduje niepowodzenie kroku zadania ACR, kończąc zadanie. Testy mogą być rozwinięte w dowolnej formie narzędzi, w tym wyniki rejestrowania. Brama jest zarządzana przez odpowiedź z przekazywaniem/niepowodzeniem w skrypcie, odtwarzanym tutaj:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>YAML zadań 

Przejrzyj `acr-task.yaml` `import-baseimage-node` repozytorium, wykonując następujące czynności:

1. Utwórz podstawowy obraz testowy przy użyciu następującego pliku dockerfile:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Po zakończeniu Sprawdź poprawność obrazu, uruchamiając kontener, który jest uruchamiany `./test.sh`
1. Dopiero po pomyślnym ukończeniu wykonaj kroki importowania, które są warunkowe `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Utwórz zadanie do importowania i testowania obrazu podstawowego

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Dodaj poświadczenia do zadania dla symulowanego rejestru publicznego:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Przyznaj zadanie dostępu do odczytu wartości z magazynu kluczy:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Uruchom zadanie importowania:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Jeśli zadanie nie powiedzie się z powodu błędu `./test.sh: Permission denied` , upewnij się, że skrypt ma uprawnienia do wykonywania, i zatwierdź z powrotem do repozytorium git:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>Aktualizowanie `hello-world` obrazu do kompilacji z poziomu obrazu warunkowego `node`

Utwórz [token dostępu][acr-tokens] , aby uzyskać dostęp do rejestru artefaktów podstawowych, w zakresie `read` od `node` repozytorium. Następnie ustaw w magazynie kluczy:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Dodaj poświadczenia do zadania **Hello-World** dla rejestru artefaktów podstawowych:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Aktualizowanie zadania w celu zmiany sposobu `REGISTRY_FROM_URL` korzystania z `BASE_ARTIFACTS` rejestru

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Uruchom zadanie **Hello-World** , aby zmienić jego zależność obrazu podstawowego:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Aktualizowanie obrazu podstawowego przy użyciu prawidłowej zmiany

1. Otwórz `Dockerfile` repozytorium w `base-image-node` .
1. Zmień `BACKGROUND_COLOR` element na, `Green` Aby symulować prawidłową zmianę.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Zatwierdź zmianę i monitoruj sekwencję aktualizacji:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Po uruchomieniu **naciśnij klawisze CTRL + C** i monitoruj dzienniki:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Po wykonaniu tych czynności Monitoruj zadanie **importowania obrazu podstawowego** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Po uruchomieniu **naciśnij klawisze CTRL + C** i monitoruj dzienniki:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Po zakończeniu Monitoruj zadanie **Hello-World** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Po uruchomieniu **naciśnij klawisze CTRL + C** i monitoruj dzienniki:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Po zakończeniu Pobierz adres IP witryny obsługującej zaktualizowany `hello-world` obraz:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

W przeglądarce przejdź do witryny, która powinna mieć zielony (prawidłowy) tło.

### <a name="view-the-gated-workflow"></a>Wyświetlanie warunkowego przepływu pracy

Wykonaj ponownie kroki opisane w poprzedniej sekcji z kolorem tła czerwieni.

1. Otwórz `Dockerfile` w `base-image-node` repozytorium
1. Zmień na, aby `BACKGROUND_COLOR` `Red` symulować nieprawidłową zmianę.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Zatwierdź zmianę i monitoruj sekwencję aktualizacji:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Po uruchomieniu **naciśnij klawisze CTRL + C** i monitoruj dzienniki:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Po wykonaniu tych czynności Monitoruj zadanie **importowania obrazu podstawowego** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Po uruchomieniu **naciśnij klawisze CTRL + C** i monitoruj dzienniki:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

W tym momencie powinna zostać wyświetlona Walidacja zadania w **węźle Base-import-Node** i zatrzymywanie sekwencji publikowania `hello-world` aktualizacji. Dane wyjściowe są podobne do następujących:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Opublikuj aktualizację do programu `hello-world`

Zmiany wprowadzone w `hello-world` obrazie będą kontynuowane przy użyciu ostatniego zweryfikowanego `node` obrazu.

Wszelkie dodatkowe zmiany w `node` obrazie podstawowym, które przekazują sprawdzanie warunkowe, będą wyzwalać aktualizacje obrazu podstawowego do `hello-world` obrazu.

## <a name="cleaning-up"></a>Czyszczenie

Gdy nie jest już potrzebne, Usuń zasoby używane w tym artykule.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Następne kroki

W tym artykule. użyto zadań ACR do utworzenia zautomatyzowanego przepływu pracy kontroli w celu wprowadzenia zaktualizowanych obrazów podstawowych do środowiska. Zobacz powiązane informacje, aby zarządzać obrazami w Azure Container Registry.


* [Zalecenia dotyczące tagowania i przechowywania wersji obrazów kontenerów](container-registry-image-tag-version.md)
* [Blokowanie obrazu kontenera w usłudze Azure Container Registry](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io