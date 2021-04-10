---
title: Usługi operacjonalizować ML w usłudze HDInsight — Azure
description: Dowiedz się, jak operacjonalizować model danych, aby dokonać prognoz przy użyciu usług ML w usłudze Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: 7d597c4dbb81562050e9523c61d47d0020fc9059
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869478"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Klaster usług operacjonalizować ML w usłudze Azure HDInsight

Po użyciu klastra usług ML w usłudze HDInsight w celu ukończenia modelowania danych można operacjonalizować model, aby dokonać prognoz. Ten artykuł zawiera instrukcje dotyczące wykonywania tego zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi ML w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **usługi ml** dla **typu klastra**.

* Klient protokołu Secure Shell (SSH): klient SSH jest używany do zdalnego łączenia z klastrem usługi HDInsight i uruchamiania poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Klaster usług operacjonalizować ML z konfiguracją jednokrotną

> [!NOTE]  
> Poniższe kroki dotyczą R Server 9,0 i ML Server 9,1. ML Server 9,3 można znaleźć w tematach [Zarządzanie konfiguracją operacjonalizacji za pomocą narzędzia administracyjnego](/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Aby uzyskać instrukcje dotyczące korzystania z protokołu SSH z usługą Azure HDInsight, zobacz [Używanie protokołu SSH z usługą HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Zmień katalog dla odpowiedniej wersji i sudo NET dll: 

    - Dla Microsoft ML Server 9,1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - W przypadku oprogramowania Microsoft R Server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. Zostaną wyświetlone opcje do wyboru. Wybierz pierwszą opcję, jak pokazano na poniższym zrzucie ekranu, aby **skonfigurować ml Server dla operacjonalizacji**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-1.png" alt-text="Wybór narzędzi administracyjnych programu R Server" border="true":::

1. Zostanie wyświetlona opcja wyboru, w jaki sposób chcesz operacjonalizować ML Server. Z przedstawionych opcji wybierz pierwszy z nich, **wprowadzając.**

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-2.png" alt-text="Operacjonalizować narzędzi administracyjnych programu R Server" border="true":::

1. Po wyświetleniu monitu wprowadź hasło administratora lokalnego i wprowadź je ponownie.

1. Powinny zostać wyświetlone dane wyjściowe z sugestią, że operacja zakończyła się pomyślnie. Zostanie również wyświetlony monit o wybranie innej opcji z menu. Wybierz pozycję E, aby wrócić do menu głównego.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-3.png" alt-text="Powodzenie narzędzia administracyjnego programu R Server" border="true":::

1. Opcjonalnie można przeprowadzić kontrolę diagnostyczną, uruchamiając test diagnostyczny w następujący sposób:

    a. Z menu głównego wybierz **6** , aby uruchomić testy diagnostyczne.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic1.png" alt-text="Diagnostyka narzędzi administracyjnych programu R Server" border="true":::

    b. Z menu testy **diagnostyczne wybierz pozycję**. Po wyświetleniu monitu wprowadź hasło podane dla użytkownika administratora lokalnego.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic2.png" alt-text="Test narzędzi administracyjnych programu R Server" border="true":::

    c. Sprawdź, czy dane wyjściowe pokazują, że ogólna kondycja jest przebiegu.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic3.png" alt-text="Przebieg narzędzia administracyjnego programu R Server" border="true":::

    d. W wyświetlonych opcjach menu Wprowadź wartość **E** , aby powrócić do menu głównego, a następnie wprowadź **8** , aby wyjść z narzędzia administracyjnego.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Długotrwałe opóźnienia podczas korzystania z usługi sieci Web na Apache Spark

Jeśli wystąpią duże opóźnienia podczas próby korzystania z usługi internetowej utworzonej za pomocą funkcji mrsdeploy w kontekście obliczeniowym Apache Spark, może być konieczne dodanie niektórych brakujących folderów. Aplikacja Spark należy do użytkownika o nazwie „*rserve2*” zawsze wtedy, gdy jest wywoływana z usługi internetowej przy użyciu funkcji mrsdeploy. Aby obejść ten problem:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

Na tym etapie konfiguracja operacjonalizacji jest ukończona. Teraz możesz użyć `mrsdeploy` pakietu w RClient, aby połączyć się z operacjonalizacji w węźle brzegowym i zacząć korzystać z jego funkcji, takich jak [zdalne wykonywanie](/machine-learning-server/r/how-to-execute-code-remotely) i [usługi sieci Web](/machine-learning-server/operationalize/concept-what-are-web-services). W zależności od tego, czy klaster został skonfigurowany w sieci wirtualnej, może być konieczne skonfigurowanie tunelowania przekierowania portów za pomocą logowania SSH. W poniższych sekcjach wyjaśniono, jak skonfigurować taki tunel.

### <a name="ml-services-cluster-on-virtual-network"></a>Klaster usług ML w sieci wirtualnej

Sprawdź, czy ruch przez port 12800 węzła krawędzi jest dozwolony. Pozwala to użyć węzła krawędzi do nawiązania połączenia z funkcją operacjonalizacji.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Jeśli metoda `remoteLogin()` nie może połączyć się z węzłem krawędzi, lecz nawiązanie połączenia SSH z węzłem krawędzi jest możliwe, sprawdź, czy reguła zezwalająca na ruch przez port 12800 jest skonfigurowana poprawnie. Jeśli problem nie ustąpi, możesz go obejść, konfigurując tunelowanie przekierowania portów przez połączenie SSH. Aby uzyskać instrukcje, zobacz następującą sekcję:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Klaster usług ML nie jest skonfigurowany w sieci wirtualnej

Jeśli klaster nie jest skonfigurowany w sieci wirtualnej lub występują problemy z korzystaniem z sieci wirtualnej, możesz użyć tunelowania przekierowania portów za pomocą protokołu SSH:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Gdy sesja SSH jest aktywna, ruch z portu 12800 komputera lokalnego jest przekazywany do portu 12800 węzła brzegowego za pośrednictwem sesji SSH. Upewnij się, że w metodzie `remoteLogin()` użyto adresu `127.0.0.1:12800`. Spowoduje to zalogowanie się do operacjonalizacji węzła krawędzi za pomocą przekazywania portów.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skalowanie operacyjnych węzłów obliczeniowych w węzłach procesu roboczego usługi HDInsight

Aby skalować węzły obliczeniowe, najpierw zlikwidowanie węzłów procesu roboczego, a następnie skonfigurowanie węzłów obliczeniowych w zlikwidowanych węzłach procesu roboczego.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1. likwidowanie węzłów procesu roboczego

Klaster usług ML nie jest zarządzany przez [Apache HADOOP przędzą](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Jeśli węzły procesu roboczego nie zostaną zlikwidowane, Menedżer zasobów PRZĘDZa nie będzie działała zgodnie z oczekiwaniami, ponieważ nie ma informacji o zasobach, które są wykonywane przez serwer. Aby tego uniknąć, zalecamy zlikwidowanie węzłów procesu roboczego przed przystąpieniem do skalowania węzłów obliczeniowych na zewnątrz.

Wykonaj następujące kroki, aby zlikwidować węzły procesu roboczego:

1. Zaloguj się do konsoli Ambari klastra i kliknij kartę **hosty** .

1. Wybierz węzły procesu roboczego (do zlikwidowania).

1. Kliknij kolejno pozycje **Akcje**  >  **wybrane hosty**  >    >  **Włącz tryb konserwacji**. Na przykład na poniższej ilustracji węzły wn3 i wn4 są przeznaczone do likwidacji.  

   :::image type="content" source="./media/r-server-operationalize/get-started-operationalization.png" alt-text="Apache Ambari Włącz tryb konserwacji" border="true":::  

* Wybierz pozycję **Akcje**  >  **wybrane hosty**  >  **datanodes** > kliknij pozycję **likwidowanie**.
* Wybierz pozycję **Akcje**  >  **wybrane hosty**  >  **NodeManagers** > kliknij pozycję **likwidowanie**.
* Wybierz **Akcje**  >  **wybrane hosty**  >  **datanodes** > kliknij przycisk **Zatrzymaj**.
* Wybierz pozycję **Akcje**  >  **wybrane hosty**  >  **NodeManagers** > kliknij przycisk **Zatrzymaj**.
* Wybierz **Akcje**  >  **wybrane hosty**  >  **hosty** > kliknij przycisk **Zatrzymaj wszystkie składniki**.
* Usuń zaznaczenie węzłów procesu roboczego i wybierz węzły główne.
* Wybierz pozycję **Akcje**  >  **wybrane hosty** > "**hosty**  >  **uruchamiają ponownie wszystkie składniki**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2. Konfigurowanie węzłów obliczeniowych na wszystkich zlikwidowanych węzłach procesu roboczego

1. Za pomocą protokołu SSH połącz się z każdym zlikwidowanym węzłem procesu roboczego.

1. Uruchom narzędzie administracyjne za pomocą odpowiedniej biblioteki DLL dla posiadanego klastra usługi ML. W przypadku ML Server 9,1 Uruchom następujące polecenie:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Wprowadź **1** , aby wybrać opcję **konfigurowania ml Server dla operacjonalizacji**.

1. Wprowadź **C** , aby wybrać opcję `C. Compute node` . Umożliwi to skonfigurowanie węzła obliczeniowego w węźle procesu roboczego.

1. Zamknij narzędzie administracyjne.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3. Dodawanie szczegółów węzłów obliczeniowych w węźle sieci Web

Po skonfigurowaniu wszystkich zlikwidowanych węzłów procesu roboczego pod kątem uruchamiania węzła obliczeniowego Wróć do węzła brzegowego i Dodaj adresy IP zlikwidowanych węzłów procesu roboczego do konfiguracji węzła ML Server sieci Web:

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.

1. Uruchom polecenie `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Poszukaj sekcji "URI" i Dodaj adres IP i port węzła procesu roboczego.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", &quot;http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

* [Manage ML Services cluster on HDInsight (Zarządzanie klastrem usług ML w usłudze HDInsight)](r-server-hdinsight-manage.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Azure Storage options for ML Services cluster on HDInsight (Opcje usługi Azure Storage dla klastra usług ML w usłudze HDInsight)](r-server-storage.md)