---
title: Omówienie klastra Kubernetes na urządzeniu z systemem Microsoft Azure Stack EDGE Pro | Microsoft Docs
description: Opisuje, w jaki sposób Kubernetes jest implementowany na urządzeniu z systemem Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 72ba07090e6ce67501761d97876aa136f146d61c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437931"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes na urządzeniu GPU z systemem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Kubernetes to popularna platforma typu "open source" służąca do organizowania aplikacji w kontenerze. Ten artykuł zawiera omówienie Kubernetes, a następnie opisuje sposób, w jaki Kubernetes działa na urządzeniu Azure Stack EDGE Pro. 

## <a name="about-kubernetes"></a>Informacje o Kubernetes 

Kubernetes zapewnia łatwą i niezawodną platformę do zarządzania aplikacjami opartymi na kontenerach i związanymi z nimi składnikami sieci i magazynu. Możesz szybko tworzyć, dostarczać i skalować aplikacje z kontenerami za pomocą Kubernetes.

Jako otwarta platforma można używać Kubernetes do kompilowania aplikacji przy użyciu preferowanego języka programowania, bibliotek systemu operacyjnego lub magistrali obsługi komunikatów. Do zaplanowania i wdrożenia wydań Kubernetes można zintegrować z istniejącymi narzędziami ciągłej integracji i ciągłego dostarczania.

