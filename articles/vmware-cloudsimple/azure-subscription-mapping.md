---
title: Tworzenie pul zasobów przy użyciu mapowania subskrypcji platformy Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje sposób tworzenia pul zasobów dla chmury prywatnej za pomocą mapowania subskrypcji platformy Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77014967"
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
