---
title: Oferty prywatne w witrynie Microsoft Commercial Marketplace
description: Prywatne oferty w komercyjnej witrynie Microsoft Marketplace dla wydawców aplikacji i usług.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 02/22/2021
ms.openlocfilehash: 7c02c41379cfb886fd7e6f84486d815d3cdbfb5b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097180"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Oferty prywatne w portalu komercyjnym firmy Microsoft

Oferty prywatne, nazywane również planami prywatnymi, umożliwiają wydawcom tworzenie planów, które są widoczne tylko dla klientów skierowanych do określonych. W tym artykule omówiono opcje i korzyści z ofert prywatnych.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Odblokuj transakcje przedsiębiorstwa z ofertami prywatnymi

Dzięki utworzeniu ofert prywatnych wydawcy mogą prywatnie oferować dostosowane rozwiązania dla klientów z możliwościami, których wymagają przedsiębiorstwa:

- *Wynegocjowane ceny* umożliwiają wydawcom rozliczanie rabatów i cenników spoza listy dostępnych publicznie.
- Warunki i postanowienia *prywatne* umożliwiają wydawcom Dostosowywanie warunków i postanowień do określonego klienta.
- *Wyspecjalizowane konfiguracje* umożliwiają wydawcom dostosowanie swoich Virtual Machines, aplikacji platformy Azure i oprogramowania jako usługi (SaaS) do potrzeb poszczególnych klientów. Ta opcja umożliwia również wydawcom zapewnienie dostępu do wersji zapoznawczej do nowych funkcji produktu przed ich uruchomieniem wszystkim klientom.

Oferty prywatne pozwalają wydawcom korzystać z skalowalnej i globalnej dostępności publicznej witryny Marketplace, z elastycznością i kontrolą potrzebną do negocjowania i dostarczania niestandardowych ofert i konfiguracji. Przedsiębiorstwa mogą teraz kupować i sprzedawać w oczekiwany sposób.

## <a name="create-private-offers-using-plans"></a>Tworzenie ofert prywatnych przy użyciu planów

W przypadku *nowych lub istniejących ofert z planami* wydawcy mogą łatwo tworzyć nowe, prywatne zmiany przez utworzenie nowych planów (znanych wcześniej jako jednostki SKU) i oznaczenie ich jako prywatnych. Każda oferta może obejmować maksymalnie 45 planów prywatnych.

<!--- [Private SKUs]() --->

Plany prywatne są dostępne dla następujących typów ofert:

- Maszyna wirtualna platformy Azure
- Aplikacja platformy Azure (zaimplementowana jako szablony rozwiązań lub aplikacje zarządzane)
- Usługa zarządzana
- Oferty SaaS

Plany prywatne są składnikiem oferty i są widoczne tylko dla klientów, którzy są jednostek. Plany prywatne są widoczne i jednostek tylko przez klientów, których dotyczy ta funkcja. Plany prywatne można udostępnić klientom zarówno globalnym, jak i Azure Government na platformie Azure.

Plany prywatne mogą ponownie wykorzystać podstawowe obrazy i/lub zaproponować metadane dla planu publicznego. Ta opcja umożliwia wydawcom tworzenie wielu prywatnych odmian oferty publicznej bez konieczności publikowania wielu wersji tego samego obrazu podstawowego i metadanych oferty. W przypadku usługi Azure Virtual Machines i aplikacji platformy Azure tylko w przypadku, gdy plan prywatny udostępnia obraz podstawowy z planem publicznym, wszelkie zmiany obrazu podstawowego oferty będą propagowane dla wszystkich planów publicznych i prywatnych przy użyciu tego obrazu podstawowego.

W przypadku *nowych ofert, które zawierają tylko plany prywatne*, wydawcy mogą utworzyć oferty jako każdą inną ofertę, a następnie oznaczyć plany jako prywatne. Oferty, które mają tylko plany prywatne, nie będą wykrywalne lub dostępne w [Azure Portal](https://azure.microsoft.com/features/azure-portal/) przez klientów, którzy nie zostali powiązani z ofertą.

>[!NOTE]
>Oferta, która zawiera tylko plany prywatne, nie będzie widoczna w publicznym portalu Azure Marketplace lub AppSource.

## <a name="target-customers-with-private-offers"></a>Klienci docelowi z ofertami prywatnymi

W przypadku nowych i istniejących ofert prywatnych wydawcy mogą kierować klientów, którzy korzystają z identyfikatorów subskrypcji. W przypadku ofert dotyczących maszyn wirtualnych platformy Azure, aplikacji platformy Azure i usług zarządzanych wydawcy mogą ograniczyć dostępność planu prywatnego do indywidualnego identyfikatora subskrypcji platformy Azure lub przekazać plik CSV o wartości do 10 000 identyfikatorów subskrypcji platformy Azure. W przypadku ofert SaaS wydawcy mogą skojarzyć identyfikator dzierżawy Azure Active Directory, aby ograniczyć dostępność planu prywatnego przy użyciu metody przekazywania ręcznego lub woluminu CSV.

Po zacertyfikowaniu i opublikowaniu oferty klienci mogą zaktualizować lub usunąć ją z planu przy użyciu funkcji Synchronizuj subskrypcje prywatne. Ta funkcja umożliwia wydawcom szybkie i łatwe aktualizowanie listy klientów, do których plan prywatny jest prezentowany bez certyfikowania lub publikowania oferty.

## <a name="deploying-private-offers"></a>Wdrażanie ofert prywatnych

Po zalogowaniu się do Azure Portal klienci mogą wykonać te kroki, aby wybrać własne oferty.

1. Zaloguj się do [Azure Portal](https://ms.portal.azure.com/).
1. W obszarze **usługi platformy Azure** wybierz pozycję **Utwórz zasób**.
1. Na **nowej** stronie obok pozycji **Azure Marketplace** wybierz pozycję **Zobacz wszystko**. Zostanie wyświetlona strona Marketplace.
1. Na lewym pasku nawigacyjnym wybierz pozycję **oferty prywatne**.

> [!NOTE]
> Oferty prywatne są wykrywalne tylko w [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Nie są one wyświetlane w [Microsoft AppSource](https://appsource.microsoft.com/) ani w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com). Aby dowiedzieć się więcej o publikowaniu w różnych komercyjnych sklepach online Marketplace, zobacz [wprowadzenie do wyświetlania opcji](./determine-your-listing-type.md).

Oferty prywatne będą również wyświetlane w wynikach wyszukiwania i mogą być wdrażane za pomocą szablonów wiersza polecenia i Azure Resource Manager, takich jak wszystkie inne oferty.

[![[Oferty prywatne pojawiają się w wynikach wyszukiwania.]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

Oferty prywatne będą również wyświetlane w wynikach wyszukiwania. Po prostu poszukaj wskaźnika **prywatnego** .

>[!Note]
>Oferty prywatne nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu w programie Cloud Solution Provider (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
