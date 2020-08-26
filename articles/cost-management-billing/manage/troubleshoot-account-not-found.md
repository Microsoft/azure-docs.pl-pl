---
title: Rozwiązywanie problemów z wyświetlaniem konta rozliczeniowego w witrynie Azure Portal
description: Ten artykuł pomaga rozwiązywać problemy, które mogą występować podczas próby wyświetlenia konta rozliczeniowego w witrynie Azure Portal.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f701e41c62336bcd7638360a27a0fb4c3ce3ec7d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686670"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Rozwiązywanie problemów z wyświetlaniem konta rozliczeniowego w witrynie Azure Portal

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład zarejestrować się na platformie Azure do użytku osobistego. Dostęp do platformy Azure można również uzyskiwać w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. W każdym z tych scenariuszy używane jest oddzielne konto rozliczeniowe. Ten artykuł pomaga rozwiązywać problemy, które mogą występować podczas próby wyświetlenia konta rozliczeniowego w witrynie Azure Portal.

Konta rozliczeniowe można wyświetlić na stronie [Zarządzanie kosztami i rozliczenia na platformie Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade).

Aby dowiedzieć się więcej na temat kont rozliczeniowych i identyfikowania typu konta rozliczeniowego, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](view-all-accounts.md).

Jeśli nie widzisz swojego konta rozliczeniowego w witrynie Azure Portal, wypróbuj następujące opcje:

## <a name="sign-in-to-a-different-tenant"></a>Logowanie się do innej dzierżawy

Twoje konto rozliczeniowe jest skojarzone z jedną dzierżawą usługi Azure Active Directory. Nie zobaczysz swojego konta rozliczeniowego na stronie Zarządzanie kosztami i rozliczenia, jeśli logowanie nastąpiło do nieprawidłowej dzierżawy. Wykonaj następujące kroki, aby przełączyć się do innej dzierżawy w witrynie Azure Portal i wyświetlić konta rozliczeniowe w tej dzierżawie.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz swój profil (adres e-mail) w prawym górnym rogu strony.
1. Wybierz pozycję **Przełącz katalog**.  
    ![Zrzut ekranu przedstawiający wybieranie przełączenia katalogu w portalu](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Wybierz katalog w sekcji **Wszystkie katalogi**.  
    ![Zrzut ekranu przedstawiający wybieranie katalogu w portalu](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Logowanie się przy użyciu innego adresu e-mail

Niektórzy użytkownicy mają wiele adresów e-mail, za pomocą których logują się do witryny [Azure Portal](https://portal.azure.com). Nie wszystkie adresy e-mail mają dostęp do konta rozliczeniowego. Jeśli zalogujesz się przy użyciu adresu e-mail mającego uprawnienia do zarządzania zasobami, ale bez uprawnień do wyświetlania konta rozliczeniowego, nie zobaczysz konta rozliczeniowego na stronie [Zarządzanie kosztami i rozliczenia](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) w witrynie Azure Portal.

Zaloguj się do witryny Azure Portal przy użyciu adresu e-mail, który ma uprawnienia do konta rozliczeniowego, aby uzyskać do niego dostęp.

## <a name="sign-in-with-a-different-identity"></a>Logowanie się przy użyciu innej tożsamości

Niektórzy użytkownicy mają dwie tożsamości o tym samym adresie e-mail — konto służbowe i konto osobiste. Zazwyczaj tylko jedna z tych tożsamości ma uprawnienia do wyświetlania konta rozliczeniowego. Mogą istnieć dwie tożsamości korzystające z jednego adresu e-mail. Jeśli zalogujesz się przy użyciu tożsamości, która nie ma uprawnień do wyświetlania konta rozliczeniowego, konto rozliczeniowe nie będzie widoczne na stronie [Zarządzanie kosztami i rozliczenia](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade). Wykonaj następujące kroki, aby przełączyć swoją tożsamość:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) w oknie InPrivate/Incognito.
1. Jeśli Twój adres e-mail ma dwie tożsamości, zobaczysz opcję wyboru konta osobistego lub konta służbowego. Wybierz jedno z kont.
1. Jeśli nie widzisz konta rozliczeniowego na stronie Zarządzanie kosztami i rozliczenia w witrynie Azure Portal, powtórz kroki 1 oraz 2 i wybierz inną tożsamość.

## <a name="contact-us-for-help"></a>Skontaktuj się z nami, aby uzyskać pomoc

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły dotyczące rozliczeń i subskrypcji, aby uzyskać pomoc w rozwiązywaniu problemów.

- [Odrzucona karta](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Problemy z logowaniem się do subskrypcji](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [No subscriptions found (Nie odnaleziono żadnych subskrypcji)](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Wyłączony widok kosztów przedsiębiorstwa](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
