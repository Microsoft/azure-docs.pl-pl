---
title: Uprawnienia do repozytoriów w Azure Container Registry
description: Tworzenie tokenu z uprawnieniami w zakresie określonym repozytoriów w rejestrze Premium w celu ściągania lub wypychania obrazów lub wykonywania innych akcji
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: 0ac479b696a377509cee6459efd8bbb9de940d2a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781401"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Tworzenie tokenu z uprawnieniami w zakresie repozytorium

W tym artykule opisano sposób tworzenia tokenów i map zakresu w celu zarządzania dostępem do określonych repozytoriów w rejestrze kontenerów. Tworząc tokeny, właściciel rejestru może zapewnić użytkownikom lub usługom ograniczony czasowo dostęp do repozytoriów w celu ściągania lub wypychania obrazów lub wykonywania innych akcji. Token zapewnia bardziej szczegółowe uprawnienia niż inne opcje uwierzytelniania [rejestru,](container-registry-authentication.md)które mają zakres uprawnień do całego rejestru. 

Scenariusze tworzenia tokenu obejmują:

* Zezwalanie na ściąganie obrazu z repozytorium przez urządzenia IoT z poszczególnymi tokenami
* Zapewnianie organizacji zewnętrznej uprawnień do określonego repozytorium 
* Ogranicz dostęp do repozytorium do różnych grup użytkowników w organizacji. Na przykład możesz zapewnić dostęp do zapisu i odczytu deweloperom, którzy kompilowali obrazy dla określonych repozytoriów docelowych, oraz dostęp do odczytu zespołom, które wdrażają z tych repozytoriów.

