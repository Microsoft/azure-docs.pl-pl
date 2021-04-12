---
title: Zarządzanie dzierżawcami w komercyjnym portalu Marketplace — Azure Marketplace
description: Dowiedz się, jak zarządzać dzierżawcami dla komercyjnego programu Marketplace w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108329"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Zarządzanie dzierżawcami w komercyjnej witrynie Marketplace

**Odpowiednie role**

- Menedżer

Dzierżawa usługi Azure Active Directory (AD), określana również jako *konto służbowe* w tej dokumentacji, stanowi reprezentację organizacji skonfigurowanej w Azure Portal i pomaga zarządzać określonym wystąpieniem usług w chmurze firmy Microsoft dla użytkowników wewnętrznych i zewnętrznych. Jeśli Twoja organizacja subskrybuje usługę firmy Microsoft w chmurze, taką jak Azure, Microsoft Intune lub Microsoft 365, została dla Ciebie ustanowiona dzierżawa usługi Azure AD.

Można skonfigurować wiele dzierżawców do użycia z centrum partnerskim. Możesz to zrobić, jeśli firma ma wiele dzierżawców (na przykład contoso.com, contoso.uk i tak dalej), w tym miejscu możesz połączyć dodatkowe dzierżawy. To skojarzenie umożliwia dodawanie użytkowników i zarządzanie nimi z dodatkowych dzierżawców na koncie komercyjnej witryny Marketplace.

Każdy użytkownik z rolą Menedżera na koncie Centrum partnerskiego będzie miał możliwość dodawania i usuwania dzierżaw usługi Azure AD z konta.

## <a name="add-an-existing-tenant"></a>Dodaj istniejącą dzierżawę

Aby skojarzyć kolejną dzierżawę usługi Azure AD z kontem Centrum partnerskiego:

1. W prawym górnym rogu Centrum partnerskiego wybierz pozycję **Ustawienia**  >  **Ustawienia konta**.
1. W obszarze **profil organizacji** wybierz pozycję **dzierżawy**. Są wyświetlane bieżące skojarzenia dzierżawców.
1. Na karcie **deweloper** wybierz pozycję **Skojarz**.
1. Wprowadź swoje poświadczenia usługi Azure AD dla dzierżawy, którą chcesz skojarzyć.
1. Przejrzyj nazwę organizacji i domenę dzierżawy usługi Azure AD. Aby zakończyć skojarzenie, wybierz pozycję **Potwierdź**.

Jeśli skojarzenie zakończy się pomyślnie, będziesz gotowy do dodawania użytkowników konta i zarządzania nimi w sekcji Użytkownicy w centrum partnerskim. Aby dowiedzieć się więcej o dodawaniu użytkowników, zobacz [Zarządzanie użytkownikami](add-manage-users.md).

## <a name="create-a-new-tenant"></a>Tworzenie nowej dzierżawy

Aby utworzyć nową dzierżawę usługi Azure AD przy użyciu konta Centrum partnerskiego:

1. W prawym górnym rogu Centrum partnerskiego wybierz pozycję **Ustawienia**  >  **Ustawienia konta**.
1. W obszarze **profil organizacji** wybierz pozycję **dzierżawy**. Są wyświetlane bieżące skojarzenia dzierżawców.
1. Na karcie Deweloper wybierz pozycję **Utwórz**.
1. Wprowadź informacje o katalogu dla nowej usługi Azure AD:
    - **Nazwa domeny**: unikatowa nazwa, która będzie używana dla domeny usługi Azure AD wraz z ". onmicrosoft.com". Na przykład, jeśli wprowadzono "przykład", domena usługi Azure AD będzie "example.onmicrosoft.com".
    - **Kontaktowy adres e-mail**: w razie potrzeby można skontaktować się z Tobą w sprawie Twojego konta.
    - **Informacje o koncie użytkownika administratora globalnego**: imię i nazwisko, nazwisko, nazwę użytkownika i hasło, które mają być używane dla nowego konta administratora globalnego.
1. Wybierz pozycję Utwórz, aby potwierdzić nowe informacje o domenie i koncie.
1. Zaloguj się przy użyciu nowej nazwy użytkownika i hasła administratora globalnego usługi Azure AD, aby rozpocząć [Dodawanie użytkowników i zarządzanie nimi](add-manage-users.md).

Aby uzyskać więcej informacji na temat tworzenia nowych dzierżawców w ramach Azure Portal, a nie portalu Centrum partnerskiego, zobacz artykuł [Tworzenie nowej dzierżawy w programie Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

## <a name="remove-a-tenant"></a>Usuwanie dzierżawy

Aby usunąć dzierżawcę z konta Centrum partnerskiego, Znajdź jego nazwę na stronie **dzierżawców** (w obszarze **Ustawienia konta**), a następnie wybierz pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie, że chcesz usunąć dzierżawcę. Po wykonaniu tej czynności żaden użytkownik w tej dzierżawie nie będzie mógł zalogować się do konta usługi Partner Center, a wszystkie uprawnienia skonfigurowane dla tych użytkowników zostaną usunięte.

> [!TIP]
> Dzierżawy nie można usunąć, jeśli użytkownik jest obecnie zalogowany do Centrum partnerskiego przy użyciu konta w tej samej dzierżawie. Aby usunąć dzierżawcę, musisz zalogować się do Centrum partnerskiego jako **Menedżera** dla innej dzierżawy skojarzonej z tym kontem. Jeśli istnieje tylko jedna dzierżawa skojarzona z kontem, dzierżawa może zostać usunięta tylko po zalogowaniu się przy użyciu konto Microsoft, który otworzył konto.
