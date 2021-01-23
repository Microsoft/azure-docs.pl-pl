---
title: Informacje o delegowaniu roli administratora — Azure Active Directory | Microsoft Docs
description: Modele delegowania, przykłady i zabezpieczenia ról w Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77ba93b9017fa75f7247bda639880415eda1d280
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740316"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegowanie administracji w Azure Active Directory

W przypadku wzrostu organizacji jest to złożoność. Jedną z typowych odpowiedzi jest zredukowanie niektórych obciążeń związanych z zarządzaniem dostępem przy użyciu ról administratorów Azure Active Directory (AD). Aby umożliwić użytkownikom dostęp do swoich aplikacji i wykonywanie ich zadań, można do nich przypisać najniższe możliwe uprawnienia. Nawet jeśli rola administratora globalnego nie zostanie przypisana do każdego właściciela aplikacji, należy umieścić obowiązki zarządzania aplikacjami dla istniejących administratorów globalnych. Istnieje wiele powodów, dla których organizacja przenosi się na bardziej scentralizowaną administrację. Ten artykuł ułatwia planowanie delegowania w organizacji.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Scentralizowane a delegowane uprawnienia

W miarę rozwoju organizacji może być trudne śledzenie użytkowników, którzy mają określone role administratora. Jeśli pracownik ma uprawnienia administratora, które nie powinny, organizacja może być bardziej podatna na naruszenia zabezpieczeń. Ogólnie rzecz biorąc, ilu administratorów jest obsługiwana i jak szczegółowe ich uprawnienia są zależne od wielkości i złożoności wdrożenia.

* W małych i nieprawidłowych wdrożeniach, jeden lub kilku administratorów wszystko wszystko; Brak delegowania. W takim przypadku należy utworzyć każdego administratora z rolą administratora globalnego.
* W dużych wdrożeniach z większą liczbą maszyn, aplikacji i pulpitów wymagana jest więcej delegowania. Kilku administratorów może mieć bardziej specyficzne obowiązki funkcjonalne (role). Mogą na przykład być to Administratorzy tożsamości uprzywilejowanych, a inni mogą być administratorami aplikacji. Ponadto administrator może zarządzać tylko określonymi grupami obiektów, takimi jak urządzenia.
* Nawet większe wdrożenia mogą wymagać jeszcze bardziej szczegółowych uprawnień, a także administratorów, którzy mają niekonwencjonalne lub hybrydowe role.

W portalu usługi Azure AD można [wyświetlić wszystkich członków dowolnej roli](manage-roles-portal.md), co może pomóc w szybkim sprawdzeniu uprawnień do wdrożenia i delegowania.

