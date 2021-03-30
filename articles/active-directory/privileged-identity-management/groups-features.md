---
title: Zarządzanie uprzywilejowanymi grupami usługi Azure AD w Privileged Identity Management (PIM) | Microsoft Docs
description: Jak zarządzać członkami i właścicielami uprzywilejowanych grup dostępu w Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512396"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Możliwości zarządzania uprzywilejowanym dostępem do grup usługi Azure AD (wersja zapoznawcza)

W Privileged Identity Management (PIM) można teraz przypisywać uprawnienia do członkostwa lub własności uprzywilejowanych grup dostępu. Począwszy od tej wersji zapoznawczej, można przypisać wbudowane role usługi Azure Active Directory (Azure AD) do grup chmur i używać usługi PIM do zarządzania członkiem grupy oraz uprawnieniem i aktywacją właściciela. Aby uzyskać więcej informacji na temat grup, które można przypisać do ról w usłudze Azure AD, zobacz temat [Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory (wersja zapoznawcza)](../roles/groups-concept.md).

>[!Important]
> Aby przypisać uprzywilejowaną grupę dostępu do roli w celu uzyskania dostępu administracyjnego do centrum programu Exchange, zabezpieczeń i zgodności lub programu SharePoint, należy użyć **ról i administratorów** portalu usługi Azure AD, a nie w środowisku uprzywilejowanych grup dostępu, aby użytkownik lub grupa uprawnieni do aktywacji do grupy.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Wymagaj różnych zasad dla każdej grupy, którą można przypisać

Niektóre organizacje wykorzystują takie narzędzia jak współpraca z usługą Azure AD Business-to-Business (B2B), aby zapraszać partnerów jako Gości do swojej organizacji usługi Azure AD. Zamiast pojedynczych zasad just-in-Time dla wszystkich przypisań do roli uprzywilejowanej, można utworzyć dwie różne uprzywilejowane grupy dostępu przy użyciu ich własnych zasad. Można wymusić mniej rygorystyczne wymagania dla zaufanych pracowników i bardziej rygorystyczne wymagania, takie jak przepływ pracy zatwierdzania dla partnerów, gdy żądają one aktywacji do przypisanej do nich grupy.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Aktywuj wiele przypisań ról w pojedynczym żądaniu

Korzystając z wersji zapoznawczej uprzywilejowanych grup dostępu, można zapewnić administratorom specyficznym dla obciążenia szybki dostęp do wielu ról z pojedynczym żądaniem just-in-Time. Na przykład Administratorzy pakietu Office w warstwie 3 mogą potrzebować dostępu just in Time do administratora programu Exchange, administratora aplikacji pakietu Office, administratora zespołów i ról administratora wyszukiwania, aby dokładnie zbadać zdarzenia codziennie. Przed dzisiejszym dniem będzie wymagane cztery kolejne żądania, które są procesem, który zajmuje trochę czasu. Zamiast tego można utworzyć grupę z możliwością przypisania o nazwie "Administratorzy pakietu Office w warstwie 3", przypisać ją do każdej z czterech ról wymienionych wcześniej (lub dowolnej wbudowane role usługi Azure AD) i włączyć ją w celu uzyskania dostępu uprzywilejowanego w sekcji działania grupy. Po włączeniu dostępu uprzywilejowanego można skonfigurować ustawienia just in Time dla elementów członkowskich grupy i przypisać administratorów i właścicieli jako kwalifikujące się. Gdy administratorzy przewyższają grupę, staną się członkami wszystkich czterech ról usługi Azure AD.

## <a name="extend-and-renew-group-assignments"></a>Przedłuż i Odnów przydziały grupy

Po skonfigurowaniu przypisanych w czasie przypisań właściciela lub członków, pierwsze pytanie, na które może się pojawić, ma wpływ na to, co się stanie w przypadku wygaśnięcia przypisania? W tej nowej wersji udostępniamy dwie opcje w tym scenariuszu:

- Rozszerzanie — gdy przypisanie roli zbliża się do wygaśnięcia, użytkownik może użyć Privileged Identity Management, aby zażądać rozszerzenia do przypisania roli
- Odnów — Jeśli przypisanie roli już wygasło, użytkownik może użyć Privileged Identity Management, aby zażądać odnowienia dla przypisania roli

Obie akcje inicjowane przez użytkownika wymagają zatwierdzenia od administratora globalnego lub administratora roli uprzywilejowanej. Administratorzy nie będą już musieli zarządzać tymi wygasami. Mogą oni po prostu oczekiwać na żądanie przedłużenia lub odnowienia i zatwierdzić je, jeśli żądanie jest prawidłowe.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie właściciela lub członka grupy uprzywilejowanego dostępu](groups-assign-member-owner.md)
- [Zatwierdzanie lub odrzucanie żądań aktywacji dla członków i właścicieli grupy uprzywilejowanego dostępu](groups-approval-workflow.md)
