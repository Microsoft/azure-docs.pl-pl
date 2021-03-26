---
title: Zarządzanie infrastrukturą hybrydową na dużą skalę za pomocą usługi Azure Arc
description: Dowiedz się, jak efektywnie zarządzać maszynami klientów i klastrami Kubernetes poza platformą Azure.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: da7f016b507f8f876d27f6924f87f078a7806563
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563603"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Zarządzanie infrastrukturą hybrydową na dużą skalę za pomocą usługi Azure Arc

Jako dostawca usług możesz dołączyć wielu dzierżawców klientów do [usługi Azure Lighthouse](../overview.md). Usługa Azure Lighthouse umożliwia dostawcom usług wykonywanie operacji na dużą skalę w wielu dzierżawach Azure Active Directory (Azure AD) jednocześnie, co sprawia, że zadania zarządzania są bardziej wydajne.

[Usługa Azure Arc](../../azure-arc/overview.md) pomaga uprościć złożone i rozproszone środowiska w środowisku lokalnym, w granicach i w chmurze, umożliwiając wdrażanie usług platformy Azure w dowolnym miejscu i rozszerzanie zarządzania platformy Azure do dowolnej infrastruktury.

Dzięki [serwerom z obsługą usługi Azure Arc](../../azure-arc/servers/overview.md)klienci mogą zarządzać wszystkimi maszynami z systemami Windows i Linux hostowanymi poza platformą Azure w sieci firmowej w taki sam sposób, jak w przypadku zarządzania natywnymi maszynami wirtualnymi platformy Azure. Po połączeniu maszyny hybrydowej z platformą Azure będzie ona traktowana jak zasób na platformie Azure. Dostawcy usług mogą następnie zarządzać tymi maszynami spoza platformy Azure wraz z zasobami platformy Azure dla klientów.

[Usługa Azure Arc Kubernetes](../../azure-arc/kubernetes/overview.md) umożliwia klientom dołączanie i konfigurowanie klastrów Kubernetes w ramach platformy Azure lub poza nią. Po dołączeniu klastra Kubernetes do usługi Azure Arc zostanie on wyświetlony w Azure Portal z IDENTYFIKATORem Azure Resource Manager i tożsamością zarządzaną. Klastry są dołączone do standardowych subskrypcji platformy Azure, znajdują się w grupie zasobów i mogą odbierać Tagi tak samo jak każdy inny zasób platformy Azure.

Ten temat zawiera omówienie sposobu, w jaki dostawcy usług mogą korzystać z serwerów z obsługą usługi Azure Arc i usługi Azure Arc Kubernetes w sposób skalowalny do zarządzania środowiskiem hybrydowym klientów, dzięki wglądowi we wszystkie zarządzane dzierżawy klientów.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, te wskazówki dotyczą również [przedsiębiorstw korzystających z usługi Azure Lighthouse do zarządzania wieloma dzierżawcami](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Zarządzanie serwerami hybrydowymi na dużą skalę przy użyciu serwerów z obsługą usługi Azure Arc

Jako dostawca usług możesz zarządzać lokalnymi maszynami z systemem Windows Server lub Linux poza platformą Azure, że klienci zostali połączeni z subskrypcją za pomocą [agenta połączonego z platformą Azure](../../azure-arc/servers/agent-overview.md).

Podczas przeglądania zasobów dla delegowanej subskrypcji w Azure Portal zostaną wyświetlone te połączone maszyny oznaczone za pomocą **usługi Azure Arc**. Tymi połączonymi maszynami można zarządzać przy użyciu konstrukcji platformy Azure, takich jak Azure Policy i tagowanie, tak samo jak w przypadku zarządzania zasobami platformy Azure klienta. Możesz również współpracować między dzierżawcami klientów, aby zarządzać wszystkimi połączonymi komputerami hybrydowymi.

Można na przykład [upewnić się, że ten sam zestaw zasad jest stosowany przez maszyny hybrydowe klientów](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). Możesz również użyć Azure Security Center do monitorowania zgodności we wszystkich środowiskach hybrydowych klientów lub [użyć Azure monitor do zbierania danych bezpośrednio z maszyn hybrydowych](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) w log Analytics obszarze roboczym. [Rozszerzenia maszyn wirtualnych](../../azure-arc/servers/manage-vm-extensions.md) można wdrażać na maszynach wirtualnych z systemem innym niż Azure lub Linux, upraszczając zarządzanie komputerami hybrydowymi klientów.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Zarządzanie hybrydowymi klastrami Kubernetes na dużą skalę przy użyciu usługi Azure Arc Kubernetes

Można zarządzać klastrami Kubernetes, które zostały [połączone z subskrypcją klienta za pomocą usługi Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md), tak jakby były one uruchomione na platformie Azure.

Jeśli klient utworzył [konto jednostki usługi w celu dołączenia klastrów Kubernetes do usługi Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md), możesz uzyskać dostęp do tego konta głównej usługi, aby dołączyć klastry i zarządzać nimi. Można to zrobić przez użytkowników w dzierżawie zarządzającej, którzy uzyskali wbudowaną rolę "Kubernetes klaster — Azure Arc dołączania", gdy subskrypcja zawierająca konto głównej usługi została dołączona [do usługi Azure Lighthouse](onboard-customer.md).

[Konfiguracje](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) i [wykresy Helm](../../azure-arc/kubernetes/use-gitops-with-helm.md) można wdrażać za pomocą GitOps dla podłączonych klastrów.

Możliwe jest również monitorowanie podłączonych klastrów przy użyciu Azure Monitor i [użycie Azure Policy do zastosowania konfiguracji klastra w odpowiedniej skali](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z Jumpstarts i przykładami w [repozytorium usługi Azure Arc](https://github.com/microsoft/azure_arc)w witrynie GitHub.
- Poznaj [obsługiwane scenariusze dla serwerów z obsługą usługi Azure Arc](../../azure-arc/servers/overview.md#supported-scenarios).
- Informacje [na temat dystrybucji Kubernetes obsługiwanych przez usługę Azure Arc](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Dowiedz się, jak [wdrażać zasady w odpowiedniej skali](policy-at-scale.md).
- Dowiedz się, jak [używać dzienników Azure monitor na dużą skalę](monitor-at-scale.md).