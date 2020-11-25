---
title: Tworzenie i kojarzenie zasad punktu końcowego usługi — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym artykule dowiesz się, jak skonfigurować zasady punktu końcowego usługi i skojarzone z nią przy użyciu Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004963"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Tworzenie, zmienianie lub usuwanie zasad punktu końcowego usługi przy użyciu Azure Portal

Zasady punktu końcowego usługi umożliwiają filtrowanie ruchu w sieci wirtualnej do określonych zasobów platformy Azure za pośrednictwem punktów końcowych usługi. Jeśli nie znasz zasad punktu końcowego usługi, zobacz [Omówienie zasad punktu końcowego usługi](virtual-network-service-endpoint-policies-overview.md) , aby dowiedzieć się więcej.

 Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie zasad punktu końcowego usługi
> * Tworzenie definicji zasad punktu końcowego usługi
> * Tworzenie sieci wirtualnej z podsiecią
> * Kojarzenie zasad punktu końcowego usługi z podsiecią

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Tworzenie zasad punktu końcowego usługi

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. W okienku wyszukiwania wpisz "zasady punktu końcowego usługi" i wybierz pozycję **zasady punktu końcowego usługi** , a następnie wybierz pozycję **Utwórz**.

![Utwórz zasady punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Wprowadź lub wybierz następujące informacje w **temacie Podstawowe** 

   - Subskrypcja: wybierz subskrypcję zasad
   - Grupa zasobów: wybierz pozycję **Utwórz nową** i *wprowadź grupę zasobów.*
   - Nazwa: myEndpointPolicy
   - Lokalizacja: środkowe stany USA
 
   ![Podstawowe informacje dotyczące tworzenia zasad punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Wybierz pozycję **+ Dodaj** w obszarze **zasoby** i wprowadź lub wybierz następujące informacje w okienku **Dodawanie zasobu**

   - Usługa: tylko **Microsoft. Storage** jest dostępna z zasadami punktu końcowego usługi
   - Zakres: wybierz jedno z jednego **konta**, **wszystkie konta w subskrypcji** i **wszystkie konta w grupie zasobów**
   - Subskrypcja: wybierz subskrypcję dla konta magazynu. Konta zasad i magazynu mogą znajdować się w różnych subskrypcjach.
   - Grupa zasobów: Wybierz grupę zasobów. Wymagane, jeśli zakres jest ustawiony jako, "wszystkie konta w grupie zasobów" lub "pojedyncze konto".  
   - Zasób: wybierz zasób usługi Azure Storage w ramach wybranej subskrypcji lub grupy zasobów
   - Kliknij przycisk **Dodaj** u dołu, aby zakończyć dodawanie zasobu

   ![Definicja zasad punktu końcowego usługi — zasób](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Dodaj więcej zasobów przez powtórzenie powyższych kroków zgodnie z wymaganiami

5. Opcjonalne: wprowadź lub wybierz następujące informacje w **tagach**:
   
   - Klucz: Wybierz klucz dla zasad. Przykład: Dept     
   - Wartość: wprowadź parę wartości dla klucza. Ex: Finanse

6. Wybierz pozycję **Recenzja + Utwórz**. Sprawdź poprawność informacji i kliknij przycisk **Utwórz**. Aby wprowadzić dalsze zmiany, kliknij przycisk **Wstecz**. 

   ![Utwórz końcowe walidacje zasad punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Wyświetl zasady punktu końcowego 

1. W polu *wszystkie usługi* w portalu zacznij pisać *zasady punktu końcowego usługi*. Wybierz pozycję **zasady punktu końcowego usługi**.
2. W obszarze **subskrypcje** wybierz swoją subskrypcję i grupę zasobów, jak pokazano na poniższej ilustracji.

   ![Pokaż zasady](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Wybierz zasady, a następnie kliknij pozycję **definicje zasad** , aby wyświetlić lub dodać więcej definicji zasad.

   ![Pokaż definicje zasad](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Wybierz **skojarzone podsieci** , aby wyświetlić podsieci, które są skojarzone z zasadami. Jeśli nie skojarzono jeszcze żadnej podsieci, postępuj zgodnie z instrukcjami w następnym kroku.

   ![Skojarzone podsieci](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Kojarzenie zasad z podsiecią

>[!WARNING] 
> Przed skojarzeniem zasad z daną podsiecią upewnij się, że wszystkie zasoby, do których uzyskano dostęp z podsieci, są dodawane do definicji zasad. Po skojarzeniu zasad tylko dostęp do zasobów *dozwolonych na liście* będzie dozwolony za pośrednictwem punktów końcowych usługi. 
>
> Upewnij się również, że żadne zarządzane usługi platformy Azure nie istnieją w podsieci, która jest skojarzona z zasadami punktu końcowego usługi

- Aby można było skojarzyć zasady z podsiecią, należy utworzyć sieć wirtualną i podsieć. Aby uzyskać pomoc dotyczącą tego, zapoznaj się z artykułem [tworzenie Virtual Network](./quick-create-portal.md) .

- Po skonfigurowaniu sieci wirtualnej i podsieci należy skonfigurować Virtual Network punkty końcowe usługi Azure Storage. W bloku Virtual Network wybierz pozycję **punkty końcowe usługi**, a następnie w następnym okienku wybierz pozycję **Microsoft. Storage** i w obszarze **podsieci** wybierz żądaną sieć wirtualną lub podsieci

- Teraz można wybrać zasady punktu końcowego usługi z listy rozwijanej w powyższym okienku, jeśli wcześniej utworzono zasady punktu końcowego usługi przed skonfigurowaniem punktu końcowego usługi dla podsieci, jak pokazano poniżej

    ![Skojarz podsieć podczas tworzenia punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- LUB w przypadku kojarzenia zasad punktu końcowego usługi po skonfigurowaniu punktów końcowych usługi możesz wybrać opcję skojarzenia podsieci z bloku zasad punktu końcowego usługi, przechodząc do okienka **skojarzone podsieci** , jak pokazano poniżej.

    ![Skojarz podsieć za pośrednictwem protokołu SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Dostęp do zasobów usługi Azure Storage we wszystkich regionach będzie ograniczony do zasad punktu końcowego usługi w tej podsieci.

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono zasady punktu końcowego usługi i skojarzono je z podsiecią. Aby dowiedzieć się więcej na temat zasad punktu końcowego usługi, zobacz [Omówienie zasad punktu końcowego usługi.](virtual-network-service-endpoint-policies-overview.md)
