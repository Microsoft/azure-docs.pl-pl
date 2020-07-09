---
title: Domyślny rozmiar folderu tymczasowego jest za mały dla roli | Microsoft Docs
description: Rola usługi w chmurze ma ograniczoną ilość miejsca dla folderu tymczasowego. W tym artykule przedstawiono kilka sugestii, w których można uniknąć braku miejsca.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: bea9e163e9dde6c42de87048379d663222cb56e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82734624"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Domyślny rozmiar folderu tymczasowego jest za mały w roli sieć Web/proces roboczy usługi w chmurze
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
Zapoznaj się z blogiem opisującym [sposób zwiększenia rozmiaru folderu tymczasowego ASP.NET roli sieci Web platformy Azure](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Zobacz więcej [artykułów do rozwiązywania problemów](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) dotyczących usług Cloud Services.

Aby dowiedzieć się, jak rozwiązywać problemy z rolą usługi w chmurze, korzystając z danych diagnostycznych dotyczących komputerów z usługą Azure PaaS, zobacz [Seria blogów Jan Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
