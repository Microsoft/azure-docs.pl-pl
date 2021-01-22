---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696072"
---
W tej sekcji wyjaśniono, jak zastosować ograniczenie kontekstu zabezpieczeń (SCC). W wersji zapoznawczej te zmniejszają ograniczenia zabezpieczeń. 

1. Pobierz ograniczenie niestandardowego kontekstu zabezpieczeń (SCC). Wykonaj jedną z następujących czynności: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([RAW](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Następujące polecenie pobiera łuk-Data-SCC. YAML:

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