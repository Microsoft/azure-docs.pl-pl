---
title: Konfiguracja usługi — QnA Maker
description: Dowiedz się, jak i gdzie należy skonfigurować zasoby.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: da46084c8c2616284c31ef155927e8dbcbd19e0e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209363"
---
# <a name="service-configuration"></a>Konfiguracja usługi

Każda wersja QnA Maker używa innego zestawu zasobów platformy Azure (usług). W tym artykule opisano obsługiwane dostosowania tych usług. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

QnA Maker używa App Service do zapewnienia środowiska uruchomieniowego zapytania używanego przez [interfejs API generateAnswer](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer).

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
|QNAMAKER_EXTENSION_VERSION|Zawsze ustawiono wartość _Najnowsza_. To ustawienie spowoduje zainicjowanie rozszerzenia witryny QnAMaker w App Service.|

Po zakończeniu wprowadzania zmian należy **ponownie uruchomić** usługę na stronie z **omówieniem** Azure Portal.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Dostosowania App Service nie mają zastosowania do QnA Maker zarządzanych (wersja zapoznawcza).

---

## <a name="qna-maker-service"></a>Usługa QnA Maker

Usługa QnA Maker zapewnia konfigurację dla następujących użytkowników w celu współpracy w ramach jednej usługi QnA Maker i wszystkich jej baz wiedzy.

Dowiedz się [, jak dodać współpracowników](./reference-role-based-access-control.md) do usługi.

## <a name="change-azure-cognitive-search"></a>Zmień Wyszukiwanie poznawcze platformy Azure

Dowiedz się [, jak zmienić usługę wyszukiwanie poznawcze](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) połączoną z usługą QNA Maker.

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
