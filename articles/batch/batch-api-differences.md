---
title: Różnice między interfejsami API zarządzania i interfejsami API usługi
description: Interfejsy API działają na różnych warstwach usługi Azure Batch.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115401"
---
# <a name="service-level-and-management-level-apis"></a>Interfejsy API poziomu usług i zarządzania

Azure Batch ma dwa zestawy interfejsów API, jeden dla poziomu usługi i jeden dla poziomu zarządzania. Nazewnictwo jest często podobne, ale zwracają różne wyniki. Jeśli potrzebujesz dzienników aktywności, musisz użyć interfejsów API zarządzania. Interfejsy API poziomu usługi pomijają warstwę zarządzania zasobami platformy Azure i nie są rejestrowane.


Zarządzanie partiami i usługa Batch mają interfejsy API dla puli, na przykład. 
- Ten interfejs API służący do usuwania puli jest przeznaczony bezpośrednio na koncie usługi Batch:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Ten interfejs API służący https://docs.microsoft.com/rest/api/batchmanagement/pool/delete do usuwania puli jest przeznaczony dla warstwy Management.Azure.com.

