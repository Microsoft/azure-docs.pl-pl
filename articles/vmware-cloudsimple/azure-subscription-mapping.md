---
title: Tworzenie pul zasobów przy użyciu mapowania subskrypcji platformy Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje sposób tworzenia pul zasobów dla chmury prywatnej za pomocą mapowania subskrypcji platformy Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be4a4c601d3f33972c1e52596ef623116dcadd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897080"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Tworzenie pul zasobów dla chmury prywatnej przy użyciu mapowania subskrypcji platformy Azure
Mapowanie subskrypcji platformy Azure umożliwia tworzenie pul zasobów dla chmury prywatnej na podstawie dostępnych pul zasobów vSphere. W portalu CloudSimple można wyświetlać subskrypcję platformy Azure dla chmur prywatnych i zarządzać nią.

> [!NOTE]
> Mapowanie puli zasobów również mapuje wszystkie podrzędne pule zasobów. Nie można zamapować nadrzędnej puli zasobów, jeśli wszystkie podrzędne pule zasobów zostały już zamapowane.

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).
2. Otwórz stronę **zasoby** i wybierz pozycję **Mapowanie subskrypcji platformy Azure**.  
3. Kliknij pozycję **Edytuj mapowanie subskrypcji platformy Azure**.  
4. Aby zmapować dostępne pule zasobów, zaznacz je po lewej stronie, a następnie kliknij strzałkę skierowaną w prawo. 
5. Aby usunąć mapowania, zaznacz je po prawej stronie, a następnie kliknij strzałkę skierowaną w lewo. 

    ![Subskrypcje platformy Azure](media/resources-azure-mapping.png)

6. Kliknij przycisk **OK**.
