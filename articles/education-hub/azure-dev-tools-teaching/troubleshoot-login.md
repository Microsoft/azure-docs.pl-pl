---
title: Rozwiązywanie problemów z logowaniem dla narzędzi deweloperskich platformy Azure dla nauczania
description: W tym artykule opisano akcje, które student powinien wykonać, jeśli otrzymają komunikat o błędzie podczas logowania się do narzędzi deweloperskich platformy Azure w celu nauczania.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87097680"
---
# <a name="troubleshooting-student-login-issues"></a>Rozwiązywanie problemów z logowaniem ucznia
Dostęp do narzędzi deweloperskich platformy Azure dla nauczania wymaga, aby użytkownik miał konto Microsoft (MSA). Studenci zostaną automatycznie przekierowani do tworzenia elementu MSA, jeśli konto nie jest jeszcze kontem MSA lub zostało połączone z MSA. Jeśli domena jest skojarzona z Active Directory, wszystkie konta w tej domenie są już uznawane za MSA.

Jeśli student próbuje się zalogować i otrzyma następujący komunikat o błędzie, użyj jednego z rozwiązań opisanych poniżej.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Komunikat o błędzie logowania." border="false":::

Istnieją dwa rozwiązania: Utwórz nowe konto Microsoft lub Użyj istniejącej konto Microsoft.

## <a name="create-a-new-microsoft-account"></a>Utwórz nowy konto Microsoft
### <a name="use-a-university-email-address"></a>Użyj adresu e-mail University
Jeśli logujesz się przy użyciu adresu e-mail University (na przykład `John.Smith@university.edu` ), musisz utworzyć konto Microsoft przy użyciu tego adresu e-mail. Tworzenie konta jest bezpłatne i trwa zaledwie kilka minut. Posiadanie konto Microsoft umożliwi automatyczne logowanie do wszystkich produktów firmy Microsoft przy użyciu pojedynczej nazwy użytkownika i hasła.

### <a name="use-a-personal-email-address"></a>Użyj osobistego adresu e-mail
Jeśli logujesz się przy użyciu osobistego adresu e-mail (np `John.Smith@email.com` .), ale masz również adres e-mail University, spróbuj użyć adresu e-mail University. Jeśli wcześniej zalogowano się w sklepie webstore Twojej instytucji przy użyciu osobistego adresu e-mail lub nie masz służbowego adresu e-mail, musisz utworzyć lub połączyć konto Microsoft przy użyciu osobistego adresu e-mail.

## <a name="use-an-existing-microsoft-account"></a>Użyj istniejącego konto Microsoft
Jeśli student ma istniejący konto Microsoft (na przykład Xbox), może połączyć to konto z kontem narzędzi deweloperskich platformy Azure.

1. Przejdź do witryny https://account.microsoft.com.
1. Zaloguj się przy użyciu poświadczeń konto Microsoft.
1. Wybierz **swoje informacje** z górnego menu wstążki.

1. Kliknij pozycję **Zarządzaj sposobem logowania do firmy Microsoft**. Zostanie wyświetlony monit o zweryfikowanie Twojej tożsamości. Zostanie wysłana wiadomość e-mail z kodem zabezpieczeń.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Zarządzaj logowaniem." border="false":::

1. Wprowadź kod zabezpieczeń wiadomości e-mail.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Wprowadź kod logowania." border="false":::

1. Kliknij pozycję **Dodaj wiadomość e-mail** do swojego konta, a następnie wprowadź swój adres e-mail dla University.
Przy następnym logowaniu możesz użyć swojego adresu e-mail na Uniwersytecie, aby uzyskać dostęp do narzędzi deweloperskich platformy Azure w celu uczenia się.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Zarządzanie sposobem logowania do firmy Microsoft." border="false":::

## <a name="next-steps"></a>Następne kroki
- [Często zadawane pytania](program-faq.md)

- [Opcje pomocy technicznej](program-support.md)
