---
title: Skonfiguruj warunki zgodności zestawu reguł Standard/Premium platformy Azure
description: Ten artykuł zawiera listę różnych warunków dopasowania dostępnych w zestawie reguł Standard/Premium platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: yuajia
ms.openlocfilehash: 039effb885463c1c53085535a6980601be890340
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561515"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Warunki dopasowania zestawu reguł (wersja zapoznawcza) usługi Azure Front-Standard/Premium

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W [zestawie reguł](concept-rule-set.md)Standard/Premium platformy Azure w warstwie Standardowa reguła składa się z co najmniej zero warunków dopasowania i akcji. Ten artykuł zawiera szczegółowe opisy warunków dopasowania, których można użyć w zestawie reguł Standard/Premium platformy Azure.

Pierwszą częścią reguły jest warunek dopasowania lub zestaw warunków zgodności. Reguła może składać się z maksymalnie 10 warunków dopasowywania. Warunek dopasowania służy do identyfikowania określonych typów żądań, dla których wykonywane są określone akcje. Jeśli używasz wielu warunków dopasowywania, warunki dopasowania są pogrupowane przy użyciu i logiki. Dla wszystkich warunków dopasowania, które obsługują wiele wartości, lub jest używana logika.

Warunku dopasowywania można użyć do:

* Filtrowanie żądań na podstawie określonego adresu IP, kraju lub regionu.
* Filtruj żądania według informacji nagłówka.
* Filtrowanie żądań z urządzeń przenośnych lub urządzeń stacjonarnych.
* Filtrowanie żądań z nazwy pliku żądania i rozszerzenia pliku.
* Filtrowanie żądań z adresu URL żądania, protokołu, ścieżki, ciągu zapytania, post args itd.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Typ urządzenia

Warunek dopasowania **typu urządzenia** służy do identyfikowania żądań, które zostały wykonane z urządzenia przenośnego lub urządzenia stacjonarnego.  

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-------|------------------|
| Operator | <ul><li>W Azure Portal: `Equal` , `Not Equal`</li><li>W szablonach ARM: `Equal` ; Użyj `negateCondition` właściwości, aby określić _nie równa_ się |
| Wartość | `Mobile`, `Desktop` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, które zostały wykryte jako pochodzące z urządzenia przenośnego.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania typu urządzenia.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Ogłoś argumenty

Warunek zgodności **post args** służy do identyfikowania żądań na podstawie argumentów dostarczonych w treści żądania post. Jeden warunek dopasowania pasuje do pojedynczego argumentu z treści żądania POST. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

> [!NOTE]
> Warunek dopasowania **post args** działa z `application/x-www-form-urlencoded` typem zawartości.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Ogłoś argumenty | Wartość ciągu reprezentująca nazwę argumentu POST. |
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Wartość | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość argumentu POST do dopasowania. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie zgadzamy się na wszystkie żądania POST `customerName` , w których argument jest podany w treści żądania i gdzie wartość zaczyna się od `customerName` litery `J` lub `K` . Użyjemy transformacji przypadku, aby przekonwertować wartości wejściowe na wielkie litery, tak aby wartości zaczynające się od `J` ,, `j` `K` i `k` są zgodne.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania wpisów.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Ciąg zapytania

Warunek dopasowania **ciągu zapytania** służy do identyfikowania żądań zawierających konkretny ciąg zapytania. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

> [!NOTE]
> Cały ciąg zapytania jest dopasowywany do pojedynczego ciągu, bez wiodącego `?` .

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Ciąg zapytania | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość ciągu zapytania do dopasowania. Nie dołączaj `?` na początku ciągu zapytania. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w których ciąg zapytania zawiera ciąg `language=en-US` . Chcemy, aby warunek dopasowania miał wielkość liter, więc nie przekształcamy wielkości liter.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania ciągu zapytania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Adres zdalny

Warunek zgodności **adresów zdalnych** identyfikuje żądania na podstawie lokalizacji lub adresu IP osoby żądającej. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

* Użyj notacji CIDR podczas określania bloków adresów IP. Oznacza to, że składnia bloku adresów IP to podstawowy adres IP, po którym następuje ukośnik i rozmiar prefiksu. Na przykład:
    * **Przykład IPv4**: `5.5.5.64/26` dopasowuje wszystkie żądania odbierane z adresów 5.5.5.64 za pośrednictwem 5.5.5.127.
    * **Przykład IPv6**: `1:2:3:/48` dopasowuje wszystkie żądania przychodzące z adresów od 1:2:3:0:0:0:0:0 do 1:2:3: FFFF: FFFF: FFFF: FFFF: FFFF.
