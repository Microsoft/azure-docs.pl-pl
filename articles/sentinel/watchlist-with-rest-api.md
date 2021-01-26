---
title: Zarządzanie watchlists na platformie Azure — wskaźnik przy użyciu interfejsu API REST | Microsoft Docs
description: W tym artykule opisano sposób zarządzania usługą Azure watchlists za pomocą interfejsu API REST Log Analytics w celu tworzenia, modyfikowania i usuwania watchlists oraz ich elementów.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798463"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Zarządzanie watchlists na platformie Azure — za pomocą interfejsu API REST

> [!IMPORTANT]
> Funkcja watchlists jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Platforma Azure — wskaźnikowa, która jest wbudowana w część Log Analytics Azure Monitor, umożliwia zarządzanie watchlists za pomocą interfejsu API REST Log Analytics. W tym dokumencie przedstawiono sposób tworzenia, modyfikowania i usuwania watchlists oraz ich elementów przy użyciu interfejsu API REST.  Watchlists utworzone w ten sposób będą wyświetlane w interfejsie użytkownika usługi Azure wskaźnikowej.

## <a name="common-uri-parameters"></a>Typowe parametry URI

Poniżej przedstawiono wspólne parametry URI dla wszystkich poleceń interfejsu API listy do obejrzenia:

| Nazwa | W | Wymagany | Typ | Opis |
|-|-|-|-|-|
| **Identyfikator** | path | tak | GUID | Identyfikator subskrypcji platformy Azure |
| **ResourceGroupName** | path | tak | ciąg | Nazwa grupy zasobów w ramach subskrypcji |
| **WorkspaceName** | path | tak | ciąg | Nazwa obszaru roboczego Log Analytics |
| **{watchlistAlias}** | path | opcję | ciąg | nazwa danego listy do obejrzenia<br>\* Niewymagane podczas pobierania wszystkich watchlists |
| **{watchlistItemId}** | path | tak * * | GUID | Identyfikator elementu do utworzenia, Dodaj do lub Usuń z listy do obejrzenia<br>\*\* Wymagane tylko dla poleceń elementu listy do obejrzenia |
| **{API-Version}** | query | tak | ciąg | wersja protokołu użyta do zgłoszenia tego żądania. Od 29 października 2020 listy do obejrzenia interfejsu API to *2019-01-01 — wersja zapoznawcza* |
| **{bearerToken}** | autoryzacja | tak | token | Token autoryzacji okaziciela |
|  

## <a name="retrieve-all-watchlists"></a>Pobierz wszystkie watchlists

To polecenie umożliwia pobranie wszystkich watchlists skojarzonych z obszarem roboczym bez ich elementów.

### <a name="request-uri"></a>Identyfikator URI żądania
(Identyfikator URI jest pojedynczym wierszem, podzielony w celu ułatwienia czytelności)

| Metoda | Identyfikator URI żądania |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpowiedzi

| Kod stanu | Treść odpowiedzi | Opis |
|-|-|-|
| 200/OK | Lista istniejących watchlists lub pusta, jeśli nie znaleziono listy do obejrzenia |  |
| 400/złe żądanie |  | Nieprawidłowo sformułowana składnia żądania, nieprawidłowy parametr żądania... |
|

## <a name="look-up-a-watchlist-by-name"></a>Wyszukaj listy do obejrzenia według nazwy

To polecenie pobiera określony listy do obejrzenia skojarzony z obszarem roboczym bez jego elementów.

### <a name="request-uri"></a>Identyfikator URI żądania
(Identyfikator URI jest pojedynczym wierszem, podzielony w celu ułatwienia czytelności)

| Metoda | Identyfikator URI żądania |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpowiedzi

| Kod stanu | Treść odpowiedzi | Opis |
|-|-|-|
| 200/OK | Żądany listy do obejrzenia |  |
| 400/złe żądanie |  | Nieprawidłowo sformułowana składnia żądania, nieprawidłowy parametr żądania... |
| 404/nie znaleziono |  | Nie znaleziono listy do obejrzenia z żądaną nazwą |
|

