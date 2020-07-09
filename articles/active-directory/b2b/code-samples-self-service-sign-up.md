---
title: Przykłady kodu łącznika interfejsu API dla przepływów użytkownika — Azure AD
description: Przykłady kodu dla łączników interfejsu API w ramach samoobsługowego przepływu rejestracji dla Azure Active Directory tożsamości zewnętrznych.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0814312bb12582dd9e9ebfafc60fba470f6a9a9
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905161"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Przykłady dotyczące rejestracji w ramach samoobsługowego rejestrowania tożsamości zewnętrznych

W poniższych tabelach przedstawiono linki do przykładów kodu na potrzeby używania interfejsów API sieci Web w przepływach użytkownika samoobsługowego rejestrowania za pomocą [łączników interfejsu API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Łącznik interfejsu API — Przewodniki Szybki Start

| Przykład                                                                                                                          | Opis                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Ten przykład funkcji platformy .NET Core Azure demonstruje, jak ograniczyć logowanie do określonych domen dzierżaw i sprawdzić poprawność informacji podanych przez użytkownika. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Ten Node.js przykład funkcji platformy Azure pokazuje, jak ograniczyć logowanie do określonych domen dzierżaw i sprawdzić poprawność informacji podanych przez użytkownika.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Ten przykład funkcji platformy Azure w języku Python demonstruje, jak ograniczyć logowanie do określonych domen dzierżaw i sprawdzić poprawność informacji podanych przez użytkownika.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Niestandardowe przepływy pracy zatwierdzania

| Przykład | Opis |
|--------| ----------- |
| [Przepływ pracy zatwierdzania ręcznego](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Ten przykład pokazuje kompleksowy przepływ pracy zatwierdzania do zarządzania tworzeniem konta użytkownika-gościa w ramach rejestracji samoobsługowej |

## <a name="identity-verification"></a>Weryfikacja tożsamości

| Przykład                                                                                                            | Opis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Ten przykład pokazuje, jak zweryfikować tożsamość użytkownika w ramach rejestracji samoobsługowej przy użyciu łącznika interfejsu API w celu zintegrowania z usługą IDology. |

<!-- | [Experian](https://github.com/Azure-Samples/) | This sample shows how add identity verification to your self-service sign-up user flow by using an API connector to integrate with Experian. | -->
