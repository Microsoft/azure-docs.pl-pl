---
title: Pojedyncze i wielodostępne aplikacje w usłudze Azure AD
titleSuffix: Microsoft identity platform
description: Poznaj funkcje i różnice między aplikacjami z jedną dzierżawą i wieloma dzierżawcami w usłudze Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2020
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 0868d87d977b15a552b04d5dbd6d19de6931f0ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91395910"
---
# <a name="tenancy-in-azure-active-directory"></a>Dzierżawa w usłudze Azure Active Directory

Azure Active Directory (Azure AD) organizuje obiekty, takie jak użytkownicy i aplikacje, do grup nazywanych *dzierżawcami*. Dzierżawy umożliwiają administratorowi Ustawianie zasad dla użytkowników w organizacji oraz aplikacji, do których należy organizacja, aby spełniały ich zasady zabezpieczeń i działania. 

## <a name="who-can-sign-in-to-your-app"></a>Kto może zalogować się do swojej aplikacji?

Gdy chodzi o opracowywanie aplikacji, deweloperzy mogą zdecydować się na skonfigurowanie aplikacji jako pojedynczej dzierżawy lub wielu dzierżawców podczas rejestracji aplikacji w [Azure Portal](https://portal.azure.com).
* Aplikacje z jedną dzierżawą są dostępne tylko w dzierżawie, w której zostały zarejestrowane, nazywane również dzierżawą domową.
* Aplikacje z wieloma dzierżawcami są dostępne dla użytkowników zarówno w ramach dzierżawy głównej, jak i innych dzierżawców.

W Azure Portal można skonfigurować aplikację jako pojedynczą dzierżawę lub wiele dzierżawców, ustawiając odbiorców w następujący sposób.

| Grupy odbiorców | Jeden/wiele dzierżawców | Kto może się zalogować | 
|----------|--------| ---------|
| Tylko konta w tym katalogu | Pojedyncza dzierżawa | Wszystkie konta użytkowników i Gości w katalogu mogą korzystać z aplikacji lub interfejsu API.<br>*Użyj tej opcji, jeśli docelowi odbiorcy są wewnętrzną organizacją.* |
| Konta w dowolnym katalogu usługi Azure AD | Wiele dzierżaw | Wszyscy użytkownicy i Goście z kontem służbowym firmy Microsoft mogą korzystać z aplikacji lub interfejsu API. Obejmuje to szkoły i firmy używające Microsoft 365.<br>*Użyj tej opcji, jeśli docelowi odbiorcy są klientami biznesowymi lub edukacyjnymi.* |
| Konta w dowolnym katalogu usługi Azure AD i osobiste konta Microsoft (takie jak Skype, Xbox, Outlook.com) | Wiele dzierżaw | Wszyscy użytkownicy z firmowymi lub szkolnymi konto Microsoft mogą korzystać z aplikacji lub interfejsu API. Obejmuje ona szkoły i firmy, które używają Microsoft 365, a także kont osobistych, które są używane do logowania się do usług, takich jak Xbox i Skype.<br>*Użyj tej opcji, aby wybrać szerszy zbiór kont Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Najlepsze rozwiązania dotyczące aplikacji z wieloma dzierżawcami

Tworzenie doskonałych aplikacji z wieloma dzierżawcami może być trudne z powodu liczby różnych zasad, które Administratorzy IT mogą ustawiać w swoich dzierżawcach. Jeśli zdecydujesz się na utworzenie aplikacji z wieloma dzierżawcami, postępuj zgodnie z następującymi najlepszymi rozwiązaniami:

* Przetestuj aplikację w dzierżawie, która ma skonfigurowane [zasady dostępu warunkowego](../azuread-dev/conditional-access-dev-guide.md).
* Postępuj zgodnie z zasadą najmniejszego dostępu użytkownika, aby upewnić się, że aplikacja żąda tylko wymaganych uprawnień. 
* Podaj odpowiednie nazwy i opisy wszelkich uprawnień, które ujawniasz w ramach aplikacji. Dzięki temu użytkownicy i Administratorzy wiedzą, co zgadza się z użyciem interfejsów API aplikacji. Aby uzyskać więcej informacji, zobacz sekcję najlepsze rozwiązania w [podręczniku uprawnień](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Następne kroki

* [Jak przekonwertować aplikację na wiele dzierżawców](howto-convert-app-to-be-multi-tenant.md)
