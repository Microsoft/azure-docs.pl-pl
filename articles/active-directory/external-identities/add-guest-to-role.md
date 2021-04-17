---
title: Dodawanie użytkownika współpracy B2B do roli — Azure Active Directory
description: Dodawanie użytkownika-gościa do roli w Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575670"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Udzielanie uprawnień użytkownikom z organizacji partnerskich w dzierżawie Azure Active Directory dzierżawy

Azure Active Directory współpracy B2B (Azure AD) są dodawana jako użytkownicy-goście do katalogu, a uprawnienia gościa w katalogu są domyślnie ograniczone. Firma może wymagać od niektórych użytkowników-gości wypełniania ról o wyższych uprawnieniach w organizacji. Aby obsługiwać definiowanie ról o wyższych uprawnieniach, użytkowników-gości można dodawać do dowolnych ról w zależności od potrzeb organizacji.

Jeśli rola katalogu jest przypisana do użytkownika-gościa, zostanie mu przyznane dodatkowe uprawnienia, które są wraz z rolą, w tym podstawowe uprawnienia do odczytu. Zobacz [Wbudowane role usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

## <a name="default-role"></a>Rola domyślna

![Zrzut ekranu przedstawiający domyślną rolę katalogu](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Rola administratora globalnego

![Zrzut ekranu przedstawiający rolę administratora globalnego](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Ograniczona rola administratora

![Zrzut ekranu przedstawiający ograniczoną rolę administratora](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)
