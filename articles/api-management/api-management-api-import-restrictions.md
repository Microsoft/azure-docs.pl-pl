---
title: Ograniczenia i szczegóły obsługi formatów interfejsu API
titleSuffix: Azure API Management
description: Szczegóły znanych problemów i ograniczeń dotyczących obsługi formatów Open API, WSDL i WADL w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932638"
---
# <a name="api-import-restrictions-and-known-issues"></a>Ograniczenia importu interfejsu API i znane problemy

## <a name="about-this-list"></a>Informacje o tej liście

Podczas importowania interfejsu API mogą występować pewne ograniczenia lub zidentyfikować problemy, które należy skorygować przed pomyślnym zaimportowaniem. W tym artykule opisano te ograniczenia uporządkowane według formatu importu interfejsu API. Opisano w nim również, jak działa eksport OpenAPI.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Ograniczenia importu openapi/Swagger

Jeśli otrzymujesz błędy podczas importowania dokumentu OpenAPI, upewnij się, że został on wcześniej sprawdzony. Można to zrobić przy użyciu narzędzia Projektant w Azure Portal (Edytor specyfikacji OpenAPI frontonu) lub za pomocą narzędzi innych firm, takich jak <a href="https://editor.swagger.io">Edytor Swagger</a>.

### <a name="general"></a><a name="open-api-general"> </a>Ogólne

