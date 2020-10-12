---
title: Uruchamianie zadania Apache Spark za pomocą usługi Azure Kubernetes Service (AKS)
description: Użyj usługi Azure Kubernetes Service (AKS), aby utworzyć i uruchomić zadanie Apache Spark na potrzeby przetwarzania danych na dużą skalę.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 074e3db3234794aa891d5452b0c19060193c6d0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243974"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Uruchamianie Apache Spark zadań w AKS

[Apache Spark][apache-spark] to szybki aparat do przetwarzania danych na dużą skalę. W przypadku [wersji Spark 2.3.0][spark-kubernetes-earliest-version]program Apache Spark obsługuje natywną integrację z klastrami Kubernetes. Usługa Azure Kubernetes Service (AKS) to zarządzane środowisko Kubernetes uruchomione na platformie Azure. Ten dokument zawiera szczegółowe informacje na temat przygotowywania i uruchamiania Apache Spark zadań w klastrze usługi Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Podstawowe informacje na temat Kubernetes i [Apache Spark][spark-quickstart].
* Konto [centrum platformy Docker][docker-hub] lub [Azure Container Registry][acr-create].
* Interfejs wiersza polecenia platformy Azure został [zainstalowany][azure-cli] w systemie deweloperskim.
* [JDK 8][java-install] zainstalowany w systemie.
* [Apache Maven][maven-install] zainstalowany w systemie.
* SBT ([Narzędzie Scala Build][sbt-install]) zainstalowane w systemie.
* Narzędzia wiersza polecenia usługi git zainstalowane w systemie.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Platforma Spark jest używana do przetwarzania danych na dużą skalę i wymaga, aby węzły Kubernetes miały rozmiar zgodny z wymaganiami dotyczącymi zasobów platformy Spark. Zalecamy minimalny rozmiar `Standard_D3_v2` węzłów usługi Azure Kubernetes Service (AKS).

Jeśli potrzebujesz klastra AKS, który spełnia to minimalne zalecenie, uruchom następujące polecenia.

Utwórz grupę zasobów dla klastra.

```azurecli
az group create --name mySparkCluster --location eastus
```

Utwórz nazwę główną usługi dla klastra. Po jego utworzeniu do następnego polecenia potrzebny będzie identyfikator appId i hasło jednostki usługi.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Utwórz klaster AKS z węzłami o rozmiarze `Standard_D3_v2` , a wartości identyfikatora AppID i hasła przechodzą jako parametry podmiotu zabezpieczeń i klucza klienta.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Nawiąż połączenie z klastrem AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Jeśli używasz Azure Container Registry (ACR) do przechowywania obrazów kontenerów, Skonfiguruj uwierzytelnianie między AKS i ACR. Zapoznaj się z [dokumentacją dotyczącą uwierzytelniania ACR][acr-aks] .

## <a name="build-the-spark-source"></a>Kompiluj Źródło platformy Spark

Przed uruchomieniem zadań platformy Spark w klastrze AKS należy skompilować kod źródłowy Spark i spakować go do obrazu kontenera. Źródło Spark zawiera skrypty, których można użyć do ukończenia tego procesu.

Sklonuj repozytorium projektów platformy Spark do systemu deweloperskiego.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Przejdź do katalogu sklonowanego repozytorium i Zapisz ścieżkę źródła Spark do zmiennej.

```bash
cd spark
sparkdir=$(pwd)
```

Jeśli masz zainstalowaną wiele wersji JDK, ustaw opcję `JAVA_HOME` na Użyj wersji 8 dla bieżącej sesji.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Uruchom następujące polecenie, aby skompilować kod źródłowy Spark z obsługą Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Poniższe polecenia umożliwiają utworzenie obrazu kontenera platformy Spark i wypchnięcie go do rejestru obrazów kontenerów. Zastąp ciąg `registry.example.com` nazwą rejestru kontenerów i `v1` znacznikiem, którego wolisz użyć. W przypadku korzystania z usługi Docker Hub ta wartość jest nazwą rejestru. Jeśli jest używany Azure Container Registry (ACR), ta wartość jest nazwą serwera logowania ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Wypchnij obraz kontenera do rejestru obrazu kontenera.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Przygotowywanie zadania platformy Spark

Następnie przygotuj zadanie Spark. Plik JAR jest używany do przechowywania zadania platformy Spark i jest wymagany podczas uruchamiania `spark-submit` polecenia. Plik JAR można udostępnić za pomocą publicznego adresu URL lub wstępnie spakowanego w ramach obrazu kontenera. W tym przykładzie jest tworzony przykładowy plik JAR służący do obliczania wartości pi. Ten plik JAR jest następnie przekazywany do usługi Azure Storage. Jeśli masz już istniejący plik JAR, możesz go zastąpić

