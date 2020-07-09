---
title: Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory | Microsoft Docs
description: Informacje o kwestiach, które należy znać, oraz o tym, co należy unikać podczas konfigurowania zasad dostępu warunkowego.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63cb1d7e2b0086a3d9ef6e3917ebefa11c7ccba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253379"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory

Za pomocą [dostępu warunkowego usługi Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)można kontrolować sposób, w jaki autoryzowani użytkownicy uzyskują dostęp do aplikacji w chmurze. Ten artykuł zawiera informacje na temat:

- Rzeczy, które należy znać 
- Co należy unikać podczas konfigurowania zasad dostępu warunkowego. 

W tym artykule założono, że znasz koncepcje i terminologię podaną w temacie [co to jest dostęp warunkowy w Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Co jest wymagane do wprowadzenia zasad?

Podczas tworzenia nowych zasad nie wybrano żadnych użytkowników, grup, aplikacji ani kontroli dostępu.

![Aplikacje w chmurze](./media/best-practices/02.png)

Aby zasady działały, należy skonfigurować:

| Elementy           | W jaki sposób?                                  | Zalet |
| :--            | :--                                  | :-- |
| **Aplikacje w chmurze** |Wybierz co najmniej jedną aplikację.  | Celem zasad dostępu warunkowego jest umożliwienie kontroli nad sposobem, w jaki autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze.|
| **Użytkownicy i grupy** | Wybierz co najmniej jednego użytkownika lub grupę, która ma uprawnienia dostępu do wybranych aplikacji w chmurze. | Zasady dostępu warunkowego, które nie mają przypisanych użytkowników i grup, nigdy nie są wyzwalane. |
| **Kontrola dostępu** | Wybierz co najmniej jedną kontrolę dostępu. | Jeśli warunki są spełnione, procesor zasad musi wiedzieć, co należy zrobić. |

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="how-are-conditional-access-policies-applied"></a>Jak są stosowane zasady dostępu warunkowego?

Podczas uzyskiwania dostępu do aplikacji w chmurze mogą być stosowane więcej niż jedna zasada dostępu warunkowego. W takim przypadku wszystkie zasady, które mają zastosowanie, muszą być spełnione. Na przykład jeśli jedna zasada wymaga uwierzytelniania wieloskładnikowego (MFA), a inna wymaga zgodnego urządzenia, należy przeprowadzić uwierzytelnianie wieloskładnikowe i korzystać z zgodnego urządzenia. 

Wszystkie zasady są wymuszane w dwóch fazach:

- Etap 1: 
   - Kolekcja Szczegółowa: Zbierz szczegóły, aby zidentyfikować zasady, które zostały już spełnione.
   - W tej fazie użytkownicy mogą wyświetlić monit o podanie certyfikatu, jeśli zgodność urządzenia jest częścią zasad dostępu warunkowego. Ten monit może wystąpić w przypadku aplikacji przeglądarki, gdy system operacyjny urządzenia nie jest systemem Windows 10.
   - Faza 1 oceny zasad występuje dla wszystkich włączonych zasad i zasad w [trybie tylko do raportowania](concept-conditional-access-report-only.md).
- Etap 2:
   - Wymuszanie: biorąc pod uwagę Szczegóły zebrane w fazie 1, Zażądaj od użytkownika spełnienia wszelkich dodatkowych wymagań, które nie zostały spełnione.
   - Zastosuj wyniki do sesji. 
   - Faza 2 oceny zasad występuje dla wszystkich włączonych zasad.

### <a name="how-are-assignments-evaluated"></a>Jak są oceniane przypisania?

Wszystkie przydziały są logicznie **ANDed**. W przypadku skonfigurowania więcej niż jednego przypisania należy spełnić wszystkie przypisania, aby wyzwolić zasady.  

Jeśli trzeba skonfigurować warunek lokalizacji dotyczący wszystkich połączeń wykonanych poza siecią organizacji:

- Uwzględnij **wszystkie lokalizacje**
- Wyklucz **wszystkie Zaufane adresy IP**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co należy zrobić, jeśli masz zablokowany Portal administratora usługi Azure AD?

Jeśli masz zablokowany dostęp do portalu usługi Azure AD z powodu niepoprawnego ustawienia zasad dostępu warunkowego:

- Sprawdź, czy w organizacji znajdują się inni administratorzy, którzy nie zostali jeszcze Zablokowani. Administrator z dostępem do Azure Portal może wyłączyć zasady, które mają wpływ na logowanie. 
- Jeśli żadna z administratorów w organizacji nie może zaktualizować zasad, należy przesłać żądanie pomocy technicznej. Pomoc techniczna firmy Microsoft może przeglądać i aktualizować zasady dostępu warunkowego, które uniemożliwiają dostęp.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co się stanie w przypadku, gdy masz zasady w klasycznym portalu Azure i Azure Portal skonfigurowany?  

Obie zasady są wymuszane przez Azure Active Directory, a użytkownik uzyskuje dostęp tylko po spełnieniu wszystkich wymagań.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co się stanie w przypadku posiadania zasad w portalu usługi Intune Silverlight i Azure Portal?

Obie zasady są wymuszane przez Azure Active Directory, a użytkownik uzyskuje dostęp tylko po spełnieniu wszystkich wymagań.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co się stanie, jeśli dla tego samego użytkownika skonfigurowano wiele zasad?  

Dla każdego logowania Azure Active Directory oblicza wszystkie zasady i gwarantuje, że wszystkie wymagania są spełnione przed udzieleniem dostępu użytkownikowi. Blokuj dostęp ma priorytet przed wszystkie inne ustawienia konfiguracji. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Czy dostęp warunkowy działa z programem Exchange ActiveSync?

Tak, można użyć programu Exchange ActiveSync w zasadach dostępu warunkowego.

Niektóre aplikacje w chmurze, takie jak SharePoint Online i Exchange Online, obsługują również starsze protokoły uwierzytelniania. Gdy aplikacja kliencka może korzystać z starszego protokołu uwierzytelniania w celu uzyskania dostępu do aplikacji w chmurze, usługa Azure AD nie może wymusić zasady dostępu warunkowego dla tej próby dostępu. Aby uniemożliwić aplikacji klienta pomijanie wymuszania zasad, należy sprawdzić, czy można włączyć tylko nowoczesne uwierzytelnianie w aplikacjach w chmurze, których to dotyczy.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Jak należy skonfigurować dostęp warunkowy przy użyciu aplikacji pakietu Office 365?

Ze względu na to, że aplikacje pakietu Office 365 są połączone, zalecamy przypisanie często używanych aplikacji podczas tworzenia zasad.

Często połączone aplikacje obejmują Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online i Office 365 Yammer.

Jest to ważne w przypadku zasad wymagających interakcji użytkownika, takich jak uwierzytelnianie wieloskładnikowe, gdy dostęp jest kontrolowany na początku sesji lub zadania. W przeciwnym razie użytkownicy nie będą mogli wykonywać niektórych zadań w ramach aplikacji. Na przykład, jeśli wymagane jest uwierzytelnianie wieloskładnikowe na urządzeniach niezarządzanych w celu uzyskania dostępu do programu SharePoint, ale nie do poczty e-mail, użytkownicy pracujący w ich wiadomości e-mail nie będą mogli dołączać plików programu SharePoint do wiadomości. Więcej informacji można znaleźć w artykule [co to są zależności usługi w Azure Active Directory dostęp warunkowy?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Co należy unikać

Struktura dostępu warunkowego zapewnia dużą elastyczność konfiguracji. Jednak doskonałe elastyczność oznacza, że należy uważnie przejrzeć każdą zasadę konfiguracji przed jej zwolnieniem, aby uniknąć niepożądanych wyników. W tym kontekście należy zwrócić szczególną uwagę na przydziały mające wpływ na kompletne zestawy, takie jak **Wszyscy użytkownicy/grupy/aplikacje w chmurze**.

W danym środowisku należy unikać następujących konfiguracji:

**Dla wszystkich użytkowników wszystkie aplikacje w chmurze:**

- **Blokuj dostęp** — ta konfiguracja blokuje całą organizację, która nie jest dobrym pomysłem.
- **Wymagaj zgodnego urządzenia** — dla użytkowników, którzy nie zarejestrowali jeszcze urządzeń, ta zasada blokuje dostęp do portalu usługi Intune. Jeśli jesteś administratorem programu bez zarejestrowanego urządzenia, ta zasada uniemożliwia powrót do Azure Portal, aby zmienić zasady.
- **Wymagaj przyłączenia do domeny** — ta zasada blokuje również możliwość blokowania dostępu dla wszystkich użytkowników w organizacji, jeśli nie masz jeszcze urządzenia przyłączonego do domeny.
- **Wymagaj zasad ochrony aplikacji** — ten blok dostępu blokuje również możliwość blokowania dostępu dla wszystkich użytkowników w organizacji, jeśli nie masz zasad usługi Intune. Jeśli jesteś administratorem bez aplikacji klienckiej z zasadami ochrony aplikacji usługi Intune, te zasady uniemożliwiają powrót do portali, takich jak usługa Intune i platforma Azure.

**Dla wszystkich użytkowników, wszystkie aplikacje w chmurze, wszystkie platformy urządzeń:**

- **Blokuj dostęp** — ta konfiguracja blokuje całą organizację, która nie jest dobrym pomysłem.

## <a name="how-should-you-deploy-a-new-policy"></a>Jak należy wdrożyć nowe zasady?

Najpierw należy oszacować zasady za pomocą [Narzędzia](what-if-tool.md)do wykonywania.

Jeśli nowe zasady są gotowe dla danego środowiska, wdróż je w fazach:

1. Zastosuj zasady do małego zestawu użytkowników i sprawdź, czy działa ono zgodnie z oczekiwaniami. 
1. Po rozszerzeniu zasad w celu uwzględnienia większej liczby użytkowników. Nadal Wyklucz wszystkich administratorów z zasad, aby upewnić się, że nadal mają dostęp, i zaktualizuj zasady, jeśli jest wymagana zmiana.
1. Zastosuj zasady dla wszystkich użytkowników tylko w razie potrzeby. 

Najlepszym rozwiązaniem jest utworzenie konta użytkownika, które jest następujące:

- Przeznaczone do administrowania zasadami 
- Wykluczone ze wszystkich zasad

## <a name="policy-migration"></a>Migracja zasad

Rozważ Migrowanie zasad, które nie zostały utworzone w Azure Portal, ponieważ:

- Teraz możesz rozwiązywać scenariusze, których nie można obsłużyć wcześniej.
- Aby zmniejszyć liczbę zasad, którymi trzeba zarządzać, można je skonsolidować.   
- Można zarządzać wszystkimi zasadami dostępu warunkowego w jednej centralnej lokalizacji.
- Klasyczny portal Azure został wycofany.   

Aby uzyskać więcej informacji, zobacz artykuł [Migrate classic policies in the Azure portal (Migrowanie zasad klasycznych w witrynie Azure Portal)](policy-migration.md).

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz poznać następujące informacje:

- Jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Jak planować zasady dostępu warunkowego, zobacz [jak zaplanować wdrożenie dostępu warunkowego w Azure Active Directory](plan-conditional-access.md).