## <a name="create-a-watchlist"></a>Utwórz listy do obejrzenia

To polecenie tworzy listy do obejrzenia i dodaje do niego elementy. Do tego punktu końcowego są odbierane dwa wywołania, aby utworzyć listy do obejrzenia i jego elementy: pierwszy z nich utworzy listy do obejrzenia (nadrzędny), a drugi doda te elementy.

### <a name="request-uri"></a>Identyfikator URI żądania
(Identyfikator URI jest pojedynczym wierszem, podzielony w celu ułatwienia czytelności)

| Metoda | Identyfikator URI żądania |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Treść żądania

Oto przykład treści żądania listy do obejrzenia Create:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Odpowiedzi

| Kod stanu | Treść odpowiedzi | Opis |
|-|-|-|
| 200/OK | Listy do obejrzenia utworzony przez żądanie, bez elementów |  |
| 400/złe żądanie |  | Nieprawidłowo sformułowana składnia żądania, nieprawidłowy parametr żądania... |
| 409/konflikt |  | Operacja nie powiodła się, istnieje już istniejący listy do obejrzenia z tym samym aliasem |
|

## <a name="delete-a-watchlist"></a>Usuń listy do obejrzenia

To polecenie usuwa listy do obejrzenia i jego elementy.

### <a name="request-uri"></a>Identyfikator URI żądania
(Identyfikator URI jest pojedynczym wierszem, podzielony w celu ułatwienia czytelności)

| Metoda | Identyfikator URI żądania |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpowiedzi

| Kod stanu | Treść odpowiedzi | Opis |
|-|-|-|
| 200/OK | Pusta treść odpowiedzi |  |
| 204/Brak zawartości | Pusta treść odpowiedzi | Nie usunięto żadnych elementów |
| 400/złe żądanie |  | Nieprawidłowo sformułowana składnia żądania, nieprawidłowy parametr żądania... |
|

## <a name="add-or-update-a-watchlist-item"></a>Dodawanie lub aktualizowanie elementu listy do obejrzenia

Gdy to polecenie jest uruchamiane na istniejącym *watchlisItemId* (GUID), zaktualizuje element o dane podane w treści żądania. W przeciwnym razie zostanie utworzony nowy element.

### <a name="request-uri"></a>Identyfikator URI żądania
(Identyfikator URI jest pojedynczym wierszem, podzielony w celu ułatwienia czytelności)

| Metoda | Identyfikator URI żądania |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Treść żądania

Oto przykład treści żądania żądania dodania/aktualizacji elementu listy do obejrzenia:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Odpowiedzi

| Kod stanu | Treść odpowiedzi | Opis |
|-|-|-|
| 200/OK | Element listy do obejrzenia utworzony lub zaktualizowany przez żądanie |  |
| 400/złe żądanie |  | Nieprawidłowo sformułowana składnia żądania, nieprawidłowy parametr żądania... |
| 409/konflikt |  | Operacja nie powiodła się, istnieje już istniejący listy do obejrzenia z tym samym aliasem |
|

## <a name="delete-a-watchlist-item"></a>Usuń element listy do obejrzenia

To polecenie usuwa istniejący element listy do obejrzenia.

### <a name="request-uri"></a>Identyfikator URI żądania
(Identyfikator URI jest pojedynczym wierszem, podzielony w celu ułatwienia czytelności)

| Metoda | Identyfikator URI żądania |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Odpowiedzi

| Kod stanu | Treść odpowiedzi | Opis |
|-|-|-|
| 200/OK | Pusta treść odpowiedzi |  |
| 204/Brak zawartości | Pusta treść odpowiedzi | Nie usunięto żadnych elementów |
| 400/złe żądanie |  | Nieprawidłowo sformułowana składnia żądania, nieprawidłowy parametr żądania... |
|

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób zarządzania watchlists i ich elementami w wskaźniku kontrolnym platformy Azure przy użyciu interfejsu API Log Analytics. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się więcej o [watchlists](watchlists.md)
- Poznaj inne sposoby korzystania z [interfejsu API badania platformy Azure](/rest/api/securityinsights/)