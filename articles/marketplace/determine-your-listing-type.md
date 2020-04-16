---
title: Określanie opcji publikowania w witrynach sklepu komercyjnego w witrynie firmy Microsoft
description: W tym artykule opisano kryteria uprawnień i wymagania dotyczące publikowania dla partnerów próbujących zrozumieć, jak publikować aplikacje w witrynie Microsoft AppSource i azure marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e91ca0334276dbe9118eb0e40d042f598e8b3c0f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415735"
---
# <a name="determine-your-publishing-option"></a>Określanie opcji publikowania

Opcja publikowania wybrana dla oferty odnosi się bezpośrednio zarówno do wymagań kwalifikacyjnych, jak i korzyści GTM na rynku komercyjnym. Co ważniejsze, wybór opcji publikowania i typu oferty określa, w jaki sposób użytkownicy będą wchodzić w interakcje z ofertą komercyjnego rynku.

Aby skonfigurować ofertę, musisz zrozumieć następujące kluczowe pojęcia dotyczące rynku komercyjnego: opcje publikowania, typy ofert i konfigurację oraz wezwania do działania, które będą regulować sposób i miejsce prezentowania oferty w witrynach sklepu na rynku komercyjnym.

Z tego artykułu dowiesz się:

- Jak określić odpowiednią witrynę sklepową dla danego rozwiązania
- Które opcje publikowania i wezwania do działania są dostępne w każdej witrynie sklepu
- Jakie typy ofert są dostępne dla każdej opcji publikowania

## <a name="commercial-marketplace-publishing-options"></a>Opcje publikowania w rynku komercyjnym

W poniższej tabeli przedstawiono opcje publikowania typów ofert w witrynach Microsoft AppSource i Azure Marketplace.

|   | **Lista (kontakt)**  | **Lista (wersja próbna)**  | **Bezpłatna** | **PRZEZOCHUSZ** | **Transact**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Maszyna wirtualna** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Usługi Azure Apps (wiele maszyn wirtualnych)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Obraz kontenera** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Moduł IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Usługi zarządzane** |  |  |  | Azure Marketplace |   |
| **Usługi konsultingowe** | Obie witryny sklepowe |  |  |  |   |
| **Aplikacja SaaS** | Obie witryny sklepowe | Obie witryny sklepowe | Obie witryny sklepowe |  | Obie witryny sklepowe* |
| **Aplikacja Microsoft 365** | AppSource | AppSource |  |  | Źródło aplikacji**  |
| **Dodatek Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; oferty transact aplikacji SaaS w usłudze Microsoft AppSource są obecnie tylko kartą kredytową.

&#42;&#42; oferty usługi Microsoft 365 są bezpłatne i mogą być monetyzowane za pośrednictwem oferty SaaS jako usługi licencjonowania. Aby uzyskać więcej informacji, zobacz [Zarabianie na dodatku usługi Office 365 za pośrednictwem komercyjnego portalu Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)marketplace .

## <a name="selecting-a-storefront"></a>Wybieranie witryny sklepowej

Przed wybraniem opcji publikowania należy zapoznać się z wymaganiami dotyczącymi uprawnień sklepu dla rozwiązań, aplikacji i usług w witrynie komercyjnej. Każda witryna sklepowa spełnia unikatowe wymagania klientów i jest kierowana do określonych odbiorców. Typ oferty, możliwości transakcji oraz kategoria lub branża określą, gdzie opublikować ofertę.

Aplikacje **Microsoft AppSource** to rozwiązania biznesowe, które można zbudować na platformie Azure lub w celu: Dynamics 365, Office 365, Power BI lub Power Apps. Usługi konsultingowe AppSource to profesjonalne usługi, które pomagają klientom rozpocząć korzystanie z dynamics 365 i usługi Power BI lub przyspieszyć korzystanie z nich.

Aplikacje **portalu Azure Marketplace** to techniczne rozwiązania oparte na bloku konstrukcyjnym wbudowane lub wbudowane w platformę Azure i przeznaczone dla odbiorców it lub deweloperów. Usługi konsultingowe portalu Azure Marketplace to profesjonalne usługi, które pomagają klientom rozpocząć korzystanie z platformy Azure lub przyspieszyć korzystanie z platformy Azure.

>[!Note]
>Lista krzyżowa (tylko w przypadku aplikacji SaaS): gdy lista lub oferta oparta na wersji próbnej spełnia kryteria zarówno dla odbiorców technicznych, jak i biznesowych, Twoja oferta zostanie wyświetlona w obu witrynach sklepowych. Dowiedz się więcej o opcjach publikowania poniżej.

## <a name="choose-a-publishing-option"></a>Wybieranie opcji publikowania

Dostępne opcje publikowania oferują zróżnicowane zaangażowanie klientów, jednocześnie zapewniając dostęp do udostępniania potencjalnych klientów i [korzyści na rynku komercyjnym.](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits) Zwróć uwagę na wezwania do działania, które odpowiadają opcji publikowania:

| **Opcja publikowania**    | **Opis**  |
| :------------------- | :-------------------|
| **Listy** | Prosta lista aplikacji lub usługi, która umożliwia użytkownikowi komercyjnego rynku żądanie połączenia się z klientem za pośrednictwem wezwania do działania **Kontakt ze mną.** |
| **Wersja próbna** | Korzystaj z komercyjnego rynku, aby zwiększyć wykrywalność i zautomatyzować aprowizacji rozwiązania wersji próbnej, umożliwiając potencjalnym użytkownikom korzystanie z usług SaaS, IaaS lub microsoft w aplikacji bez żadnych kosztów przez ograniczony czas przed zakupem. Wezwania do działania używane dla opcji publikowania próbnego są **bezpłatną wersję próbną** lub **jazdę próbną.** |
| **PRZEZOCHUSZ** | Skorzystaj z komercyjnego rynku, aby zwiększyć wykrywalność i zautomatyzować aprowizacji rozwiązania i zakończyć transakcję finansową oddzielnie. Typy ofert BYOL są idealne do migracji lokalnych do chmury. Wezwanie do działania jest **Get it Now**.
| **Transact** | Oferty transakcji są sprzedawane za pośrednictwem komercyjnego rynku. Firma Microsoft jest odpowiedzialna za rozliczenia i kolekcje. Wezwanie do działania jest **Get it Now**.|

> [!Note]
> Podczas korzystania z opcji publikowania transakcji, ważne jest, aby zrozumieć ceny, rozliczeń, fakturowania i płatności rozważań przed wybraniem typu oferty i utworzeniem oferty. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Możliwości transakcji na rynku komercyjnym](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Następne kroki

- Po wybraniu opcji publikowania możesz wybrać [typ oferty,](./publisher-guide-by-offer-type.md) który będzie używany do prezentacji oferty.
- Przejrzyj wymagania dotyczące uprawnień w sekcji Opcje publikowania według typu oferty, aby sfinalizować wybór i konfigurację oferty.
- Przejrzyj wzorce publikowania według witryny sklepowej, aby zapoznać się z przykładami, w jaki sposób rozwiązanie mapuje typ oferty i konfigurację.
