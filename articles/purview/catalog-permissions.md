---
title: Uprawnienia katalogu (wersja zapoznawcza)
description: Ten artykuł zawiera omówienie sposobu konfigurowania Access Control Role-Based (RBAC) w usłudze Azure kontrolą
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610373"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Kontrola dostępu oparta na rolach w płaszczyźnie danych usługi Azure kontrolą

W tym artykule opisano, jak Role-Based Access Control (RBAC) jest implementowana w [płaszczyźnie danych](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)usługi Azure kontrolą.

> [!IMPORTANT]
> Podmiot zabezpieczeń, który utworzył konto kontrolą, automatycznie otrzymuje wszystkie uprawnienia do płaszczyzny danych, niezależnie od tego, jakie role płaszczyzny danych mogą lub nie znajdują się w. Każdy inny użytkownik, który wykona wszystkie czynności na platformie Azure kontrolą, musi znajdować się w co najmniej jednej z wstępnie zdefiniowanych ról płaszczyzny danych.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Wstępnie zdefiniowane role płaszczyzny danych usługi Azure kontrolą

Usługa Azure kontrolą definiuje zestaw wstępnie zdefiniowanych ról płaszczyzny danych, które mogą służyć do kontrolowania, kto ma dostęp do tego, co w usłudze Azure kontrolą. Role te są następujące:

* **Rola czytnika danych kontrolą** — ma dostęp do portalu kontrolą i może odczytywać całą zawartość na platformie Azure kontrolą, z wyjątkiem powiązań skanowania
* **Kontrolą Data Curator rola** — ma dostęp do portalu kontrolą i może odczytywać całą zawartość w usłudze Azure kontrolą, z wyjątkiem powiązań skanowania, może edytować informacje o zasobach, może edytować definicje klasyfikacji i terminy słownika oraz stosować klasyfikacje i terminologię dla zasobów.
* **Rola administratora źródła danych kontrolą** — nie ma dostępu do portalu kontrolą (użytkownik musi być również członkiem czytnika danych lub ról Curator danych) i może zarządzać wszystkimi aspektami skanowania danych do usługi Azure kontrolą, ale nie ma dostępu do odczytu ani zapisu do zawartości na platformie Azure kontrolą poza tymi związanymi z skanowaniem.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Informacje na temat używania ról płaszczyzny danych usługi Azure kontrolą

Po utworzeniu konta usługi Azure kontrolą twórca będzie traktowany tak, jakby znajdowały się zarówno w roli administratora źródła danych Curator, jak i kontrolą. Jednak te role nie są przypisane do twórcy konta w magazynie ról. Usługa Azure Purview uznaje, że podmiot zabezpieczeń jest twórcą konta i rozszerza na niego te możliwości na podstawie tożsamości.

Wszyscy inni użytkownicy mogą korzystać z konta usługi Azure Purview tylko wtedy, jeśli mają co najmniej jedną z tych ról. Oznacza to, że po utworzeniu konta usługi Azure kontrolą nikt, ale nie może uzyskać dostępu do konta ani używać jego interfejsów API, dopóki nie zostaną umieszczone w co najmniej jednej z poprzednich zdefiniowanych ról.

Należy pamiętać, że rola administratora źródła danych kontrolą ma dwa obsługiwane scenariusze. Pierwszy scenariusz jest przeznaczony dla użytkowników, którzy już kontrolą czytelnicy danych lub kontrolą Curators danych, które również muszą mieć możliwość tworzenia skanów. Ci użytkownicy muszą znajdować się w dwóch rolach, co najmniej jeden z kontrolą danych kontrolą i Curator danych, a także być umieszczany w roli administratora źródła danych kontrolą.

Innym scenariuszem dla administratora źródła danych kontrolą jest dla procesów programistycznych, takich jak jednostki usługi, które muszą być w stanie skonfigurować i monitorować skany, ale nie powinny mieć dostępu do żadnych danych wykazu.

Ten scenariusz można zaimplementować przez umieszczenie nazwy głównej usługi w roli administratora źródła danych kontrolą bez umieszczania w żadnej z pozostałych dwóch ról. Podmiot zabezpieczeń nie będzie miał dostępu do portalu kontrolą, ale jest o.k. ponieważ jest to podmiot programistyczny i komunikuje się tylko za pośrednictwem interfejsów API.

