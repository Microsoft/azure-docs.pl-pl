---
title: Eksportuj interfejsy API z platformy Azure API Management do platformy dodatku Microsoft Docs
description: Dowiedz się, jak wyeksportować interfejsy API z API Management na platformę energetyczną.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 9af20972a47e2d0ad20de62f1bb9d10e4d43563c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725952"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Eksportuj interfejsy API z platformy Azure API Management do platformy 

Deweloperzy deweloperów korzystający z [platformy Microsoft energetycznego](https://powerplatform.microsoft.com) często muszą uzyskać dostęp do możliwości firmy opracowanych przez profesjonalnych deweloperów i wdrożonych na platformie Azure. Usługa [Azure API Management](https://aka.ms/apimrocks) umożliwia profesjonalnym deweloperom publikowanie ich usługi zaplecza jako interfejsów API i łatwe eksportowanie tych interfejsów API do platformy, w której są stosowane łączniki (aplikacje zaawansowane i zaawansowane). 

W tym artykule omówiono procedurę eksportowania interfejsów API z API Management do platformy dodatku. 

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
+ Upewnij się, że w wystąpieniu API Management znajduje się interfejs API, który chcesz wyeksportować do platformy usługi.
+ Upewnij się, że masz aplikacje zaawansowane lub [środowisko](https://docs.microsoft.com/powerapps/powerapps-overview#power-apps-for-admins) automatyzacji 

## <a name="export-an-api"></a>Eksportowanie interfejsu API

1. Przejdź do usługi API Management w Azure Portal i wybierz opcję **interfejsy API** z menu.
2. Kliknij trzy kropki obok interfejsu API, który chcesz wyeksportować. 
3. Wybierz pozycję **Eksportuj**.
4. Wybierz pozycję **aplikacje zaawansowane i Automatyzacja**.
5. Wybierz środowisko, do którego chcesz wyeksportować interfejs API. 
6. Podaj nazwę wyświetlaną, która będzie używana jako nazwa łącznika niestandardowego.  
7. Opcjonalnie `Client ID`, jeśli interfejs API jest chroniony przez serwer OAuth 2,0, należy również podać dodatkowe informacje, w tym, `Client secret` `Authorization URL` `Token URL`,, i. `Refresh URL`  
8. Wybierz pozycję **Eksportuj**. 

Po zakończeniu eksportowania przejdź do środowiska aplikacji Zasilaczowej lub automatyzacji. Interfejs API zostanie wyświetlony jako łącznik niestandardowy.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o platformie dodatku](https://powerplatform.microsoft.com/)
* [Poznaj typowe zadania w API Management, wykonując samouczki](https://docs.microsoft.com/azure/api-management/import-and-publish)