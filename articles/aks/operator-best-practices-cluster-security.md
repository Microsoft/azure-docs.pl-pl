---
title: Najlepsze rozwiązania dotyczące zabezpieczeń klastra
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania operatora klastra dotyczące zarządzania zabezpieczeniami i uaktualnieniami klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: ea63db2d8868a1333ae264c9cfdf31d0b7397a83
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105157"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zabezpieczeń klastrów i uaktualnień w usłudze Azure Kubernetes Service (AKS)

W miarę jak zarządzanie klastrami w usłudze Azure Kubernetes Service (AKS), obciążenie i bezpieczeństwo danych jest kluczowym zagadnieniem. W przypadku uruchamiania klastrów wielodostępnych przy użyciu izolacji logicznej, w szczególności należy zabezpieczyć dostęp do zasobów i obciążeń. Należy zminimalizować ryzyko ataku przez zastosowanie najnowszych aktualizacji zabezpieczeń Kubernetes i węzłów systemu operacyjnego.

W tym artykule omówiono sposób zabezpieczania klastra AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Użyj Azure Active Directory i Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) do zabezpieczania dostępu do serwera interfejsu API.
> * Bezpieczny dostęp do kontenera do zasobów węzła.
> * Uaktualnij klaster AKS do najnowszej wersji programu Kubernetes.
> * Utrzymywanie Aktualności węzłów i automatyczne stosowanie poprawek zabezpieczeń.

Można także zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zarządzania obrazami kontenerów][best-practices-container-image-management] i [zabezpieczenia pod][best-practices-pod-security].

Możesz również użyć [integracji usług Azure Kubernetes Services z usługą Security Center][security-center-aks] , aby pomóc wykrywać zagrożenia i wyświetlać zalecenia dotyczące zabezpieczania klastrów AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Zabezpieczanie dostępu do serwera interfejsu API i węzłów klastra

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Jednym z najważniejszych sposobów zabezpieczania klastra jest Zabezpieczanie dostępu do serwera interfejsu API Kubernetes. Aby kontrolować dostęp do serwera interfejsu API, Zintegruj Kubernetes RBAC z usługą Azure Active Directory (Azure AD). Dzięki tym kontrolkom można zabezpieczyć AKS w taki sam sposób, jak bezpieczny dostęp do subskrypcji platformy Azure.

Serwer interfejsu API Kubernetes zapewnia pojedynczy punkt połączenia dla żądań wykonywania akcji w ramach klastra. Aby zabezpieczyć i kontrolować dostęp do serwera interfejsu API, należy ograniczyć dostęp i zapewnić najniższy możliwy poziom uprawnień. Chociaż ta metoda nie jest unikatowa dla Kubernetes, jest szczególnie ważna w przypadku logicznie izolowania klastra AKS w celu użycia wielu dzierżawców.

Usługa Azure AD oferuje gotowe do używania w przedsiębiorstwie rozwiązanie do zarządzania tożsamościami, które integruje się z klastrami AKS. Ponieważ Kubernetes nie zapewnia rozwiązania do zarządzania tożsamościami, może być trudno naciśnięty, aby szczegółowo ograniczyć dostęp do serwera interfejsu API. W przypadku klastrów zintegrowanych z usługą Azure AD w programie AKS można używać istniejących kont użytkowników i grup do uwierzytelniania użytkowników na serwerze interfejsu API.