Utwórz katalog, w którym chcesz utworzyć projekt dla zadania platformy Spark.

```bash
mkdir myprojects
cd myprojects
```

Utwórz nowy projekt Scala na podstawie szablonu.

```bash
sbt new sbt/scala-seed.g8
```

Po wyświetleniu monitu wprowadź `SparkPi` nazwę projektu.

```bash
name [Scala Seed Project]: SparkPi
```

Przejdź do nowo utworzonego katalogu projektu.

```bash
cd sparkpi
```

Uruchom następujące polecenia, aby dodać wtyczkę SBT, która umożliwia pakowanie projektu jako pliku JAR.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Uruchom te polecenia, aby skopiować przykładowy kod do nowo utworzonego projektu i dodać wszystkie wymagane zależności.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Aby spakować projekt do jar, uruchom następujące polecenie.

```bash
sbt assembly
```

Po pomyślnym wykonaniu pakowania powinny zostać wyświetlone dane wyjściowe podobne do następujących.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopiuj zadanie do magazynu

Utwórz konto usługi Azure Storage i kontener do przechowywania pliku JAR.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Przekaż plik jar do konta usługi Azure Storage za pomocą następujących poleceń.

```azurecli
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Zmienna `jarUrl` zawiera teraz publicznie dostępną ścieżkę do pliku JAR.

## <a name="submit-a-spark-job"></a>Przesyłanie zadania platformy Spark

Uruchom polecenia-proxy w osobnym wierszu polecenia z poniższym kodem.

```bash
kubectl proxy
```

Przejdź z powrotem do katalogu głównego repozytorium Spark.

```bash
cd $sparkdir
```

Utwórz konto usługi, które ma wystarczające uprawnienia do uruchamiania zadania.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Prześlij zadanie przy użyciu `spark-submit` .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Ta operacja uruchamia zadanie Spark, które przesyła strumieniowo stan zadania do sesji powłoki. Gdy zadanie jest uruchomione, można zobaczyć sterowniki platformy Spark pod i moduł uruchamiający, korzystając z polecenia polecenia kubectl GetBinding. Otwórz drugą sesję terminala, aby uruchomić te polecenia.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Gdy zadanie jest uruchomione, można także uzyskać dostęp do interfejsu użytkownika Spark. W drugiej sesji terminalu Użyj `kubectl port-forward` polecenia, aby zapewnić dostęp do interfejsu użytkownika Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Aby uzyskać dostęp do interfejsu użytkownika platformy Spark, otwórz go `127.0.0.1:4040` w przeglądarce.

![Interfejs użytkownika Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Pobierz wyniki zadania i dzienniki

Po zakończeniu zadania sterownik pod zostanie w stanie "ukończone". Pobierz nazwę elementu pod za pomocą poniższego polecenia.

```bash
kubectl get pods --show-all
```

Dane wyjściowe:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Użyj `kubectl logs` polecenia, aby pobrać dzienniki ze sterownika Spark pod. Zastąp nazwę pod nazwą sterownika.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

W tych dziennikach można zobaczyć wynik zadania Spark, który jest wartością liczby pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Pakowanie na słoik przy użyciu obrazu kontenera

W powyższym przykładzie plik JAR magazynu Spark został przekazany do usługi Azure Storage. Innym rozwiązaniem jest spakowanie pliku JAR do obrazów platformy Docker utworzonych niestandardowo.

Aby to zrobić, Znajdź `dockerfile` obraz dla platformy Spark znajdujący się w `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` katalogu. Dodaj `ADD` instrukcję dla zadania Spark w `jar` miejscu między `WORKDIR` i `ENTRYPOINT` deklaracjami.

Zaktualizuj ścieżkę jar do lokalizacji `SparkPi-assembly-0.1.0-SNAPSHOT.jar` pliku w systemie deweloperskim. Możesz również użyć własnego pliku JAR.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Kompiluj i wypchnij obraz z dołączonymi skryptami platformy Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Gdy uruchamiasz zadanie, zamiast wskazywać zdalny adres URL jar, `local://` schemat może być używany z ścieżką do pliku JAR w obrazie platformy Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Z [dokumentacji][spark-docs]platformy Spark: "usługa Kubernetes Scheduler jest obecnie eksperymentalna. W przyszłych wersjach mogą wystąpić zmiany w konfiguracji, obrazy kontenerów i wejścia/wyjścia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją platformy Spark.

> [!div class="nextstepaction"]
> [Dokumentacja platformy Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/?view=azure-cli-latest
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
