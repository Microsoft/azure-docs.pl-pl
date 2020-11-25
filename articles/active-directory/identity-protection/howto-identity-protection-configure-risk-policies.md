---
title: Zasady dotyczące ryzyka — Azure Active Directory Identity Protection
description: Włączanie i Konfigurowanie zasad ryzyka w Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997634"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Instrukcje: Konfigurowanie i Włączanie zasad ryzyka

Jak opisano w poprzednim artykule, [Zasady ochrony tożsamości](concept-identity-protection-policies.md) mają dwie zasady dotyczące ryzyka, które możemy włączyć w naszym katalogu. 

- Zasady ryzyka związanego z logowaniem
- Zasady ryzyka związanego z użytkownikiem

![Strona Przegląd zabezpieczeń umożliwiająca Włączanie zasad ryzyka użytkownika i logowania](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Obie zasady działają w celu zautomatyzowania reakcji na wykrycie zagrożeń w środowisku i umożliwiają użytkownikom samodzielne korygowanie w przypadku wykrycia ryzyka. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Wymagania wstępne 

Jeśli organizacja chce zezwolić użytkownikom na samodzielne korygowanie w przypadku wykrycia zagrożeń, użytkownicy muszą być zarejestrowani do samoobsługowego resetowania haseł i usługi Azure AD Multi-Factor Authentication. Zalecamy [włączenie usługi rejestracji informacji o zabezpieczeniach](../authentication/howto-registration-mfa-sspr-combined.md) w celu uzyskania najlepszego środowiska. Umożliwienie użytkownikom samodzielnego korygowania umożliwia szybsze pobieranie stanu wydajności bez konieczności interwencji administratora. Administratorzy nadal mogą zobaczyć te zdarzenia i zbadać je po fakcie. 

## <a name="choosing-acceptable-risk-levels"></a>Wybór dopuszczalnych poziomów ryzyka

Organizacje muszą decydować o tym, jaki jest poziom ryzyka, aby akceptować możliwości użytkowników i stan zabezpieczeń. 

Zalecenie firmy Microsoft polega na ustawieniu wartości progowej dla zasad ryzyka dla użytkowników na **wysoki** i zasady ryzyka związanego **z logowaniem.**

Wybranie **wysokiego** progu zmniejsza liczbę wyzwalanych zasad i minimalizuje wpływ na użytkowników. Nie dotyczy to jednak wykrycia **niskiego** i **średniego** ryzyka z zasad, co może uniemożliwić atakującemu wykorzystanie tożsamości. Wybranie **niskiego** progu powoduje wprowadzenie dodatkowych przerwań przez użytkownika, ale zwiększoną stan zabezpieczeń.

## <a name="exclusions"></a>Wykluczenia

Wszystkie zasady zezwalają na wykluczenie użytkowników, takich jak [dostęp awaryjny lub konta administratora z przerwaniem](../roles/security-emergency-access.md). Organizacje mogą określić, że muszą wykluczać inne konta z określonych zasad w zależności od sposobu korzystania z kont. Wszystkie wykluczenia powinny być regularnie przeglądane w celu sprawdzenia, czy nadal mają zastosowanie.

Skonfigurowane lokalizacje zaufanej [sieci](../conditional-access/location-condition.md) są używane przez funkcję ochrony tożsamości podczas niektórych wykryć ryzyka, aby zmniejszyć liczbę fałszywie dodatnich.

## <a name="enable-policies"></a>Włącz zasady

Aby włączyć ryzyko dla użytkownika i zasady dotyczące ryzyka związanego z logowaniem, wykonaj następujące czynności.

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do omówienia usługi **Azure Active Directory**  >  **Security**  >  **Identity Protection**  >  **Overview**.
1. Wybierz pozycję **zasady ryzyka dla użytkowników**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
      1. **Warunki**  -  **Ryzyko użytkownika** Zalecenie firmy Microsoft polega na ustawieniu tej opcji na **wysoki**.
   1. W obszarze **Controls**
      1. **Dostęp** — zalecenia firmy Microsoft mają **na celu umożliwienie dostępu** i **wymaganie zmiany hasła**.
   1. **Wymuś zasady**  -  **Na**
   1. **Zapisz** — ta akcja spowoduje powrót do strony **Przegląd** .
1. Wybierz pozycję **zasady dotyczące ryzyka związanego z logowaniem**.
   1. W obszarze **przypisania**
      1. **Użytkownicy** — wybierz opcję **Wszyscy użytkownicy** lub **Wybierz osoby i grupy w** przypadku ograniczenia wdrożenia.
         1. Opcjonalnie możesz zdecydować się na wykluczenie użytkowników z zasad.
      1. **Warunki**  -  **Ryzyko związane z logowaniem** Zalecenie firmy Microsoft polega na ustawieniu tej opcji na **średni i wyższy**.
   1. W obszarze **Controls**
      1. **Dostęp** — zalecenia firmy Microsoft mają na celu **umożliwienie dostępu** i **wymaganie uwierzytelniania wieloskładnikowego**.
   1. **Wymuś zasady**  -  **Na**
   1. **Zapisz**

## <a name="next-steps"></a>Następne kroki

- [Włącz zasady rejestracji Multi-Factor Authentication usługi Azure AD](howto-identity-protection-configure-mfa-policy.md)

- [Co to jest ryzyko](concept-identity-protection-risks.md)

- [Badanie wykryć ryzyka](howto-identity-protection-investigate-risk.md)

- [Symulowanie wykryć ryzyka](howto-identity-protection-simulate-risk.md)
