---
title: Interfejsy API realizacji SaaS | Portal Azure Marketplace
description: Wprowadza wersje interfejsów API realizacji, które umożliwiają integrację ofert SaaS z portalem Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275734"
---
# <a name="saas-fulfillment-apis"></a>Interfejsy API realizacji SaaS

Interfejsy API realizacji SaaS zapewniają niezależnym dostawcom oprogramowania (ISV) integrację swoich aplikacji SaaS z portalem Azure Marketplace. Te interfejsy API umożliwiają aplikacjom niezależnego dostawcy oprogramowania uczestnictwo we wszystkich kanałach z obsługą handlu: bezpośrednich, partnerskich (odsprzedawców) i na terenie.  Są one wymagane do tworzenia listy ofert SaaS transakcyjnych w portalu Azure Marketplace.

> [!WARNING]
> Bieżąca wersja tego interfejsu API jest w wersji 2, która powinna być używana dla wszystkich nowych ofert SaaS.  Wersja 1 interfejsu API jest przestarzała i jest utrzymywana do obsługi istniejących ofert.


## <a name="business-model-support"></a>Obsługa modelu biznesowego

Ten interfejs API obsługuje następujące możliwości modelu biznesowego: Można:

* Określ wiele planów dla oferty. Te plany mają różne funkcje i mogą być wyceniane w różny sposób.
* Oferowanie oferty dla poszczególnych witryn lub poszczególnych modeli rozliczeń na użytkownika.
* Zapewnianie miesięcznych i rocznych (płatnych z góry) opcji rozliczeń.
* Podawanie klientom prywatnych cen w oparciu o negocjowaną umowę biznesową.


## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, zarejestruj swoją aplikację SaaS w [Azure Portal](https://ms.portal.azure.com) zgodnie z opisem w temacie [Rejestrowanie aplikacji usługi Azure AD](./pc-saas-registration.md).  Następnie użyj najnowszej wersji tego interfejsu na potrzeby programowania: [SaaS realizacji interfejsu API w wersji 2](./pc-saas-fulfillment-api-v2.md).
