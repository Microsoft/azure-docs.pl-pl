---
title: Dostosowywanie konfiguracji węzła dla pul węzłów usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza)
description: Dowiedz się, jak dostosować konfigurację w węzłach klastra usługi Azure Kubernetes Service (AKS) i pulach węzłów.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 7b39242a7d7208b33a070e86088b25e9414ead04
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714633"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Dostosuj konfigurację węzła dla pul węzłów usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza)

Dostosowanie konfiguracji węzła umożliwia skonfigurowanie lub dostosowanie ustawień systemu operacyjnego lub parametrów kubelet, aby odpowiadały potrzebom obciążeń. Podczas tworzenia klastra AKS lub dodawania puli węzłów do klastra można dostosować podzestaw często używanych ustawień systemu operacyjnego i kubelet. Aby skonfigurować ustawienia wykraczające poza ten podzbiór, [Użyj zestawu demonów, aby dostosować potrzebne konfiguracje bez obsługi utracie AKS dla węzłów](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Rejestrowanie `CustomNodeConfigPreview` funkcji w wersji zapoznawczej

Aby utworzyć klaster AKS lub pulę węzłów, która może dostosowywać parametry kubelet lub ustawienia systemu operacyjnego, należy włączyć `CustomNodeConfigPreview` flagę funkcji w subskrypcji.

Zarejestruj `CustomNodeConfigPreview` flagę funkcji za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Wyświetlenie stanu *rejestracji* może potrwać kilka minut. Sprawdź stan rejestracji za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster AKS lub pulę węzłów, która może dostosowywać parametry kubelet lub ustawienia systemu operacyjnego, należy dysponować najnowszym rozszerzeniem interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej AKS* . Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej AKS* , używając polecenie [AZ Extension Add][az-extension-add] . Lub zainstalować wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Użyj konfiguracji węzła niestandardowego

### <a name="kubelet-custom-configuration"></a>Konfiguracja niestandardowa Kubelet

Poniżej wymieniono obsługiwane parametry Kubelet i akceptowane wartości.

| Parametr | Dozwolone wartości/interwał | Domyślne | Opis |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | Brak, statyczne | brak | Zasady statyczne umożliwiają kontenery w [gwarantowanym zasobniku](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) z liczbą całkowitą żądania dostępu do wyłącznego procesora CPU w węźle. |
| `cpuCfsQuota` | wartość true, false | true |  Włącz/Wyłącz wymuszanie przydziałów procesora CPU CFS dla kontenerów, które określają limity procesora CPU. | 
| `cpuCfsQuotaPeriod` | Interwał w milisekundach (MS) | `100ms` | Ustawia wartość okresu przydziału CFS procesora CPU. | 
| `imageGcHighThreshold` | 0-100 | 85 | Procent użycia dysku, po którym jest zawsze uruchamiane odzyskiwanie pamięci obrazu. Minimalne użycie **dysku, które wyzwoli** wyrzucanie elementów bezużytecznych. Aby wyłączyć odzyskiwanie pamięci obrazu, ustaw wartość na 100. | 
| `imageGcLowThreshold` | 0-100, nie więcej niż `imageGcHighThreshold` | 80 | Procent użycia dysku przed tym, że odzyskiwanie pamięci obrazu nigdy nie jest uruchamiane. Minimalne użycie dysku, które **może** wyzwolić wyrzucanie elementów bezużytecznych. |
| `topologyManagerPolicy` | Brak, najlepszy nakład pracy, ograniczony, pojedynczy węzeł NUMA | brak | Zoptymalizuj wyrównanie węzłów NUMA, zobacz [tutaj](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Tylko Kubernetes v 1.18 +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Brak | Lista dozwolonych wzorców niebezpiecznego sysctls lub niebezpiecznych elementów sysctl. | 

### <a name="linux-os-custom-configuration"></a>Konfiguracja niestandardowa systemu operacyjnego Linux

Poniżej wymieniono obsługiwane ustawienia systemu operacyjnego i akceptowane wartości.

#### <a name="file-handle-limits"></a>Limity dojścia plików

W przypadku korzystania z dużej ilości ruchu jest to typowy ruch pochodzący z dużej liczby plików lokalnych. Można dostosować poniższe ustawienia jądra i wbudowane limity, aby umożliwić obsługę większej liczby kosztów w przypadku niektórych pamięci systemowej.

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 – 12000500 | 709620 | Maksymalna liczba dojść do plików, które zostaną przydzielone przez jądro systemu Linux przez zwiększenie tej wartości można zwiększyć maksymalną dozwoloną liczbę otwartych plików. |
| `fs.inotify.max_user_watches` | 781250 – 2097152 | 1048576 | Maksymalna liczba obserwujących plików dozwolona przez system. Każde *czujka* jest około 90 bajtów na jądrze 32-bitowym i w przybliżeniu 160 bajty na jądrze 64-bitowego. | 
| `fs.aio-max-nr` | 65536 – 6553500 | 65536 | AIO-nr przedstawia bieżącą liczbę asynchronicznych żądań we/wy na poziomie systemu. AIO — Max-nr pozwala zmienić wartość maksymalną AIO-nr, aby można było powiększyć do. |
| `fs.nr_open` | 8192 – 20000500 | 1048576 | Maksymalna liczba dojść do plików, które może przydzielić proces. |


#### <a name="socket-and-network-tuning"></a>Dostrajanie gniazda i sieci

W przypadku węzłów agentów, które powinny obsługiwać bardzo dużą liczbę współbieżnych sesji, można użyć podzestawu opcji TCP i sieci poniżej, które można dostosować do puli węzłów. 

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 – 3240000 | 16384 | Maksymalna liczba żądań połączeń, które mogą być umieszczone w kolejce dla danego gniazda nasłuchiwania. Górny limit wartości parametru zaległości przekazaną do funkcji [Listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) . Jeśli argument zaległości jest większy niż `somaxconn` , wówczas jest on w trybie dyskretnym obcinany do tego limitu.
| `net.core.netdev_max_backlog` | 1000 – 3240000 | 1000 | Maksymalna liczba pakietów umieszczonych w kolejce po stronie WEJŚCIOWEj, gdy interfejs odbiera pakiety szybciej niż jądro może je przetworzyć. |
| `net.core.rmem_max` | 212992 – 134217728 | 212992 | Maksymalny rozmiar buforu gniazda odbioru w bajtach. |
| `net.core.wmem_max` | 212992 – 134217728 | 212992 | Maksymalny rozmiar buforu gniazda wysyłania w bajtach. | 
| `net.core.optmem_max` | 20480 – 4194304 | 20480 | Maksymalny rozmiar buforu pomocniczego (bufor pamięci) dozwolony na gniazdo. Pamięć opcji gniazda jest używana w kilku przypadkach w celu przechowywania dodatkowych struktur odnoszących się do użycia gniazda. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 – 3240000 | 16384 | Maksymalna liczba żądań połączeń umieszczonych w kolejce, które nadal nie otrzymały potwierdzenia od klienta nawiązującego połączenie. W przypadku przekroczenia liczby, jądro rozpocznie usuwanie żądań. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 – 1440000 | 32768 | Maksymalna liczba `timewait` gniazd przechowywanych przez system jednocześnie. Jeśli ta liczba zostanie przekroczona, gniazdo oczekiwania czasu jest natychmiast niszczone i zostanie wyświetlone ostrzeżenie. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Czas, przez który oddzielona wartość (nie jest już przywoływana przez żadną aplikację) będzie pozostawać w stanie FIN_WAIT_2 przed przerwaniem na lokalnym końcu. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Częstotliwość, z jaką protokół TCP wysyła `keepalive` komunikaty, gdy `keepalive` jest włączona. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Liczba `keepalive` sond wysyłanych przez protokół TCP do momentu, gdy nie zdecyduje się na zerwanie połączenia. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Częstotliwość, z jaką sondy są wysyłane. Pomnożone przez `tcp_keepalive_probes` ten czas na wyczyszczenie połączenia, które nie odpowiada, po rozpoczęciu sondowania. | 
| `net.ipv4.tcp_tw_reuse` | 0 lub 1 | 0 | Zezwalaj na ponowne używanie `TIME-WAIT` gniazd dla nowych połączeń, gdy jest to bezpieczne z punktu widzenia protokołu. | 
| `net.ipv4.ip_local_port_range` | Najpierw: 1024-60999 i Last: 32768-65000] | Pierwszy: 32768 i ostatni: 60999 | Zakres portów lokalnych używany przez ruch TCP i UDP do wybierania portu lokalnego. Składający się z dwóch liczb: pierwszy numer to pierwszy port lokalny dozwolony dla ruchu TCP i UDP w węźle agenta, a drugi to ostatni numer portu lokalnego. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 – 80000 | 4096 | Minimalna liczba wpisów, które mogą znajdować się w pamięci podręcznej ARP. Wyrzucanie elementów bezużytecznych nie zostanie wyzwolone, jeśli liczba wpisów jest poniżej tego ustawienia. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 – 90000 | 8192 | Maksymalna liczba wpisów, które mogą znajdować się w pamięci podręcznej ARP. To ustawienie jest raczej najważniejsze, ponieważ wyrzucanie elementów bezużytecznych ARP zostanie wyzwolone około 5 sekund po osiągnięciu tej nietrwałej wartości maksymalnej. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 – 100000 | 16384 | Twarda Maksymalna liczba wpisów w pamięci podręcznej ARP. |
| `net.netfilter.nf_conntrack_max` | 131072 – 589824 | 131072 | `nf_conntrack` jest modułem, który śledzi wpisy połączenia dla translatora adresów sieciowych w systemie Linux. `nf_conntrack`Moduł używa tabeli skrótów do rejestrowania *ustalonego rekordu połączenia* protokołu TCP. `nf_conntrack_max` jest maksymalną liczbą węzłów w tabeli skrótów, czyli maksymalną liczbę połączeń obsługiwaną przez `nf_conntrack` moduł lub rozmiar tabeli śledzenia połączeń. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 – 147456 | 65536 | `nf_conntrack` jest modułem, który śledzi wpisy połączenia dla translatora adresów sieciowych w systemie Linux. `nf_conntrack`Moduł używa tabeli skrótów do rejestrowania *ustalonego rekordu połączenia* protokołu TCP. `nf_conntrack_buckets` jest rozmiarem tabeli skrótów. | 

#### <a name="worker-limits"></a>Limity procesów roboczych

Podobnie jak w przypadku limitów deskryptorów plików, liczba procesów roboczych lub wątków, które proces może utworzyć, jest ograniczona przez ustawienie jądra i limity użytkowników. Limit użytkowników w AKS jest nieograniczony. 

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Procesy mogą uruchamiać wątki robocze. Maksymalna liczba wątków, które można utworzyć, jest ustawiona z ustawieniem jądra `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Pamięć wirtualna

Poniższe ustawienia mogą służyć do dostrajania operacji w podsystemie pamięci wirtualnej jądra systemu Linux oraz `writeout` o zanieczyszczonych danych na dysku.

| Ustawienie | Dozwolone wartości/interwał | Domyślne | Opis |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 – 262144 | 65530 | Ten plik zawiera maksymalną liczbę obszarów mapy pamięci, jaką może mieć proces. Obszary mapy pamięci są używane jako efekt uboczny wywoływania `malloc` , bezpośrednio przez `mmap` , `mprotect` , i `madvise` , a także podczas ładowania bibliotek udostępnionych. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Ta wartość procentowa kontroluje tendencję jądra do odzyskiwania pamięci, która jest używana do buforowania obiektów katalogu i inode. |
| `vm.swappiness` | 0 - 100 | 60 | Ten formant służy do definiowania sposobu, w jaki agresywne jądro zamienia strony pamięci. Wyższe wartości spowodują zwiększenie stopnia agresywności, ale niższe wartości zmniejszają liczbę zamian. Wartość 0 instruuje jądro, aby nie inicjować wymiany do momentu, gdy liczba stron wolnych i kopii zapasowych nie jest mniejsza niż w przypadku górnego znacznika w strefie. | 
| `swapFileSizeMB` | 1 MB — rozmiar [dysku tymczasowego](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Brak | SwapFileSizeMB określa rozmiar w MEGABAJTach pliku wymiany, który zostanie utworzony w węzłach agenta z tej puli węzłów. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) jest funkcją jądra systemu Linux, która pozwala zwiększyć wydajność dzięki wydajniejszemu użyciu sprzętowego mapowania pamięci procesora. Po włączeniu jądra próbuje przydzielić, `hugepages` gdy to możliwe, a każdy proces systemu Linux otrzyma strony o rozmiarze 2 MB, jeśli `mmap` region jest w sposób naturalny wyrównany do 2 MB. W niektórych przypadkach `hugepages` , gdy system jest włączony, aplikacje mogą zakończyć przydzielanie większej ilości zasobów pamięci. Aplikacja może `mmap` mieć duży region, ale tylko Touch 1 bajt, w takim przypadku można przydzielyć stronę o rozmiarze 2 MB zamiast strony 4K z niedobrej przyczyny. W tym scenariuszu można wyłączyć `hugepages` cały system lub w taki sposób, aby były one w `MADV_HUGEPAGE madvise` regionach. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Ta wartość określa, czy jądro powinno zwiększyć użycie kompaktowania pamięci, aby zwiększyć `hugepages` dostępność. | 

> [!IMPORTANT]
> Aby ułatwić wyszukiwanie i czytelność, ustawienia systemu operacyjnego są wyświetlane w tym dokumencie według ich nazwy, ale należy je dodać do pliku JSON konfiguracji lub interfejsu API AKS przy użyciu [Konwencji z wielką literą CamelCase](/dotnet/standard/design-guidelines/capitalization-conventions).

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

Utwórz nowy klaster, określając konfiguracje kubelet i OS przy użyciu plików JSON utworzonych w poprzednim kroku. 

> [!NOTE]
> Podczas tworzenia klastra można określić konfigurację kubelet, konfigurację systemu operacyjnego lub oba te elementy. Jeśli określisz konfigurację podczas tworzenia klastra, ta konfiguracja zostanie zastosowana tylko do węzłów w puli węzłów początkowych. Wszystkie ustawienia, które nie zostały skonfigurowane w pliku JSON, będą zachować wartość domyślną.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Dodaj nową pulę węzłów określającą Kubelet parametry przy użyciu utworzonego pliku JSON.

> [!NOTE]
> Po dodaniu puli węzłów do istniejącego klastra można określić konfigurację kubelet, konfigurację systemu operacyjnego lub oba te elementy. Jeśli określisz konfigurację podczas dodawania puli węzłów, ta konfiguracja zostanie zastosowana tylko do węzłów w nowej puli węzłów. Wszystkie ustawienia, które nie zostały skonfigurowane w pliku JSON, będą zachować wartość domyślną.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak skonfigurować klaster AKS](cluster-configuration.md).
- Dowiedz się [, jak uaktualnić obrazy węzłów](node-image-upgrade.md) w klastrze.
- Zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)](upgrade-cluster.md) , aby dowiedzieć się, jak uaktualnić klaster do najnowszej wersji Kubernetes.
- Zapoznaj się z listą [często zadawanych pytań dotyczących AKS](faq.md) , aby znaleźć odpowiedzi na niektóre często zadawane pytania dotyczące AKS.

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
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why