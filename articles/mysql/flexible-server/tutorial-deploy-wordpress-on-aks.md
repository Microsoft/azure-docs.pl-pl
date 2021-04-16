---
title: 'Samouczek: wdrażanie rozwiązania WordPress w klastrze usługi AKS za pomocą serwera elastycznego MySQL przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak szybko skompilować i wdrożyć platformę WordPress w u Azure Database for MySQL — elastyczny serwer.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b631173ed92905870e73e6c560d90aab08476ce1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480158"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Samouczek: wdrażanie aplikacji WordPress w u usługach AKS Azure Database for MySQL — elastyczny serwer

W tym przewodniku Szybki start wdrożysz aplikację WordPress w klastrze usługi Azure Kubernetes Service (AKS) za pomocą usługi Azure Database for MySQL — elastyczny serwer (wersja zapoznawcza) przy użyciu interfejsu wiersza polecenia platformy Azure. 
**[AKS](../../aks/intro-kubernetes.md)** to zarządzana usługa Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. **[Azure Database for MySQL — serwer elastyczny (wersja zapoznawcza)](overview.md)** to w pełni zarządzana usługa bazy danych zaprojektowana w celu zapewnienia bardziej szczegółowej kontroli i elastyczności funkcji zarządzania bazami danych i ustawień konfiguracji. Obecnie serwer elastyczny jest w wersji zapoznawczej.

> [!NOTE]
> - Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej
> - W tym przewodniku Szybki start założono, że rozumiesz pojęcia związane z platformą Kubernetes, platformą WordPress i bazą danych MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga najnowszej wersji interfejsu wiersza polecenia platformy Azure. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

> [!NOTE]
> Jeśli polecenia w tym przewodniku Szybki start są uruchamiane lokalnie (zamiast Azure Cloud Shell), upewnij się, że polecenia są uruchamiane jako administrator.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórzmy grupę zasobów *wordpress-project* przy użyciu polecenia [az group create][az-group-create] w *lokalizacji eastus.*

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Lokalizacja grupy zasobów to lokalizacja przechowywania metadanych grupy zasobów. Jest to również miejsce, w którym zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów.

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

