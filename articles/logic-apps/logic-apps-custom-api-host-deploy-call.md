---
title: Wdróż i Wywołaj interfejsy API sieci Web & interfejsy API REST z Azure Logic Apps
description: Wdrażanie i wywoływanie interfejsów API sieci Web & interfejsów API REST dla przepływów pracy integracji systemu w programie Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74790755"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Wdrażanie i wywoływanie niestandardowych interfejsów API z przepływów pracy w Azure Logic Apps

Po [utworzeniu niestandardowych interfejsów API](./logic-apps-create-api-app.md) do użycia w przepływach pracy aplikacji logiki należy wdrożyć interfejsy API, aby można było je wywoływać. Interfejsy API można wdrożyć jako [aplikacje sieci Web](../app-service/overview.md), ale warto rozważyć wdrożenie interfejsów API jako [aplikacji interfejsu API](../app-service/app-service-web-tutorial-rest-api.md), co ułatwia wykonywanie zadań podczas kompilowania, hostowania i używania interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w interfejsach API — wystarczy wdrożyć kod w aplikacji interfejsu API. Interfejsy API można hostować w [Azure App Service](../app-service/overview.md), oferującej platformę jako usługę (PaaS), która zapewnia wysoce skalowalne i łatwe hosting interfejsów API.

Chociaż można wywołać dowolny interfejs API z poziomu aplikacji logiki, aby uzyskać najlepsze środowisko, Dodaj [metadane openapi (poprzednio Swagger)](https://swagger.io/specification/) opisujące operacje i parametry interfejsu API. Ten plik OpenAPI ułatwia korzystanie z interfejsu API w łatwiejszy i wydajny sposób.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Wdrażanie interfejsu API jako aplikacji sieci Web lub aplikacji interfejsu API

Aby można było wywołać niestandardowy interfejs API z poziomu aplikacji logiki, wdróż swój interfejs API jako aplikację sieci Web lub aplikację interfejsu API w celu Azure App Service. Ponadto, aby plik OpenAPI był możliwy do odczytania przez projektanta Logic Apps, ustaw właściwości definicji interfejsu API i Włącz [udostępnianie zasobów między źródłami (CORS)](../app-service/overview.md) dla aplikacji sieci Web lub aplikacji interfejsu API.

1. W [Azure Portal](https://portal.azure.com)wybierz aplikację sieci Web lub aplikację interfejsu API.

2. W otwartym menu aplikacji w obszarze **interfejs API**wybierz pozycję **definicja interfejsu API**. Ustaw **lokalizację definicji interfejsu API** na adres URL pliku openapi Swagger. JSON.

   Zazwyczaj adres URL jest wyświetlany w tym formacie:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link do pliku OpenAPI dla niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. W obszarze **interfejs API**wybierz pozycję **CORS**. Ustaw zasady CORS dla **dozwolonych źródeł** na **"*"** (Zezwalaj na wszystko).

   To ustawienie umożliwia żądanie od projektanta aplikacji logiki.

   ![Zezwalanie na żądania od projektanta aplikacji logiki do niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Aby uzyskać więcej informacji, zobacz [hostowanie interfejsu API RESTful z mechanizmem CORS w Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Wywoływanie niestandardowego interfejsu API z przepływów pracy aplikacji logiki

Po skonfigurowaniu właściwości definicji interfejsu API i mechanizmu CORS należy udostępnić niestandardowe wyzwalacze i akcje interfejsu API, które mają być dołączane do przepływu pracy aplikacji logiki. 

*  Aby wyświetlić witryny sieci Web z adresami URL OpenAPI, możesz przeglądać witryny sieci Web subskrypcji w programie Logic Apps Designer.

*  Aby wyświetlić dostępne akcje i dane wejściowe, wskazując na dokument OpenAPI, użyj [akcji protokołu HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Aby wywołać dowolny interfejs API, w tym interfejsy API, które nie mają lub nie uwidaczniają dokumentu OpenAPI, można zawsze utworzyć żądanie z [akcją http](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Następne kroki

* [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md)