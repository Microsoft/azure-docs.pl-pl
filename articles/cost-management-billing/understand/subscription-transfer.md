---
title: Informacje na temat przenoszenia własności rozliczeń subskrypcji platformy Azure
description: W tym artykule wyjaśniono zagadnienia, z którymi należy się zapoznać przed przeniesieniem własności rozliczeń subskrypcji platformy Azure na inne konto.
keywords: przeniesienie subskrypcji platformy Azure, przeniesienie subskrypcji platformy Azure na inne konto, zmiana właściciela subskrypcji platformy Azure, transfer subskrypcji platformy Azure na inne konto, transfer rozliczeń na platformie Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: f4dd6d67d60603ed6cad7056cff4bb07dcb1c2e5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149370"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Informacje na temat przenoszenia własności rozliczeń subskrypcji platformy Azure

Ten artykuł ułatwia zapoznanie się z najważniejszymi rzeczami, które trzeba wiedzieć przed przeniesieniem własności rozliczeń subskrypcji platformy Azure na inne konto. 

Możesz chcieć przenieść własność rozliczeń subskrypcji platformy Azure, jeśli opuszczasz organizację lub chcesz, aby Twoja subskrypcja była rozliczana w ramach innego konta. Przeniesienie własności rozliczeń do innego konta zapewnia administratorom nowego konta uprawnienia do zadań związanych z rozliczeniami. Mogą zmieniać formę płatności, wyświetlać opłaty i anulować subskrypcję.

Jeśli chcesz zachować własność rozliczeń, ale zmienić typ subskrypcji, zobacz [Przełączanie subskrypcji platformy Azure na inną ofertę](../manage/switch-azure-offer.md). Aby kontrolować, kto może uzyskać dostęp do zasobów w ramach subskrypcji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Jeśli jesteś klientem z Umową Enterprise (EA), administratorzy w Twoim przedsiębiorstwie mogą przenosić własność rozliczeń subskrypcji między kontami.

Tylko administrator rozliczeń konta może przenieść własność subskrypcji.

## <a name="determine-account-billing-administrator"></a>Określanie administratora rozliczeń konta

<a name="whoisaa"></a>

