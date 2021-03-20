---
title: Tworzenie subskrypcji na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak tworzyć subskrypcje na platformie Azure API Management. Aby uzyskać dostęp do interfejsów API, należy uzyskać subskrypcję.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 191323a4c150c00c93245be35c9c8af381e26b42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87904867"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Tworzenie subskrypcji w usłudze Azure API Management

Po opublikowaniu interfejsów API za pomocą usługi Azure API Management jest to łatwe i powszechne do zabezpieczenia dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Aplikacje klienckie, które muszą używać opublikowanych interfejsów API, muszą zawierać prawidłowy klucz subskrypcji w żądaniach HTTP podczas wykonywania wywołań do tych interfejsów API. Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Aby uzyskać więcej informacji o subskrypcjach, zobacz [subskrypcje na platformie Azure API Management](api-management-subscriptions.md).

W tym artykule omówiono procedurę tworzenia subskrypcji w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagania wstępne są następujące:

+ [Utwórz wystąpienie API Management](get-started-create-service-instance.md).
+ Informacje [o subskrypcjach w API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Utwórz nową subskrypcję

1. Wybierz pozycję **subskrypcje** w menu po lewej stronie.
2. Wybierz pozycję **Dodaj subskrypcję**.
3. Podaj nazwę subskrypcji i wybierz zakres.
4. Opcjonalnie możesz wybrać, czy subskrypcja ma być skojarzona z użytkownikiem.
5. Wybierz pozycję **Zapisz**.

![Elastyczne subskrypcje](./media/api-management-subscriptions/flexible-subscription.png)

Po utworzeniu subskrypcji są udostępniane dwa klucze interfejsu API w celu uzyskania dostępu do interfejsów API. Jeden klucz jest podstawowy, a drugi to pomocniczy. 

## <a name="next-steps"></a>Następne kroki
Uzyskaj więcej informacji na API Management:

+ Zapoznaj się z innymi [pojęciami](api-management-terminology.md) w API Management.
+ Postępuj zgodnie z naszymi [samouczkami](import-and-publish.md) , aby dowiedzieć się więcej o API Management.
+ Zapoznaj się z naszą [stroną często zadawanych](api-management-faq.md) pytań.