---
title: 'Samouczek: wdrażanie aplikacji WordPress w klastrze AKS z elastycznym serwerem MySQL przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak szybko tworzyć i wdrażać platformy WordPress w systemie AKS przy użyciu serwera Azure Database for MySQL-elastycznie.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: a02cb30b0f00f732fa0c4ac9319a652ef5cb6fc1
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657060"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Samouczek: wdrażanie aplikacji WordPress w systemie AKS z serwerem elastycznym Azure Database for MySQL

W tym przewodniku szybki start wdrożono aplikację WordPress w klastrze usługi Azure Kubernetes Service (AKS) z serwerem elastycznym Azure Database for MySQL (wersja zapoznawcza) przy użyciu interfejsu wiersza polecenia platformy Azure. 
**[AKS](../../aks/intro-kubernetes.md)** to zarządzana usługa Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. **[Azure Database for MySQL — elastyczny serwer (wersja zapoznawcza)](overview.md)** to w pełni zarządzana usługa bazy danych, która umożliwia dokładniejszą kontrolę i elastyczność w zakresie funkcji zarządzania bazami danych i ustawień konfiguracji. Obecnie elastyczny serwer jest w wersji zapoznawczej.

> [!NOTE]
> - Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej
> - W tym przewodniku szybki start założono podstawową wiedzę na temat koncepcji Kubernetes, WordPress i MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- W tym artykule jest wymagana Najnowsza wersja interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

> [!NOTE]
> W przypadku uruchamiania poleceń w tym przewodniku szybki start lokalnie (zamiast Azure Cloud Shell) Upewnij się, że polecenia są uruchamiane jako administrator.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórzmy grupę zasobów, *WordPress-Project* przy użyciu polecenia [AZ Group Create] [AZ-Group-Create] w lokalizacji *Wschodnie* .

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Lokalizacja grupy zasobów to miejsce, w którym przechowywane są metadane grupy zasobów. W przypadku, gdy zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobu.

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzoną grupę zasobów:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Tworzenie klastra AKS

Utwórz klaster AKS za pomocą polecenia [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create). W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Wykonanie tej czynności potrwa kilka minut.

```azurecli-interactive
az aks create --resource-group wordpress-project --name wordpresscluster--node-count 1 --generate-ssh-keys
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

> [!NOTE]
> Podczas tworzenia klastra AKS zostaje automatycznie utworzona druga grupa zasobów do przechowywania zasobów AKS. Zobacz, [dlaczego dwie grupy zasobów utworzono za pomocą AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials). To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name wordpresscluster
```

> [!NOTE]
> Powyższe polecenie używa domyślnej lokalizacji [pliku konfiguracji Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) `~/.kube/config` . Możesz określić inną lokalizację pliku konfiguracji Kubernetes za pomocą polecenia *--File*.

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), aby powrócić do listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe zawierają jeden węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła to *Gotowy*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Utwórz serwer elastyczny Azure Database for MySQL
Utwórz elastyczny serwer przy użyciu polecenia [AZ MySQL elastyczny-Server Create](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true). Następujące polecenie tworzy serwer przy użyciu wartości domyślnych usługi i z lokalnego kontekstu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Utworzony serwer ma następujące atrybuty:
- Nowa pusta baza danych ```flexibleserverdb``` jest tworzona podczas pierwszego aprowizacji serwera. W tym przewodniku szybki start użyjemy tej bazy danych.
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwa grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji, w której znajduje się grupa zasobów
- Ustawienia domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (z możliwością przetwarzania), rozmiar obliczeń/jednostka SKU (B1MS), okres przechowywania kopii zapasowych (7 dni) i wersja programu MySQL (5,7)
- Użycie argumentu dostęp publiczny umożliwia utworzenie serwera z dostępem publicznym chronionych przez reguły zapory. Podając swój adres IP, aby dodać regułę zapory, aby zezwolić na dostęp z komputera klienckiego.
- Ponieważ polecenie używa kontekstu lokalnego, utworzy serwer w grupie zasobów ```wordpress-project``` i w regionie ```eastus``` .


