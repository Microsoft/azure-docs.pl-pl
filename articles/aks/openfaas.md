---
title: Korzystanie z OpenFaaS z usługą Azure Kubernetes Service (AKS)
description: Dowiedz się, jak wdrażać i używać OpenFaaS w klastrze usługi Azure Kubernetes Service (AKS) w celu kompilowania funkcji bezserwerowych za pomocą kontenerów.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747769"
---
# <a name="using-openfaas-on-aks"></a>Korzystanie z OpenFaaS na AKS

[OpenFaaS][open-faas] to struktura służąca do kompilowania funkcji bezserwerowych za pomocą kontenerów. Jako projekt Open Source uzyskano w społeczności wdrożenie na dużą skalę. Ten dokument zawiera szczegółowe informacje dotyczące instalowania i używania OpenFaas w klastrze usługi Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Podstawowe informacje o Kubernetes.
* Klaster usługi Azure Kubernetes Service (AKS) i poświadczenia AKS skonfigurowane w systemie deweloperskim.
* Interfejs wiersza polecenia platformy Azure został zainstalowany w systemie deweloperskim.
* Narzędzia wiersza polecenia usługi git zainstalowane w systemie.

## <a name="add-the-openfaas-helm-chart-repo"></a>Dodawanie repozytorium wykresu OpenFaaS Helm

Przejdź do [https://shell.azure.com](https://shell.azure.com) okna, aby otworzyć Azure Cloud Shell w przeglądarce.

OpenFaaS utrzymuje własne wykresy Helm, aby zapewnić aktualność wraz ze wszystkimi najnowszymi zmianami.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Wdróż OpenFaaS

Dobrym sposobem jest przechowywanie funkcji OpenFaaS i OpenFaaS w ich własnej przestrzeni nazw Kubernetes.

Utwórz przestrzeń nazw dla systemu OpenFaaS i funkcji:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Generuj hasło dla portalu interfejsu użytkownika OpenFaaS i interfejsu API REST:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Wartość wpisu tajnego można uzyskać za pomocą parametru `echo $PASSWORD` .

Utworzone tutaj hasło będzie używane przez wykres Helm w celu włączenia uwierzytelniania podstawowego na bramie OpenFaaS, która jest dostępna w Internecie za pomocą modułu równoważenia obciążenia w chmurze.

Wykres Helm dla OpenFaaS jest zawarty w sklonowanym repozytorium. Ten wykres służy do wdrażania OpenFaaS w klastrze AKS.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Dane wyjściowe:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Publiczny adres IP jest tworzony w celu uzyskania dostępu do bramy OpenFaaS. Aby pobrać ten adres IP, użyj polecenia [polecenia kubectl Get Service][kubectl-get] . Przypisanie adresu IP do usługi może potrwać minutę.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Rozdzielczości.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Aby przetestować system OpenFaaS, przejdź do zewnętrznego adresu IP w porcie 8080, `http://52.186.64.52:8080` w tym przykładzie. Zostanie wyświetlony monit o zalogowanie się. Aby pobrać hasło, wprowadź `echo $PASSWORD` .

![Interfejs użytkownika OpenFaaS](media/container-service-serverless/openfaas.png)

Na koniec Zainstaluj interfejs wiersza polecenia OpenFaaS. W tym przykładzie użyto rozwiązania brew, zapoznaj się z [dokumentacją interfejsu wiersza polecenia OpenFaaS][open-faas-cli] w celu uzyskania dodatkowych opcji.

```console
brew install faas-cli
```

Ustaw `$OPENFAAS_URL` na publiczny adres IP znaleziony powyżej.

Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Utwórz pierwszą funkcję

Teraz, gdy OpenFaaS działa, Utwórz funkcję przy użyciu portalu OpenFaas.

Kliknij pozycję **Wdróż nową funkcję** i Wyszukaj **FIGlet** . Wybierz funkcję FIGlet, a następnie kliknij pozycję **Wdróż** .

![Zrzut ekranu przedstawia okno dialogowe Wdróż nową funkcję z tekstem FIGlet w wierszu wyszukiwania.](media/container-service-serverless/figlet.png)

Użyj zwinięciea, aby wywołać funkcję. W poniższym przykładzie Zastąp adres IP używany przez bramę OpenFaas.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Dane wyjściowe:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Create — Druga funkcja

Teraz Utwórz drugą funkcję. Ten przykład zostanie wdrożony przy użyciu interfejsu wiersza polecenia OpenFaaS i zawiera niestandardowy obraz kontenera oraz pobranie danych z Cosmos DB. Przed utworzeniem funkcji należy skonfigurować kilka elementów.

Najpierw utwórz nową grupę zasobów dla Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Wdróż wystąpienie CosmosDB rodzaju `MongoDB` . Wystąpienie musi mieć unikatową nazwę i aktualizować `openfaas-cosmos` ją do swojego środowiska.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Pobierz parametry połączenia z bazą danych Cosmos i Zapisz ją w zmiennej.

Zaktualizuj wartość `--resource-group` argumentu do nazwy grupy zasobów, a `--name` argument na nazwę Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Teraz Wypełnij Cosmos DB danymi testowymi. Utwórz plik o nazwie `plans.json` i skopiuj w poniższym kodzie JSON.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Użyj narzędzia *mongoimport* , aby załadować wystąpienie CosmosDB z danymi.

W razie konieczności Zainstaluj narzędzia MongoDB. Poniższy przykład instaluje te narzędzia za pomocą rozwiązania brew, zapoznaj się z [dokumentacją MongoDB][install-mongo] w celu uzyskania innych opcji.

```console
brew install mongodb
```

Załaduj dane do bazy danych programu.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Dane wyjściowe:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Uruchom następujące polecenie, aby utworzyć funkcję. Zaktualizuj wartość `-g` argumentu przy użyciu adresu bramy OpenFaaS.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Po wdrożeniu należy zobaczyć nowo utworzony punkt końcowy OpenFaaS dla funkcji.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Przetestuj funkcję przy użyciu zwinięcia. Zaktualizuj adres IP przy użyciu adresu bramy OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Dane wyjściowe:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Możesz również przetestować funkcję w interfejsie użytkownika OpenFaaS.

![tekst alternatywny](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Następne kroki

Możesz w dalszym ciągu dowiedzieć się więcej na temat warsztatów OpenFaaS za pomocą zestawu praktycznych laboratoriów, na przykład jak utworzyć własny bot GitHub, zużywać wpisy tajne, wyświetlać metryki i skalowanie automatyczne.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
