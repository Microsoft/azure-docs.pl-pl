---
title: Ustawianie ról platformy Azure dla dostępu administracyjnego platformy Azure
titleSuffix: Azure Cognitive Search
description: Kontrola dostępu oparta na rolach (Azure RBAC) w Azure Portal do kontrolowania i delegowania zadań administracyjnych dotyczących zarządzania Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519503"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Ustawianie ról platformy Azure na potrzeby dostępu administracyjnego do usługi Azure Wyszukiwanie poznawcze

Platforma Azure udostępnia [globalny model autoryzacji oparty na rolach](../role-based-access-control/role-assignments-portal.md) dla wszystkich usług zarządzanych za pośrednictwem portalu lub Menedżer zasobów interfejsów API. Role Owner, współautor i Reader określają poziom *administrowania usługą* dla Active Directory użytkowników, grup i podmiotów zabezpieczeń przypisanych do każdej roli. 

> [!Note]
> Nie ma żadnej kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby zabezpieczania zawartości w usłudze. Użyj klucza interfejsu API administratora lub klucza interfejsu API zapytania dla uwierzytelnionych żądań do samej usługi. W przypadku dostępu opartego na tożsamościach za pomocą wyników wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwając dokumenty, dla których obiekt żądający nie powinien mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtry zabezpieczeń](search-security-trimming-for-azure-search.md).

## <a name="management-tasks-by-role"></a>Zadania zarządzania według roli

W przypadku usługi Azure Wyszukiwanie poznawcze role są skojarzone z poziomem uprawnień, które obsługują następujące zadania zarządzania:

| Rola | Zadanie |
| --- | --- |
| Właściciel |Utwórz lub Usuń usługę lub dowolny obiekt z usługi, w tym klucze API-Keys, indeksy, indeksatory, źródła danych indeksatora i harmonogramy indeksatora.<p>Wyświetlanie stanu usługi, w tym liczby i rozmiaru magazynu.<p>Dodaj lub Usuń członkostwo w roli (tylko właściciel może zarządzać członkostwem w roli).<p>Administratorzy subskrypcji i właściciele usług mają automatyczne członkostwo w roli właściciele. |
| Współautor | Taki sam poziom dostępu jak właściciel, bez zarządzania rolami platformy Azure. Na przykład współautor może tworzyć lub usuwać obiekty lub wyświetlać i generować ponownie [klucze API](search-security-api-keys.md), ale nie mogą modyfikować członkostw ról.<br><br>[Współautor Search Service](../role-based-access-control/built-in-roles.md#search-service-contributor) jest równoważny z wbudowaną rolą współautora. |
| Czytelnik |Wyświetl podstawowe informacje dotyczące usługi, takie jak punkt końcowy usługi, subskrypcja, Grupa zasobów, region, warstwa i pojemność. Możesz również wyświetlić metryki usługi, takie jak średnie zapytania na sekundę, na karcie monitorowanie. Członkowie tej roli nie mogą wyświetlać informacji o indeksie, indeksatorze, źródle danych ani zestawu umiejętności. Dotyczy to również danych użycia dla tych obiektów, takich jak liczba indeksów istniejących w usłudze. |

Role nie przyznają praw dostępu do punktu końcowego usługi. Operacje usługi wyszukiwania, takie jak zarządzanie indeksami, populacja indeksu i zapytania dotyczące danych wyszukiwania, są kontrolowane przez klucze API-Keys, a nie role. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami interfejsu API](search-security-api-keys.md).

## <a name="permissions-table"></a>Tabela uprawnień

Poniższa tabela zawiera podsumowanie operacji dozwolonych w usłudze Azure Wyszukiwanie poznawcze i odblokowanie dostępu do określonej operacji.

Uprawnienia kontroli RBAC platformy Azure dotyczą operacji portalu i zarządzania usługami (tworzenie, usuwanie lub zmienianie usługi lub jej kluczy interfejsu API). Klucze interfejsu API są tworzone po istniejącej usłudze i mają zastosowanie do operacji zawartości w usłudze. Ponadto w przypadku operacji związanych z zawartością w portalu, takich jak tworzenie lub usuwanie obiektów, właściciel lub współautor usługi Azure RBAC współpracują z usługą z implikowanym kluczem interfejsu API administratora.

| Operacja | Kontrolowane przez |
|-----------|-------------------------|
| Tworzenie usługi | Uprawnienia usługi Azure RBAC: właściciel lub współautor |
| Skalowanie usługi | Uprawnienia usługi Azure RBAC: właściciel lub współautor|
| Usuwanie usługi | Uprawnienia usługi Azure RBAC: właściciel lub współautor |
| Zarządzaj administratorami lub kluczami zapytań | Uprawnienia usługi Azure RBAC: właściciel lub współautor|
| Wyświetlanie informacji o usłudze w portalu lub interfejsie API zarządzania | Uprawnienia usługi Azure RBAC: właściciel, współautor lub czytelnik  |
| Wyświetlanie informacji o obiektach i metryk w portalu lub interfejsie API zarządzania | Uprawnienia usługi Azure RBAC: właściciel lub współautor |
| Tworzenie, modyfikowanie i usuwanie obiektów w usłudze: <br>Indeksy i części składników (w tym definicje analizatora, profile oceniania, opcje CORS), indeksatory, źródła danych, synonimy, sugestie | Klucz administratora w przypadku używania interfejsu API, właściciela usługi Azure RBAC lub współautora w przypadku korzystania z portalu |
| Tworzenie zapytań względem indeksu | Administrator lub klucz zapytania w przypadku używania interfejsu API, właściciela lub współautora usługi Azure RBAC w przypadku korzystania z portalu |
| Zbadaj informacje o systemie dotyczące obiektów, takich jak zwracanie statystyk, liczników i list obiektów | Klucz administratora w przypadku używania interfejsu API, właściciela usługi Azure RBAC lub współautora w przypadku korzystania z portalu |

## <a name="next-steps"></a>Następne kroki

+ [Zarządzanie przy użyciu programu PowerShell](search-manage-powershell.md) 
+ [Wydajność i optymalizacja na platformie Azure Wyszukiwanie poznawcze](search-performance-optimization.md)
+ [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md).
