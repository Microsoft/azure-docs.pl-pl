---
title: Zarządzanie użytkownikami wykluczonym z zasad dostępu warunkowego — Azure AD
description: Dowiedz się, jak używać przeglądów Azure Active Directory (Azure AD) do zarządzania użytkownikami, którzy są wykluczeni z zasad dostępu warunkowego
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
ms.openlocfilehash: d4b2ac36ad1140968fd17db0bed0b60a8aca6a02
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532684"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Używanie przeglądów dostępu usługi Azure AD do zarządzania użytkownikami wykluczonym z zasad dostępu warunkowego

W idealnym świecie wszyscy użytkownicy postępują zgodnie z zasadami dostępu, aby zabezpieczyć dostęp do zasobów organizacji. Jednak czasami istnieją przypadki biznesowe, które wymagają wyjątków. W tym artykule przedstawiono kilka przykładów sytuacji, w których wykluczenia mogą być konieczne. Jako administrator IT możesz zarządzać tym zadaniem, unikać nadzoru nad wyjątkami zasad i dostarczać audytorom dowód, że te wyjątki są regularnie przeglądane przy użyciu przeglądów dostępu usługi Azure Active Directory (Azure AD).

>[!NOTE]
> Do korzystania z przeglądów dostępu w usłudze Azure AD jest wymagana Azure AD — wersja Premium P2, Enterprise Mobility + Security płatna E5 lub licencja próbna. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Dlaczego należy wykluczyć użytkowników z zasad?

Załóżmy, że jako administrator decydujesz się na użycie dostępu warunkowego usługi [Azure AD](../conditional-access/concept-conditional-access-policy-common.md) w celu wymagania uwierzytelniania wieloskładnikowego (MFA) i ograniczenia żądań uwierzytelniania do określonych sieci lub urządzeń. Podczas planowania wdrożenia zdajesz sobie sprawę, że nie wszyscy użytkownicy mogą spełnić te wymagania. Na przykład użytkownicy mogą pracować z biur zdalnych, a nie z sieci wewnętrznej. Może być również trzeba uwzględnić użytkowników łączących się przy użyciu nieobsługiwanych urządzeń podczas oczekiwania na zastąpienie tych urządzeń. Krótko mówiąc, firma potrzebuje tych użytkowników, aby się logować i wykonać swoją pracę, aby wykluczyć ich z zasad dostępu warunkowego.

Innym przykładem może być [](../conditional-access/location-condition.md) użycie nazwanych lokalizacji w dostępie warunkowym w celu określenia zestawu krajów i regionów, z których nie chcesz zezwalać użytkownikom na dostęp do ich dzierżawy.

![Nazwane lokalizacje w dostępie warunkowym](./media/conditional-access-exclusion/named-locations.png)

Niestety niektórzy użytkownicy mogą nadal mieć prawidłowy powód, aby logować się z tych zablokowanych krajów/regionów. Na przykład użytkownicy mogą podróżować do pracy i muszą uzyskać dostęp do zasobów firmy. W takim przypadku zasady dostępu warunkowego blokujące te kraje/regiony mogą używać grupy zabezpieczeń w chmurze dla użytkowników wykluczonych z zasad. Użytkownicy, którzy potrzebują dostępu podczas podróży, mogą dodać się do grupy przy użyciu samoobsługowego zarządzania grupą [w usłudze Azure AD.](../enterprise-users/groups-self-service-management.md)

Innym przykładem może być to, że masz zasady dostępu warunkowego blokujące [starsze uwierzytelnianie](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)dla większości użytkowników. Jeśli jednak niektórzy użytkownicy muszą korzystać ze starszych metod uwierzytelniania w celu uzyskania dostępu do zasobów za pośrednictwem klientów opartych na psłudze Office 2010 lub IMAP/SMTP/POP, można wykluczyć tych użytkowników z zasad, które blokują starsze metody uwierzytelniania.

>[!NOTE]
>Firma Microsoft zdecydowanie zaleca zablokowanie korzystania ze starszych protokołów w dzierżawie w celu poprawy poziomu bezpieczeństwa.

## <a name="why-are-exclusions-challenging"></a>Dlaczego wykluczenia są trudne?