## <a name="putting-users-into-roles"></a>Umieszczanie użytkowników w rolach

W pierwszej kolejności biznesowej po utworzeniu konta usługi Azure kontrolą należy przypisać osoby do tych ról.

Przypisanie roli jest zarządzane przez funkcję [RBAC na platformie Azure](../role-based-access-control/overview.md).

Tylko dwie wbudowane role płaszczyzny kontroli na platformie Azure mogą przypisywać role użytkowników, są to właściciele lub Administratorzy dostępu użytkowników. Aby umieścić osoby w tych rolach dla usługi Azure kontrolą, należy wyszukać kogoś, kto jest właścicielem lub administratorem dostępu użytkowników lub stać się jednym siebieem.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Przykład przypisania kogoś do roli

1. Przejdź do https://portal.azure.com konta usługi Azure kontrolą i przejdź do niego
1. Po lewej stronie kliknij pozycję "kontrola dostępu (IAM)"
1. Następnie postępuj zgodnie z ogólnymi instrukcjami podanymi [tutaj](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)

## <a name="role-definitions-and-actions"></a>Definicje ról i akcje

Rola jest definiowana jako kolekcja akcji. Zobacz [tutaj](../role-based-access-control/role-definitions.md) , aby uzyskać więcej informacji na temat sposobu definiowania ról. I zobacz [tutaj](../role-based-access-control/built-in-roles.md) , aby zapoznać się z definicjami ról dla ról usługi Azure kontrolą.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Dodawanie do roli płaszczyzny danych na koncie usługi Azure kontrolą

Jeśli chcesz uzyskać dostęp do konta usługi Azure kontrolą, możesz użyć jego Studio lub wywołać interfejsy API, które należy dodać do roli płaszczyzny danych usługi Azure kontrolą. Jedyną osobą, która może to zrobić, są ci właściciele lub Administratorzy dostępu użytkowników na koncie usługi Azure kontrolą. W przypadku większości użytkowników następnym krokiem jest znalezienie administratora lokalnego, który może pomóc w znalezieniu odpowiednich osób, które mogą uzyskać dostęp.

W przypadku użytkowników mających dostęp do [Azure Portal](https://portal.azure.com) firmy można wyszukać konkretne konto usługi Azure kontrolą, do którego chcesz dołączyć, kliknij kartę "kontrola dostępu (IAM)" i sprawdź, kto jest właścicielem lub administratorem dostępu użytkowników (UAAs). Należy jednak zauważyć, że w niektórych przypadkach Azure Active Directory grup lub jednostek usługi mogą być używane jako właściciele lub UAAs, w takim przypadku może nie być możliwe bezpośrednie kontaktowanie się z nimi. Zamiast nich musi znaleźć administratora, aby uzyskać pomoc.

## <a name="who-should-be-assigned-to-what-role"></a>Kto ma być przypisany do roli?

|Scenariusz użytkownika|Odpowiednie role|
|-------------|-----------------|
|Muszę znaleźć zasoby, ale nie chcę edytować niczego|Rola czytnika danych kontrolą|
|Chcę edytować informacje o zasobach, umieszczać w nich klasyfikacje, kojarzyć je z wpisami słownika itp.|Rola Curator danych kontrolą|
|Chcę edytować słownik lub skonfigurować nowe definicje klasyfikacji|Rola Curator danych kontrolą|
|Nazwa główna usługi aplikacji musi wypchnąć dane do usługi Azure kontrolą|Rola Curator danych kontrolą|
|Muszę skonfigurować skanowanie za pośrednictwem programu kontrolą Studio|Rola administratora źródła danych kontrolą oraz co najmniej jedna rola czytnika danych kontrolą lub rola Curator danych kontrolą|
|Muszę włączyć jednostkę usługi lub inną tożsamość programistyczną, aby skonfigurować i monitorować skany w usłudze Azure kontrolą bez zezwalania na tożsamość programistyczną w celu uzyskania dostępu do informacji o wykazie |Rola administratora źródła danych kontrolą|
|Muszę umieścić użytkowników w rolach na platformie Azure kontrolą | Administrator dostępu użytkownika lub właściciela |

Aby uzyskać więcej informacji na temat dodawania podmiotu zabezpieczeń do roli, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md) .

## <a name="next-steps"></a>Następne kroki

* [Analiza danych](concept-insights.md)
