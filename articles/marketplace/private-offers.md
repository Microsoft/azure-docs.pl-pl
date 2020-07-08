---
title: Oferty prywatne w witrynie Microsoft Commercial Marketplace
description: Prywatne oferty w komercyjnej witrynie Microsoft Marketplace dla wydawców aplikacji i usług.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: e449f65ca65a679aa6c6823938fa561b84e38368
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82872377"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Oferty prywatne w portalu komercyjnym firmy Microsoft

Oferty prywatne w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) umożliwiają wydawcom tworzenie planów, które są widoczne tylko dla klientów skierowanych do określonych. Ten artykuł zawiera informacje na temat opcji i korzyści związanych z ofertami prywatnymi.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odblokuj transakcje przedsiębiorstwa z ofertami prywatnymi

Klienci korporacyjni coraz częściej korzystają z rynków online do znajdowania, wypróbowania i kupowania rozwiązań w chmurze. Teraz dzięki ofertom prywatnym wydawcy mogą używać portalu Marketplace do prywatnego udostępniania dostosowanych rozwiązań klientom przeznaczonym dla klientów z możliwościami wymaganymi przez przedsiębiorstwa:

- *Wynegocjowane ceny* umożliwiają wydawcom rozliczanie rabatów i cenników spoza listy dostępnych publicznie.
- Warunki i postanowienia *prywatne* umożliwiają wydawcom Dostosowywanie warunków i postanowień do określonego klienta.
- *Wyspecjalizowane konfiguracje* umożliwiają wydawcom Dostosowywanie Virtual Machines, aplikacji platformy Azure i aplikacji SaaS do potrzeb poszczególnych klientów. Ta opcja umożliwia również wydawcom zapewnienie dostępu do wersji zapoznawczej do nowych funkcji produktu, przed rozpoczęciem szerszego uruchamiania wszystkich klientów.

Oferty prywatne umożliwiają wydawcom skorzystanie z zalet skalowalności i globalnej dostępności publicznej witryny Marketplace, z elastycznością i kontrolą potrzebną do negocjowania i dostarczania niestandardowych transakcji i konfiguracji. Razem te funkcje otwierają drzwi w celu uzyskania silnych rozwiązań korporacyjnych w chmurze. Przedsiębiorstwa mogą teraz kupować i sprzedawać w oczekiwany sposób i popyt.

Oferty prywatne są teraz dostępne dla maszyny wirtualnej, aplikacji platformy Azure (wdrożonej jako szablony rozwiązań lub aplikacji zarządzanych) i oferowanych przez aplikacje SaaS. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Tworzenie ofert prywatnych przy użyciu jednostek SKU i planów

W przypadku *nowych lub istniejących ofert z publicznymi jednostkami SKU lub planami*wydawcy mogą łatwo tworzyć nowe, prywatne zmiany, tworząc nowe jednostki SKU lub plany i oznacz je jako prywatne.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

Prywatne jednostki SKU i plany są składnikami oferty i są widoczne tylko dla klientów, którzy są jednostek. Prywatne jednostki SKU i plany mogą ponownie używać obrazów bazowych i/lub zaproponować już istniejące metadane dla publicznej jednostki SKU lub planu. Ta opcja umożliwia wydawcom tworzenie wielu prywatnych odmian oferty publicznej bez konieczności publikowania wielu wersji tego samego obrazu podstawowego i metadanych oferty. W przypadku maszyn wirtualnych i aplikacji platformy Azure tylko w przypadku, gdy prywatna jednostka SKU udostępnia obraz podstawowy z publiczną jednostką SKU, wszelkie zmiany obrazu podstawowego oferty będą propagowane dla wszystkich publicznych i prywatnych jednostek SKU przy użyciu tego obrazu podstawowego.

W przypadku *nowych ofert, które zawierają tylko prywatne jednostki SKU lub plany*, wydawcy mogą utworzyć oferty jako każdą inną ofertę, a następnie oznaczyć jednostki SKU lub plany jako prywatne. Oferty, które mają tylko prywatne jednostki SKU lub plany, nie będą wykrywalne lub dostępne za pośrednictwem komercyjnej witryny Marketplace firmy Microsoft ani [Azure Portal](https://azure.microsoft.com/features/azure-portal/) przez klientów, którzy nie zostali powiązani z ofertą.

## <a name="targeting-customers-with-private-offers"></a>Kierowanie klientów do ofert prywatnych

W przypadku nowych i istniejących ofert prywatnych wydawcy mogą kierować klientów, którzy korzystają z identyfikatorów subskrypcji. Wydawcy korzystający z maszyny wirtualnej lub oferty aplikacji platformy Azure mogą ograniczyć dostępność prywatnej jednostki SKU do indywidualnego identyfikatora subskrypcji platformy Azure lub przekazać plik CSV o wartości do 20 000 identyfikatorów subskrypcji platformy Azure. W przypadku korzystania z prywatnej oferty aplikacji SaaS wydawcy mogą skojarzyć identyfikator dzierżawy, aby ograniczyć dostępność planu prywatnego przy użyciu metody przekazywania ręcznego lub woluminu CSV.

Po pobraniu i opublikowaniu oferty klienci mogą zaktualizować lub usunąć ją z jednostki SKU lub zaplanować w ciągu kilku minut, korzystając z funkcji Synchronizuj subskrypcje prywatne. Ta funkcja umożliwia wydawcom szybkie i łatwe aktualizowanie listy klientów, do których zostanie przedstawiona prywatna jednostka SKU lub plan bez poświadczania lub opublikowania oferty.

## <a name="deploying-private-offers"></a>Wdrażanie ofert prywatnych

Oferty prywatne są wykrywalne wyłącznie za pośrednictwem [Azure Portal](https://azure.microsoft.com/features/azure-portal/) i nie są wyświetlane w [Microsoft AppSource](https://appsource.microsoft.com/) lub [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com). Aby dowiedzieć się więcej o publikowaniu w różnych komercyjnych witrynie portalu Marketplace, zobacz [Określanie opcji publikowania](./determine-your-listing-type.md).

Po zalogowaniu się do Azure Portal klienci mogą wybrać element nawigacyjny portalu Marketplace, aby uzyskać dostęp do swoich ofert prywatnych. Oferty prywatne będą również wyświetlane w wynikach wyszukiwania i można je wdrożyć za pośrednictwem wiersza polecenia i szablonów Azure Resource Manager, takich jak wszystkie inne oferty.

![[Oferty prywatne]](./media/marketplace-publishers-guide/private-offer.png)

Oferty prywatne będą również wyświetlane w wynikach wyszukiwania. Po prostu poszukaj wskaźnika "Private".

>[!Note]
>Oferty prywatne nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->