### <a name="build-your-wordpress-docker-image"></a>Kompilowanie obrazu platformy Docker WordPress

Pobierz najnowszą wersję oprogramowania [WordPress](https://wordpress.org/download/) . Utwórz nowy katalog ```my-wordpress-app``` dla projektu i Użyj tej prostej struktury folderów

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Zmień nazwę ```wp-config-sample.php```  na ```wp-config.php``` i Zastąp wiersze 21 do 32 za pomocą tego fragmentu kodu. Poniższy fragment kodu odczytuje hosta bazy danych, nazwę użytkownika i hasło z pliku manifestu Kubernetes.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Tworzenie pliku Dockerfile
Utwórz nowy pliku dockerfile i skopiuj ten fragment kodu. To pliku dockerfile w konfigurowaniu serwera Apache Web Server przy użyciu języka PHP i włączeniu rozszerzenia mysqli.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Kompilowanie obrazu platformy Docker
Upewnij się, że jesteś w katalogu ```my-wordpress-app``` w terminalu przy użyciu ```cd``` polecenia. Uruchom następujące polecenie, aby skompilować obraz:

``` bash

docker build --tag myblog:latest .

```

Wdróż obraz w usłudze [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) lub [usłudze Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Jeśli korzystasz z usługi Azure Container regdistry (ACR), uruchom ```az aks update``` polecenie w celu dołączenia konta ACR z klastrem AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Utwórz plik manifestu Kubernetes

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. Utwórzmy plik manifestu o nazwie `mywordpress.yaml` i skopiujesz go w następującej definicji YAML.

>[!IMPORTANT]
> - Zamień na na ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` przykład rzeczywistą nazwę i tag obrazu platformy Docker ```docker-hub-user/myblog:latest``` .
> - Zaktualizuj ```env``` sekcję poniżej, w której znajduje się ```SERVERNAME``` ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` serwer elastyczny MySQL.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>Wdrażanie platformy WordPress w klastrze AKS
Wdróż aplikację przy użyciu polecenia [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) i podaj nazwę manifestu YAML:

```console
kubectl apply -f mywordpress.yaml
```

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzone wdrożenia i usługi:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) z argumentem `--watch`.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Początkowo *zewnętrzny adres IP* dla usługi *WordPress-blog* jest pokazywany jako *oczekujący*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Przeglądaj witrynę WordPress

Otwórz przeglądarkę internetową na zewnętrzny adres IP usługi, aby wyświetlić stronę instalacji WordPress.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Pomyślne zainstalowanie oprogramowania WordPress na serwerze elastycznym AKS i MySQL":::

>[!NOTE]
> - Obecnie witryna WordPress nie korzysta z protokołu HTTPS. Zaleca się [włączenie protokołu TLS z własnymi certyfikatami](../../aks/ingress-own-tls.md).
> - [Routing http](../../aks/http-application-routing.md) można włączyć dla klastra.

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Aby uniknąć naliczania opłat za platformę Azure, należy wyczyścić zasoby niepotrzebne.  Gdy klaster nie będzie już potrzebny, usuń grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete).

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)](../../aks/kubernetes-service-principal.md#additional-considerations). Jeśli używana jest tożsamość zarządzana, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak uzyskać dostęp do pulpitu nawigacyjnego sieci Web Kubernetes](../../aks/kubernetes-dashboard.md) dla klastra AKS
- Dowiedz się, jak [skalować klaster](../../aks/tutorial-kubernetes-scale.md)
- Dowiedz się, jak zarządzać [serwerem elastycznym MySQL](./quickstart-create-server-cli.md)
- Dowiedz się, jak [skonfigurować parametry serwera](./how-to-configure-server-parameters-cli.md) dla serwera bazy danych.

