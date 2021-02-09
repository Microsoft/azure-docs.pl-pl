---
title: Uprawnienia do repozytoriów w Azure Container Registry
description: Utwórz token z uprawnieniami objętymi zakresem określonych repozytoriów w rejestrze Premium w celu ściągania lub wypychania obrazów lub wykonywania innych akcji
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: ceec69d746f77ea7a23bc70d029c8b3736e7f292
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988263"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Tworzenie tokenu z uprawnieniami do zakresu repozytorium

W tym artykule opisano sposób tworzenia mapowań tokenów i zakresu w celu zarządzania dostępem do określonych repozytoriów w rejestrze kontenerów. Tworząc tokeny, właściciel rejestru może zapewnić użytkownikom lub usługom zakres, ograniczony czasowo dostęp do repozytoriów w celu ściągania lub wypychania obrazów lub wykonywania innych akcji. Token zapewnia bardziej szczegółowe uprawnienia niż inne [Opcje uwierzytelniania](container-registry-authentication.md)rejestru, które mają zakres uprawnień do całego rejestru. 

Scenariusze tworzenia tokenu obejmują:

* Zezwalaj urządzeniom IoT z indywidualnymi tokenami na ściąganie obrazu z repozytorium
* Udostępnianie zewnętrznej organizacji z uprawnieniami do określonego repozytorium 
* Ogranicz dostęp do repozytorium do różnych grup użytkowników w organizacji. Na przykład zapewniają dostęp do zapisu i odczytu do deweloperów, którzy tworzą obrazy przeznaczone do określonych repozytoriów, oraz dostęp do odczytu do zespołów, które wdrażają te repozytoria.

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Obecnie nie można przypisać uprawnień o zakresie repozytorium do tożsamości Azure Active Directory, takiej jak nazwa główna usługi lub tożsamość zarządzana.
* Nie można utworzyć mapy zakresu w rejestrze z włączonym [dostępem do ściągania](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Pojęcia

Aby skonfigurować uprawnienia do zakresu repozytorium, należy utworzyć *token* ze skojarzoną *mapą zakresu*. 

* **Token** wraz z wygenerowanym hasłem umożliwia użytkownikowi uwierzytelnianie przy użyciu rejestru. Możesz ustawić datę wygaśnięcia hasła tokenu lub wyłączyć token w dowolnym momencie.  

  Po uwierzytelnieniu przy użyciu tokenu użytkownik lub usługa może wykonać jedną lub więcej *akcji* objętych zakresem do co najmniej jednego repozytoria.

  |Akcja  |Opis  | Przykład |
  |---------|---------|--------|
  |`content/delete`    | Usuwanie danych z repozytorium  | Usuwanie repozytorium lub manifestu |
  |`content/read`     |  Odczytaj dane z repozytorium |  Ściąganie artefaktu |
  |`content/write`     |  Zapisywanie danych w repozytorium     | Używanie z programem w `content/read` celu wypchnięcia artefaktu |
  |`metadata/read`    | Odczytaj metadane z repozytorium   | Wyświetlanie listy tagów lub manifestów |
  |`metadata/write`     |  Zapisz metadane w repozytorium  | Włączanie lub wyłączanie operacji odczytu, zapisu lub usuwania |

* **Mapa zakresu** grupuje uprawnienia do repozytorium, które są stosowane do tokenu i mogą być ponownie stosowane do innych tokenów. Każdy token jest skojarzony z jedną mapą zakresu. 

   Z mapą zakresu:

    * Konfigurowanie wielu tokenów z identycznymi uprawnieniami z zestawem repozytoriów
    * Aktualizowanie uprawnień tokenu po dodaniu lub usunięciu akcji repozytorium w mapie zakresu lub zastosowaniu innej mapy zakresu 

  Azure Container Registry oferuje także kilka zdefiniowanych przez system map zakresu, które można zastosować podczas tworzenia tokenów. Uprawnienia mapowań zakresu zdefiniowane przez system mają zastosowanie do wszystkich repozytoriów w rejestrze.

Na poniższej ilustracji przedstawiono relacje między tokenami i mapami zakresów. 

![Mapowania tokenów i zakresów rejestru](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Wymagania wstępne

* **Interfejs wiersza polecenia platformy** Azure — przykłady poleceń interfejsu CLI platformy Azure w tym artykule wymagają platformy Azure w wersji 2.17.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* **Docker** — w celu uwierzytelnienia w rejestrze w celu ściągania lub wypychania obrazów konieczna jest lokalna instalacja platformy Docker. Platforma Docker dostarcza instrukcje na temat instalacji w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Rejestr kontenerów** — jeśli go nie masz, Utwórz rejestr kontenerów w warstwie Premium w ramach subskrypcji platformy Azure lub Uaktualnij istniejący rejestr. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Create token — interfejs wiersza polecenia

### <a name="create-token-and-specify-repositories"></a>Tworzenie tokenu i określanie repozytoriów

Utwórz token za pomocą polecenia [AZ ACR token Create][az-acr-token-create] . Podczas tworzenia tokenu można określić jeden lub więcej repozytoriów i skojarzonych akcji w każdym repozytorium. Repozytoria nie muszą jeszcze znajdować się w rejestrze. Aby utworzyć token przez określenie istniejącej mapy zakresu, zobacz [następną sekcję](#create-token-and-specify-scope-map).

Poniższy przykład tworzy token *w rejestrze rejestru z następującymi* uprawnieniami w `samples/hello-world` repozytorium: `content/write` i `content/read` . Domyślnie polecenie ustawia domyślny stan tokenu na `enabled` , ale w dowolnym momencie można zaktualizować stan `disabled` .

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Dane wyjściowe zawierają szczegółowe informacje o tokenie. Domyślnie są generowane dwa hasła, które nie wygasną, ale opcjonalnie można ustawić datę wygaśnięcia. Zaleca się zapisanie haseł w bezpiecznym miejscu do użycia w celu późniejszego uwierzytelniania. Hasła nie mogą zostać pobrane ponownie, ale można generować nowe.

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
> Aby ponownie wygenerować hasła tokenu i okresy ważności, zobacz Ponowne [generowanie haseł tokenu](#regenerate-token-passwords) w dalszej części tego artykułu.

Dane wyjściowe zawierają szczegóły dotyczące zakresu mapowania utworzonego polecenia. Można użyć mapy zakresu, tutaj o nazwie `MyToken-scope-map` , aby zastosować te same akcje repozytorium do innych tokenów. Można też zaktualizować mapowanie zakresu później, aby zmienić uprawnienia skojarzonych tokenów.

### <a name="create-token-and-specify-scope-map"></a>Tworzenie tokenu i określanie mapy zakresu

Alternatywnym sposobem utworzenia tokenu jest określenie istniejącej mapy zakresu. Jeśli nie masz jeszcze mapy zakresu, najpierw utwórz ją, określając repozytoria i skojarzone akcje. Następnie określ mapę zakresu podczas tworzenia tokenu. 

Aby utworzyć mapę zakresu, użyj polecenia [AZ ACR Scope-map Create][az-acr-scope-map-create] . Następujące polecenie tworzy mapę zakresu z tymi samymi uprawnieniami w `samples/hello-world` używanym wcześniej repozytorium. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Uruchom polecenie [AZ ACR token Create][az-acr-token-create] , aby utworzyć token, określając mapę zakresu *MyScopeMap* . Jak w poprzednim przykładzie polecenie ustawia domyślny stan tokenu na `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Dane wyjściowe zawierają szczegółowe informacje o tokenie. Domyślnie są generowane dwa hasła. Zaleca się zapisanie haseł w bezpiecznym miejscu do użycia w celu późniejszego uwierzytelniania. Hasła nie mogą zostać pobrane ponownie, ale można generować nowe.

> [!NOTE]
> Aby ponownie wygenerować hasła tokenu i okresy ważności, zobacz Ponowne [generowanie haseł tokenu](#regenerate-token-passwords) w dalszej części tego artykułu.

## <a name="create-token---portal"></a>Tworzenie tokenu — Portal

Za pomocą Azure Portal można tworzyć tokeny i mapy zakresów. Podobnie jak w przypadku `az acr token create` polecenia CLI, można zastosować istniejącą mapę zakresu lub utworzyć mapę zakresu podczas tworzenia tokenu przez określenie co najmniej jednego repozytoria i skojarzonych akcji. Repozytoria nie muszą jeszcze znajdować się w rejestrze. 

Poniższy przykład tworzy token i tworzy mapę zakresu z następującymi uprawnieniami w `samples/hello-world` repozytorium: `content/write` i `content/read` .

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **uprawnienia repozytorium** wybierz pozycję **tokeny (wersja zapoznawcza) > + Dodaj**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Utwórz token w portalu":::
1. Wprowadź nazwę tokenu.
1. W obszarze **Mapa zakresu** wybierz pozycję **Utwórz nowy**.
1. Skonfiguruj mapę zakresu:
    1. Wprowadź nazwę i opis mapy zakresu. 
    1. W obszarze **repozytoria** wprowadź i `samples/hello-world` w obszarze **uprawnienia** wybierz pozycję  `content/read` i `content/write` . Następnie wybierz pozycję **+ Dodaj**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Tworzenie mapy zakresu w portalu":::

    1. Po dodaniu repozytoriów i uprawnień wybierz pozycję **Dodaj** , aby dodać mapę zakresu.
1. Zaakceptuj domyślny **status** tokenu **włączone** , a następnie wybierz pozycję **Utwórz**.

Po sprawdzeniu poprawności i utworzeniu tokenu na ekranie **tokeny** pojawią się szczegóły tokenu.

### <a name="add-token-password"></a>Dodawanie hasła tokenu

Aby użyć tokenu utworzonego w portalu, należy wygenerować hasło. Można wygenerować jedno lub dwa hasła i ustawić datę wygaśnięcia dla każdej z nich. 

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **uprawnienia repozytorium** wybierz pozycję **tokeny (wersja zapoznawcza)**, a następnie wybierz token.
1. W obszarze Szczegóły tokenu wybierz pozycję **password1** lub **password2**, a następnie wybierz ikonę Generuj.
1. Na ekranie hasła opcjonalnie Ustaw datę wygaśnięcia hasła i wybierz pozycję **Generuj**. Zalecane jest ustawienie daty wygaśnięcia.
1. Po wygenerowaniu hasła skopiuj je i Zapisz w bezpiecznej lokalizacji. Nie można pobrać wygenerowanego hasła po zamknięciu ekranu, ale można wygenerować nowy.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Tworzenie hasła tokenu w portalu":::

## <a name="authenticate-with-token"></a>Uwierzytelnianie za pomocą tokenu

Gdy użytkownik lub usługa używa tokenu do uwierzytelniania w rejestrze docelowym, udostępnia nazwę tokenu jako nazwę użytkownika i jedno z wygenerowanych haseł. 

Metoda uwierzytelniania zależy od skonfigurowanej akcji lub akcji skojarzonych z tokenem.

|Akcja  |Jak uwierzytelniać  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` w interfejsie wiersza polecenia platformy Azure<br/><br/>Przykład: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` w interfejsie wiersza polecenia platformy Azure<br/><br/>Przykład: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` w interfejsie wiersza polecenia platformy Azure     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` w interfejsie wiersza polecenia platformy Azure   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` w interfejsie wiersza polecenia platformy Azure |

## <a name="examples-use-token"></a>Przykłady: Użyj tokenu

W poniższych przykładach użyto tokenu utworzonego wcześniej w tym artykule, aby wykonać typowe operacje na repozytorium: wypychanie i ściąganie obrazów, usuwanie obrazów i wyświetlanie listy tagów repozytorium. Token został początkowo skonfigurowany z uprawnieniami wypychania ( `content/write` i `content/read` akcjami) w `samples/hello-world` repozytorium.

### <a name="pull-and-tag-test-images"></a>Obrazy testów ściągania i tagów

W poniższych przykładach można ściągnąć publiczne `hello-world` i `nginx` obrazy z firmy Microsoft Container Registry i oznaczyć je jako rejestr i repozytorium.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Uwierzytelnianie przy użyciu tokenu

Uruchom `docker login` lub, `az acr login` aby uwierzytelnić się w rejestrze w celu wypychania lub ściągania obrazów. Podaj nazwę tokenu jako nazwę użytkownika i podaj jedno z jego haseł. Token musi mieć `Enabled` stan.

Poniższy przykład jest sformatowany dla powłoki bash i zawiera wartości przy użyciu zmiennych środowiskowych.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Dane wyjściowe powinny zawierać pomyślne uwierzytelnianie:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Wypychanie obrazów do rejestru

Po pomyślnym zalogowaniu się spróbuj wypchnąć otagowane obrazy do rejestru. Ponieważ token ma uprawnienia do wypychania obrazów do `samples/hello-world` repozytorium, następujące wypychanie zakończy się pomyślnie:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token nie ma uprawnień do `samples/nginx` repozytorium, więc następująca próba wypychania kończy się niepowodzeniem z powodu błędu podobnego do `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Aktualizowanie uprawnień tokenu

Aby zaktualizować uprawnienia tokenu, zaktualizuj uprawnienia w skojarzonej mapie zakresów. Zaktualizowana Mapa zakresu jest stosowana natychmiast do wszystkich skojarzonych tokenów. 

Na przykład zaktualizuj program `MyToken-scope-map` `content/write` i `content/read` Akcje w `samples/ngnx` repozytorium i Usuń `content/write` akcję z `samples/hello-world` repozytorium.  

Aby użyć interfejsu wiersza polecenia platformy Azure, uruchom polecenie [AZ ACR Scope-map Update][az-acr-scope-map-update] w celu zaktualizowania mapowania zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

W witrynie Azure Portal:

1. Przejdź do rejestru kontenerów.
1. W obszarze **uprawnienia repozytorium** wybierz pozycję **mapy zakresu (wersja zapoznawcza)**, a następnie wybierz mapę zakresu do zaktualizowania.
1. W obszarze **repozytoria** wprowadź i `samples/nginx` w obszarze **uprawnienia** wybierz pozycję `content/read` i `content/write` . Następnie wybierz pozycję **+ Dodaj**.
1. W obszarze **repozytoria** wybierz pozycję `samples/hello-world` i w obszarze **uprawnienia** Usuń zaznaczenie `content/write` . Następnie wybierz pozycję **Zapisz**.

Po zaktualizowaniu mapy zakresu następujące wypychanie zakończy się pomyślnie:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Ponieważ Mapa zakresu ma uprawnienia tylko do `content/read` `samples/hello-world` repozytorium, próba wypychania do `samples/hello-world` repozytorium kończy się niepowodzeniem:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Ściąganie obrazów z obu repozytoriów powiedzie się, ponieważ Mapa zakresu zapewnia `content/read` uprawnienia zarówno dla repozytoriów:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Usuwanie obrazów

Zaktualizuj mapę zakresu przez dodanie `content/delete` akcji do `nginx` repozytorium. Ta akcja umożliwia usunięcie obrazów w repozytorium lub usunięcie całego repozytorium.

Dla zwięzłości wyświetlamy tylko polecenie [AZ ACR Scope-map Update][az-acr-scope-map-update] w celu zaktualizowania mapy zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Aby zaktualizować mapę zakresu przy użyciu portalu, zobacz [poprzednią sekcję](#update-token-permissions).

Aby usunąć repozytorium, użyj następującego polecenia [AZ ACR Repository Delete][az-acr-repository-delete] `samples/nginx` . Aby usunąć obrazy lub repozytoria, przekaż nazwę i hasło tokenu do polecenia. W poniższym przykładzie zastosowano zmienne środowiskowe utworzone wcześniej w artykule:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Pokaż Tagi repozytorium 

Zaktualizuj mapę zakresu przez dodanie `metadata/read` akcji do `hello-world` repozytorium. Ta akcja umożliwia odczytywanie manifestu i danych tagów w repozytorium.

Dla zwięzłości wyświetlamy tylko polecenie [AZ ACR Scope-map Update][az-acr-scope-map-update] w celu zaktualizowania mapy zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Aby zaktualizować mapę zakresu przy użyciu portalu, zobacz [poprzednią sekcję](#update-token-permissions).

Aby odczytać metadane w `samples/hello-world` repozytorium, uruchom polecenie [AZ ACR Repository show-Manifests][az-acr-repository-show-manifests] lub [AZ ACR Repository show-Tags][az-acr-repository-show-tags] . 

Aby odczytać metadane, przekaż nazwę i hasło tokenu do dowolnego polecenia. W poniższym przykładzie zastosowano zmienne środowiskowe utworzone wcześniej w artykule:

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

### <a name="list-scope-maps"></a>Mapowanie listy zakresów

Aby wyświetlić listę wszystkich map zakresu skonfigurowanych w rejestrze, użyj polecenia [AZ ACR Scope-map list][az-acr-scope-map-list] lub okna **map zakresu (wersja zapoznawcza)** w portalu. Na przykład:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Dane wyjściowe składają się z trzech zdefiniowanych w systemie mapowań zakresu i innych map zakresu wygenerowanych przez użytkownika. Tokeny można skonfigurować przy użyciu dowolnego z tych map zakresu.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Pokaż szczegóły tokenu

Aby wyświetlić szczegóły tokenu, takie jak jego stan i daty wygaśnięcia hasła, uruchom polecenie [AZ ACR token show][az-acr-token-show] lub wybierz token na ekranie **tokeny (wersja zapoznawcza)** w portalu. Na przykład:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Użyj polecenia [AZ ACR token list][az-acr-token-list] lub ekranu **tokenów (wersja zapoznawcza)** w portalu, aby wyświetlić listę wszystkich tokenów skonfigurowanych w rejestrze. Na przykład:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Ponowne generowanie haseł tokenu

Jeśli nie Wygenerowano hasła tokenu lub chcesz wygenerować nowe hasła, uruchom polecenie [AZ ACR token Credential Generate][az-acr-token-credential-generate] . 

Poniższy przykład generuje nową wartość dla *password1 tokena tokena* z okresem ważności wynoszącym 30 dni. Hasło jest przechowywane w zmiennej środowiskowej `TOKEN_PWD` . Ten przykład jest sformatowany dla powłoki bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Aby użyć Azure Portal do wygenerowania hasła tokenu, zapoznaj się z instrukcjami w sekcji [Tworzenie tokenu-Portal](#create-token---portal) we wcześniejszej części tego artykułu.

### <a name="update-token-with-new-scope-map"></a>Aktualizowanie tokenu przy użyciu nowej mapy zakresu

Jeśli chcesz zaktualizować token z inną mapą zakresu, uruchom polecenie [AZ ACR token Update][az-acr-token-update] i określ nową mapę zakresu. Na przykład:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

W portalu na ekranie **tokeny (wersja zapoznawcza)** wybierz token i w obszarze **Mapa zakresu** wybierz inną mapę zakresu.

> [!TIP]
> Po zaktualizowaniu tokenu przy użyciu nowej mapy zakresu można generować nowe hasła tokenu. Użyj polecenia [AZ ACR token Credential Generate][az-acr-token-credential-generate] lub ponownie Wygeneruj hasło tokenu w Azure Portal.

## <a name="disable-or-delete-token"></a>Wyłączanie lub usuwanie tokenu

Może być konieczne tymczasowe wyłączenie poświadczeń tokenu dla użytkownika lub usługi. 

Korzystając z interfejsu wiersza polecenia platformy Azure, uruchom polecenie [AZ ACR token Update][az-acr-token-update] , aby ustawić `status` `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

W portalu wybierz token na ekranie **tokeny (wersja zapoznawcza)** , a następnie wybierz pozycję **wyłączone** w obszarze **stan**.

Aby usunąć token w celu trwałego unieważnienia dostępu przez każdego użytkownika przy użyciu jego poświadczeń, uruchom polecenie [AZ ACR token Delete][az-acr-token-delete] . 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

W portalu wybierz token na ekranie **tokeny (wersja zapoznawcza)** , a następnie wybierz pozycję **Odrzuć**.

## <a name="next-steps"></a>Następne kroki

* Aby zarządzać mapami zakresu i tokenami, użyj dodatkowych poleceń w grupach poleceń [AZ ACR Scope-map][az-acr-scope-map] i [AZ ACR token][az-acr-token] .
* Zapoznaj się z [omówieniem uwierzytelniania](container-registry-authentication.md) , aby poznać inne opcje uwierzytelniania przy użyciu usługi Azure Container Registry, w tym za pomocą tożsamości Azure Active Directory, nazwy głównej usługi lub konta administratora.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
