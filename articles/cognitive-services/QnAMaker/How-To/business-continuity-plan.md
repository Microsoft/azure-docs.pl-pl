---
title: Plan ciągłości biznesowej — QnA Maker
description: Głównym celem planu ciągłości działalności biznesowej jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak czasu dla bot lub aplikacji.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887076"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Utwórz plan ciągłości biznesowej dla usługi QnA Maker

Głównym celem planu ciągłości działalności biznesowej jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak czasu dla bot lub aplikacji.

## <a name="business-continuity-with-traffic-manager"></a>Ciągłość działania dzięki usłudze Traffic Manager

> [!div class="mx-imgBorder"]
> ![QnA Maker plan BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Pomysł wysokiego poziomu przedstawiony powyżej jest następujący:

1. Skonfiguruj dwie [usługi QNA Maker Parallel Services](set-up-qnamaker-service-azure.md) w [sparowanych regionach platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Utwórz kopię zapasową](../../../app-service/manage-backup.md) głównej usługi App Service QNA Maker i [Przywróć](../../../app-service/web-sites-restore.md) ją w konfiguracji pomocniczej. Zapewni to, że obie konfiguracje będą działały z tą samą nazwą hosta i kluczami.

3. Zachowaj synchronizację podstawowych i pomocniczych indeksów usługi Azure Search. Skorzystaj [z przykładu GitHub, aby](https://github.com/pchoudhari/QnAMakerBackupRestore) zobaczyć, jak tworzyć kopie zapasowe indeksów platformy Azure.

4. Wykonaj kopię zapasową Application Insights za pomocą [eksportu ciągłego](../../../application-insights/app-insights-export-telemetry.md).

5. Po skonfigurowaniu stosów podstawowych i pomocniczych Użyj usługi [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) , aby skonfigurować dwa punkty końcowe i skonfigurować metodę routingu.

6. Należy utworzyć Transport Layer Security (TLS), wcześniej znany jako SSL (SSL), certyfikat dla punktu końcowego usługi Traffic Manager. [Powiąż certyfikat TLS/SSL](../../../app-service/configure-ssl-bindings.md) w usługach aplikacji.

7. Na koniec użyj punktu końcowego w usłudze Traffic Manager w bot lub aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie pojemności](./improve-knowledge-base.md)