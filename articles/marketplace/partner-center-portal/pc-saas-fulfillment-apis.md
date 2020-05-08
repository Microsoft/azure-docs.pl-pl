---
title: Interfejsy API realizacji SaaS w komercyjnej witrynie Microsoft Marketplace
description: Wprowadzenie do interfejsów API realizacji, które umożliwiają integrację ofert SaaS w Microsoft AppSource i witrynie Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858092"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>Interfejsy API realizacji SaaS w komercyjnej witrynie Microsoft Marketplace

Interfejsy API do realizacji SaaS umożliwiają niezależnym dostawcom oprogramowania integrację swoich aplikacji SaaS w Microsoft AppSource i witrynie Azure Marketplace. Te interfejsy API umożliwiają aplikacjom niezależnego dostawcy oprogramowania uczestnictwo we wszystkich kanałach z obsługą handlu: bezpośredni, partnerski-LED (odsprzedawca) i dioda. Są one wymagane do wyświetlania listy ofert SaaS transakcyjnych w Microsoft AppSource i witrynie Azure Marketplace.

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
