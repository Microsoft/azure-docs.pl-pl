---
title: Funkcje szablonów
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager (szablon ARM) do pobierania wartości, pracy z ciągami i wartościami liczbowymi oraz pobierania informacji o wdrożeniu.
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: dbda5c635a5189158424363c341b269d331a5fdc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707323"
---
# <a name="arm-template-functions"></a>Funkcje szablonu ARM

W tym artykule opisano wszystkie funkcje, których można użyć w szablonie Azure Resource Manager (szablon ARM). Aby uzyskać informacje o używaniu funkcji w szablonie, zobacz [składnia szablonu](template-expressions.md).

Aby utworzyć własne funkcje, zobacz [funkcje zdefiniowane przez użytkownika](template-syntax.md#functions).

Większość funkcji działa tak samo w przypadku wdrożenia w grupie zasobów, subskrypcji, grupie zarządzania lub dzierżawie. Nie można używać kilku funkcji we wszystkich zakresach. Są one wymienione na poniższej liście.

<a id="array" aria-hidden="true"></a>
<a id="concatarray" aria-hidden="true"></a>
<a id="contains" aria-hidden="true"></a>
<a id="createarray" aria-hidden="true"></a>
<a id="empty" aria-hidden="true"></a>
<a id="first" aria-hidden="true"></a>
<a id="intersection" aria-hidden="true"></a>
<a id="last" aria-hidden="true"></a>
<a id="length" aria-hidden="true"></a>
<a id="min" aria-hidden="true"></a>
<a id="max" aria-hidden="true"></a>
<a id="range" aria-hidden="true"></a>
<a id="skip" aria-hidden="true"></a>
<a id="take" aria-hidden="true"></a>
<a id="union" aria-hidden="true"></a>

## <a name="any-function"></a>Dowolna funkcja

[Każda funkcja](template-functions-any.md) jest dostępna w Bicep, aby pomóc w rozwiązywaniu problemów dotyczących ostrzeżeń dotyczących typów danych.

## <a name="array-functions"></a>Funkcje tablicy

Menedżer zasobów udostępnia kilka funkcji do pracy z tablicami.

* [array](template-functions-array.md#array)
* [Concat](template-functions-array.md#concat)
* [wyświetlana](template-functions-array.md#contains)
* [przearray](template-functions-array.md#createarray)
* [puste](template-functions-array.md#empty)
* [pierwszego](template-functions-array.md#first)
* [część wspólną](template-functions-array.md#intersection)
* [ostatniego](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [Maksymalny](template-functions-array.md#max)
* [zakresu](template-functions-array.md#range)
* [Skocz](template-functions-array.md#skip)
* [czasochłonn](template-functions-array.md#take)
* [Unii](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true"></a>
<a id="equals" aria-hidden="true"></a>
<a id="less" aria-hidden="true"></a>
<a id="lessorequals" aria-hidden="true"></a>
<a id="greater" aria-hidden="true"></a>
<a id="greaterorequals" aria-hidden="true"></a>

## <a name="comparison-functions"></a>Funkcje porównania

Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonach.

* [łączonych](template-functions-comparison.md#coalesce)
* [ubiegł](template-functions-comparison.md#equals)
* [wcześniejsz](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true"></a>
<a id="parameters" aria-hidden="true"></a>
<a id="variables" aria-hidden="true"></a>

## <a name="date-functions"></a>Funkcje daty

Menedżer zasobów udostępnia następujące funkcje do pracy z datami.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funkcje wartości wdrożenia

Menedżer zasobów udostępnia następujące funkcje do uzyskiwania wartości z sekcji szablonu i wartości związanych ze wdrożeniem:

* [mieszczeniu](template-functions-deployment.md#deployment)
* [środowisko](template-functions-deployment.md#environment)
* [wejściowe](template-functions-deployment.md#parameters)
* [modyfikacj](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true"></a>
<a id="bool" aria-hidden="true"></a>
<a id="if" aria-hidden="true"></a>
<a id="not" aria-hidden="true"></a>
<a id="or" aria-hidden="true"></a>

## <a name="logical-functions"></a>Funkcje logiczne

Menedżer zasobów udostępnia następujące funkcje do pracy z warunkami logicznymi:

* [and](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [false](template-functions-logical.md#false)
* [if](template-functions-logical.md#if)
* [niemożliwe](template-functions-logical.md#not)
* [oraz](template-functions-logical.md#or)
* [oznacza](template-functions-logical.md#true)

<a id="add" aria-hidden="true"></a>
<a id="copyindex" aria-hidden="true"></a>
<a id="div" aria-hidden="true"></a>
<a id="float" aria-hidden="true"></a>
<a id="int" aria-hidden="true"></a>
<a id="minint" aria-hidden="true"></a>
<a id="maxint" aria-hidden="true"></a>
<a id="mod" aria-hidden="true"></a>
<a id="mul" aria-hidden="true"></a>
<a id="sub" aria-hidden="true"></a>

## <a name="numeric-functions"></a>Funkcje liczbowe

Menedżer zasobów udostępnia następujące funkcje do pracy z liczbami całkowitymi:

* [add](template-functions-numeric.md#add)
* [Funkcji copyindex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [liczba zmiennoprzecinkowa](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [Maksymalny](template-functions-numeric.md#max)
* [Funkcja](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true"></a>

## <a name="object-functions"></a>Funkcje obiektów

Menedżer zasobów udostępnia kilka funkcji do pracy z obiektami.

* [wyświetlana](template-functions-object.md#contains)
* [createObject](template-functions-object.md#createobject)
* [puste](template-functions-object.md#empty)
* [część wspólną](template-functions-object.md#intersection)
* [kodu](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [null](template-functions-object.md#null)
* [Unii](template-functions-object.md#union)

<a id="extensionResourceId" aria-hidden="true"></a>
<a id="listkeys" aria-hidden="true"></a>
<a id="list" aria-hidden="true"></a>
<a id="providers" aria-hidden="true"></a>
<a id="reference" aria-hidden="true"></a>
<a id="resourcegroup" aria-hidden="true"></a>
<a id="resourceid" aria-hidden="true"></a>
<a id="subscription" aria-hidden="true"></a>
<a id="subscriptionResourceId" aria-hidden="true"></a>
<a id="tenantResourceId" aria-hidden="true"></a>

## <a name="resource-functions"></a>Funkcje zasobów

Menedżer zasobów udostępnia następujące funkcje pobierania wartości zasobów:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [staw](template-functions-resource.md#list)
* [pickZones](template-functions-resource.md#pickzones)
* [udostępnia](template-functions-resource.md#providers)
* [odwoła](template-functions-resource.md#reference)
* Grupa [zasobów — może](template-functions-resource.md#resourcegroup) być używana tylko we wdrożeniach w ramach grupy.
* [ResourceID](template-functions-resource.md#resourceid) — można użyć w dowolnym zakresie, ale prawidłowe parametry zmieniają się w zależności od zakresu.
* [subskrypcja](template-functions-resource.md#subscription) — może być używana tylko we wdrożeniach w grupie zasobów lub subskrypcji.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true"></a>
<a id="base64tojson" aria-hidden="true"></a>
<a id="base64tostring" aria-hidden="true"></a>
<a id="concat" aria-hidden="true"></a>
<a id="containsstring" aria-hidden="true"></a>
<a id="datauri" aria-hidden="true"></a>
<a id="datauritostring" aria-hidden="true"></a>
<a id="emptystring" aria-hidden="true"></a>
<a id="endswith" aria-hidden="true"></a>
<a id="firststring" aria-hidden="true"></a>
<a id="guid" aria-hidden="true"></a>
<a id="indexof" aria-hidden="true"></a>
<a id="laststring" aria-hidden="true"></a>
<a id="lastindexof" aria-hidden="true"></a>
<a id="lengthstring" aria-hidden="true"></a>
<a id="padleft" aria-hidden="true"></a>
<a id="replace" aria-hidden="true"></a>
<a id="skipstring" aria-hidden="true"></a>
<a id="split" aria-hidden="true"></a>
<a id="startswith" aria-hidden="true"></a>
<a id="string" aria-hidden="true"></a>
<a id="substring" aria-hidden="true"></a>
<a id="takestring" aria-hidden="true"></a>
<a id="tolower" aria-hidden="true"></a>
<a id="toupper" aria-hidden="true"></a>
<a id="trim" aria-hidden="true"></a>
<a id="uniquestring" aria-hidden="true"></a>
<a id="uri" aria-hidden="true"></a>
<a id="uricomponent" aria-hidden="true"></a>
<a id="uricomponenttostring" aria-hidden="true"></a>

## <a name="string-functions"></a>Funkcje ciągów

Menedżer zasobów udostępnia następujące funkcje do pracy z ciągami:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [wyświetlana](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [puste](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [pierwszego](template-functions-string.md#first)
* [Formatowanie](template-functions-string.md#format)
* [guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [ostatniego](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [stępować](template-functions-string.md#replace)
* [Skocz](template-functions-string.md#skip)
* [podziału](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [ciąg](template-functions-string.md#string)
* [podciąg](template-functions-string.md#substring)
* [czasochłonn](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [adresu](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie ARM można znaleźć [w temacie Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure](linked-templates.md).
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [iteracja zasobów w szablonach ARM](copy-resources.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
