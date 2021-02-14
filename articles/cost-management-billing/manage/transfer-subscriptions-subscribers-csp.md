---
title: Przenoszenie subskrypcji platformy Azure między subskrybentami i dostawcami usług w chmurze
description: Dowiedz się, jak przenosić subskrypcje platformy Azure między subskrybentami i dostawcami usług w chmurze.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/11/2021
ms.author: banders
ms.openlocfilehash: 63fbf76b2211e530707f3598d176b646c317cc53
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363053"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Przenoszenie subskrypcji platformy Azure między subskrybentami i dostawcami usług w chmurze

W tym artykule przedstawiono ogólną procedurę przenoszenia subskrypcji platformy Azure między partnerami będącymi dostawcami rozwiązań w chmurze (CSP) a ich klientami. Informacje zawarte w tym miejscu są przeznaczone dla subskrybenta platformy Azure, aby pomóc mu we współpracy z partnerem. Informacje, które są używane przez partnerów firmy Microsoft do procesu przenoszenia, zostały udokumentowane w artykule [Dowiedz się, jak przenieść subskrypcje platformy Azure klienta do innego partnera](/partner-center/switch-azure-subscriptions-to-a-different-partner).

Przed rozpoczęciem żądania przeniesienia musisz pobrać lub wyeksportować wszelkie informacje dotyczące kosztów i rozliczeń, które chcesz zachować. Informacje dotyczące rozliczeń i wykorzystania nie są przenoszone razem z subskrypcją. Aby uzyskać więcej informacji na temat eksportowania danych dotyczących zarządzania kosztami, zobacz [Eksportowanie danych i zarządzanie nimi](../costs/tutorial-export-acm-data.md). Aby uzyskać więcej informacji na temat pobierania danych dotyczących faktur i użycia, zobacz [Pobieranie lub wyświetlanie faktury rozliczeniowej i danych dziennego użycia na platformie Azure](download-azure-invoice-daily-usage-date.md).

Jeśli masz jakiekolwiek rezerwacje, po przeniesieniu subskrypcji przestaną one być stosowane. Przed przeniesieniem subskrypcji pamiętaj, aby [anulować wszelkie rezerwacje i uzyskać zwrot pieniędzy](../reservations/exchange-and-refund-azure-reservations.md).

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Przenoszenie subskrypcji EA do partnera CSP

