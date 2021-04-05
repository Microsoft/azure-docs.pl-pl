---
title: Zasady ochrony aplikacji z dostępem warunkowym — Azure Active Directory
description: Dowiedz się, jak wymagać zasad ochrony aplikacji dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720cd46ac37a82f56aa37c0041ca8d92db177071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575775"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Instrukcje: wymaganie zasad ochrony aplikacji oraz zatwierdzonej aplikacji klienckiej do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego

Osoby regularnie korzystają z urządzeń przenośnych zarówno do zadań osobistych, jak i służbowych. Mimo że pracownicy mogą pracować wydajnie, organizacje chcą również zapobiec utracie danych z potencjalnie niezabezpieczonych aplikacji. W przypadku dostępu warunkowego organizacje mogą ograniczyć dostęp do zatwierdzonych aplikacji klienckich (nowoczesnego uwierzytelniania) przy użyciu zasad ochrony aplikacji usługi Intune.

W tym artykule przedstawiono trzy scenariusze konfigurowania zasad dostępu warunkowego dla zasobów, takich jak Microsoft 365, Exchange Online i SharePoint.

- [Scenariusz 1: aplikacje Microsoft 365 wymagają zatwierdzonych aplikacji przy użyciu zasad ochrony aplikacji](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenariusz 2: aplikacje przeglądarki wymagają zatwierdzonych aplikacji z zasadami ochrony aplikacji](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Scenariusz 3: usługi Exchange Online i SharePoint wymagają zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

W przypadku dostępu warunkowego te aplikacje klienckie są nazywane ochroną za pomocą zasad ochrony aplikacji. Więcej informacji na temat zasad ochrony aplikacji można znaleźć w artykule [Omówienie zasad ochrony aplikacji](/intune/apps/app-protection-policy)

> [!WARNING]
> Nie wszystkie aplikacje są obsługiwane jako zatwierdzone aplikacje lub obsługują zasady ochrony aplikacji. Aby uzyskać listę kwalifikujących się aplikacji klienckich, zobacz [wymagania dotyczące zasad ochrony aplikacji](concept-conditional-access-grant.md#require-app-protection-policy).

> [!NOTE]
> "Wymagaj jednej z wybranych kontrolek" w obszarze kontrolki dotacji jest taka sama jak klauzula OR. Ta wartość jest używana w ramach zasad, aby umożliwić użytkownikom korzystanie z aplikacji, które obsługują kontrolki **Wymagaj ochrony aplikacji** lub **wymagają zatwierdzonej aplikacji klienckiej** . **Ustawienie Wymagaj zasad ochrony aplikacji** jest wymuszane, gdy aplikacja obsługuje tę kontrolę. Aby uzyskać więcej informacji o tym, które aplikacje obsługują kontrolę przydzielenia **zasad ochrony aplikacji** , zobacz [wymagania dotyczące zasad ochrony aplikacji](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenariusz 1: aplikacje Microsoft 365 wymagają zatwierdzonych aplikacji przy użyciu zasad ochrony aplikacji

W tym scenariuszu firma Contoso zdecydowała się, że wszystkie dostępy mobilne do zasobów Microsoft 365 muszą używać zatwierdzonych aplikacji klienckich, takich jak Outlook Mobile i OneDrive, chronione przez zasady ochrony aplikacji przed uzyskaniem dostępu. Wszyscy użytkownicy logują się już przy użyciu poświadczeń usługi Azure AD i mają przypisane licencje, które obejmują Azure AD — wersja Premium P1 lub P2 i Microsoft Intune.

Aby wymagać użycia zatwierdzonej aplikacji klienckiej na urządzeniach przenośnych, organizacje muszą wykonać następujące czynności.

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla Microsoft 365**

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**  >  **Uwzględnij** opcję **Office 365**.
1. W obszarze **warunki** wybierz pozycję **platformy urządzeń**.
   1. Ustaw **wartość** **tak**.
   1. Uwzględnij **systemy Android** i **iOS**.
1. W obszarze **warunki** wybierz pozycję **aplikacje klienckie**.
   1. Ustaw **wartość** **tak**.
   1. Wybierz pozycję **aplikacje mobilne i klienci stacjonarni** i usuń zaznaczenie wszystkich innych elementów.
1. W obszarze **kontrole dostępu**  >  **Udziel** wybierz następujące opcje:
   - **Wymaganie zatwierdzonej aplikacji klienckiej**
   - **Wymaganie zasad ochrony aplikacji**
   - **Wymagaj jednej z wybranych kontrolek**
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 2. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla usługi Exchange Online z programem ActiveSync (EAS)**

W przypadku zasad dostępu warunkowego w tym kroku skonfiguruj następujące składniki:

1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**  >  **Uwzględnij** opcję **Office 365 Exchange Online**.
1. W obszarze **warunki** wybierz pozycję **aplikacje klienckie**:
   1. Ustaw **wartość** **tak**.
   1. Wybierz pozycję **klienci programu Exchange ActiveSync** i usuń zaznaczenie wszystkich innych elementów.
1. W obszarze **Kontrola dostępu**  >  **Przydziel**, wybierz pozycję **Udziel dostępu**, **Wymagaj zasad ochrony aplikacji** i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

Zapoznaj się z artykułem [jak utworzyć i przypisać zasady ochrony aplikacji](/intune/apps/app-protection-policies), aby zapoznać się z procedurą tworzenia zasad ochrony aplikacji dla systemów Android i iOS. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Scenariusz 2: aplikacje przeglądarki wymagają zatwierdzonych aplikacji z zasadami ochrony aplikacji

W tym scenariuszu firma Contoso zdecydowała się, że wszystkie dostępy do sieci Web dla urządzeń przenośnych do zasobów Microsoft 365 muszą używać zatwierdzonej aplikacji klienckiej, takiej jak Edge dla systemów iOS i Android, chronione przez zasady ochrony aplikacji przed uzyskaniem dostępu. Wszyscy użytkownicy logują się już przy użyciu poświadczeń usługi Azure AD i mają przypisane licencje, które obejmują Azure AD — wersja Premium P1 lub P2 i Microsoft Intune.

Aby wymagać użycia zatwierdzonej aplikacji klienckiej na urządzeniach przenośnych, organizacje muszą wykonać następujące czynności.

**Krok 1. Konfigurowanie zasad dostępu warunkowego usługi Azure AD dla Microsoft 365**

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**  >  **Uwzględnij** opcję **Office 365**.
1. W obszarze **warunki** wybierz pozycję **platformy urządzeń**.
   1. Ustaw **wartość** **tak**.
   1. Uwzględnij **systemy Android** i **iOS**.
1. W obszarze **warunki** wybierz pozycję **aplikacje klienckie**.
   1. Ustaw **wartość** **tak**.
   1. Wybierz pozycję **przeglądarka** i usuń zaznaczenie opcji wszystkie inne.
1. W obszarze **kontrole dostępu**  >  **Udziel** wybierz następujące opcje:
   - **Wymaganie zatwierdzonej aplikacji klienckiej**
   - **Wymaganie zasad ochrony aplikacji**
   - **Wymagaj jednej z wybranych kontrolek**
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 2. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android**

Zapoznaj się z artykułem [jak utworzyć i przypisać zasady ochrony aplikacji](/intune/apps/app-protection-policies), aby zapoznać się z procedurą tworzenia zasad ochrony aplikacji dla systemów Android i iOS. 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>Scenariusz 3: usługi Exchange Online i SharePoint wymagają zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji

W tym scenariuszu firma Contoso zdecydowała się, że użytkownicy mogą uzyskiwać dostęp tylko do danych poczty e-mail i programu SharePoint na urządzeniach przenośnych, o ile korzystają z zatwierdzonej aplikacji klienckiej, takiej jak Outlook Mobile chroniona przez zasady ochrony aplikacji przed uzyskaniem dostępu. Wszyscy użytkownicy logują się już przy użyciu poświadczeń usługi Azure AD i mają przypisane licencje, które obejmują Azure AD — wersja Premium P1 lub P2 i Microsoft Intune.

Aby wymagać użycia zatwierdzonej aplikacji klienckiej na urządzeniach przenośnych i klientach programu Exchange ActiveSync, organizacje muszą wykonać następujące trzy kroki.

**Krok 1. zasady dla klientów korzystających z nowoczesnego uwierzytelniania systemu Android i iOS wymagające użycia zatwierdzonej aplikacji klienckiej i zasad ochrony aplikacji podczas uzyskiwania dostępu do usługi Exchange Online i programu SharePoint.**

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**  >  **Uwzględnij** opcję **Office 365 Exchange Online** i **Office 365 SharePoint Online**.
1. W obszarze **warunki** wybierz pozycję **platformy urządzeń**.
   1. Ustaw **wartość** **tak**.
   1. Uwzględnij **systemy Android** i **iOS**.
1. W obszarze **warunki** wybierz pozycję **aplikacje klienckie**.
   1. Ustaw **wartość** **tak**.
   1. Wybierz pozycję **aplikacje mobilne i klienci stacjonarni** i usuń zaznaczenie wszystkich innych elementów.
1. W obszarze **kontrole dostępu**  >  **Udziel** wybierz następujące opcje:
   - **Wymaganie zatwierdzonej aplikacji klienckiej**
   - **Wymaganie zasad ochrony aplikacji**
   - **Wymagaj jednej z wybranych kontrolek**
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 2. zasady dla klientów programu Exchange ActiveSync wymagające użycia zatwierdzonej aplikacji klienckiej.**

1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**  >  **Uwzględnij** opcję **Office 365 Exchange Online**.
1. W obszarze **warunki** wybierz pozycję **aplikacje klienckie**:
   1. Ustaw **wartość** **tak**.
   1. Wybierz pozycję **klienci programu Exchange ActiveSync** i usuń zaznaczenie wszystkich innych elementów.
1. W obszarze **Kontrola dostępu**  >  **Przydziel**, wybierz pozycję **Udziel dostępu**, **Wymagaj zasad ochrony aplikacji** i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

**Krok 3. Konfigurowanie zasad ochrony aplikacji usługi Intune dla aplikacji klienckich dla systemów iOS i Android.**

Zapoznaj się z artykułem [jak utworzyć i przypisać zasady ochrony aplikacji](/intune/apps/app-protection-policies), aby zapoznać się z procedurą tworzenia zasad ochrony aplikacji dla systemów Android i iOS. 

## <a name="next-steps"></a>Następne kroki

[Co to jest dostęp warunkowy?](overview.md)

[Składniki dostępu warunkowego](concept-conditional-access-policies.md)

[Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

