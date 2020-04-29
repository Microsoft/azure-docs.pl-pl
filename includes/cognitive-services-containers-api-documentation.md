---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "70034424"
---
## <a name="validate-that-a-container-is-running"></a>Sprawdzanie, czy kontener jest uruchomiony 

Istnieje kilka sposobów sprawdzenia, czy kontener jest uruchomiony. Znajdź *zewnętrzny adres IP* i uwidoczniony port danego kontenera, a następnie otwórz ulubioną przeglądarkę internetową. Użyj różnych adresów URL żądań poniżej, aby sprawdzić, czy kontener jest uruchomiony. Przykładowe adresy URL żądania wymienione poniżej to `http://localhost:5000`, ale konkretny kontener może się różnić. Pamiętaj, że polegasz na *zewnętrznym* adresie IP kontenera i uwidocznionym porcie.

| Adres URL żądania | Przeznaczenie |
|--|--|
| `http://localhost:5000/` | Kontener zawiera stronę główną. |
| `http://localhost:5000/status` | Zażądano przy użyciu protokołu HTTP GET, aby sprawdzić, czy kontener jest uruchomiony bez powodowania zapytania punktu końcowego. To żądanie może służyć do [sondowania na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)Kubernetes. |
| `http://localhost:5000/swagger` | Kontener zawiera pełen zestaw dokumentacji dla punktów końcowych i funkcję **Wypróbuj ją** . Korzystając z tej funkcji, można wprowadzić ustawienia w formie HTML opartej na sieci Web i utworzyć zapytanie bez konieczności pisania kodu. Po powrocie zapytania zostanie podane przykładowe ZWINIĘCIE polecenia, aby przedstawić nagłówki HTTP i format treści, który jest wymagany. |

![Strona główna kontenera](./media/cognitive-services-containers-api-documentation/container-webpage.png)