Ta funkcja jest dostępna w warstwie **usługi rejestru** kontenerów w warstwie Premium. Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry usługi](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej i obowiązują [pewne ograniczenia.](#preview-limitations) Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Obecnie nie można przypisywać uprawnień w zakresie repozytorium do tożsamości Azure Active Directory, takiej jak jednostka usługi lub tożsamość zarządzana.
* Nie można utworzyć mapy zakresu w rejestrze z włączonym dostępem [anonimowym do ściągania.](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)

## <a name="concepts"></a>Pojęcia

Aby skonfigurować uprawnienia w zakresie repozytorium, należy utworzyć *token ze* skojarzoną *mapą zakresu*. 

* **Token wraz** z wygenerowanym hasłem umożliwia użytkownikowi uwierzytelnianie w rejestrze. Możesz ustawić datę wygaśnięcia hasła tokenu lub wyłączyć token w dowolnym momencie.  

  Po uwierzytelnieniu za pomocą tokenu użytkownik lub  usługa może wykonać jedną lub więcej akcji w zakresie co najmniej jednego repozytorium.

  |Akcja  |Opis  | Przykład |
  |---------|---------|--------|
  |`content/delete`    | Usuwanie danych z repozytorium  | Usuwanie repozytorium lub manifestu |
  |`content/read`     |  Odczytywanie danych z repozytorium |  Ściąganie artefaktu |
  |`content/write`     |  Zapis danych w repozytorium     | Używanie z `content/read` w celu wypychania artefaktu |
  |`metadata/read`    | Odczytywanie metadanych z repozytorium   | Lista tagów lub manifestów |
  |`metadata/write`     |  Pisanie metadanych w repozytorium  | Włączanie lub wyłączanie operacji odczytu, zapisu lub usuwania |

* Mapa **zakresu grupuje** uprawnienia repozytorium stosowane do tokenu i może ponownie stosować do innych tokenów. Każdy token jest skojarzony z pojedynczą mapą zakresu. 

   Mapa zakresu:

    * Konfigurowanie wielu tokenów z identycznymi uprawnieniami do zestawu repozytoriów
    * Aktualizowanie uprawnień tokenu podczas dodawania lub usuwania akcji repozytorium na mapie zakresu lub stosowania innej mapy zakresu 

  Azure Container Registry udostępnia również kilka map zakresu zdefiniowanych przez system, które można zastosować podczas tworzenia tokenów. Uprawnienia map zakresu zdefiniowanych przez system mają zastosowanie do wszystkich repozytoriów w rejestrze.

Na poniższej ilustracji przedstawiono relację między tokenami i mapami zakresu. 

![Tokeny rejestru i mapy zakresu](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Wymagania wstępne

* **Interfejs wiersza polecenia platformy Azure** — przykłady poleceń interfejsu wiersza polecenia platformy Azure w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.17.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* **Docker** — do uwierzytelniania za pomocą rejestru w celu ściągania lub wypychania obrazów potrzebna jest lokalna instalacja platformy Docker. Platforma Docker dostarcza instrukcje na temat instalacji w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Rejestr kontenerów —** jeśli go nie masz, utwórz rejestr kontenerów w chmurze Premium w ramach subskrypcji platformy Azure lub uaktualnij istniejący rejestr. Na przykład użyj interfejsu [Azure Portal](container-registry-get-started-portal.md) lub interfejsu wiersza [polecenia platformy Azure.](container-registry-get-started-azure-cli.md) 

## <a name="create-token---cli"></a>Tworzenie tokenu — interfejs wiersza polecenia

### <a name="create-token-and-specify-repositories"></a>Tworzenie tokenu i określanie repozytoriów

Utwórz token przy użyciu [polecenia az acr token create.][az-acr-token-create] Podczas tworzenia tokenu można określić co najmniej jedno repozytorium i skojarzone akcje w każdym repozytorium. Repozytoria nie muszą jeszcze być w rejestrze. Aby utworzyć token, określając istniejącą mapę zakresu, zobacz [następną sekcję](#create-token-and-specify-scope-map).

Poniższy przykład tworzy token w rejestrze *myregistry* z następującymi uprawnieniami w `samples/hello-world` repo: `content/write` i `content/read` . Domyślnie polecenie ustawia domyślny stan tokenu na , ale stan można zaktualizować `enabled` `disabled` do w dowolnym momencie.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Dane wyjściowe pokazują szczegółowe informacje o tokenie. Domyślnie generowane są dwa hasła, które nie wygasają, ale opcjonalnie można ustawić datę wygaśnięcia. Zaleca się zapisanie haseł w bezpiecznym miejscu w celu późniejszego użycia do uwierzytelniania. Haseł nie można pobrać ponownie, ale można wygenerować nowe.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> Aby ponownie wygenerować hasła tokenu i okresy wygaśnięcia, zobacz [Ponowne generowanie haseł tokenu w](#regenerate-token-passwords) dalszej części tego artykułu.

Dane wyjściowe zawierają szczegółowe informacje o mapie zakresu utworzonego przez polecenie. Możesz użyć mapy zakresu o nazwie , aby zastosować te same akcje `MyToken-scope-map` repozytorium do innych tokenów. Możesz też zaktualizować mapę zakresu później, aby zmienić uprawnienia skojarzonych tokenów.

### <a name="create-token-and-specify-scope-map"></a>Tworzenie tokenu i określanie mapy zakresu

Alternatywnym sposobem utworzenia tokenu jest określenie istniejącej mapy zakresu. Jeśli nie masz jeszcze mapy zakresu, najpierw utwórz mapę, określając repozytoria i skojarzone akcje. Następnie określ mapę zakresu podczas tworzenia tokenu. 

Aby utworzyć mapę zakresu, użyj [polecenia az acr scope-map create.][az-acr-scope-map-create] Następujące polecenie tworzy mapę zakresu z tym samymi uprawnieniami w `samples/hello-world` repozytorium, które było używane wcześniej. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Uruchom [az acr token create,][az-acr-token-create] aby utworzyć token, określając *mapę zakresu MyScopeMap.* Podobnie jak w poprzednim przykładzie, polecenie ustawia domyślny stan tokenu na `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Dane wyjściowe pokazują szczegółowe informacje o tokenie. Domyślnie generowane są dwa hasła. Zaleca się zapisanie haseł w bezpiecznym miejscu do późniejszego użycia na użytek uwierzytelniania. Haseł nie można pobrać ponownie, ale można wygenerować nowe.

> [!NOTE]
> Aby ponownie wygenerować hasła tokenu i okresy wygaśnięcia, zobacz [Ponowne generowanie haseł tokenu w](#regenerate-token-passwords) dalszej części tego artykułu.

## <a name="create-token---portal"></a>Tworzenie tokenu — portal

Możesz użyć tej Azure Portal do tworzenia tokenów i map zakresu. Podobnie jak w przypadku polecenia interfejsu wiersza polecenia, podczas tworzenia tokenu można zastosować istniejącą mapę zakresu lub utworzyć mapę zakresu, określając co najmniej jedno repozytorium i `az acr token create` skojarzone akcje. Repozytoria nie muszą jeszcze być w rejestrze. 

Poniższy przykład tworzy token i mapę zakresu z następującymi uprawnieniami w `samples/hello-world` repozytorium: `content/write` i `content/read` .

1. W portalu przejdź do rejestru kontenerów.
1. W **obszarze Uprawnienia repozytorium** wybierz **pozycję Tokeny (wersja zapoznawcza) > + Dodaj**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Tworzenie tokenu w portalu":::
1. Wprowadź nazwę tokenu.
1. W **obszarze Mapa zakresu** wybierz pozycję Utwórz **nowy.**
1. Konfigurowanie mapy zakresu:
    1. Wprowadź nazwę i opis mapy zakresu. 
    1. W **obszarze Repozytoria** wprowadź `samples/hello-world` , a następnie w obszarze **Uprawnienia** wybierz  `content/read` i `content/write` . Następnie wybierz **pozycję +Dodaj**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Tworzenie mapy zakresu w portalu":::

    1. Po dodaniu repozytoriów i uprawnień wybierz pozycję **Dodaj,** aby dodać mapę zakresu.
1. Zaakceptuj domyślny stan **tokenu** **Włączone,** a następnie wybierz pozycję **Utwórz**.

Po weryfikacji i utworzeniu tokenu szczegóły tokenu są wyświetlane na **ekranie Tokeny.**

### <a name="add-token-password"></a>Dodawanie hasła tokenu

Aby użyć tokenu utworzonego w portalu, należy wygenerować hasło. Możesz wygenerować jedno lub dwa hasła i ustawić datę wygaśnięcia dla każdego z nich. 

1. W portalu przejdź do rejestru kontenerów.
1. W **obszarze Uprawnienia repozytorium** wybierz **pozycję Tokeny (wersja zapoznawcza)** i wybierz token.
1. W szczegółach tokenu wybierz **pozycję password1** **lub password2,** a następnie wybierz ikonę Generuj.
1. Na ekranie hasła opcjonalnie ustaw datę wygaśnięcia hasła, a następnie wybierz pozycję **Generuj**. Zaleca się ustawienie daty wygaśnięcia.
1. Po wygenerowaniu hasła skopiuj je i zapisz w bezpiecznej lokalizacji. Wygenerowanego hasła nie można pobrać po zamknięciu ekranu, ale można wygenerować nowe.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Tworzenie hasła tokenu w portalu":::

## <a name="authenticate-with-token"></a>Uwierzytelnianie za pomocą tokenu

Gdy użytkownik lub usługa używa tokenu do uwierzytelniania w rejestrze docelowym, podaje nazwę tokenu jako nazwę użytkownika i jedno z wygenerowanych haseł. 

Metoda uwierzytelniania zależy od skonfigurowanej akcji lub akcji skojarzonych z tokenem.

|Akcja  |Jak się uwierzytelnić  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` w interfejsie wiersza polecenia platformy Azure<br/><br/>Przykład: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` w interfejsie wiersza polecenia platformy Azure<br/><br/>Przykład: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` w interfejsie wiersza polecenia platformy Azure     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` w interfejsie wiersza polecenia platformy Azure   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` w interfejsie wiersza polecenia platformy Azure |

## <a name="examples-use-token"></a>Przykłady: Używanie tokenu

Następujące przykłady używają tokenu utworzonego wcześniej w tym artykule do wykonywania typowych operacji w repozytorium: wypychania i ściągania obrazów, usuwania obrazów i tworzenia listy tagów repozytorium. Token został początkowo ustawiony z uprawnieniami wypychania `content/write` (i `content/read` akcjami) w `samples/hello-world` repozytorium.

### <a name="pull-and-tag-test-images"></a>Ściąganie i tagowanie obrazów testowych

W poniższych przykładach ściągnij obrazy publiczne i Microsoft Container Registry i otaguj je dla `hello-world` `nginx` rejestru i repozytorium.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Uwierzytelnianie przy użyciu tokenu

Uruchom `docker login` program lub , aby `az acr login` uwierzytelnić się w rejestrze, aby wypchnąć lub ściągnąć obrazy. Podaj nazwę tokenu jako nazwę użytkownika i podaj jedno z jego haseł. Token musi mieć `Enabled` stan .

Poniższy przykład jest sformatowany dla powłoki Bash i udostępnia wartości przy użyciu zmiennych środowiskowych.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Dane wyjściowe powinny pokazywać pomyślne uwierzytelnienie:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Wypychanie obrazów do rejestru

Po pomyślnym zalogowaniu spróbuj wypchnąć oznakowane obrazy do rejestru. Ponieważ token ma uprawnienia do wypychania obrazów do `samples/hello-world` repozytorium, następujące wypchanie powiedzie się:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token nie ma uprawnień do tego repo, więc następująca próba wypychania kończy się niepowodzeniem `samples/nginx` z powodu błędu podobnego do `requested access to the resource is denied` następującego:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Aktualizowanie uprawnień tokenu

Aby zaktualizować uprawnienia tokenu, zaktualizuj uprawnienia w skojarzonej mapie zakresu. Zaktualizowana mapa zakresu jest natychmiast stosowana do wszystkich skojarzonych tokenów. 

Na przykład zaktualizuj za pomocą akcji i w `MyToken-scope-map` `content/write` `content/read` `samples/ngnx` repozytorium i usuń `content/write` akcję w `samples/hello-world` repozytorium.  

Aby użyć interfejsu wiersza polecenia platformy Azure, uruchom [az acr scope-map update,][az-acr-scope-map-update] aby zaktualizować mapę zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

W witrynie Azure Portal:

1. Przejdź do rejestru kontenerów.
1. W **obszarze Uprawnienia repozytorium** wybierz **pozycję Mapy zakresu (wersja zapoznawcza)** i wybierz mapę zakresu do zaktualizowania.
1. W **obszarze Repozytoria** wprowadź `samples/nginx` , a następnie w obszarze **Uprawnienia** wybierz `content/read` i `content/write` . Następnie wybierz **pozycję +Dodaj**.
1. W **obszarze Repozytoria** wybierz pozycję `samples/hello-world` i w obszarze **Uprawnienia** usuń zaznaczenie opcji `content/write` . Następnie wybierz pozycję **Zapisz**.

Po zaktualizowaniu mapy zakresu następujące wypchanie powiedzie się:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Ponieważ mapa zakresu ma tylko uprawnienia do repozytorium, próba wypchnięć do repozytorium kończy `content/read` `samples/hello-world` się teraz `samples/hello-world` niepowodzeniem:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Ściąganie obrazów z obu repozytoriów powiedzie się, ponieważ mapa zakresu zapewnia `content/read` uprawnienia do obu repozytoriów:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Usuwanie obrazów

Zaktualizuj mapę zakresu, dodając `content/delete` akcję do `nginx` repozytorium. Ta akcja umożliwia usunięcie obrazów w repozytorium lub usunięcie całego repozytorium.

W celu zwięzłości wyświetlamy tylko [polecenie az acr scope-map update,][az-acr-scope-map-update] aby zaktualizować mapę zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Aby zaktualizować mapę zakresu przy użyciu portalu, zobacz [poprzednią sekcję](#update-token-permissions).

Użyj następującego [polecenia az acr repository delete,][az-acr-repository-delete] aby usunąć `samples/nginx` repozytorium. Aby usunąć obrazy lub repozytoria, przekaż nazwę tokenu i hasło do polecenia . W poniższym przykładzie użyto zmiennych środowiskowych utworzonych wcześniej w tym artykule:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Pokazywanie tagów repo 

Zaktualizuj mapę zakresu, dodając `metadata/read` akcję do `hello-world` repozytorium. Ta akcja umożliwia odczytywanie danych manifestu i tagów w repozytorium.

W celu zwięzłości wyświetlamy tylko [polecenie az acr scope-map update,][az-acr-scope-map-update] aby zaktualizować mapę zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Aby zaktualizować mapę zakresu przy użyciu portalu, zobacz [poprzednią sekcję](#update-token-permissions).

Aby odczytać metadane w repozytorium, uruchom polecenie `samples/hello-world` [az acr repository show-manifests][az-acr-repository-show-manifests] lub [az acr repository show-tags.][az-acr-repository-show-tags] 

Aby odczytać metadane, przekaż nazwę tokenu i hasło do jednego z poleceń. W poniższym przykładzie użyto zmiennych środowiskowych utworzonych wcześniej w tym artykule:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Przykładowe dane wyjściowe:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Zarządzanie tokenami i mapami zakresu

### <a name="list-scope-maps"></a>Lista map zakresu

Użyj [polecenia az acr scope-map list][az-acr-scope-map-list] lub ekranu Mapy zakresu **(wersja zapoznawcza)** w portalu, aby wyświetlić listę wszystkich map zakresu skonfigurowanych w rejestrze. Na przykład:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Dane wyjściowe składają się z trzech map zakresu zdefiniowanych przez system i innych map zakresu wygenerowanych przez użytkownika. Tokeny można skonfigurować za pomocą dowolnej z tych map zakresu.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Pokaż szczegóły tokenu

Aby wyświetlić szczegóły tokenu, takie jak jego stan i daty wygaśnięcia hasła, uruchom polecenie [az acr token show][az-acr-token-show] lub wybierz token na ekranie **Tokeny (wersja zapoznawcza)** w portalu. Na przykład:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Użyj polecenia [az acr token list][az-acr-token-list] lub ekranu **Tokeny (wersja zapoznawcza)** w portalu, aby wyświetlić listę wszystkich tokenów skonfigurowanych w rejestrze. Na przykład:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Ponowne generowanie haseł tokenu

Jeśli nie wygenerowaliśmy hasła tokenu lub chcesz wygenerować nowe hasła, uruchom polecenie [az acr token credential generate.][az-acr-token-credential-generate] 

Poniższy przykład generuje nową wartość dla hasła password1 dla *tokenu MyToken* z okresem wygaśnięcia 30 dni. Hasło jest przechowywane w zmiennej środowiskowej `TOKEN_PWD` . Ten przykład jest sformatowany dla powłoki bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Aby użyć Azure Portal do wygenerowania hasła tokenu, zobacz kroki opisane w artykule [Tworzenie tokenu — portal](#create-token---portal) wcześniej w tym artykule.

### <a name="update-token-with-new-scope-map"></a>Aktualizowanie tokenu przy użyciu nowej mapy zakresu

Jeśli chcesz zaktualizować token przy użyciu innej mapy zakresu, uruchom [az acr token update][az-acr-token-update] i określ nową mapę zakresu. Na przykład:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

W portalu na ekranie **Tokeny (wersja zapoznawcza)** wybierz token, a następnie w obszarze Mapa **zakresu** wybierz inną mapę zakresu.

> [!TIP]
> Po zaktualizowaniu tokenu przy użyciu nowej mapy zakresu warto wygenerować nowe hasła tokenu. Użyj polecenia [az acr token credential generate][az-acr-token-credential-generate] lub wygeneruj ponownie hasło tokenu w Azure Portal.

## <a name="disable-or-delete-token"></a>Wyłączanie lub usuwanie tokenu

Może być konieczne tymczasowe wyłączenie użycia poświadczeń tokenu dla użytkownika lub usługi. 

Za pomocą interfejsu wiersza polecenia platformy Azure uruchom [polecenie az acr token update,][az-acr-token-update] aby ustawić `status` wartość `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

W portalu wybierz token na ekranie **Tokeny (wersja zapoznawcza)** i wybierz pozycję **Wyłączone w** obszarze **Stan**.

Aby usunąć token w celu trwałego unieważnienia dostępu przez każdego przy użyciu jego poświadczeń, uruchom [polecenie az acr token delete.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

W portalu wybierz token na ekranie **Tokeny (wersja zapoznawcza)** i wybierz pozycję **Odrzuć**.

## <a name="next-steps"></a>Następne kroki

* Aby zarządzać mapami zakresu i tokenami, użyj dodatkowych poleceń w grupach [poleceń az acr scope-map][az-acr-scope-map] i [az acr token.][az-acr-token]
* Zobacz omówienie [uwierzytelniania,](container-registry-authentication.md) aby uzyskać inne opcje uwierzytelniania w rejestrze kontenerów platformy Azure, w tym Azure Active Directory tożsamości, jednostki usługi lub konta administratora.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az_acr_repository_show_manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az_acr_repository_delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az_acr_scope_map_create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az_acr_scope_map_update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az_acr_token_show
[az-acr-token-list]: /cli/azure/acr/token/#az_acr_token_list
[az-acr-token-delete]: /cli/azure/acr/token/#az_acr_token_delete
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-update]: /cli/azure/acr/token/#az_acr_token_update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az_acr_token_credential_generate