Administrator rozliczeń to osoba, która ma uprawnienia do zarządzania rozliczeniami konta. Administratorzy mogą uzyskiwać dostęp do rozliczeń w witrynie [Azure Portal](https://portal.azure.com) i wykonywać różne zadania rozliczeniowe, takie jak tworzenie subskrypcji, wyświetlanie i płacenie faktur lub aktualizowanie form płatności.

Aby zidentyfikować konta, dla których jesteś administratorem rozliczeń, odwiedź [stronę Cost Management + Billing w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). Następnie zaznacz opcję **Wszystkie zakresy rozliczeniowe** w okienku po lewej stronie. Na stronie Subskrypcje są wyświetlane wszystkie te subskrypcje, w których jesteś administratorem rozliczeń.

Jeśli nie wiesz, kto jest administratorem konta dla subskrypcji, odwiedź [stronę Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Następnie wybierz subskrypcję, którą chcesz sprawdzić i przejrzyj **Ustawienia**. Po wybraniu pozycji **Właściwości** administrator konta subskrypcji zostanie wyświetlony w polu **Administrator konta**.


## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Przenoszenie subskrypcji w witrynie Azure Portal jest dostępne dla typów subskrypcji wymienionych poniżej. Obecnie przenoszenie nie jest obsługiwane w przypadku [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p/) ani [subskrypcji platformy Azure w ramach programu licencjonowania Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Więcej informacji — zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Aby przenieść inne subskrypcje, takie jak plany pomocy technicznej, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> Za pośrednictwem portalu EA.

<sup>2</sup> Obsługiwane tylko w przypadku kont utworzonych podczas tworzenia konta w witrynie internetowej platformy Azure.

## <a name="resources-transferred-with-subscriptions"></a>Zasoby przenoszone wraz z subskrypcjami

Wszystkie zasoby, takie jak maszyny wirtualne, dyski i witryny internetowe, są przenoszone na nowe konto. Jeśli jednak przeniesiesz subskrypcję do konta w innej dzierżawie usługi Azure AD, żadne [role administratora](../manage/add-change-subscription-administrator.md) ani [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) w ramach subskrypcji nie zostaną przeniesione. Ponadto [rejestracje aplikacji](../../active-directory/develop/quickstart-register-app.md) ani inne usługi specyficzne dla dzierżawy nie są przenoszone razem z subskrypcją.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Przenoszenie własności konta do innego kraju/regionu

Niestety, nie można przenieść subskrypcji między krajami lub regionami przy użyciu witryny Azure Portal. Można je jednak przenieść, jeśli otworzysz wniosek o pomocą techniczną platformy Azure. Aby utworzyć wniosek o pomoc techniczną, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Przenoszenie subskrypcji z jednego konta na inne

Jeśli jesteś administratorem dwóch kont, możesz przenieść subskrypcję między tymi kontami. Twoje konta są koncepcyjnie uznawane za konta dwóch różnych użytkowników, możesz zatem przenieść subskrypcje między kontami.
Aby wyświetlić kroki wymagane do przeniesienia subskrypcji, zobacz [Przeniesienie własności rozliczeń subskrypcji platformy Azure](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>Przenoszenie subskrypcji nie powinno spowodować przestoju

Przeniesienie subskrypcji do konta w tej samej dzierżawie usługi Azure Active Directory nie ma wpływu na zasoby działające w ramach subskrypcji. Jednak informacje kontekstowe zapisane w programie PowerShell nie są aktualizowane, więc może być konieczne ich wyczyszczenie lub zmiana ustawień. Jeśli subskrypcja zostanie przeniesiona do konta w innej dzierżawie, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [przypisaniami ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) umożliwiającymi uzyskanie dostępu do zasobów w ramach subskrypcji utracą dostęp. Może to spowodować przestój usługi.

## <a name="new-account-usage-and-billing-history"></a>Użycie nowego konta i historia rozliczeń

Jedyne informacje dostępne dla użytkowników dotyczące nowego konta to koszt subskrypcji z ostatniego miesiąca. Pozostała część historii użycia i rozliczeń nie jest przenoszona z subskrypcją.

## <a name="manually-migrate-data-and-services"></a>Ręczna migracja danych i usług

Podczas przenoszenia subskrypcji, jej zasoby pozostają razem z nią. Jeśli nie możesz przenieść własności subskrypcji, możesz ręcznie zmigrować jej zasoby. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../../azure-resource-manager/management/move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="remaining-subscription-credits"></a>Pozostałe środki w ramach subskrypcji 

Jeśli masz subskrypcję programu Visual Studio lub Microsoft Partner Network, otrzymujesz miesięczne środki. Twoje środki nie są przenoszone wraz z subskrypcją na nowe konto. Użytkownik akceptujący żądanie przeniesienia musi mieć licencję programu Visual Studio, aby zaakceptować żądanie przeniesienia. Subskrypcja korzysta ze środków programu Visual Studio, które są dostępne na koncie użytkownika. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji programu Visual Studio i Partner Network](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>Użytkownicy zachowują dostęp do przeniesionych zasobów

Należy pamiętać, że użytkownicy z dostępem do zasobów w ramach subskrypcji zachowują go po przeniesieniu własności. Jednak [role administratorów](../manage/add-change-subscription-administrator.md) i [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) mogą zostać usunięte. Utrata dostępu następuje, gdy Twoje konto znajduje się w dzierżawie usługi Azure AD innej niż dzierżawa subskrypcji, a użytkownik, który wysłał żądanie przeniesienia, przenosi subskrypcję do dzierżawy Twojego konta. 

W witrynie Azure Portal możesz wyświetlić użytkowników, którzy mają przypisania ról platformy Azure umożliwiające uzyskanie dostępu do zasobów w ramach subskrypcji. Odwiedź [stronę subskrypcji w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Następnie wybierz subskrypcję, którą chcesz wyświetlić, po czym w lewym okienku wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** . W dalszej kolejności wybierz pozycję **Przypisania ról** w górnej części strony. Na stronie przypisań ról są wyświetleni wszyscy użytkownicy z dostępem do subskrypcji.

Nawet jeśli [przypisania ról platformy Azure](../../role-based-access-control/role-assignments-portal.md) zostaną usunięte podczas przenoszenia, użytkownicy pierwotnego konta właściciela mogą nadal mieć dostęp do subskrypcji za pośrednictwem innych mechanizmów zabezpieczeń, takich jak:

* Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
* Klucze dostępu dla usług, takich jak Storage. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-account-create.md).
* Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do zasobów platformy Azure, powinien rozważyć zaktualizowanie wszystkich wpisów tajnych skojarzonych z usługą. Większość zasobów można zaktualizować. Zaloguj się do witryny [Azure Portal](https://portal.azure.com), a następnie w menu Centrum wybierz pozycję **Wszystkie zasoby**. W dalszej kolejności wybierz zasób. Następnie na stronie zasobu wybierz opcję **Ustawienia**. Możesz tam wyświetlać i aktualizować istniejące wpisy tajne.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>Płatność za użycie po otrzymaniu własności

Twoje konto jest odpowiedzialne za opłacenie użycia zgłoszonego od momentu przeniesienia. Pewna część użycia, która miała miejsce przed przeniesieniem, mogła zostać zgłoszona dopiero po nim. Jest ona ujęta na rachunku konta.

## <a name="use-a-different-payment-method"></a>Używanie innej formy płatności

Akceptując żądanie przeniesienia, możesz wybrać istniejącą formę płatności połączoną z Twoim kontem lub wybrać nową.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Przenoszenie własności subskrypcji Enterprise Agreement

Administrator przedsiębiorstwa może zaktualizować własność dowolnego konta nawet wtedy, gdy pierwotny właściciel nie należy już do organizacji. Aby uzyskać więcej informacji na temat przenoszenia kont z umową Enterprise Agreement platformy Azure, zobacz [Transfery umowy Azure Enterprise](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Przeniesienie własności rozliczeń subskrypcji platformy Azure](../manage/billing-subscription-transfer.md)