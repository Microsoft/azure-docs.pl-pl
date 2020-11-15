---
title: Wprowadzenie do usługi Azure Red Hat OpenShift
description: Poznaj funkcje i zalety Microsoft Azure Red Hat OpenShift do wdrażania aplikacji opartych na kontenerach i zarządzania nimi.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2020
ms.custom: mvc
ms.openlocfilehash: dbc0f34e57297b9879a865511f6e7b4ed7ca2a3c
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636898"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Usługa *Red Hat OpenShift* systemu Microsoft Azure umożliwia wdrażanie w pełni zarządzanych klastrów [OpenShift](https://www.openshift.com/) .

Azure Red Hat OpenShift rozszerza [Kubernetes](https://kubernetes.io/). Uruchamianie kontenerów w środowisku produkcyjnym z Kubernetes wymaga dodatkowych narzędzi i zasobów. Często jest to potrzebne do Juggle rejestrów obrazów, zarządzania magazynem, rozwiązań sieciowych oraz narzędzi do rejestrowania i monitorowania — wszystko to, co musi mieć wersję i przetestowano. Tworzenie aplikacji opartych na kontenerach wymaga jeszcze większej integracji z programem pośredniczącym, strukturami, bazami danych i narzędziami do tworzenia i ciągłego tworzenia dysków. W systemie Azure Red Hat OpenShift wszystkie te elementy są połączone z jedną platformą, dzięki czemu można ułatwić zespołom IT wykonywanie prostych operacji, jednocześnie dostarczając zespoły aplikacji, których potrzebują.

W systemie Red Hat OpenShift jest wspólnie projektowana, obsługiwana i wspierana przez rozwiązania Red Hat i firmę Microsoft w celu zapewnienia zintegrowanego środowiska pomocy technicznej. Brak maszyn wirtualnych do działania i nie jest wymagane stosowanie poprawek. Węzły główne, infrastruktury i aplikacji są poprawione, aktualizowane i monitorowane w imieniu firmy Red Hat i Microsoft. Twoje klastry usługi Azure Red Hat OpenShift są wdrażane w ramach subskrypcji platformy Azure i są uwzględnione na rachunku na korzystanie z platformy Azure.

Możesz wybrać własne rozwiązania dotyczące rejestru, sieci, magazynu i ciągłej integracji/ciągłego dostarczania lub użyć wbudowanych rozwiązań do automatycznego zarządzania kodem źródłowym, kompilacji kontenerów i aplikacji, wdrożeń, skalowania, zarządzania kondycją i innych. Usługa Azure Red Hat OpenShift zapewnia zintegrowane środowisko logowania za pomocą Azure Active Directory.

Aby rozpocząć pracę, Wypełnij samouczek [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="access-security-and-monitoring"></a>Dostęp, zabezpieczenia i monitorowanie

W celu zwiększenia bezpieczeństwa i zarządzania usługa Azure Red Hat OpenShift umożliwia integrację z usługą Azure Active Directory (Azure AD) i korzystanie z kontroli dostępu opartej na rolach (RBAC) Kubernetes. Możesz także monitorować kondycję klastra i zasobów.

## <a name="cluster-and-node"></a>Klaster i węzeł

Węzły usługi Azure Red Hat OpenShift są uruchamiane na maszynach wirtualnych platformy Azure. Można połączyć magazyn z węzłami i składnikami programu.

## <a name="service-level-agreement"></a>Umowa dotycząca poziomu usług

W systemie Azure Red Hat Umowa dotycząca poziomu usług OpenShift można zagwarantować, że usługa będzie dostępna przez 99,95% czasu. Aby uzyskać więcej informacji na temat umowy SLA, zobacz [Azure Red Hat OPENSHIFT SLA](https://azure.microsoft.com/en-au/support/legal/sla/openshift/v1_0/).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z wymaganiami wstępnymi dotyczącymi usługi Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurowanie środowiska deweloperskiego](tutorial-create-cluster.md)
