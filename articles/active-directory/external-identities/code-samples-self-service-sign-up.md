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
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87905946"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Przykłady dotyczące rejestracji w ramach samoobsługowego rejestrowania tożsamości zewnętrznych

W poniższych tabelach przedstawiono linki do przykładów kodu na potrzeby używania interfejsów API sieci Web w przepływach użytkownika samoobsługowego rejestrowania za pomocą [łączników interfejsu API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Łącznik interfejsu API — Przewodniki Szybki Start

| Sample                                                                                                                          | Opis                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Ten przykład funkcji platformy .NET Core Azure demonstruje, jak ograniczyć logowanie do określonych domen dzierżaw i sprawdzić poprawność informacji podanych przez użytkownika. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Ten Node.js przykład funkcji platformy Azure pokazuje, jak ograniczyć logowanie do określonych domen dzierżaw i sprawdzić poprawność informacji podanych przez użytkownika.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Ten przykład funkcji platformy Azure w języku Python demonstruje, jak ograniczyć logowanie do określonych domen dzierżaw i sprawdzić poprawność informacji podanych przez użytkownika.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Niestandardowe przepływy pracy zatwierdzania

| Sample | Opis |
|--------| ----------- |
| [Przepływ pracy zatwierdzania ręcznego](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Ten przykład pokazuje kompleksowy przepływ pracy zatwierdzania do zarządzania tworzeniem konta użytkownika-gościa w ramach rejestracji samoobsługowej |

## <a name="identity-verification"></a>Weryfikacja tożsamości

| Sample                                                                                                            | Opis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Ten przykład pokazuje, jak zweryfikować tożsamość użytkownika w ramach rejestracji samoobsługowej przy użyciu łącznika interfejsu API w celu zintegrowania z usługą IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Ten przykład pokazuje, jak zweryfikować tożsamość użytkownika w ramach rejestracji samoobsługowej przy użyciu łącznika interfejsu API w celu zintegrowania z usługą Experian. |
