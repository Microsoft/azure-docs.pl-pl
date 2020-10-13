---
title: Wdróż w istniejącej sieci wirtualnej
description: Opisuje, jak umożliwić użytkownikom aplikacji zarządzanej wybór istniejącej sieci wirtualnej. Sieć wirtualna może znajdować się poza zarządzaną aplikacją.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84261289"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Użyj istniejącej sieci wirtualnej z Azure Managed Applications

W tym artykule opisano sposób definiowania aplikacji zarządzanej przez platformę Azure, która integruje się z istniejącą siecią wirtualną w ramach subskrypcji konsumenta. Aplikacja zarządzana pozwala klientowi zdecydować, czy należy utworzyć nową sieć wirtualną, czy użyć istniejącej. Istniejąca sieć wirtualna może znajdować się poza zarządzaną grupą zasobów.

## <a name="main-template"></a>Szablon główny

Najpierw przyjrzyjmy się **mainTemplate.js** pliku. Poniżej przedstawiono cały szablon służący do wdrażania maszyny wirtualnej i skojarzonych z nią zasobów. Później przeanalizuje się części szablonu, które są związane z używaniem istniejącej sieci wirtualnej.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Należy zauważyć, że sieć wirtualna jest [wdrażana warunkowo](../templates/conditional-resource-deployment.md). Odbiorca przekazuje wartość parametru, która wskazuje, czy należy utworzyć nową lub użyć istniejącej sieci wirtualnej. Jeśli użytkownik wybierze nową sieć wirtualną, zasób zostanie wdrożony. W przeciwnym razie zasób zostanie pominięty podczas wdrażania.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

Zmienna identyfikatora sieci wirtualnej ma dwie właściwości. Po wdrożeniu nowej sieci wirtualnej jedna właściwość zwraca identyfikator zasobu. Druga Właściwość zwraca identyfikator zasobu, gdy jest używana istniejąca sieć wirtualna. Identyfikator zasobu dla istniejącej sieci wirtualnej zawiera nazwę grupy zasobów zawierającej sieć wirtualną.

Identyfikator podsieci jest zbudowany na podstawie wartości identyfikatora sieci wirtualnej. Używa wartości dopasowuje wybór odbiorców.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

Interfejs sieciowy jest ustawiony na zmienną identyfikatora podsieci.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>Definicja interfejsu użytkownika

Teraz przyjrzyjmy się **createUiDefinition.js** pliku. Cały plik to:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

Plik zawiera element sieci wirtualnej.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Ten element umożliwia odbiorcy wybranie nowej lub istniejącej sieci wirtualnej.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Nowa lub istniejąca sieć wirtualna":::

W danych wyjściowych należy uwzględnić wartość wskazującą, czy konsument wybrał nową, czy istniejącą sieć wirtualną. Istnieje również zarządzana wartość tożsamości.

> [!NOTE]
> Wartość wyjściowa dla tożsamości zarządzanej musi mieć nazwę **managedIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia pliku definicji interfejsu użytkownika, zobacz [CreateUiDefinition.jsna potrzeby tworzenia środowiska aplikacji zarządzanej przez platformę Azure](create-uidefinition-overview.md).
