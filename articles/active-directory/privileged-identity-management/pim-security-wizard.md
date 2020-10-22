---
title: Odnajdowanie ról usługi Azure AD i szczegółowe informacje (wersja zapoznawcza) w Privileged Identity Management dawnym Kreatorze zabezpieczeń — Azure Active Directory
description: Odnajdywanie i szczegółowe informacje (dawniej Kreator zabezpieczeń) ułatwiają konwertowanie trwałych przypisań ról usługi Azure AD do przypisań just in Time z Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4662e9fb537a93cb87c554e319256e2eca40d2e7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372433"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Odnajdywanie i szczegółowe informacje (wersja zapoznawcza) dla ról usługi Azure AD (dawniej Kreator zabezpieczeń)

Jeśli zaczynasz pracę z usługą Privileged Identity Management (PIM) w organizacji Azure Active Directory (Azure AD), możesz użyć strony **odnajdywanie i szczegółowe informacje (wersja zapoznawcza)** , aby rozpocząć pracę. Ta funkcja pokazuje, kto jest przypisany do ról uprzywilejowanych w organizacji i jak używać programu PIM, aby szybko zmieniać trwałe przypisania ról na przydziały just in Time. Można wyświetlać lub wprowadzać zmiany w trwałych przypisaniach ról uprzywilejowanych w **odnajdywaniu i szczegółowych danych (wersja zapoznawcza)**. Jest to narzędzie do analizy i narzędzie do działania.

## <a name="discovery-and-insights-preview"></a>Odnajdywanie i wgląd w szczegółowe dane (wersja zapoznawcza)

Zanim organizacja zacznie korzystać z Privileged Identity Management, wszystkie przypisania ról są trwałe. Użytkownicy zawsze mają przypisane role nawet wtedy, gdy nie potrzebują ich uprawnień. Odnajdywanie i szczegółowe informacje (wersja zapoznawcza), które zastępują poprzedni Kreator zabezpieczeń, pokazują listę ról uprzywilejowanych i liczbę użytkowników, którzy obecnie znajdują się w tych rolach. Możesz wyświetlić listę przypisań dla roli, aby dowiedzieć się więcej na temat przypisanych użytkowników, jeśli co najmniej jedna z nich jest nieznana.

: heavy_check_mark: **firma Microsoft zaleca** , aby zachować dwa konta ze szlifem, które są trwale przypisane do roli administratora globalnego. Upewnij się, że te konta nie wymagają tego samego mechanizmu usługi uwierzytelniania wieloskładnikowego co normalne konta administracyjne, aby się zalogować, zgodnie z opisem w temacie [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md).

Ponadto należy zachować stałe przypisania ról, jeśli użytkownik ma konto Microsoft (innymi słowy, konto używane do logowania się do usług firmy Microsoft, takich jak Skype lub Outlook.com). Jeśli wymagane jest uwierzytelnianie wieloskładnikowe dla użytkownika z konto Microsoft w celu aktywowania przypisania roli, użytkownik zostanie zablokowany.

## <a name="open-discovery-and-insights-preview"></a>Otwórz odnajdywanie i szczegółowe informacje (wersja zapoznawcza)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD** , a następnie wybierz pozycję **odnajdywanie i szczegółowe informacje (wersja zapoznawcza)**. Otwarcie strony rozpoczyna proces odnajdywania w celu znalezienia odpowiednich przypisań ról.

    ![Usługa Azure AD role — odnajdywanie i szczegółowe informacje przedstawiające 3 opcje](./media/pim-security-wizard/new-preview-link.png)

1. Wybierz pozycję **Zmniejsz administratorów globalnych**.

    ![Zrzut ekranu pokazujący "odnajdywanie i szczegółowe informacje (wersja zapoznawcza)" z wybraną akcją "Zmniejsz administratorów globalnych".](./media/pim-security-wizard/new-preview-page.png)

1. Przejrzyj listę przypisań roli administratora globalnego.

    ![Ogranicz administratorów globalnych — okienko role przedstawiające wszystkich administratorów globalnych](./media/pim-security-wizard/new-global-administrator-list.png)

1. Wybierz pozycję **dalej** , aby wybrać użytkowników lub grupy, które chcesz udostępnić, a następnie wybierz pozycję **Udostępnij** lub **Usuń przypisanie**.

    ![Konwertuj elementy członkowskie na kwalifikujące się strony z opcjami wyboru członków, którzy mają kwalifikować się do ról](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. Możesz również wymagać, aby wszyscy administratorzy globalni przeglądali swój dostęp.

    ![Strona Administratorzy globalni przedstawiająca sekcję przeglądy dostępu](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Po wybraniu jednej z tych zmian zobaczysz powiadomienie platformy Azure.

1. Następnie możesz wybrać opcję **eliminowanie stałych dostępów** lub **przejrzeć nazwy główne usług** , aby powtórzyć powyższe kroki dla innych ról uprzywilejowanych i przypisań ról głównych usług. W przypadku przypisywania ról jednostki usługi można usunąć tylko przypisania roli.

    ![Dodatkowe opcje szczegółowych informacji w celu wyeliminowania stałego dostępu i przeglądania podmiotów usługi ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Udzielanie dostępu innym administratorom w celu zarządzania Privileged Identity Management](pim-how-to-give-access-to-pim.md)
