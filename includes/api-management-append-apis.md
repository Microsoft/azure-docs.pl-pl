---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601923"
---
## <a name="append-other-apis"></a>Dołączanie innych interfejsów API

Możesz utworzyć interfejs API interfejsów API ujawnionych przez różne usługi, w tym:
* Specyfikacja openapi
* Interfejs API PROTOKOŁU SOAP
* Funkcja API Apps systemu Azure App Service
* Aplikacja funkcji platformy Azure
* Azure Logic Apps
* Azure Service Fabric

Dołącz inny interfejs API do istniejącego interfejsu API, korzystając z poniższych kroków. 

>[!NOTE] 
> Po zaimportowaniu innego interfejsu API operacje są dołączane do bieżącego interfejsu API.

1. Przejdź do wystąpienia usługi Azure API Management w witrynie Azure Portal.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Przejdź do wystąpienia narzędzia Mgmt interfejsu API platformy Azure":::

1. Wybierz pozycję **Interfejsy API** z menu po lewej stronie.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="Wybieranie interfejsów API":::

1. Kliknij przycisk **...** obok interfejsu API, który chcesz dołączyć do innego interfejsu API.
1. Z menu rozwijanego wybierz pozycję **Importuj**.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Wybieranie opcji importu":::

1. Wybierz usługę, z której chcesz zaimportować interfejs API.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Wybieranie usługi":::