---
title: Konfiguracja usługi — QnA Maker
description: Dowiedz się, jak i gdzie należy skonfigurować zasoby.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: eac930971cab041fbf398da1ac5f8a055412832d
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376863"
---
# <a name="service-configuration"></a>Konfiguracja usługi

Każda wersja QnA Maker używa innego zestawu zasobów platformy Azure (usług). W tym artykule opisano obsługiwane dostosowania tych usług. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

QnA Maker używa App Service do zapewnienia środowiska uruchomieniowego zapytania używanego przez [interfejs API generateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).

Te ustawienia są dostępne w Azure Portal w App Service. Ustawienia są dostępne po wybraniu pozycji **Ustawienia** , a następnie **konfiguracji**.

Można ustawić pojedyncze ustawienie za pomocą listy ustawień aplikacji lub zmodyfikować kilka ustawień, wybierając pozycję **Edycja zaawansowana**.

|Zasób|Ustawienie|
|--|--|
|AzureSearchAdminKey|Wyszukiwanie poznawcze — używany do QnA pary magazynu i rangi #1|
|AzureSearchName|Wyszukiwanie poznawcze — używany do QnA pary magazynu i rangi #1|
|DefaultAnswer|Tekst odpowiedzi po znalezieniu dopasowania|
|UserAppInsightsAppId|Dziennik i dane telemetryczne rozmowy|
|UserAppInsightsKey|Dziennik i dane telemetryczne rozmowy|
|UserAppInsightsName|Dziennik i dane telemetryczne rozmowy|

Po zakończeniu wprowadzania zmian należy **ponownie uruchomić** usługę na stronie z **omówieniem** Azure Portal.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Dostosowania App Service nie mają zastosowania do QnA Maker zarządzanych (wersja zapoznawcza).

---

## <a name="qna-maker-service"></a>Usługa QnA Maker

Usługa QnA Maker zapewnia konfigurację dla następujących użytkowników w celu współpracy w ramach jednej usługi QnA Maker i wszystkich jej baz wiedzy.

Dowiedz się [, jak dodać współpracowników](./how-to/collaborate-knowledge-base.md) do usługi.

## <a name="change-azure-cognitive-search"></a>Zmień Wyszukiwanie poznawcze platformy Azure

Dowiedz się [, jak zmienić usługę wyszukiwanie poznawcze](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) połączoną z usługą QNA Maker.

## <a name="change-default-answer"></a>Zmiana domyślnej odpowiedzi

Dowiedz się [, jak zmienić tekst domyślnych odpowiedzi](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetria

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Application Insights służy do monitorowania danych telemetrycznych z QnA Maker GA. Brak ustawień konfiguracji specyficznych dla QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Dowiedz się, [jak dodać telemetrię do usługi QNA Maker Managed (wersja zapoznawcza)](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>Plan usługi App Service

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (stabilna wersja)](#tab/v1)

Plan App Service nie ma ustawień konfiguracji specyficznych dla QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[Zarządzane QnAMaker (wersja zapoznawcza)](#tab/v2)

Plan App Service nie jest używany z QnA Maker zarządzanym (wersja zapoznawcza).

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [formatów](reference-document-format-guidelines.md) dokumentów i adresów URL, które chcesz zaimportować do bazy wiedzy.