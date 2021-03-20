---
title: Model rozliczeń MAU dla tożsamości zewnętrznych usługi Azure AD
description: Dowiedz się więcej o modelu rozliczeń (MAU) usługi Azure AD — informacje o comiesięcznych tożsamościach dla użytkowników-Gości (B2B) w usłudze Azure AD. Dowiedz się, jak połączyć dzierżawę usługi Azure AD z subskrypcją platformy Azure.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983a803245467145a0b1161a4495e8045759e7ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442069"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Model rozliczeń dla tożsamości zewnętrznych usługi Azure AD

Cennik usług Azure Active Directory (Azure AD) dotyczący tożsamości zewnętrznych jest oparty na miesięcznych aktywnych użytkowników (MAU), czyli liczbie unikatowych użytkowników z aktywnością uwierzytelniania w ciągu miesiąca kalendarzowego. Ten model rozliczeń dotyczy zarówno funkcji współpracy użytkownika gościa usługi Azure AD, jak i [dzierżawców Azure AD B2C](../../active-directory-b2c/billing.md). Rozliczanie MAU pomaga obniżyć koszty, oferując bezpłatną warstwę i elastyczne, przewidywalne ceny. W tym artykule dowiesz się więcej na temat rozliczeń MAU i powiązania dzierżaw usługi Azure AD z subskrypcją.

> [!IMPORTANT]
> Ten artykuł nie zawiera szczegółów cennika. Aby uzyskać najnowsze informacje dotyczące rozliczeń i cen użytkowania, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>Co mam zrobić?

Aby skorzystać z rozliczeń MAU, dzierżawa usługi Azure AD musi być połączona z subskrypcją platformy Azure.

|Jeśli Twoja dzierżawa to:  |Należy:  |
|---------|---------|
| Dzierżawa usługi Azure AD jest już połączona z subskrypcją     | Nic nie robić. W przypadku korzystania z funkcji tożsamości zewnętrznych do współpracy z użytkownikami-gośćmi opłaty zostaną naliczone automatycznie przy użyciu modelu MAU.        |
| Dzierżawa usługi Azure AD nie została jeszcze połączona z subskrypcją     | [Połącz dzierżawę usługi Azure AD z subskrypcją](#link-your-azure-ad-tenant-to-a-subscription) w celu aktywowania rozliczeń Mau.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>Informacje o rozliczeniach miesięcznych aktywnych użytkowników (MAU)

W dzierżawie usługi Azure AD użycie funkcji współpracy użytkowników gościa jest rozliczane na podstawie liczby unikatowych użytkowników-Gości z aktywnością uwierzytelniania w ciągu miesiąca kalendarzowego. Ten model zastępuje model rozliczeń dotyczący współczynnika 1:5, który zezwala maksymalnie pięciu użytkownikom-Gościom na każdą licencję Azure AD — wersja Premium w dzierżawie. Gdy dzierżawa jest połączona z subskrypcją i używasz funkcji tożsamości zewnętrznych do współpracy z użytkownikami-Gośćmi, będzie ona automatycznie rozliczana przy użyciu modelu rozliczania opartego na MAU.
  
Warstwa cenowa stosowana dla użytkowników-Gości korzysta z najwyższej warstwy cenowej przypisanej do dzierżawy usługi Azure AD. Na przykład jeśli najwyższego poziomu cenowego w dzierżawie jest Azure AD — wersja Premium P1, warstwa cenowa Premium P1 dotyczy również użytkowników-Gości. Jeśli jest Azure AD — wersja Bezpłatna najwyższa cena, zostanie wyświetlony monit o uaktualnienie do warstwy cenowej Premium przy próbie skorzystania z funkcji premium dla użytkowników-Gości.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Łączenie dzierżawy usługi Azure AD z subskrypcją

Dzierżawa usługi Azure AD musi być połączona z subskrypcją platformy Azure w celu uzyskania odpowiednich rozliczeń i dostępu do funkcji. Jeśli katalog nie ma już subskrypcji, z którą możesz się połączyć, możesz dodać ją w trakcie tego procesu.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta platformy Azure, do którego przypisano co najmniej rolę [współautor](../../role-based-access-control/built-in-roles.md) w ramach subskrypcji lub grupy zasobów w ramach subskrypcji.

2. Wybierz katalog, który chcesz połączyć: na pasku narzędzi Azure Portal wybierz ikonę katalog i **subskrypcja** , a następnie wybierz katalog.

    ![Wybierz ikonę katalogu i subskrypcji](media/external-identities-pricing/portal-mau-pick-directory.png)

3. W obszarze **usługi platformy Azure** wybierz pozycję **Azure Active Directory**.

4. W menu po lewej stronie wybierz pozycję **tożsamości zewnętrzne**.

5. W obszarze **subskrypcje** wybierz pozycję **połączone subskrypcje**.

6. Na liście dzierżawców zaznacz pole wyboru obok dzierżawy, a następnie wybierz pozycję **Połącz subskrypcję**.

    ![Wybierz dzierżawcę i Połącz subskrypcję](media/external-identities-pricing/linked-subscriptions.png)

7. W okienku Połącz subskrypcję wybierz **subskrypcję** i **grupę zasobów**. Następnie wybierz przycisk **Zastosuj**.

   > [!NOTE]
   > Jeśli na liście nie ma żadnych subskrypcji, możesz [skojarzyć subskrypcję z dzierżawcą](../fundamentals/active-directory-how-subscriptions-associated-directory.md). Możesz też dodać nową subskrypcję, wybierając link, **Jeśli nie masz jeszcze subskrypcji, którą możesz utworzyć tutaj**.

    ![Wybierz subskrypcję i grupę zasobów](media/external-identities-pricing/link-subscription-resource.png)

Po wykonaniu tych kroków subskrypcja platformy Azure jest rozliczana na podstawie szczegółowych informacji dotyczących usługi Azure Direct lub Enterprise Agreement, jeśli ma to zastosowanie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać najnowsze informacje o cenach, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).