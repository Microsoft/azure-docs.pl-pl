---
title: Zarządzanie użytkownikami wykluczonymi z zasad dostępu warunkowego — Azure AD
description: Dowiedz się, jak za pomocą przeglądów dostępu w usłudze Azure Active Directory (Azure AD) zarządzać użytkownikami, którzy zostali wykluczeni z zasad dostępu warunkowego
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 426e28048ae370919529ea710717a3a3867d999d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746256"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Korzystanie z przeglądów dostępu usługi Azure AD do zarządzania użytkownikami wykluczonymi z zasad dostępu warunkowego

W idealnym świecie wszyscy użytkownicy przestrzegają zasad dostępu w celu zabezpieczenia dostępu do zasobów organizacji. Czasami jednak istnieją przypadki biznesowe, które wymagają wprowadzenia wyjątków. W tym artykule przedstawiono kilka przykładów sytuacji, w których może być konieczne wyłączenie. Administrator IT może zarządzać tym zadaniem, unikać nadzoru wyjątków zasad i zapewniać audytorom dowód, że te wyjątki są regularnie analizowane za pomocą przeglądów dostępu Azure Active Directory (Azure AD).

>[!NOTE]
> Do korzystania z przeglądów dostępu do usługi Azure AD wymagana jest prawidłowa Azure AD — wersja Premium P2, Enterprise Mobility + Security E5 lub wersja próbna. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Dlaczego należy wykluczyć użytkowników z zasad?

Załóżmy, że jako administrator zdecyduje się użyć [dostępu warunkowego usługi Azure AD](../conditional-access/concept-conditional-access-policy-common.md) , aby wymagać uwierzytelniania wieloskładnikowego (MFA) i ograniczyć żądania uwierzytelniania do określonych sieci lub urządzeń. Podczas planowania wdrożenia należy pamiętać, że nie wszyscy użytkownicy mogą spełnić te wymagania. Na przykład użytkownicy mogą korzystać z zdalnych biur, a nie części sieci wewnętrznej. Może być również konieczne, aby umożliwić użytkownikom łączenie się za pomocą nieobsługiwanych urządzeń podczas oczekiwania na zamienienie tych urządzeń. W skrócie firma wymaga od użytkowników zalogowania się i wykonania zadania w celu wykluczenia ich z zasad dostępu warunkowego.

Innym przykładem może być użycie [nazwanych lokalizacji](../conditional-access/location-condition.md) w dostępie warunkowym w celu określenia zestawu krajów i regionów, z których użytkownik nie chce zezwalać użytkownikom na dostęp do swojej dzierżawy.

![Nazwane lokalizacje w dostępie warunkowym](./media/conditional-access-exclusion/named-locations.png)

Niestety niektórzy użytkownicy mogą nadal mieć ważną przyczynę zalogowania się z tych zablokowanych krajów/regionów. Na przykład użytkownicy mogą Podróżujący do pracy i muszą uzyskiwać dostęp do zasobów firmy. W takim przypadku zasady dostępu warunkowego do blokowania tych krajów/regionów mogą korzystać z grupy zabezpieczeń chmury dla wykluczonych użytkowników z zasad. Użytkownicy, którzy potrzebują dostępu w podróży, mogą dodać siebie do grupy przy użyciu samoobsługowego [zarządzania grupami w usłudze Azure AD](../enterprise-users/groups-self-service-management.md).

