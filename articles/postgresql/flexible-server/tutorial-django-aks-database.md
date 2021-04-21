---
title: 'Samouczek: wdrażanie platformy Django w klastrze AKS za pomocą serwera elastycznego PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak szybko skompilować i wdrożyć platformę Django w u Azure Database for PostgreSQL — elastyczny serwer.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9315e6fd7dd9880d20108e3f0ed28cd32904f1a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791539"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Samouczek: wdrażanie aplikacji Django w u usługach AKS Azure Database for PostgreSQL — elastyczny serwer

W tym przewodniku Szybki start wdrożysz aplikację Django w klastrze usługi Azure Kubernetes Service (AKS) za Azure Database for PostgreSQL — elastyczny serwer (wersja zapoznawcza) przy użyciu interfejsu wiersza polecenia platformy Azure.

**[AKS](../../aks/intro-kubernetes.md)** to zarządzana usługa Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. Azure Database for PostgreSQL — serwer elastyczny **[(wersja zapoznawcza)](overview.md)** to w pełni zarządzana usługa bazy danych zaprojektowana w celu zapewnienia bardziej szczegółowej kontroli i elastyczności funkcji zarządzania bazami danych i ustawień konfiguracji.

> [!NOTE]
> - Azure Database for PostgreSQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej
> - W tym przewodniku Szybki start założono, że rozumiesz pojęcia związane z platformą Kubernetes, platformą Django i bazą danych PostgreSQL.

