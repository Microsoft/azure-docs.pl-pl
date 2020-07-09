---
title: Inicjowanie obsługi administracyjnej bramy na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak zainicjować obsługę administracyjną bramy na platformie Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 2870a654faad4e760a9d022488cb2c4c406cbeab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82203135"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Inicjowanie obsługi administracyjnej bramy na platformie Azure API Management

Inicjowanie obsługi administracyjnej zasobu bramy w wystąpieniu usługi Azure API Management jest wymaganiem wstępnym do wdrożenia bramy samohostowanej. W tym artykule przedstawiono kroki inicjowania obsługi administracyjnej zasobu bramy w API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Aprowizowanie własnej bramy

1. Wybierz **bramy** z sekcji **Ustawienia**.
2. Kliknij pozycję **+ Dodaj**.
3. Wprowadź **nazwę** i **region** bramy.
> [!TIP]
> **Region** określa zamierzoną lokalizację węzłów bramy, które będą skojarzone z tym zasobem bramy. Jest ona semantycznie równoważna z podobną właściwością skojarzoną z dowolnym zasobem platformy Azure, ale można ją przypisać do dowolnej wartości ciągu.

4. Opcjonalnie wprowadź **Opis** zasobu bramy.
5. Opcjonalnie wybierz opcję **+** w obszarze **interfejsy API** , aby skojarzyć jeden lub więcej interfejsów API z tym zasobem bramy.
> [!IMPORTANT]
> Domyślnie żaden z istniejących interfejsów API nie zostanie skojarzony z nowym zasobem bramy. W związku z tym próby wywołania ich za pośrednictwem nowej bramy będą powodować `404 Resource Not Found` odpowiedzi.

6. Kliknij pozycję **Dodaj**.

Teraz zasób bramy został zainicjowany w wystąpieniu API Management. Możesz rozpocząć wdrażanie bramy.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [usługa Azure API Management](self-hosted-gateway-overview.md) samodzielna Brama — Omówienie
* Dowiedz się więcej o sposobie [wdrażania bramy samohostowanej w usłudze Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* Dowiedz się więcej o sposobie [wdrażania bramy samohostowanej na platformie Docker](how-to-deploy-self-hosted-gateway-docker.md)
