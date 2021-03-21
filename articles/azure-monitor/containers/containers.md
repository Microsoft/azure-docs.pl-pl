---
title: Rozwiązanie do monitorowania kontenerów w Azure Monitor | Microsoft Docs
description: Rozwiązanie do monitorowania kontenerów w Azure Monitor ułatwia wyświetlanie hostów platformy Docker i kontenerów systemu Windows oraz zarządzanie nimi w jednej lokalizacji.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/06/2020
ms.openlocfilehash: 59f81103e72be6c0f1b629981fdb53c8467000a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037425"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Rozwiązanie do monitorowania kontenerów w Azure Monitor

![Symbol kontenera](./media/containers/containers-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania do monitorowania kontenerów w Azure Monitor, które ułatwia wyświetlanie hostów platformy Docker i kontenerów systemu Windows oraz zarządzanie nimi w jednej lokalizacji. Docker to system wirtualizacji oprogramowania służący do tworzenia kontenerów, które automatyzują wdrażanie oprogramowania do infrastruktury IT.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Rozwiązanie pokazuje, które kontenery są uruchomione, jaki obraz kontenera jest uruchomiony, oraz miejsce, w którym są uruchomione kontenery. Można wyświetlić szczegółowe informacje o inspekcji zawierające polecenia używane z kontenerami. Ponadto możesz rozwiązywać problemy z kontenerami, wyświetlając i przeszukując scentralizowane dzienniki bez konieczności zdalnego wyświetlania hostów Docker lub Windows. Możesz znaleźć kontenery, które mogą być zakłóceniami i zużywać nadmierne zasoby na hoście. Można też wyświetlać scentralizowane informacje o użyciu procesora CPU, pamięci, magazynu i sieci oraz wydajność dla kontenerów. Na komputerach z systemem Windows można scentralizować i porównać dzienniki z systemu Windows Server, funkcji Hyper-V i kontenerów platformy Docker. Rozwiązanie obsługuje następujące Koordynatory kontenerów:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Jeśli masz kontenery wdrożone w [usłudze Azure Service Fabric](../../service-fabric/service-fabric-overview.md), zalecamy włączenie [rozwiązania Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) i tego rozwiązania w celu uwzględnienia monitorowania zdarzeń klastra. Przed włączeniem rozwiązania Service Fabric, zapoznaj się z tematem [Service Fabric rozwiązanie](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) , aby zrozumieć, jakie informacje zawiera i jak z nich korzystać.

Jeśli interesuje Cię monitorowanie wydajności obciążeń wdrożonych w środowiskach Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS), zobacz [monitorowanie usługi Azure Kubernetes](../containers/container-insights-overview.md). Rozwiązanie do monitorowania kontenerów nie obsługuje monitorowania tej platformy.  

Na poniższym diagramie przedstawiono relacje między różnymi hostami kontenerów i agentami z Azure Monitor.

![Diagram kontenerów](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Wymagania systemowe i obsługiwane platformy

Przed rozpoczęciem zapoznaj się z poniższymi szczegółami, aby upewnić się, że spełniasz wymagania wstępne.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Obsługa rozwiązań do monitorowania kontenerów dla programu Docker Orchestrator i platformy systemu operacyjnego

W poniższej tabeli przedstawiono wsparcie w zakresie organizowania i monitorowania systemu operacyjnego platformy Docker, a następnie obsługę spisu kontenerów, wydajności i dzienników przy użyciu Azure Monitor.   

|Aranżacja platformy Docker | ACS | Linux | Windows | Kontener<br>Stan zapasów | Obraz<br>Stan zapasów | Węzeł<br>Stan zapasów | Kontener<br>Wydajność | Kontener<br>Zdarzenie | Zdarzenie<br>Dziennik | Kontener<br>Dziennik |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Usługa<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat<br>Przesunięcia | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>autonomicznej | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serwer z systemem Linux<br>autonomicznej | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Wersje platformy Docker obsługiwane w systemie Linux

- Docker 1,11 do 1,13
- Docker CE i EE v 17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>dystrybucje systemu Linux x64 obsługiwane jako hosty kontenerów

- Ubuntu 14,04 LTS i 16,04 LTS
- CoreOS (stabilne)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE przestępnie 42,2
- CentOS 7,2 i 7,3
- SLES 12
- RHEL 7,2 i 7,3
- OpenShift kontenerów Red Hat (OCP) 3,4 i 3,5
- Usługa ACS mesosphere DC/OS 1.7.3 do 1.8.8
- Usługa ACS Kubernetes 1.4.5 do 1,6
    - Zdarzenia Kubernetes, spis Kubernetes i procesy kontenera są obsługiwane tylko w wersji 1.4.1-45 i nowszych Log Analytics Agent dla systemu Linux
- Usługa ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Obsługiwany system operacyjny Windows

- Windows Server 2016
- Windows 10 w wersji rocznicowej (Professional lub Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Wersje platformy Docker obsługiwane w systemie Windows

- Docker 1,12 i 1,13
- Docker 17.03.0 i nowsze

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

1. Dodaj rozwiązanie do monitorowania kontenerów do obszaru roboczego Log Analytics z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) lub przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań monitorowania z Galeria rozwiązań](../insights/solutions.md).