* Po określeniu wielu adresów IP i bloków adresów IP stosowana jest logika "OR".
    * **Przykład IPv4**: Jeśli dodasz dwa adresy IP `1.2.3.4` `10.20.30.40` , warunek jest dopasowywany dla wszystkich żądań, które dotarły do adresu 1.2.3.4 lub 10.20.30.40.
    * **Przykład IPv6**: Jeśli dodasz dwa adresy IP `1:2:3:4:5:6:7:8` `10:20:30:40:50:60:70:80` , warunek jest dopasowywany dla wszystkich żądań, które dotarły do adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | <ul><li>W Azure Portal: `Geo Match` , `Geo Not Match` , `IP Match` lub `IP Not Match`</li><li>W szablonach ARM: `GeoMatch` , `IPMatch` ; Użyj `negateCondition` właściwości, aby określić _niezgodność geograficzną_ lub _niezgodność adresu IP_</li></ul> |
| Wartość | <ul><li>Dla `IP Match` operatorów or `IP Not Match` : Określ jeden lub więcej zakresów adresów IP. Jeśli określono wiele zakresów adresów IP, są one oceniane przy użyciu lub logiki.</li><li>Dla `Geo Match` operatorów or `Geo Not Match` : Określ co najmniej jedną lokalizację przy użyciu kodu kraju.</li></ul> |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w przypadku których żądanie nie pochodzi z Stany Zjednoczone.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania adresu zdalnego.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Treść żądania

Warunek dopasowania **treści żądania** identyfikuje żądania na podstawie określonego tekstu, który pojawia się w treści żądania. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

> [!NOTE]
> Jeśli treść żądania przekracza rozmiar 64 KB, tylko pierwsza 64 KB będzie brana pod uwagę warunek dopasowania **treści żądania** .

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Wartość | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość tekstu treści żądania do dopasowania. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w których treść żądania zawiera ciąg `ERROR` . Treść żądania jest przekształcana na wielkie przed ocenęm dopasowania, więc `error` i inne odmiany wielkości liter spowodują również wyzwolenie tego warunku dopasowywania.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania treści żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Nazwa pliku żądania

Warunek dopasowania **nazwy pliku żądania** identyfikuje żądania, które zawierają określoną nazwę pliku w adresie URL żądania. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Wartość | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość nazwy pliku żądania do dopasowania. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w przypadku których nazwa pliku żądania to `media.mp4` . Nazwa pliku zostanie przekształcona na małe przed ocenęm dopasowania, więc `MEDIA.MP4` a inne odmiany przypadku również spowodują wyzwolenie tego warunku dopasowywania.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania nazw plików żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Rozszerzenie pliku żądania

Warunek dopasowania **rozszerzenia pliku żądania** identyfikuje żądania, które zawierają określone rozszerzenie pliku w nazwie pliku w adresie URL żądania. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

> [!NOTE]
> Nie dołączaj wiodącego okresu. Na przykład: użyj opcji `html` zamiast `.html`.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Wartość | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość rozszerzenia pliku żądania do dopasowania. Nie dołączaj wiodącego okresu. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w przypadku których rozszerzenie pliku żądania to `pdf` lub `docx` . Przekształćmy rozszerzenie pliku żądania na małe litery przed dokonaniem oceny dopasowania, więc `PDF` , `DocX` i inne różnice wielkości liter spowodują również wyzwolenie tego warunku dopasowywania.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Zrzut ekranu portalu pokazujący warunek dopasowania rozszerzenia pliku żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Nagłówek żądania

Warunek dopasowania **nagłówka żądania** identyfikuje żądania, które zawierają określony nagłówek w żądaniu. Możesz użyć tego warunku dopasowania, aby sprawdzić, czy nagłówek istnieje niezależnie od jego wartości, lub sprawdzić, czy nagłówek jest zgodny z określoną wartością. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Nazwa nagłówka | Wartość ciągu reprezentująca nazwę argumentu POST. |
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Wartość | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość nagłówka żądania do dopasowania. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w których żądanie zawiera nagłówek o nazwie `MyCustomHeader` , niezależnie od jego wartości.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania nagłówka żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Metoda żądania

