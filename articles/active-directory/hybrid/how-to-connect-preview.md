---
title: 'Azure AD Connect: funkcje w wersji zapoznawczej | Microsoft Docs'
description: W tym temacie opisano bardziej szczegółowe funkcje, które są w wersji zapoznawczej w Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79261283"
---
# <a name="more-details-about-features-in-preview"></a>Więcej szczegółów na temat funkcji w wersji zapoznawczej
W tym temacie opisano, jak używać funkcji obecnie dostępnych w wersji zapoznawczej.

## <a name="group-writeback"></a>Zapisywanie zwrotne grup
Opcja stornowania grupowego w funkcjach opcjonalnych umożliwia Stornowanie **grup pakietu Office 365** do lasu z zainstalowanym programem Exchange. Jest to grupa, która jest zawsze zarządzana w chmurze. Jeśli używasz lokalnego programu Exchange, możesz zapisać te grupy w środowisku lokalnym, aby użytkownicy korzystający z lokalnej skrzynki pocztowej programu Exchange mogli wysyłać i odbierać wiadomości e-mail z tych grup.

Więcej informacji o grupach pakietu Office 365 i sposobach ich użycia można znaleźć [tutaj](https://aka.ms/O365g).

Grupa Office 365 jest reprezentowana jako grupa dystrybucyjna w AD DS lokalnym. Lokalny serwer Exchange musi znajdować się w programie Exchange 2013 zbiorczej aktualizacji 8 (wydanej w marcu 2015) lub w programie Exchange 2016 w celu rozpoznania tego typu nowej grupy.

**Uwagi w wersji zapoznawczej**

* Atrybut książka adresowa nie jest obecnie wypełniany w wersji zapoznawczej. Bez tego atrybutu Grupa nie jest widoczna w liście. Najprostszym sposobem wypełnienia tego atrybutu jest użycie polecenia cmdlet `update-recipient`programu Exchange PowerShell.
* Tylko lasy ze schematem programu Exchange są prawidłowymi obiektami docelowymi dla grup. Jeśli nie wykryto żadnego programu Exchange, nie można włączyć funkcji zapisywania zwrotnego grup.
* Obecnie są obsługiwane tylko wdrożenia organizacji programu Exchange z jednym lasem. Jeśli masz więcej niż jedną lokalną organizację programu Exchange, musisz mieć lokalne rozwiązanie GALSync dla tych grup, które mają być wyświetlane w innych lasach.
* Funkcja zapisywania zwrotnego grup nie obsługuje grup zabezpieczeń ani grup dystrybucyjnych.

> [!NOTE]
> Do zapisywania zwrotnego grup jest wymagana subskrypcja Azure AD — wersja Premium.
> 
>

## <a name="user-writeback"></a>Zapisywanie zwrotne użytkownika
> [!IMPORTANT]
> Funkcja wersji zapoznawczej zapisywania zwrotnego użytkownika została usunięta z aktualizacji 2015 sierpnia do Azure AD Connect. Jeśli ją włączono, należy wyłączyć tę funkcję.
>
>

## <a name="next-steps"></a>Następne kroki
Kontynuuj [instalację Niestandardową Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
