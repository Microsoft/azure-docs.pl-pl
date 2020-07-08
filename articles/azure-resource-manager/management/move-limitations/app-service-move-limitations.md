---
title: Przenoszenie Azure App Service zasobów
description: Użyj Azure Resource Manager, aby przenieść zasoby App Service do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80655783"
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
- Wszystkie zasoby App Service w grupie zasobów należy przenieść razem. Należy pamiętać, że nie można przenieść środowisk App Service do nowej grupy zasobów ani do nowej subskrypcji.
- Można przenieść certyfikat powiązany z siecią Web bez usuwania powiązań TLS, o ile certyfikat zostanie przeniesiony ze wszystkimi innymi zasobami w grupie zasobów.
- Zasoby App Service można przenosić tylko z grupy zasobów, w której zostały pierwotnie utworzone. Jeśli zasób App Service nie jest już w oryginalnej grupie zasobów, przenieś go z powrotem do jego oryginalnej grupy zasobów. Następnie przenieś zasób między subskrypcjami.

Jeśli nie pamiętasz oryginalnej grupy zasobów, możesz ją znaleźć za pomocą diagnostyki. W przypadku aplikacji sieci Web wybierz opcję **Diagnozuj i rozwiąż problemy**. Następnie wybierz pozycję **Konfiguracja i zarządzanie**.

![Wybierz diagnostykę](./media/app-service-move-limitations/select-diagnostics.png)

Wybierz **Opcje migracji**.

![Wybieranie opcji migracji](./media/app-service-move-limitations/select-migration.png)

Wybierz opcję zalecanych kroków, aby przenieść aplikację sieci Web.

![Wybierz zalecane kroki](./media/app-service-move-limitations/recommended-steps.png)

Przed przeniesieniem zasobów zobaczysz zalecane czynności do wykonania. Informacje obejmują oryginalną grupę zasobów dla aplikacji sieci Web.

![Zalecenia](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Obsługa przenoszenia

Aby określić, które zasoby App Service można przenieść, zobacz Przenoszenie stanu wsparcia dla:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać polecenia przenoszenia zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../move-resource-group-and-subscription.md).
