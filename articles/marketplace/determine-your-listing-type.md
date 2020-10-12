---
title: Określanie opcji publikowania — Microsoft Commercial Marketplace
description: W tym artykule opisano kryteria kwalifikujące i wymagania dotyczące publikowania ofert do Microsoft AppSource i witryny Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: 4acdd8bfd567a317b954d58629468fa8cd929669
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032388"
---
# <a name="determine-your-publishing-option"></a>Określanie opcji publikowania

Opcja publikowania wybrana dla oferty odnosi się bezpośrednio zarówno do wymagań dotyczących uprawnień, jak i komercyjnych korzyści GTM na rynku. Co ważniejsze, wybór opcji publikowania i typu oferty definiuje sposób, w jaki użytkownicy będą korzystać z komercyjnej oferty portalu Marketplace.

Aby skonfigurować swoją ofertę, musisz zrozumieć następujące kluczowe koncepcje dotyczące portalu Marketplace: Opcje publikowania, typy ofert i konfiguracji oraz informacje o opcjach, które będą decydować, w jaki sposób i gdzie oferta zostanie przedstawiona w komercyjnych sklepach online w portalu Marketplace.

Z tego artykułu dowiesz się:

- Jak ustalić odpowiedni sklep online dla Twojego rozwiązania.
- Opcje publikowania i opcje wyświetlania są dostępne w każdym sklepie online.
- Jakie typy ofert są dostępne dla każdej opcji publikacji.

## <a name="commercial-marketplace-publishing-options"></a>Komercyjne opcje publikacji Marketplace

W poniższej tabeli przedstawiono opcje publikowania dla typów ofert w Microsoft AppSource i witrynie Azure Marketplace.

|   | **Lista (kontakt)**  | **Lista (wersja próbna)**  | **Bezpłatna** | **BYOL** | **Transakcja**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Maszyna wirtualna** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplikacje platformy Azure (wiele maszyn wirtualnych)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Obraz kontenera** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Moduł IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Zarządzane usługi** |  |  |  | Azure Marketplace |   |
| **Usługi konsultingowe** | Oba Sklepy online |  |  |  |   |
| **Aplikacja SaaS** | Oba Sklepy online | Oba Sklepy online | Oba Sklepy online |  | Oba Sklepy online * |
| **Aplikacja Microsoft 365** | AppSource | AppSource |  |  | AppSource * *  |
| **Dodatek Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

Oferty w usłudze SaaS App Transact w Microsoft AppSource są obecnie tylko kartą kredytową. &#42;

&#42;&#42; Microsoft 365 oferty są bezpłatne do zainstalowania i mogą być sprzedawane deweloperom zewnętrznym za pośrednictwem oferty SaaS jako usługi licencjonowania. Aby uzyskać więcej informacji, zobacz [Zarabiaj dodatku Microsoft 365 za pomocą komercyjnego portalu Microsoft Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-listing-option"></a>Wybierz opcję wyświetlania listy

