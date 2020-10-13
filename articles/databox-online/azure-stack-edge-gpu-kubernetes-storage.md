---
title: Informacje o zarządzaniu magazynem Kubernetes na urządzeniu Azure Stack EDGE Pro | Microsoft Docs
description: Opisuje, w jaki sposób Zarządzanie magazynem Kubernetes odbywa się na urządzeniu Azure Stack brzeg Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: ff2a473ca008e9b283d03ebb05f35122473d778a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899265"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes Zarządzanie magazynem na urządzeniu GPU z systemem Azure Stack Edge

Na urządzeniu Azure Stack EDGE Pro tworzony jest klaster Kubernetes podczas konfigurowania roli obliczeniowej. Po utworzeniu klastra Kubernetes można wdrożyć aplikacje kontenera w klastrze Kubernetes w zasobnikach. Istnieją różne sposoby dostarczania magazynu do zasobników w klastrze Kubernetes. 

W tym artykule opisano metody inicjowania obsługi administracyjnej magazynu w klastrze Kubernetes w ogólności i w kontekście programu Azure Stack Edge na urządzeniu. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Wymagania dotyczące magazynu Kubernetes

Kubernetes są bezstanowe, ale uruchomione aplikacje są zwykle stanowe. Ze względu na to, że zasobniki mogą być krótkotrwałe i ponownie uruchamiane, przełączane w tryb failover lub przenoszone między węzłami Kubernetes, muszą być spełnione następujące wymagania dotyczące magazynu skojarzonego z usługą. 

Magazyn musi:

- Na żywo poza obszarem.
- Być niezależne od cyklu życia.
- Dostępne ze wszystkich węzłów Kubernetes.

Aby zrozumieć, jak magazyn jest zarządzany przez program Kubernetes, musi on zrozumieć dwa zasoby interfejsu API: 

- **PersistentVolume (PV)**: to jest magazyn w klastrze Kubernetes. Magazyn Kubernetes można statycznie zainicjować jako `PersistentVolume` . Można ją również dynamicznie zainicjować jako  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: jest to żądanie dotyczące magazynu przez użytkownika. Obwody PVC wykorzystują zasoby PV. Obwody PVC mogą żądać określonych trybów rozmiaru i dostępu. 

    Ze względu na to, że użytkownicy potrzebują różnorodnych `PersistentVolumes` właściwości dla różnych problemów, Administratorzy klastrów muszą mieć możliwość oferowania różnorodnych różnych `PersistentVolumes` metod, które różnią się od ich rozmiaru i trybu dostępu. W przypadku tych potrzeb potrzebny jest `StorageClass` zasób.

Inicjowanie obsługi magazynu może być statyczne lub dynamiczne. Każdy typ aprowizacji został omówiony w poniższych sekcjach.

## <a name="staticprovisioning"></a>Inicjowanie obsługi statycznej

Administratorzy klastrów Kubernetes mogą statycznie inicjować magazyn. W tym celu mogą korzystać z zaplecza magazynu opartego na systemach plików SMB/NFS lub używać dysków iSCSI, które łączą się lokalnie za pośrednictwem sieci w środowisku lokalnym, a także używają Azure Files lub dysków platformy Azure w chmurze. Ten typ magazynu nie jest obsługiwany domyślnie, a administratorzy klastrów muszą planować tę prowizję i zarządzać nią. 
 
Poniżej znajduje się diagram, który przedstawia sposób korzystania z magazynu z alokacją statyczną w programie Kubernetes: 

