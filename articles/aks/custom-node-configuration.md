---
title: Dostosowywanie konfiguracji węzłów dla pul Azure Kubernetes Service (AKS) (wersja zapoznawcza)
description: Dowiedz się, jak dostosować konfigurację w węzłach Azure Kubernetes Service (AKS) i pulach węzłów.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c2173118b58ca92d69286fb36014872c19058bd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779979"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Dostosowywanie konfiguracji węzłów dla Azure Kubernetes Service (AKS) (wersja zapoznawcza)

Dostosowanie konfiguracji węzła umożliwia skonfigurowanie lub dostrojenie ustawień systemu operacyjnego lub parametrów kubelet w celu dopasowania ich do potrzeb obciążeń. Podczas tworzenia klastra usługi AKS lub dodawania puli węzłów do klastra można dostosować podzbiór często używanych ustawień systemu operacyjnego i kubelet. Aby skonfigurować ustawienia poza tym podzbiorem, użyj zestawu demonów, aby dostosować wymagane konfiguracje bez utraty obsługi usługi [AKS dla węzłów.](support-policies.md#shared-responsibility)

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Rejestrowanie funkcji w `CustomNodeConfigPreview` wersji zapoznawczej

Aby utworzyć klaster lub pulę węzłów usługi AKS, która może dostosowywać parametry kubelet lub ustawienia systemu operacyjnego, należy włączyć `CustomNodeConfigPreview` flagę funkcji w subskrypcji.

Zarejestruj `CustomNodeConfigPreview` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Wyświetlanie zarejestrowanego stanu może potrwać *kilka minut.* Sprawdź stan rejestracji za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster usługi AKS lub pulę węzłów, która może dostosowywać parametry kubelet lub ustawienia systemu operacyjnego, potrzebne jest najnowsze rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview.* Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add.][az-extension-add] Możesz też zainstalować wszystkie dostępne aktualizacje za pomocą [polecenia az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Korzystanie z niestandardowej konfiguracji węzła

### <a name="kubelet-custom-configuration"></a>Konfiguracja niestandardowa rozwiązania Kubelet

Poniżej wymieniono obsługiwane parametry i akceptowane wartości kubelet.

| Parametr | Dozwolone wartości/interwał | Domyślne | Opis |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | brak, statyczny | brak | Zasady statyczne umożliwiają kontenerom w zasobnikach [Gwarantowane](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) z liczbami całkowitymi żądań procesora dostęp do wyłącznych procesorów CPU w węźle. |
| `cpuCfsQuota` | wartość true, false | true |  Włączanie/wyłączanie wymuszania limitu przydziału cfs procesora CPU dla kontenerów, które określają limity procesora CPU. | 
| `cpuCfsQuotaPeriod` | Interwał w milisekundach (ms) | `100ms` | Ustawia wartość okresu limitu przydziału cfs procesora CPU. | 
| `imageGcHighThreshold` | 0-100 | 85 | Procent użycia dysku, po którym zawsze jest uruchamiane odzyskiwanie pamięci obrazu. Minimalne użycie dysku, które **spowoduje wyzwolenie** wyrzucania elementów bezużytecznych. Aby wyłączyć odzyskiwanie pamięci obrazu, ustaw wartość 100. | 
| `imageGcLowThreshold` | 0–100, nie wyższe niż `imageGcHighThreshold` | 80 | Procent użycia dysku, przed którym nigdy nie jest uruchamiane odzyskiwanie pamięci obrazu. Minimalne użycie dysku, które **może wyzwalać** wyrzucanie elementów bezużytecznych. |
| `topologyManagerPolicy` | none, best-effort, restricted, single-numa-node | brak | Zoptymalizuj wyrównanie węzła NUMA. Więcej [informacji można znaleźć tutaj.](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/) Tylko kubernetes v1.18+. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Brak | Dozwolona lista niebezpiecznych wzorców sysctls lub unsafe sysctl. | 

### <a name="linux-os-custom-configuration"></a>Niestandardowa konfiguracja systemu operacyjnego Linux

Obsługiwane ustawienia systemu operacyjnego i akceptowane wartości są wymienione poniżej.

#### <a name="file-handle-limits"></a>Limity dojścia do plików

Gdy obsługujesz duży ruch, często jest to ruch, który obsługujesz, pochodzi z dużej liczby plików lokalnych. Możesz dostosować poniższe ustawienia jądra i wbudowane limity, aby umożliwić obsługę większej ilości pamięci systemowej.

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | Maksymalna liczba dojść do plików przydzielanych przez jądro systemu Linux przez zwiększenie tej wartości pozwala zwiększyć maksymalną dozwoloną liczbę otwartych plików. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | Maksymalna liczba zegarków plików dozwolonych przez system. Każdy *zegarek* ma około 90 bajtów na jądrze 32-bitowym i około 160 bajtów w jądrze 64-bitowym. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | Aio-nr pokazuje bieżącą liczbę asynchronicznych żądań we/wy dla całego systemu. AIO-max-nr umożliwia zmianę maksymalnej wartości, na która może rosnąć. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | Maksymalna liczba plików, które proces może przydzielić. |


#### <a name="socket-and-network-tuning"></a>Dostrajanie gniazd i sieci

W przypadku węzłów agenta, które powinny obsługiwać bardzo dużą liczbę współbieżnych sesji, można użyć poniższego podzestawu opcji tcp i sieci, które można dostosować dla puli węzłów. 

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | Maksymalna liczba żądań połączeń, które mogą być kolejkowane dla dowolnego gniazda nasłuchiwania. Górny limit wartości parametru listy prac przekazanej do funkcji [listen(2).](http://man7.org/linux/man-pages/man2/listen.2.html) Jeśli argument listy prac jest większy niż , zostanie dyskretnie obcięty `somaxconn` do tego limitu.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | Maksymalna liczba pakietów w kolejce po stronie INPUT, gdy interfejs odbiera pakiety szybciej niż jądro może je przetworzyć. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | Maksymalny rozmiar buforu gniazda odbioru w bajtach. |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | Maksymalny rozmiar buforu gniazda wysyłania w bajtach. | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | Maksymalny rozmiar buforu pomocniczego (bufor pamięci opcji) dozwolony na gniazdo. Pamięć opcji gniazda jest używana w kilku przypadkach do przechowywania dodatkowych struktur związanych z użyciem gniazda. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | Maksymalna liczba żądań połączeń w kolejce, które nadal nie otrzymały potwierdzenia od łączącego się klienta. Jeśli ta liczba zostanie przekroczona, jądro rozpocznie porzucanie żądań. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | Maksymalna liczba `timewait` gniazd, które są przechowywane jednocześnie przez system. W przypadku przekroczenia tej liczby gniazdo czasu oczekiwania jest natychmiast niszczone i wyświetlane jest ostrzeżenie. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Czas, przez który oddzielone połączenie (do którego nie odwołuje się już żadna aplikacja) pozostanie w stanie FIN_WAIT_2, zanim zostanie przerwane na końcu lokalnym. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Jak często protokół TCP wysyła `keepalive` komunikaty, `keepalive` gdy jest włączony. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Ile sond wysyła protokół TCP, dopóki nie `keepalive` zadecyduje, że połączenie zostało przerwane. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Jak często sondy są wysyłane. Pomnożony przez niego czas na zabicia połączenia, które nie `tcp_keepalive_probes` odpowiada, po uruchomionych sondach. | 
| `net.ipv4.tcp_tw_reuse` | 0 lub 1 | 0 | Zezwalaj na `TIME-WAIT` ponowne używanie gniazd dla nowych połączeń, gdy są one bezpieczne z punktu widzenia protokołu. | 
| `net.ipv4.ip_local_port_range` | Pierwsze: 1024–60999 i Ostatnie: 32768–65000] | Pierwsze: 32768 i Ostatnie: 60999 | Zakres portów lokalnych, który jest używany przez ruch TCP i UDP do wybrania portu lokalnego. Składa się z dwóch liczb: pierwsza liczba to pierwszy port lokalny dozwolony dla ruchu TCP i UDP w węźle agenta, a drugi to ostatni numer portu lokalnego. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | Minimalna liczba wpisów, które mogą być w pamięci podręcznej usługi ARP. Odzyskiwanie pamięci nie zostanie wyzwolone, jeśli liczba wpisów jest poniżej tego ustawienia. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | Maksymalna nieu programowa liczba wpisów, które mogą być w pamięci podręcznej ARP. To ustawienie jest prawdopodobnie najważniejsze, ponieważ odzyskiwanie pamięci ARP zostanie wyzwolone około 5 sekund po osiągnięciu tego maksymalnego czasu programowego. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | Hard maksymalna liczba wpisów w pamięci podręcznej ARP. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` to moduł, który śledzi wpisy połączenia dla nat w systemie Linux. Moduł `nf_conntrack` używa tabeli skrótów do rekordu *ustanowionego połączenia* protokołu TCP. `nf_conntrack_max` to maksymalna liczba węzłów w tabeli skrótów, czyli maksymalna liczba połączeń obsługiwanych przez moduł lub rozmiar tabeli `nf_conntrack` śledzenia połączeń. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` to moduł, który śledzi wpisy połączenia dla nat w systemie Linux. Moduł `nf_conntrack` używa tabeli skrótów do rekordu *ustanowionego połączenia* protokołu TCP. `nf_conntrack_buckets` to rozmiar tabeli skrótów. | 

#### <a name="worker-limits"></a>Limity procesów roboczych

Podobnie jak limity deskryptora plików, liczba procesów roboczych lub wątków, które może utworzyć proces, jest ograniczona zarówno przez ustawienie jądra, jak i limity użytkownika. Limit użytkowników w ucieku AKS jest nieograniczony. 

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Procesy mogą rozkręcać wątki robocze. Maksymalna liczba wszystkich wątków, które można utworzyć, jest ustawiana przy użyciu ustawienia jądra `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Pamięć wirtualna

Poniższe ustawienia mogą służyć do dostrajania działania podsystemu pamięci wirtualnej jądra systemu Linux i zanieczyszczonych `writeout` danych na dysku.

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | Ten plik zawiera maksymalną liczbę obszarów mapy pamięci, które może mieć proces. Obszary mapy pamięci są używane jako efekt uboczny wywoływania , bezpośrednio przez , i , a także `malloc` `mmap` podczas ładowania bibliotek `mprotect` `madvise` udostępnionych. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Ta wartość procentowa kontroluje tendencję jądra do odzyskiwania pamięci, która jest używana do buforowania obiektów katalogów i i-w-wędrowych. |
| `vm.swappiness` | 0 - 100 | 60 | Ta kontrolka służy do definiowania, jak agresywne jądro zamieni strony pamięci. Wyższe wartości zwiększają poziom agresywności, a niższe wartości zmniejszają ilość wymiany. Wartość 0 powoduje, że jądro nie inicjuje zamiany, dopóki ilość wolnych stron i stron z kopiami plików nie będzie mniejsza niż znacznik najwyższego poziomu w strefie. | 
| `swapFileSizeMB` | 1 MB — rozmiar dysku [tymczasowego](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Brak | SwapFileSizeMB określa rozmiar w MB pliku wymiany, który zostanie utworzony w węzłach agenta z tej puli węzłów. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages to](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) funkcja jądra systemu Linux, która ma na celu zwiększenie wydajności dzięki efektywniejszemu użyciu sprzętu do mapowania pamięci procesora. Po włączeniu jądro próbuje przydzielić zawsze, gdy jest to możliwe, a dowolny proces systemu Linux otrzyma 2 MB stron, jeśli region jest `hugepages` `mmap` naturalnie wyrównany o rozmiarze 2 MB. W niektórych przypadkach, `hugepages` gdy włączono system, aplikacje mogą przydzielać więcej zasobów pamięci. Aplikacja może mieć duży region, ale tylko 1 bajt, w takim przypadku może zostać przydzielona strona o rozmiarze 2 MB, a nie strona o rozmiarze `mmap` 4 tys. bez powodu. W tym scenariuszu można wyłączyć dostęp dla całego systemu lub tylko w `hugepages` `MADV_HUGEPAGE madvise` regionach. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Ta wartość określa, czy jądro powinno agresywnie korzystać z kompaktowania pamięci, aby było bardziej `hugepages` dostępne. | 

> [!IMPORTANT]
> Aby ułatwić wyszukiwanie i czytelność, ustawienia systemu operacyjnego są wyświetlane w tym dokumencie według ich nazwy, ale należy je dodać do pliku konfiguracji json lub interfejsu API usługi AKS przy użyciu konwencji wielkich liter [camelCase.](/dotnet/standard/design-guidelines/capitalization-conventions)

Utwórz `kubeletconfig.json` plik o następującej zawartości:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Utwórz `linuxosconfig.json` plik o następującej zawartości:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Utwórz nowy klaster określający konfiguracje kubelet i systemu operacyjnego przy użyciu plików JSON utworzonych w poprzednim kroku. 

> [!NOTE]
> Podczas tworzenia klastra można określić konfigurację kubeletu, konfigurację systemu operacyjnego lub obie te konfiguracje. Jeśli określisz konfigurację podczas tworzenia klastra, ta konfiguracja zostanie zastosowana tylko w węzłach w początkowej puli węzłów. Wszystkie ustawienia nieskonfigurowane w pliku JSON zachowają wartość domyślną.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Dodaj nową pulę węzłów określającą parametry kubeletu przy użyciu utworzonego pliku JSON.

> [!NOTE]
> Podczas dodawania puli węzłów do istniejącego klastra można określić konfigurację kubelet, konfigurację systemu operacyjnego lub oba te ustawienia. Jeśli określisz konfigurację podczas dodawania puli węzłów, ta konfiguracja zostanie zastosowana tylko do węzłów w nowej puli węzłów. Wszystkie ustawienia nieskonfigurowane w pliku JSON zachowają wartość domyślną.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak skonfigurować klaster usługi AKS.](cluster-configuration.md)
- Dowiedz [się, jak uaktualnić obrazy węzłów](node-image-upgrade.md) w klastrze.
- Zobacz [Upgrade an Azure Kubernetes Service (AKS) cluster (Uaktualnianie](upgrade-cluster.md) klastra usługi AKS), aby dowiedzieć się, jak uaktualnić klaster do najnowszej wersji rozwiązania Kubernetes.
- Zapoznaj się z listą [często zadawanych pytań dotyczących aks,](faq.md) aby znaleźć odpowiedzi na niektóre typowe pytania dotyczące AKS.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why