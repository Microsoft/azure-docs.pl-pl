---
title: Zrozumienie i rozwiązywanie problemów dotyczących platformy Azure serwer proxy aplikacji usługi Azure AD CORS
description: Zawiera informacje o funkcji CORS w usłudze Azure serwer proxy aplikacji usługi Azure AD i sposobach identyfikowania i rozwiązywania problemów z mechanizmem CORS.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2019802725e36c2400f57952fedf7af40877c8c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84759933"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Zrozumienie i rozwiązywanie problemów serwer proxy aplikacji usługi Azure Active Directory CORS

[Współużytkowanie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/)   Czasami mogą wystąpić wyzwania dla aplikacji i interfejsów API publikowanych za pomocą serwer proxy aplikacji usługi Azure Active Directory. W tym artykule omówiono zagadnienia i rozwiązania CORS serwer proxy aplikacji usługi Azure AD platformy Azure.

Zabezpieczenia przeglądarki zazwyczaj uniemożliwiają stronom sieci Web wykonywanie żądań AJAX do innej domeny. To ograniczenie jest nazywane *zasadami tego samego źródła*i uniemożliwia złośliwym lokacjom odczytywanie poufnych danych z innej lokacji. Czasami jednak może być konieczne, aby inne Lokacje wywoływały internetowy interfejs API. CORS to W3C standard, który umożliwia serwerowi złagodzenie zasad tego samego źródła i Zezwalanie na niektóre żądania między źródłami podczas odrzucania innych.

## <a name="understand-and-identify-cors-issues"></a>Zrozumienie i identyfikowanie problemów CORS

Dwa adresy URL mają te same źródła, jeśli mają identyczne schematy, hosty i porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)), takie jak:

-   http: \/ /contoso.com/foo.html
-   http: \/ /contoso.com/bar.html

Następujące adresy URL mają różne źródła niż poprzednie dwa:

-   http: \/ /contoso.NET — inna domena
-   http: \/ /contoso.com:9000/foo.html — inny port
-   https: \/ /contoso.com/foo.html — inny schemat
-   http: \/ /www.contoso.com/foo.html — inna poddomena

Zasady tego samego źródła uniemożliwiają aplikacjom uzyskiwanie dostępu do zasobów z innych źródeł, chyba że korzystają z prawidłowych nagłówków kontroli dostępu. Jeśli nagłówki CORS są nieobecne lub nieprawidłowe, żądania między źródłami kończą się niepowodzeniem. 

Problemy CORS można zidentyfikować za pomocą narzędzi debugowania przeglądarki:

1. Uruchom przeglądarkę i przejdź do aplikacji sieci Web.
1. Naciśnij klawisz **F12** , aby wyświetlić konsolę debugowania.
1. Spróbuj odtworzyć transakcję i przejrzyj komunikat konsoli. Naruszenie mechanizmu CORS powoduje błąd konsoli dotyczącej źródła.

Na poniższym zrzucie ekranu, wybierając przycisk **Wypróbuj ten** komunikat o błędzie, że nie znaleziono protokołu https: \/ /corswebclient-contoso.msappproxy.NET w nagłówku Access-Control-Allow-Origin.