Rozliczający się bezpośrednio partnerzy będący dostawcami usług w chmurze, którzy są certyfikowani jako dostawcy [Azure Expert Managed Services Provider (MSP)](https://partner.microsoft.com/membership/azure-expert-msp), mogą zażądać przeniesienia subskrypcji platformy Azure dla swoich klientów, którzy mają bezpośrednią umowę Enterprise Agreement (EA). Transfery subskrypcji są dozwolone tylko dla klientów, którzy zaakceptowali Umowę z Klientem Microsoft (MCA) i zakupili plan platformy Azure z programem CSP.

Po zatwierdzeniu żądania dostawca usług w chmurze może udostępnić swoim klientom łączną fakturę. Aby dowiedzieć się więcej o tym, jak dostawcy usług w chmurze przenoszą subskrypcje, zobacz [Uzyskiwanie własności rozliczeń subskrypcji platformy Azure dla konta umowy partnerskiej firmy Microsoft](mpa-request-ownership.md).

>[!IMPORTANT]
> Po przeniesieniu subskrypcji EA do partnera dostawcy rozwiązań w chmurze każde zwiększenie limitu przydziału wcześniej zastosowane w subskrypcji EA zostanie zresetowane do wartości domyślnej. Jeśli po przeniesieniu subskrypcji wymagany jest dodatkowy limit przydziału, dostawca rozwiązań w chmurze musi przesłać żądanie [zwiększenia limitu przydziału](../../azure-portal/supportability/regional-quota-requests.md). 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Przenoszenie innych subskrypcji do partnera będącego dostawcą usług w chmurze CSP

Aby przenieść wszelkie inne subskrypcje platformy Azure do partnera będącego dostawcą usług w chmurze, subskrybent musi przenieść zasoby z subskrypcji źródłowych do subskrypcji dostawcy usług w chmurze. Poniższe wskazówki pokazują, jak przenosić zasoby między subskrypcjami.

1. Ustanów [relację odsprzedawcy](/partner-center/request-a-relationship-with-a-customer) z klientem. Zapoznaj się z [omówieniem regionalnej autoryzacji dostawcy usług kryptograficznych](/partner-center/regional-authorization-overview) , aby upewnić się, że wszyscy klienci i partnerzy znajdują się w tych samych uprawnionych
1. Aby utworzyć docelowe subskrypcje dostawcy usług w chmurze platformy Azure, należy współpracować z partnerem będącym dostawcą usług w chmurze.
1. Upewnij się, że źródłowa i docelowa subskrypcja dostawcy usług w chmurze znajdują się w tej samej dzierżawie usługi Azure Active Directory (Azure AD).  
    Nie możesz zmienić dzierżawy usługi Azure AD dla subskrypcji dostawcy usług w chmurze platformy Azure. Zamiast tego musisz dodać lub skojarzyć subskrypcję źródłową z dzierżawą usługi Azure AD dostawcy usług w chmurze. Aby uzyskać więcej informacji, zobacz [Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub jej dodawanie do niej](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Po skojarzeniu subskrypcji z innym katalogiem usługi Azure AD użytkownicy, którzy mają przypisane role za pomocą [kontroli dostępu na podstawie ról platformy Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md), utracą dostęp. Klasyczni administratorzy subskrypcji, w tym administrator usługi i współadministratorzy, również utracą dostęp.
    > - Przypisania zasad są również usuwane z subskrypcji, gdy subskrypcja jest kojarzona z innym katalogiem.
1. Konto użytkownika używane do przenoszenia musi mieć dostęp właściciela [RBAC platformy Azure](add-change-subscription-administrator.md) do obu subskrypcji.
1. Przed rozpoczęciem [zweryfikuj](/rest/api/resources/resources/validatemoveresources), czy wszystkie zasoby platformy Azure mogą zostać przeniesione z subskrypcji źródłowej do subskrypcji docelowej.  
    Niektórych zasobów platformy Azure nie można przenosić między subskrypcjami. Aby wyświetlić pełną listę zasobów platformy Azure, które można przenieść, zobacz [Obsługa operacji przenoszenia dla zasobów](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Dostawca usług w chmurze platformy Azure obsługuje tylko zasoby usługi Azure Resource Manager. Jeśli jakiekolwiek zasoby platformy Azure w subskrypcji źródłowej zostały utworzone przy użyciu klasycznego modelu wdrażania platformy Azure, przed rozpoczęciem migracji należy zmigrować je do usługi [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm). Aby wyświetlić stronę internetową, musisz być partnerem.

1. Sprawdź, czy wszystkie usługi subskrypcji źródłowej korzystają z modelu usługi Azure Resource Manager. Następnie przenieś zasoby z subskrypcji źródłowej do subskrypcji docelowej przy użyciu funkcji [przenoszenia zasobów platformy Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Przenoszenie zasobów platformy Azure między subskrypcjami może skutkować przestojem usługi w zależności od zasobów w ramach subskrypcji.

## <a name="transfer-csp-subscription-to-other-offer"></a>Przenoszenie subskrypcji dostawcy CSP do innej oferty

Aby przenieść wszelkie inne subskrypcje od partnera będącego dostawcą usług w chmurze do dowolnej innej oferty platformy Azure, subskrybent musi przenieść zasoby między źródłowymi subskrypcjami partnera CSP a subskrypcjami docelowymi.

1. Utwórz docelowe subskrypcje platformy Azure.
1. Upewnij się, że subskrypcje źródłowe i docelowe znajdują się w tej samej dzierżawie usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat zmiany dzierżawy usługi Azure AD, zobacz [Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub dodawanie subskrypcji](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Pamiętaj, że opcja katalogu zmian nie jest obsługiwana w ramach subskrypcji programu CSP. Na przykład przechodzisz z programu CSP na subskrypcję z opcją płatności zgodnie z rzeczywistym użyciem. Aby zapewnić zgodność z katalogiem, należy zmienić katalog subskrypcji z opcją płatności zgodnie z rzeczywistym użyciem.

    > [!IMPORTANT]
    >  - Po skojarzeniu subskrypcji z innym katalogiem użytkownicy, którzy mają przypisane role za pomocą systemu [RBAC platformy Azure](../../role-based-access-control/role-assignments-portal.md), utracą dostęp. Klasyczni administratorzy subskrypcji, w tym administrator usługi i współadministratorzy, również utracą dostęp.
    >  - Przypisania zasad są również usuwane z subskrypcji, gdy subskrypcja jest kojarzona z innym katalogiem.

1. Konto użytkownika używane do przenoszenia musi mieć dostęp właściciela [RBAC platformy Azure](add-change-subscription-administrator.md) do obu subskrypcji.
1. Przed rozpoczęciem [zweryfikuj](/rest/api/resources/resources/validatemoveresources), czy wszystkie zasoby platformy Azure mogą zostać przeniesione z subskrypcji źródłowej do subskrypcji docelowej.
    > [!IMPORTANT]
    >  - Niektórych zasobów platformy Azure nie można przenosić między subskrypcjami. Aby wyświetlić pełną listę zasobów platformy Azure, które można przenieść, zobacz [Obsługa operacji przenoszenia dla zasobów](../../azure-resource-manager/management/move-support-resources.md).

1. Przenieś zasoby z subskrypcji źródłowej do subskrypcji docelowej przy użyciu [przenoszenia zasobów platformy Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Przenoszenie zasobów platformy Azure między subskrypcjami może skutkować przestojem usługi w zależności od zasobów w ramach subskrypcji.

## <a name="next-steps"></a>Następne kroki
- [Uzyskiwanie własności rozliczeń subskrypcji platformy Azure dla konta umowy partnerskiej firmy Microsoft](mpa-request-ownership.md).
- Przeczytaj o tym, jak [zarządzać kontami i subskrypcjami przy użyciu rozliczeń platformy Azure](../index.yml).
