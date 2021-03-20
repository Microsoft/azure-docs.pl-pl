---
title: Zarządzanie usługą Azure Cloud Services (klasyczną) przy użyciu Azure Automation | Microsoft Docs
description: Dowiedz się, w jaki sposób usługa Azure Automation może być używana do zarządzania usługami w chmurze platformy Azure na dużą skalę.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743495"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Zarządzanie usługą Azure Cloud Services (klasyczną) przy użyciu Azure Automation

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).
W tym przewodniku zawarto informacje o usłudze Azure Automation i sposobach ich użycia do uproszczenia zarządzania usługami w chmurze platformy Azure.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) to usługa platformy Azure, która upraszcza zarządzanie chmurą poprzez automatyzację procesów. Korzystanie z Azure Automation, długotrwałych, ręcznych, podatnych na błędy i często powtarzanych zadań może być zautomatyzowane, aby zwiększyć niezawodność, wydajność i czas do osiągnięcia korzyści dla organizacji.

Azure Automation zapewnia wysoce niezawodny i wysoce dostępny aparat wykonywania przepływu pracy, który skaluje się do potrzeb użytkownika w miarę rozwoju organizacji. W Azure Automation procesy mogą być uruchamiane ręcznie, przez systemy innych firm lub w zaplanowanych odstępach czasu, dzięki czemu zadania będą wykonywane dokładnie w razie potrzeby.

Obniż koszty operacyjne i zwolnij personel IT/DevOps, aby skoncentrować się na pracy, która zwiększa wartość biznesową, przenosząc zadania zarządzania chmurą, które mają być uruchamiane automatycznie przez Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Jak można Azure Automation pomóc w zarządzaniu usługami Azure Cloud Services?
Usługi Azure Cloud Services mogą być zarządzane w Azure Automation przy użyciu poleceń cmdlet programu PowerShell dostępnych w [narzędziach Azure PowerShell](/powershell/). Azure Automation dostępne są następujące polecenia cmdlet programu PowerShell dla usługi w chmurze, dzięki którym można wykonywać wszystkie zadania zarządzania usługą w chmurze w ramach usługi. Możesz również sparować te polecenia cmdlet w Azure Automation z poleceniami cmdlet dla innych usług platformy Azure, aby zautomatyzować złożone zadania w ramach usług platformy Azure i systemów innych firm.

Oto przykładowe zastosowania Azure Automation do zarządzania usługą Azure Cloud Services obejmują:

* [Ciągłe wdrażanie usługi w chmurze po każdej aktualizacji pliku cscfg lub cspkg w usłudze Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Ponowne uruchamianie wystąpień usługi w chmurze równolegle, jedna domena uaktualnienia w danym momencie](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Azure Automation i jak można jej użyć do zarządzania usługami w chmurze Azure, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o Azure Automation.

* [Omówienie usługi Azure Automation](../automation/automation-intro.md)
* [Mój pierwszy element Runbook](../automation/learn/automation-tutorial-runbook-graphical.md)