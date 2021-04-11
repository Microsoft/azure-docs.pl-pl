---
title: Pojęcia — Storage w usłudze Azure Kubernetes Services (AKS)
description: Informacje o magazynie w usłudze Azure Kubernetes Service (AKS), w tym woluminy, woluminy trwałe, klasy magazynu i oświadczenia
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105786"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opcje magazynu dla aplikacji w usłudze Azure Kubernetes Service (AKS)

Aplikacje działające w usłudze Azure Kubernetes Service (AKS) mogą wymagać przechowywania i pobierania danych. Niektóre obciążenia aplikacji mogą korzystać z lokalnego, szybkiego magazynu w niepotrzebnych, opróżnionych węzłach, a inne wymagają magazynu, który utrzymuje więcej zwykłych woluminów danych na platformie Azure. 

Może być potrzebne wiele zasobników:
* Współużytkowanie tych samych woluminów danych. 
* Ponownie Dołącz woluminy danych, jeśli na innym węźle zostanie przeplanowane ponowne zaplanowanie. 

Na koniec może zajść potrzeba dodania poufnych danych lub informacji konfiguracyjnych aplikacji do zasobników.

W tym artykule przedstawiono podstawowe koncepcje zapewniające magazyn dla aplikacji w AKS:

- [Woluminy](#volumes)
- [Trwałe woluminy](#persistent-volumes)
- [Klasy magazynu](#storage-classes)
- [Trwałe woluminy — oświadczenia](#persistent-volume-claims)

![Opcje magazynu dla aplikacji w klastrze usługi Azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Woluminy

Kubernetes zazwyczaj traktuje poszczególne zasobniki jako tymczasowe, jednorazowe zasoby. Aplikacje mają różne podejścia do użycia i utrwalania danych. *Wolumin* reprezentuje sposób przechowywania, pobierania i utrwalania danych w różnych magazynach i za pośrednictwem cyklu życia aplikacji.

Tradycyjne woluminy są tworzone jako zasoby Kubernetes, które są obsługiwane przez usługę Azure Storage. Można ręcznie tworzyć woluminy danych do przypisania do zasobników bezpośrednio lub Kubernetes je automatycznie. Woluminy danych mogą korzystać z dysków lub Azure Files platformy Azure.

### <a name="azure-disks"></a>Azure Disks

Użyj *usługi Azure disks* , aby utworzyć zasób Kubernetes *Datadisk* . Dyski mogą używać:
* Usługa Azure Premium Storage, obsługiwana przez dysków SSD o wysokiej wydajności, lub 
* Usługa Azure Standard Storage, obsługiwana za pomocą regularnego HDD. 

> [!TIP]
>W przypadku większości obciążeń produkcyjnych i programistycznych należy użyć usługi Premium Storage. 

Ponieważ dyski platformy Azure są zainstalowane jako *ReadWriteOnce*, są one dostępne tylko dla jednego z nich. W przypadku woluminów magazynu, do których można uzyskać dostęp jednocześnie przez wiele zasobników, użyj Azure Files.

### <a name="azure-files"></a>Azure Files
Użyj *Azure Files* , aby zainstalować udział SMB 3,0 w ramach konta usługi Azure Storage w jednostkach pojemności. Pliki umożliwiają udostępnianie danych między wieloma węzłami i zasobnikami oraz korzystanie z nich:
* Usługa Azure Premium Storage, obsługiwana przez dysków SSD o wysokiej wydajności, lub 
* Usługa Azure Standard Storage obsługiwana za pomocą zwykłego HDD.

### <a name="volume-types"></a>Typy woluminów
Woluminy Kubernetes reprezentują więcej niż tradycyjny dysk do przechowywania i pobierania informacji. Woluminy Kubernetes mogą być również używane jako sposób wstrzykiwania danych do użycia przez kontenery. 

Typy wspólnych woluminów w Kubernetes obejmują:

#### <a name="emptydir"></a>emptyDir

Często używane jako tymczasowe miejsce dla. Wszystkie kontenery w ramach programu mogą uzyskiwać dostęp do danych w woluminie. Dane zapisywane do tego typu woluminu są utrwalane tylko dla cykl życia pod. Po usunięciu pod, wolumin zostanie usunięty. Ten wolumin zazwyczaj używa bazowego magazynu dysku lokalnego, chociaż może również istnieć tylko w pamięci węzła.

#### <a name="secret"></a>wpis tajny

Można użyć woluminów *tajnych* , aby wstrzyknąć dane poufne do zasobników, takich jak hasła. 
1. Utwórz wpis tajny przy użyciu interfejsu API Kubernetes. 
1. Zdefiniuj element lub wdrożenie i Żądaj określonego klucza tajnego. 
    * Wpisy tajne są dostarczane tylko do węzłów z zaplanowanym terminem, które ich wymagają.
    * Wpis tajny jest przechowywany w *tmpfs*, nie jest zapisywany na dysku. 
1. Po usunięciu ostatniego elementu znajdującego się w węźle wymagającym wpisu tajnego, wpis tajny jest usuwany z tmpfs węzła. 
   * Wpisy tajne są przechowywane w danym obszarze nazw i można do nich uzyskiwać dostęp tylko w obrębie tego samego obszaru nazw.

#### <a name="configmap"></a>configMap

Można użyć *configMap* , aby wstrzyknąć właściwości pary klucz-wartość do zasobników, takich jak informacje o konfiguracji aplikacji. Zdefiniuj informacje o konfiguracji aplikacji jako zasób Kubernetes, które są łatwo aktualizowane i stosowane do nowych wystąpień w miarę ich wdrażania. 

Jak przy użyciu klucza tajnego:
1. Utwórz element ConfigMap przy użyciu interfejsu API Kubernetes. 
1. Zażądaj ConfigMap przy definiowaniu lub wdrożeniu. 
   * ConfigMaps są przechowywane w obrębie danego obszaru nazw i można do nich uzyskać dostęp tylko w obrębie tego samego obszaru nazw.

## <a name="persistent-volumes"></a>Trwałe woluminy

Woluminy zdefiniowane i utworzone w ramach cyklu życia w programie istnieją dopiero po usunięciu obszaru pod. W przypadku, gdy na innym hoście w ramach zdarzeń konserwacyjnych, w szczególności w StatefulSets. *Wolumin trwały* (PV) jest zasobem magazynu utworzonym i zarządzanym przez interfejs API Kubernetes, który może istnieć poza okresem istnienia poszczególnych elementów.

Aby zapewnić PersistentVolume, możesz użyć dysków lub plików platformy Azure. Jak wspomniano w sekcji [woluminy](#volumes) , wybór dysków lub plików jest często określany na podstawie potrzeby jednoczesnego dostępu do danych lub warstwy wydajności.

![Woluminy trwałe w klastrze usługi Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

PersistentVolume może być *statycznie* tworzony przez administratora klastra lub tworzony *dynamicznie* przez serwer interfejsu API Kubernetes. W przypadku zaplanowania i żądania aktualnie niedostępnego magazynu program Kubernetes może utworzyć podstawowy magazyn dyskowy lub plików platformy Azure, a następnie dołączyć go do usługi pod. Dynamiczna obsługa administracyjna używa *StorageClass* , aby określić, jakiego typu usługi Azure Storage należy utworzyć.

## <a name="storage-classes"></a>Klasy magazynu

Aby zdefiniować różne warstwy magazynu, takie jak Premium i Standard, można utworzyć *StorageClass*. 

StorageClass również definiuje *reclaimPolicy*. Po usunięciu elementu pod i trwały wolumin nie jest już wymagany, reclaimPolicy kontroluje zachowanie bazowego zasobu usługi Azure Storage. Podstawowy zasób magazynu można usunąć lub zachować do użycia w przyszłości.

W AKS cztery inicjały `StorageClasses` są tworzone dla klastra przy użyciu wtyczek magazynu w drzewie:

| Uprawnienie | Przyczyna |
|---|---|
| `default` | Program używa usługi Azure StandardSSD Storage do tworzenia dysku zarządzanego. Zasady odzyskiwania zapewniają, że podstawowy dysk platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty. |
| `managed-premium` | Używa usługi Azure Premium Storage do utworzenia dysku zarządzanego. Zasady odzyskiwania ponownie zapewniają, że podstawowy dysk platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty. |
| `azurefile` | Za pomocą usługi Azure Standard Storage można utworzyć udział plików platformy Azure. Zasady odzyskiwania zapewniają, że podstawowy udział plików platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty. |
| `azurefile-premium` | Za pomocą usługi Azure Premium Storage można utworzyć udział plików platformy Azure. Zasady odzyskiwania zapewniają, że podstawowy udział plików platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty.|

W przypadku klastrów korzystających z nowych wtyczek usługi Container Storage Interface (CSI) tworzone są następujące dodatkowe elementy `StorageClasses` :

| Uprawnienie | Przyczyna |
|---|---|
| `managed-csi` | Program używa platformy Azure StandardSSD lokalnie nadmiarowego magazynu (LRS) w celu utworzenia dysku zarządzanego. Zasady odzyskiwania zapewniają, że podstawowy dysk platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty. Klasa magazynu konfiguruje również woluminy trwałe do rozwijania, więc wystarczy zmienić wartość w polu trwały zbiorcze żądania o nowy rozmiar. |
| `managed-csi-premium` | Do utworzenia dysku zarządzanego używa usługi Azure Premium Storage lokalnie nadmiarowy (LRS). Zasady odzyskiwania ponownie zapewniają, że podstawowy dysk platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty. Podobnie ta Klasa magazynu pozwala na rozszerzanie woluminów trwałych. |
| `azurefile-csi` | Za pomocą usługi Azure Standard Storage można utworzyć udział plików platformy Azure. Zasady odzyskiwania zapewniają, że podstawowy udział plików platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty. |
| `azurefile-csi-premium` | Za pomocą usługi Azure Premium Storage można utworzyć udział plików platformy Azure. Zasady odzyskiwania zapewniają, że podstawowy udział plików platformy Azure zostanie usunięty, gdy wolumin trwały, który go używał, zostanie usunięty.|

Jeśli nie określisz StorageClass dla woluminu trwałego, zostanie użyta wartość domyślna StorageClass. Upewnij się, że woluminy używają odpowiedniego magazynu potrzebnego podczas żądania woluminów trwałych. 

Można utworzyć StorageClass do dodatkowych potrzeb przy użyciu `kubectl` . Poniższy przykład używa Managed Disks Premium i określa, że podstawowy dysk platformy Azure powinien zostać *zachowany* po usunięciu:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS uzgadnia domyślne klasy magazynu i zastępuje wszelkie zmiany wprowadzone w tych klasach magazynu.

## <a name="persistent-volume-claims"></a>Trwałe woluminy — oświadczenia

PersistentVolumeClaim żąda dysku lub magazynu plików dla konkretnej StorageClass, trybu dostępu i rozmiaru. Serwer interfejsu API Kubernetes umożliwia dynamiczne Inicjowanie obsługi bazowego zasobu usługi Azure Storage, jeśli żaden istniejący zasób nie może spełnić tego żądania na podstawie zdefiniowanego StorageClassu. 

Definicja pod obejmuje instalację woluminu, gdy wolumin został połączony z pod.

![Trwałe oświadczenia woluminów w klastrze usługi Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Po przypisaniu dostępnego zasobu magazynu do magazynu żądania PersistentVolume jest on *powiązany* z PersistentVolumeClaim. Woluminy trwałe są 1:1 mapowane na oświadczenia.

Poniższy przykład manifestu YAML przedstawia trwałe żądanie woluminu, które korzysta z StorageClass *Managed-Premium* i żąda *5Gi* dysku:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Podczas tworzenia definicji pod należy również określić:
* Żądanie trwałego woluminu w celu zażądania odpowiedniego magazynu. 
* *VolumeMount* dla aplikacji, aby odczytywać i zapisywać dane. 

Poniższy przykład manifestu YAML pokazuje, jak poprzednie trwałego żądania woluminu można użyć do zainstalowania woluminu w */mnt/Azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Aby zainstalować wolumin w kontenerze systemu Windows, należy określić literę dysku i ścieżkę. Na przykład:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Następne kroki

W przypadku skojarzonych najlepszych rozwiązań zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w AKS][operator-best-practices-storage].

Aby dowiedzieć się, jak tworzyć dynamiczne i statyczne woluminy korzystające z dysków lub Azure Files platformy Azure, zobacz następujące artykuły:

- [Tworzenie woluminu statycznego przy użyciu dysków platformy Azure][aks-static-disks]
- [Tworzenie woluminu statycznego przy użyciu Azure Files][aks-static-files]
- [Tworzenie woluminu dynamicznego przy użyciu dysków platformy Azure][aks-dynamic-disks]
- [Tworzenie woluminu dynamicznego przy użyciu Azure Files][aks-dynamic-files]

Aby uzyskać więcej informacji na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

- [Kubernetes/AKS klastrów i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes/AKS tożsamość][aks-concepts-identity]
- [Zabezpieczenia Kubernetes/AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes/AKS][aks-concepts-network]
- [Skala Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
