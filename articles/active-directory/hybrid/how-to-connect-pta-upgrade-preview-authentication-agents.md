---
title: Azure AD Connect — uwierzytelnianie przekazywane-uaktualnianie agentów uwierzytelniania | Microsoft Docs
description: W tym artykule opisano sposób uaktualniania konfiguracji uwierzytelniania przekazującego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji Active Directory, wymaganych składników usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0507f7751305af5e626cbd7dd6e0dfd1a63a74
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973052"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory uwierzytelniania przekazywanego: agenci uwierzytelniania wersji zapoznawczej uaktualnienia

## <a name="overview"></a>Omówienie

Ten artykuł jest przeznaczony dla klientów korzystających z uwierzytelniania przekazywanego za pomocą usługi Azure AD w wersji zapoznawczej. Niedawno uaktualnione (i oznaczone jako) oprogramowanie agenta uwierzytelniania. Należy _ręcznie_ uaktualnić agentów uwierzytelniania w wersji zapoznawczej zainstalowanych na serwerach lokalnych. To uaktualnienie ręczne jest tylko jednorazową akcją. Wszystkie przyszłe aktualizacje agentów uwierzytelniania są automatyczne. Przyczyny uaktualnienia są następujące:

- Wersje zapoznawcze agentów uwierzytelniania nie będą otrzymywać żadnych dalszych poprawek zabezpieczeń ani błędów.
-   Wersji zapoznawczej agentów uwierzytelniania nie można zainstalować na dodatkowych serwerach, aby zapewnić wysoką dostępność.

## <a name="check-versions-of-your-authentication-agents"></a>Sprawdź wersje agentów uwierzytelniania

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1. sprawdzenie, czy agenci uwierzytelniania są zainstalowani

Wykonaj następujące kroki, aby sprawdzić, gdzie są zainstalowane agenci uwierzytelniania:

1. Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz pozycję **Azure Active Directory** w nawigacji po lewej stronie.
3. Wybierz **Azure AD Connect**. 
4. Wybierz pozycję **uwierzytelnianie przekazywane**. Ten blok zawiera listę serwerów, na których są zainstalowane agenci uwierzytelniania.