2. Instalowanie i używanie platformy Docker z agentem Log Analytics. W oparciu o system operacyjny i program Docker Orchestrator można skonfigurować agenta przy użyciu poniższych metod.
   - W przypadku hostów autonomicznych:
     - W obsługiwanych systemach operacyjnych Linux Zainstaluj i Uruchom platformę Docker, a następnie zainstaluj i skonfiguruj [agenta log Analytics dla systemu Linux](../vm/quick-collect-linux-computer.md).  
     - W systemie CoreOS nie można uruchomić agenta Log Analytics dla systemu Linux. Zamiast tego należy uruchomić kontener Log Analytics agenta dla systemu Linux. Przejrzyj hosty kontenerów systemu Linux, w tym CoreOS lub Azure Government hosty z systemem Linux, w tym CoreOS, jeśli pracujesz z kontenerami w Azure Government Cloud.
     - W systemach Windows Server 2016 i Windows 10 Zainstaluj aparat platformy Docker i klienta, a następnie połącz agenta w celu zebrania informacji i wysłania go do Azure Monitor. Zapoznaj się z tematem [Instalowanie i Konfigurowanie hostów kontenera systemu Windows](#install-and-configure-windows-container-hosts) , jeśli masz środowisko systemu Windows.
   - W przypadku aranżacji z obsługą wielohosta platformy Docker:
     - Jeśli masz środowisko Red Hat OpenShift, zapoznaj się z tematem Konfigurowanie agenta Log Analytics dla Red Hat OpenShift.
     - Jeśli masz klaster Kubernetes przy użyciu Azure Container Service:
       - Zapoznaj [się z tematem konfigurowanie log Analytics agenta systemu Linux dla usługi Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Zapoznaj [się z tematem konfigurowanie log Analytics agenta systemu Windows dla Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Zapoznaj się z tematem korzystanie z Helm w celu wdrożenia agenta Log Analytics w systemie Linux Kubernetes.
     - Jeśli masz klaster DC/OS Azure Container Service, Dowiedz się więcej na temat [monitorowania Azure Container Service klastra DC/OS przy użyciu Azure monitor](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms).
     - Jeśli masz środowisko Docker Swarm Mode, Dowiedz się więcej na temat konfigurowania agenta Log Analytics dla platformy Docker Swarm.
     - Jeśli masz klaster Service Fabric, Dowiedz się więcej w obszarze [monitorowanie kontenerów z Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Zapoznaj się z artykułem [aparat platformy Docker w systemie Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon) , aby uzyskać dodatkowe informacje na temat instalowania i konfigurowania aparatów platformy Docker na komputerach z systemem Windows.

> [!IMPORTANT]
> Program Docker musi być uruchomiony **przed** zainstalowaniem [agenta log Analytics dla systemu Linux](../vm/quick-collect-linux-computer.md) na hostach kontenerów. Jeśli Agent został już zainstalowany przed instalacją platformy Docker, należy ponownie zainstalować agenta Log Analytics dla systemu Linux. Aby uzyskać więcej informacji na temat platformy Docker, zobacz [witrynę sieci Web platformy Docker](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalowanie i Konfigurowanie hostów kontenera systemu Linux

Po zainstalowaniu platformy Docker Użyj następujących ustawień dla hosta kontenera, aby skonfigurować agenta do użycia z platformą Docker. Najpierw potrzebujesz identyfikatora i klucza obszaru roboczego Log Analytics, który można znaleźć w Azure Portal. W obszarze roboczym kliknij pozycję **Szybki Start**  >  **komputery** , aby wyświetlić **Identyfikator obszaru roboczego** i **klucz podstawowy**.  Skopiuj i wklej obie wartości do ulubionego edytora.

**Dla wszystkich hostów kontenera systemu Linux z wyjątkiem CoreOS:**

- Aby uzyskać więcej informacji i zapoznać się z instrukcjami dotyczącymi sposobu instalowania agenta Log Analytics dla systemu Linux, zobacz [Omówienie agenta usługi log Analytics](../agents/log-analytics-agent.md).

**Wszystkie hosty kontenerów systemu Linux, w tym CoreOS:**

Uruchom kontener, który ma być monitorowany. Zmodyfikuj i użyj poniższego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Wszystkie hosty kontenerów Azure Government Linux, w tym CoreOS:**

Uruchom kontener, który ma być monitorowany. Zmodyfikuj i użyj poniższego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Przełączanie z używania zainstalowanego agenta systemu Linux do jednego w kontenerze**

Jeśli wcześniej zainstalowano agenta, który ma być używany przez agenta, należy najpierw usunąć agenta Log Analytics dla systemu Linux. Zobacz [Odinstalowywanie agenta log Analytics dla systemu Linux](../vm/quick-collect-linux-computer.md) , aby poznać sposób pomyślnego odinstalowania agenta.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurowanie agenta Log Analytics dla platformy Docker Swarm

Agenta Log Analytics można uruchomić jako usługę globalną na platformie Docker Swarm. Skorzystaj z poniższych informacji, aby utworzyć usługę agenta Log Analytics. Musisz podać identyfikator obszaru roboczego Log Analytics i klucz podstawowy.

- Uruchom następujące elementy w węźle głównym.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Zabezpieczanie wpisów tajnych dla platformy Docker Swarm

W przypadku usługi Docker Swarm, gdy zostanie utworzony wpis tajny dla identyfikatora obszaru roboczego i klucza podstawowego, Skorzystaj z poniższych informacji, aby utworzyć tajne informacje.

1. Uruchom następujące elementy w węźle głównym.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Sprawdź, czy wpisy tajne zostały utworzone prawidłowo.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Uruchom następujące polecenie, aby zainstalować klucze tajne do Log Analytics agenta kontenera.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurowanie agenta Log Analytics dla Red Hat OpenShift

Istnieją trzy sposoby dodawania agenta Log Analytics do Red Hat OpenShift, aby rozpocząć zbieranie danych monitorowania kontenerów.

* [Zainstaluj agenta log Analytics dla systemu Linux](../vm/quick-collect-linux-computer.md) bezpośrednio w każdym węźle OpenShift  
* [Włącz log Analytics rozszerzenie maszyny wirtualnej](../vm/quick-collect-azurevm.md) na każdym węźle OpenShift znajdującym się na platformie Azure  
* Instalowanie agenta Log Analytics jako zestawu demonów OpenShift  

W tej sekcji opisano kroki wymagane do zainstalowania agenta Log Analytics jako zestawu demonów OpenShift.  

1. Zaloguj się do węzła głównego OpenShift i skopiuj plik YAML [OCP-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z usługi GitHub do węzła głównego i zmodyfikuj wartość przy użyciu identyfikatora obszaru roboczego log Analytics i klucza podstawowego.
2. Uruchom następujące polecenia, aby utworzyć projekt dla Azure Monitor i ustawić konto użytkownika.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Aby wdrożyć zestaw demonów, uruchom następujące polecenie:

    `oc create -f ocp-omsagent.yaml`

4. Aby sprawdzić, czy jest skonfigurowany i działa prawidłowo, wpisz następujące polecenie:

    `oc describe daemonset omsagent`  

    a dane wyjściowe powinny wyglądać następująco:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Jeśli chcesz użyć kluczy tajnych do zabezpieczenia identyfikatora obszaru roboczego Log Analytics i klucza podstawowego przy użyciu demona agenta Log Analytics — Ustaw plik YAML, wykonaj następujące czynności.

1. Zaloguj się do węzła głównego OpenShift i skopiuj plik YAML [OCP-ds-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) i Secret generujący skrypt [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z usługi GitHub.  Ten skrypt generuje plik tajemnicy YAML Log Analytics dla identyfikatora obszaru roboczego i klucza podstawowego w celu zabezpieczenia informacji tajnych.  
2. Uruchom następujące polecenia, aby utworzyć projekt dla Azure Monitor i ustawić konto użytkownika. Skrypt generujący wpis tajny pyta o identyfikator obszaru roboczego Log Analytics `<WSID>` i klucz podstawowy `<KEY>` i po zakończeniu tworzy plik OCP-Secret. YAML.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Wdróż plik tajny, uruchamiając następujące czynności:

    `oc create -f ocp-secret.yaml`

4. Sprawdź wdrożenie, wykonując następujące czynności:

    `oc describe secret omsagent-secret`  

    a dane wyjściowe powinny wyglądać następująco:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. Wdróż plik YAML Log Analytics z demonem agenta programu, uruchamiając następujące czynności:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Sprawdź wdrożenie, wykonując następujące czynności:

    `oc describe ds oms`

    a dane wyjściowe powinny wyglądać następująco:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurowanie Log Analytics agenta systemu Linux dla usługi Kubernetes

W przypadku Kubernetes Użyj skryptu do wygenerowania pliku tajnego YAML dla identyfikatora obszaru roboczego i klucza podstawowego w celu zainstalowania agenta Log Analytics dla systemu Linux. Na stronie [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) znajdują się pliki, których można używać z lub bez informacji o kluczu tajnym.

- Domyślny Agent Log Analytics dla systemu Linux elementu daemonset nie ma informacji o kluczu tajnym (omsagent. YAML)
- Elementu daemonset Agent dla systemu Linux YAML plik używa informacji tajnych (omsagent-ds-Secret. YAML) z skryptami generowania wpisów tajnych w celu wygenerowania pliku tajnego YAML (omsagentsecret. YAML). Log Analytics

Można utworzyć omsagent DaemonSets z lub bez wpisów tajnych.

**Domyślny plik OMSagent elementu daemonset YAML bez wpisów tajnych**

- W przypadku Log Analytics domyślnego pliku elementu daemonset YAML agenta Zastąp ciąg `<WSID>` and `<KEY>` kluczem WSID i. Skopiuj plik do węzła głównego i uruchom następujące czynności:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Domyślny plik OMSagent elementu daemonset YAML z wpisami tajnymi**

1. Aby użyć Log Analytics agenta elementu daemonset przy użyciu informacji o kluczu tajnym, najpierw należy utworzyć wpisy tajne.
    1. Skopiuj skrypt i plik tajny szablonu i upewnij się, że znajdują się one w tym samym katalogu.
        - Skrypt generowania wpisu tajnego — secret-gen.sh
        - Secret Template-Secret-Template. YAML
    2. Uruchom skrypt, jak w poniższym przykładzie. Skrypt monituje o podanie identyfikatora obszaru roboczego Log Analytics i klucza podstawowego, a po jego wprowadzeniu skrypt tworzy plik tajny YAML, aby można było go uruchomić.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Utwórz wpisy tajne pod, wykonując następujące czynności:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Aby sprawdzić, uruchom następujące polecenie:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Dane wyjściowe powinny wyglądać następująco:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Dane wyjściowe powinny wyglądać następująco:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Tworzenie demona omsagent — zestaw przez uruchomienie ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Sprawdź, czy Agent Log Analytics elementu daemonset jest uruchomiony, podobny do następującego:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

W przypadku Kubernetes Użyj skryptu, aby wygenerować plik Secret YAML dla identyfikatora obszaru roboczego i klucza podstawowego dla agenta Log Analytics dla systemu Linux. Skorzystaj z następujących przykładowych informacji w [pliku omsagent YAML](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) , aby zabezpieczyć tajne informacje.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurowanie Log Analytics agenta systemu Windows dla Kubernetes

W przypadku systemu Windows Kubernetes używasz skryptu do wygenerowania pliku tajnego YAML dla identyfikatora obszaru roboczego i klucza podstawowego w celu zainstalowania agenta Log Analytics. Na stronie [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) znajdują się pliki, których można używać z informacjami o kluczowym poziomie użytkownika.  Agenta Log Analytics należy zainstalować oddzielnie dla węzłów głównych i agentów.  

1. Aby użyć Log Analytics agenta elementu daemonset przy użyciu informacji o kluczu tajnym w węźle głównym, najpierw Zaloguj się i Utwórz wpisy tajne.
    1. Skopiuj skrypt i plik tajny szablonu i upewnij się, że znajdują się one w tym samym katalogu.
        - Skrypt generowania wpisu tajnego — secret-gen.sh
        - Secret Template-Secret-Template. YAML

    2. Uruchom skrypt, jak w poniższym przykładzie. Skrypt monituje o podanie identyfikatora obszaru roboczego Log Analytics i klucza podstawowego, a po jego wprowadzeniu skrypt tworzy plik tajny YAML, aby można było go uruchomić.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Tworzenie demona omsagent — zestaw przez uruchomienie ```kubectl create -f omsagentsecret.yaml```
    4. Aby sprawdzić, uruchom następujące polecenie:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Dane wyjściowe powinny wyglądać następująco:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Tworzenie demona omsagent — zestaw przez uruchomienie ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Sprawdź, czy Agent Log Analytics elementu daemonset jest uruchomiony, podobny do następującego:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Aby zainstalować agenta w węźle procesu roboczego z systemem Windows, wykonaj kroki opisane w sekcji [Instalowanie i Konfigurowanie hostów kontenera systemu Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Korzystanie z Helm do wdrażania agenta Log Analytics w systemie Linux Kubernetes

Aby użyć Helm do wdrożenia agenta Log Analytics w środowisku Kubernetes systemu Linux, wykonaj następujące czynności.

1. Tworzenie demona omsagent — zestaw przez uruchomienie ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Wyniki będą wyglądać podobnie do następujących:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. Stan omsagent można sprawdzić, uruchamiając: ```helm status "omsagent"``` , a dane wyjściowe będą wyglądać podobnie do następujących:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Aby uzyskać więcej informacji, odwiedź [Wykres Helm rozwiązania kontenera](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalowanie i Konfigurowanie hostów kontenera systemu Windows

Skorzystaj z informacji w sekcji, aby zainstalować i skonfigurować hosty kontenerów systemu Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Przygotowanie przed zainstalowaniem agentów systemu Windows

Przed zainstalowaniem agentów na komputerach z systemem Windows należy skonfigurować usługę platformy Docker. Konfiguracja umożliwia agentowi systemu Windows lub Azure Monitor rozszerzenie maszyny wirtualnej do korzystania z gniazda TCP platformy Docker, dzięki czemu agenci mogą zdalnie uzyskiwać dostęp do demona platformy Docker i przechwytywać dane do monitorowania.

##### <a name="to-configure-the-docker-service"></a>Aby skonfigurować usługę platformy Docker  

Aby włączyć potok protokołu TCP i nazwany potok dla systemu Windows Server, wykonaj następujące polecenia programu PowerShell:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Aby uzyskać więcej informacji na temat konfiguracji demona platformy Docker używanej z kontenerami systemu Windows, zobacz [aparat platformy Docker w systemie Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalowanie agentów systemu Windows

Aby włączyć monitorowanie kontenera systemu Windows i funkcji Hyper-V, zainstaluj Microsoft Monitoring Agent (MMA) na komputerach z systemem Windows, które są hostami kontenerów. W przypadku komputerów z systemem Windows w środowisku lokalnym zobacz [łączenie komputerów z systemem Windows w celu Azure monitor](../agents/agent-windows.md). W przypadku maszyn wirtualnych działających na platformie Azure podłącz je do Azure Monitor przy użyciu [rozszerzenia maszyny wirtualnej](../vm/quick-collect-azurevm.md).

Można monitorować kontenery systemu Windows działające na Service Fabric. Jednak tylko [maszyny wirtualne działające na platformie Azure](../vm/quick-collect-azurevm.md) i [komputerach z systemem Windows w środowisku lokalnym](../agents/agent-windows.md) są obecnie obsługiwane dla Service Fabric.

Możesz sprawdzić, czy rozwiązanie do monitorowania kontenerów zostało prawidłowo ustawione dla systemu Windows. Aby sprawdzić, czy pakiet administracyjny został pobrany prawidłowo, poszukaj *ContainerManagement.xxx*. Pliki powinny znajdować się w folderze C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Pack.

## <a name="solution-components"></a>Składniki rozwiązania

W Azure Portal przejdź do *Galeria rozwiązań* i Dodaj **rozwiązanie do monitorowania kontenerów**. Jeśli używasz agentów systemu Windows, po dodaniu tego rozwiązania jest instalowany następujący pakiet administracyjny na każdym komputerze z agentem. W pakiecie administracyjnym nie jest wymagana żadna konfiguracja ani konserwacja.

- *ContainerManagement.xxx* zainstalowany w folderze C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Pack

## <a name="container-data-collection-details"></a>Szczegóły zbierania danych kontenera

Rozwiązanie do monitorowania kontenerów zbiera różne metryki wydajności i dane dzienników z hostów kontenerów i kontenerów przy użyciu agentów, które są włączone.

Dane są zbierane co trzy minuty przez następujące typy agentów.

- [Agent Log Analytics dla systemu Linux](../vm/quick-collect-linux-computer.md)
- [Agent systemu Windows](../agents/agent-windows.md)
- [Log Analytics rozszerzenie maszyny wirtualnej](../vm/quick-collect-azurevm.md)

### <a name="container-records"></a>Rekordy kontenerów

W poniższej tabeli przedstawiono przykłady rekordów zebranych przez rozwiązanie do monitorowania kontenerów i typy danych, które pojawiają się w wynikach przeszukiwania dzienników.

| Typ danych | Typ danych w przeszukiwaniu dzienników | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | Komputer, nazwa obiektu, CounterName &#40;% czasu procesora, odczyty dysku MB, zapisy dysku MB, użycie pamięci MB, bajty odbioru sieci, bajty wysyłania w sieci, użycie procesora SEC,&#41; sieci, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Spis kontenerów | `ContainerInventory` | TimeGenerated, Computer, nazwa kontenera, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenie, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Spis obrazów kontenerów | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, uruchomione, wstrzymane, zatrzymane, zakończone niepowodzeniem, SourceSystem, ImageID, TotalContainer |
| Dziennik kontenera | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, nazwa kontenera, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Dziennik usługi kontenera | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Spis węzłów kontenera | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Proces kontenera | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s |
| Zdarzenia Kubernetes | `KubeEvents_CL` | TimeGenerated, komputer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, komunikat |

Etykiety dołączone do typów danych *PodLabel* są własnymi etykietami niestandardowymi. W tabeli przedstawiono przykłady dołączanych etykiet PodLabel. Dlatego, `PodLabel_deployment_s` , `PodLabel_deploymentconfig_s` ,, `PodLabel_docker_registry_s` będzie różnić się w zestawie danych środowiska i w sposób ogólny `PodLabel_yourlabel_s` .

## <a name="monitor-containers"></a>Monitorowanie kontenerów
Po włączeniu rozwiązania w Azure Portal na kafelku **kontenery** zostaną wyświetlone informacje podsumowujące dotyczące hostów kontenerów i kontenerów uruchomionych na hostach.

![Kafelek kontenerów](./media/containers/containers-title.png)

Kafelek zawiera przegląd informacji o liczbie kontenerów w środowisku oraz o tym, czy są one uszkodzone, uruchomione, czy zatrzymane.

### <a name="using-the-containers-dashboard"></a>Korzystanie z pulpitu nawigacyjnego kontenery

Kliknij kafelek **kontenery** . W tym miejscu zobaczysz widoki zorganizowane według:

- **Zdarzenia kontenerów** — pokazuje stan kontenera i komputery z kontenerami zakończonymi niepowodzeniem.
- **Dzienniki kontenerów** — pokazuje wykres plików dziennika kontenerów generowanych w czasie i listę komputerów z największą liczbą plików dziennika.
- **Zdarzenia Kubernetes** — pokazuje wykres zdarzeń Kubernetes generowanych w czasie oraz listę przyczyn, dla których te zdarzenia zostały wygenerowane. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Spis przestrzeni nazw Kubernetes** — pokazuje liczbę przestrzeni nazw i zasobników oraz pokazuje ich hierarchię. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Spis węzłów kontenera** — pokazuje liczbę typów aranżacji używanych na węzłach kontenerów/hostach. Węzły/hosty komputera są również wyświetlane na podstawie liczby kontenerów. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Spis obrazów kontenerów** — pokazuje łączną liczbę użytych obrazów kontenerów i liczbę typów obrazów. Liczba obrazów jest również wyświetlana na liście przez tag obrazu.
- **Stan kontenerów** — pokazuje łączną liczbę węzłów kontenera/komputerów hostów, na których działają kontenery. Komputery są również wyświetlane na podstawie liczby uruchomionych hostów.
- **Proces kontenera** — pokazuje wykres liniowy procesów kontenerów uruchomionych w czasie. Kontenery są również wymienione przez uruchomienie polecenia/procesu w kontenerach. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Wydajność procesora CPU kontenera** — przedstawia wykres liniowy średniego użycia procesora CPU w czasie dla węzłów/hostów komputerów. Wyświetla również węzły/hosty komputera na podstawie średniego użycia procesora CPU.
- **Wydajność pamięci kontenera** — pokazuje wykres liniowy użycia pamięci w czasie. Wyświetla również listę użycia pamięci komputera na podstawie nazwy wystąpienia.
- **Wydajność komputera** — przedstawia wykresy liniowe procentu wydajności procesora CPU w miarę upływu czasu, procent użycia pamięci w czasie i megabajty wolnego miejsca na dysku w czasie. Możesz umieścić kursor na dowolnym wierszu wykresu, aby wyświetlić więcej szczegółów.

Każdy obszar pulpitu nawigacyjnego jest wizualną reprezentacją wyszukiwania, które jest uruchamiane na zebranych danych.

![Zrzut ekranu przedstawiający pulpit nawigacyjny do wyświetlania zebranych danych. ](./media/containers/containers-dash01.png)

![Pulpit nawigacyjny kontenerów](./media/containers/containers-dash02.png)

W obszarze **stan kontenera** kliknij górny obszar, jak pokazano poniżej.

![Stan kontenerów](./media/containers/containers-status.png)

Log Analytics zostanie otwarty, wyświetlając informacje o stanie kontenerów.

![Log Analytics kontenerów](./media/containers/containers-log-search.png)

W tym miejscu możesz edytować zapytanie wyszukiwania, aby zmodyfikować je w celu znalezienia konkretnych interesujących Cię informacji. Aby uzyskać więcej informacji na temat zapytań dzienników, zobacz [Dziennik zapytań w Azure monitor](../logs/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Rozwiązywanie problemów przez znalezienie kontenera zakończonego niepowodzeniem

Log Analytics oznacza kontener jako **zakończony niepowodzeniem** , jeśli zakończył działanie z kodem zakończenia innym niż zero. W obszarze **kontenerów zakończonych niepowodzeniem** można zobaczyć Omówienie błędów i niepowodzeń w środowisku.

### <a name="to-find-failed-containers"></a>Aby znaleźć kontenery zakończone niepowodzeniem

1. Kliknij obszar **stan kontenera** .  
   ![stan kontenerów](./media/containers/containers-status.png)
2. Log Analytics otwiera i wyświetla stan kontenerów, podobny do poniższego.  
   ![stan kontenerów](./media/containers/containers-log-search.png)
3. Rozwiń wiersz niepowodzenie i kliknij przycisk +, aby dodać jego kryteria do zapytania. Następnie Dodaj komentarz do wiersza podsumowania w zapytaniu.
   ![Zrzut ekranu pokazujący wiersz, który powinien zostać oznaczony jako komentarz.](./media/containers/containers-state-failed-select.png)  
1. Uruchom zapytanie, a następnie rozwiń wiersz w wynikach, aby wyświetlić identyfikator obrazu.  
   ![Zrzut ekranu pokazujący sposób wyświetlania identyfikatora obrazu.](./media/containers/containers-state-failed.png)  
1. Wpisz następujące w zapytaniu dziennika. `ContainerImageInventory | where ImageID == <ImageID>` Aby wyświetlić szczegółowe informacje o obrazie, takie jak rozmiar obrazu i Liczba obrazów zatrzymanych i zakończonych niepowodzeniem.  
   ![Kontenery zakończone niepowodzeniem](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Dzienniki zapytań dla danych kontenera

W przypadku rozwiązywania problemów z określonym błędem może być pomocne sprawdzenie, gdzie występuje w danym środowisku. Poniższe typy dzienników ułatwią tworzenie zapytań w celu zwrócenia żądanych informacji.

- **ContainerImageInventory** — Użyj tego typu, gdy próbujesz znaleźć informacje uporządkowane według obrazu i wyświetlać informacje o obrazach, takie jak identyfikatory obrazów lub rozmiary.
- **ContainerInventory** — Użyj tego typu, aby uzyskać informacje o lokalizacji kontenera, jego nazwach i obrazach, które są w nich uruchamiane.
- **ContainerLog** — Użyj tego typu, aby znaleźć szczegółowe informacje o dzienniku błędów i wpisy.
- **ContainerNodeInventory_CL**  Użyj tego typu, aby uzyskać informacje na temat hosta/węzła, gdzie znajdują się kontenery. Zapewnia ona wersję platformy Docker, typ aranżacji, magazyn i informacje o sieci.
- **ContainerProcess_CL** Użyj tego typu, aby szybko zobaczyć proces uruchomiony w kontenerze.
- **ContainerServiceLog** — ten typ jest używany podczas próby znalezienia informacji o dzienniku inspekcji dla demona platformy Docker, takich jak uruchamianie, zatrzymywanie, usuwanie lub ściąganie poleceń.
- **KubeEvents_CL**  Użyj tego typu, aby wyświetlić zdarzenia Kubernetes.
- **KubePodInventory_CL**  Użyj tego typu, aby zrozumieć informacje o hierarchii klastra.

### <a name="to-query-logs-for-container-data"></a>Aby zbadać dzienniki dla danych kontenera

* Wybierz nieznany obraz, który został niedawno zakończony niepowodzeniem i Znajdź w nim dzienniki błędów. Zacznij od znalezienia nazwy kontenera, na którym działa ten obraz z wyszukiwaniem **ContainerInventory** . Na przykład wyszukaj ciąg `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Wyszukaj kontenery Ubuntu](./media/containers/search-ubuntu.png)

  Rozwiń dowolny wiersz w wynikach, aby wyświetlić szczegóły dla tego kontenera.

## <a name="example-log-queries"></a>Przykładowe zapytania dziennika

Często warto tworzyć zapytania, rozpoczynając od przykładu lub dwa, a następnie modyfikując je tak, aby pasowały do środowiska. Jako punkt początkowy można eksperymentować z obszarem **zapytań przykładowych** po prawej stronie rozwiązania, aby ułatwić tworzenie bardziej zaawansowanych zapytań.

![Zapytania kontenerów](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Zapisywanie zapytań dziennika

Zapisywanie zapytań jest standardową funkcją w Azure Monitor. Zapisując je, będziesz mieć te, które okazały się przydatne do użycia w przyszłości.

Po utworzeniu kwerendy, która jest przydatna, Zapisz ją, klikając pozycję **Ulubione** w górnej części strony przeszukiwania dzienników. Następnie możesz łatwo uzyskać do niego dostęp z poziomu strony **mój pulpit nawigacyjny** .

## <a name="next-steps"></a>Następne kroki

[Dzienniki zapytań](../logs/log-query-overview.md) , aby wyświetlić szczegółowe rekordy danych kontenera.

