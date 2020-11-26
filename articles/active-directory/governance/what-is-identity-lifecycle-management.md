---
title: Co to jest zarządzanie cyklem życia tożsamości za pomocą Azure Active Directory? | Microsoft Docs
description: Opisuje Omówienie zarządzania cyklem życia tożsamości.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65e1217041d85b66664792d9475cdfcb517559b9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172439"
---
# <a name="what-is-identity-lifecycle-management"></a>Co to jest zarządzanie cyklem życia tożsamości?

Zarządzanie tożsamościami pomaga organizacjom w osiągnięciu równowagi między produktywnością — jak szybko można uzyskać dostęp do potrzebnych zasobów, takich jak dołączanie do mojej organizacji? I zabezpieczenia — jak należy zmienić ich dostęp z upływem czasu, na przykład ze względu na zmiany stanu zatrudnienia osoby?

**Zarządzanie cyklem życia tożsamości** jest podstawą do zarządzania tożsamościami, a skuteczne zarządzanie na dużą skalę wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji. Zarządzanie cyklem życia tożsamości pozwala zautomatyzować cały proces cyklu życia tożsamości cyfrowych i zarządzać nim. 

![Inicjowanie obsługi chmury](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>Co to jest tożsamość cyfrowa?

Tożsamość cyfrowa to informacje o jednostce używanej przez jeden lub więcej zasobów obliczeniowych, takich jak systemy operacyjne lub aplikacje. Te jednostki mogą reprezentować osoby, organizacje, aplikacje lub urządzenia.  Tożsamość jest zwykle opisywana przez skojarzone z nim atrybuty, takie jak nazwa, identyfikatory, a także właściwości, takie jak role używane do zarządzania dostępem.  Te atrybuty pomagają systemom wyznaczać, kto ma dostęp do tego, co i kto może korzystać z tego lub systemu.  

## <a name="managing-the-lifecycle-of-digital-identities"></a>Zarządzanie cyklem życia tożsamości cyfrowych

Zarządzanie tożsamościami cyfrowymi to złożone zadanie, szczególnie w odniesieniu do korelacji rzeczywistych obiektów, takich jak osoba i ich relacje z organizacją jako pracownik tej organizacji, z reprezentacją cyfrową.    W małych organizacjach, przechowywanie cyfrowych reprezentacji osób, które wymagają tożsamości, może być procesem ręcznym — w przypadku zatrudniania kogoś lub przybycia wykonawcy Specjalista IT może utworzyć konto dla nich w katalogu i przypisać im dostęp do nich.  Jednak w przypadku średniej wielkości i dużych organizacji Automatyzacja może umożliwić bardziej wydajne skalowanie i dokładne zachowanie tożsamości.

Typowym procesem ustanowienia zarządzania cyklem życia tożsamości w organizacji jest wykonanie następujących czynności:

1. Ustal, czy istnieją już systemy rekordów: źródła danych, które organizacja traktuje jako autorytatywne.  Na przykład organizacja może mieć systemowy dzień roboczy usługi HR i ten system jest autorytatywny do zapewnienia bieżącej listy pracowników i niektórych ich właściwości, takich jak nazwisko pracownika lub dział.  Lub system poczty e-mail, taki jak Exchange Online, może być autorytatywny dla adresu e-mail pracownika.

2. Połącz te systemy rekordów z co najmniej jednym katalogiem i bazami danych używanymi przez aplikacje i rozwiąż wszelkie niespójności między katalogami i systemami rekordów. Na przykład katalog może mieć przestarzałe dane, takie jak konto dla byłego pracownika, które nie jest już potrzebne. 

3. Określ, które procesy mogą służyć do dostarczania autorytatywnych informacji w przypadku braku systemu rekordów.  Na przykład jeśli istnieją tożsamości cyfrowe, ale dla osób odwiedzających, ale organizacja nie ma bazy danych dla odwiedzających, może być konieczne znalezienie alternatywnego sposobu ustalenia, kiedy tożsamość cyfrowa dla gościa nie jest już potrzebna.

4. Skonfiguruj zmiany z systemu rekordów lub innych procesów, które są replikowane do każdego z katalogów lub baz danych, które wymagają aktualizacji.

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>Zarządzanie cyklem życia tożsamości do reprezentowania pracowników i innych osób z relacją organizacyjną

W przypadku planowania zarządzania cyklem życia tożsamości dla pracowników lub innych osób z relacją organizacyjną, taką jak Zleceniobiorca lub student, wiele organizacji modeluje proces "przyłączania, przenoszenia i opuszczania".  Są to:
    
   - Dołączanie — gdy osoba w zakresie wymaganego dostępu jest wymagana przez te aplikacje, może być konieczne utworzenie nowej tożsamości cyfrowej, jeśli nie jest ona jeszcze dostępna.
   - Przenoszenie — gdy użytkownik przechodzi między granicami, które wymagają dodatkowych autoryzacji dostępu do dodania lub usunięcia do ich tożsamości cyfrowej
   - Pozostawienie — gdy osoba opuszcza zakres wymaganego dostępu, może być konieczne usunięcie dostępu, a następnie tożsamość może nie być dłużej wymagana przez aplikacje inne niż w celu przeprowadzenia inspekcji lub dowodowych

Jeśli na przykład nowi pracownik dołączy się do organizacji, który nigdy nie został powiązany z Twoją organizacją, ten pracownik będzie wymagał nowej tożsamości cyfrowej reprezentowanej jako konto użytkownika w usłudze Azure AD.  Tworzenie tego konta zostanie wykonane w procesie "łącznika", który może być zautomatyzowany, jeśli istnieje system rekordów, taki jak Workday, który może wskazywać, kiedy nowy pracownik zacznie działać.  Później, jeśli organizacja ma pracownika przechodzenie od sprzedaży do marketingu, będzie to proces "przenoszenia".  Wymagało to usunięcia praw dostępu, które miały w organizacji sprzedaży, które nie są już wymagane, i przyznania im praw w organizacji marketingowej, którą potrzebują.

## <a name="identity-lifecycle-management-for-guests"></a>Zarządzanie cyklem życia tożsamości dla Gości

Podobne procesy są również niezbędne dla Gości i innych użytkowników.  Zarządzanie prawami w usłudze Azure AD korzysta z usługi Azure AD Business-to-Business (B2B), aby zapewnić kontrolę cyklu życia potrzebną do współpracy z osobami spoza organizacji, które wymagają dostępu do zasobów organizacji. Przy użyciu usługi Azure AD B2B użytkownicy zewnętrzni są uwierzytelniani w katalogu macierzystym, ale mają reprezentację w katalogu. Reprezentacja w katalogu umożliwia użytkownikowi przypisanie dostępu do zasobów.  Zarządzanie prawami umożliwia osobom spoza organizacji żądanie dostępu, tworząc dla nich tożsamość cyfrową w razie potrzeby. Te tożsamości cyfrowe są automatycznie usuwane po utracie dostępu przez użytkownika.  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>Jak usługa Azure AD automatyzuje Zarządzanie cyklem życia tożsamości?

Usługa Azure AD udostępnia obecnie następujące funkcje:

* Użytkownicy reprezentujący pracowników mogą być automatycznie tworzeniu i aktualizować w usłudze Azure AD, a Active Directory przy użyciu [aprowizacji opartej](what-is-hr-driven-provisioning.md) na usłudze kadr
* Użytkownicy już obecni w Active Directory mogą być automatycznie tworzeniu i obsługiwani w usłudze Azure AD przy użyciu [aprowizacji między katalogami](what-is-inter-directory-provisioning.md)
* Użytkownicy mogą być automatycznie przypisani do grup na podstawie ich właściwości, przy użyciu [grup dynamicznych](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups) , a na żądanie są przypisywane do grup, zespołów, ról usługi Azure AD, ról zasobów platformy Azure i witryn usługi SharePoint Online, przy użyciu [zarządzania uprawnieniami](entitlement-management-scenarios.md) i [Privileged Identity Management](../privileged-identity-management/pim-configure.md)
* Aktualizacje użytkowników mogą być automatycznie wysyłane do większej liczby aplikacji przy użyciu [aprowizacji aplikacji](what-is-app-provisioning.md)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Zarządzanie dostępem użytkowników zewnętrznych w usłudze Azure AD upoważnienia](./entitlement-management-external-users.md)
- [Co to jest obsługa administracyjna oparta na HR?](what-is-hr-driven-provisioning.md)
- [Co to jest aprowizacja aplikacji?](what-is-app-provisioning.md)
- [Co to jest aprowizacja między katalogami?](what-is-inter-directory-provisioning.md)