-   Wymagane parametry w ścieżce i zapytaniu muszą mieć unikatowe nazwy. (W OpenAPI Nazwa parametru musi być unikatowa w obrębie lokalizacji, na przykład Path, Query, header. Jednakże w API Management zezwalamy na rozróżnienie operacji przy użyciu parametrów Path i Query (które nie są obsługiwane przez OpenAPI). Dlatego wymagamy, aby nazwy parametrów były unikatowe w ramach całego szablonu adresu URL.
-   `\$ref` wskaźniki nie mogą odwoływać się do zewnętrznych plików.
-   `x-ms-paths` i `x-servers` są jedynymi obsługiwanymi rozszerzeniami.
-   Rozszerzenia niestandardowe są ignorowane podczas importowania i nie są zapisywane ani zachowywane do eksportu.
-   `Recursion` -API Management nie obsługuje definicji zdefiniowanych cyklicznie (na przykład schematów odwołujących się do siebie).
-   Adres URL pliku źródłowego (jeśli jest dostępny) jest stosowany do względnych adresów URL serwera.
-   Definicje zabezpieczeń zostały zignorowane.
-   Wbudowane definicje schematu dla operacji interfejsu API nie są obsługiwane. Definicje schematu są zdefiniowane w zakresie interfejsu API i można do nich odwoływać się w zakresach żądań lub odpowiedzi operacji interfejsu API.
-   Określony parametr adresu URL musi być częścią szablonu adresu URL.
-   `server` Obiekt nie jest obsługiwany na poziomie operacji interfejsu API.
-   `Produces` słowo kluczowe, które opisuje typy MIME zwracane przez interfejs API, nie jest obsługiwane. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>Openapi wersja 2

-   Obsługiwany jest tylko format JSON.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>Openapi wersja 3

-   Jeśli `servers` określono wiele, API Management spróbuje wybrać pierwszy adres URL https. Jeśli nie ma żadnych adresów URL HTTPs — pierwszy adres URL protokołu HTTP. Jeśli nie ma adresów URL protokołu HTTP, adres URL serwera będzie pusty.
-   `Examples` nie jest obsługiwana, ale `example` jest.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI mechanizmy importowania, aktualizowania i eksportowania

### <a name="general"></a><a name="open-import-export-general"> </a>Ogólne

-   Definicje interfejsu API wyeksportowane z usługi API Management są przeznaczone głównie dla aplikacji zewnętrznych dla API Management usługi, które wymagają wywołania interfejsu API hostowanego w usłudze API Management. Definicje wyeksportowanych interfejsów API nie są przeznaczone do zaimportowania do tej samej lub innej usługi API Management. Aby zarządzać konfiguracją interfejsu API defiitions w różnych usługach/envionments, zapoznaj się z dokumentacją dotyczącą korzystania z usługi API Management z usługą git. 

### <a name="add-new-api-via-openapi-import"></a>Dodawanie nowego interfejsu API za pomocą importu OpenAPI

Dla każdej operacji znalezionej w dokumencie OpenAPI zostanie utworzona nowa operacja z nazwą zasobu platformy Azure, a nazwa wyświetlana zostanie ustawiona na `operationId` `summary` odpowiednio. `operationId` wartość jest znormalizowana po zasadach opisanych poniżej. `summary` wartość jest zaimportowana jako-is, a jej długość jest ograniczona do 300 znaków.

Jeśli `operationId` nie zostanie określony (oznacza to, że nie jest obecny, `null` lub pusty), wartość nazwy zasobu platformy Azure zostanie wygenerowana przez połączenie metody http i szablonu ścieżki, na przykład `get-foo` .

Jeśli `summary` nie zostanie określony (to oznacza, że nie jest obecny, `null` lub pusty), `display name` wartość zostanie ustawiona na `operationId` . Jeśli `operationId` nie jest określony, wyświetlana wartość nazwy będzie generowana przez połączenie metody http i szablonu ścieżki, na przykład `Get - /foo` .

### <a name="update-an-existing-api-via-openapi-import"></a>Aktualizowanie istniejącego interfejsu API za pomocą importu OpenAPI

Podczas importowania istniejący interfejs API jest zmieniany na zgodny z interfejsem API opisanym w dokumencie OpenAPI. Każda operacja w dokumencie OpenAPI jest dopasowywana do istniejącej operacji, porównując jej `operationId` wartość z nazwą zasobu platformy Azure istniejącej operacji.

W przypadku znalezienia dopasowania właściwości istniejącej operacji zostaną zaktualizowane "w miejscu".

Jeśli dopasowanie nie zostanie znalezione, Nowa operacja zostanie utworzona przy użyciu reguł opisanych w powyższej sekcji. Dla każdej nowej operacji import podejmie próbę skopiowania zasad z istniejącej operacji z tą samą metodą HTTP i szablonem ścieżki.

Wszystkie istniejące niedopasowane operacje zostaną usunięte.

Aby zaimportować bardziej przewidywalne instrukcje, postępuj zgodnie z następującymi wskazówkami:

- Upewnij się, że określono `operationId` Właściwość dla każdej operacji.
- Nie należy zmieniać `operationId` po początkowym imporcie.
- `operationId`W tym samym czasie nigdy nie zmieniaj metody ani szablonu ścieżki.

### <a name="export-api-as-openapi"></a>Eksportowanie interfejsu API jako OpenAPI

Dla każdej operacji nazwa zasobu platformy Azure zostanie wyeksportowana jako `operationId` , a nazwa wyświetlana zostanie wyeksportowana jako `summary` .
Reguły normalizacji dla operationId

- Konwertuj na małe litery.
- Zastąp każdą sekwencję znaków innych niż alfanumeryczne pojedynczą kreską, na przykład, `GET-/foo/{bar}?buzz={quix}` zostanie przekształcona w `get-foo-bar-buzz-quix-` .
- Przycinanie łączników na obu stronach, na przykład, `get-foo-bar-buzz-quix-` stanie się `get-foo-bar-buzz-quix`
- Obcinaj do 76 znaków, cztery znaki poniżej maksymalnego limitu dla nazwy zasobu.
- Użyj pozostałych czterech znaków dla sufiksu deduplikacji, w razie potrzeby, w postaci `-1, -2, ..., -999` .


## <a name="wsdl"></a><a name="wsdl"> </a>Język WSDL

Pliki WSDL są używane do tworzenia protokołu SOAP przekazującego i interfejsów API protokołu SOAP-to-REST.

-   **Powiązania SOAP** — obsługiwane są tylko powiązania SOAP ze stylem "Document" i "literal". Nie ma obsługi stylu "RPC" ani kodowania SOAP.
-   **WSDL: import** — ten atrybut nie jest obsługiwany. Klienci powinni scalić Importy do jednego dokumentu.
-   **Komunikaty z wieloma częściami** — te typy komunikatów nie są obsługiwane.
-   **WCF WSHttpBinding** — usługa SOAP utworzona przy użyciu Windows Communication Foundation powinna używać BasicHttpBinding-WSHttpBinding nie jest obsługiwana.
-   **MTOM** — usługi korzystające z mechanizmu MTOM <em>mogą</em> funkcjonować. Oficjalne wsparcie nie jest w tej chwili oferowane.
-   **Rekursja** — typy, które są zdefiniowane cyklicznie (na przykład odnoszą się do tablicy samej) nie są obsługiwane przez APIM.
-   **Wiele przestrzeni nazw** — w schemacie można używać wielu przestrzeni nazw, ale tylko docelowa przestrzeń nazw może służyć do definiowania części komunikatów. Obszary nazw inne niż element docelowy, które są używane do definiowania innych elementów wejściowych lub wyjściowych, nie są zachowywane. Mimo że ten dokument WSDL można zaimportować, w obszarze Eksportuj wszystkie części wiadomości będzie miał docelowy obszar nazw WSDL.
-   **Tablice** — transformacja protokołu SOAP do REST obsługuje tylko opakowane tablice przedstawione w poniższym przykładzie:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Obecnie nie ma żadnych znanych problemów z importem WADL.
