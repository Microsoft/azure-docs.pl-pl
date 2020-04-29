---
title: Uwierzytelnianie wieloskładnikowe i samoobsługowe resetowanie haseł oparte na ryzykach za pomocą usługi Azure Identity Protection
description: W tym samouczku włączysz integracje usługi Azure Identity Protection na potrzeby uwierzytelniania wieloskładnikowego i samoobsługowego resetowania haseł, aby zmniejszyć liczbę ryzykownych zachowań.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1a6858d5eda8227b3f7c1b90dee86f44273a258
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74846355"
---
# <a name="tutorial-use-risk-detections-to-trigger-multi-factor-authentication-and-password-changes"></a>Samouczek: używanie wykrywania ryzyka do wyzwalania zmian Multi-Factor Authentication i haseł

W tym samouczku włączysz funkcje usługi Azure Active Directory (Azure AD) Identity Protection, funkcję usługi Azure AD w warstwie Premium P2, która jest więcej niż tylko narzędziem do monitorowania i raportowania. Aby chronić tożsamości w organizacji, można skonfigurować zasady oparte na ryzykach, które reagują automatycznie na ryzykowne zachowania. Te zasady mogą automatycznie stosować blokadę lub inicjować rozwiązanie problemu, w tym wymaganie zmiany hasła lub wymuszanie uwierzytelniania wieloskładnikowego.

Zasad Azure AD Identity Protection można używać oprócz istniejących zasad dostępu warunkowego jako dodatkowej warstwy ochrony. Użytkownicy być może nigdy nie wyzwolą ryzykownego zachowania wymagającego zastosowania jednej z tych zasad, ale jako administrator będziesz wiedzieć, że są chronieni.

Niektóre elementy, które mogą wyzwolić wykrywanie ryzyka, obejmują:

* Użytkownicy z ujawnionymi poświadczeniami
* Logowania z anonimowych adresów IP
* Niemożliwa podróż do nietypowych lokalizacji
* Logowania z zainfekowanych urządzeń
* Logowania z adresów IP związanych z podejrzanymi działaniami
* Logowania z nieznanych lokalizacji

Więcej informacji na temat usługi Azure AD Identity Protection można znaleźć w artykule [Co to jest usługa Azure AD Identity Protection](../active-directory-identityprotection.md)

> [!div class="checklist"]
> * Włączanie rejestrowania w usłudze Azure MFA
> * Włączanie zmiany haseł opartej na ryzykach
> * Włączanie uwierzytelniania wieloskładnikowego opartego na ryzykach

## <a name="prerequisites"></a>Wymagania wstępne

* Dostęp do działającej dzierżawy usługi Azure AD z przypisaną co najmniej próbną wersją usługi Azure AD Premium P2.
* Konto z uprawnieniami administratora globalnego w dzierżawie usługi Azure AD.
* Ukończone wcześniejsze samouczki samoobsługowego resetowania haseł (SSPR) i uwierzytelniania wieloskładnikowego (MFA).

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Włączanie zasad opartych na ryzykach dla funkcji samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego

Włączanie zasad opartych na ryzykach jest dość proste. Poniższe kroki przeprowadzą Cię przez przykładową konfigurację.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Umożliwianie użytkownikom rejestrowania się w celu uwierzytelniania wieloskładnikowego

Usługa Azure AD Identity Protection zawiera domyślne zasady, które ułatwiają rejestrowanie użytkowników w celu uwierzytelniania wieloskładnikowego oraz określanie bieżącego stanu rejestracji. Włączenie tych zasad nie powoduje, że od użytkowników będzie wymagane uwierzytelnianie wieloskładnikowe. Zostaną oni jedynie poproszeni o wstępną rejestrację.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **Wszystkie usługi**, a następnie przejdź do usługi **Azure AD Identity Protection**.
1. Kliknij pozycję **Rejestracja w usłudze MFA**.
1. Dla opcji Wymuś zasady ustaw wartość **Włączone**.
   1. Ustawienie tych zasad spowoduje, że wszyscy użytkownicy będą musieli zarejestrować metody w celu przygotowania ich do używania w usłudze Multi-Factor Authentication.
1. Kliknij przycisk **Zapisz**.

   ![Wymagaj od użytkowników zarejestrowania się na potrzeby uwierzytelniania wieloskładnikowego podczas logowania](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Włączanie zmiany haseł opartej na ryzykach

Firma Microsoft współpracuje z naukowcami, organami ścigania, różnymi zespołami ds. zabezpieczeń w firmie Microsoft i innymi zaufanymi źródłami w celu wyszukiwania par „nazwa użytkownika i hasło”. Gdy jedna z takich par jest zgodna z kontem w Twoim środowisku, można wyzwolić zmianę hasła opartą na ryzykach, używając następujących zasad.

1. Kliknij pozycję Zasady dotyczące ryzyka związanego z użytkownikiem.
1. W obszarze **Warunki** wybierz pozycję **Ryzyko związane z użytkownikiem**, a następnie wybierz pozycję **Średnie i wyższe**.
1. Kliknij pozycję „Wybierz”, a następnie pozycję „Gotowe”
1. W obszarze **Dostęp** wybierz pozycję **Zezwól na dostęp**, a następnie wybierz pozycję **Wymagaj zmiany hasła**.
1. Kliknij pozycję „Wybierz”
1. Dla opcji Wymuś zasady ustaw wartość **Włączone**.
1. Kliknij przycisk **Zapisz**

### <a name="enable-risk-based-multi-factor-authentication"></a>Włączanie uwierzytelniania wieloskładnikowego opartego na ryzykach

Większości użytkowników dotyczy zachowanie normalne, które można śledzić. Gdy ta norma zostanie przekroczona, zezwolenie tym użytkownikom na zwykłe logowanie się może stanowić ryzyko. Takiego użytkownika można zablokować lub można poprosić go o przeprowadzenie uwierzytelniania wieloskładnikowego, aby potwierdził, że jest tym, za kogo się podaje. Aby włączyć zasady wymagające uwierzytelniania wieloskładnikowego, gdy zostanie wykryte ryzykowne logowanie, użyj następujących zasad.

1. Kliknij pozycję Zasady dotyczące ryzyka związanego z logowaniem
1. W obszarze **Warunki** wybierz pozycję **Ryzyko związane z użytkownikiem**, a następnie wybierz pozycję **Średnie i wyższe**.
1. Kliknij pozycję „Wybierz”, a następnie pozycję „Gotowe”
1. W obszarze **Dostęp** wybierz pozycję **Zezwól na dostęp**, a następnie wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
1. Kliknij pozycję „Wybierz”
1. Dla opcji Wymuś zasady ustaw wartość **Włączone**.
1. Kliknij przycisk **Zapisz**

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli po ukończeniu testowania nie będziesz chcieć, aby zasady oparte na ryzykach były nadal włączone, wróć do każdej zasady, którą chcesz wyłączyć, i dla opcji **Wymuś zasady** ustaw wartość **Wyłączone**.