Warunek dopasowania **metody żądania** identyfikuje żądania, które używają określonej metody żądania HTTP. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | <ul><li>W Azure Portal: `Equal` , `Not Equal`</li><li>W szablonach ARM: `Equal` ; Użyj `negateCondition` właściwości, aby określić _nie równa_ się |
| Metoda żądania | Co najmniej jedna metoda HTTP z: `GET` ,,,,, `POST` `PUT` `DELETE` `HEAD` `OPTIONS` , `TRACE` . Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |

### <a name="example"></a>Przykład

W tym przykładzie są one zgodne ze wszystkimi żądaniami, w których żądanie używa `DELETE` metody.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania metody żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Ścieżka żądania

Warunek dopasowania **ścieżki żądania** identyfikuje żądania, które zawierają określoną ścieżkę w adresie URL żądania. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

> [!NOTE]
> Ścieżka jest częścią adresu URL po nazwie hosta i ukośniku. Na przykład, w adresie URL `https://www.contoso.com/files/secure/file1.pdf` , ścieżka ma wartość `files/secure/file1.pdf` .

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Wartość | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość ścieżki żądania do dopasowania. Nie dołączaj ukośnika wiodącego. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w których rozpoczyna się ścieżka pliku żądania `files/secure/` . Przekształćmy rozszerzenie pliku żądania na małe litery przed ocenęm dopasowania, więc żądania do `files/SECURE/` i inne różnice wielkości liter spowodują również wyzwolenie tego warunku dopasowywania.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania ścieżki żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Protokół żądania

Warunek dopasowania **protokołu żądania** identyfikuje żądania, które używają określonego protokołu (http lub https).

> [!NOTE]
> *Protokół* jest czasami nazywany również *schematem*.

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | <ul><li>W Azure Portal: `Equal` , `Not Equal`</li><li>W szablonach ARM: `Equal` ; Użyj `negateCondition` właściwości, aby określić _nie równa_ się |
| Metoda żądania | `HTTP`, `HTTPS` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w przypadku których żądanie używa `HTTP` protokołu.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania protokołu żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> Adres URL żądania

Identyfikuje żądania zgodne z określonym adresem URL. Cały adres URL jest oceniany. Można określić wiele wartości do dopasowania, które zostaną połączone przy użyciu logiki lub.

> [!TIP]
> Jeśli używasz tego warunku reguły, pamiętaj o uwzględnieniu protokołu. Na przykład użyj `https://www.contoso.com` zamiast tylko `www.contoso.com` .

### <a name="properties"></a>Właściwości