Dostępne opcje aukcji oferują zróżnicowane zaangażowanie klientów, zapewniając dostęp do udostępniania potencjalnych klientów i [komercyjnych korzyści z witryny Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Zwróć uwagę na listę opcji, które odpowiadają opcji publikowania:

| **Opcja publikowania**    | **Opis**  |
| :------------------- | :-------------------|
| **Lista** | Prosta lista aplikacji lub usług umożliwiających komercyjnemu użytkownikowi portalu Marketplace zażądanie połączenia z klientem za pośrednictwem opcji wyświetlania listy **kontaktów** . |
| **Wersja próbna** | Skorzystaj z komercyjnej witryny Marketplace, aby zwiększyć możliwości wykrywania i automatyzowania aprowizacji wersji próbnej rozwiązania, dzięki czemu potencjalni użytkownicy mogą bezpłatnie korzystać z usługi SaaS, IaaS lub firmy Microsoft bez opłat przez ograniczony czas przed zakupem. Opcja tworzenia listy dla opcji publikowania wersji próbnej to **bezpłatna wersja próbna** lub **testowa**. |
| **BYOL** | Skorzystaj z komercyjnej witryny Marketplace, aby zwiększyć możliwości wykrywania i automatyzowania aprowizacji rozwiązania oraz dokończyć transakcję finansowo. Typy ofert BYOL doskonale sprawdzają się w przypadku migracji do chmury. Opcja wystaw to **teraz**.
| **Transakcja** | Oferty Transact są sprzedawane za pomocą komercyjnej witryny Marketplace. Firma Microsoft jest odpowiedzialna za rozliczanie i zbieranie danych. Opcja wystaw to **teraz**.|

> [!Note]
> W przypadku korzystania z opcji publikacji Transact należy poznać zagadnienia dotyczące cen, rozliczeń, fakturowania i wypłaty przed wybraniem typu oferty i utworzeniem oferty. Aby dowiedzieć się więcej, zapoznaj się z artykułem [komercyjne funkcje Transact Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-an-online-store"></a>Wybieranie sklepu online

Każdy sklep online obsługuje różne wymagania klienta dotyczące rozwiązań firmowych i IT. Typ oferty, funkcje Transact i Kategoria określają, gdzie Twoja oferta zostanie opublikowana. Kategorie i podkategorie są mapowane na każdy sklep online w oparciu o typ publikowanego rozwiązania:

**Microsoft AppSource** oferuje rozwiązania biznesowe, takie jak rozwiązania branżowe i usługi doradcze, dla systemu Dynamics 365, Microsoft 365 i platformy.

**Portal Azure Marketplace** oferuje rozwiązania IT przeznaczone dla systemu Azure, a także usługi doradcze, które przyspieszają korzystanie z platformy Azure przez klientów.

Wybierz kategorię i podkategorię, która najlepiej odpowiada typowi rozwiązania. Na przykład Zapora aplikacji sieci Web to rozwiązanie IT, które powinno zostać opublikowane w portalu Azure Marketplace w kategorii zabezpieczenia. Aplikacja do zarządzania kontraktami to rozwiązanie biznesowe, które powinno zostać opublikowane w AppSource w kategorii sprzedaż. Wybranie nieprawidłowej kategorii lub podkategorii może spowodować opublikowanie oferty w nieprawidłowym sklepie online.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publikowanie w sklepach online (tylko oferty SaaS)

Oferty SaaS można publikować w witrynie Azure Marketplace lub AppSource. Jeśli Twoja oferta *SaaS to rozwiązanie* IT (Azure Marketplace) i rozwiązanie biznesowe (AppSource), wybierz kategorię i/lub podkategorię mającą zastosowanie do każdego sklepu online. Oferty opublikowane w ramach obu magazynów online powinny mieć wartość propozycja jako rozwiązanie IT *i* rozwiązanie biznesowe.

> [!IMPORTANT]
> Oferty SaaS z [naliczanymi opłatami](partner-center-portal/saas-metered-billing.md) są dostępne w witrynie Azure Marketplace i Azure Portal. Oferty SaaS z tylko planami prywatnymi są dostępne za pomocą Azure Portal.

| Naliczanie opłat | Plan publiczny | Plan prywatny | Dostępne w: |
|---|---|---|---|
| Tak             | Tak         | Nie           | Portal Azure Marketplace i Azure Portal |
| Tak             | Tak         | Tak          | Azure Marketplace i Azure Portal * |
| Tak             | Nie          | Tak          | Tylko Azure Portal |
| Nie              | Nie          | Tak          | Tylko Azure Portal |

&#42; plan prywatny oferty będzie dostępny tylko za pośrednictwem Azure Portal

Na przykład oferta z naliczaniem opłatowym i tylko planem prywatnym (bez planu publicznego) zostanie zakupiona przez klientów w Azure Portal. Dowiedz się więcej o [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](private-offers.md).

### <a name="categories"></a>Kategorie

Kategorie i podkategorie są mapowane na każdy sklep online na podstawie typu rozwiązania. Wybierz kategorie i podkategorie, które najlepiej dopasowują się do rozwiązania. Dostępne są następujące opcje:

- Co najmniej jeden i maksymalnie dwie kategorie. Można wybrać kategorię podstawową i pomocniczą.
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli nie wybierzesz żadnej podkategorii, oferta nadal będzie wykrywalna w ramach wybranej kategorii.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Następne kroki

- Po wybraniu opcji publikowania zapoznaj się z tematem [Publikowanie podręcznika według typu oferty](./publisher-guide-by-offer-type.md).
