---
title: Dostęp do obiektów blob magazynu przy użyciu Azure CDN domeny niestandardowej za pośrednictwem protokołu HTTPS
description: Dowiedz się, jak dodać domenę niestandardową Azure CDN i włączyć protokół HTTPS w tej domenie dla niestandardowego punktu końcowego magazynu obiektów BLOB.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 5b6fe2b2704f101a7775b7eb700375105b0a9eca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81259888"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Samouczek: uzyskiwanie dostępu do obiektów blob magazynu w domenie niestandardowej usługi Azure CDN za pośrednictwem protokołu HTTPS

Po zintegrowaniu konta magazynu platformy Azure z usługą Azure Content Delivery Network (CDN) możesz dodać domenę niestandardową i włączyć protokół HTTPS na tej domenie dla niestandardowego punktu końcowego magazynu obiektów blob. 

## <a name="prerequisites"></a>Wymagania wstępne

Zanim będzie możliwe wykonanie kroków opisanych w tym samouczku, najpierw należy zintegrować konto magazynu platformy Azure z usługą Azure CDN. Aby uzyskać więcej informacji, zobacz [Szybki start: integrowanie konta magazynu platformy Azure z usługą Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Dodawanie domeny niestandardowej
Po utworzeniu punktu końcowego usługi CDN w profilu nazwa punktu końcowego, czyli domena podrzędna domeny azureedge.net, jest uwzględniana w adresie URL w celu domyślnego dostarczania zawartości usługi CDN. Dostępna jest także opcja kojarzenia domeny niestandardowej z punktem końcowym usługi CDN. Ta opcja umożliwia dostarczanie zawartości przy użyciu domeny niestandardowej w adresie URL zamiast korzystania z nazwy punktu końcowego. Aby dodać domenę niestandardową do punktu końcowego, postępuj zgodnie z instrukcjami w tym samouczku: [Dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Konfigurowanie protokołu HTTPS
Korzystając z protokołu HTTPS w domenie niestandardowej masz pewność, że dane są przesyłane przez Internet w bezpieczny sposób przy użyciu szyfrowania TLS/SSL. Gdy przeglądarka internetowa łączy się z witryną internetową za pośrednictwem protokołu HTTPS, sprawdza, czy certyfikat zabezpieczeń witryny internetowej jest poprawny i czy został wystawiony przez autentyczny urząd certyfikacji. Aby skonfigurować protokół HTTPS w domenie niestandardowej, postępuj zgodnie z instrukcjami w tym samouczku: [Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego
Jeśli konfiguracja punktu końcowego magazynu obiektów blob nie zezwala na anonimowy dostęp w trybie odczytu, należy podać token [sygnatury dostępu współdzielonego (SAS)](cdn-sas-storage-support.md) w każdym żądaniu wysłanym do domeny niestandardowej. Domyślnie punkty końcowe magazynu obiektów blob nie zezwalają na anonimowy dostęp w trybie odczytu. Aby uzyskać więcej informacji o sygnaturze SAS, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../storage/blobs/storage-manage-access-to-resources.md).

Usługa Azure CDN ignoruje wszelkie ograniczenia dodane do tokenu SAS. Na przykład wszystkie tokeny SAS mają czas wygaśnięcia, co oznacza, że wciąż można uzyskać dostęp do zawartości przy użyciu wygasłej sygnatury SAS do momentu wyczyszczenia zawartości z serwerów punktu obecności (POP) usługi CDN. Możesz kontrolować, jak długo dane są buforowane w usłudze Azure CDN, ustawiając nagłówek odpowiedzi pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Zarządzanie wygasaniem obiektów blob usługi Azure Storage w usłudze Azure CDN](cdn-manage-expiration-of-blob-content.md).

Jeśli tworzysz wiele adresów URL sygnatur SAS dla tego samego punktu końcowego obiektu blob, rozważ włączenie buforowania ciągu zapytania. Daje to gwarancję, że każdy adres URL jest traktowany jako unikatowy obiekt. Aby uzyskać więcej informacji, zobacz [Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Przekierowywanie protokołu HTTP do HTTPS
Można wybrać przekierowywanie ruchu HTTP do protokołu HTTPS przez utworzenie reguły przekierowywania adresu URL za pomocą [aparatu reguł standardowych](cdn-standard-rules-engine.md) lub [aparatu reguł Verizon Premium](cdn-verizon-premium-rules-engine.md). Aparat reguł standardowych jest dostępny tylko dla Azure CDN z profilów firmy Microsoft, podczas gdy aparat reguł warstwy Premium jest dostępny tylko z poziomu Azure CDN profilów Premium.

![Reguła przekierowania firmy Microsoft](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

W powyższej regule pozostawienie nazwy hosta, ścieżki, ciągu zapytania i fragmentu spowoduje, że wartości przychodzące są używane w przekierowaniu. 

![Reguła przekierowania Verizon](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

W powyższej regule usługa *CDN-Endpoint-Name* odnosi się do nazwy skonfigurowanej dla punktu końcowego usługi CDN, którą można wybrać z listy rozwijanej. Wartość właściwości *origin-path* odnosi się do ścieżki w ramach konta magazynu źródła, w którym znajduje się zawartość statyczna. Jeśli hostujesz całą zawartość statyczną w jednym kontenerze, zastąp właściwość *origin-path* nazwą tego kontenera.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia
Gdy uzyskujesz dostęp do obiektów blob za pomocą usługi Azure CDN, opłaty są naliczane według [cen magazynu obiektów blob](https://azure.microsoft.com/pricing/details/storage/blobs/) za ruch między serwerami POP a źródłem (magazynem obiektów blob) oraz według [cennika usługi Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) za dane, do których uzyskano dostęp z serwerów POP.

Jeśli na przykład masz konto magazynu w Stanach Zjednoczonych, do którego dostęp jest uzyskiwany przy użyciu usługi Azure CDN, a ktoś w Europie spróbuje uzyskać dostęp do jednego z obiektów blob w tym koncie magazynu za pośrednictwem usługi Azure CDN, usługa Azure CDN najpierw sprawdzi punkt POP położony najbliżej Europy w poszukiwaniu tego obiektu blob. Jeśli zostanie znaleziony, usługa Azure CDN uzyska dostęp do kopii tego obiektu blob i zastosuje cennik usługi CDN, ponieważ dostęp będzie uzyskiwany w usłudze Azure CDN. Jeśli nie zostanie znaleziony, usługa Azure CDN skopiuje obiekt blob do serwera POP, co spowoduje wystąpienie ruchu wychodzącego i naliczenie opłat za transakcje zgodnie z cennikiem magazynu obiektów blob, a następnie uzyska dostęp do pliku na serwerze POP, co spowoduje naliczenie opłat za usługę Azure CDN.

## <a name="next-steps"></a>Następne kroki
[Samouczek: ustawianie reguł buforowania usługi Azure CDN](cdn-caching-rules-tutorial.md)