Innym przykładem mogą być zasady dostępu warunkowego [blokujące starsze uwierzytelnianie dla większości użytkowników](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Jednak w przypadku niektórych użytkowników, którzy muszą używać starszych metod uwierzytelniania w celu uzyskania dostępu do zasobów za pośrednictwem klientów pakietu Office 2010 lub protokołu IMAP/wyskakujących okienek, można wykluczyć tych użytkowników z zasad, które blokują starsze metody uwierzytelniania.

>[!NOTE]
>Firma Microsoft zdecydowanie zaleca zablokowanie użycia starszych protokołów w dzierżawie w celu usprawnienia stan zabezpieczeń.

## <a name="why-are-exclusions-challenging"></a>Dlaczego wyjątki są trudne?

W usłudze Azure AD można ograniczyć zakres zasad dostępu warunkowego do zestawu użytkowników. Wykluczenia można także skonfigurować, wybierając pozycję Role usługi Azure AD, poszczególnych użytkowników lub Gości. Należy pamiętać, że po skonfigurowaniu wykluczeń nie można wymusić przeznaczenie zasad dla wykluczonych użytkowników. Jeśli wykluczenia są skonfigurowane przy użyciu listy użytkowników lub starszych lokalnych grup zabezpieczeń, będziesz mieć ograniczoną widoczność wykluczeń. W efekcie:

- Użytkownicy mogą nie wiedzieć, że są wykluczeni.

- Użytkownicy mogą dołączać grupę zabezpieczeń w celu obejścia zasad.

- Wykluczeni użytkownicy mogą zostać zakwalifikowani do wykluczenia, ale nie mogą już z niego korzystać.

Często podczas pierwszej konfiguracji wykluczenia istnieje Shortlist użytkowników, którzy pomijają zasady. Wraz z upływem czasu więcej niż jeden użytkownik zostanie dodany do wykluczenia, a lista rośnie. W pewnym momencie należy przejrzeć listę i potwierdzić, że każdy z tych użytkowników nadal kwalifikuje się do wykluczenia. Zarządzanie listą wykluczeń z punktu widzenia technicznego może być stosunkowo proste, ale kto podejmuje decyzje biznesowe i jak upewnić się, że jest on dostępny do inspekcji? Jeśli jednak zostanie skonfigurowane wykluczenie przy użyciu grupy usługi Azure AD, można użyć przeglądów dostępu jako kontroli kompensowania, aby zwiększyć widoczność dysku i zmniejszyć liczbę wykluczonych użytkowników.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Jak utworzyć grupę wykluczeń w zasadach dostępu warunkowego

Wykonaj następujące kroki, aby utworzyć nową grupę usługi Azure AD i zasady dostępu warunkowego, które nie mają zastosowania do tej grupy.

### <a name="create-an-exclusion-group"></a>Tworzenie grupy wykluczeń

1. Zaloguj się w witrynie Azure Portal.

2. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **grupy**.

3. W górnym menu kliknij pozycję **Nowa grupa** , aby otworzyć okienko grupy.

4. Z listy **Typ grupy** wybierz pozycję **zabezpieczenia**. Określ nazwę i opis.

5. Upewnij się, że typ **członkostwa** jest ustawiony na wartość **przypisane**.

6. Wybierz użytkowników, którzy powinni być częścią tej grupy wykluczeń, a następnie kliknij przycisk **Utwórz**.

![Okienko nowej grupy w Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Tworzenie zasad dostępu warunkowego, które nie wykluczają grupy

Teraz można utworzyć zasady dostępu warunkowego, które korzystają z tej grupy wykluczeń.

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **dostęp warunkowy** , aby otworzyć blok **zasady** .

2. Kliknij pozycję **nowe zasady** , aby otworzyć **nowe** okienko.

3. Określ nazwę.

4. W obszarze przypisania kliknij pozycję **Użytkownicy i grupy**.

5. Na karcie **Dołącz** wybierz pozycję **Wszyscy użytkownicy**.

6. Na karcie **Wyklucz** , Dodaj znacznik wyboru do opcji **Użytkownicy i grupy** , a następnie kliknij pozycję **Wybierz wykluczonych użytkowników**.

7. Wybierz utworzoną grupę wykluczeń.

   > [!NOTE] 
   > Najlepszym rozwiązaniem jest wykluczenie co najmniej jednego konta administratora z zasad podczas testowania, aby upewnić się, że nie masz zablokowanej dzierżawy.

8. Kontynuuj Konfigurowanie zasad dostępu warunkowego zgodnie z wymaganiami organizacji.

![Wybieranie okienka wykluczonych użytkowników w dostępie warunkowym](./media/conditional-access-exclusion/select-excluded-users.png)
  
Zapoznajmy się z dwoma przykładami, które umożliwiają zarządzanie wykluczeniami w zasadach dostępu warunkowego.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Przykład 1: przegląd dostępu dla użytkowników uzyskujących dostęp z zablokowanych krajów/regionów

Załóżmy, że masz zasady dostępu warunkowego, które blokują dostęp z określonych krajów/regionów. Obejmuje grupę, która jest wykluczona z zasad. Poniżej znajduje się Zalecany przegląd dostępu, w którym są przeglądane członkowie grupy.

> [!NOTE] 
> Aby można było tworzyć przeglądy dostępu, wymagana jest rola administratora globalnego lub administratora użytkowników.

1. Przegląd będzie miał miejsce co tydzień.

2. Nie zakończy się, aby upewnić się, że ta grupa wykluczeń jest aktualna.

3. Wszystkie elementy członkowskie tej grupy będą znajdować się w zakresie przeglądu.

4. Każdy użytkownik będzie musiał zaświadczać, że nadal potrzebują dostępu z tych zablokowanych krajów/regionów, dlatego nadal musi być członkiem grupy.

5. Jeśli użytkownik nie odpowie na żądanie przejrzenia, zostanie automatycznie usunięty z grupy i nie będzie miał już dostępu do dzierżawy podczas podróży do tych krajów/regionów.

6. Włącz powiadomienia e-mail, aby umożliwić użytkownikom zapoznanie się z rozpoczęciem i zakończeniem przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu na przykład 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Przykład 2: przegląd dostępu dla użytkowników korzystających z starszego uwierzytelniania

Załóżmy, że masz zasady dostępu warunkowego, które blokują dostęp dla użytkowników korzystających ze starszego uwierzytelniania i starszych wersji klienta i zawierają grupę, która jest wykluczona z zasad. Poniżej znajduje się Zalecany przegląd dostępu, w którym są przeglądane członkowie grupy.

1. Ten przegląd musi być cyklicznym przeglądem.

2. Należy przejrzeć wszystkie osoby w grupie.

3. Można ją skonfigurować tak, aby wystawiali właścicieli jednostek firmy jako wybranych recenzentów.

4. Stosuj autouwierzytelnianie i Usuń użytkowników, którzy nie zostali zatwierdzeni, aby nadal używać starszych metod uwierzytelniania.

5. Warto włączyć zalecenia, aby recenzenci dużych grup mogli w łatwy sposób podejmować decyzje.

6. Włącz powiadomienia e-mail, aby użytkownicy byli powiadamiani o rozpoczęciu i zakończeniu przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu na przykład 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Jeśli masz wiele grup wykluczeń i w związku z tym trzeba utworzyć wiele przeglądów dostępu, w punkcie końcowym Microsoft Graph wersji beta jest teraz dostępny interfejs API, który umożliwia programowe tworzenie i zarządzanie nimi. Aby rozpocząć, zobacz informacje o [interfejsie API przeglądów usługi Azure AD](/graph/api/resources/accessreviews-root?view=graph-rest-beta) i [przykład pobierania przeglądów dostępu do usługi Azure AD za pośrednictwem Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Wyniki przeglądu dostępu i dzienniki inspekcji

Teraz, gdy masz wszystko na bieżąco, grupy, zasady dostępu warunkowego i przeglądy dostępu, jest czas na monitorowanie i śledzenie wyników tych przeglądów.

1. W Azure Portal Otwórz blok **przeglądy dostępu** .

2. Otwórz formant i program utworzony w celu zarządzania grupą wykluczeń.

3. Kliknij pozycję **wyniki** , aby zobaczyć, kto został zatwierdzony do pozostawania na liście i kto został usunięty.

    ![Wyniki przeglądów dostępu pokazują, kto został zatwierdzony](./media/conditional-access-exclusion/access-reviews-results.png)

4. Następnie kliknij pozycję **dzienniki inspekcji** , aby wyświetlić akcje, które zostały wykonane podczas tego przeglądu.

    ![Przeglądy inspekcji dostępu Inspekcja listy akcje](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako administrator IT wiesz, że zarządzanie grupami wykluczeń do zasad jest czasami nieuniknione. Jednak utrzymywanie tych grup, przeglądanie ich regularnie przez właściciela firmy lub samych użytkowników, a także Inspekcja tych zmian można ułatwić przy użyciu przeglądów dostępu do usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)