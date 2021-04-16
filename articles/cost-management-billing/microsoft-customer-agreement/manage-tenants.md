---
title: Zarządzanie dzierżawami na koncie rozliczeniowym Umowa z Klientem Microsoft — Azure
description: Ten artykuł ułatwia zrozumienie dzierżaw skojarzonych z kontem rozliczeniowym Umowa z Klientem Microsoft zarządzanie nimi.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493126"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Zarządzanie dzierżawami na Umowa z Klientem Microsoft rozliczeniowym

Ten artykuł ułatwia zrozumienie dzierżaw skojarzonych z kontem rozliczeniowym Umowa z Klientem Microsoft zarządzanie nimi. Skorzystaj z tych informacji, aby zarządzać dzierżawami, przenosić subskrypcje i administrować własnością rozliczeń przy jednoczesnym zagwarantowanym bezpiecznym dostępie do środowiska rozliczeniowego.

## <a name="whats-a-tenant"></a>Co to jest dzierżawa?

Dzierżawa jest cyfrową reprezentacją organizacji i jest głównie skojarzona z domeną, na przykład Microsoft.com. Jest to środowisko zarządzane za pośrednictwem usługi Azure Active Directory które umożliwia przypisywanie użytkownikom uprawnień do zarządzania zasobami platformy Azure i rozliczeniami.

Każda dzierżawa jest odrębna i oddzielona od innych dzierżaw, ale możesz zezwolić użytkownikom-gościom z innych dzierżaw na dostęp do twojej dzierżawy, aby śledzić koszty i zarządzać rozliczeniami.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Jak dzierżawy i subskrypcje odnoszą się do konta rozliczeniowego

Konta rozliczeniowego Umowa z Klientem Microsoft do śledzenia kosztów i zarządzania rozliczeniami. Każde konto rozliczeniowe ma co najmniej jeden profil rozliczeniowy. Profil rozliczeniowy umożliwia zarządzanie fakturami i metodami płatności. Każdy profil rozliczeniowy domyślnie zawiera jedną sekcję faktury. W razie potrzeby możesz utworzyć więcej sekcji faktur, aby grupować i śledzić koszty oraz zarządzać nimi na bardziej szczegółowym poziomie.

- Twoje konto rozliczeniowe jest skojarzone z jedną dzierżawą. Oznacza to, że tylko użytkownicy, którzy są częścią dzierżawy, mogą uzyskać dostęp do Twojego konta rozliczeniowego.
- Po utworzeniu nowej subskrypcji platformy Azure dla konta rozliczeniowego jest ona zawsze tworzona w dzierżawie konta rozliczeniowego. Można jednak przenosić subskrypcje do innych dzierżaw. Możesz również połączyć istniejące subskrypcje z innych dzierżaw z kontem rozliczeniowym. Umożliwia centralne zarządzanie rozliczeniami za pośrednictwem jednej dzierżawy przy jednoczesnym zachowaniu zasobów i subskrypcji w innych dzierżawach zgodnie z potrzebami.

Na poniższym diagramie przedstawiono sposób, w jaki konta rozliczeniowe i subskrypcje są połączone z dzierżawami. Konto rozliczeniowe Contoso MCA jest skojarzone z dzierżawą 1, a konto contoso payg jest skojarzone z dzierżawą 2. Załóżmy, że firma Contoso chce płacić za subskrypcję z płatnością za pomocą konta rozliczeniowego MCA. Aby połączyć subskrypcję ze swoim kontem rozliczeniowym mca, może użyć przeniesienia własności rozliczeń. Subskrypcja i jej zasoby będą nadal kojarzone z dzierżawą 2, ale są opłacane za korzystanie z konta rozliczeniowego mca.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Diagram przedstawiający przykład hierarchii rozliczeń." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Zarządzanie subskrypcjami w ramach wielu dzierżaw w jednym Umowa z Klientem Microsoft

Właściciele rozliczeń mogą tworzyć subskrypcje, jeśli mają [odpowiednie uprawnienia](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) do konta rozliczeniowego. Domyślnie wszystkie nowe subskrypcje utworzone w ramach Umowa z Klientem Microsoft znajdują się w Umowa z Klientem Microsoft dzierżawie.

