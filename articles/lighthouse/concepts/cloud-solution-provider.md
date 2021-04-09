---
title: Zagadnienia dotyczące programu Cloud Solution Provider
description: W przypadku partnerów programu CSP delegowane zarządzanie zasobami systemu Azure pomaga zwiększyć bezpieczeństwo i kontrolę dzięki włączeniu szczegółowych uprawnień.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 8736cf913739f2bd16fb519aed98fd336f6876a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419392"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse i program dostawcy rozwiązań w chmurze

Jeśli jesteś partnerem [CSP (dostawca rozwiązań w chmurze)](/partner-center/csp-overview) , możesz uzyskać dostęp do subskrypcji platformy Azure utworzonych dla klientów za pośrednictwem programu CSP przy użyciu funkcji [Administruj w imieniu (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Ten dostęp pozwala bezpośrednio obsługiwać i konfigurować subskrypcje klientów oraz zarządzać nimi.

[Usługa Azure Lighthouse](../overview.md)umożliwia zarządzanie zasobami delegowanymi na platformie Azure wraz z AOBO. Pozwala to zwiększyć bezpieczeństwo i ograniczyć niepotrzebny dostęp przez umożliwienie użytkownikom bardziej szczegółowych uprawnień. Umożliwia również zwiększenie wydajności i skalowalności, ponieważ użytkownicy mogą korzystać z wielu subskrypcji klienta przy użyciu jednej nazwy logowania w dzierżawie.

> [!TIP]
> Aby pomóc w zabezpieczeniu zasobów klientów, należy zapoznać się z naszymi [zalecanymi rozwiązaniami](recommended-security-practices.md) dotyczącymi zabezpieczeń oraz z [wymaganiami dotyczącymi zabezpieczeń partnerów](/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Administruj w imieniu (AOBO)

W przypadku usługi AOBO każdy użytkownik z rolą [agenta administracyjnego](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) w Twojej dzierżawie będzie miał dostęp AOBO do subskrypcji platformy Azure utworzonych za pomocą programu CSP. Wszyscy użytkownicy, którzy potrzebują dostępu do subskrypcji klientów, muszą być członkami tej grupy. AOBO nie pozwala na elastyczność tworzenia odrębnych grup, które współpracują z różnymi klientami, lub aby włączyć różne role dla grup lub użytkowników.

![Diagram przedstawiający zarządzanie dzierżawcą przy użyciu AOBO.](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Za pomocą usługi Azure Lighthouse można przypisywać różne grupy do różnych klientów lub ról, jak pokazano na poniższym diagramie. Ze względu na to, że użytkownicy będą mieć odpowiedni poziom dostępu za pomocą zarządzania zasobami delegowanymi przez platformę Azure, można zmniejszyć liczbę użytkowników, którzy mają rolę agenta administratora (i w związku z tym mieć pełny dostęp AOBO). Pozwala to zwiększyć bezpieczeństwo przez ograniczenie niepotrzebnego dostępu do zasobów klientów. Zapewnia również większą elastyczność zarządzania wieloma klientami w odpowiedniej skali.

Aby dołączać subskrypcję utworzoną za pomocą programu CSP, wykonaj kroki opisane w sekcji Dołączanie [subskrypcji do usługi Azure Lighthouse](../how-to/onboard-customer.md). Każdy użytkownik, który ma rolę Agent administracyjny w dzierżawie, może wykonać to dołączenie.

![Diagram przedstawiający zarządzanie dzierżawcą przy użyciu usługi AOBO i zarządzania zasobami delegowanymi przez platformę Azure.](../media/csp-2.jpg)

> [!TIP]
> [Oferty usług zarządzanych](managed-services-offers.md) z planami prywatnymi nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu w programie Cloud Solution Provider (CSP). Możesz dołączyć te subskrypcje do usługi Azure Lighthouse za [pomocą szablonów Azure Resource Manager](../how-to/onboard-customer.md).

> [!NOTE]
> Na [stronie **moi klienci** w Azure Portal](../how-to/view-manage-customers.md) teraz znajduje się sekcja **dostawca rozwiązań w chmurze (wersja zapoznawcza)** , która wyświetla informacje o rozliczeniach i zasoby dla klientów programu CSP, którzy [podpisali umowę klienta firmy Microsoft (MCA)](/partner-center/confirm-customer-agreement) i są [objęte planem platformy Azure](/partner-center/azure-plan-get-started). Aby uzyskać więcej informacji, zobacz artykuł Wprowadzenie [do rozliczeń umowy partnerskiej firmy Microsoft](../../cost-management-billing/understand/mpa-overview.md).
>
> Klienci korzystający z programu CSP mogą pojawić się w tej sekcji, niezależnie od tego, czy zostały również dołączone do usługi Azure Lighthouse. Jeśli są one dostępne, pojawią się również w sekcji **klienci** zgodnie z opisem w temacie [Wyświetlanie i zarządzanie klientami oraz delegowanymi zasobami](../how-to/view-manage-customers.md). Podobnie klient dostawcy usług kryptograficznych nie musi być widoczny w sekcji **dostawca rozwiązań w chmurze (wersja zapoznawcza)** w obszarze **moi klienci** w celu dołączenia ich do usługi Azure Lighthouse.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się [, jak dołączyć subskrypcję do usługi Azure Lighthouse](../how-to/onboard-customer.md).
- Dowiedz się więcej o [programie dostawcy rozwiązań w chmurze](/partner-center/csp-overview).
