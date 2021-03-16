---
title: 'ML Studio (klasyczny): Migruj do Azure Machine Learning — korzystaj z punktów końcowych potoku'
description: Integruj punkty końcowe potoku z aplikacjami klienckimi w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565246"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Korzystanie z punktów końcowych potoków z aplikacji klienckich

W tym artykule dowiesz się, jak zintegrować aplikacje klienckie z Azure Machine Learning punktami końcowymi. Aby uzyskać więcej informacji na temat pisania kodu aplikacji, zobacz [Korzystanie z punktu końcowego Azure Machine Learning](../how-to-consume-web-service.md).

Ten artykuł jest częścią Studio (klasyczną) do Azure Machine Learning serii migracji. Aby uzyskać więcej informacji na temat migracji do Azure Machine Learning, zobacz [artykuł Omówienie migracji](migrate-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Obszar roboczy usługi Azure Machine Learning. [Utwórz obszar roboczy Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- [Azure Machine Learning punkt końcowy w czasie rzeczywistym lub punkt końcowy potoku](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Korzystanie z punktu końcowego w czasie rzeczywistym 

Jeśli model został wdrożony jako **punkt końcowy w czasie rzeczywistym**, można znaleźć punkt końcowy Rest oraz wstępnie wygenerowany kod zużycia w językach C#, Python i R:

1. Przejdź do Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Przejdź do karty **punkty końcowe** .
1. Wybierz punkt końcowy w czasie rzeczywistym.
1. Wybierz **pozycję** Użyj.

> [!NOTE]
> Specyfikacja struktury Swagger dla punktu końcowego można również znaleźć na karcie **szczegóły** . Użyj definicji Swagger, aby zrozumieć schemat punktu końcowego. Aby uzyskać więcej informacji na temat definicji struktury Swagger, zobacz [oficjalny dokument struktury Swagger](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Korzystanie z punktu końcowego potoku

Istnieją dwa sposoby korzystania z punktu końcowego potoku:

- Wywołania interfejsu API REST
- Integracja z usługą Azure Data Factory

### <a name="use-rest-api-calls"></a>Korzystanie z wywołań interfejsu API REST

Wywołaj punkt końcowy REST z aplikacji klienckiej. Aby zrozumieć swój schemat, można użyć specyfikacji Swagger dla punktu końcowego:

1. Przejdź do Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Przejdź do karty **punkty końcowe** .
1. Wybierz **punkty końcowe potoku**.
1. Wybierz punkt końcowy potoku.
1. W okienku **Przegląd punktu końcowego potoku** wybierz link w obszarze **Dokumentacja punktu końcowego REST**.

### <a name="use-azure-data-factory"></a>Za pomocą usługi Azure Data Factory

Potok Azure Machine Learning można wywołać jako krok w potoku Azure Data Factory. Aby uzyskać więcej informacji, zobacz [wykonywanie potoków Azure Machine Learning w Azure Data Factory](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób znajdowania schematu i przykładowy kod dla punktów końcowych potoku. Aby uzyskać więcej informacji na temat konsumowania punktów końcowych z aplikacji klienckiej, zobacz [Korzystanie z punktu końcowego Azure Machine Learning](../how-to-consume-web-service.md).

Zapoznaj się z pozostałymi artykułami w Azure Machine Learning seriach migracji: 
1. [Omówienie migracji](migrate-overview.md).
1. [Migrowanie zestawu danych](migrate-register-dataset.md).
1. [Kompiluj potok szkoleniowy Studio (klasyczny)](migrate-rebuild-experiment.md).
1. [Kompiluj ponownie usługę sieci Web programu Studio (klasyczna)](migrate-rebuild-web-service.md).
1. **Zintegruj usługę sieci web Azure Machine Learning z aplikacjami klienckimi**.
1. [Migruj skrypt wykonania skryptu języka R](migrate-execute-r-script.md).