![Obsługa statyczna za pośrednictwem PersistentVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

Wystąpią następujące czynności: 

1. **Inicjowanie obsługi administracyjnej magazynu**: administrator klastra udostępnia magazyn. W tym przykładzie administrator klastra tworzy jeden lub więcej udziałów SMB, które automatycznie tworzą trwałe obiekty woluminów w klastrze Kubernetes odpowiadającym tym udziałom. 

1. **Przejmij magazyn**: przesyłasz wdrożenie obwodu PVC, które żąda magazynu. To zgłoszenie do magazynu to PersistentVolumeClaim (PVC). Jeśli rozmiar i tryb dostępu PV są zgodne z obwodem PVC, to obwód PVC jest powiązany z wa. Obwód PVC i WB mapują jeden do jednego.

1. **Instalowanie obwodu PVC do kontenera**: po POwiązaniu obwodu PVC z WB można zainstalować ten obwód PVC na ścieżce w kontenerze. Gdy logika aplikacji w kontenerze odczytuje/zapisuje z/do tej ścieżki, dane są zapisywane w magazynie SMB.
 

## <a name="dynamicprovisioning"></a>Dynamiczna obsługa administracyjna

Poniżej znajduje się diagram, który przedstawia sposób korzystania z magazynu z alokacją statyczną w programie Kubernetes: 

![Dynamiczna obsługa administracyjna za pośrednictwem StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

Wystąpią następujące czynności: 

1. **Definiowanie klasy magazynu**: administrator klastra definiuje klasę magazynu w zależności od środowiska operacyjnego klastra Kubernetes. Administrator klastra wdraża również prowizję, która jest jeszcze inna pod względem lub aplikacją wdrożoną w klastrze Kubernetes. Provisioner ma wszystkie szczegóły dotyczące dynamicznego udostępniania udziałów.  

1. Zażądaj **magazynu**: przesyłana jest aplikacja, która będzie domagać magazynu. Po utworzeniu obwodu PVC z tym odwołaniem do klasy magazynu jest wywoływana provisioner. 

1. **Zapewnianie obsługi administracyjnej magazynu dynamicznie**: program przydzielający dynamicznie tworzy udział skojarzony z lokalnym magazynem dyskowym. Po utworzeniu udziału tworzy on również obiekt PV, który jest automatycznie odpowiadający temu udziałowi.

1. **Zainstaluj obwód PVC do kontenera**: po POwiązaniu obwodu PVC z WB można zainstalować obwód PVC w kontenerze na ścieżce w taki sam sposób jak statyczna obsługa administracyjna i Odczyt lub zapis w udziale.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Inicjowanie obsługi magazynu na Azure Stack EDGE Pro

Na urządzeniu z systemem Azure Stack Edge, alokacja statyczna `PersistentVolumes` jest tworzona przy użyciu funkcji magazynu urządzenia. Gdy udostępniasz udział i **korzystasz z opcji udział z funkcją COMPUTE Edge** , ta akcja powoduje automatyczne utworzenie zasobu PV w klastrze Kubernetes.

![Tworzenie udziału lokalnego w Azure Portal na potrzeby inicjowania obsługi statycznej](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

Aby korzystać z obsługi warstw w chmurze, możesz utworzyć udział w chmurze krawędzi z włączoną opcją Użyj udziału z funkcją obliczeń Edge. Funkcja PV jest ponownie tworzona automatycznie dla tego udziału. Wszystkie dane aplikacji, które można zapisywać w udziale krawędzi, są warstwowe w chmurze. 

![Tworzenie udziału w chmurze w Azure Portal na potrzeby inicjowania obsługi statycznej](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

Można utworzyć udziały SMB i NFS do statycznego udostępniania PVs na urządzeniu Azure Stack EDGE Pro. Po zainicjowaniu obsługi PV zostanie przesłany obwód PVC, aby przejąć ten magazyn. Poniżej znajduje się przykład wdrożenia obwodu PVC `yaml` , które jest oświadczeniem magazynu i korzysta z udostępnianych udziałów.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji stanowej za pośrednictwem statycznej aprowizacji na Azure Stack EDGE Pro za pośrednictwem polecenia kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Azure Stack EDGE Pro ma także wbudowaną `StorageClass` nazwę `ase-node-local` , która używa magazynu danych dołączonego do węzła Kubernetes. `StorageClass`Obsługuje to dynamiczną obsługę administracyjną. Możesz wprowadzić `StorageClass` odwołanie w aplikacjach pod, a funkcja PV zostanie automatycznie utworzona. Aby uzyskać więcej informacji, zobacz [pulpit nawigacyjny Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) , aby wykonać zapytanie `ase-node-local StorageClass` .

![Wbudowana Klasa magazynu na pulpicie nawigacyjnym Kubernetes](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji stanowej za pośrednictwem dynamicznej obsługi administracyjnej na Azure Stack EDGE Pro za pośrednictwem kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Wybierz typ magazynu

W zależności od wdrożonego obciążenia może być konieczne wybranie typu magazynu. 

- Jeśli potrzebujesz `ReadWriteMany` trybu dostępu dla `PersistentVolumes` miejsca, w którym woluminy są zainstalowane jako do odczytu i zapisu przez wiele węzłów, użyj statycznej aprowizacji dla udziałów SMB/NFS.

- Jeśli wdrażane aplikacje mają wymóg zgodności ze standardem POSIX, na przykład aplikacje, takie jak MongoDB, PostgreSQL, MySQL lub Prometheus, użyj wbudowanego StorageClass. Tryby dostępu to `ReadWriteOnce` lub wolumin jest instalowany jako do odczytu i zapisu przez pojedynczy węzeł. 


Aby uzyskać więcej informacji na temat trybów dostępu, zobacz [tryb dostępu do woluminów Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Następne kroki

Aby zrozumieć, jak można statycznie zainicjować obsługę administracyjną `PersistentVolume` , zobacz:

- [Wdróż aplikację stanową za pośrednictwem statycznej aprowizacji w Azure Stack EDGE Pro za pośrednictwem polecenia kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Aby dowiedzieć się, jak można dynamicznie zainicjować obsługę administracyjną `StorageClass` , zobacz:

- [Wdróż aplikację stanową za pośrednictwem dynamicznej obsługi administracyjnej na Azure Stack brzeg Pro za pośrednictwem kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
