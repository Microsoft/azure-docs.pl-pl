---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494020"
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

   > [!NOTE]
   > RedHat OpenShift 4,5 lub nowszy, zmienia sposób zastosowania SCC do konta usługi.
   > Użyj tej samej przestrzeni nazw tutaj i `azdata arc dc create` poniższego polecenia. Przykład to `arc` . 
   > 
   > Jeśli używasz programu RedHat OpenShift 4,5 lub nowszego, uruchom polecenie: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