Utwórz klaster AKS za pomocą polecenia [az aks create](/cli/azure/aks#az-aks-create). W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Potrwa to kilka minut.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

> [!NOTE]
> Podczas tworzenia klastra usługi AKS automatycznie tworzona jest druga grupa zasobów do przechowywania zasobów usługi AKS. Zobacz [Dlaczego dwie grupy zasobów są tworzone za pomocą aKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli](/cli/azure/aks#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials). To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> Powyższe polecenie używa domyślnej lokalizacji pliku konfiguracji [kubernetes,](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)czyli `~/.kube/config` . Możesz określić inną lokalizację dla pliku konfiguracji kubernetes przy użyciu *opcji --file*.

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), aby powrócić do listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe zawierają jeden węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła to *Gotowy*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Tworzenie serwera Azure Database for MySQL — elastyczny serwer
Utwórz serwer elastyczny za pomocą [polecenia az mysql flexible-server create.](/cli/azure/mysql/flexible-server) Następujące polecenie tworzy serwer przy użyciu wartości domyślnych i wartości usługi z lokalnego kontekstu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Utworzony serwer ma poniższe atrybuty:
- Nowa pusta baza danych jest ```flexibleserverdb``` tworzona podczas pierwszej aprowizowana serwera. W tym przewodniku Szybki start użyjemy tej bazy danych.
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwa grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji co grupa zasobów
- Wartości domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (z możliwością serii), rozmiar obliczeniowy/jednostkę SKU (B1MS), okres przechowywania kopii zapasowych (7 dni) i wersja programu MySQL (5.7)
- Użycie argumentu dostępu publicznego umożliwia utworzenie serwera z dostępem publicznym chronionym przez reguły zapory. Podając swój adres IP, aby dodać regułę zapory zezwalając na dostęp z komputera klienckiego.
- Ponieważ polecenie używa kontekstu lokalnego, utworzy serwer w grupie zasobów i ```wordpress-project``` w regionie ```eastus``` .


### <a name="build-your-wordpress-docker-image"></a>Tworzenie obrazu platformy Docker wordpress

Pobierz [najnowszą wersję rozwiązania WordPress.](https://wordpress.org/download/) Utwórz nowy katalog ```my-wordpress-app``` dla projektu i użyj tej prostej struktury folderów

```
â””â”€â”€â”€my-wordpress-app
    â””â”€â”€â”€public
        â”œâ”€â”€â”€wp-admin
        â”‚   â”œâ”€â”€â”€css
        . . . . . . .
        â”œâ”€â”€â”€wp-content
        â”‚   â”œâ”€â”€â”€plugins
        . . . . . . .
        â””â”€â”€â”€wp-includes
        . . . . . . .
        â”œâ”€â”€â”€wp-config-sample.php
        â”œâ”€â”€â”€index.php
        . . . . . . .
    â””â”€â”€â”€ Dockerfile

```


Zmień ```wp-config-sample.php```  nazwę na i zastąp wiersze od ```wp-config.php``` 21 do 32 tym fragmentem kodu. Poniższy fragment kodu odczytuje hosta bazy danych, nazwę użytkownika i hasło z pliku manifestu kubernetes.

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
Utwórz nowy plik Dockerfile i skopiuj ten fragment kodu. Ten plik Dockerfile umożliwia skonfigurowanie serwera internetowego Apache przy użyciu języka PHP i włączenie rozszerzenia mysqli.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Kompilowanie obrazu platformy Docker
Upewnij się, że jesteś w katalogu ```my-wordpress-app``` w terminalu, używając ```cd``` polecenia . Uruchom następujące polecenie, aby skompilować obraz:

``` bash

docker build --tag myblog:latest .

```

Wdrażanie obrazu w [usłudze Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) lub [Azure Container Registry.](../../container-registry/container-registry-get-started-azure-cli.md)

> [!IMPORTANT]
>Jeśli używasz usługi Azure Container regdistry (ACR), uruchom polecenie , aby dołączyć konto ```az aks update``` usługi ACR do klastra usługi AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Tworzenie pliku manifestu kubernetes

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. Utwórzmy plik manifestu o nazwie `mywordpress.yaml` i skopiujmy go w poniższej definicji YAML.

>[!IMPORTANT]
> - Zastąp wartość rzeczywistą nazwą obrazu platformy Docker i tagiem platformy ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` Docker platformy WordPress, na przykład ```docker-hub-user/myblog:latest``` .
> - Zaktualizuj ```env``` sekcję poniżej przy użyciu ```SERVERNAME``` serwera ```YOUR-DATABASE-USERNAME``` elastycznego ```YOUR-DATABASE-PASSWORD``` MySQL , .

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

## <a name="deploy-wordpress-to-aks-cluster"></a>Wdrażanie rozwiązania WordPress w klastrze usługi AKS
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

Początkowo adres *EXTERNAL-IP dla* usługi *wordpress-blog* jest wyświetlany *jako* oczekujący.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Przeglądanie witryny WordPress

Otwórz w przeglądarce internetowej zewnętrzny adres IP usługi, aby wyświetlić stronę instalacji rozwiązania WordPress.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Powodzenie instalacji programu Wordpress na serwerze elastycznym usług AKS i MySQL":::

>[!NOTE]
> - Obecnie witryna WordPress nie używa protokołu HTTPS. Zaleca się włączenie [obsługi TLS z własnymi certyfikatami.](../../aks/ingress-own-tls.md)
> - Routing [HTTP można włączyć](../../aks/http-application-routing.md) dla klastra.

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Aby uniknąć opłat za platformę Azure, należy wyczyścić niepotrzebnych zasobów.  Gdy klaster nie będzie już potrzebny, usuń grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)](../../aks/kubernetes-service-principal.md#additional-considerations). Jeśli używasz tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak uzyskać dostęp do internetowego pulpitu nawigacyjnego [usługi Kubernetes](../../aks/kubernetes-dashboard.md) dla klastra usługi AKS
- Dowiedz się, jak [skalować klaster](../../aks/tutorial-kubernetes-scale.md)
- Dowiedz się, jak zarządzać serwerem [elastycznym MySQL](./quickstart-create-server-cli.md)
- Dowiedz się, [jak skonfigurować parametry serwera](./how-to-configure-server-parameters-cli.md) dla serwera bazy danych.

