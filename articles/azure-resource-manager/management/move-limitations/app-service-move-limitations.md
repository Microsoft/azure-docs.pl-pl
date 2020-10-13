---
title: Przenoszenie Azure App Service zasobów
description: Użyj Azure Resource Manager, aby przenieść zasoby App Service do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531376"
---
# <a name="move-guidance-for-app-service-resources"></a>Wskazówki dotyczące przenoszenia dla App Service zasobów

W tym artykule opisano czynności, które należy wykonać, aby przenieść App Service zasoby. Istnieją pewne wymagania dotyczące przechodzenia App Service zasobów do nowej subskrypcji.

## <a name="move-across-subscriptions"></a>Przechodzenie między subskrypcjami

Podczas przemieszczania aplikacji sieci Web w ramach subskrypcji są stosowane następujące wskazówki:

- Docelowa Grupa zasobów nie może mieć żadnych istniejących zasobów App Service. App Service zasoby obejmują:
    - Web Apps
    - Plany usługi App Service
    - Przekazano lub zaimportowano certyfikaty TLS/SSL
    - Środowiska usługi App Service
- Wszystkie zasoby App Service w grupie zasobów należy przenieść razem.
- Nie można przenieść środowisk App Service do nowej grupy zasobów lub subskrypcji. Można jednak przenieść aplikację sieci Web i plan usługi App Service do nowej subskrypcji bez przechodzenia App Service Environment. Po przeniesieniu aplikacja internetowa nie będzie już hostowana w App Service Environment.
- Można przenieść certyfikat powiązany z siecią Web bez usuwania powiązań TLS, o ile certyfikat zostanie przeniesiony ze wszystkimi innymi zasobami w grupie zasobów.
- Zasoby App Service można przenosić tylko z grupy zasobów, w której zostały pierwotnie utworzone. Jeśli zasób App Service nie jest już w oryginalnej grupie zasobów, przenieś go z powrotem do jego oryginalnej grupy zasobów. Następnie przenieś zasób między subskrypcjami.

Jeśli nie pamiętasz oryginalnej grupy zasobów, możesz ją znaleźć za pomocą diagnostyki. W przypadku aplikacji sieci Web wybierz opcję **Diagnozuj i rozwiąż problemy**. Następnie wybierz pozycję **Konfiguracja i zarządzanie**.

![Wybierz diagnostykę](./media/app-service-move-limitations/select-diagnostics.png)

Wybierz **Opcje migracji**.

![Wybieranie opcji migracji](./media/app-service-move-limitations/select-migration.png)

Wybierz opcję zalecanych kroków, aby przenieść aplikację sieci Web.

![Wybierz zalecane kroki](./media/app-service-move-limitations/recommended-steps.png)

Przed przeniesieniem zasobów zobaczysz zalecane czynności do wykonania. Informacje obejmują oryginalną grupę zasobów dla aplikacji sieci Web.

![W obszarze przechwytywanie ekranu przedstawiono zalecane kroki, które należy wykonać, aby przenieść zasoby internetowe Microsoft dot.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Obsługa przenoszenia

Aby określić, które zasoby App Service można przenieść, zobacz Przenoszenie stanu wsparcia dla:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
