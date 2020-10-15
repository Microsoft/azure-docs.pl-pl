---
title: Umowa standardowa dla portalu komercyjnego firmy Microsoft
description: Kontrakt standardowy dla portalu Azure Marketplace i AppSource w centrum partnerskim
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/20/2020
ms.openlocfilehash: 3079cb18f7bc88d691081a7970f5833e8b8d6887
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078191"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Umowa standardowa dla portalu komercyjnego firmy Microsoft

Firma Microsoft oferuje standardową umowę na potrzeby portalu komercyjnego firmy Microsoft. Pozwala to uprościć proces zaopatrzenia dla klientów, zmniejszyć złożoność dla dostawców oprogramowania i ułatwić wykonywanie transakcji w portalu Marketplace. Zamiast przedstawiać niestandardowe warunki i postanowienia, jako komercyjny wydawca portalu Marketplace możesz zaoferować swoje oprogramowanie w ramach [standardowej umowy](https://go.microsoft.com/fwlink/?linkid=2041178), której klienci muszą jedynie Zweryfikuj i akceptować.

Warunki i postanowienia oferty są definiowane podczas tworzenia oferty w centrum partnerskim. Możesz wybrać opcję użycia standardowej umowy dla komercyjnej witryny Marketplace firmy Microsoft zamiast podawania własnych niestandardowych warunków i postanowień.

>[!Note]
>Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft nie będziesz w stanie korzystać ze swoich niestandardowych warunków i postanowień. Możesz zaoferować swoje rozwiązanie w ramach standardowej umowy *lub* własnych warunków i postanowień. Niestandardowe warunki i postanowienia są definiowane na poziomie oferty i są stosowane do wszystkich planów; Napisz niestandardowe warunki i postanowienia na stronie **Właściwości** oferty w centrum partnerskim. Jeśli chcesz zmodyfikować warunki standardowego kontraktu, możesz to zrobić za pomocą standardowych poprawek kontraktu.

## <a name="standard-contract-amendments"></a>Standardowe zmiany kontraktu

Standardowe zmiany umów umożliwiają wydawcom wybranie standardowej kontraktu dla uproszczenia oraz dostosowane warunki dla ich produktu lub firmy. Klienci muszą jedynie przejrzeć zmiany w umowie, jeśli zostali już zrecenzowani i zaakceptowali umowę standardową firmy Microsoft.

Istnieją dwa rodzaje zmian dostępnych dla komercyjnych wydawców portalu Marketplace:

* Uniwersalne zmiany: te zmiany są powszechnie stosowane do standardowej umowy dla wszystkich klientów. Uniwersalne zmiany są wyświetlane dla każdego klienta oferty w przepływie zakupu. Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki.

* Zmiany niestandardowe: te zmiany są specjalnymi zmianami w standardowej umowie, które są przeznaczone dla określonych klientów tylko za pośrednictwem identyfikatorów dzierżaw platformy Azure. Wydawcy mogą wybrać dzierżawcę, do którego chce się kierować. Tylko klienci z dzierżawy będą widzieć niestandardowe warunki zmiany w przepływie zakupu oferty.  Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki.

>[!Note]
>Te dwa typy zmian stosują się między sobą. Klienci z niestandardowymi zmianami otrzymają także uniwersalną zmianę do standardowej umowy podczas zakupu. Zmiany są ograniczone do 4000 znaków, w tym spacji.

Możesz skorzystać z standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft, aby uzyskać następujące typy ofert: aplikacje platformy Azure (szablony rozwiązań i zarządzane aplikacje), Virtual Machines i SaaS.

## <a name="customer-experience"></a>obsługa klienta

W ramach funkcji odnajdywania w witrynie Azure Marketplace lub AppSource klienci będą mogli zobaczyć warunki skojarzone z ofertą jako standardową umowę dla komercyjnego portalu Microsoft Marketplace oraz wszystkie uniwersalne zmiany.

![Azure Portal funkcji wykrywania klienta.](media/marketplace-publishers-guide/azure-discovery-process.png)

W ramach procesu zakupu w Azure Portal klienci będą mogli zapoznać się z postanowieniami związanymi z ofertą jako standardową umową dotyczącą komercyjnego portalu Microsoft Marketplace oraz wszelkimi zmianami specyficznymi dla wszystkich uniwersalnych i/lub dzierżawców.

![Azure Portal możliwości zakupu klientów.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>Interfejs API

Klienci mogą używać Get-AzureRmMarketplaceTerms, aby pobrać warunki oferty i zaakceptować ją. W danych wyjściowych polecenia cmdlet zostanie zwrócony kontrakt standardowy i skojarzone zmiany.