![Problem CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Wyzwania mechanizmu CORS z serwerem proxy aplikacji

W poniższym przykładzie przedstawiono typowy scenariusz CORS serwer proxy aplikacji usługi Azure AD platformy Azure. Wewnętrzny serwer obsługuje kontroler internetowego interfejsu API **CORSWebService** oraz **CORSWebClient** , który wywołuje **CORSWebService**. Istnieje żądanie AJAX od **CORSWebClient** do **CORSWebService**.

![Lokalne żądanie tego samego źródła](./media/application-proxy-understand-cors-issues/image1.png)

Aplikacja CORSWebClient działa w przypadku hostowania jej w środowisku lokalnym, ale nie może ładować ani wyłączać błędów podczas publikowania za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD. Jeśli aplikacje CORSWebClient i CORSWebService są publikowane osobno jako różne aplikacje za pomocą serwera proxy aplikacji, te dwie aplikacje są hostowane w różnych domenach. Żądanie AJAX od CORSWebClient do CORSWebService jest żądaniem między źródłami i kończy się niepowodzeniem.

![Żądanie CORS serwera proxy aplikacji](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Rozwiązania dotyczące problemów z serwerem proxy aplikacji

Poprzedni problem z modelem CORS można rozwiązać na jeden z kilku sposobów.

### <a name="option-1-set-up-a-custom-domain"></a>Opcja 1: Skonfiguruj domenę niestandardową

Użyj [domeny niestandardowej](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) serwer proxy aplikacji usługi Azure AD platformy Azure do publikowania z tego samego źródła, bez konieczności wprowadzania zmian w źródłach aplikacji, kodzie lub nagłówkach. 

### <a name="option-2-publish-the-parent-directory"></a>Opcja 2: publikowanie katalogu nadrzędnego

Opublikuj katalog nadrzędny obu aplikacji. To rozwiązanie działa szczególnie w przypadku, gdy na serwerze sieci Web znajdują się tylko dwie aplikacje. Zamiast publikować poszczególne aplikacje oddzielnie, można opublikować wspólny katalog nadrzędny, co skutkuje tym samym źródłem.

W poniższych przykładach przedstawiono stronę serwer proxy aplikacji usługi Azure AD platformy Azure dla aplikacji CORSWebClient.  Gdy **wewnętrzny adres URL** jest ustawiony na *contoso.com/CORSWebClient*, aplikacja nie może wykonać pomyślnych żądań do katalogu *contoso.com/CORSWebService* , ponieważ są to między innymi źródłami. 

![Publikowanie aplikacji indywidualnie](./media/application-proxy-understand-cors-issues/image4.png)

Zamiast tego należy ustawić **wewnętrzny adres URL** w celu opublikowania katalogu nadrzędnego, który zawiera katalogi *CORSWebClient* i *CORSWebService* :

![Publikowanie katalogu nadrzędnego](./media/application-proxy-understand-cors-issues/image5.png)

W efekcie adresy URL aplikacji skutecznie rozwiązują problem z CORS:

- https: \/ /corswebclient-contoso.msappproxy.NET/CORSWebService
- https: \/ /corswebclient-contoso.msappproxy.NET/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opcja 3: aktualizowanie nagłówków HTTP

Dodaj niestandardowy nagłówek odpowiedzi HTTP w usłudze sieci Web, aby dopasować żądanie pierwotne. W przypadku witryn sieci Web działających w Internet Information Services (IIS) należy zmodyfikować nagłówek przy użyciu Menedżera usług IIS:

![Dodawanie niestandardowego nagłówka odpowiedzi w Menedżerze usług IIS](./media/application-proxy-understand-cors-issues/image6.png)

Ta modyfikacja nie wymaga żadnych zmian w kodzie. Można to sprawdzić w śladach programu Fiddler:

**Dodawanie nagłówka**\
HTTP/1.1 200 OK \
Cache-Control: Brak pamięci podręcznej \
Pragma: Brak pamięci podręcznej \
Content-Type: text/zwykły; charset = utf-8 \
Wygasa:-1 \
Zróżnicuj: Accept-Encoding \
Serwer: Microsoft-IIS/8,5 Microsoft-pliku HTTPAPI/2.0 \
**Access-Control-Allow-Origin: https \: //corswebclient-contoso.msappproxy.NET**\
X-AspNet-Version: 4.0.30319 \
X-zasilany przez: ASP.NET \
Długość zawartości: 17

### <a name="option-4-modify-the-app"></a>Opcja 4: modyfikowanie aplikacji

Możesz zmienić aplikację, aby obsługiwała mechanizm CORS poprzez dodanie nagłówka Access-Control-Allow-Origin z odpowiednimi wartościami. Sposób dodawania nagłówka zależy od języka kodu aplikacji. Zmiana kodu jest najmniejszą zalecaną opcją, ponieważ wymaga najwięcej wysiłku.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opcja 5: zwiększenie okresu istnienia tokenu dostępu

Nie można rozwiązać niektórych problemów CORS, na przykład gdy aplikacja przekieruje się do *login.microsoftonline.com* w celu uwierzytelnienia, a token dostępu wygasa. Wywołanie CORS nie powiedzie się. Obejście tego scenariusza polega na przekroczeniu okresu istnienia tokenu dostępu, aby zapobiec jego wygaśnięciu podczas sesji użytkownika. Aby uzyskać więcej informacji o tym, jak to zrobić, zobacz [konfigurowalne okresy istnienia tokenu w usłudze Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Zobacz też
- [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planowanie wdrożenia serwera proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md) 
- [Dostęp zdalny do aplikacji lokalnych za serwer proxy aplikacji usługi Azure Active Directory](application-proxy.md) 
