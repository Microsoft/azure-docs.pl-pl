---
title: Skonfiguruj współużytkowanie zasobów między źródłami w interfejsie API platformy Azure dla usługi FHIR
description: W tym artykule opisano, jak skonfigurować udostępnianie zasobów między źródłami w usłudze Azure API for FHIR.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871750"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Skonfiguruj współużytkowanie zasobów między źródłami w interfejsie API platformy Azure dla usługi FHIR

Interfejs API platformy Azure dla zasobów współdziałania w ramach usług opieki zdrowotnej (FHIR) obsługuje [udostępnianie zasobów między źródłami (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). Mechanizm CORS umożliwia skonfigurowanie ustawień tak, aby aplikacje z jednej domeny (pochodzenia) mogły uzyskiwać dostęp do zasobów z innej domeny, znanej jako żądanie międzydomenowe.

Mechanizm CORS jest często używany w jednostronicowej aplikacji, która musi wywołać interfejs API RESTful w innej domenie.

Aby skonfigurować ustawienie CORS w interfejsie API platformy Azure dla usługi FHIR, określ następujące ustawienia:

- **Źródła (dostęp-kontrola-Zezwól-Źródło)**. Lista domen, które mogą wprowadzać żądania między źródłami do interfejsu API platformy Azure dla FHIR. Każda domena (pierwotna) musi być wprowadzona w osobnym wierszu. Możesz wprowadzić gwiazdkę (*), aby zezwolić na wywołania z dowolnej domeny, ale nie jest to zalecane, ponieważ jest to zagrożenie bezpieczeństwa.

- **Nagłówki (Access-Control-Allow-Headers)**. Lista nagłówków, które zawiera żądanie pochodzenia. Aby zezwolić na wszystkie nagłówki, Wprowadź gwiazdkę (*).

- **Metody (Access-Control-Allow-Methods)**. Dozwolone metody (PUT, GET, POST itd.) w wywołaniu interfejsu API. Wybierz **pozycję Zaznacz wszystko** dla wszystkich metod.

- **Maksymalny wiek (kontrola dostępu — maks. wiek)**. Wartość w sekundach, w której mają być buforowane wyniki żądania wstępnego inspekcji w celu zapewnienia dostępu do-i---------i kontroli dostępu.

- **Zezwalaj na poświadczenia (dostęp-kontrola-Zezwalaj-poświadczenia)**. Żądania CORS zwykle nie uwzględniają plików cookie, aby zapobiec atakom [CSRFym między witrynami](https://en.wikipedia.org/wiki/Cross-site_request_forgery) . W przypadku wybrania tego ustawienia żądanie może zawierać poświadczenia, takie jak pliki cookie. Nie można skonfigurować tego ustawienia, jeśli pochodzenie zostało już ustawione przy użyciu gwiazdki (*).

![Ustawienia funkcji udostępniania zasobów między źródłami (CORS)](media/cors/cors.png)

>[!NOTE]
>Nie można określić różnych ustawień dla różnych źródeł domeny. Wszystkie ustawienia (**nagłówki**, **metody**, **Maksymalny wiek**i **Zezwalaj na poświadczenia**) stosują się do wszystkich źródeł określonych w ustawieniu źródła.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania udostępniania między źródłami w usłudze Azure API for FHIR. Następnie wdróż w pełni zarządzany interfejs API platformy Azure dla usługi FHIR:
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)