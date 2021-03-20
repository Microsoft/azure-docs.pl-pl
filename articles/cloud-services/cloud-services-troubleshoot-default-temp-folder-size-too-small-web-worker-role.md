---
title: Domyślny rozmiar folderu tymczasowego jest za mały dla roli | Microsoft Docs
description: Rola usługi w chmurze ma ograniczoną ilość miejsca dla folderu tymczasowego. W tym artykule przedstawiono kilka sugestii, w których można uniknąć braku miejsca.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1b7bfb47168c31f9e2e1b7e40764439118c00805
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743206"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>Domyślny rozmiar folderu tymczasowego jest za mały w usłudze w chmurze (klasycznej) w roli sieć Web/proces roboczy

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Domyślny katalog tymczasowy procesu roboczego usługi w chmurze lub roli sieci Web ma maksymalny rozmiar 100 MB, który może być zapełniony w pewnym momencie. W tym artykule opisano, jak uniknąć braku miejsca na katalog tymczasowy.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Dlaczego zabrakło miejsca?
Standardowe zmienne środowiskowe systemu Windows w TEMP i TMP są dostępne dla kodu, który jest uruchomiony w aplikacji. Zarówno TEMP, jak i PZT wskazują pojedynczy katalog o maksymalnym rozmiarze 100 MB. Wszystkie dane przechowywane w tym katalogu nie są utrwalane w cyklu życia usługi w chmurze; Jeśli wystąpienia roli w usłudze w chmurze są odtwarzane, katalog jest czyszczony.

## <a name="suggestion-to-fix-the-problem"></a>Sugestia usunięcia problemu
Zaimplementuj jedną z następujących alternatyw:

* Skonfiguruj zasób magazynu lokalnego i uzyskuj dostęp do niego bezpośrednio, zamiast korzystać z usługi TEMP lub TMP. Aby uzyskać dostęp do zasobu magazynu lokalnego z kodu, który jest uruchomiony w aplikacji, wywołaj metodę [RoleEnvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Skonfiguruj zasób magazynu lokalnego i wskaż katalogi TEMP i TMP, aby wskazywały ścieżkę lokalnego zasobu magazynu. Tę modyfikację należy wykonać w metodzie [RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

Poniższy przykład kodu pokazuje, jak modyfikować katalogi docelowe dla TEMP i TMP z poziomu metody OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z blogiem opisującym [sposób zwiększenia rozmiaru folderu tymczasowego ASP.NET roli sieci Web platformy Azure](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Zobacz więcej [artykułów do rozwiązywania problemów](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) dotyczących usług Cloud Services.

Aby dowiedzieć się, jak rozwiązywać problemy z rolą usługi w chmurze, korzystając z danych diagnostycznych dotyczących komputerów z usługą Azure PaaS, zobacz [Seria blogów Jan Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).