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
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075286"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Inicjowanie obsługi administracyjnej bramy na platformie Azure API Management

Inicjowanie obsługi administracyjnej zasobu bramy w wystąpieniu usługi Azure API Management jest wymaganiem wstępnym do wdrożenia bramy samohostowanej. W tym artykule przedstawiono kroki inicjowania obsługi administracyjnej zasobu bramy w API Management.

> [!NOTE]
> Funkcja samoobsługowego bramy jest dostępna w wersji zapoznawczej. W wersji zapoznawczej Brama samoobsługowa jest dostępna tylko w warstwach deweloper i Premium bez dodatkowych opłat. Warstwa dewelopera jest ograniczona do jednego wdrożenia bramy samoobsługowego.

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
> [!TIP]
> Interfejs API można skojarzyć i usunąć z bramy na karcie **Ustawienia** interfejsu API.

> [!IMPORTANT]
> Domyślnie żaden z istniejących interfejsów API nie zostanie skojarzony z nowym zasobem bramy. W związku z tym próby wywołania ich za pośrednictwem nowej bramy będą `404 Resource Not Found` powodować odpowiedzi.

6. Kliknij pozycję **Add** (Dodaj).

Teraz zasób bramy został zainicjowany w wystąpieniu API Management. Możesz rozpocząć wdrażanie bramy.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz temat [usługa Azure API Management](self-hosted-gateway-overview.md) samodzielna Brama — Omówienie
* Dowiedz się więcej o sposobie [wdrażania bramy samohostowanej w usłudze Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Dowiedz się więcej o sposobie [wdrażania bramy samohostowanej na platformie Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