W usłudze Azure AD można określić zakres zasad dostępu warunkowego dla zestawu użytkowników. Możesz również skonfigurować wykluczenia, wybierając role usługi Azure AD, poszczególnych użytkowników lub gości. Należy pamiętać, że po skonfigurowaniu wykluczeń nie można wymusić intencji zasad dla wykluczonych użytkowników. Jeśli wykluczenia są konfigurowane przy użyciu listy użytkowników lub starszych lokalnych grup zabezpieczeń, wgląd w wykluczenia będzie ograniczony. W efekcie:

- Użytkownicy mogą nie wiedzieć, że są wykluczeni.

- Użytkownicy mogą dołączyć do grupy zabezpieczeń, aby pominąć zasady.

- Wykluczeni użytkownicy mogą wcześniej kwalifikować się do wykluczenia, ale mogą już nie kwalifikować się do tego wykluczenia.

Często podczas pierwszej konfiguracji wykluczenia istnieje krótka lista użytkowników, którzy pomijają zasady. Z czasem do wykluczenia dodawanych jest coraz więcej użytkowników, a lista rośnie. W pewnym momencie należy przejrzeć listę i potwierdzić, że każdy z tych użytkowników nadal kwalifikuje się do wykluczenia. Zarządzanie listą wykluczeń z technicznego punktu widzenia może być stosunkowo proste, ale kto podejmuje decyzje biznesowe i jak upewnić się, że wszystkie te informacje można poddać inspekcji? Jeśli jednak skonfigurujesz wykluczenie przy użyciu grupy usługi Azure AD, możesz użyć przeglądów dostępu jako kontroli wyrównującej, aby sterować widocznością i zmniejszyć liczbę wykluczonych użytkowników.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Jak utworzyć grupę wykluczeń w zasadach dostępu warunkowego

Wykonaj następujące kroki, aby utworzyć nową grupę usługi Azure AD i zasady dostępu warunkowego, które nie mają zastosowania do tej grupy.

### <a name="create-an-exclusion-group"></a>Tworzenie grupy wykluczeń

1. Zaloguj się w witrynie Azure Portal.

2. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Grupy.**

3. W górnym menu kliknij pozycję **Nowa grupa,** aby otworzyć okienko grupy.

4. Na liście **Typ grupy** wybierz pozycję **Zabezpieczenia**. Określ nazwę i opis.

5. Upewnij się, że dla **ustawienia Typ członkostwa** ustawiono wartość **Przypisane.**

6. Wybierz użytkowników, którzy powinni być częścią tej grupy wykluczeń, a następnie kliknij przycisk **Utwórz.**

