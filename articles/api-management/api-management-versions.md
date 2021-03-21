---
title: Wersje na platformie Azure API Management | Microsoft Docs
description: Dowiedz się więcej na temat koncepcji wersji platformy Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 55951f288314d92cf5057e7d5c1e988f65cb3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040383"
---
# <a name="versions-in-azure-api-management"></a>Wersje na platformie Azure API Management

Wersje umożliwiają prezentowanie grup powiązanych interfejsów API deweloperom. Możesz użyć wersji, aby bezpiecznie obsługiwać istotne zmiany w interfejsie API. Klienci mogą korzystać z nowej wersji interfejsu API, gdy są gotowi, natomiast istniejący klienci nadal korzystają ze starszej wersji. Wersje są odróżniane za pomocą identyfikatora wersji (jest to dowolna wybrana wartość ciągu), a schemat przechowywania wersji umożliwia klientom identyfikację wersji interfejsu API, których chcą używać.

W większości przypadków każda wersja interfejsu API może być traktowana jako własny niezależny interfejs API. Dwie różne wersje interfejsu API mogą mieć różne zestawy operacji i różne zasady.

Z wersjami można:

- Publikowanie wielu wersji interfejsu API w tym samym czasie.
- Użyj ścieżki, ciągu zapytania lub nagłówka w celu rozróżnienia między wersjami.
- Użyj dowolnej wartości ciągu, która ma identyfikować swoją wersję, która może być liczbą, datą lub nazwą.
- Pokaż wersje interfejsu API pogrupowane w portalu dla deweloperów.
- Korzystanie z istniejącego interfejsu API (bez wersji) i tworzenie nowej wersji programu bez przerywania istniejących klientów.

