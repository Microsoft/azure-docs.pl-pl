---
title: Najlepsze rozwiązania dotyczące zabezpieczeń klastra
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania operatora klastra dotyczące zarządzania zabezpieczeniami i uaktualnieniami klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: a56cf35fe3780aa53b12581358bd91fe44e8c8a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585064"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zabezpieczeń klastrów i uaktualnień w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS), bezpieczeństwo obciążeń i danych jest kluczowym zagadnieniem. Szczególnie w przypadku uruchamiania klastrów wielodostępnych przy użyciu izolacji logicznej należy zabezpieczyć dostęp do zasobów i obciążeń. Aby zminimalizować ryzyko ataku, należy również upewnić się, że zastosowano najnowsze aktualizacje zabezpieczeń Kubernetes i Node OS.

W tym artykule omówiono sposób zabezpieczania klastra AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Używanie Azure Active Directory i Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) do bezpiecznego dostępu do serwera interfejsu API
> * Bezpieczny dostęp do kontenera do zasobów węzła
> * Uaktualnianie klastra AKS do najnowszej wersji Kubernetes
> * Utrzymywanie Aktualności węzłów i automatyczne stosowanie poprawek zabezpieczeń

Można także zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zarządzania obrazami kontenerów][best-practices-container-image-management] i [zabezpieczenia pod][best-practices-pod-security].

Możesz również użyć [integracji usług Azure Kubernetes Services z usługą Security Center][security-center-aks] , aby pomóc wykrywać zagrożenia i wyświetlać zalecenia dotyczące zabezpieczania klastrów AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Zabezpieczanie dostępu do serwera interfejsu API i węzłów klastra

**Wskazówki dotyczące najlepszych** rozwiązań — Zabezpieczanie dostępu do Kubernetes API-Server jest jednym z najważniejszych możliwości zabezpieczania klastra. Integracja kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC) z Azure Active Directory w celu kontrolowania dostępu do serwera interfejsu API. Te kontrolki pozwalają zabezpieczyć AKS w taki sam sposób, jak bezpieczny dostęp do subskrypcji platformy Azure.

Serwer interfejsu API Kubernetes zapewnia pojedynczy punkt połączenia dla żądań wykonywania akcji w ramach klastra. Aby zabezpieczyć i kontrolować dostęp do serwera interfejsu API, należy ograniczyć dostęp i zapewnić wymagane uprawnienia dostępu najmniej uprzywilejowanego. Takie podejście nie jest unikatowe dla Kubernetes, ale jest szczególnie ważne, gdy klaster AKS jest logicznie odizolowany do użycia z wieloma dzierżawcami.

Azure Active Directory (AD) zapewnia rozwiązanie do zarządzania tożsamościami gotowe do używania w przedsiębiorstwie, które integruje się z klastrami AKS. Ponieważ Kubernetes nie oferuje rozwiązania do zarządzania tożsamościami, może być trudno zapewnić szczegółowy sposób ograniczania dostępu do serwera interfejsu API. W przypadku klastrów zintegrowanych z usługą Azure AD w programie AKS można używać istniejących kont użytkowników i grup do uwierzytelniania użytkowników na serwerze interfejsu API.