![Okienko Nowa grupa w Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Tworzenie zasad dostępu warunkowego, które wykluczają grupę

Teraz możesz utworzyć zasady dostępu warunkowego, które będą używać tej grupy wykluczeń.

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Dostęp warunkowy,** aby otworzyć **blok** Zasady.

2. Kliknij **pozycję Nowe zasady,** aby otworzyć **okienko** Nowe.

3. Określ nazwę.

4. W obszarze Przypisania kliknij pozycję **Użytkownicy i grupy.**

5. Na karcie **Dołączanie** wybierz pozycję **Wszyscy użytkownicy.**

6. Na karcie **Wykluczanie** dodaj znacznik wyboru do opcji Użytkownicy **i grupy,** a następnie kliknij pozycję **Wybierz wykluczonych użytkowników.**

7. Wybierz utworzoną grupę wykluczeń.

   > [!NOTE] 
   > Najlepszym rozwiązaniem jest wykluczenie co najmniej jednego konta administratora z zasad podczas testowania, aby upewnić się, że dzierżawa nie jest zablokowana.

8. Kontynuuj konfigurowanie zasad dostępu warunkowego zgodnie z wymaganiami organizacji.

![Okienko Wybierania wykluczonych użytkowników w dostępie warunkowym](./media/conditional-access-exclusion/select-excluded-users.png)
  
Omówmy dwa przykłady, w których można używać przeglądów dostępu do zarządzania wykluczeniami w zasadach dostępu warunkowego.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Przykład 1: Przegląd dostępu dla użytkowników, którzy mają dostęp z zablokowanych krajów/regionów

Załóżmy, że masz zasady dostępu warunkowego, które blokają dostęp z określonych krajów/regionów. Obejmuje ona grupę, która jest wykluczona z zasad. Poniżej znajduje się zalecany przegląd dostępu, w którym są przeglądani członkowie grupy.

> [!NOTE] 
> Do administrator globalny przeglądów dostępu jest wymagana rola administratora użytkowników lub użytkowników.

1. Przegląd będzie odbywać się co tydzień.

2. Nigdy nie zakończy się, aby upewnić się, że ta grupa wykluczeń jest naj bieżąco.

3. Wszyscy członkowie tej grupy będą w zakresie przeglądu.

4. Każdy użytkownik musi samodzielnie zaświadczyć, że nadal potrzebuje dostępu z tych zablokowanych krajów/regionów, dlatego nadal musi być członkiem grupy.

5. Jeśli użytkownik nie odpowie na żądanie przeglądu, zostanie automatycznie usunięty z grupy i nie będzie miał już dostępu do dzierżawy podczas podróży do tych krajów/regionów.

6. Włącz powiadomienia e-mail, aby umożliwić użytkownikom powiadomienie o rozpoczęciu i zakończeniu przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu, na przykład 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Przykład 2: Przegląd dostępu dla użytkowników, którzy mają dostęp przy użyciu starszego uwierzytelniania

Załóżmy, że masz zasady dostępu warunkowego, które blokają dostęp dla użytkowników korzystających ze starszego uwierzytelniania i starszych wersji klienta i obejmują grupę, która jest wykluczona z zasad. Poniżej znajduje się zalecany przegląd dostępu, w którym są przeglądani członkowie grupy.

1. Ten przegląd musi być przeglądem cyklicznym.

2. Wszyscy w grupie musieliby zostać przejrzeni.

3. Można ją skonfigurować tak, aby zawierała listę właścicieli jednostek biznesowych jako wybranych recenzentów.

4. Zastosuj automatycznie wyniki i usuń użytkowników, którzy nie mają zatwierdzenia, aby nadal korzystać ze starszych metod uwierzytelniania.

5. Włączenie rekomendacji może być korzystne, aby recenzentzy dużych grup mogli łatwo podejmować decyzje.

6. Włącz powiadomienia e-mail, aby użytkownicy otrzymywać powiadomienia o rozpoczęciu i zakończeniu przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu, na przykład 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Jeśli masz wiele grup wykluczeń i w związku z tym musisz utworzyć wiele przeglądów dostępu, mamy teraz interfejs API w punkcie końcowym usługi Microsoft Graph beta, który umożliwia ich programowe tworzenie i zarządzanie nimi. Aby rozpocząć pracę, zobacz dokumentacja interfejsu API przeglądów dostępu usługi [Azure AD](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) i Przykład pobierania przeglądów dostępu usługi Azure AD za [pośrednictwem Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Uzyskiwanie dostępu do wyników przeglądu i dzienników inspekcji

Teraz, gdy masz już wszystko na miejscu, grupę, zasady dostępu warunkowego i przeglądy dostępu, nas czas na monitorowanie i śledzenie wyników tych przeglądów.

1. W Azure Portal otwórz blok **Przeglądy** dostępu.

2. Otwórz kontrolkę i program utworzony do zarządzania grupą wykluczeń.

3. Kliknij **pozycję Wyniki,** aby zobaczyć, kto został zatwierdzony do pozostania na liście i kto został usunięty.

    ![Wyniki przeglądów dostępu pokazują, kto został zatwierdzony](./media/conditional-access-exclusion/access-reviews-results.png)

4. Następnie kliknij **pozycję Dzienniki inspekcji,** aby wyświetlić akcje, które zostały wykonane podczas tego przeglądu.

    ![Dzienniki inspekcji przeglądów dostępu z listą akcji](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako administrator IT wiesz, że zarządzanie grupami wykluczeń w zasadach jest czasami nieuniknione. Jednak konserwacja tych grup, regularne przeglądanie ich przez właściciela firmy lub samych użytkowników oraz inspekcja tych zmian może być łatwiejsza dzięki przeglądom dostępu do usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)