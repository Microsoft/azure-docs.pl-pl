---
title: Monitorowanie platformy Hadoop i zarządzanie nią za pomocą interfejsu API REST Ambari — Azure HDInsight
description: Dowiedz się, jak używać Ambari do monitorowania klastrów Hadoop i zarządzania nimi w usłudze Azure HDInsight. W tym dokumencie dowiesz się, jak używać interfejsu API REST Ambari dołączonego do klastrów usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 6ba1d1e15b1dbb3efb24219b6c09a6827e701d46
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546079"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST usługi Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Dowiedz się, jak używać interfejsu API REST usługi Apache Ambari do zarządzania klastrami Apache Hadoop w usłudze Azure HDInsight i ich monitorowania.

## <a name="what-is-apache-ambari"></a>Co to jest Apache Ambari

Platforma Apache Ambari upraszcza zarządzanie i monitorowanie klastrów usługi Hadoop, zapewniając łatwy do użycia interfejs użytkownika sieci Web, który jest obsługiwany przez [interfejsy API REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari jest domyślnie dostarczany z klastrami usługi HDInsight opartymi na systemie Linux.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash na Ubuntu w systemie Windows 10.  W przykładach w tym artykule użyto powłoki bash w systemie Windows 10. Aby uzyskać instrukcje dotyczące instalacji, zobacz artykuł [podsystem instalacji systemu Windows dla systemu Linux w systemie Windows 10](/windows/wsl/install-win10) .  Inne [powłoki systemu UNIX](https://www.gnu.org/software/bash/) również będą działały.  Przykłady, z niewielkimi modyfikacjami, mogą współpracować z wierszem polecenia systemu Windows.  Można też użyć programu Windows PowerShell.

* JQ, procesor JSON w wierszu polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Windows PowerShell.  Można też użyć bash.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Podstawowe Uniform Resource Identifier interfejsu API REST Ambari

 Podstawowy Uniform Resource Identifier (URI) dla interfejsu API REST Ambari w usłudze HDInsight to `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , gdzie `CLUSTERNAME` jest nazwą klastra.  Nazwy klastra w identyfikatorach URI są **rozróżniane wielkości** liter.  W czasie, gdy nazwa klastra w w pełni kwalifikowana nazwa domeny (FQDN) w ramach identyfikatora URI ( `CLUSTERNAME.azurehdinsight.net` ) nie uwzględnia wielkości liter, inne wystąpienia w identyfikatorze URI uwzględniają wielkość liter.

## <a name="authentication"></a>Authentication

Połączenie z usługą Ambari w usłudze HDInsight wymaga protokołu HTTPS. Użyj nazwy konta administratora (wartość domyślna to **admin** ) i hasła podanego podczas tworzenia klastra.

W przypadku klastrów pakiet Enterprise Security zamiast tego należy `admin` użyć w pełni kwalifikowanej nazwy użytkownika, takiej jak `username@domain.onmicrosoft.com` .

## <a name="examples"></a>Przykłady

### <a name="setup-preserve-credentials"></a>Instalacja (zachowywanie poświadczeń)

Zachowaj poświadczenia, aby uniknąć ich przetworzenia w każdym przykładzie.  Nazwa klastra zostanie zachowana w osobnym kroku.

**A. bash**  
Edytuj Poniższy skrypt, zastępując go `PASSWORD` rzeczywistym hasłem.  Następnie wprowadź polecenie.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Zidentyfikuj poprawną nazwę klastra z wielkością liter

Rzeczywista wielkość liter nazwy klastra może być inna niż oczekiwano.  Kroki opisane w tym miejscu spowodują wyświetlenie rzeczywistej wielkości liter, a następnie zapisanie jej w zmiennej dla wszystkich późniejszych przykładów.

Edytuj poniższe skrypty, aby zastąpić je `CLUSTERNAME` nazwą klastra. Następnie wprowadź polecenie. (Nazwa klastra nazwy FQDN nie jest rozróżniana wielkość liter).

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Analizowanie danych JSON

Poniższy przykład używa [JQ](https://stedolan.github.io/jq/) lub [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) do analizy dokumentu odpowiedzi JSON i wyświetlania tylko `health_report` informacji z wyników.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>Pobieranie nazwy FQDN węzłów klastra

Może być konieczne poznanie w pełni kwalifikowanej nazwy domeny (FQDN) węzła klastra. Możesz łatwo pobrać nazwę FQDN dla różnych węzłów w klastrze, korzystając z następujących przykładów:

**Wszystkie węzły**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Węzły główne**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Węzły procesu roboczego**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Dozorcy węzły**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Pobieranie wewnętrznego adresu IP węzłów klastra

Adresy IP zwracane przez przykłady w tej sekcji nie są bezpośrednio dostępne przez Internet. Są one dostępne tylko w ramach Virtual Network platformy Azure, które zawierają klaster usługi HDInsight.

Aby uzyskać więcej informacji na temat pracy z usługą HDInsight i sieciami wirtualnymi, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

Aby znaleźć adres IP, należy znać wewnętrzną w pełni kwalifikowaną nazwę domeny (FQDN) węzłów klastra. Po skonfigurowaniu nazwy FQDN można uzyskać adres IP hosta. Poniższe przykłady najpierw pytają Ambari o nazwę FQDN wszystkich węzłów hosta. Następnie zapytania Ambari dla adresu IP każdego hosta.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Pobieranie magazynu domyślnego

Klastry HDInsight muszą używać konta usługi Azure Storage lub Data Lake Storage jako magazynu domyślnego. Możesz użyć Ambari, aby pobrać te informacje po utworzeniu klastra. Na przykład, jeśli chcesz odczytywać i zapisywać dane w kontenerze poza usługą HDInsight.

Poniższe przykłady pobierają domyślną konfigurację magazynu z klastra:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Te przykłady zwracają pierwszą konfigurację zastosowana do serwera ( `service_config_version=1` ), która zawiera te informacje. W przypadku pobrania wartości, która została zmodyfikowana po utworzeniu klastra, może być konieczne wyświetlenie listy wersji konfiguracji i pobranie najnowszego.

Wartość zwracana jest podobna do jednego z następujących przykładów:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` — Ta wartość wskazuje, że klaster używa konta usługi Azure Storage do przechowywania domyślnego. `ACCOUNTNAME`Wartość to nazwa konta magazynu. `CONTAINER`Część jest nazwą kontenera obiektów BLOB na koncie magazynu. Kontener jest katalogiem głównym magazynu zgodnego z systemem plików HDFS dla klastra.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` — Ta wartość wskazuje, że klaster używa Azure Data Lake Storage Gen2 dla magazynu domyślnego. `ACCOUNTNAME`Wartości i `CONTAINER` mają takie same znaczenie jak w przypadku usługi Azure Storage wymienionej wcześniej.

* `adl://home` — Ta wartość wskazuje, że klaster używa Azure Data Lake Storage Gen1 dla magazynu domyślnego.

    Aby znaleźć nazwę konta Data Lake Storage, użyj następujących przykładów:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Wartość zwracana jest podobna do `ACCOUNTNAME.azuredatalakestore.net` , gdzie `ACCOUNTNAME` jest nazwą konta Data Lake Storage.

    Aby znaleźć katalog w Data Lake Storage zawierającym magazyn klastra, użyj następujących przykładów:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Wartość zwracana jest podobna do `/clusters/CLUSTERNAME/` . Ta wartość jest ścieżką w ramach konta Data Lake Storage. Ta ścieżka jest katalogiem głównym zgodnego systemu plików HDFS dla klastra.  

> [!NOTE]  
> Polecenie cmdlet [Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) zapewniane przez [Azure PowerShell](/powershell/azure/) również zwraca informacje o magazynie dla klastra.

### <a name="get-all-configurations"></a>Pobierz wszystkie konfiguracje

Pobierz konfiguracje, które są dostępne dla danego klastra.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Ten przykład zwraca dokument JSON zawierający bieżącą konfigurację zainstalowanych składników. Zobacz wartość *tagu* . Poniższy przykład to fragment danych zwróconych z typu klastra Spark.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Pobierz konfigurację określonego składnika

Pobierz konfigurację dla interesującego Cię składnika. W poniższym przykładzie Zastąp `INITIAL` wartość tag wartością zwracaną z poprzedniego żądania.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Ten przykład zwraca dokument JSON zawierający bieżącą konfigurację `livy2-conf` składnika.

### <a name="update-configuration"></a>Aktualizowanie konfiguracji

1. Utwórz `newconfig.json` .  
   Zmodyfikuj, a następnie wprowadź poniższe polecenia:

   * Zamień na `livy2-conf` Nowy składnik.
   * Zamień na `INITIAL` wartość rzeczywistą pobieraną dla opcji `tag` [Pobierz wszystkie konfiguracje](#get-all-configurations).

     **A. bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     Skrypt programu PowerShell używa [JQ](https://stedolan.github.io/jq/).  Edytuj `C:\HD\jq\jq-win64` poniżej, aby odzwierciedlić rzeczywistą ścieżkę i wersję [JQ](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     JQ służy do przekształcania danych pobranych z usługi HDInsight do nowego szablonu konfiguracji. W tych przykładach wykonaj następujące czynności:

   * Tworzy unikatową wartość zawierającą ciąg "wersja" i datę, która jest przechowywana w `newtag` .

   * Tworzy dokument główny dla nowej konfiguracji.

   * Pobiera zawartość `.items[]` tablicy i dodaje ją do **desired_config** elementu.

   * Usuwa `href` elementy, `version` i, `Config` ponieważ te elementy nie są potrzebne do przesłania nowej konfiguracji.

   * Dodaje `tag` element o wartości `version#################` . Część liczbowa jest zależna od bieżącej daty. Każda konfiguracja musi mieć unikatowy tag.

     Na koniec dane są zapisywane do `newconfig.json` dokumentu. Struktura dokumentu powinna wyglądać podobnie do poniższego przykładu:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Edytuj `newconfig.json` .  
   Otwórz `newconfig.json` dokument i zmodyfikuj/Dodaj wartości w `properties` obiekcie. Poniższy przykład zmienia wartość `"livy.server.csrf_protection.enabled"` z z `"true"` na `"false"` .

    ```json
    "livy.server.csrf_protection.enabled": "false",
    ```

    Zapisz plik po zakończeniu wprowadzania zmian.

3. Prześlij `newconfig.json` .  
   Użyj następujących poleceń, aby przesłać zaktualizowaną konfigurację do Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Te polecenia przesyłają zawartość **newconfig.jsw** pliku do klastra jako nową konfigurację. Żądanie zwraca dokument JSON. Element **versionTag** w tym dokumencie powinien odpowiadać przesłanej wersji, a obiekt **configs** zawiera żądane zmiany konfiguracji.

### <a name="restart-a-service-component"></a>Ponowne uruchamianie składnika usługi

W tym momencie interfejs użytkownika sieci Web Ambari wskazuje, że usługa Spark musi zostać uruchomiona ponownie, aby można było zastosować nową konfigurację. Wykonaj następujące kroki, aby ponownie uruchomić usługę.

1. Aby włączyć tryb konserwacji dla usługi Spark2, należy wykonać następujące czynności:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. Sprawdź tryb konserwacji  

    Te polecenia umożliwiają wysłanie dokumentu JSON do serwera, który włącza tryb konserwacji. Możesz sprawdzić, czy usługa działa teraz w trybie konserwacji, używając następującego żądania:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Wartość zwracana to `ON` .

3. Następnie użyj poniższego, aby wyłączyć usługę Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    Odpowiedź jest podobna do poniższego przykładu:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > `href`Wartość zwrócona przez ten identyfikator URI używa wewnętrznego adresu IP węzła klastra. Aby użyć go spoza klastra, Zastąp `10.0.0.18:8080` część nazwą FQDN klastra.  

4. Sprawdź poprawność żądania.  
    Edytuj poniższe polecenie, zastępując `29` wartość rzeczywistą `id` zwracaną z poprzedniego kroku.  Następujące polecenia powodują pobranie stanu żądania:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Odpowiedź `COMPLETED` wskazuje, że żądanie zostało zakończone.

5. Po zakończeniu poprzedniego żądania wykonaj następujące czynności, aby uruchomić usługę Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    Usługa używa teraz nowej konfiguracji.

6. Na koniec użyj poniższego, aby wyłączyć tryb konserwacji.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełne informacje na temat interfejsu API REST, zobacz [wersja 1 interfejsu API platformy Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Zobacz również [Autoryzowanie użytkowników na potrzeby widoków Apache Ambari](./hdinsight-authorize-users-to-ambari.md)