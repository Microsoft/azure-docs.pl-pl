---
title: Określanie opcji publikowania — Microsoft Commercial Marketplace
description: W tym artykule opisano kryteria kwalifikujące i wymagania dotyczące publikowania ofert do Microsoft AppSource i witryny Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 37647a9591c0f686e4fc3f1fd858baa46e01f7ac
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498076"
---
# <a name="determine-your-publishing-option"></a>Określanie opcji publikowania

Opcja publikowania wybrana dla oferty odnosi się bezpośrednio zarówno do wymagań dotyczących uprawnień, jak i komercyjnych korzyści GTM na rynku. Co ważniejsze, wybór opcji publikowania i typu oferty definiuje sposób, w jaki użytkownicy będą korzystać z komercyjnej oferty portalu Marketplace.

Aby skonfigurować ofertę, musisz zrozumieć następujące kluczowe koncepcje dotyczące portalu Marketplace: Opcje publikowania, typy ofert i konfiguracji oraz wywołania do akcji, które określają, jak i gdzie oferta zostanie przedstawiona w portalu komercyjnej witryny Marketplace.

Z tego artykułu dowiesz się:

- Jak określić odpowiedni sklep dla swojego rozwiązania
- Które opcje publikowania i wywołania do akcji są dostępne w każdej sklepie
- Jakie typy ofert są dostępne dla każdej opcji publikowania

## <a name="commercial-marketplace-publishing-options"></a>Komercyjne opcje publikacji Marketplace

W poniższej tabeli przedstawiono opcje publikowania dla typów ofert w Microsoft AppSource i witrynie Azure Marketplace.

|   | **Lista (kontakt)**  | **Lista (wersja próbna)**  | **Bezpłatna** | **BYOL** | **Transakcja**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Maszyna wirtualna** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplikacje platformy Azure (wiele maszyn wirtualnych)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Obraz kontenera** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Moduł IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Zarządzane usługi** |  |  |  | Azure Marketplace |   |
| **Usługi konsultingowe** | Obie witryny sklepu |  |  |  |   |
| **Aplikacja SaaS** | Obie witryny sklepu | Obie witryny sklepu | Obie witryny sklepu |  | Obie witryny sklepu * |
| **Aplikacja Microsoft 365** | AppSource | AppSource |  |  | AppSource * *  |
| **Dodatek Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

Oferty w usłudze SaaS App Transact w Microsoft AppSource są obecnie tylko kartą kredytową. &#42;

&#42;&#42; Microsoft 365 oferty są bezpłatne do zainstalowania i mogą być sprzedawane deweloperom zewnętrznym za pośrednictwem oferty SaaS jako usługi licencjonowania. Aby uzyskać więcej informacji, zobacz [Zarabiaj The Office 365 dodatek za pomocą komercyjnej witryny Microsoft Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-call-to-action"></a>Wybierz wywołanie akcji

