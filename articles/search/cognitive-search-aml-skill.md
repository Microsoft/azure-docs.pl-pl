---
title: Niestandardowa umiejętność AML w umiejętności
titleSuffix: Azure Cognitive Search
description: Zwiększaj możliwości usługi Azure Wyszukiwanie poznawcze umiejętności z modelami Azure Machine Learning.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: d1e6f4e16e3eda8519913a9e2ae14f7cc909bf61
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445459"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>AML umiejętność w potoku wzbogacenia Wyszukiwanie poznawcze platformy Azure

> [!IMPORTANT] 
> Ta umiejętność jest obecnie dostępna w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Obecnie nie ma obsługi zestawu SDK platformy .NET.

Umiejętność **AML** umożliwia zwiększenie wzbogacenia AI przy użyciu modelu niestandardowego [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) (AML). Gdy model AML zostanie [przeszkolony i wdrożony](../machine-learning/concept-azure-machine-learning-architecture.md#workspace), umiejętność **AML** integruje ją w wzbogacanie AI.

Podobnie jak w przypadku umiejętności wbudowanych, umiejętność **AML** ma dane wejściowe i wyjściowe. Dane wejściowe są wysyłane do wdrożonej usługi AML jako obiekt JSON, który wyprowadza ładunek JSON jako odpowiedź wraz z kodem stanu sukcesu. Oczekiwane jest, że odpowiedź będzie miała dane wyjściowe określone przez umiejętność **AML** . Jakakolwiek inna odpowiedź jest traktowana jako błąd i nie są wykonywane żadne wzbogacania.

> [!NOTE]
> Indeksator zostanie dwukrotnie ponowiony dla niektórych standardowych kodów stanu HTTP zwróconych z usługi AML. Kody stanu HTTP:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Wymagania wstępne

* [Obszar roboczy AML](../machine-learning/concept-workspace.md)
* [Obiekt docelowy obliczeń usługi Azure Kubernetes Service AML](../machine-learning/concept-compute-target.md) w tym obszarze roboczym ze [wdrożonym modelem](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * [Element docelowy obliczeń powinien mieć włączony protokół SSL](../machine-learning/how-to-secure-web-service.md#deploy-on-aks-and-field-programmable-gate-array-fpga). Usługa Azure Wyszukiwanie poznawcze zezwala tylko na dostęp do punktów końcowych **https**
  * Nie można używać certyfikatów z podpisem własnym.

## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. Custom. AmlSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter. Które parametry wybrane do użycia zależą od tego [, jakie uwierzytelnianie wymaga usługa AML (jeśli istnieje](#WhatSkillParametersToUse) )

| Nazwa parametru | Opis |
|--------------------|-------------|
| `uri` | (Wymagane w przypadku [braku uwierzytelniania lub uwierzytelniania klucza](#WhatSkillParametersToUse)) [Identyfikator URI oceniania usługi AML](../machine-learning/how-to-consume-web-service.md) , do której zostanie wysłany ładunek _JSON_ . Dozwolony jest tylko schemat **https** URI. |
| `key` | (Wymagane na potrzeby [uwierzytelniania klucza](#WhatSkillParametersToUse)) [Klucz usługi AML](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Wymagane na potrzeby [uwierzytelniania tokenu](#WhatSkillParametersToUse)). Identyfikator zasobu Azure Resource Manager usługi AML. Powinien być w formacie subskrypcje/{GUID}/resourceGroups/{Resource-Group-Name}/Microsoft. MachineLearningServices/Workspaces/{Workspace-Name}/Services/{service_name}. |
| `region` | (Opcjonalne dla [uwierzytelniania tokenu](#WhatSkillParametersToUse)). [Region](https://azure.microsoft.com/global-infrastructure/regions/) , w którym jest wdrożona usługa AML. |
| `timeout` | Obowiązkowe Gdy ta wartość jest określona, wskazuje limit czasu dla klienta http wykonującego wywołanie interfejsu API. Musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Na przykład `PT60S` przez 60 sekund. Jeśli nie zostanie ustawiona, zostanie wybrana wartość domyślna wynosząca 30 sekund. Limit czasu można ustawić na maksymalnie 230 sekund i co najmniej 1 sekundę. |
| `degreeOfParallelism` | Obowiązkowe Gdy ta wartość jest określona, wskazuje liczbę wywołań, które indeksator wykona równolegle do określonego punktu końcowego. Możesz zmniejszyć tę wartość, jeśli punkt końcowy kończy się niepowodzeniem z zbyt dużą liczbą żądań lub zgłoś go, jeśli punkt końcowy jest w stanie zaakceptować więcej żądań i chcesz zwiększyć wydajność indeksatora.  Jeśli nie zostanie ustawiona, zostanie użyta wartość domyślna 5. Dla degreeOfParallelism można ustawić maksymalnie 10 i co najmniej 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Jakie parametry umiejętności mają być używane

Które parametry umiejętności AML są wymagane, zależy od tego, jakiego uwierzytelniania używa Usługa AML (jeśli istnieje). Usługi AML Services oferują trzy opcje uwierzytelniania:

* [Uwierzytelnianie oparte na kluczach](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). Do uwierzytelniania żądań oceniania z poziomu umiejętności AML jest dostarczany klucz statyczny
  * Użyj _identyfikatora URI_ i parametrów _klucza_
* [Uwierzytelnianie oparte na tokenach](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). Usługa AML jest [wdrażana przy użyciu uwierzytelniania opartego na tokenach](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). [Zarządzana tożsamość](../active-directory/managed-identities-azure-resources/overview.md) usługi Wyszukiwanie poznawcze platformy Azure ma przyznane [rolę czytelnik](../machine-learning/how-to-assign-roles.md) w obszarze roboczym usługi AML. Umiejętność AML używa zarządzanej tożsamości usługi Azure Wyszukiwanie poznawcze do uwierzytelniania w usłudze AML, bez konieczności używania kluczy statycznych.
  * Użyj parametru _ResourceID_ .
  * Jeśli usługa Wyszukiwanie poznawcze platformy Azure znajduje się w innym regionie niż obszar roboczy AML, użyj parametru _region_ , aby ustawić region, w którym została wdrożona usługa AML.
* Brak uwierzytelniania. Do korzystania z usługi AML nie jest wymagane uwierzytelnianie
  * Użyj parametru _identyfikatora URI_

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

Brak "wstępnie zdefiniowanych" danych wejściowych dla tej umiejętności. Można wybrać jedno lub więcej pól, które będą już dostępne w momencie wykonywania tego umiejętności, ponieważ dane wejściowe i ładunek _JSON_ wysyłane do usługi AML będą mieć różne pola.

## <a name="skill-outputs"></a>Wyniki umiejętności

Brak "wstępnie zdefiniowanych" danych wyjściowych dla tej umiejętności. W zależności od odpowiedzi zwracanej przez usługę AML należy dodać pola wyjściowe, aby można je było pobrać z odpowiedzi _JSON_ .

## <a name="sample-definition"></a>Definicja Przykładowa

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Przykładowa wejściowa struktura JSON

Ta struktura _JSON_ reprezentuje ładunek, który zostanie wysłany do usługi AML. Pola najwyższego poziomu struktury będą odpowiadać nazwie "names" określonej w `inputs` sekcji definicji umiejętności. Wartości tych pól będą pochodzić z `source` tych pól (które mogą pochodzić z pola w dokumencie lub potencjalnie z innej umiejętności)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Przykładowa wyjściowa struktura JSON

Dane wyjściowe odpowiadają odpowiedzi zwróconej przez usługę AML. Usługa AML powinna zwracać tylko ładunek _JSON_ (zweryfikowany w `Content-Type` nagłówku odpowiedzi) i powinien być obiektem, w którym pola są wzbogacane pasujące do "names" w `output` i których wartość jest uważana za wzbogacanie.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Definicja przykładowego kształtowania wbudowanego

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Wbudowana struktura danych wejściowych JSON

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Przykładowa wyjściowa struktura JSON

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Przypadki błędów
Oprócz AML jest niedostępna lub nie można wysyłać kodów stanu o nieprawidłowej kondycji, następujące są uważane za błędne przypadki:

* Jeśli usługa AML zwraca kod stanu sukcesu, ale odpowiedź wskazuje, że nie `application/json` , wówczas odpowiedź jest uznawana za nieprawidłową i nie zostaną wykonane żadne wzbogacania.
* Jeśli usługa AML zwróci nieprawidłowy kod JSON

W przypadku gdy usługa AML jest niedostępna lub zwraca błąd HTTP, w historii wykonywania indeksatora zostanie dodany przyjazny błąd ze wszystkimi dostępnymi szczegółami dotyczącymi błędu HTTP.

## <a name="see-also"></a>Zobacz też

+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Rozwiązywanie problemów z usługą AML](../machine-learning/how-to-troubleshoot-deployment.md)