---
title: Funkcje żądania danych klienta dla usługi Azure Digital bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: W tym artykule przedstawiono procesy eksportowania i usuwania danych osobowych w usłudze Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461763"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Funkcje żądania danych klienta Digital bliźniaczych reprezentacji na platformie Azure

Usługa Azure Digital bliźniaczych reprezentacji to platforma dewelopera służąca do tworzenia bezpiecznych reprezentacji cyfrowych w środowisku biznesowym. Reprezentacje są prowadzone przez dane stanu na żywo ze źródeł danych wybranych przez użytkowników.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Reprezentacje cyfrowe o nazwie *Digital bliźniaczych reprezentacji* w usłudze Azure Digital bliźniaczych reprezentacji reprezentują jednostki w środowiskach rzeczywistych i są skojarzone z identyfikatorami. Firma Microsoft nie przechowuje żadnych informacji i nie ma dostępu do danych, które mogłyby umożliwić skorelowanie identyfikatorów z użytkownikami. 

Wiele bliźniaczych reprezentacji cyfrowych w usłudze Azure Digital bliźniaczych reprezentacji nie reprezentuje bezpośrednio jednostek osobistych — typowe reprezentowane obiekty mogą być pokoju spotkań biurowych lub fabryki fabryk. Jednak użytkownicy mogą rozważyć pewne jednostki, które mają być identyfikowalne, i według ich uznania mogą zachować własne metody śledzenia zasobów lub spisu, które wiążą cyfrowy bliźniaczych reprezentacji z użytkownikami. Usługa Azure Digital bliźniaczych reprezentacji zarządza i przechowuje wszystkie dane skojarzone z cyfrowym bliźniaczych reprezentacji, tak jakby były danymi osobistymi.

Aby wyświetlać, eksportować i usuwać dane osobowe, do których można się odwołać w żądaniu podmiotu danych, administrator Digital bliźniaczych reprezentacji może używać [**Azure Portal**](https://portal.azure.com/) dla użytkowników i ról lub [**interfejsów API REST usługi Azure Digital bliźniaczych reprezentacji**](/rest/api/azure-digitaltwins/) dla cyfrowych bliźniaczych reprezentacji. Azure Portal i interfejsy API REST zapewniają różne metody obsługi takich żądań podmiotu danych.

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Usługa Azure Digital bliźniaczych reprezentacji traktuje *dane osobowe* , które są skojarzone z administratorami i użytkownikami. 

Usługa Azure Digital bliźniaczych reprezentacji przechowuje [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *Identyfikator obiektu* Azure Active Directory użytkowników mających dostęp do środowiska. Usługa Azure Digital bliźniaczych reprezentacji w Azure Portal wyświetla adresy e-mail użytkowników, ale te adresy e-mail nie są przechowywane w usłudze Azure Digital bliźniaczych reprezentacji. Są one dynamicznie wyszukiwane w Azure Active Directory przy użyciu identyfikatora obiektu Azure Active Directory.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administratorzy usługi Azure Digital bliźniaczych reprezentacji mogą używać Azure Portal do usuwania danych związanych z użytkownikami. Możliwe jest również wykonywanie operacji usuwania na pojedynczych bliźniaczych reprezentacji cyfrowych przy użyciu interfejsów API REST usługi Azure Digital bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat dostępnych interfejsów API, zobacz [dokumentację interfejsów API REST usługi Azure Digital bliźniaczych reprezentacji](/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Usługa Azure Digital bliźniaczych reprezentacji przechowuje dane związane z bliźniaczych reprezentacji cyfrowym. Użytkownicy mogą pobierać i wyświetlać te dane za pośrednictwem interfejsów API REST i eksportować te dane przy użyciu funkcji kopiowania i wklejania. 

Dane klienta, w tym role użytkowników i przypisania ról, można wybierać, kopiować i wklejać z Azure Portal. 

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Aby uzyskać pełną listę interfejsów API usługi Digital bliźniaczych reprezentacji na platformie Azure, zobacz [dokumentację interfejsów API REST usługi Azure Digital bliźniaczych reprezentacji](/rest/api/azure-digitaltwins/).