[Rozpocznij pracę z wersjami, postępując zgodnie z naszym przewodnikiem.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Schematy obsługi wersji

Różne deweloperzy interfejsów API mają różne wymagania dotyczące obsługi wersji. Usługa Azure API Management nie nakazuje pojedynczemu podejściu do przechowywania wersji, ale udostępnia kilka opcji.

### <a name="path-based-versioning"></a>Przechowywanie wersji opartych na ścieżkach

Gdy jest używany schemat przechowywania wersji ścieżki, identyfikator wersji musi być uwzględniony w ścieżce URL dla dowolnych żądań interfejsu API.

Na przykład, `https://apis.contoso.com/products/v1` i `https://apis.contoso.com/products/v2` może odwoływać się do tego samego `products` interfejsu API, ale do wersji `v1` i `v2` odpowiednio.

Format adresu URL żądania interfejsu API w przypadku korzystania z wersji opartej na ścieżkach: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Przechowywanie wersji na podstawie nagłówka

Gdy jest używany schemat przechowywania wersji nagłówka, identyfikator wersji musi być uwzględniony w nagłówku żądania HTTP dla każdego żądania interfejsu API. Możesz określić nazwę nagłówka żądania HTTP.

Na przykład można utworzyć niestandardowy nagłówek o nazwie `Api-Version` , a klienci mogą określić lub wpisać `v1` `v2` wartość tego nagłówka.

### <a name="query-string-based-versioning"></a>Przechowywanie wersji na podstawie ciągu zapytania

Gdy jest używany schemat obsługi wersji ciągu zapytania, identyfikator wersji musi być uwzględniony w parametrze ciągu zapytania dla wszystkich żądań interfejsu API. Możesz określić nazwę parametru ciągu zapytania.

Format adresu URL żądania interfejsu API w przypadku korzystania z wersji opartej na ciągach zapytania: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

Na przykład, `https://apis.contoso.com/products?api-version=v1` i `https://apis.contoso.com/products/api-version=v2` może odwoływać się do tego samego `products` interfejsu API, ale do wersji `v1` i `v2` odpowiednio.

## <a name="original-versions"></a>Wersje oryginalne

W przypadku dodania wersji do interfejsu API bez wersji, `Original` wersja zostanie utworzona automatycznie i będzie odpowiadać na domyślny adres URL bez określonego identyfikatora wersji. `Original`Wersja zapewnia, że wszelkie istniejące obiekty wywołujące nie zostaną przerwane przez proces dodawania wersji. Jeśli tworzysz nowy interfejs API z wersjami włączonymi na początku, `Original` wersja nie zostanie utworzona.

## <a name="how-versions-are-represented"></a>Jak są reprezentowane wersje

Usługa Azure API Management obsługuje zasób o nazwie *zestaw wersji*, który reprezentuje zestaw wersji jednego logicznego interfejsu API. Zestaw wersji zawiera nazwę wyświetlaną interfejsu API z wersjami oraz [schemat przechowywania wersji służący](#versioning-schemes) do kierowania żądań do określonych wersji.

Każda wersja interfejsu API jest utrzymywana jako własny zasób interfejsu API, który następnie jest skojarzony z zestawem wersji. Zestaw wersji może zawierać interfejsy API z różnymi operacjami lub zasadami. Mogą wprowadzać znaczące zmiany między wersjami w zestawie.

Azure Portal tworzy zestawy wersji. Możesz zmodyfikować nazwę i opis wersji ustawionej w Azure Portal.

Zestawy wersji można wyświetlać i zarządzać nimi bezpośrednio przy użyciu interfejsu [wiersza polecenia platformy Azure](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), [szablonów Menedżer zasobów](/azure/templates/microsoft.apimanagement/service/apiversionsets)lub [Azure Resource Manager API](/rest/api/apimanagement/2020-06-01-preview/apiversionset).

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migrowanie interfejsu API bez wersji do interfejsu API z wersjami

W przypadku korzystania z Azure Portal w celu włączenia obsługi wersji na istniejącym interfejsie API w zasobach API Management są wprowadzane następujące zmiany:

 * Tworzony jest nowy zestaw wersji.
 * Istniejąca wersja jest utrzymywana i [skonfigurowana jako `Original` wersja interfejsu API](#original-versions). Interfejs API jest połączony z zestawem wersji, ale nie wymaga określenia identyfikatora wersji.
 * Nowa wersja zostanie utworzona jako nowy interfejs API i jest połączona z zestawem wersji. Do nowego interfejsu API należy uzyskać dostęp przy użyciu schematu i identyfikatora przechowywania wersji.

## <a name="versions-and-revisions"></a>Wersje i poprawki

Wersje i poprawki są odrębnymi funkcjami. Każda wersja może mieć wiele poprawek, podobnie jak interfejs API bez wersji. Można używać poprawek bez używania wersji lub w inny sposób. Zwykle wersje są używane do oddzielania wersji interfejsu API z istotnymi zmianami, podczas gdy poprawki mogą być używane dla drobnych i nieistotnych zmian w interfejsie API.

Należy sprawdzić, czy poprawka zawiera istotne zmiany, lub jeśli chcesz formalnie przekształcić poprawkę w wersję beta/testową, możesz utworzyć wersję z poprawki. Za pomocą Azure Portal kliknij pozycję "Utwórz wersję z poprawki" w menu kontekstowym poprawek na karcie poprawki.

## <a name="developer-portal"></a>Portal deweloperów

[Portal dla deweloperów](./api-management-howto-developer-portal.md) osobno wymienia poszczególne wersje interfejsu API.

![Portal deweloperów API Management wyświetlający listę interfejsów API z wersjami](media/api-management-versions/portal-list.png)

Szczegółowe informacje o interfejsie API zawierają również listę wszystkich wersji tego interfejsu API. `Original`Wersja jest wyświetlana bez identyfikatora wersji.

![Portal deweloperów API Management zawierający szczegóły interfejsu API i listę wersji tego interfejsu API](media/api-management-versions/portal-details.png)

> [!TIP]
> Wersje interfejsu API należy dodać do produktu, zanim będą widoczne w portalu dla deweloperów.
