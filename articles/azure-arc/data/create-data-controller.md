---
title: Tworzenie kontrolera danych
description: Utwórz kontroler danych usługi Azure Arc w typowym wielowęzłowym klastrze Kubernetes, który już został wdrożony.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 329df78bb5829695b95fcca5b7ed7e1439ced821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688369"
---
# <a name="create-the-azure-arc-data-controller"></a>Utwórz kontroler danych usługi Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Omówienie tworzenia kontrolera danych usługi Azure Arc

Usługi danych z włączoną funkcją Azure Arc można tworzyć na wielu różnych typach klastrów Kubernetes i zarządzanych usługach Kubernetes przy użyciu wielu różnych metod.

Obecnie obsługiwana Lista usług Kubernetes Services i dystrybucji jest następująca:

- Azure Kubernetes Service (AKS)
- Aparat usługi Kubernetes platformy Azure (aparat AKS) na Azure Stack
- Usługa Azure Kubernetes na Azure Stack HCL
- Azure RedHat OpenShift (ARO)
- Platforma kontenera OpenShift (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Aparat usługi Google Cloud Kubernetes (GKE)
- Nadrzędna platforma Kubernetes typu open source, zwykle wdrożona za pomocą metody kubeadm

> [!IMPORTANT]
> * Minimalna obsługiwana wersja programu Kubernetes to v 1.17. Zobacz [znane problemy](./release-notes.md#known-issues) , aby uzyskać dodatkowe informacje. 
> * Minimalna obsługiwana wersja OCP to 4,5.
> * Zapoznaj się z [wymaganiami dotyczącymi łączności](connectivity.md) , aby zrozumieć, jakie połączenia są wymagane między środowiskiem i platformą Azure.
> * Zapoznaj się ze [wskazówkami dotyczącymi konfiguracji magazynu](storage-configuration.md) , aby poznać szczegóły dotyczące konfigurowania magazynu trwałego.
> * W przypadku korzystania z usługi Azure Kubernetes należy mieć co najmniej **Standard_D8s_v3** i korzystać z **dysków w warstwie Premium.** Klaster nie powinien obejmować wielu stref dostępności. 
> * Jeśli używasz innej usługi Kubernetes lub dystrybucji, upewnij się, że masz minimalny rozmiar węzła wynoszący 8 GB pamięci RAM i 4 rdzenie oraz łączną pojemność 32 GB pamięci RAM dostępne we wszystkich węzłach Kubernetes. Można na przykład mieć 1 węzeł w 32 GB pamięci RAM i 4 rdzenie lub 2 węzły z 16GB pamięci RAM i 4 rdzenie.

> [!NOTE]
> Jeśli korzystasz z platformy OpenShift w systemie Red Hat na platformie Azure, zalecamy użycie najnowszej dostępnej wersji.

W zależności od wybranej opcji _wymagane_ są pewne narzędzia, ale zaleca się [zainstalowanie wszystkich narzędzi klienta](./install-client-tools.md) przed rozpoczęciem tworzenia kontrolera danych usługi Azure Arc.

Bez względu na wybraną opcję, podczas procesu tworzenia należy podać następujące informacje:

- **Nazwa kontrolera danych** — opisowa nazwa kontrolera danych — np. "kontroler danych produkcyjnych", "kontroler danych Seattle".
- **Nazwa użytkownika kontrolera danych** — dowolna nazwa użytkownika administratora kontrolera danych.
- **Hasło kontrolera danych** — hasło dla użytkownika administratora kontrolera danych.
- **Nazwa przestrzeni nazw Kubernetes** — nazwa przestrzeni nazw Kubernetes, w której ma zostać utworzony kontroler danych.
- **Tryb** łączności — tryb łączności określa stopień łączności ze środowiska usług danych usługi Azure ARC z włączonym systemem Azure. Wersja zapoznawcza obecnie obsługuje tylko bezpośrednie połączone i połączone tryby.  Aby uzyskać więcej informacji, zobacz [tryb łączności](./connectivity.md). 
- **Identyfikator subskrypcji platformy Azure** — identyfikator GUID subskrypcji platformy Azure, dla którego ma zostać utworzony zasób kontrolera danych na platformie Azure.
- **Nazwa grupy zasobów platformy Azure** — nazwa grupy zasobów, w której ma zostać utworzony zasób kontrolera danych na platformie Azure.
- **Lokalizacja platformy Azure** — lokalizacja platformy Azure, w której będą przechowywane metadane zasobów kontrolera danych na platformie Azure. Aby uzyskać listę dostępnych regionów, zobacz [globalna infrastruktura/produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Następne kroki

Istnieje wiele opcji tworzenia kontrolera danych usługi Azure ARC:

> **Chcesz, aby wypróbować coś?**  
> Szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), aparacie Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.
> 
- [Tworzenie kontrolera danych przy użyciu [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Tworzenie kontrolera danych przy użyciu Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Utwórz kontroler danych z Azure Portal za pośrednictwem notesu Jupyter w programie Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Tworzenie kontrolera danych przy użyciu narzędzi Kubernetes, takich jak polecenia kubectl lub OC](create-data-controller-using-kubernetes-native-tools.md)
- [Tworzenie kontrolera danych przy użyciu usługi Azure Arc szybko Rozpocznij pracę na potrzeby przyspieszonego środowiska wdrożenia testowego](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