Aby uzyskać więcej informacji, zobacz [jak działa Kubernetes](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes na Azure Stack brzeg Pro

Na urządzeniu Azure Stack EDGE Pro można utworzyć klaster Kubernetes, konfigurując obliczenia. Po skonfigurowaniu roli obliczeniowej klaster Kubernetes obejmujący węzły główne i procesy robocze są wdrażane i konfigurowane. Ten klaster jest następnie używany do wdrażania obciążeń za pośrednictwem `kubectl` , IoT Edge lub Azure Arc.

Urządzenie Azure Stack EDGE Pro jest dostępne jako Konfiguracja 1-węzłowa, która stanowi klaster infrastruktury. Klaster Kubernetes jest oddzielony od klastra infrastruktury i wdrażany w oparciu o klaster infrastruktury. Klaster infrastruktury udostępnia magazyn trwały dla urządzenia z Azure Stack EDGE Pro, gdy klaster Kubernetes jest odpowiedzialny wyłącznie za aranżację aplikacji. 

Klaster Kubernetes w tym przypadku ma węzeł główny i węzeł procesu roboczego. Węzły Kubernetes w klastrze są maszynami wirtualnymi, na których działają aplikacje i przepływy pracy w chmurze. 

Główny węzeł Kubernetes jest odpowiedzialny za utrzymywanie żądanego stanu klastra. Węzeł główny kontroluje również węzeł procesu roboczego, który z kolei uruchamia aplikacje kontenerowe. 

Na poniższym diagramie przedstawiono implementację Kubernetes na urządzeniu z 1 węzłem Azure Stack Edge. Urządzenie z 1 węzłem nie ma wysokiej dostępności, a jeśli jeden węzeł ulegnie awarii, urządzenie zostanie wyłączone. Klaster Kubernetes również ulegnie awarii.

![Kubernetes architekturę dla urządzenia z 1 węzłem Azure Stack Edge](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Aby uzyskać więcej informacji na temat architektury klastra Kubernetes, przejdź do [podstawowych pojęć związanych z Kubernetes](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Inicjowanie obsługi woluminu magazynu

Aby zapewnić obsługę obciążeń aplikacji, można zainstalować woluminy magazynu dla danych trwałych w udziałach urządzeń Azure Stack EDGE Pro. Mogą być używane woluminy statyczne i dynamiczne. 

Aby uzyskać więcej informacji, zobacz Opcje inicjowania obsługi magazynu dla aplikacji w [magazynie Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Sieć

Usługa Kubernetes Networking pozwala na skonfigurowanie komunikacji w sieci Kubernetes, w tym między innymi sieciami kontenera-kontenerów, sieciami opartymi na usłudze, sieciami i sieciami internetowymi. Aby uzyskać więcej informacji, zobacz model sieci w programie [Kubernetes Networking dla urządzenia z Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Aktualizacje

Ponieważ nowe wersje Kubernetes staną się dostępne, klaster można uaktualnić przy użyciu standardowych aktualizacji dostępnych dla urządzenia z Azure Stack EDGE Pro. Aby zapoznać się z instrukcjami dotyczącymi sposobu uaktualniania, zobacz [apply Updates for the Azure Stack Edge](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Dostęp, monitorowanie

Klaster Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro umożliwia Kubernetes kontrolę dostępu opartą na rolach (Kubernetes RBAC). Aby uzyskać więcej informacji, zobacz [Kubernetes kontroli dostępu opartej na rolach na urządzeniu GPU Azure Stack Edge](azure-stack-edge-gpu-kubernetes-rbac.md).

Możesz również monitorować kondycję klastra i zasobów za pośrednictwem pulpitu nawigacyjnego Kubernetes. Dzienniki kontenerów są również dostępne. Aby uzyskać więcej informacji, zobacz [Korzystanie z pulpitu nawigacyjnego Kubernetes do monitorowania kondycji klastra Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Azure Monitor jest również dostępna jako dodatek do zbierania danych o kondycji z kontenerów, węzłów i kontrolerów. Aby uzyskać więcej informacji, zobacz [Azure monitor przegląd](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Zarządzanie aplikacjami

Po utworzeniu klastra Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro można zarządzać aplikacjami wdrożonymi w tym klastrze za pomocą dowolnej z następujących metod:

- Dostęp natywny za pośrednictwem `kubectl`
- IoT Edge 
- Azure Arc

Te metody zostały wyjaśnione w poniższych sekcjach.


### <a name="kubernetes-and-kubectl"></a>Kubernetes i polecenia kubectl

Po wdrożeniu klastra Kubernetes można zarządzać aplikacjami wdrożonymi w klastrze lokalnie z komputera klienckiego. Aby korzystać z aplikacji, należy użyć natywnego narzędzia, takiego jak *polecenia kubectl* . 

Aby uzyskać więcej informacji na temat wdrażania klastra Kubernetes, przejdź do [wdrożenia klastra Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Aby uzyskać informacje na temat zarządzania, przejdź do [usługi polecenia kubectl w celu zarządzania klastrem Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes i IoT Edge

Kubernetes można także zintegrować z obciążeniami IoT Edge na urządzeniach z systemem Azure Stack EDGE Pro, gdzie Kubernetes zapewnia skalę, a ekosystem i IoT zapewniają ekosystem skoncentrowany na IoT. Warstwa Kubernetes jest używana jako warstwa infrastruktury do wdrażania obciążeń Azure IoT Edge. Okres istnienia modułu i równoważenie obciążenia sieciowego są zarządzane przez program Kubernetes, podczas gdy platforma aplikacji brzegowych jest zarządzana przez IoT Edge.

Aby uzyskać więcej informacji na temat wdrażania aplikacji w klastrze Kubernetes za pomocą IoT Edge, przejdź do: 

- [Uwidaczniaj bezstanowe aplikacje na urządzeniu Azure Stack EDGE Pro za pośrednictwem IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes i Azure — łuk

Azure Arc to narzędzie do zarządzania hybrydowego, które umożliwi wdrażanie aplikacji w klastrach Kubernetes. Usługa Azure Arc umożliwia także używanie Azure Monitor do kontenerów do wyświetlania i monitorowania klastrów. Aby uzyskać więcej informacji, przejdź do [co to jest Azure-Arc włączone Kubernetes?](../azure-arc/kubernetes/overview.md). Aby uzyskać informacje na temat cen usługi Azure ARC, przejdź do [cennika usługi Azure Arc](https://azure.microsoft.com/services/azure-arc/#pricing).

Począwszy od marca 2021, usługa Azure ARC z włączonym Kubernetes będzie ogólnie dostępna dla użytkowników i standardowych opłat za użycie. Jako cenny klient wersji zapoznawczej usługa Azure Arc Kubernetes będzie dostępna bezpłatnie dla Azure Stack urządzeń brzegowych. Aby skorzystać z oferty wersji zapoznawczej, Utwórz [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

1. W kategorii **Typ problemu** wybierz pozycję **Rozliczenia**.
2. W obszarze **Subskrypcja** wybierz swoją subskrypcję.
3. W obszarze **Usługa** wybierz pozycję **Moje usługi**, a następnie wybierz pozycję **Azure Stack Edge**.
4. W obszarze **zasób** wybierz zasób.
5. W obszarze **Podsumowanie** wpisz opis problemu.
6. W obszarze **typ problemu** wybierz pozycję **nieoczekiwane opłaty**.
7. W obszarze **podtyp problemu** wybierz pozycję **Pomóż mi zrozumieć opłaty za bezpłatną wersję próbną**.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat Kubernetes Storage na [urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-storage.md).
- Poznaj model sieci Kubernetes na [urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-networking.md).
- Wdróż [Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-prep.md) w Azure Portal.
