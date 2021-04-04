---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996466"
---
## <a name="validate-that-a-container-is-running"></a>Sprawdzanie, czy kontener jest uruchomiony 

Istnieje kilka sposobów sprawdzenia, czy kontener jest uruchomiony. Znajdź *zewnętrzny adres IP* i uwidoczniony port danego kontenera, a następnie otwórz ulubioną przeglądarkę internetową. Użyj różnych adresów URL żądań poniżej, aby sprawdzić, czy kontener jest uruchomiony. Przykładowe adresy URL żądania wymienione poniżej to `http://localhost:5000` , ale konkretny kontener może się różnić. Pamiętaj, że polegasz na *zewnętrznym* adresie IP kontenera i uwidocznionym porcie.

| Adres URL żądania | Przeznaczenie |
|--|--|
| `http://localhost:5000/` | Kontener zawiera stronę główną. |
| `http://localhost:5000/ready` | Zażądano przy użyciu GET, dzięki czemu można sprawdzić, czy kontener jest gotowy do akceptowania zapytania względem modelu.  To żądanie może służyć do [sondowania na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)Kubernetes. |
| `http://localhost:5000/status` | Zażądano również przy POBIERAniu, sprawdza, czy klucz API użyty do uruchomienia kontenera jest prawidłowy bez powodowania zapytania punktu końcowego. To żądanie może służyć do [sondowania na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)Kubernetes. |
| `http://localhost:5000/swagger` | Kontener zawiera pełen zestaw dokumentacji dla punktów końcowych oraz funkcję **Wypróbuj**. Korzystając z tej funkcji, można wprowadzić ustawienia w formie HTML opartej na sieci Web i utworzyć zapytanie bez konieczności pisania kodu. Po powrocie zapytania zostanie podane przykładowe ZWINIĘCIE polecenia, aby przedstawić nagłówki HTTP i format treści, który jest wymagany. |

![Strona główna kontenera](./media/cognitive-services-containers-api-documentation/container-webpage.png)