![Azure Active Directory centrum administracyjnego — blok uwierzytelniania przekazywanego](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2. sprawdzenie wersji agentów uwierzytelniania

Aby sprawdzić wersje agentów uwierzytelniania, na każdym serwerze zidentyfikowanym w poprzednim kroku postępuj zgodnie z następującymi instrukcjami:

1. Przejdź do **Panelu sterowania — > programy — > programy i funkcje** na serwerze lokalnym.
2. Jeśli istnieje wpis "**Microsoft Azure AD Connect Authentication Agent**", nie musisz podejmować żadnych działań na tym serwerze.
3. Jeśli istnieje wpis "**Microsoft Azure AD łącznika serwera proxy aplikacji**", musisz ręcznie uaktualnić na tym serwerze.

![Wersja zapoznawcza agenta uwierzytelniania](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Najlepsze rozwiązania, które należy wykonać przed rozpoczęciem uaktualniania

Przed uaktualnieniem upewnij się, że masz następujące elementy:

1. **Utwórz konto administratora globalnego tylko w chmurze**: Nie uaktualniaj bez posiadania konta administratora globalnego tylko w chmurze do użycia w sytuacjach awaryjnych, w których agenci uwierzytelniania przekazywanego nie działają prawidłowo. Dowiedz się więcej [na temat dodawania konta administratora globalnego tylko w chmurze](../fundamentals/add-users-azure-active-directory.md). Ten krok ma krytyczne znaczenie i gwarantuje, że nie można zablokować dzierżawy.
2.  **Zapewnij wysoką dostępność**: Jeśli nie zostało to wcześniej wykonane, zainstaluj drugiego autonomicznego agenta uwierzytelniania, aby zapewnić wysoką dostępność żądań logowania, korzystając z tych [instrukcji](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uaktualnianie agenta uwierzytelniania na serwerze Azure AD Connect

Przed uaktualnieniem agenta uwierzytelniania na tym samym serwerze należy uaktualnić Azure AD Connect. Wykonaj następujące kroki na serwerach podstawowych i przejściowych Azure AD Connect:

1. **Azure AD Connect uaktualniania**: Postępuj zgodnie z tym [artykułem](how-to-upgrade-previous-version.md) i przeprowadź uaktualnienie do najnowszej wersji Azure AD Connect.
2. **Odinstaluj wersję zapoznawczą agenta uwierzytelniania**: Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako administrator na serwerze.
3. **Pobierz najnowszą wersję agenta uwierzytelniania (w wersji 1.5.389.0 lub nowszej)**: Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy. Wybierz pozycję **Azure Active Directory-> Azure AD Connect > uwierzytelnianie przekazywane-> Agent pobierania**. Zaakceptuj [warunki korzystania z usługi](https://aka.ms/authagenteula) i Pobierz najnowszą wersję agenta uwierzytelniania. Możesz również pobrać z tego [miejsca](https://aka.ms/getauthagent)agenta uwierzytelniania.
4. **Zainstaluj najnowszą wersję agenta uwierzytelniania**: Uruchom plik wykonywalny pobrany w kroku 3. Po wyświetleniu monitu podaj poświadczenia administratora globalnego dzierżawy.
5. **Sprawdź, czy zainstalowano najnowszą wersję**: jak wcześniej, przejdź do **panelu sterowania — > programy — > programy i funkcje** i sprawdź, czy istnieje wpis "**Microsoft Azure AD Połącz agenta uwierzytelniania**".

>[!NOTE]
>W przypadku zaznaczenia bloku uwierzytelnianie przekazywane w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com) po wykonaniu powyższych kroków zobaczysz dwa wpisy agenta uwierzytelniania dla każdego serwera — jednego wpisu, który będzie zawierać agenta uwierzytelniania jako **aktywny** , a drugi jako **nieaktywny**. Jest to _oczekiwane_. **Nieaktywny** wpis jest automatycznie usuwany po kilku dniach.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uaktualnianie agenta uwierzytelniania na innych serwerach

Wykonaj następujące kroki, aby uaktualnić agentów uwierzytelniania na innych serwerach (gdzie Azure AD Connect nie jest zainstalowana):

1. **Odinstaluj wersję zapoznawczą agenta uwierzytelniania**: Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako administrator na serwerze.
2. **Pobierz najnowszą wersję agenta uwierzytelniania (w wersji 1.5.389.0 lub nowszej)**: Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy. Wybierz pozycję **Azure Active Directory-> Azure AD Connect > uwierzytelnianie przekazywane-> Agent pobierania**. Zaakceptuj warunki korzystania z usługi i Pobierz najnowszą wersję.
3. **Zainstaluj najnowszą wersję agenta uwierzytelniania**: Uruchom plik wykonywalny pobrany w kroku 2. Po wyświetleniu monitu podaj poświadczenia administratora globalnego dzierżawy.
4. **Upewnij się, że zainstalowano najnowszą wersję**: jak wcześniej, przejdź do **panelu sterowania — > programy — > programy i funkcje** i sprawdź, czy istnieje wpis o nazwie **Microsoft Azure AD Połącz agenta uwierzytelniania**.

>[!NOTE]
>W przypadku zaznaczenia bloku uwierzytelnianie przekazywane w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com) po wykonaniu powyższych kroków zobaczysz dwa wpisy agenta uwierzytelniania dla każdego serwera — jednego wpisu, który będzie zawierać agenta uwierzytelniania jako **aktywny** , a drugi jako **nieaktywny**. Jest to _oczekiwane_. **Nieaktywny** wpis jest automatycznie usuwany po kilku dniach.

## <a name="next-steps"></a>Następne kroki
- [**Rozwiązywanie problemów**](tshoot-connect-pass-through-authentication.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.