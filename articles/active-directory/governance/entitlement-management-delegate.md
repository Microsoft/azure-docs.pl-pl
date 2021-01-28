---
title: Delegowanie i role w usłudze zarządzania uprawnieniami — Azure AD
description: Dowiedz się, jak delegować nadzór dostępu od administratorów IT do kierowników działu i menedżerów projektów, aby mogli zarządzać dostępem do siebie.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5812d4dbbd1a63b16d5bee77bb018ea18dd1d6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937681"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegowanie i role w zarządzaniu prawami usługi Azure AD

Domyślnie Administratorzy globalni i Administratorzy użytkowników mogą tworzyć wszystkie aspekty zarządzania prawami usługi Azure AD i zarządzać nimi. Jednak użytkownicy z tych ról mogą nie wiedzieć o wszystkich sytuacjach, w których wymagane są pakiety dostępu. Zwykle są to użytkownicy w ramach odpowiednich działów, zespołów lub projektów, które wiedzą, komu pracują z usługą, za pomocą których zasobów i jak długo. Zamiast udzielania nieograniczonych uprawnień administratorom niebędącym administratorami, można udzielić użytkownikom najniższych uprawnień, których potrzebują do wykonywania swoich zadań, i uniknąć tworzenia sprzecznych lub nieodpowiednich praw dostępu.

Ten film wideo zawiera omówienie sposobu delegowania dostępu do ładu od administratora IT do użytkowników, którzy nie są administratorami.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Przykład delegata

Aby zrozumieć, jak można delegować zarządzanie dostępem w usłudze zarządzania prawami, warto rozważyć przykład. Załóżmy, że organizacja ma następujących administratorów i menedżerów.

