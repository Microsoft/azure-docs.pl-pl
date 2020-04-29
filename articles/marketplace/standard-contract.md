---
title: Kontrakt standardowy | Portal Azure Marketplace
description: Kontrakt standardowy dla portalu Azure Marketplace i AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681444"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardowa umowa dla komercyjnego portalu Microsoft Marketplace

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardową umowę dotyczącą komercyjnej witryny Marketplace firmy Microsoft w celu ułatwienia realizacji transakcji w portalu Marketplace. Zamiast przedstawiać niestandardowe warunki i postanowienia, Komercyjni wydawcy portalu Marketplace mogą zaoferować swoje oprogramowanie zgodnie z umową standardową, której klienci muszą jedynie Zweryfikuj i akceptować. Kontrakt standardowy można znaleźć tutaj: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Warunki i postanowienia oferty są definiowane podczas tworzenia oferty w centrum partnerskim. Możesz wybrać opcję użycia standardowej umowy dla komercyjnej witryny Marketplace firmy Microsoft zamiast podawania własnych niestandardowych warunków i postanowień.

>[!Note]
>Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft nie będziesz w stanie korzystać ze swoich niestandardowych warunków i postanowień. Jest to scenariusz "lub". Możesz zaoferować swoje rozwiązanie w ramach standardowej umowy *lub* własnych warunków i postanowień. Jeśli chcesz zmodyfikować warunki kontraktu standardowego, możesz to zrobić za pomocą standardowych poprawek kontraktu.

## <a name="standard-contract-amendments"></a>Standardowe zmiany kontraktu

Standardowe zmiany umów umożliwiają wydawcom wybranie standardowej kontraktu dla uproszczenia oraz dostosowane warunki dla ich produktu lub firmy. Klienci muszą jedynie przejrzeć zmiany w umowie, jeśli zostali już zrecenzowani i zaakceptowali umowę standardową firmy Microsoft.

Istnieją dwa rodzaje zmian dostępnych dla komercyjnych wydawców portalu Marketplace:

* Uniwersalne zmiany: te zmiany są powszechnie stosowane do standardowej umowy dla wszystkich klientów. Uniwersalne zmiany są wyświetlane dla każdego klienta oferty w przepływie zakupu. Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki.

* Zmiany niestandardowe: te zmiany są specjalnymi zmianami w standardowej umowie, które są przeznaczone dla określonych klientów tylko za pośrednictwem identyfikatorów dzierżaw platformy Azure. Wydawcy mogą wybrać dzierżawcę, do którego chce się kierować. Tylko klienci z dzierżawy będą widzieć niestandardowe warunki zmiany w przepływie zakupu oferty.  Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki.

>[!Note]
>Te dwa typy zmian stosują się między sobą. Klienci z niestandardowymi zmianami otrzymają także uniwersalną zmianę do standardowej umowy podczas zakupu.

Możesz skorzystać z standardowej umowy dotyczącej rynku komercyjnego firmy Microsoft, aby uzyskać następujące typy ofert: aplikacje platformy Azure (szablony rozwiązań i zarządzane aplikacje), Virtual Machines, kontenery, aplikacje kontenerów, moduły IoT Edge i SaaS.

## <a name="customer-experience"></a>obsługa klienta

W ramach funkcji odnajdywania w witrynie Azure Marketplace lub AppSource klienci będą mogli zobaczyć warunki skojarzone z ofertą jako standardową umowę dla komercyjnego portalu Microsoft Marketplace oraz wszystkie uniwersalne zmiany.

![Azure Portal funkcji wykrywania klienta.](media/marketplace-publishers-guide/azure-discovery-process.png)

W ramach procesu zakupu w Azure Portal klienci będą mogli zapoznać się z postanowieniami związanymi z ofertą jako standardową umową dotyczącą komercyjnego portalu Microsoft Marketplace oraz wszelkimi zmianami specyficznymi dla wszystkich uniwersalnych i/lub dzierżawców.

![Azure Portal możliwości zakupu klientów.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>Interfejs API

Klienci mogą używać Get-AzureRmMarketplaceTerms, aby pobrać warunki oferty i zaakceptować ją. W danych wyjściowych polecenia cmdlet zostanie zwrócony kontrakt standardowy i skojarzone zmiany.
