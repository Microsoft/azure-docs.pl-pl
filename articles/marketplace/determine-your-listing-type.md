---
title: Określanie opcji publikowania — Microsoft Commercial Marketplace
description: W tym artykule opisano kryteria kwalifikujące i wymagania dotyczące publikowania ofert do Microsoft AppSource i witryny Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 92a24a59038eb74a6a2f6a738005ff8aadf35ff0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658064"
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

|   | **Lista (kontakt)**  | **Lista (wersja próbna)**  | **Bezpłatna** | **BYOL** | **Transact**|
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

## <a name="selecting-a-storefront"></a>Wybieranie witryny sklepu

Przed wybraniem opcji publikowania należy zapoznać się z wymaganiami dotyczącymi uprawnień witryny Marketplace dotyczącymi rozwiązań komercyjnych, aplikacji i usług. Każda witryna sklepu obsługuje unikatowe wymagania klienta i kieruje określonych odbiorców. Typ oferty, możliwości Transact i Kategoria lub branża określają miejsce, w którym ma zostać opublikowana oferta.

Aplikacje **Microsoft AppSource** są rozwiązaniami biznesowymi, które mogą być wbudowane w platformę Azure lub wbudowaną dla: Dynamics 365, Office 365, Power BI lub aplikacji zaawansowanych. Usługi doradcze AppSource są ofertami usług profesjonalnych, które ułatwiają klientom rozpoczęcie pracy z systemem Dynamics Power BI 365 lub przyspieszeniem korzystania z niego.

Aplikacje w **portalu Azure Marketplace** są technicznymi rozwiązaniami "konstrukcyj-Block" opartymi na platformie Azure i przeznaczonymi dla odbiorców IT lub deweloperów. Usługi doradcze w witrynie Azure Marketplace to profesjonalne oferty usług, które ułatwiają klientom rozpoczęcie pracy z platformą Azure lub przyspieszenie korzystania z niej.

>[!Note]
>Wiele list (tylko w przypadku aplikacji SaaS): gdy lista lub oferta oparta na wersji próbnej spełnia kryteria dla użytkowników technicznych i dla odbiorców, oferta zostanie wyświetlona w obu witrynach. Dowiedz się więcej na temat opcji publikowania poniżej.

## <a name="choose-a-publishing-option"></a>Wybierz opcję publikowania

Dostępne opcje publikowania oferują zróżnicowane zaangażowanie klientów, zapewniając dostęp do udostępniania potencjalnych klientów i [komercyjnych korzyści z witryny Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Zanotuj wywołania do akcji, które odpowiadają opcji publikowania:

| **Opcja publikowania**    | **Opis**  |
| :------------------- | :-------------------|
| **Staw** | Prosta lista aplikacji lub usług, które umożliwiają komercyjnemu użytkownikowi portalu Marketplace zażądać połączenia z klientem za pośrednictwem wywołania do akcji **kontaktowania się ze mną** . |
| **Wersja próbna** | Skorzystaj z komercyjnej witryny Marketplace, aby zwiększyć możliwości wykrywania i automatyzowania aprowizacji wersji próbnej rozwiązania, dzięki czemu potencjalni użytkownicy mogą bezpłatnie korzystać z usługi SaaS, IaaS lub firmy Microsoft bez opłat przez ograniczony czas przed zakupem. Opcja wywołania do akcji używana dla opcji publikowania wersji próbnej to **bezpłatna wersja próbna** lub **testowa**. |
| **BYOL** | Skorzystaj z komercyjnej witryny Marketplace, aby zwiększyć możliwości wykrywania i automatyzowania aprowizacji rozwiązania oraz dokończyć transakcję finansowo. Typy ofert BYOL doskonale sprawdzają się w przypadku migracji do chmury. Wywołanie do akcji jest **teraz odbierane**.
| **Transact** | Oferty Transact są sprzedawane za pomocą komercyjnej witryny Marketplace. Firma Microsoft jest odpowiedzialna za rozliczanie i zbieranie danych. Wywołanie do akcji jest **teraz odbierane**.|

> [!Note]
> W przypadku korzystania z opcji publikacji Transact należy poznać zagadnienia dotyczące cen, rozliczeń, fakturowania i wypłaty przed wybraniem typu oferty i utworzeniem oferty. Aby dowiedzieć się więcej, zapoznaj się z artykułem [komercyjne funkcje Transact Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Następne kroki

- Po wybraniu opcji publikowania możesz [wybrać typ oferty](./publisher-guide-by-offer-type.md) , który będzie używany do prezentowania oferty.
- Zapoznaj się z wymaganiami dotyczącymi kwalifikacji w sekcji Opcje publikowania według typu oferty, aby zakończyć wybór i konfigurację oferty.
- Zapoznaj się z wzorcem publikowania według witryny sklepu, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.