- Subskrypcje z innych dzierżaw można połączyć z kontem Umowa z Klientem Microsoft rozliczeniowego. Przejęcie własności rozliczeń subskrypcji zmienia tylko sposób fakturowania. Nie ma to wpływu na dzierżawę usługi ani role RBAC platformy Azure.
- Aby zmienić właściciela subskrypcji w dzierżawie usługi, musisz przenieść subskrypcję do innego [Azure Active Directory usługi](../../role-based-access-control/transfer-subscription.md).

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Dodawanie użytkowników-gości do dzierżawy Umowa z Klientem Microsoft dzierżawy

Użytkownicy dodani do dzierżawy Umowa z Klientem Microsoft rozliczeń w celu zarządzania obowiązkami rozliczeniowymi z innej dzierżawy muszą być zaproszeni jako gość.

Aby zaprosić kogoś jako gościa, użytkownik musi mieć istniejący adres e-mail z domeną inną niż domena Azure Active Directory (AD). Usługa Azure AD wysyła użytkownikowi-gościowi wiadomość e-mail z linkiem do uwierzytelniania.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Zrzut ekranu przedstawiający przykładowe zaproszenie w wiadomości e-mail." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Gdy użytkownik zostanie dodany do dzierżawy Umowa z Klientem Microsoft, musi [zaakceptować zaproszenie](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation).

Po wybraniu linku **Zaakceptuj** zaproszenie zostanie wyświetlony monit o uwierzytelnienie na platformie Azure.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Zrzut ekranu przedstawiający monit o uwierzytelnienie na platformie Azure." lightbox="./media/manage-tenants/authenticate.png" :::

Następnie wybiera pozycję **Zaakceptuj**.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Zrzut ekranu przedstawiający monit o zaakceptowanie zaproszenia." lightbox="./media/manage-tenants/accept-invitation.png" :::

Po zaakceptowaniu mogą wyświetlić [konto rozliczeniowe Umowa z Klientem Microsoft w obszarze Cost Management + Billing](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Zrzut ekranu przedstawiający Umowa z Klientem Microsoft na liście kont rozliczeniowych." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

Autoryzacja do zapraszania użytkowników-gości jest kontrolowana przez ustawienia usługi Azure AD. Wartość ustawień jest wyświetlana w obszarze **Ustawienia** na stronie **Relacje organizacyjne.** Upewnij się, że ustawienie jest zaznaczone. W przeciwnym razie zaproszenie zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Ograniczanie uprawnień dostępu użytkowników-gości.](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Zrzut ekranu przedstawiający ustawienia współpracy zewnętrznej." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Użytkownicy-goście mają dostęp do Umowa z Klientem Microsoft dzierżawy, co może stanowić problem z bezpieczeństwem. Aby uzyskać więcej informacji, zobacz Informacje na temat ograniczania domyślnych uprawnień [użytkowników-gości.](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Zarządzanie wieloma usługami w chmurze firmy Microsoft w ramach dzierżawy usługi Azure AD

Możesz zarządzać wieloma usługami w chmurze dla swojej organizacji w ramach jednej dzierżawy usługi Azure AD. Konta użytkowników dla wszystkich ofert firmy Microsoft w chmurze są przechowywane w dzierżawie usługi Azure AD, która zawiera konta użytkowników i grupy. Na poniższym diagramie przedstawiono przykład organizacji z wieloma usługami korzystającymi ze wspólnej dzierżawy usługi Azure AD zawierającej konta. Każda usługa ma własny portal w niebieskim tekście, w którym użytkownicy zarządzają swoimi usługami.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagram przedstawiający przykład organizacji z wieloma usługami korzystającymi ze wspólnej dzierżawy usługi Azure AD zawierającej konta." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły, aby dowiedzieć się, jak administrować elastyczną własnością rozliczeń i zapewnić bezpieczny dostęp do Umowa z Klientem Microsoft.

- [Jak skonfigurować dzierżawę](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Wbudowane role platformy Azure](../../role-based-access-control/built-in-roles.md)
- [Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD](../../role-based-access-control/transfer-subscription.md)
- [Ograniczanie uprawnień dostępu gościa (wersja zapoznawcza) w Azure Active Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Dodawanie użytkowników-gości do katalogu w witrynie Azure Portal](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Jakie są domyślne uprawnienia użytkownika w Azure Active Directory?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Co to jest usługa Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)