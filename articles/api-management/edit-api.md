---
title: Edytowanie interfejsu API przy użyciu witryny Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia sposób użycia usługi API Management (APIM) do edytowania interfejsu API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6be36493fabce07838991c789e111e918a9a826d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "70072151"
---
# <a name="edit-an-api"></a>Edytowanie interfejsu API

Kroki opisane w tym samouczku umożliwiają użycie usługi API Management (APIM) do edytowania interfejsu API. 

+ Można dodawać i usuwać operacje oraz zmieniać ich nazwy w wystąpieniu usługi APIM. 
+ Można edytować program Swagger interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md)
+ [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Edytowanie interfejsu API w usłudze APIM

![Edytowanie interfejsu API](./media/edit-api/edit-api001.png)

1. Kliknij kartę **Interfejsy API**.
2. Wybierz jeden z wcześniej zaimportowanych interfejsów API.
3. Wybierz kartę **Projekt**.
4. Wybierz operację, którą chcesz edytować.
5. Aby zmienić nazwę operacji, wybierz ikonę **ołówka** w oknie **Fronton**.

## <a name="update-the-swagger"></a>Aktualizowanie programu Swagger

Możesz zaktualizować interfejs API zaplecza w witrynie Azure Portal, wykonując następujące czynności:

1. Wybierz **wszystkie operacje**
2. Kliknij ikonę ołówka w oknie **Fronton**.

    ![Edytowanie interfejsu API](./media/edit-api/edit-api002.png)

    Zostanie wyświetlony program Swagger interfejsu API.

    ![Edytowanie interfejsu API](./media/edit-api/edit-api003.png)

3. Zaktualizuj program Swagger.
4. Naciśnij pozycję **Zapisz**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przykłady zasad APIM](policy-samples.md)
> [przekształcają i chronią opublikowany interfejs API](transform-api.md)