| Właściwość | Obsługiwane wartości |
|-|-|
| Operator | Dowolny operator z [listy standardowych operatorów](#operator-list). |
| Wartość | Co najmniej jedna wartość ciągu lub liczba całkowita reprezentująca wartość adresu URL żądania do dopasowania. Jeśli określono wiele wartości, są one oceniane przy użyciu lub logiki. |
| Przekształcanie wielkości liter | `Lowercase`, `Uppercase` |

### <a name="example"></a>Przykład

W tym przykładzie są zgodne wszystkie żądania, w których rozpoczyna się adres URL żądania `https://api.contoso.com/customers/123` . Przekształćmy rozszerzenie pliku żądania na małe litery przed ocenęm dopasowania, więc żądania do `https://api.contoso.com/Customers/123` i inne różnice wielkości liter spowodują również wyzwolenie tego warunku dopasowywania.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Zrzut ekranu portalu przedstawiający warunek dopasowania adresu URL żądania.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Lista operatorów

Dla reguł, które akceptują wartości z standardowej listy operatorów, są prawidłowe następujące operatory:

| Operator                   | Opis                                                                                                                    | Obsługa szablonów ARM                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Dowolne                        | Dopasowuje, gdy istnieje jakakolwiek wartość, niezależnie od tego, co to jest.                                                                     | `operator`: `Any`                                               |
| Równe                      | Dopasowuje, gdy wartość dokładnie pasuje do określonego ciągu.                                                                   | `operator`: `Equal`                                             |
| Contains                   | Dopasowuje, gdy wartość zawiera określony ciąg.                                                                          | `operator`: `Contains`                                          |
| Mniejsze niż                  | Dopasowuje, gdy długość wartości jest mniejsza niż określona liczba całkowita.                                                       | `operator`: `LessThan`                                          |
| Większe niż               | Dopasowuje, gdy długość wartości jest większa niż określona liczba całkowita.                                                    | `operator`: `GreaterThan`                                       |
| Mniejsze niż lub równe         | Dopasowuje, jeśli długość wartości jest mniejsza lub równa określonej liczbie całkowitej.                                           | `operator`: `LessThanOrEqual`                                   |
| Większe niż lub równe      | Dopasowuje, gdy długość wartości jest większa lub równa określonej liczbie całkowitej.                                        | `operator`: `GreaterThanOrEqual`                                |
| Zaczyna się od                | Dopasowuje, gdy wartość zaczyna się od określonego ciągu.                                                                       | `operator`: `BeginsWith`                                        |
| Kończy się na                  | Dopasowuje, gdy wartość zostanie zakończona z określonym ciągiem.                                                                         | `operator`: `EndsWith`                                          |
| Wyrażeń                      | Dopasowuje, gdy wartość jest zgodna z określonym wyrażeniem regularnym. [Aby uzyskać więcej informacji, zobacz poniżej.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Nie wszystkie                    | Dopasowuje, jeśli nie ma wartości.                                                                                                | `operator`: `Any` i `negateCondition` : `true`                |
| Nie równa się                  | Dopasowuje, gdy wartość jest niezgodna z określonym ciągiem.                                                                    | `operator`: `Equal` i `negateCondition` : `true`              |
| Nie zawiera               | Dopasowuje, gdy wartość nie zawiera określonego ciągu.                                                                  | `operator`: `Contains` i `negateCondition` : `true`           |
| Nie mniejsze niż              | Dopasowuje, jeśli długość wartości nie jest mniejsza niż określona liczba całkowita.                                                   | `operator`: `LessThan` i `negateCondition` : `true`           |
| Nie większe niż           | Dopasowuje, jeśli długość wartości nie jest większa niż określona liczba całkowita.                                                | `operator`: `GreaterThan` i `negateCondition` : `true`        |
| Nie mniejsze niż lub równe     | Dopasowuje, jeśli długość wartości nie jest mniejsza lub równa określonej liczbie całkowitej.                                       | `operator`: `LessThanOrEqual` i `negateCondition` : `true`    |
| Nie większe niż lub równe | Dopasowuje, jeśli długość wartości nie jest większa lub równa określonej liczbie całkowitej.                                    | `operator`: `GreaterThanOrEqual` i `negateCondition` : `true` |
| Nie zaczyna się od            | Dopasowuje, gdy wartość nie zaczyna się od określonego ciągu.                                                               | `operator`: `BeginsWith` i `negateCondition` : `true`         |
| Nie kończący się na              | Dopasowuje, gdy wartość nie kończy się określonym ciągiem.                                                                 | `operator`: `EndsWith` i `negateCondition` : `true`           |
| Bez wyrażenia regularnego                  | Dopasowuje, gdy wartość jest niezgodna z określonym wyrażeniem regularnym. [Aby uzyskać więcej informacji, zobacz poniżej.](#regular-expressions) | `operator`: `RegEx` i `negateCondition` : `true`              |

> [!TIP]
> W przypadku operatorów liczbowych, takich jak *mniejsza niż* i *większa niż lub równa*, użyte porównanie jest zależne od długości. Wartość w warunku dopasowywania powinna być liczbą całkowitą określającą długość, którą chcesz porównać.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Wyrażenia regularne

Wyrażenia regularne nie obsługują następujących operacji:

* Odwołania wsteczne i przechwytywanie podwyrażeń.
* Dowolne potwierdzenia o zerowej szerokości.
* Odwołania podprocedury i wzorce cykliczne.
* Wzorce warunkowe.
* Wycofywanie czasowników kontroli.
* `\C`Dyrektywa jednobajtowa.
* `\R`Dyrektywa dopasowania nowego wiersza.
* `\K`Dyrektywa początku resetowania dopasowania.
* Wywołania i kod osadzony.
* Grupowanie niepodzielne i Kwantyfikatory Possessive.

## <a name="arm-template-support"></a>Obsługa szablonów ARM

Zestawy reguł można skonfigurować przy użyciu szablonów Azure Resource Manager. [Zobacz przykładowy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set). Możesz dodać warunki dopasowania przy użyciu fragmentów kodu JSON lub Bicep zawartych w powyższych przykładach.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zestawie reguł](concept-rule-set.md).
* Dowiedz się, jak [skonfigurować swój pierwszy zestaw reguł](how-to-configure-rule-set.md).
* Dowiedz się więcej o [akcjach zestawu reguł](concept-rule-set-actions.md).
