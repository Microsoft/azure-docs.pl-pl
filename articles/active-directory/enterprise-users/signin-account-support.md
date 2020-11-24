---
title: Czy moja strona logowania do usługi Azure AD akceptuje konta Microsoft | Microsoft Docs
description: Jak komunikaty na ekranie odzwierciedlają wyszukiwanie nazw użytkowników podczas logowania
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f580e214017a0a599acd290f15f6be8e0a478d22
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95490752"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opcje logowania dla kont Microsoft w Azure Active Directory

Strona logowania Microsoft 365 dla Azure Active Directory (Azure AD) obsługuje konta służbowe i konta Microsoft, ale w zależności od sytuacji użytkownika może to być jeden lub drugi lub oba te elementy. Na przykład strona logowania usługi Azure AD obsługuje:

* Aplikacje akceptujące logowania z obu typów kont
* Organizacje akceptujące Gości

## <a name="identification"></a>Identyfikacja
Możesz określić, czy strona logowania używana przez organizację obsługuje konta Microsoft, przeglądając tekst wskazówki w polu Nazwa użytkownika. Jeśli tekst wskazówki brzmi "Poczta E-mail, telefon lub Skype", Strona logowania obsługuje konta Microsoft.

![Różnica między stronami logowania na koncie](./media/signin-account-support/ui-prompt.png)

[Dodatkowe opcje logowania działają tylko dla osobistych kont Microsoft](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , ale nie można ich używać do logowania się do zasobów konta służbowego.

## <a name="next-steps"></a>Następne kroki

[Dostosuj znakowanie logowania](../fundamentals/add-custom-domain.md)