---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687598"
---
W tej sekcji wyjaśniono, jak zastosować ograniczenie kontekstu zabezpieczeń (SCC). W wersji zapoznawczej te zmniejszają ograniczenia zabezpieczeń. 

1. Pobierz ograniczenie niestandardowego kontekstu zabezpieczeń (SCC). Wykonaj jedną z następujących czynności: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Nieprzetworzone](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      Następujące polecenie pobiera łuk-Data-SCC. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Utwórz SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Zastosuj SCC do konta usługi.

   > [!NOTE]
   > Użyj tej samej przestrzeni nazw tutaj i `azdata arc dc create` poniższego polecenia. Przykład to `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
