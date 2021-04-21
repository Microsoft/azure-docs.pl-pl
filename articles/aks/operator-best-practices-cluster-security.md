---
title: Najlepsze rozwiązania dotyczące zabezpieczeń klastra
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania dotyczące operatora klastra dotyczące zarządzania zabezpieczeniami i uaktualnieniami klastra w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 5cb103d843aafbb7f72c03d65b45fe3a84f8d1cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782985"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zabezpieczeń klastra i uaktualnień w Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w Azure Kubernetes Service (AKS) kluczowe znaczenie ma bezpieczeństwo obciążeń i danych. W przypadku uruchamiania klastrów wielodostępnych przy użyciu izolacji logicznej należy szczególnie zabezpieczyć dostęp do zasobów i obciążeń. Zminimalizuj ryzyko ataku, stosując najnowsze aktualizacje zabezpieczeń systemu operacyjnego węzła i rozwiązania Kubernetes.

W tym artykule opisano sposób zabezpieczania klastra usługi AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Użyj Azure Active Directory kontroli dostępu opartej na rolach (RBAC) platformy Kubernetes i platformy Kubernetes, aby zabezpieczyć dostęp do serwera interfejsu API.
> * Bezpieczny dostęp kontenera do zasobów węzła.
> * Uaktualnij klaster AKS do najnowszej wersji rozwiązania Kubernetes.
> * Utrzymuj aktualizacje węzłów i automatycznie zastosuj poprawki zabezpieczeń.

Możesz również zapoznać się z najlepszymi rozwiązaniami w zakresie zarządzania [obrazami kontenerów][best-practices-container-image-management] i zabezpieczeń [zasobników.][best-practices-pod-security]

Możesz również użyć [integracji usług Azure Kubernetes Services z usługą Security Center,][security-center-aks] aby ułatwić wykrywanie zagrożeń i wyświetlanie zaleceń dotyczących zabezpieczania klastrów usługi AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Bezpieczny dostęp do serwera interfejsu API i węzłów klastra

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Jednym z najważniejszych sposobów zabezpieczania klastra jest zabezpieczenie dostępu do serwera interfejsu API Kubernetes. Aby kontrolować dostęp do serwera interfejsu API, zintegruj kontrolę RBAC platformy Kubernetes z Azure Active Directory (Azure AD). Za pomocą tych kontrolek zabezpieczasz usługę AKS w taki sam sposób, jak zabezpieczasz dostęp do subskrypcji platformy Azure.

Serwer interfejsu API platformy Kubernetes zapewnia jeden punkt połączenia dla żądań do wykonania akcji w klastrze. Aby zabezpieczyć i zweryfikować dostęp do serwera interfejsu API, ogranicz dostęp i zapewnij najniższe możliwe poziomy uprawnień. Chociaż to podejście nie jest unikatowe dla rozwiązania Kubernetes, jest szczególnie ważne w przypadku logicznego odizolowania klastra usługi AKS do użycia z wieloma dzierżawami.

Usługa Azure AD udostępnia rozwiązanie do zarządzania tożsamościami gotowe do użycia w przedsiębiorstwie, które integruje się z klastrami usługi AKS. Ponieważ rozwiązanie Kubernetes nie zapewnia rozwiązania do zarządzania tożsamościami, możesz mieć trudności ze szczegółowym ograniczeniem dostępu do serwera interfejsu API. Klastry zintegrowane z usługą Azure AD w usłudze AKS używają istniejących kont użytkowników i grup do uwierzytelniania użytkowników na serwerze interfejsu API.

