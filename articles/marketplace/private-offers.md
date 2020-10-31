---
title: Oferty prywatne w witrynie Microsoft Commercial Marketplace
description: Prywatne oferty w komercyjnej witrynie Microsoft Marketplace dla wydawców aplikacji i usług.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: 0bb5dccfc99a693ec89ed08ac31b77eb16442f73
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129970"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Oferty prywatne w portalu komercyjnym firmy Microsoft

Oferty prywatne w [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) umożliwiają wydawcom tworzenie planów, które są widoczne tylko dla klientów skierowanych do określonych. W tym artykule omówiono opcje i korzyści z ofert prywatnych.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odblokuj transakcje przedsiębiorstwa z ofertami prywatnymi

Klienci korporacyjni coraz częściej korzystają z rynków online do znajdowania, wypróbowania i kupowania rozwiązań w chmurze. Teraz dzięki ofertom prywatnym wydawcy mogą używać portalu Marketplace do prywatnego udostępniania dostosowanych rozwiązań klientom przeznaczonym dla klientów z możliwościami wymaganymi przez przedsiębiorstwa:

- *Wynegocjowane ceny* umożliwiają wydawcom rozliczanie rabatów i cenników spoza listy dostępnych publicznie.
- Warunki i postanowienia *prywatne* umożliwiają wydawcom Dostosowywanie warunków i postanowień do określonego klienta.
- *Wyspecjalizowane konfiguracje* umożliwiają wydawcom Dostosowywanie Virtual Machines, aplikacji platformy Azure i aplikacji SaaS do potrzeb poszczególnych klientów. Ta opcja umożliwia również wydawcom zapewnienie dostępu do wersji zapoznawczej do nowych funkcji produktu, przed rozpoczęciem szerszego uruchamiania wszystkich klientów.

Oferty prywatne umożliwiają wydawcom skorzystanie z zalet skalowalności i globalnej dostępności publicznej witryny Marketplace, z elastycznością i kontrolą potrzebną do negocjowania i dostarczania niestandardowych transakcji i konfiguracji. Razem te funkcje otwierają drzwi w celu uzyskania silnych rozwiązań korporacyjnych w chmurze. Przedsiębiorstwa mogą teraz kupować i sprzedawać w oczekiwany sposób i popyt.

Oferty prywatne są teraz dostępne dla maszyny wirtualnej, aplikacji platformy Azure (wdrożonej jako szablony rozwiązań lub aplikacji zarządzanych) i oferowanych przez aplikacje SaaS.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Tworzenie ofert prywatnych przy użyciu planów

W przypadku *nowych lub istniejących ofert z planami* wydawcy mogą łatwo tworzyć nowe, prywatne zmiany przez utworzenie nowych planów (znanych wcześniej jako jednostki SKU) i oznaczenie ich jako prywatnych. Każda oferta może obejmować maksymalnie 45 planów prywatnych.

<!--- [Private SKUs]() --->

Plany prywatne są składnikiem oferty i są widoczne tylko dla klientów, którzy są jednostek. Plany prywatne mogą ponownie wykorzystać podstawowe obrazy i/lub zaproponować metadane dla planu publicznego. Ta opcja umożliwia wydawcom tworzenie wielu prywatnych odmian oferty publicznej bez konieczności publikowania wielu wersji tego samego obrazu podstawowego i metadanych oferty. W przypadku maszyn wirtualnych i aplikacji platformy Azure tylko w przypadku, gdy plan prywatny udostępnia obraz podstawowy z planem publicznym, wszelkie zmiany obrazu podstawowego oferty będą propagowane dla wszystkich planów publicznych i prywatnych przy użyciu tego obrazu podstawowego.

W przypadku *nowych ofert, które zawierają tylko plany prywatne* , wydawcy mogą utworzyć oferty jako każdą inną ofertę, a następnie oznaczyć plany jako prywatne. Oferty, które mają tylko plany prywatne, nie będą wykrywalne lub dostępne za pośrednictwem komercyjnej witryny Marketplace firmy Microsoft ani [Azure Portal](https://azure.microsoft.com/features/azure-portal/) przez klientów, którzy nie zostali powiązani z ofertą.

>[!NOTE]
>Oferta, która zawiera tylko plany prywatne, nie będzie widoczna w publicznym portalu Azure Marketplace lub AppSource.

## <a name="targeting-customers-with-private-offers"></a>Kierowanie klientów do ofert prywatnych

W przypadku nowych i istniejących ofert prywatnych wydawcy mogą kierować klientów, którzy korzystają z identyfikatorów subskrypcji. Wydawcy korzystający z maszyny wirtualnej lub oferty aplikacji platformy Azure mogą ograniczyć dostępność planu prywatnego do indywidualnego identyfikatora subskrypcji platformy Azure lub przekazać plik CSV o wartości do 20 000 identyfikatorów subskrypcji platformy Azure. W przypadku korzystania z prywatnej oferty aplikacji SaaS wydawcy mogą skojarzyć identyfikator dzierżawy, aby ograniczyć dostępność planu prywatnego przy użyciu metody przekazywania ręcznego lub woluminu CSV.

Po zacertyfikowaniu i opublikowaniu oferty klienci mogą zaktualizować lub usunąć z planu w ciągu kilku minut, korzystając z funkcji Synchronizuj subskrypcje prywatne. Ta funkcja umożliwia wydawcom szybkie i łatwe aktualizowanie listy klientów, do których plan prywatny jest prezentowany bez certyfikowania lub publikowania oferty.

## <a name="deploying-private-offers"></a>Wdrażanie ofert prywatnych

Oferty prywatne są wykrywalne wyłącznie za pośrednictwem [Azure Portal](https://azure.microsoft.com/features/azure-portal/) i nie są wyświetlane w [Microsoft AppSource](https://appsource.microsoft.com/) lub [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com). Aby dowiedzieć się więcej o publikowaniu w różnych komercyjnych sklepach online portalu Marketplace, zobacz [Określanie opcji publikowania](./determine-your-listing-type.md).

Po zalogowaniu się do Azure Portal klienci mogą wybrać element nawigacyjny portalu Marketplace, aby uzyskać dostęp do swoich ofert prywatnych. Oferty prywatne będą również wyświetlane w wynikach wyszukiwania i można je wdrożyć za pośrednictwem wiersza polecenia i szablonów Azure Resource Manager, takich jak wszystkie inne oferty.

![[Oferty prywatne]](./media/marketplace-publishers-guide/private-offer.png)

Oferty prywatne będą również wyświetlane w wynikach wyszukiwania. Po prostu poszukaj wskaźnika **prywatnego** .

>[!Note]
>Oferty prywatne nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu w programie Cloud Solution Provider (CSP).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->