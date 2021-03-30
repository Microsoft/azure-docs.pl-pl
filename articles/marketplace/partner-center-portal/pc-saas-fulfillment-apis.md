---
title: Interfejsy API realizacji SaaS w komercyjnej witrynie Microsoft Marketplace
description: Wprowadzenie do interfejsów API realizacji, które umożliwiają integrację ofert SaaS w Microsoft AppSource i witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037528"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Interfejsy API realizacji SaaS w komercyjnej witrynie Microsoft Marketplace

Interfejsy API realizacji SaaS umożliwiają wydawcom, znanym także jako niezależnym dostawcom oprogramowania (ISV), publikowanie i sprzedawanie aplikacji SaaS w Microsoft AppSource, Azure Marketplace i Azure Portal. Te interfejsy API umożliwiają aplikacjom niezależnego dostawcy oprogramowania uczestnictwo we wszystkich kanałach z obsługą handlu: bezpośredni, partnerski-LED (odsprzedawca) i dioda.  Integracja z tymi interfejsami API jest wymaganiem do tworzenia i publikowania oferty SaaS z możliwością zrealizowania w centrum partnerskim.

Dostawcy ISV muszą zaimplementować następujące przepływy interfejsu API, dodając do niego kod usługi SaaS w celu utrzymania tego samego stanu subskrypcji dla niezależnych dostawców oprogramowania i firmy Microsoft:

* Przepływ strony docelowej: Firma Microsoft powiadamia wydawcę, że oferta SaaS wydawcy została zakupiona przez klienta w portalu Marketplace.
* Przepływ aktywacji: Wydawca powiadamia firmę Microsoft o tym, że nowo zakupione konto SaaS zostało skonfigurowane na stronie wydawcy.
* Aktualizacja przepływu: zmiana zakupionego planu i/lub liczby zakupionych stanowisk.
* Wstrzymywanie i przywracanie przepływu: Wstrzymywanie zakupionej oferty SaaS w przypadku, gdy metoda płatności klienta nie jest już prawidłowa. Zawieszoną ofertę można przywrócić po rozwiązaniu problemu z metodą płatności.
* Przepływy elementu webhook: Firma Microsoft powiadomi wydawcę o zmianach subskrypcji SaaS i odwołaniu wywołanym przez klienta po stronie firmy Microsoft.

W przypadku anulowania zakupionej subskrypcji SaaS integracja jest opcjonalna, ponieważ może zostać wykonana przez klienta po stronie firmy Microsoft.

Poprawna integracja z interfejsami API realizacji SaaS ma kluczowe znaczenie dla zapewnienia, że

* klienci końcowi, którzy kupili ofertę SaaS wydawcy, są rozliczani prawidłowo przez firmę Microsoft.
* klienci końcowi uzyskują poprawność zakupów, konfigurowania i używania subskrypcji SaaS zakupionych w witrynie Marketplace oraz zarządzania nimi.

Te interfejsy API umożliwiają wydawcom uczestnictwo we wszystkich kanałach z obsługą handlu:

* bezpośrednie
* Partner — dioda (odsprzedawca, dostawca CSP)
* z obsługą pola

W scenariuszu Odsprzedawcy (CSP) dostawca CSP kupuje ofertę SaaS w imieniu klienta końcowego. Klient powinien korzystać z oferty SaaS, ale dostawca CSP jest jednostką, która wykonuje następujące czynności:

* Rozliczanie klienta
* Zmiana planów subskrypcji/ilości zakupionych stanowisk
* Anulowanie subskrypcji

W tym scenariuszu Wydawca nie musi implementować żadnego z przepływów wywołań interfejsu API.

Aby uzyskać więcej informacji na temat dostawcy usług kryptograficznych, zapoznaj się z tematem https://partner.microsoft.com/licensing .

>[!Warning]
>Bieżąca wersja tego interfejsu API jest w wersji 2, która powinna być używana dla wszystkich nowych ofert SaaS. Wersja 1 interfejsu API jest przestarzała i jest utrzymywana do obsługi istniejących ofert.

>[!Note]
>Interfejsy API realizacji SaaS są przeznaczone tylko do wywoływania z poziomu usługi zaplecza wydawcy. Integracja z interfejsami API bezpośrednio ze strony sieci Web wydawcy nie jest obsługiwana. Należy używać tylko przepływu uwierzytelniania między usługami.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, zarejestruj swoją aplikację SaaS w [Azure Portal](https://ms.portal.azure.com) zgodnie z opisem w temacie [Rejestrowanie aplikacji usługi Azure AD](./pc-saas-registration.md).  Następnie użyj najnowszej wersji tego interfejsu na potrzeby programowania: [SaaS realizacji interfejsu API w wersji 2](./pc-saas-fulfillment-api-v2.md).
