---
title: Omówienie openshift w usłudze Azure
description: Omówienie funkcji OpenShift na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c338e9a6a793d1c2d0557d70242996175d5a85ed
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759459"
---
# <a name="openshift-in-azure"></a>OpenShift na platformie Azure

OpenShift to otwarta i rozszerzalna platforma aplikacji kontenera, która przynosi docker i Kubernetes do przedsiębiorstwa.  

OpenShift zawiera kubernetes do aranżacji kontenera i zarządzania. Dodaje narzędzia zorientowane na dewelopera i zorientowane na operacje, które umożliwiają:

- Szybki rozwój aplikacji.
- Łatwe wdrażanie i skalowanie.
- Długoterminowa konserwacja cyklu życia zespołów i aplikacji.

Dostępnych jest wiele wersji programu OpenShift.  Z tych wersji tylko dwie są obecnie dostępne dla klientów do wdrożenia na platformie Azure: OpenShift Container Platform i OKD (dawniej OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift to w pełni zarządzana oferta OpenShift działająca na platformie Azure. Ta usługa jest wspólnie zarządzana i obsługiwana przez firmy Microsoft i Red Hat. Aby uzyskać więcej informacji, zobacz dokumentację [usługi Azure Red Hat OpenShift.](https://docs.microsoft.com/azure/openshift/)

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform to [komercyjna wersja](https://www.openshift.com) gotowa dla przedsiębiorstw z i obsługiwana przez Red Hat. W tej wersji klienci kupują niezbędne uprawnienia do platformy kontenerowej OpenShift i są odpowiedzialni za instalację i zarządzanie całą infrastrukturą.

Ponieważ klienci "właścicielem" całej platformy, mogą zainstalować go w lokalnym centrum danych lub w chmurze publicznej (na przykład platformy Azure).

## <a name="okd"></a>OKD

OKD to [projekt open-source](https://www.okd.io/) openstream OpenShift, który jest obsługiwany przez społeczność. OKD można zainstalować na CentOS lub Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie typowych wymagań wstępnych dla usługi OpenShift na platformie Azure](./openshift-container-platform-3x-prerequisites.md)
- [Wdrażanie platformy kontenerów OpenShift na platformie Azure](./openshift-container-platform-3x.md)
- [Wdrażanie oferty platformy kontenerowej OpenShift self-managed Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Wdrażanie usługi OpenShift w usłudze Azure Stack](./openshift-azure-stack.md)
- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift](./openshift-container-platform-3x-troubleshooting.md)