Dostępne opcje publikowania oferują zróżnicowane zaangażowanie klientów, zapewniając dostęp do udostępniania potencjalnych klientów i [komercyjnych korzyści z witryny Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Zanotuj wywołania do akcji, które odpowiadają opcji publikowania:

| **Opcja publikowania**    | **Opis**  |
| :------------------- | :-------------------|
| **Lista** | Prosta lista aplikacji lub usług, które umożliwiają komercyjnemu użytkownikowi portalu Marketplace zażądać połączenia z klientem za pośrednictwem wywołania do akcji **kontaktowania się ze mną** . |
| **Wersja próbna** | Skorzystaj z komercyjnej witryny Marketplace, aby zwiększyć możliwości wykrywania i automatyzowania aprowizacji wersji próbnej rozwiązania, dzięki czemu potencjalni użytkownicy mogą bezpłatnie korzystać z usługi SaaS, IaaS lub firmy Microsoft bez opłat przez ograniczony czas przed zakupem. Opcja wywołania do akcji używana dla opcji publikowania wersji próbnej to **bezpłatna wersja próbna** lub **testowa**. |
| **BYOL** | Skorzystaj z komercyjnej witryny Marketplace, aby zwiększyć możliwości wykrywania i automatyzowania aprowizacji rozwiązania oraz dokończyć transakcję finansowo. Typy ofert BYOL doskonale sprawdzają się w przypadku migracji do chmury. Wywołanie do akcji jest **teraz odbierane**.
| **Transakcja** | Oferty Transact są sprzedawane za pomocą komercyjnej witryny Marketplace. Firma Microsoft jest odpowiedzialna za rozliczanie i zbieranie danych. Wywołanie do akcji jest **teraz odbierane**.|

> [!Note]
> W przypadku korzystania z opcji publikacji Transact należy poznać zagadnienia dotyczące cen, rozliczeń, fakturowania i wypłaty przed wybraniem typu oferty i utworzeniem oferty. Aby dowiedzieć się więcej, zapoznaj się z artykułem [komercyjne funkcje Transact Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-a-storefront"></a>Wybieranie witryny sklepu

Każda witryna sklepu obsługuje unikatowe wymagania klienta i kieruje określonych odbiorców. Typ oferty, funkcje Transact i Kategoria określają, gdzie Twoja oferta zostanie opublikowana. Kategorie i podkategorie są mapowane do poszczególnych sklepów w oparciu o docelowych odbiorców:

**Microsoft AppSource** są przeznaczone dla użytkowników biznesowych szukających rozwiązań biznesowych lub branżowych oraz usług konsultingowych dla systemu Dynamics 365, Microsoft 365 i platformy.

**Portal Azure Marketplace** przeznaczony dla informatyków i deweloperów szukających rozwiązań opartych na platformie Azure, a także usług konsultingowych, które przyspieszają korzystanie z platformy Azure.

Wybierz kategorię i podkategorię, które najlepiej odpowiadają docelowym odbiorcom. Na przykład Zapora aplikacji sieci Web powinna zostać opublikowana w witrynie Azure Marketplace w kategorii zabezpieczenia, ponieważ zamierzone odbiorcy to specjaliści IT. Aplikacja do zarządzania kontraktami powinna zostać opublikowana w usłudze AppSource w kategorii sprzedaż, ponieważ zamierzone odbiorcy są użytkownikami biznesowymi. Wybranie nieprawidłowej kategorii lub podkategorii może spowodować opublikowanie oferty w niewłaściwym sklepie.

### <a name="publishing-to-both-storefronts-saas-offers-only"></a>Publikowanie w obu sklepach (tylko oferty SaaS)

Oferty SaaS można publikować w witrynie Azure Marketplace lub AppSource. Jeśli oferta SaaS jest *przeznaczona dla odbiorców* technicznych (Azure Marketplace) i Business odbiorca (AppSource), wybierz kategorię i/lub podkategorię mającą zastosowanie do każdego sklepu. Oferty opublikowane w obu firmach powinny mieć propozycję wartości, która wykracza poza specjalistów IT *i* użytkowników biznesowych.

> [!IMPORTANT]
> Oferty SaaS z naliczanymi opłatami są dostępne w witrynie Azure Marketplace i Azure Portal. Oferty SaaS z tylko planami prywatnymi są dostępne za pomocą Azure Portal.

| Naliczanie opłat | Plan publiczny | Plan prywatny | Dostępne w: |
|---|---|---|---|
| Tak             | Yes         | Nie           | Portal Azure Marketplace i Azure Portal |
| Tak             | Tak         | Tak          | Azure Marketplace i Azure Portal * |
| Tak             | Nie          | Tak          | Tylko Azure Portal |
| Nie              | Nie          | Tak          | Tylko Azure Portal |

&#42; plan prywatny oferty będzie dostępny tylko za pośrednictwem Azure Portal

Na przykład oferta z naliczaniem opłatowym i tylko planem prywatnym (bez planu publicznego) zostanie zakupiona przez klientów w Azure Portal. Dowiedz się więcej o [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](private-offers.md).

### <a name="categories"></a>Kategorie

Kategorie i podkategorie są mapowane na poszczególne witryny sklepu w oparciu o docelową grupę odbiorców. Wybierz kategorie i podkategorie, które najlepiej dopasowują się do oferty i zamierzonych odbiorców. Dostępne są następujące opcje:

- Co najmniej jeden i maksymalnie dwie kategorie. Istnieje możliwość wybrania podstawowej i pomocniczej kategorii.
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli nie wybierzesz żadnej podkategorii, oferta nadal będzie wykrywalna w ramach wybranej kategorii.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Następne kroki

- Po wybraniu opcji publikowania możesz [wybrać typ oferty](./publisher-guide-by-offer-type.md) , który będzie używany do prezentowania oferty.
- Zapoznaj się z wymaganiami dotyczącymi kwalifikacji w sekcji Opcje publikowania według typu oferty, aby zakończyć wybór i konfigurację oferty.
- Zapoznaj się z wzorcem publikowania według witryny sklepu, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.