Jeśli interesuje Cię delegowanie dostępu do zasobów platformy Azure zamiast dostępu administracyjnego w usłudze Azure AD, zobacz [Przypisywanie roli platformy Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planowanie delegowania

Pracujemy nad opracowaniem modelu delegowania odpowiadającego Twoim potrzebom. Tworzenie modelu delegowania jest procesem iteracyjnym projektu i sugerujemy wykonanie następujących czynności:

* Definiowanie potrzebnych ról
* Delegowanie administrowania aplikacjami
* Przyznaj możliwość rejestrowania aplikacji
* Delegowanie własności aplikacji
* Opracowywanie planu zabezpieczeń
* Ustanów konta awaryjne
* Zabezpiecz role administratorów
* Tymczasowe podwyższenie poziomu uprawnień

## <a name="define-roles"></a>Definiowanie ról

Określ Active Directory zadania, które są wykonywane przez administratorów i jak są mapowane na role. [Szczegółowe opisy ról można wyświetlić](manage-roles-portal.md) w Azure Portal.

Każde zadanie powinno być oceniane pod kątem częstotliwości, ważności i trudności. Te kryteria są istotnymi aspektami definicji zadania, ponieważ określają, czy uprawnienie powinno być delegowane:

* Zadania, które wykonują rutynowo, mają ograniczone ryzyko i są proste do zapełnienia, są doskonałymi kandydatami do delegowania.
* Zadania, które można wykonać rzadko, ale mają doskonały wpływ na organizację i wymagają dużej ilości umiejętności, powinny być uważane za bardzo uważnie przed delegowaniem. Zamiast tego można [tymczasowo podnieść poziom konta do wymaganej roli](../privileged-identity-management/pim-configure.md) lub ponownie przypisać zadanie.

## <a name="delegate-app-administration"></a>Delegowanie administrowania aplikacjami

Rozprzestrzenianie się aplikacji w organizacji może ograniczyć model delegowania. Jeśli nawiąże to obciążenie związane z zarządzaniem dostępem do aplikacji w Administratorze globalnym, prawdopodobnie model zwiększy obciążenie w miarę przekroczenia czasu. W przypadku przyznania osobom roli administratora globalnego dla elementów, takich jak Konfigurowanie aplikacji dla przedsiębiorstw, można teraz odciążyć je do następujących ról o niższych uprawnieniach. Dzięki temu można ulepszyć stan zabezpieczeń i ograniczyć potencjalne błędy. Role administratora aplikacji z najwyższymi uprawnieniami są następujące:

* Rola **administrator aplikacji** , która umożliwia zarządzanie wszystkimi aplikacjami w katalogu, w tym rejestracjami, ustawieniami logowania jednokrotnego, przypisaniami użytkowników i grup oraz licencjonowaniem, ustawieniami serwera proxy aplikacji i wyrażanie zgody. Nie przyznaje możliwości zarządzania dostępem warunkowym.
* Rola **administrator aplikacji w chmurze** , która zapewnia wszystkie możliwości administratora aplikacji, z wyjątkiem tego, że nie udziela dostępu do ustawień serwera proxy aplikacji (ponieważ nie ma uprawnienia lokalnego).

## <a name="delegate-app-registration"></a>Delegowanie rejestracji aplikacji

Domyślnie wszyscy użytkownicy mogą tworzyć rejestracje aplikacji. Aby wybiórczo udzielić możliwości tworzenia rejestracji aplikacji:

* Ustaw **użytkownikom możliwość rejestrowania aplikacji** w **ustawieniach użytkownika**
* Przypisywanie użytkownika do roli Deweloper aplikacji

Aby wybiórczo udzielić zgody na zezwolenie aplikacji na dostęp do danych:

* Ustaw, aby **użytkownicy mogli wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu** do nie w **ustawieniach użytkownika**
* Przypisywanie użytkownika do roli Deweloper aplikacji

Gdy Deweloper aplikacji tworzy nową rejestrację aplikacji, jest automatycznie dodawana jako pierwszy właściciel.

## <a name="delegate-app-ownership"></a>Delegowanie własności aplikacji

Aby uzyskać bardziej szczegółowe delegowanie dostępu do aplikacji, można przypisać własność do poszczególnych aplikacji dla przedsiębiorstw. To uzupełnienie istniejącej obsługi przypisywania właścicieli rejestracji aplikacji. Własność jest przypisywana do poszczególnych aplikacji dla przedsiębiorstw w bloku aplikacje dla przedsiębiorstw. Korzyścią jest właściciele mogą zarządzać tylko tymi aplikacjami przedsiębiorstwa. Można na przykład przypisać właściciela do aplikacji Salesforce i ten właściciel może zarządzać dostępem do usługi Salesforce i konfiguracją, a nie innymi aplikacjami. Aplikacja dla przedsiębiorstw może mieć wielu właścicieli, a użytkownik może być właścicielem wielu aplikacji dla przedsiębiorstw. Istnieją dwie role właściciela aplikacji:

* Rola **właściciela aplikacji przedsiębiorstwa** umożliwia zarządzanie aplikacjami biznesowymi, do których należy użytkownik, w tym ustawieniami logowania jednokrotnego, przypisaniami użytkowników i grup oraz dodawaniem dodatkowych właścicieli. Nie przyznaje możliwości zarządzania ustawieniami serwera proxy aplikacji ani dostępem warunkowym.
* Rola **właściciela rejestracji aplikacji** umożliwia zarządzanie rejestracjami aplikacji dla aplikacji, do której należy użytkownik, w tym manifest aplikacji i dodanie dodatkowych właścicieli.

## <a name="develop-a-security-plan"></a>Opracowywanie planu zabezpieczeń

Usługa Azure AD zawiera obszerny przewodnik planowania i wykonywania planu zabezpieczeń w rolach administratora usługi Azure AD, [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i w chmurze](security-planning.md).

## <a name="establish-emergency-accounts"></a>Ustanów konta awaryjne

Aby zapewnić dostęp do magazynu zarządzania tożsamościami w przypadku wystąpienia problemu, przygotuj konta dostępu awaryjnego zgodnie z zasadami [tworzenia kont administracyjnych z dostępem awaryjnym](security-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Zabezpiecz role administratorów

Osoby atakujące, które uzyskują kontrolę nad kontami uprzywilejowanymi, mogą mieć ogromne szkody, więc najpierw należy chronić te konta przy użyciu [zasad dostępu do linii bazowej](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) dostępnych domyślnie dla wszystkich organizacji usługi Azure AD (w publicznej wersji zapoznawczej). Zasady wymuszają uwierzytelnianie wieloskładnikowe na uprzywilejowanych kontach usługi Azure AD. Następujące role usługi Azure AD są objęte zasadami odniesienia usługi Azure AD:

* Administrator globalny
* Administrator programu SharePoint
* Administrator programu Exchange
* Administrator dostępu warunkowego
* Administrator zabezpieczeń

## <a name="elevate-privilege-temporarily"></a>Tymczasowe podwyższenie poziomu uprawnień

W przypadku większości codziennych działań nie wszyscy użytkownicy muszą mieć prawa administratora globalnego, a nie wszystkie z nich powinny być trwale przypisane do roli administratora globalnego. Gdy użytkownicy potrzebują uprawnień administratora globalnego, powinni aktywować przypisanie roli w usłudze Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md) przy użyciu własnego konta lub alternatywnego konta administracyjnego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat opisu ról usługi Azure AD, zobacz [Przypisywanie ról administratora w usłudze Azure AD](permissions-reference.md)