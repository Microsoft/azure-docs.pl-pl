---
title: Rozwiązywanie typowych problemów z usługą Azure Kubernetes
description: Dowiedz się, jak rozwiązywać typowe problemy związane z korzystaniem z usługi Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79368336"
---
# <a name="aks-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Kubernetes Service

W przypadku tworzenia klastrów usługi Azure Kubernetes Service (AKS) lub zarządzania nimi czasami mogą wystąpić problemy. W tym artykule opisano niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Ogólnie rzecz biorąc, gdzie można znaleźć informacje o problemach z debugowaniem Kubernetes?

Wypróbuj [oficjalny przewodnik dotyczący rozwiązywania problemów z klastrami Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Istnieje również [Przewodnik rozwiązywania problemów](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)Opublikowany przez inżyniera firmy Microsoft w celu rozwiązywania problemów z planami, węzłami, klastrami i innymi funkcjami.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Otrzymuję błąd "Przekroczono limit przydziału" podczas tworzenia lub uaktualniania. Co mam zrobić? 

Musisz [zażądać rdzeni](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Co to jest ustawienie maksymalnej liczby elementów w poszczególnych węzłach dla AKS?

Ustawienie Maksymalna liczba sztuk na węzeł domyślnie jest 30, Jeśli klaster AKS jest wdrażany w Azure Portal.
Ustawienie maksymalny rozmiar poszczególnych węzłów domyślnie 110 w przypadku wdrażania klastra AKS w interfejsie wiersza polecenia platformy Azure. (Upewnij się, że używasz najnowszej wersji interfejsu wiersza polecenia platformy Azure). To ustawienie domyślne można zmienić przy użyciu `–-max-pods` flagi w `az aks create` poleceniu.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Otrzymuję błąd insufficientSubnetSize podczas wdrażania klastra AKS przy użyciu zaawansowanej sieci. Co mam zrobić?

Jeśli używana jest usługa Azure CNI (Advanced Network), AKS przydziela adresy IP na podstawie skonfigurowanego węzła "Max-Binding". W oparciu o skonfigurowaną maksymalną liczbę zasobników na węzeł rozmiar podsieci musi być większy niż iloczyn liczby węzłów i maksymalne ustawienie pod na węzeł. Poniższe równanie zawiera opis:

Rozmiar podsieci > liczbę węzłów w klastrze (biorąc pod uwagę przyszłe wymagania dotyczące skalowania) * Maksymalna liczba zasobników na zestaw węzłów.

Aby uzyskać więcej informacji, zobacz [Planowanie adresów IP w klastrze](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mój pod jest zablokowany w trybie CrashLoopBackOff. Co mam zrobić?

Może istnieć różne przyczyny zablokowania w tym trybie. Możesz zajrzeć do:

* Samego siebie, przy użyciu `kubectl describe pod <pod-name>`.
* Dzienniki przy użyciu programu `kubectl logs <pod-name>`.

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [debugowanie aplikacji](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Próbuję włączyć funkcję RBAC w istniejącym klastrze. Jak to zrobić?

Niestety włączenie kontroli dostępu opartej na rolach (RBAC) w istniejących klastrach nie jest obecnie obsługiwane. Należy jawnie utworzyć nowe klastry. W przypadku korzystania z interfejsu wiersza polecenia RBAC jest domyślnie włączone. Jeśli używasz portalu AKS, przycisk przełączania umożliwiający włączenie RBAC jest dostępny w przepływie pracy tworzenia.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Po utworzeniu klastra z włączoną funkcją RBAC przy użyciu interfejsu wiersza polecenia platformy Azure z wartościami domyślnymi lub Azure Portal, a teraz widzimy wiele ostrzeżeń na pulpicie nawigacyjnym Kubernetes. Pulpit nawigacyjny służący do pracy bez żadnych ostrzeżeń. Co mam zrobić?

Przyczyną ostrzeżeń na pulpicie nawigacyjnym jest to, że klaster jest teraz włączony przy użyciu RBAC i dostęp do niego jest domyślnie wyłączony. Ogólnie rzecz biorąc, to podejście jest dobrym rozwiązaniem, ponieważ domyślne narażenie pulpitu nawigacyjnego na wszystkich użytkowników klastra może prowadzić do zagrożeń bezpieczeństwa. Jeśli nadal chcesz włączyć pulpit nawigacyjny, postępuj zgodnie z instrukcjami w [tym wpisie w blogu](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nie mogę nawiązać połączenia z pulpitem nawigacyjnym. Co mam zrobić?

Najprostszym sposobem, aby uzyskać dostęp do usługi poza klastrem, `kubectl proxy`jest uruchomienie, które serwery proxy żądania wysyłane do portu localhost 8001 do serwera interfejsu API Kubernetes. Z tego miejsca serwer interfejsu API może być serwerem proxy usługi: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Jeśli nie widzisz pulpitu nawigacyjnego Kubernetes, sprawdź, `kube-proxy` czy pod `kube-system` przestrzeni nazw jest uruchomiony program. Jeśli nie jest w stanie uruchomionym, Usuń element pod, a zostanie uruchomiony ponownie.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nie mogę pobrać dzienników przy użyciu dzienników polecenia kubectl lub nie mogę nawiązać połączenia z serwerem interfejsu API. Otrzymuję komunikat "błąd z serwera: błąd podczas wybierania numeru zaplecza: wybierz TCP...". Co mam zrobić?

Upewnij się, że domyślna grupa zabezpieczeń sieci nie jest modyfikowana i że dla połączenia z serwerem interfejsu API jest otwarty zarówno port 22, jak i 9000. Sprawdź, czy `tunnelfront` w przestrzeni nazw *polecenia systemu* przy użyciu `kubectl get pods --namespace kube-system` polecenia jest uruchomiona. Jeśli tak nie jest, Wymuś usunięcie elementu pod i zostanie on ponownie uruchomiony.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Próbuję uaktualnić lub skalować i uzyskać "komunikat: zmiana właściwości" elementu imagereference "jest niedozwolona. Jak mogę rozwiązać ten problem?

Ten błąd może być spowodowany modyfikacją tagów w węzłach agenta wewnątrz klastra AKS. Modyfikowanie i usuwanie tagów oraz innych właściwości zasobów w grupie zasobów MC_ * może prowadzić do nieoczekiwanych wyników. Modyfikacja zasobów w grupie MC_ * w klastrze AKS powoduje przerwanie celu poziomu usługi (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Otrzymuję błędy, które są w stanie awarii mojego klastra, a uaktualnienie lub skalowanie nie będzie działało, dopóki nie zostanie naprawione

*Ta pomoc w rozwiązywaniu problemów jest skierowana zhttps://aka.ms/aks-cluster-failed*

Ten błąd występuje, gdy klastry wchodzą w stan niepowodzenia z wielu powodów. Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać stan awarii klastra przed ponowną próbą wykonania poprzedniej operacji zakończonej niepowodzeniem:

1. Dopóki klaster nie jest w `failed` stanie, `upgrade` a `scale` operacje nie powiodą się. Typowe problemy główne i rozwiązania obejmują:
    * Skalowanie z **niewystarczającym limitem przydziału obliczeń (CRP)**. Aby rozwiązać ten problem, należy najpierw skalować klaster z powrotem do stanu stabilnego celu w ramach limitu przydziału. Następnie wykonaj następujące [kroki, aby zażądać zwiększenia przydziału obliczeń](../azure-portal/supportability/resource-manager-core-quotas-request.md) przed ponowną próbą skalowania w górę poza początkowymi limitami przydziału.
    * Skalowanie klastra przy użyciu zaawansowanej sieci i **niewystarczającej liczby zasobów podsieci (sieci)**. Aby rozwiązać ten problem, należy najpierw skalować klaster z powrotem do stanu stabilnego celu w ramach limitu przydziału. Następnie wykonaj [następujące kroki, aby zażądać zwiększenia przydziału zasobów](../azure-resource-manager/templates/error-resource-quota.md#solution) przed ponowną próbą skalowania w górę poza początkowymi limitami przydziału.
2. Po usunięciu podstawowej przyczyny niepowodzenia uaktualnienia klaster powinien działać w stanie sukces. Po zweryfikowaniu stanu, ponów próbę wykonania oryginalnej operacji.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Otrzymuję błędy podczas próby uaktualnienia lub skalowania stanu, w którym mój klaster jest aktualnie uaktualniany lub nie przeprowadzono uaktualnienia

*Ta pomoc w rozwiązywaniu problemów jest skierowana zhttps://aka.ms/aks-pending-upgrade*

Operacje uaktualniania i skalowania w klastrze z pojedynczą pulą węzłów lub klastra z [wieloma pulami węzłów](use-multiple-node-pools.md) wzajemnie się wykluczają. Klaster ani Pula węzłów nie mogą być jednocześnie uaktualniane i skalowane. W zamian każdy typ operacji musi zakończyć się w odniesieniu do zasobu docelowego przed następnym żądaniem tego samego zasobu. W związku z tym operacje są ograniczone w przypadku wystąpienia lub próby aktywnego uaktualnienia lub operacji skalowania, a następnie niepowodzenie. 

Aby ułatwić zdiagnozowanie uruchomienia `az aks show -g myResourceGroup -n myAKSCluster -o table` problemu w celu pobrania szczegółowego stanu w klastrze. W oparciu o wynik:

* Jeśli klaster jest aktywnie uaktualniany, poczekaj na zakończenie operacji. Jeśli zakończyło się pomyślnie, ponów próbę wykonania poprzedniej operacji zakończonej niepowodzeniem.
* Jeśli uaktualnienie nie powiodło się, wykonaj kroki opisane w poprzedniej sekcji.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Czy mogę przenieść klaster na inną subskrypcję lub moją subskrypcję z moim klastrem do nowej dzierżawy?

Jeśli klaster AKS został przeniesiony do innej subskrypcji lub klastra będącego właścicielem subskrypcji do nowej dzierżawy, klaster utraci funkcjonalność ze względu na utratę przypisań ról i uprawnień podmiotów usługi. Usługa **AKS nie obsługuje przesuwania klastrów między subskrypcjami ani dzierżawcami** z powodu tego ograniczenia.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Otrzymuję błędy podczas próby użycia funkcji, które wymagają zestawów skalowania maszyn wirtualnych

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-vmss-enablement*

Mogą pojawić się błędy wskazujące, że klaster AKS nie znajduje się w zestawie skalowania maszyn wirtualnych, na przykład w poniższym przykładzie:

**Nieznanej obiektu agentpool "nieznanej obiektu agentpool" ustawił automatyczne skalowanie jako włączone, ale nie znajduje się na Virtual Machine Scale Sets**

Aby korzystać z funkcji, takich jak automatyczne skalowanie klastra lub pule wielu węzłów, należy utworzyć klastry AKS korzystające z zestawów skalowania maszyn wirtualnych. Błędy są zwracane, jeśli spróbujesz użyć funkcji, które są zależne od zestawów skalowania maszyn wirtualnych, oraz dla klastra AKS z regularnym zestawem skalowania maszyn wirtualnych.

Postępuj zgodnie z instrukcjami *przed rozpoczęciem* w odpowiednim dokumencie, aby poprawnie utworzyć klaster AKS:

* [Korzystanie z automatycznego skalowania klastra](cluster-autoscaler.md)
* [Tworzenie i używanie pul wielu węzłów](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jakie ograniczenia nazewnictwa są wymuszane dla zasobów AKS i parametrów?

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-naming-rules*

Ograniczenia nazewnictwa są implementowane przez platformę Azure i AKS. Jeśli nazwa zasobu lub parametr przerywa jedno z tych ograniczeń, zwracany jest błąd, który prosi o podanie innych danych wejściowych. Stosuje się następujące typowe wskazówki dotyczące nazewnictwa:

* Nazwy klastrów muszą składać się z 1-63 znaków. Jedyne dozwolone znaki to litery, cyfry, łączniki i podkreślenia. Pierwszy i ostatni znak musi być literą lub cyfrą.
* Nazwa grupy zasobów AKS *MC_* łączy nazwę grupy zasobów i nazwę zasobu. Wygenerowana automatycznie składnia nie `MC_resourceGroupName_resourceName_AzureRegion` może być większa niż 80 znaków. W razie konieczności Zmniejsz długość nazwy grupy zasobów lub nazwę klastra AKS.
* *DnsPrefix* musi zaczynać i kończyć się wartościami alfanumerycznymi i muszą zawierać od 1-54 znaków. Prawidłowe znaki to wartości alfanumeryczne i łączniki (-). *DnsPrefix* nie może zawierać znaków specjalnych, takich jak kropka (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Otrzymuję błędy podczas próby utworzenia, zaktualizowania, skalowania, usunięcia lub uaktualnienia klastra, ta operacja nie jest dozwolona, ponieważ inna operacja jest w toku.

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-pending-operation*

Operacje klastra są ograniczone, gdy Poprzednia operacja jest nadal w toku. Aby pobrać szczegółowy stan klastra, użyj `az aks show -g myResourceGroup -n myAKSCluster -o table` polecenia. W razie konieczności Użyj własnej grupy zasobów i nazwy klastra AKS.

Na podstawie danych wyjściowych stanu klastra:

* Jeśli klaster jest w stanie aprowizacji *innym niż* *powodzenie lub nieudany*, poczekaj na zakończenie operacji (*uaktualnianie/aktualizowanie/tworzenie/skalowanie/usuwanie/Migrowanie*). Po zakończeniu poprzedniej operacji ponów próbę wykonania ostatniej operacji klastra.

* Jeśli uaktualnienie nie powiodło się, wykonaj kroki opisane w temacie jak pojawiają się [błędy, w których klaster jest w stanie niepowodzenia, a uaktualnienie lub skalowanie nie będzie działało do momentu jego naprawienia](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Otrzymuję błędy, których nazwa główna usługi nie została znaleziona podczas próby utworzenia nowego klastra bez przechodzenia w istniejącym klastrze.

Podczas tworzenia klastra AKS wymaga jednostki usługi do tworzenia zasobów w Twoim imieniu. Usługa AKS oferuje możliwość utworzenia nowego elementu podczas tworzenia klastra, ale wymaga Azure Active Directory w pełni propagowanie nowej jednostki usługi w odpowiednim czasie w celu pomyślnego utworzenia klastra. Gdy Propagacja trwa zbyt długo, klaster nie będzie mógł wykonać walidacji, ponieważ nie może znaleźć dostępnej jednostki usługi. 

Wykonaj następujące obejścia tego problemu:
1. Użyj istniejącej jednostki usługi, która została już rozpropagowana w regionach i istnieje do przekazania do AKS podczas tworzenia klastra.
2. W przypadku korzystania ze skryptów automatyzacji należy dodać opóźnienia czasu między utworzeniem jednostki usługi i tworzeniem klastra AKS.
3. Jeśli używasz Azure Portal, Wróć do ustawień klastra podczas tworzenia i ponów próbę wykonania strony walidacji po kilku minutach.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Otrzymuję błędy po ograniczeniu ruchu wychodzącego

W przypadku ograniczania ruchu wychodzącego z klastra AKS są [wymagane i opcjonalne zalecane](limit-egress-traffic.md) porty wyjściowe/reguły sieci oraz nazwy FQDN/aplikacji dla AKS. Jeśli ustawienia są w konflikcie z dowolną z tych reguł, uruchomienie niektórych `kubectl` poleceń może nie być możliwe. Podczas tworzenia klastra AKS mogą pojawić się także błędy.

Sprawdź, czy ustawienia nie powodują konfliktu z żadnym z wymaganych lub opcjonalnymi zalecanymi portami wychodzącymi/regułami sieciowymi oraz nazwą FQDN/reguł aplikacji.

## <a name="azure-storage-and-aks-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Storage i AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Jakie są zalecane stabilne wersje programu Kubernetes for Azure Disk? 

| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.9 lub nowszy |
| 1.13 | 1.13.6 lub nowszy |
| 1,14 | 1.14.2 lub nowszy |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Jakie wersje programu Kubernetes mają obsługiwać dysk platformy Azure w chmurze suwerennej?

| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.0 lub nowszy |
| 1.13 | 1.13.0 lub nowszy |
| 1,14 | 1.14.0 lub nowszy |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach dla dysku platformy Azure nie powiodło się: analiza "/dev/Disk/Azure/scsi1/lun1": nieprawidłowa składnia

W programie Kubernetes w wersji 1,10, MountVolume. WaitForAttach może zakończyć się niepowodzeniem przy ponownej instalacji dysku platformy Azure.

W systemie Linux może zostać wyświetlony nieprawidłowy błąd formatu DevicePath. Przykład:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

W systemie Windows może zostać wyświetlony nieprawidłowy błąd numeru DevicePath (LUN). Przykład:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
| -- | :--: |
| 1.10 | 1.10.2 lub nowszy |
| 1,11 | 1.11.0 lub nowszy |
| 1,12 i nowsze | Nie dotyczy |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Niepowodzenie podczas ustawiania identyfikatorów UID i GID w mountOptions dla dysku platformy Azure

Dysk Azure domyślnie używa systemu plików ext4, XFS i mountOptions, takiego jak UID = x, GID = x, nie można ustawić w czasie instalacji. Na przykład jeśli podjęto próbę ustawienia mountOptions UID = 999, GID = 999, zobaczysz błąd, jak:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Problem można rozwiązać, wykonując jedną z następujących czynności:

* [Skonfiguruj kontekst zabezpieczeń pod kątem](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) ustawienia UID w runAsUser i GID w fsGroup. Na przykład następujące ustawienie ustawi polecenie Uruchom jako root, udostępnij je dla dowolnego pliku:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Ponieważ GID i UID są domyślnie instalowane jako root lub 0. Jeśli gid lub UID są ustawione jako spoza katalogu głównego, na przykład 1000, Kubernetes będzie używać `chown` do zmiany wszystkich katalogów i plików znajdujących się na tym dysku. Ta operacja może być czasochłonna i może spowodować, że instalacja dysku będzie bardzo niska.

* Użyj `chown` w initContainers, aby ustawić GID i UID. Przykład:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Wystąpił błąd podczas usuwania usługi Azure Disk PersistentVolumeClaim używanej przez element pod

Jeśli spróbujesz usunąć PersistentVolumeClaim dysku platformy Azure, który jest używany przez element pod, może zostać wyświetlony błąd. Przykład:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

W programie Kubernetes w wersji 1,10 i nowszych istnieje funkcja ochrony PersistentVolumeClaim włączona domyślnie, aby uniknąć tego błędu. Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, możesz wyeliminować ten problem, usuwając go przy użyciu PersistentVolumeClaim przed usunięciem PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Błąd "nie można odnaleźć jednostki LUN dla dysku" podczas dołączania dysku do węzła

Po dołączeniu dysku do węzła może zostać wyświetlony następujący błąd:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
| -- | :--: |
| 1.10 | 1.10.10 lub nowszy |
| 1,11 | 1.11.5 lub nowszy |
| 1.12 | 1.12.3 lub nowszy |
| 1.13 | 1.13.0 lub nowszy |
| 1,14 i nowsze | Nie dotyczy |

Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, możesz wyeliminować problem, czekając kilka minut i ponawiając próbę.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Awarie dołączania/odłączania dysku platformy Azure, problemy z instalowaniem lub błędy we/wy podczas wielu operacji dołączania/odłączania

Począwszy od Kubernetes w wersji 1.9.2, w przypadku równoczesnego uruchamiania wielu operacji dołączania/odłączania, mogą pojawić się następujące problemy z dyskiem z powodu zanieczyszczonej pamięci podręcznej maszyn wirtualnych:

* Awarie dołączania/odłączania dysku
* Błędy we/wy dysku
* Nieoczekiwany odłączenie dysku od maszyny wirtualnej
* Maszyna wirtualna jest uruchomiona w stanie niepowodzenia z powodu dołączenia nieistniejącego dysku

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
| -- | :--: |
| 1.10 | 1.10.12 lub nowszy |
| 1,11 | 1.11.6 lub nowszy |
| 1.12 | 1.12.4 lub nowszy |
| 1.13 | 1.13.0 lub nowszy |
| 1,14 i nowsze | Nie dotyczy |

Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, możesz wyeliminować problem, wykonując następujące czynności:

* Jeśli dysk oczekuje na odłączenie przez długi czas, spróbuj odłączyć dysk ręcznie

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Dysk platformy Azure oczekuje na odłączenie w nieskończoność

W niektórych przypadkach, jeśli operacja odłączenia dysku platformy Azure zakończy się niepowodzeniem przy pierwszej próbie, nie zostanie ponowiona operacja odłączenia i pozostanie ona dołączona do oryginalnej maszyny wirtualnej węzła. Ten błąd może wystąpić podczas przesuwania dysku z jednego węzła do drugiego. Przykład:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
| -- | :--: |
| 1,11 | 1.11.9 lub nowszy |
| 1.12 | 1.12.7 lub nowszy |
| 1.13 | 1.13.4 lub nowszy |
| 1,14 i nowsze | Nie dotyczy |

Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, możesz wyeliminować problem, ręcznie odłączając dysk.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Błąd odłączania dysku platformy Azure, który prowadzi do potencjalnego problemu z sytuacją wyścigu i nieprawidłowa lista dysków danych

Gdy nie można odłączyć dysku z platformą Azure, zostanie ponowiona próba ponowienia przez sześć razy, aby odłączyć dysk przy użyciu wycofywania wykładniczego. Będzie również utrzymywać blokadę na poziomie węzła na liście dysków danych przez około 3 minuty. Jeśli lista dysków jest aktualizowana ręcznie w tym okresie, na przykład w przypadku operacji dołączania lub odłączania ręcznego, spowoduje to, że lista dysków przechowywana przez blokadę na poziomie węzła będzie przestarzała i spowoduje to niestabilność na maszynie wirtualnej węzła.

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
| -- | :--: |
| 1.12 | 1.12.9 lub nowszy |
| 1.13 | 1.13.6 lub nowszy |
| 1,14 | 1.14.2 lub nowszy |
| 1,15 i nowsze | Nie dotyczy |

Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, a maszyna wirtualna węzła ma przestarzałą listę dysków, możesz rozwiązać ten problem, odłączając wszystkie nieistniejące dyski z maszyny wirtualnej jako pojedynczą operację zbiorczą. **Pojedyncze odłączenie nieistniejących dysków może zakończyć się niepowodzeniem.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Duża liczba dysków platformy Azure powoduje spowolnienie dołączania/odłączania

Gdy liczba dysków platformy Azure dołączonych do maszyny wirtualnej węzła jest większa niż 10, operacje dołączania i odłączania mogą być wolne. Ten problem jest znany i nie ma w tej chwili żadnych rozwiązań.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Niepowodzenie odłączenia dysku platformy Azure do potencjalnej maszyny wirtualnej węzła w stanie Niepowodzenie

W niektórych przypadkach, odłączenie dysku platformy Azure może zakończyć się częściowo niepowodzeniem i pozostawić maszynę wirtualną w stanie Niepowodzenie.

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
| -- | :--: |
| 1.12 | 1.12.10 lub nowszy |
| 1.13 | 1.13.8 lub nowszy |
| 1,14 | 1.14.4 lub nowszy |
| 1,15 i nowsze | Nie dotyczy |

Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, a maszyna wirtualna w węźle jest w stanie awarii, możesz rozwiązać problem, ręcznie aktualizując stan maszyny wirtualnej przy użyciu jednego z poniższych elementów:

* W przypadku klastra opartego na zestawie dostępności:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* W przypadku klastra opartego na VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files i rozwiązywanie problemów z AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Jakie są zalecane stabilne wersje programu Kubernetes for Azure Files?
 
| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.6 lub nowszy |
| 1.13 | 1.13.4 lub nowszy |
| 1,14 | 1.14.0 lub nowszy |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Jakie wersje Kubernetes mają Azure Files być obsługiwane w chmurze suwerennej?

| Wersja Kubernetes | Zalecana wersja |
| -- | :--: |
| 1.12 | 1.12.0 lub nowszy |
| 1.13 | 1.13.0 lub nowszy |
| 1,14 | 1.14.0 lub nowszy |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Jakie są domyślne mountOptions w przypadku korzystania z Azure Files?

Zalecane ustawienia:

| Wersja Kubernetes | wartość parametru FileMode i dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 i nowsze | 0777 |

W przypadku używania klastra z Kubernetes w wersji 1.8.5 lub nowszej i dynamicznego tworzenia woluminu trwałego za pomocą klasy magazynu opcje instalacji można określić w obiekcie klasy magazynu. W poniższym przykładzie są ustawiane *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Niektóre dodatkowe użyteczne ustawienia *mountOptions* :

* *mfsymlinks* Azure Files instalacji (CIFS) obsługuje linki symboliczne
* *nobrl* uniemożliwi wysyłanie żądań blokady zakresu bajtów do serwera. To ustawienie jest niezbędne w przypadku niektórych aplikacji, które są przerywane przez obowiązkowe zablokowanie zakresu bajtów w stylu CIFS. Większość serwerów CIFS nie obsługuje jeszcze żądań zablokowania zakresu bajtów doradczych. Jeśli nie korzystasz z *nobrl*, aplikacje, które są przerywane przez obowiązkowe blokowanie zakresu bajtów w stylu CIFS mogą powodować komunikaty o błędach podobne do:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Błąd "nie można zmienić uprawnień" podczas korzystania z Azure Files

Po uruchomieniu programu PostgreSQL na wtyczce Azure Files może zostać wyświetlony komunikat o błędzie podobny do:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Ten błąd jest spowodowany przez wtyczkę Azure Files przy użyciu protokołu CIFS/SMB. W przypadku korzystania z protokołu CIFS/SMB nie można zmienić uprawnień plików i katalogów po zainstalowaniu.

Aby rozwiązać ten problem, użyj *ścieżki podrzędnej* razem z wtyczką dysku platformy Azure. 

> [!NOTE] 
> W przypadku typu dysku ext3/4 istnieje utracony i znaleziony katalog po sformatowaniu dysku.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files ma duże opóźnienie w porównaniu do dysku platformy Azure w przypadku obsługi wielu małych plików

W niektórych przypadkach, takich jak obsługa wielu małych plików, podczas porównywania z dyskiem platformy Azure mogą wystąpić duże Azure Files opóźnienia.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Błąd podczas włączania ustawienia "Zezwalaj na dostęp z wybranej sieci" na koncie magazynu

Jeśli włączysz opcję *Zezwalaj na dostęp z wybranej sieci* na koncie magazynu, które jest używane do inicjowania obsługi dynamicznej w programie AKS, zostanie wyświetlony komunikat o błędzie, gdy AKS utworzy udział plików:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Ten błąd jest spowodowany tym, że Kubernetes *persistentvolume — kontroler* nie znajduje się w sieci wybranej, gdy ustawienie *Zezwalaj na dostęp z wybranej sieci*.

Problem można rozwiązać, korzystając [z inicjowania obsługi statycznej z Azure Files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Nie można ponownie zainstalować Azure Files w systemie Windows pod

Jeśli system Windows pod Azure Files instalacji zostanie usunięty, a następnie zaplanowane do ponownego utworzenia w tym samym węźle, instalacja zakończy się niepowodzeniem. Ten błąd wynika z faktu `New-SmbGlobalMapping` , że polecenie kończy się niepowodzeniem, ponieważ instalacja Azure Files jest już zainstalowana w węźle.

Na przykład może zostać wyświetlony komunikat o błędzie podobny do:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
| -- | :--: |
| 1.12 | 1.12.6 lub nowszy |
| 1.13 | 1.13.4 lub nowszy |
| 1,14 i nowsze | Nie dotyczy |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Azure Files instalacji nie powiodła się z powodu zmiany klucza konta magazynu

Jeśli klucz konta magazynu został zmieniony, mogą pojawić się błędy instalacji Azure Files.

Problem można rozwiązać, ręcznie aktualizując pole *azurestorageaccountkey* w kluczu tajnym systemu Azure za pomocą klucza konta magazynu zakodowanego w formacie base64.

Aby zakodować klucz konta magazynu w formacie Base64, można `base64`użyć programu. Przykład:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Aby zaktualizować plik tajny platformy Azure, `kubectl edit secret`Użyj programu. Przykład:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Po kilku minutach węzeł agenta ponowi próbę instalacji pliku platformy Azure przy użyciu zaktualizowanego klucza magazynu.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Skalowanie automatycznego skalowania klastra nie powiodło się z powodu błędu nie można naprawić rozmiarów grup węzłów

Jeśli klaster automatycznego skalowania nie jest skalowany w górę i w dół, a w [dziennikach automatycznego skalowania klastra][view-master-logs]zobaczysz błąd podobny do poniższego.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Ten błąd występuje ze względu na sytuację wyścigu w przypadku nadrzędnego skalowania klastra, w którym program automatyczne skalowanie klastra zakończy się inną wartością niż ta, która znajduje się w klastrze. Aby uzyskać dostęp do tego stanu, wystarczy wyłączyć i ponownie włączyć [Automatyczne skalowanie klastra][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Wolne miejsce na dysku, GetAzureDiskLun trwa 10 do 15 minut i występuje błąd

W wersji Kubernetes **starszej niż 1.15.0** może zostać wyświetlony błąd, taki jak **błąd WaitForAttach nie można znaleźć jednostki LUN dla dysku**.  Obejście tego problemu ma potrwać około 15 minut i ponowić próbę.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