![Azure Active Directory integracji klastrów usługi AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Korzystając z kontroli dostępu na poziomie ról platformy Kubernetes i integracji z usługą Azure AD, można zabezpieczyć serwer interfejsu API i zapewnić minimalne uprawnienia wymagane do zestawu zasobów o zakresie, takiego jak pojedyncza przestrzeń nazw. Możesz przyznać różnym użytkownikom lub grupom usługi Azure AD różne role platformy Kubernetes. Za pomocą szczegółowych uprawnień można ograniczyć dostęp do serwera interfejsu API i udostępnić przejrzysty dziennik inspekcji wykonanych akcji.

Zalecanym najlepszym rozwiązaniem jest użycie grup *w* celu zapewnienia dostępu do plików i folderów zamiast poszczególnych tożsamości. Możesz na przykład użyć członkostwa w grupie usługi Azure *AD,* aby powiązać użytkowników z rolami platformy Kubernetes, a nie z poszczególnymi *użytkownikami.* W przypadku zmiany członkostwa użytkownika w grupie jego uprawnienia dostępu do klastra usługi AKS odpowiednio się zmieniają. 

W międzyczasie załóżmy, że powiąż indywidualnego użytkownika bezpośrednio z rolą, a jego funkcja zmieni się. Podczas aktualizowania członkostwa w grupach usługi Azure AD ich uprawnienia w klastrze usługi AKS nie będą miały uprawnień. W tym scenariuszu użytkownik ma więcej uprawnień niż jest to wymagane.

Aby uzyskać więcej informacji na temat integracji usługi Azure AD, kontroli RBAC platformy Kubernetes i kontroli RBAC platformy Azure, zobacz Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji [w usłudze AKS.][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Bezpieczny dostęp kontenera do zasobów

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Ogranicz dostęp do akcji, które mogą wykonywać kontenery. Podaj najmniejszą liczbę uprawnień i unikaj korzystania z dostępu do konta głównego lub eskalacji uprzywilejowanej.

W taki sam sposób, w jaki należy przyznać użytkownikom lub grupom minimalne wymagane uprawnienia, należy również ograniczyć kontenery tylko do niezbędnych akcji i procesów. Aby zminimalizować ryzyko ataku, należy unikać konfigurowania aplikacji i kontenerów, które wymagają eskalacji uprawnień lub dostępu do katalogu głównego. 

Na przykład ustaw wartość `allowPrivilegeEscalation: false` w manifeście zasobnika. Te wbudowane konteksty zabezpieczeń  zasobników kubernetes umożliwiają definiowanie dodatkowych uprawnień, takich jak użytkownik lub grupa, która ma być uruchamiana jako, lub możliwości systemu Linux do uwidocznienia. Aby uzyskać więcej najlepszych rozwiązań, zobacz [Bezpieczny dostęp zasobnika do zasobów.][pod-security-contexts]

Aby uzyskać jeszcze bardziej szczegółową kontrolę nad akcjami kontenera, można również użyć wbudowanych funkcji zabezpieczeń systemu Linux, takich jak *AppArmor* *i seccomp.* 
1. Zdefiniuj funkcje zabezpieczeń systemu Linux na poziomie węzła.
1. Implementowanie funkcji za pomocą manifestu zasobnika. 

Wbudowane funkcje zabezpieczeń systemu Linux są dostępne tylko w węzłach i zasobnikach systemu Linux.

> [!NOTE]
> Obecnie środowiska Kubernetes nie są całkowicie bezpieczne w przypadku użycia hostów wielodostępnych. Dodatkowe funkcje zabezpieczeń, takie jak *AppArmor,* *seccomp,**Zasady* zabezpieczeń zasobników lub Kubernetes RBAC dla węzłów, skutecznie blokują luki w zabezpieczeniach. 
>
>Aby zapewnić prawdziwe zabezpieczenia podczas uruchamiania hostowych obciążeń wielodostępnych, ufaj tylko funkcji hypervisor. Domena zabezpieczeń dla usługi Kubernetes staje się całym klastrem, a nie indywidualnym węzłem. 
>
> W przypadku tego typu hostowanych obciążeń wielodostępnych należy używać fizycznie izolowanych klastrów.

### <a name="app-armor"></a>App Armor

Aby ograniczyć akcje kontenera, można użyć modułu zabezpieczeń jądra [AppArmor][k8s-apparmor] systemu Linux. Funkcja AppArmor jest dostępna jako część podstawowego systemu operacyjnego węzła usługi AKS i jest domyślnie włączona. Tworzysz profile AppArmor, które ograniczają akcje odczytu, zapisu lub wykonywania albo funkcje systemowe, takie jak instalowanie systemów plików. Domyślne profile AppArmor ograniczają dostęp do różnych lokalizacji i oraz zapewniają sposób logicznego izolowania kontenerów `/proc` `/sys` od węzła źródłowego. AppArmor działa w przypadku każdej aplikacji, która działa w systemie Linux, a nie tylko zasobników Kubernetes.

![Profile AppArmor w użyciu w klastrze usługi AKS w celu ograniczenia akcji kontenera](media/operator-best-practices-container-security/apparmor.png)

Aby zobaczyć, jak działa aplikacja AppArmor, poniższy przykład tworzy profil, który uniemożliwia zapisywanie w plikach. 
1. [Nasyć SSH][aks-ssh] z węzłem AKS.
1. Utwórz plik o nazwie *deny-write.profile.*
1. Wklej następującą zawartość:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

Profile AppArmor są dodawane przy użyciu `apparmor_parser` polecenia . 
1. Dodaj profil do aplikacji AppArmor.
1. Określ nazwę profilu utworzonego w poprzednim kroku:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Jeśli profil jest poprawnie analizowany i stosowany do aplikacji AppArmor, nie będą wyświetlane żadne dane wyjściowe i zostanie wyświetlony wiersz polecenia.

1. Na komputerze lokalnym utwórz manifest zasobnika o nazwie *aks-apparmor.yaml.* Ten manifest:
    * Definiuje adnotację dla `container.apparmor.security.beta.kubernetes` .
    * Odwołuje się *do profilu odmowy zapisu* utworzonego w poprzednich krokach.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. Po wdrożeniu zasobnika użyj funkcji verify *the hello-apparmor* pod shows as *blocked*( Sprawdź, czy zasobnik hello-apparmor jest zablokowany):

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Aby uzyskać więcej informacji na temat programu AppArmor, zobacz [AppArmor profiles in Kubernetes (Profile AppArmor na platformie Kubernetes).][k8s-apparmor]

### <a name="secure-computing"></a>Bezpieczne przetwarzanie

Program AppArmor działa dla dowolnej aplikacji systemu Linux, natomiast [plik seccomp *(sec* ure *comp uting)*][seccomp] działa na poziomie procesu. Seccomp jest również modułem zabezpieczeń jądra systemu Linux i jest natywnie obsługiwany przez środowisko uruchomieniowe platformy Docker używane przez węzły usługi AKS. Za pomocą seccomp można ograniczyć wywołania procesów kontenerów. Dopasuj do najlepszego rozwiązania w zakresie udzielania kontenerowi minimalnych uprawnień tylko do uruchamiania przez:
* Definiowanie za pomocą filtrów określa, jakie akcje mają być zezwalane lub odrzucane.
* Adnotacje w manifeście YAML zasobnika w celu skojarzenia z filtrem seccomp. 

Aby zobaczyć seccomp w akcji, utwórz filtr, który uniemożliwia zmianę uprawnień do pliku. 
1. [SSH][aks-ssh] z węzłem AKS.
1. Utwórz filtr seccomp o *nazwie /var/lib/kubelet/seccomp/prevent-chmod.*
1. Wklej następującą zawartość:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    W wersji 1.19 lub nowszej należy skonfigurować następujące elementy:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. Na komputerze lokalnym utwórz manifest zasobnika o nazwie *aks-seccomp.yaml* i wklej następującą zawartość. Ten manifest:
    * Definiuje adnotację dla `seccomp.security.alpha.kubernetes.io` .
    * Odwołuje się *do filtru prevent-chmod* utworzonego w poprzednim kroku.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    W wersji 1.19 lub nowszej należy skonfigurować następujące elementy:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. Wd wdrażaj przykładowy zasobnik przy użyciu [polecenia kubectl apply:][kubectl-apply]

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Wyświetl stan zasobnika za pomocą [polecenia kubectl get pods.][kubectl-get] 
    * Zasobnik zgłasza błąd. 
    * Polecenie `chmod` nie może być uruchomione przez filtr seccomp, jak pokazano w następujących przykładowych danych wyjściowych:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Aby uzyskać więcej informacji na temat dostępnych filtrów, zobacz [Seccomp security profiles for Docker (Zabezpieczanie profilów zabezpieczeń platformy Docker).][seccomp]

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regularne aktualizowanie do najnowszej wersji rozwiązania Kubernetes

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Aby być na bieżąco z nowymi funkcjami i poprawkami błędów, regularnie uaktualniaj wersję rozwiązania Kubernetes w klastrze usługi AKS.

Kubernetes wydaje nowe funkcje w szybszym tempie niż tradycyjne platformy infrastruktury. Aktualizacje kubernetes obejmują:
* Nowe funkcje
* Poprawki usterek lub zabezpieczeń 

Nowe funkcje zwykle są w stanie *alfa* i *beta,* zanim staną się *stabilne.* Gdy jest stabilna, są ogólnie dostępne i zalecane do użytku produkcyjnego. Cykl wydania nowej funkcji rozwiązania Kubernetes umożliwia aktualizację rozwiązania Kubernetes bez regularnego napotykania zmian niestandardowych ani dostosowywania wdrożeń i szablonów.

AKS obsługuje trzy pomocnicze wersje kubernetes. Po wprowadzeniu nowej wersji pomocniczej poprawki najstarsza obsługiwana wersja pomocnicza i wersje poprawek zostaną wycofane. Drobne aktualizacje kubernetes są okresowo dostępne. Aby utrzymać pomoc techniczną, upewnij się, że masz proces nadzoru w celu sprawdzenia niezbędnych uaktualnień. Aby uzyskać więcej informacji, zobacz [Obsługiwane wersje kubernetes AKS.][aks-supported-versions]

Aby sprawdzić wersje dostępne dla klastra, użyj polecenia [az aks get-upgrades,][az-aks-get-upgrades] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Następnie możesz uaktualnić klaster usługi AKS przy użyciu [polecenia az aks upgrade.][az-aks-upgrade] Proces uaktualniania jest bezpieczny:
* Cordon i opróżnia po jednym węźle na raz.
* Planuje zasobniki na pozostałych węzłach.
* Wdraża nowy węzeł z najnowszymi wersjami systemu operacyjnego i rozwiązania Kubernetes.

>[!IMPORTANT]
> Przetestuj nowe wersje pomocnicze w środowisku testowym dewelopera i sprawdź, czy obciążenie pozostaje w dobrej kondycji, dzięki nowej wersji kubernetes. 
>
> Kubernetes może oznaczać interfejsy API jako przestarzałe (tak jak w wersji 1.16), z których korzystają obciążenia. Podczas wprowadzania nowych wersji do [](use-multiple-node-pools.md) produkcji należy rozważyć użycie wielu pul węzłów w oddzielnych wersjach i uaktualnić poszczególne pule pojedynczo w celu stopniowego wprowadzania aktualizacji w klastrze. W przypadku uruchamiania wielu klastrów uaktualnij jeden klaster na raz, aby stopniowo monitorować wpływ lub zmiany.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Aby uzyskać więcej informacji na temat uaktualnień usługi AKS, zobacz Obsługiwane wersje [usługi Kubernetes][aks-supported-versions] w u usługi AKS i [Uaktualnianie klastra usługi AKS.][aks-upgrade]

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Przetwarzanie aktualizacji i ponownych uruchomień węzłów systemu Linux przy użyciu systemu Linux

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Chociaż w każdym węźle systemu Linux jest automatycznie pobierany i instalowany system AKS, nie jest on automatycznie ponownie uruchamiany. 
> 1. Użyj `kured` , aby obserwować oczekujące ponowne uruchomienia.
> 1. Bezpiecznie odiszukaj i opróżnij węzeł, aby umożliwić jego ponowne uruchomienie.
> 1. Zastosuj aktualizacje.
> 1. Zapewnij jak najlepsze bezpieczeństwo systemu operacyjnego. 

W przypadku węzłów systemu Windows Server regularnie wykonuj operację uaktualniania usługi AKS, aby bezpiecznie odień i opróżnić zasobniki oraz wdrożyć zaktualizowane węzły.

Każdego wieczoru węzły systemu Linux w UKS uzyskają poprawki zabezpieczeń za pośrednictwem kanału aktualizacji dystrybucji. To zachowanie jest automatycznie konfigurowane podczas wdrażania węzłów w klastrze usługi AKS. Aby zminimalizować zakłócenia i potencjalny wpływ na uruchomione obciążenia, węzły nie są automatycznie ponownie uruchamiane, jeśli wymaga tego poprawka zabezpieczeń lub aktualizacja jądra.

Projekt open source ["open source" (KUbernetes REboot Daemon)][kured] autorstwaWorks obserwuje oczekiwanie na ponowne uruchomienie węzła. Gdy węzeł systemu Linux stosuje aktualizacje wymagające ponownego uruchomienia, węzeł jest bezpiecznie odieńczany i opróżniony w celu przeniesienia i zaplanowania zasobników w innych węzłach w klastrze. Po ponownym uruchomieniu węzeł jest ponownie dodawany do klastra, a usługa Kubernetes wznawia planowanie zasobników. Aby zminimalizować zakłócenia, tylko jeden węzeł na raz może zostać ponownie uruchomiony przez usługę `kured` .

![Proces ponownego uruchamiania węzła usługi AKS przy użyciu narzędzia nodeed](media/operator-best-practices-cluster-security/node-reboot-process.png)

Jeśli chcesz jeszcze dokładniej kontrolować ponowne uruchamianie, możesz zintegrować program z usługą Prometheus, aby zapobiec ponownemu uruchamianiu w przypadku innych zdarzeń konserwacji lub problemów `kured` z klastrem w toku. Ta integracja zmniejsza komplikacje przez ponowne uruchamianie węzłów podczas aktywnego rozwiązywania innych problemów.

Aby uzyskać więcej informacji na temat sposobu obsługi ponownego uruchamiania węzłów, zobacz Apply security and kernel updates to nodes in AKS (Stosowanie aktualizacji zabezpieczeń i [aktualizacji jądra do węzłów w UKS).][aks-kured]

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób zabezpieczania klastra usługi AKS. Aby zaimplementować niektóre z tych obszarów, zobacz następujące artykuły:

* [Integrowanie Azure Active Directory z aks][aks-aad]
* [Uaktualnianie klastra usługi AKS do najnowszej wersji rozwiązania Kubernetes][aks-upgrade]
* [Przetwarzanie aktualizacji zabezpieczeń i ponownych uruchomień węzłów za pomocą programu][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