## <a name="pre-requisites"></a>Wymagania wstępne
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Skorzystaj z usługi [Azure Cloud Shell](../../cloud-shell/quickstart.md) za pośrednictwem środowiska bash.

   [![Uruchamianie osadzane](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)  
- Jeśli wolisz, zainstaluj [interfejs wiersza polecenia](/cli/azure/install-azure-cli) platformy Azure, aby uruchamiać polecenia referencyjne interfejsu wiersza polecenia.
  - Jeśli korzystasz z instalacji lokalnej, zaloguj się za pomocą polecenia [az login](/cli/azure/reference-index#az_login) interfejsu wiersza polecenia platformy Azure.  Aby ukończyć proces uwierzytelniania, wykonaj kroki wyświetlane w terminalu.  Dodatkowe opcje logowania opisano w sekcji [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).
  - Po wyświetleniu monitu przy pierwszym użyciu zainstaluj rozszerzenia interfejsu wiersza polecenia platformy Azure.  Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Korzystanie z rozszerzeń w interfejsie wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).
  - Uruchom polecenie [az version](/cli/azure/reference-index?#az_version), aby znaleźć zainstalowane wersje i biblioteki zależne. Aby uaktualnić do najnowszej wersji, uruchom polecenie [az upgrade](/cli/azure/reference-index?#az_upgrade). Ten artykuł wymaga najnowszej wersji interfejsu wiersza polecenia platformy Azure. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

> [!NOTE]
> Jeśli polecenia w tym przewodniku Szybki start są uruchamiane lokalnie (zamiast Azure Cloud Shell), upewnij się, że polecenia są uruchamiane jako administrator.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórzmy grupę zasobów *django-project* przy użyciu polecenia [az group create][az-group-create] w *lokalizacji eastus.*

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> Lokalizacja grupy zasobów to lokalizacja przechowywania metadanych grupy zasobów. Jest to również miejsce, w którym zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów.

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzoną grupę zasobów:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Tworzenie klastra AKS

Utwórz klaster AKS za pomocą polecenia [az aks create](/cli/azure/aks#az_aks_create). W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Potrwa to kilka minut.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

> [!NOTE]
> Podczas tworzenia klastra usługi AKS automatycznie tworzona jest druga grupa zasobów do przechowywania zasobów usługi AKS. Zobacz [Dlaczego dwie grupy zasobów są tworzone za pomocą aKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/). Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

> [!NOTE]
> Powyższe polecenie używa domyślnej lokalizacji dla pliku konfiguracji [kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), czyli `~/.kube/config` . Możesz określić inną lokalizację dla pliku konfiguracji kubernetes przy użyciu *opcji --file*.

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get), aby powrócić do listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe zawierają jeden węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła to *Gotowy*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Tworzenie serwera Azure Database for PostgreSQL — elastyczny serwer
Utwórz serwer elastyczny za pomocą [polecenia az postgreSQL flexible-server create.](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) Następujące polecenie tworzy serwer przy użyciu wartości domyślnych i wartości usługi z kontekstu lokalnego interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Utworzony serwer ma poniższe atrybuty:
- Nowa pusta baza danych ```postgres``` jest tworzona podczas pierwszej aprowrowi serwera. W tym przewodniku Szybki start użyjemy tej bazy danych.
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwa grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji co grupa zasobów
- Wartości domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (Ogólnego przeznaczenia), rozmiar obliczeniowy/jednostkę SKU (Standard_D2s_v3, która używa 2 rdzenie), okres przechowywania kopii zapasowych (7 dni) i wersja PostgreSQL (12)
- Użycie argumentu dostępu publicznego umożliwia utworzenie serwera z dostępem publicznym chronionym przez reguły zapory. Podając swój adres IP, aby dodać regułę zapory, aby zezwolić na dostęp z komputera klienckiego.
- Ponieważ polecenie używa kontekstu lokalnego, utworzy serwer w grupie zasobów i ```django-project``` w regionie ```eastus``` .


## <a name="build-your-django-docker-image"></a>Kompilowanie obrazu platformy Docker django

Utwórz nową aplikację [Django lub](https://docs.djangoproject.com/en/3.1/intro/) użyj istniejącego projektu Django. Upewnij się, że kod znajduje się w tej strukturze folderów.

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
          . . . . . . .
    ├───static
         . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Zaktualizuj w programie , aby upewnić się, że aplikacja Django używa zewnętrznego adresu IP, który ```ALLOWED_HOSTS``` ```settings.py``` jest przypisywany do aplikacji kubernetes.

```python
ALLOWED_HOSTS = ['*']
```

Zaktualizuj ```DATABASES={ }``` sekcję w ```settings.py```  pliku . Poniższy fragment kodu odczytuje host bazy danych, nazwę użytkownika i hasło z pliku manifestu kubernetes.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Generowanie requirements.txt plików
Utwórz ```requirements.txt``` plik, aby wyświetlić listę zależności dla aplikacji Django. Oto ```requirements.txt``` przykładowy plik. Możesz użyć [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) do wygenerowania requirements.txt pliku dla istniejącej aplikacji.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Tworzenie pliku Dockerfile
Utwórz nowy plik o nazwie ```Dockerfile``` i skopiuj poniższy fragment kodu. Ten plik Dockerfile podczas konfigurowania języka Python 3.8 i instalowania wszystkich wymagań wymienionych w requirements.txt pliku.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Kompilowanie obrazu
Upewnij się, że jesteś w katalogu ```my-django-app``` w terminalu, używając ```cd``` polecenia . Uruchom następujące polecenie, aby skompilować obraz tablicy biuletynowej:

``` bash

docker build --tag myblog:latest .

```

Wd wdrażaj obraz [w usłudze Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) [lub Azure Container Registry.](../../container-registry/container-registry-get-started-azure-cli.md)

> [!IMPORTANT]
>Jeśli używasz usługi Azure Container regdistry (ACR), uruchom polecenie , aby dołączyć konto ```az aks update``` usługi ACR do klastra usługi AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Tworzenie pliku manifestu kubernetes

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. Utwórzmy plik manifestu o nazwie ```djangoapp.yaml``` i skopiujmy go w poniższej definicji YAML.

>[!IMPORTANT]
> - Zastąp ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` wartość rzeczywistą nazwą obrazu platformy Docker django i tagiem, na przykład ```docker-hub-user/myblog:latest``` .
> - Zaktualizuj ```env``` sekcję poniżej przy użyciu ```SERVERNAME``` serwera ```YOUR-DATABASE-USERNAME``` elastycznego ```YOUR-DATABASE-PASSWORD``` postgres , .


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Wdrażanie rozwiązania Django w klastrze usługi AKS
Wdróż aplikację przy użyciu polecenia [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) i podaj nazwę manifestu YAML:

```console
kubectl apply -f djangoapp.yaml
```

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzone wdrożenia i usługi:

```output
deployment "django-app" created
service "python-svc" created
```

Wdrożenie umożliwia opis szczegółów wdrożenia, takich jak obrazy do użycia dla aplikacji, liczba zasobników i ```django-app``` konfiguracja zasobników. Usługa jest ```python-svc``` tworzona w celu uwidoczninia aplikacji za pośrednictwem zewnętrznego adresu IP.

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) z argumentem `--watch`.

```azurecli-interactive
kubectl get service django-app --watch
```

Początkowo adres *EXTERNAL-IP dla* *usługi django-app* service jest wyświetlany jako oczekujący . 

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Teraz otwórz w przeglądarce internetowej zewnętrzny adres IP twojej usługi w widoku aplikacji Django.  

>[!NOTE]
> - Obecnie witryna Django nie używa protokołu HTTPS. Zaleca się włączenie [obsługi TLS przy użyciu własnych certyfikatów.](../../aks/ingress-own-tls.md)
> - Routing [HTTP można włączyć](../../aks/http-application-routing.md) dla klastra. Po włączeniu routingu HTTP konfiguruje kontroler ruchu przychodzących w klastrze usługi AKS. Podczas wdrażania aplikacji rozwiązanie tworzy również publicznie dostępne nazwy DNS dla punktów końcowych aplikacji.

## <a name="run-database-migrations"></a>Uruchamianie migracji bazy danych

W przypadku dowolnej aplikacji django należy uruchomić migrację bazy danych lub zebrać pliki statyczne. Te polecenia powłoki django można uruchomić przy użyciu polecenia ```$ kubectl exec <pod-name> -- [COMMAND]``` .  Przed uruchomieniem polecenia należy znaleźć nazwę zasobnika przy użyciu polecenia ```kubectl get pods``` . 

```bash
$ kubectl get pods
```

Zostaną wyświetlony dane wyjściowe podobne do tych
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Po znalezionej nazwie zasobnika możesz uruchomić migracje bazy danych django za pomocą polecenia ```$ kubectl exec <pod-name> -- [COMMAND]``` . Uwaga: ```/code/``` katalog roboczy projektu został w powyższym ```Dockerfile``` zdefiniowaniu.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

Dane wyjściowe będą wyglądać jak 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Jeśli wystąpią problemy, uruchom program , aby zobaczyć, jaki ```kubectl logs <pod-name>```  wyjątek jest zgłaszany przez aplikację. Jeśli aplikacja działa prawidłowo, podczas uruchamiania programu zobaczysz dane wyjściowe podobne do ```kubectl logs``` tych.

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Aby uniknąć nalicznych opłat za platformę Azure, należy wyczyścić niepotrzebnych zasobów.  Gdy klaster nie będzie już potrzebny, usuń grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)](../../aks/kubernetes-service-principal.md#additional-considerations). Jeśli używasz tożsamości zarządzanej, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [uzyskać dostęp do internetowego pulpitu nawigacyjnego usługi Kubernetes](../../aks/kubernetes-dashboard.md) dla klastra usługi AKS
- Dowiedz się, jak [włączyć ciągłe wdrażanie](../../aks/deployment-center-launcher.md)
- Dowiedz się, jak [skalować klaster](../../aks/tutorial-kubernetes-scale.md)
- Dowiedz się, jak zarządzać serwerem [elastycznym postgres](./quickstart-create-server-cli.md)
- Dowiedz się, [jak skonfigurować parametry serwera](./howto-configure-server-parameters-using-cli.md) dla serwera bazy danych.