![Integracja Azure Active Directory klastrów AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Użyj Kubernetes RBAC i Azure AD — integracja, aby zabezpieczyć serwer interfejsu API i zapewnić najmniejszą liczbę uprawnień wymaganą do zakresu zasobów, na przykład pojedynczej przestrzeni nazw. Różnym użytkownikom lub grupom w usłudze Azure AD można przydzielić różne role Kubernetes. Te szczegółowe uprawnienia pozwalają ograniczyć dostęp do serwera interfejsu API i zapewnić czysty dziennik inspekcji wykonanych akcji.

Zalecanym najlepszym rozwiązaniem jest użycie grup w celu zapewnienia dostępu do plików i folderów, a w przypadku pojedynczych tożsamości, używania członkostwa w *grupach* usługi Azure AD w celu powiązania użytkowników z rolami Kubernetes, a nie poszczególnymi *użytkownikami*. Zmiany członkostwa w grupach użytkowników będą odpowiednio zmieniać ich uprawnienia dostępu do klastra AKS. Jeśli użytkownik powiąże się bezpośrednio z rolą, jego funkcja zadania może się zmienić. Członkostwa w grupach usługi Azure AD byłyby aktualizowane, ale uprawnienia w klastrze AKS nie odzwierciedlają tego. W tym scenariuszu użytkownik końcowy otrzymuje więcej uprawnień niż jest to wymagane przez użytkownika.

Aby uzyskać więcej informacji na temat integracji z usługą Azure AD, Kubernetes RBAC i usługi Azure RBAC, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Bezpieczny dostęp do kontenera do zasobów

**Wskazówki dotyczące najlepszych** rozwiązań — ograniczanie dostępu do akcji, które kontenery mogą wykonywać. Podaj minimalną liczbę uprawnień i Unikaj używania eskalacji root/Privileged.

W taki sam sposób, w jaki należy udzielić użytkownikom lub grupom minimalnej liczby wymaganych uprawnień, kontenery powinny również być ograniczone tylko do działań i procesów, które są im potrzebne. Aby zminimalizować ryzyko ataku, nie należy konfigurować aplikacji i kontenerów, które wymagają eskalacji uprawnień lub dostępu głównego. Na przykład ustaw `allowPrivilegeEscalation: false` w manifeście pod. Te *konteksty zabezpieczeń* podlegają wbudowaniu w Kubernetes i pozwalają definiować dodatkowe uprawnienia, takie jak użytkownik lub Grupa do uruchamiania jako lub jakie funkcje systemu Linux mają być ujawnione. Aby uzyskać więcej najlepszych rozwiązań, zobacz [bezpieczny dostęp pod dostępem do zasobów][pod-security-contexts].

Aby uzyskać bardziej szczegółową kontrolę nad akcjami kontenera, możesz również użyć wbudowanych funkcji zabezpieczeń systemu Linux, takich jak *AppArmor* i *seccomp*. Te funkcje są zdefiniowane na poziomie węzła, a następnie implementowane za pomocą manifestu pod. Wbudowane funkcje zabezpieczeń systemu Linux są dostępne tylko w węzłach i w systemach Linux.

> [!NOTE]
> Środowiska Kubernetes, w AKS lub w innym miejscu, nie są całkowicie bezpieczne do korzystania z wielu dzierżawców. Dodatkowe funkcje zabezpieczeń, takie jak *AppArmor*, *Seccomp*, na *zasadach zabezpieczeń* lub bardziej precyzyjna kontrola dostępu oparta na rolach Kubernetes (Kubernetes RBAC) dla węzłów sprawia, że luki w zabezpieczeniach są trudniejsze. Jednak w celu zapewnienia prawdziwych zabezpieczeń przy uruchamianiu nieprzechodnich obciążeń z wieloma dzierżawcami funkcja hypervisor jest jedynym poziomem zabezpieczeń, który należy zaufać. Domena zabezpieczeń dla Kubernetes jest cały klaster, a nie pojedynczy węzeł. W przypadku tych typów nieszkodliwych obciążeń z wieloma dzierżawcami należy używać klastrów fizycznie izolowanych.

### <a name="app-armor"></a>Ochrona aplikacji

Aby ograniczyć akcje, które mogą być wykonywane przez kontenery, można użyć modułu zabezpieczeń jądra [AppArmor][k8s-apparmor] systemu Linux. AppArmor jest dostępny jako część podstawowego systemu operacyjnego węzła AKS i jest domyślnie włączony. Tworzysz profile AppArmor, które ograniczają akcje, takie jak Odczyt, zapis lub wykonywanie, lub funkcje systemowe, takie jak Instalowanie systemów plików. Domyślne profile AppArmor ograniczają dostęp do `/proc` różnych `/sys` lokalizacji i zapewniają sposób logicznego izolowania kontenerów z bazowego węzła. AppArmor działa dla każdej aplikacji działającej w systemie Linux, a nie tylko Kubernetes.

![Profile AppArmor używane w klastrze AKS do ograniczania akcji kontenera](media/operator-best-practices-container-security/apparmor.png)

Aby wyświetlić AppArmor w działaniu, w poniższym przykładzie tworzony jest profil, który uniemożliwia zapis do plików. Użyj protokołu [SSH][aks-ssh] do węzła AKS, a następnie utwórz plik o nazwie *Odmów-Write. profile* i wklej następującą zawartość:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Profile AppArmor są dodawane za pomocą `apparmor_parser` polecenia. Dodaj profil do AppArmor i określ nazwę profilu utworzonego w poprzednim kroku:

```console
sudo apparmor_parser deny-write.profile
```

Nie są zwracane żadne dane wyjściowe, jeśli profil został prawidłowo przeanalizowany i zastosowany do AppArmor. Nastąpi powrót do wiersza polecenia.

Na komputerze lokalnym Utwórz manifest pod nazwą *AKS-AppArmor. YAML* i wklej poniższą zawartość. Ten manifest definiuje adnotację dla `container.apparmor.security.beta.kubernetes` dodawania odwołuje się do profilu *Odmów-zapisu* utworzonego w poprzednich krokach:

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

Wdróż przykład przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f aks-apparmor.yaml
```

Po wdrożeniu należy użyć Sprawdź, czy *Witaj, AppArmor* pod jako *zablokowane*:

```
$ kubectl get pods

NAME             READY   STATUS    RESTARTS   AGE
aks-ssh          1/1     Running   0          4m2s
hello-apparmor   0/1     Blocked   0          50s
```

Aby uzyskać więcej informacji na temat AppArmor, zobacz [Profile AppArmor w Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Zabezpieczanie obliczeń

Podczas gdy AppArmor działa dla dowolnej aplikacji systemu Linux, [seccomp (*s* uruj *COMP* uting)][seccomp] działa na poziomie procesu. Seccomp jest również modułem zabezpieczeń jądra systemu Linux i jest natywnie obsługiwany przez środowisko uruchomieniowe Docker używane przez węzły AKS. W przypadku seccomp proces wywołuje, że kontenery mogą być wykonywane, są ograniczone. Tworzysz filtry definiujące akcje, które mają być dozwolone lub odrzucane, a następnie użyj adnotacji w manifeście pod YAML, aby skojarzyć z filtrem seccomp. Jest to zgodne z najlepszymi rozwiązaniami dotyczącymi tylko dostępu do kontenera, które są konieczne do uruchomienia, i nie tylko.

Aby wyświetlić seccomp w akcji, Utwórz filtr uniemożliwiający zmianę uprawnień do pliku. Użyj protokołu [SSH][aks-ssh] do węzła AKS, a następnie utwórz filtr seccomp o nazwie */var/lib/kubelet/seccomp/Prevent-chmod* i wklej następującą zawartość:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Na komputerze lokalnym Utwórz manifest pod nazwą *AKS-seccomp. YAML* i wklej poniższą zawartość. Ten manifest definiuje adnotację dla `seccomp.security.alpha.kubernetes.io` i odwołuje się do filtru *zapobiegania chmod* utworzonego w poprzednim kroku:

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

Wdróż przykład przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f ./aks-seccomp.yaml
```

Wyświetl stan polecenia kubectl, korzystając z polecenia [GetBinding][kubectl-get] . Na stronie raportowany jest błąd. `chmod`Polecenie nie jest uruchamiane przez filtr seccomp, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Aby uzyskać więcej informacji na temat dostępnych filtrów, zobacz [Profile zabezpieczeń Seccomp dla platformy Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regularnie Aktualizuj do najnowszej wersji programu Kubernetes

**Wskazówki dotyczące najlepszych** rozwiązań — aby zachować bieżące informacje o nowych funkcjach i poprawkach błędów, należy regularnie uaktualnić wersję Kubernetes w klastrze AKS.

Kubernetes zwalnia nowe funkcje w szybszym tempie niż w przypadku tradycyjnych platform infrastruktury. Aktualizacje Kubernetes obejmują nowe funkcje i poprawki błędów lub zabezpieczeń. Nowe funkcje zwykle przechodzą przez *alfa* , a następnie stan *wersji beta* , zanim staną się *stabilne* i są ogólnie dostępne i zalecane do użycia w środowisku produkcyjnym. Ten cykl wydawniczy powinien zezwalać na aktualizację Kubernetes bez regularnego napotkania istotnych zmian lub dostosowywania wdrożeń i szablonów.

AKS obsługuje trzy drobne wersje programu Kubernetes. Oznacza to, że w przypadku wprowadzenia nowej wersji poprawek mniejszych najstarsza wersja pomocnicza i obsługiwane wersje poprawek zostaną wycofane. Niewielkie aktualizacje Kubernetes są wykonywane okresowo. Upewnij się, że masz proces ładu do sprawdzenia i uaktualnienia w miarę potrzeb, aby nie wyprowadzić pomocy technicznej. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje KUBERNETES AKS][aks-supported-versions].

Aby sprawdzić wersje dostępne dla klastra, użyj polecenia [AZ AKS Get-Upgrades][az-aks-get-upgrades] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Następnie można uaktualnić klaster AKS za pomocą polecenia [AZ AKS upgrade][az-aks-upgrade] . Proces uaktualniania bezpiecznie cordons i opróżnia jeden węzeł w danym momencie, planuje w pozostałych węzłach, a następnie wdraża nowy węzeł z najnowszymi wersjami systemów operacyjnych i Kubernetes.

Zdecydowanie zaleca się przetestowanie nowych wersji pomocniczych w środowisku testowym deweloperskim, dzięki czemu można sprawdzić poprawność działania w dobrej kondycji w przypadku nowej wersji Kubernetes. Kubernetes mogą przestarzałe interfejsy API, takie jak w wersji 1,16, które mogą być używane przez obciążenia. Podczas wprowadzania nowych wersji do środowiska produkcyjnego należy rozważyć użycie [wielu pul węzłów w różnych wersjach](use-multiple-node-pools.md) i uaktualnienie pojedynczych pul pojedynczo w celu stopniowego wycofania aktualizacji w klastrze. W przypadku uruchamiania wielu klastrów należy uaktualnić jeden klaster jednocześnie, aby stopniowo monitorować wpływ lub zmiany.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Aby uzyskać więcej informacji na temat uaktualnień w programie AKS, zobacz [obsługiwane wersje Kubernetes w AKS][aks-supported-versions] i [Uaktualnij klaster AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Przetwarzanie aktualizacji węzłów systemu Linux i ponownych uruchomień przy użyciu kured

**Wskazówki dotyczące najlepszych** rozwiązań — AKS automatycznie pobiera i instaluje poprawki zabezpieczeń w poszczególnych węzłach systemu Linux, ale w razie potrzeby nie uruchamia się automatycznie. Użyj `kured` , aby obejrzeć oczekiwanie na ponowne uruchomienie, a następnie bezpiecznie Cordon i Opróżnij węzeł, aby zezwolić na ponowny rozruch węzła, Zastosuj aktualizacje i możliwie jak najbardziej bezpieczny w odniesieniu do systemu operacyjnego. W przypadku węzłów systemu Windows Server regularnie wykonaj operację uaktualnienia AKS, aby bezpiecznie Cordon i opróżnić i wdrożyć zaktualizowane węzły.

Każdy wieczór, węzły systemu Linux w AKS pobierają poprawki zabezpieczeń dostępne za pośrednictwem ich kanału aktualizacji dystrybucji. To zachowanie jest konfigurowane automatycznie, ponieważ węzły są wdrażane w klastrze AKS. Aby zminimalizować zakłócenia i potencjalny wpływ na uruchamianie obciążeń, węzły nie są automatycznie uruchamiane ponownie, Jeśli poprawka zabezpieczeń lub Aktualizacja jądra tego wymaga.

Projekt typu open source [kured (KUbernetes Boot daemon)][kured] przez Weaveworks czujki dla oczekujących ponownych uruchomień węzłów. Gdy węzeł systemu Linux zastosuje aktualizacje, które wymagają ponownego uruchomienia, węzeł jest bezpiecznie odizolowywane i opróżniany w celu przeniesienia i zaplanowania w innych węzłach w klastrze. Po ponownym uruchomieniu węzła zostanie on dodany z powrotem do klastra, a Kubernetes wznawia na nim planowanie. Aby zminimalizować zakłócenia, może być uruchamiany ponownie tylko jeden węzeł w danym momencie `kured` .

![Proces ponownego uruchamiania węzła AKS przy użyciu kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Jeśli potrzebujesz bardziej precyzyjnej kontroli ziarna podczas ponownego uruchamiania, `kured` można zintegrować z usługą Prometheus, aby zapobiec ponownym uruchomieniu w przypadku wystąpienia innych zdarzeń konserwacji lub problemów z klastrem. Ta integracja minimalizuje dodatkowe komplikacje przez ponowne uruchomienie węzłów podczas aktywnego rozwiązywania problemów z innymi problemami.

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