![Integracja Azure Active Directory klastrów AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Korzystając z Kubernetes RBAC i usługi Azure AD — integracja, można zabezpieczyć serwer interfejsu API i podać minimalne uprawnienia wymagane do zestawu zasobów w zakresie, jak w przypadku pojedynczej przestrzeni nazw. Różne role Kubernetes można przyznać różnym użytkownikom lub grupom usługi Azure AD. Mając szczegółowe uprawnienia, można ograniczyć dostęp do serwera interfejsu API i zapewnić czysty dziennik inspekcji wykonanych akcji.

Zalecanym najlepszym rozwiązaniem jest użycie *grup* w celu zapewnienia dostępu do plików i folderów zamiast poszczególnych tożsamości. Na przykład użyj członkostwa w *grupie* usługi Azure AD, aby powiązać użytkowników z rolami Kubernetes, a nie pojedynczymi *użytkownikami*. W miarę zmiany członkostwa w grupach użytkowników ich uprawnienia dostępu do klastra AKS są odpowiednio zmieniane. 

Załóżmy, że powiążesz pojedynczego użytkownika bezpośrednio z rolą, a ich funkcja zadania zmienia się. W trakcie aktualizacji członkostwa w grupach usługi Azure AD ich uprawnienia w klastrze AKS nie są. W tym scenariuszu użytkownik zostanie zakończony z większą liczbą uprawnień niż to wymagane.

Aby uzyskać więcej informacji na temat integracji z usługą Azure AD, Kubernetes RBAC i usługi Azure RBAC, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Bezpieczny dostęp do kontenera do zasobów

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Ogranicz dostęp do akcji, które kontenery mogą wykonywać. Podaj minimalną liczbę uprawnień i Unikaj korzystania z głównego dostępu lub eskalacji uprzywilejowanej.

W taki sam sposób, w jaki należy udzielić użytkownikom lub grupom minimalnych wymaganych uprawnień, należy również ograniczyć kontenery tylko do niezbędnych akcji i procesów. Aby zminimalizować ryzyko ataku, należy unikać konfigurowania aplikacji i kontenerów, które wymagają eskalacji uprawnień lub dostępu głównego. 

Na przykład ustaw `allowPrivilegeEscalation: false` w manifeście pod. Te wbudowane *konteksty zabezpieczeń* Kubernetes na poziomie umożliwiają zdefiniowanie dodatkowych uprawnień, takich jak użytkownik lub Grupa do uruchamiania jako lub możliwość udostępnienia systemu Linux. Aby uzyskać więcej najlepszych rozwiązań, zobacz [bezpieczny dostęp pod dostępem do zasobów][pod-security-contexts].

Aby uzyskać bardziej szczegółową kontrolę nad akcjami kontenera, możesz również użyć wbudowanych funkcji zabezpieczeń systemu Linux, takich jak *AppArmor* i *seccomp*. 
1. Zdefiniuj funkcje zabezpieczeń systemu Linux na poziomie węzła.
1. Zaimplementuj funkcje za pomocą manifestu pod. 

Wbudowane funkcje zabezpieczeń systemu Linux są dostępne tylko w węzłach i w systemach Linux.

> [!NOTE]
> Obecnie środowiska Kubernetes nie są całkowicie bezpieczne dla niebezpiecznym użyciem wielu dzierżawców. Dodatkowe funkcje zabezpieczeń, takie jak *AppArmor*, *Seccomp*,*pod zasadami zabezpieczeń* lub Kubernetes RBAC dla węzłów, efektywnie uniemożliwiają luki w zabezpieczeniach. 
>
>W celu zapewnienia prawdziwych zabezpieczeń w przypadku nieprzechodnich obciążeń z wieloma dzierżawcami należy jedynie zaufać funkcji hypervisor. Domena zabezpieczeń dla Kubernetes jest cały klaster, a nie pojedynczy węzeł. 
>
> W przypadku tych typów nieszkodliwych obciążeń z wieloma dzierżawcami należy używać klastrów fizycznie izolowanych.

### <a name="app-armor"></a>Ochrona aplikacji

Aby ograniczyć akcje kontenera, można użyć modułu zabezpieczeń jądra [AppArmor][k8s-apparmor] systemu Linux. AppArmor jest dostępny jako część podstawowego systemu operacyjnego węzła AKS i jest domyślnie włączony. Tworzysz profile AppArmor, które ograniczają operacje odczytu, zapisu lub wykonywania, lub funkcje systemowe, takie jak Instalowanie systemów plików. Domyślne profile AppArmor ograniczają dostęp do `/proc` różnych `/sys` lokalizacji i zapewniają sposób logicznego izolowania kontenerów z bazowego węzła. AppArmor działa dla każdej aplikacji działającej w systemie Linux, a nie tylko Kubernetes.

![Profile AppArmor używane w klastrze AKS do ograniczania akcji kontenera](media/operator-best-practices-container-security/apparmor.png)

Aby wyświetlić AppArmor w działaniu, w poniższym przykładzie tworzony jest profil, który uniemożliwia zapis do plików. 
1. Protokół [SSH][aks-ssh] do węzła AKS.
1. Utwórz plik o nazwie *Odmów-Write. profile*.
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

Profile AppArmor są dodawane za pomocą `apparmor_parser` polecenia. 
1. Dodaj profil do AppArmor.
1. Określ nazwę profilu utworzonego w poprzednim kroku:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Jeśli profil jest prawidłowo analizowany i stosowany do AppArmor, nie będą wyświetlane żadne dane wyjściowe i zostanie zwrócony wiersz polecenia.

1. Na komputerze lokalnym Utwórz manifest pod nazwą *AKS-AppArmor. YAML*. Ten manifest:
    * Definiuje adnotację dla `container.apparmor.security.beta.kubernetes` .
    * Odwołuje się do profilu *Odmów/zapisu* utworzonego w poprzednich krokach.

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

1. Po wdrożeniu należy użyć Sprawdź, czy *Witaj, AppArmor* pod jako *zablokowane*:

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Aby uzyskać więcej informacji na temat AppArmor, zobacz [Profile AppArmor w Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Zabezpieczanie obliczeń

Podczas gdy AppArmor działa dla dowolnej aplikacji systemu Linux, [seccomp (*s* uruj *COMP* uting)][seccomp] działa na poziomie procesu. Seccomp jest również modułem zabezpieczeń jądra systemu Linux i jest natywnie obsługiwany przez środowisko uruchomieniowe Docker używane przez węzły AKS. Za pomocą seccomp można ograniczyć wywołania procesu kontenera. Dopasuj do najlepszego rozwiązania dotyczącego przyznania kontenera minimalnych uprawnień tylko do uruchomienia przez:
* Definiowanie z filtrami, jakie akcje mają być dozwolone lub odrzucane.
* Dodawanie adnotacji wewnątrz manifestu YAML pod kątem skojarzenia z filtrem seccomp. 

Aby wyświetlić seccomp w akcji, Utwórz filtr uniemożliwiający zmianę uprawnień do pliku. 
1. Protokół [SSH][aks-ssh] do węzła AKS.
1. Utwórz filtr seccomp o nazwie */var/lib/kubelet/seccomp/Prevent-chmod*.
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

    W wersji 1,19 i nowszych należy skonfigurować następujące elementy:

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

1. Na komputerze lokalnym Utwórz manifest pod nazwą *AKS-seccomp. YAML* i wklej poniższą zawartość. Ten manifest:
    * Definiuje adnotację dla `seccomp.security.alpha.kubernetes.io` .
    * Odwołuje się do filtru *zapobiegania chmod* utworzonego w poprzednim kroku.

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

    W wersji 1,19 i nowszych należy skonfigurować następujące elementy:

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

1. Wdróż przykład przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Wyświetl stan pod za pomocą polecenia [polecenia kubectl Get-podst][kubectl-get] . 
    * Na stronie raportowany jest błąd. 
    * `chmod`Polecenie nie jest uruchamiane przez filtr seccomp, jak pokazano w następujących przykładowych danych wyjściowych:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Aby uzyskać więcej informacji na temat dostępnych filtrów, zobacz [Profile zabezpieczeń Seccomp dla platformy Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regularnie Aktualizuj do najnowszej wersji programu Kubernetes

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Aby zachować bieżące informacje o nowych funkcjach i poprawkach błędów, należy regularnie uaktualnić wersję Kubernetes w klastrze AKS.

Kubernetes zwalnia nowe funkcje w szybszym tempie niż w przypadku tradycyjnych platform infrastruktury. Aktualizacje Kubernetes obejmują:
* Nowe funkcje
* Poprawki błędów lub zabezpieczeń 

Nowe funkcje zwykle przechodzą przez stan *alfa* i *beta* , zanim staną się *stabilne*. Gdy jest stabilny, są ogólnie dostępne i zalecane do użycia w środowisku produkcyjnym. Nowy cykl wydania funkcji Kubernetes umożliwia zaktualizowanie Kubernetes bez regularnego napotkania istotnych zmian lub dostosowania wdrożeń i szablonów.

AKS obsługuje trzy drobne wersje programu Kubernetes. Po wprowadzeniu nowej wersji poprawki pomocniczej obsługiwane są najstarsze wersje pomocnicze i poprawki. Pomocnicze aktualizacje Kubernetes są wykonywane okresowo. Aby zachować pomoc techniczną, upewnij się, że masz proces ładu, aby sprawdzić, czy są wymagane uaktualnienia. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje KUBERNETES AKS][aks-supported-versions].

Aby sprawdzić wersje dostępne dla klastra, użyj polecenia [AZ AKS Get-Upgrades][az-aks-get-upgrades] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Następnie można uaktualnić klaster AKS za pomocą polecenia [AZ AKS upgrade][az-aks-upgrade] . Proces uaktualniania jest bezpieczny:
* Cordons i opróżnia jeden węzeł w danym momencie.
* Planuje w pozostałych węzłach.
* Wdraża nowy węzeł z najnowszymi wersjami systemów operacyjnych i Kubernetes.

>[!IMPORTANT]
> Przetestuj nowe wersje pomocnicze w środowisku testowym deweloperskim i sprawdź, czy obciążenie pozostaje w dobrej kondycji z nową wersją Kubernetes. 
>
> Kubernetes może przestarzałe interfejsy API (jak w wersji 1,16), na których bazują obciążenia. Podczas wprowadzania nowych wersji do środowiska produkcyjnego należy rozważyć użycie [wielu pul węzłów w różnych wersjach](use-multiple-node-pools.md) i uaktualnienie pojedynczych pul pojedynczo w celu stopniowego wycofania aktualizacji w klastrze. W przypadku uruchamiania wielu klastrów należy uaktualnić jeden klaster jednocześnie, aby stopniowo monitorować wpływ lub zmiany.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Aby uzyskać więcej informacji na temat uaktualnień w programie AKS, zobacz [obsługiwane wersje Kubernetes w AKS][aks-supported-versions] i [Uaktualnij klaster AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Przetwarzanie aktualizacji węzłów systemu Linux i ponownych uruchomień przy użyciu kured

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Podczas gdy program AKS automatycznie pobiera i instaluje poprawki zabezpieczeń w każdym węźle systemu Linux, nie jest automatycznie uruchamiany ponownie. 
> 1. Użyj `kured` , aby obejrzeć oczekiwanie na ponowne uruchomienie.
> 1. Bezpiecznie Cordon i Opróżniaj węzeł, aby umożliwić ponowne uruchomienie węzła.
> 1. Zastosuj aktualizacje.
> 1. Być możliwie jak najbardziej bezpieczne w odniesieniu do systemu operacyjnego. 

W przypadku węzłów systemu Windows Server regularnie wykonaj operację uaktualnienia AKS, aby bezpiecznie Cordon i opróżnić i wdrożyć zaktualizowane węzły.

Każdy wieczór, węzły systemu Linux w AKS pobierają poprawki zabezpieczeń za pośrednictwem ich kanału aktualizacji dystrybucji. To zachowanie jest automatycznie konfigurowane, ponieważ węzły są wdrażane w klastrze AKS. Aby zminimalizować zakłócenia i potencjalny wpływ na uruchamianie obciążeń, węzły nie są automatycznie uruchamiane ponownie, Jeśli poprawka zabezpieczeń lub Aktualizacja jądra tego wymaga.

Projekt typu open source [kured (KUbernetes Boot daemon)][kured] przez Weaveworks czujki dla oczekujących ponownych uruchomień węzłów. Gdy węzeł systemu Linux zastosuje aktualizacje, które wymagają ponownego uruchomienia, węzeł jest bezpiecznie odizolowywane i opróżniany w celu przeniesienia i zaplanowania w innych węzłach w klastrze. Po ponownym uruchomieniu węzła zostanie on dodany z powrotem do klastra, a Kubernetes zostaje wznowiony. Aby zminimalizować zakłócenia, może być uruchamiany ponownie tylko jeden węzeł w danym momencie `kured` .

![Proces ponownego uruchamiania węzła AKS przy użyciu kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Jeśli chcesz mieć jeszcze ściślejszą kontrolę nad ponownymi uruchomieniami, `kured` można zintegrować z usługą Prometheus, aby zapobiec ponownym uruchomieniu w przypadku wystąpienia innych zdarzeń konserwacji lub problemów z klastrem. Ta integracja zmniejsza stopień komplikacji przez ponowne uruchomienie węzłów podczas aktywnego rozwiązywania problemów z innymi problemami.

Więcej informacji o sposobie obsługi ponownych uruchomień węzłów znajduje się [w temacie stosowanie aktualizacji zabezpieczeń i jądra do węzłów w AKS][aks-kured].

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na sposobie zabezpieczania klastra AKS. Aby zaimplementować niektóre z tych obszarów, zobacz następujące artykuły:

* [Integracja Azure Active Directory z usługą AKS][aks-aad]
* [Uaktualnianie klastra AKS do najnowszej wersji programu Kubernetes][aks-upgrade]
* [Przetwarzanie aktualizacji zabezpieczeń i ponownych uruchomień węzłów za pomocą kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
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