![Delegowanie od administratora IT do menedżerów](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Jako administrator IT, Hana ma kontakty w każdym dziale — Mamta w marketingu, Markuje Finanse i Jan, którzy są odpowiedzialni za zasoby tego działu i istotną zawartość biznesową.

Zarządzanie prawami umożliwia delegowanie praw dostępu do tych, które nie są administratorami, ponieważ są one osobami, które wiedzą, którzy użytkownicy potrzebują dostępu, na czas i do jakich zasobów. Zapewnia to właściwym osobom zarządzanie dostępem do swoich działów.

Oto jeden ze sposobów, w jaki Hana może delegować zarządzanie dostępem do działów marketingu, finansów i przepisów prawnych.

1. Hana tworzy nową grupę zabezpieczeń usługi Azure AD i dodaje Mamta, Mark i Jan jako członków grupy.

1. Hana dodaje tę grupę do roli twórcy katalogu.

    Mamta, Mark i Jan mogą teraz tworzyć wykazy dla swoich działów, dodawać zasoby, których działy potrzebują, i przeprowadzenie dalszych delegowania w wykazie.

    Należy zauważyć, że Mamta, Mark i Jan nie mogą zobaczyć katalogów innych.

1. Mamta tworzy katalog **marketingowy** , który jest kontenerem zasobów.

1. Mamta dodaje zasoby, których dział marketingu należy do tego katalogu.

1. Mamta może dodawać do tego wykazu inne osoby z działu jako właściciele katalogu. Pozwala to na udostępnianie obowiązków związanych z zarządzaniem katalogiem.

1. Mamta może dodatkowo delegować tworzenie pakietów dostępu do katalogu Marketing i zarządzanie nimi do menedżerów projektu w dziale marketingu. Można to zrobić, przypisując je do roli menedżera pakietów dostępu. Menedżer pakietów dostępu może tworzyć i zarządzać pakietami dostępu. 

Na poniższym diagramie przedstawiono wykazy z zasobami dla działu marketingu, finansów i przepisów prawnych. Korzystając z tych wykazów, menedżerowie projektu mogą tworzyć pakiety dostępu dla zespołów lub projektów.

![Przykład delegata zarządzania uprawnieniami](./media/entitlement-management-delegate/elm-delegate.png)

Po przeprowadzeniu delegowania dział marketingu może mieć role podobne do poniższej tabeli.

| Użytkownik | Rola zadania | Rola usługi Azure AD | Rola zarządzania uprawnieniami |
| --- | --- | --- | --- |
| Hana | Administrator IT | Administrator globalny lub administrator użytkowników |  |
| Mamta | Menedżer marketingu | Użytkownik | Twórca katalogu i właściciel katalogu |
| Bob | Marketing — potencjalny klient | Użytkownik | Właściciel katalogu |
| Jessica | Menedżer projektu marketingowego | Użytkownik | Menedżer pakietów dostępu |

## <a name="entitlement-management-roles"></a>Role zarządzania uprawnieniami

Zarządzanie prawami obejmuje następujące role, które są specyficzne dla zarządzania prawami.

| Rola zarządzania uprawnieniami | Opis |
| --- | --- |
| Twórca katalogu | Utwórz wykazy i zarządzaj nimi. Zazwyczaj administrator IT, który nie jest administratorem globalnym lub właścicielem zasobu dla kolekcji zasobów. Osoba tworząca katalog automatycznie stanie się pierwszym właścicielem katalogu i może dodać dodatkowych właścicieli katalogu. Autor wykazu nie może zarządzać katalogami, które nie są właścicielami i nie mogą dodawać do nich zasobów. Jeśli twórca katalogu musi zarządzać innym wykazem lub dodać do nich zasoby, które nie są właścicielami, mogą zażądać od współwłaściciela tego katalogu lub zasobu. |
| Właściciel katalogu | Edytuj istniejące wykazy i zarządzaj nimi. Zazwyczaj administrator IT lub właściciele zasobów lub użytkownik, który wyznaczył właściciela katalogu. |
| Menedżer pakietów dostępu | Edytuj wszystkie istniejące pakiety dostępu i zarządzaj nimi w ramach wykazu. |
| Menedżer przypisania pakietu dostępu | Edytuj wszystkie przypisania istniejących pakietów dostępu i zarządzaj nimi. |

Ponadto Wyznaczeni osoby zatwierdzające i osoba żądająca pakietu dostępu mają również prawa, chociaż nie są rolami.

| Prawe | Opis |
| --- | --- |
| Osoba zatwierdzająca | Autoryzowany przez zasady do zatwierdzania lub odrzucania żądań dostępu do pakietów, chociaż nie mogą zmieniać definicji pakietów dostępu. |
| Requestor | Autoryzowane przez zasady pakietu dostępu, aby zażądać tego pakietu dostępu. |

Poniższa tabela zawiera listę zadań, które mogą być wykonywane przez role zarządzania uprawnieniem.

| Zadanie | Administrator | Twórca katalogu | Właściciel katalogu | Menedżer pakietów dostępu | Menedżer przypisania pakietu dostępu |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Delegowanie do twórcy katalogu](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Dodawanie połączonej organizacji](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Utwórz nowy katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Dodawanie zasobu do wykazu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Dodawanie właściciela katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Edytowanie katalogu](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Usuwanie wykazu](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Deleguj do Menedżera pakietów dostępu](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Usuwanie Menedżera pakietów programu Access](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Utwórz nowy pakiet dostępu w wykazie](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Zmiana ról zasobów w pakiecie dostępu](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Tworzenie i edytowanie zasad](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Bezpośrednie przypisywanie użytkownika do pakietu dostępu](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Bezpośrednie Usuwanie użytkownika z pakietu dostępu](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Wyświetl osoby, które mają przypisanie do pakietu dostępu](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Wyświetlanie żądań pakietu dostępu](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Wyświetl błędy dostarczania żądania](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Przetwórz ponownie żądanie](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Anulowanie oczekującego żądania](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Ukryj pakiet dostępu](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Usuwanie pakietu dostępu](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Role wymagane do dodawania zasobów do wykazu

Administrator globalny może dodawać lub usuwać dowolną grupę (grupy zabezpieczeń utworzone w chmurze lub grupy Microsoft 365 utworzone w chmurze), aplikację lub witrynę usługi SharePoint Online w wykazie. Administrator użytkowników może dodać lub usunąć dowolną grupę lub aplikację w wykazie, z wyjątkiem grup skonfigurowanych jako możliwe do przypisania do roli katalogu. Należy pamiętać, że administrator użytkownika może zarządzać pakietami dostępu w wykazie zawierającym grupy skonfigurowane jako możliwe do przypisania do roli katalogu.

Użytkownik, który nie jest administratorem globalnym lub administratorem użytkownika, w celu dodania grup, aplikacji lub witryn usługi SharePoint Online do wykazu, musi mieć *zarówno* wymaganą rolę w katalogu usługi Azure AD, jak i rolę zarządzania uprawnieniem właściciela katalogu. W poniższej tabeli wymieniono kombinacje ról, które są wymagane do dodawania zasobów do wykazu. Aby usunąć zasoby z wykazu, musisz mieć te same role.

| Rola katalogu usługi Azure AD | Rola zarządzania uprawnieniami | Może dodać grupę zabezpieczeń | Może dodać grupę Microsoft 365 | Można dodać aplikację | Może dodać witrynę usługi SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Administrator globalny](../roles/permissions-reference.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Administrator użytkowników](../roles/permissions-reference.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Administrator usługi Intune](../roles/permissions-reference.md) | Właściciel katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Administrator programu Exchange](../roles/permissions-reference.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator usługi Teams](../roles/permissions-reference.md) | Właściciel katalogu |  | :heavy_check_mark: |  |  |
| [Administrator programu SharePoint](../roles/permissions-reference.md) | Właściciel katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Administrator aplikacji](../roles/permissions-reference.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| [Administrator aplikacji w chmurze](../roles/permissions-reference.md) | Właściciel katalogu |  |  | :heavy_check_mark: |  |
| Użytkownik | Właściciel katalogu | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel aplikacji |  |

> [!NOTE]
> Jeśli użytkownik doda grupę zabezpieczeń lub grupę Microsoft 365, Grupa nie może być przypisana do roli. Jeśli użytkownik doda grupę, która jest przypisana do roli podczas tworzenia pakietu dostępu, musi również być właścicielem tej grupy przypisanej do roli. Aby uzyskać więcej informacji, należy utworzyć grupę z możliwością [przypisywania ról w Azure Active Directory](../roles/groups-create-eligible.md).

Aby określić najmniejszą rolę uprzywilejowaną dla zadania, można także odwoływać się do [ról administratorów za pomocą zadania administratora w Azure Active Directory](../roles/delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Następne kroki

- [Delegowanie dostępu ładu do twórców wykazu](entitlement-management-delegate-catalog.md)
- [Tworzenie wykazu zasobów i zarządzanie nim](entitlement-management-catalog-create.md)
