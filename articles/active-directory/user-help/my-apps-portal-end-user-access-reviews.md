---
title: Zarządzanie dostępem w organizacji do aplikacji & grup — Azure AD
description: Dowiedz się, jak przeprowadzić przegląd dostępu w celu zarządzania dostępem do zabezpieczeń dla aplikacji i grup organizacji z poziomu portalu Moje aplikacje.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 34885e2a364778a2f81f4920aa26aa3bb5f40320
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100095022"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Przeprowadzanie przeglądu dostępu z poziomu portalu Moje aplikacje

Możesz użyć konta służbowego z portalem **Moje aplikacje** oparte na sieci Web, aby wykonywać przeglądy dostępu dla aplikacji i grup. Przeglądy dostępu ułatwiają zarządzanie nieaktualnym dostępem lub zmianami wymagań dostępu i zapewniają ich przegląd i aktualizację.

Jeśli nie masz dostępu do portalu **Moje aplikacje** , skontaktuj się z pomocą techniczną, aby uzyskać odpowiednie uprawnienia.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników **moich aplikacji** . Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania i zarządzania aplikacjami opartymi na chmurze w [dokumentacji zarządzania aplikacjami](../manage-apps/index.yml).
>
> Jeśli zostanie wyświetlony komunikat o błędzie logowania przy użyciu osobistego konto Microsoft, można nadal zalogować się przy użyciu nazwy domeny dla organizacji (takiej jak contoso.com) lub **identyfikatora dzierżawy** organizacji od administratora w jednym z następujących adresów URL:
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu

Jeśli administrator udzielił Ci uprawnień do wykonywania własnych przeglądów dostępu, możesz zarządzać grupami lub aplikacjami dostępnymi na kafelku **przeglądy dostępu** na stronie portalu **Moje aplikacje** .

>[!Note]
>Jeśli nie widzisz kafelka **przeglądy dostępu** , oznacza to, że nie masz uprawnienia do przeprowadzania przeglądów dostępu lub nie masz żadnych oczekujących przeglądów oczekujących na zatwierdzenie. Jeśli uważasz, że masz dostęp do kafelka, skontaktuj się z pomocą techniczną w celu uzyskania pomocy.

## <a name="to-perform-your-access-reviews"></a>Aby przeprowadzić przeglądy dostępu

1. Zaloguj się do konta służbowego.

1. Otwórz przeglądarkę internetową i przejdź do https://myapps.microsoft.com lub Użyj linku dostarczonego przez organizację. Na przykład może nastąpić przekierowanie do dostosowanej strony organizacji, na przykład https://myapps.microsoft.com/contoso.com .

    Zostanie wyświetlona strona **aplikacje** przedstawiająca wszystkie aplikacje oparte na chmurze należące do organizacji i dostępne do użycia.

    ![Strona aplikacje w portalu Moje aplikacje](media/my-apps-portal/my-apps-home.png)

1. Wybierz kafelek **przeglądy dostępu** , aby wyświetlić listę przeglądów dostępu oczekujących na zatwierdzenie.

    ![Strona przeglądy dostępu z oczekującymi przeglądami dostępu dla organizacji](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

1. Wybierz pozycję **Rozpocznij przegląd** , aby rozpocząć przegląd dostępu.

5. Przejrzyj swój dostęp i ustal, czy nadal jest to konieczne.

    ![Strona przeglądu dostępu zawierająca szczegóły przeglądu](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Jeśli jesteś administratorem i masz uprawnienia do przeglądania dostępu do grup i aplikacji w organizacji, zobaczysz inną stronę. Aby uzyskać więcej informacji na temat przeglądania grup lub aplikacji w organizacji, zobacz [Przegląd dostępu do grup lub aplikacji w przeglądach dostępu do usługi Azure AD](../governance/perform-access-review.md).

6. Wybierz pozycję **tak** , aby zachować dostęp, lub przycisk **nie** , aby usunąć swój dostęp.

    Jeśli wybierzesz opcję **tak**, w polu **Przyczyna** może być konieczne określenie uzasadnienia.

    ![Strona przeglądu dostępu pokazująca pole przyczyna z przykładowym tekstem](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Wybierz pozycję **Prześlij**.

    Przegląd dostępu zostanie zakończony i wrócisz do portalu **My Apps** .

    >[!Note]
    >Dostęp można zmienić w dowolnym momencie do momentu zakończenia okresu przeglądu dostępu. Jeśli usuniesz swój dostęp do aplikacji lub grupy, nie zostanie on natychmiast usunięty. Usunięcie odbywa się po zakończeniu okresu przeglądu dostępu lub gdy administrator zamknie przegląd.

## <a name="next-steps"></a>Następne kroki

- [Dostęp do aplikacji i korzystanie z nich w portalu My Apps](my-apps-portal-end-user-access.md)
- [Zmienianie informacji o profilu](./my-account-portal-settings.md)
- [Wyświetlanie i aktualizowanie informacji związanych z grupami](my-apps-portal-end-user-groups.md)