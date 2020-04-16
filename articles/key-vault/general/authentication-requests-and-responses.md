---
title: Uwierzytelnianie, żądania i odpowiedzi
description: Uwierzytelnij się w uerce w celu korzystania z usługi Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 33e3bc13e67e268b82bf517033b4b1c7c51c361f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430892"
---
# <a name="authentication-requests-and-responses"></a>Uwierzytelnianie, żądania i odpowiedzi

Usługa Azure Key Vault obsługuje żądania i odpowiedzi sformatowane w formacie JSON. Żądania do usługi Azure Key Vault są kierowane do prawidłowego adresu URL usługi Azure Key Vault przy użyciu protokołu HTTPS z niektórymi parametrami adresu URL i encoded żądań i odpowiedzi w Usłudze JSON.

W tym temacie opisano szczegółowe informacje dotyczące usługi Azure Key Vault. Aby uzyskać ogólne informacje dotyczące korzystania z interfejsów usługi Azure REST, w tym uwierzytelniania/autoryzacji i sposobu uzyskiwania tokenu dostępu, zobacz Odwołanie interfejsu [API usługi Azure REST](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>Adres URL żądania  
 Operacje zarządzania kluczami używają operacji HTTP DELETE, GET, PATCH, PUT i HTTP POST, a operacje kryptograficzne w stosunku do istniejących kluczowych obiektów używają PROTOKOŁU HTTP POST. Klienci, którzy nie obsługują określonych zleceń HTTP, mogą również używać protokołu HTTP POST przy użyciu nagłówka X-HTTP-REQUEST w celu określenia zamierzonego zlecenia; żądania, które zwykle nie wymagają treści, powinny zawierać pustą treść podczas korzystania z protokołu HTTP POST, na przykład podczas korzystania z postu zamiast usuwania.  

 Aby pracować z obiektami w usłudze Azure Key Vault, poniżej przedstawiono przykładowe adresy URL:  

- Aby utworzyć klucz o nazwie TESTKEY w przechowalni kluczy —`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Aby zaimportować klucz o nazwie IMPORTEDKEY do magazynu kluczy —`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Aby uzyskać klucz tajny o nazwie MYSECRET w przechowalni kluczy -`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Aby podpisać skrót przy użyciu klucza o nazwie TESTKEY w umia dla magazynu kluczy -`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Upoważnienie do złożenia wniosku do Magazynu kluczy jest zawsze następujące,`https://{keyvault-name}.vault.azure.net/`  

  Klucze są zawsze przechowywane w /keys path, Wpisy tajne są zawsze przechowywane w /secrets path.  

## <a name="api-version"></a>Wersja interfejsu API  
 Usługa Azure Key Vault obsługuje przechowywanie wersji protokołu w celu zapewnienia zgodności z klientami na poziomie down,, chociaż nie wszystkie możliwości będą dostępne dla tych klientów. Klienci muszą `api-version` użyć parametru ciągu kwerendy, aby określić wersję protokołu, który obsługują, ponieważ nie ma wartości domyślnej.  

 Wersje protokołów usługi Azure Key Vault są zgodne ze schematem numerowania dat przy użyciu elementu {YYYY}. {MM}. {DD}.  

## <a name="request-body"></a>Treść żądania  
 Zgodnie ze specyfikacją HTTP operacje GET nie mogą mieć treści żądania, a operacje POST i PUT muszą mieć treść żądania. Treść w operacjach DELETE jest opcjonalna w http.  

 O ile w opisie operacji nie zaznaczono inaczej, typ zawartości treści żądania musi być application/json i musi zawierać szeregowaną konformanta obiektu JSON do typu zawartości.  

 O ile w opisie operacji nie zaznaczono inaczej, nagłówek Żądania zaakceptuj musi zawierać typ nośnika application/json.  

## <a name="response-body"></a>Organ odpowiedzi  
 O ile w opisie operacji nie zaznaczono inaczej, typ zawartości treści odpowiedzi zarówno udanych, jak i nieudanych operacji będzie application/json i zawiera szczegółowe informacje o błędzie.  

## <a name="using-http-post"></a>Korzystanie z protokołu HTTP POST  
 Niektórzy klienci mogą nie być w stanie używać niektórych zleceń HTTP, takich jak PATCH lub DELETE. Usługa Azure Key Vault obsługuje protokół HTTP POST jako alternatywę dla tych klientów, pod warunkiem że klient zawiera również nagłówek "X-HTTP-METHOD" do konkretnego oryginalnego zlecenia HTTP. Obsługa protokołu HTTP POST jest odnotowana dla każdego interfejsu API zdefiniowanego w tym dokumencie.  

## <a name="error-responses"></a>Odpowiedzi na błędy  
 Obsługa błędów będzie używać kodów stanu HTTP. Typowe wyniki to:  

- 2xx – Sukces: Używany do normalnej pracy. Organ reagowania będzie zawierał oczekiwany wynik  

- 3xx – Przekierowanie: 304 "Nie zmodyfikowany" może zostać zwrócony w celu spełnienia warunkowego GET. Inne kody 3xx mogą być używane w przyszłości do wskazywania zmian DNS i ścieżki.  

- 4xx - Błąd klienta: Używany do złych żądań, brakujących kluczy, błędów składni, nieprawidłowych parametrów, błędów uwierzytelniania itp. Treść odpowiedzi będzie zawierać szczegółowe wyjaśnienie błędu.  

- 5xx – Błąd serwera: Używany do wewnętrznych błędów serwera. Treść odpowiedzi będzie zawierać podsumowane informacje o błędzie.  

  System jest przeznaczony do pracy za serwerem proxy lub zaporą. W związku z tym klient może otrzymać inne kody błędów.  

  Usługa Azure Key Vault zwraca również informacje o błędzie w treści odpowiedzi, gdy wystąpi problem. Treść odpowiedzi jest sformatowana json i przyjmuje formę:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Authentication  
 Wszystkie żądania do usługi Azure Key Vault MUSZĄ być uwierzytelnione. Usługa Azure Key Vault obsługuje tokeny dostępu usługi Azure Active Directory, które można uzyskać przy użyciu usługi OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Aby uzyskać więcej informacji na temat rejestrowania aplikacji i uwierzytelniania w celu korzystania z usługi Azure Key Vault, zobacz [Rejestrowanie aplikacji klienckiej w usłudze Azure AD.](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad)
 
 Tokeny dostępu muszą być wysyłane do usługi przy użyciu nagłówka autoryzacji HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Gdy token dostępu nie zostanie dostarczony lub gdy token nie zostanie zaakceptowany przez usługę, błąd HTTP 401 zostanie zwrócony do klienta i będzie zawierał nagłówek WWW-Authenticate, na przykład:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry w nagłówku WWW-Authenticate to:  

-   autoryzacja: Adres usługi autoryzacji OAuth2, która może służyć do uzyskania tokenu dostępu dla żądania.  

-   zasób: nazwa zasobu (`https://vault.azure.net`) do użycia w żądaniu autoryzacji.  

