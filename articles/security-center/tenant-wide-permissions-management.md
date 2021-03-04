---
title: Przyznaj i Żądaj uprawnień w ramach dzierżawy w Azure Security Center
description: Dowiedz się, jak zarządzać uprawnieniami na poziomie dzierżawy w Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: b3ddbdf04dc736b6f78a04dc6bb2bc484e67f70f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107948"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Udziel i Żądaj widoczności dla całej dzierżawy

Użytkownik z rolą Azure Active Directory (AD) **administratora globalnego** może korzystać z obowiązków obejmujących wiele dzierżawców, ale nie ma uprawnień platformy Azure do wyświetlania informacji o całej organizacji w Azure Security Center. Podniesienie uprawnień jest wymagane, ponieważ przypisania ról usługi Azure AD nie zapewniają dostępu do zasobów platformy Azure. 

> [!TIP]
> Dowiedz się więcej na temat podniesienia uprawnień roli administratora globalnego w temacie [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../role-based-access-control/elevate-access-global-admin.md).

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Przyznaj sobie uprawnienia na poziomie dzierżawy

Aby przypisać własne uprawnienia na poziomie dzierżawy:

1. Jeśli organizacja zarządza dostępem do zasobów za pomocą [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)lub dowolnego innego narzędzia PIM, rola administratora globalnego musi być aktywna dla użytkownika zgodnie z poniższą procedurą.

1. Jako Administrator globalny bez przypisywania do głównej grupy zarządzania dzierżawy Otwórz stronę **przegląd** Security Center, a następnie wybierz link **widoczność na poziomie dzierżawy** na banerze. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Włącz uprawnienia na poziomie dzierżawy w Azure Security Center":::

1. Wybierz nową rolę platformy Azure, która ma zostać przypisana. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formularz służący do definiowania uprawnień na poziomie dzierżawy, które mają być przypisane do użytkownika":::

    > [!TIP]
    > Ogólnie rzecz biorąc, rola administratora zabezpieczeń jest wymagana do zastosowania zasad na poziomie głównym, podczas gdy czytelnik zabezpieczeń wystarcza do zapewnienia widoczności na poziomie dzierżawy. Więcej informacji o uprawnieniach przyznanych przez te role można znaleźć w [opisie wbudowanej roli administrator zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin) lub [Opis wbudowanej roli czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Różnice między tymi rolami, które są specyficzne dla Security Center, można znaleźć w tabeli w obszarze [role i akcje dozwolone](security-center-permissions.md#roles-and-allowed-actions).

    Widok całej organizacji jest realizowany przez przyznanie ról na głównym poziomie grupy zarządzania dzierżawy.  

1. Wyloguj się z Azure Portal, a następnie zaloguj się ponownie.

1. Po uzyskaniu podwyższonego poziomu uprawnień Otwórz lub Odśwież Azure Security Center, aby sprawdzić, czy masz wgląd we wszystkie subskrypcje w ramach dzierżawy usługi Azure AD. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Żądaj uprawnień na poziomie dzierżawy, gdy są niewystarczające

Jeśli zalogujesz się do Security Center i zobaczysz transparent informujący o tym, że Twój Widok jest ograniczony, możesz kliknąć pozycję przez, aby wysłać żądanie do administratora globalnego organizacji. W żądaniu możesz dołączyć rolę, która ma zostać przypisana, a Administrator globalny podejmie decyzję o tym, która rola ma zostać przyznana. 

Jest to decyzja administratora globalnego, czy należy akceptować lub odrzucać te żądania. 

> [!IMPORTANT]
> Jedno żądanie można przesłać co siedem dni.

Aby zażądać podniesionych uprawnień od administratora globalnego:

1. W Azure Portal Otwórz Azure Security Center.

1. Jeśli zobaczysz transparent "widzisz ograniczoną liczbę informacji". Wybierz go.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Transparent informujący użytkownika, że może zażądać uprawnień na poziomie dzierżawy.":::

1. W szczegółowym formularzu żądania wybierz żądaną rolę i uzasadnienie, dlaczego te uprawnienia są potrzebne.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Strona szczegółów do żądania uprawnień na poziomie dzierżawy od administratora globalnego platformy Azure":::

1. Wybierz pozycję **Żądaj dostępu**.

    Wiadomość e-mail jest wysyłana do administratora globalnego. Wiadomość e-mail zawiera link do Security Center, w którym można zatwierdzić lub odrzucić żądanie.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Wyślij wiadomość e-mail do administratora globalnego, aby uzyskać nowe uprawnienia":::

    Po wybraniu przez administratora globalnego **zapoznania się z żądaniem** i zakończeniu procesu decyzja zostaje wysłana pocztą e-mail do żądającego użytkownika. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o uprawnieniach Security Center na następującej stronie powiązanej:

- [Uprawnienia w usłudze Azure Security Center](security-center-permissions.md)