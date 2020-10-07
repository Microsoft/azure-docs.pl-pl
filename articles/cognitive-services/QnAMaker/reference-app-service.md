---
title: Konfiguracja usługi — QnA Maker
description: Dowiedz się, jak i gdzie należy skonfigurować zasoby.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 1d54b912d2177a3ccd0cf34d57fc0358af653199
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776718"
---
# <a name="service-configuration"></a>Konfiguracja usługi

QnA Maker używa kilku zasobów platformy Azure (usług), w tym Wyszukiwanie poznawcze, App Service, App Service planu i Application Insights.

Poniżej wymieniono wszystkie dostosowania tych ustawień, które są obsługiwane przez QnA Maker.

## <a name="app-service"></a>App Service

QnA Maker używa App Service do zapewnienia środowiska uruchomieniowego zapytania używanego przez [interfejs API generateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Te ustawienia są dostępne w Azure Portal w App Service. Ustawienia są dostępne po wybraniu pozycji **Ustawienia**, a następnie **konfiguracji**.

Można ustawić pojedyncze ustawienie za pomocą listy ustawień aplikacji lub zmodyfikować kilka ustawień, wybierając pozycję **Edycja zaawansowana**.

|Zasób|Ustawienie|
|--|--|
|AzureSearchAdminKey|Wyszukiwanie poznawcze — używany do QnA pary magazynu i rangi #1|
|AzureSearchName|Wyszukiwanie poznawcze — używany do QnA pary magazynu i rangi #1|
|DefaultAnswer|Tekst odpowiedzi po znalezieniu dopasowania|
|UserAppInsightsAppId|Dziennik i dane telemetryczne rozmowy|
|UserAppInsightsKey|Dziennik i dane telemetryczne rozmowy|
|UserAppInsightsName|Dziennik i dane telemetryczne rozmowy|

Dowiedz się [, jak dodać zmianę usługi Wyszukiwanie poznawcze](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) do usługi.

Po zakończeniu wprowadzania zmian należy **ponownie uruchomić** usługę na stronie z **omówieniem** Azure Portal.

## <a name="qna-maker-service"></a>Usługa QnA Maker

Usługa QnA Maker zapewnia konfigurację dla następujących użytkowników w celu współpracy w ramach jednej usługi QnA Maker i wszystkich jej baz wiedzy.

Dowiedz się [, jak dodać współpracowników](./how-to/collaborate-knowledge-base.md) do usługi.

## <a name="application-insights"></a>Application Insights

Application Insights nie ma ustawień konfiguracji specyficznych dla QnA Maker.

## <a name="app-service-plan"></a>Plan usługi App Service

Plan App Service nie ma ustawień konfiguracji specyficznych dla QnA Maker.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [formatów](reference-document-format-guidelines.md) dokumentów i adresów URL, które chcesz zaimportować do bazy wiedzy.