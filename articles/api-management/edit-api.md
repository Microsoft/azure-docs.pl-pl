---
title: Edytowanie interfejsu API przy użyciu witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak za pomocą API Management (APIM) edytować interfejs API. Dodaj, Usuń lub Zmień nazwę operacji w wystąpieniu APIM lub Edytuj strukturę Swagger interfejsu API.
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
ms.openlocfilehash: 8d3f476f4a6ecf05d63223f3ba2c2b5173e1d0bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905163"
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
> [Przykłady](policy-samples.md) 
>  zasad APIM [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)