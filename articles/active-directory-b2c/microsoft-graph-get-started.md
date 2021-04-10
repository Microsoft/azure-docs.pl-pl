---
title: Rejestrowanie aplikacji programu Microsoft Graph
titleSuffix: Azure AD B2C
description: Przygotuj się do zarządzania zasobami Azure AD B2C przy użyciu Microsoft Graph, rejestrując aplikację, której udzielono wymaganych uprawnień interfejs API programu Graph.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0dcb959184e12ffa22ae25443087684123598e47
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382469"
---
# <a name="register-a-microsoft-graph-application"></a>Rejestrowanie aplikacji programu Microsoft Graph

[Microsoft Graph][ms-graph] umożliwia zarządzanie wieloma zasobami w ramach dzierżawy Azure AD B2C, w tym kont użytkowników klienta i zasad niestandardowych. Pisząc skrypty lub aplikacje wywołujące [interfejs API Microsoft Graph][ms-graph-api], można zautomatyzować zadania zarządzania dzierżawcą, takie jak:

* Migrowanie istniejącego magazynu użytkownika do dzierżawy Azure AD B2C
* Wdrażanie zasad niestandardowych przy użyciu potoku platformy Azure w usłudze Azure DevOps i zarządzanie kluczami zasad niestandardowych
* Rejestracja użytkowników hosta na własnej stronie i tworzenie kont użytkowników w katalogu Azure AD B2C w tle
* Automatyzowanie rejestracji aplikacji
* Uzyskiwanie dzienników inspekcji

Poniższe sekcje ułatwiają przygotowanie się do korzystania z interfejsu API Microsoft Graph w celu zautomatyzowania zarządzania zasobami w katalogu Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Tryby interakcji interfejsu API Microsoft Graph

Istnieją dwa tryby komunikacji, których można używać podczas pracy z interfejsem API Microsoft Graph do zarządzania zasobami w dzierżawie Azure AD B2C:

* **Interaktywnie** — odpowiednie dla zadań wykonywanych jednorazowo należy użyć konta administratora w dzierżawie B2C do wykonywania zadań zarządzania. Ten tryb wymaga od administratora zalogowania się przy użyciu poświadczeń przed wywołaniem interfejsu API Microsoft Graph.

* **Zautomatyzowane** — w przypadku zaplanowanych lub ciągle wykonywanych zadań ta metoda korzysta z konta usługi skonfigurowanego z uprawnieniami wymaganymi do wykonywania zadań zarządzania. Tworzysz "konto usługi" w Azure AD B2C przez zarejestrowanie aplikacji, której aplikacje i skrypty używają do uwierzytelniania przy użyciu *identyfikatora aplikacji (klienta)* i przyznania **poświadczeń klienta OAuth 2,0** . W takim przypadku aplikacja działa jako sama do wywołania interfejsu API Microsoft Graph, a nie użytkownika administratora, tak jak w opisanej wcześniej metodzie interaktywnej.

Scenariusz interakcji **automatycznej** można włączyć przez utworzenie rejestracji aplikacji pokazanej w poniższych sekcjach.

Mimo że przesyłanie poświadczeń klienta OAuth 2,0 nie jest obecnie obsługiwane bezpośrednio przez usługę uwierzytelniania Azure AD B2C, można skonfigurować przepływ poświadczeń klienta przy użyciu usługi Azure AD i punktu końcowego Microsoft Identity platform/token dla aplikacji w dzierżawie Azure AD B2C. Dzierżawa Azure AD B2C udostępnia pewne funkcje z dzierżawami przedsiębiorstwa usługi Azure AD.

## <a name="register-management-application"></a>Zarejestruj aplikację zarządzania

Zanim skrypty i aplikacje będą mogły korzystać z [interfejsu API Microsoft Graph][ms-graph-api] w celu zarządzania zasobami Azure AD B2C, należy utworzyć rejestrację aplikacji w dzierżawie Azure AD B2C, która przyznaje wymagane uprawnienia interfejsu API.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *managementapp1*.
1. Wybierz **konta tylko w tym katalogu organizacji**.
1. W obszarze **uprawnienia** wyczyść pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.
1. Zanotuj **Identyfikator aplikacji (klienta)** , który pojawia się na stronie przeglądu aplikacji. Ta wartość jest używana w późniejszym kroku.

## <a name="grant-api-access"></a>Udzielanie dostępu do interfejsu API

Aby aplikacja mogła uzyskać dostęp do danych w Microsoft Graph, udziel zarejestrowanej aplikacji odpowiednich [uprawnień aplikacji](https://docs.microsoft.com/graph/permissions-reference). Czynne uprawnienia aplikacji to pełny poziom uprawnień implikowany przez uprawnienie. Aby na przykład *utworzyć*, *odczytywać*, *aktualizować* i *usuwać* każdego użytkownika w dzierżawie Azure AD B2C, należy dodać uprawnienie **User. ReadWrite. All** . 

> [!NOTE]
> Uprawnienie **User. ReadWrite. All** nie obejmuje możliwości aktualizowania haseł konta użytkownika. Jeśli aplikacja wymaga aktualizacji haseł kont użytkowników, [Udziel roli administratora użytkowników](#optional-grant-user-administrator-role). W przypadku przyznania roli [administratora użytkownika](../active-directory/roles/permissions-reference.md#user-administrator) **użytkownik. ReadWrite. All** nie jest wymagany. Rola administratora użytkownika obejmuje wszystko, czego potrzebujesz do zarządzania użytkownikami.

Można przyznać aplikacji wiele uprawnień aplikacji. Jeśli na przykład aplikacja musi zarządzać grupami w dzierżawie Azure AD B2C, należy również dodać uprawnienie **Grupa. ReadWrite. wszystkie** . 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>Obowiązkowe Udziel roli administratora użytkowników

Jeśli aplikacja lub skrypt musi zaktualizować hasła użytkowników, należy przypisać rolę *administratora użytkownika* do aplikacji. Rola [administratora użytkownika](../active-directory/roles/permissions-reference.md#user-administrator) ma stały zestaw uprawnień, które są przyznawane aplikacji. 

Aby dodać rolę *administrator użytkowników* , wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Użyj filtru **Directory + Subscription** , aby przełączyć się do dzierżawy Azure AD B2C.
1. Wyszukaj i wybierz **Azure AD B2C**.
1. W obszarze **Zarządzanie** wybierz pozycję **role i Administratorzy**.
1. Wybierz rolę **administratora użytkowników** . 
1. Wybierz pozycję **Dodaj przypisania**.
1. W polu tekstowym **Wybierz** wpisz nazwę lub identyfikator wcześniej zarejestrowanej aplikacji, na przykład *managementapp1*. Gdy pojawi się w wynikach wyszukiwania, wybierz aplikację.
1. Wybierz pozycję **Dodaj**. Pełne propagowanie uprawnień może potrwać kilka minut.

## <a name="create-client-secret"></a>Utwórz klucz tajny klienta

Aplikacja wymaga klucza tajnego klienta, aby potwierdzić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki:

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>Następne kroki

Teraz, po zarejestrowaniu aplikacji zarządzania i przyznaniu jej wymaganych uprawnień, Twoje aplikacje i usługi (na przykład Azure Pipelines) mogą używać swoich poświadczeń i uprawnień do współdziałania z interfejsem API Microsoft Graph. 

* [Uzyskiwanie tokenu dostępu z usługi Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Użyj tokenu dostępu do wywołania Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [B2C operacje obsługiwane przez Microsoft Graph](microsoft-graph-operations.md)
* [Zarządzanie kontami użytkowników Azure AD B2C przy użyciu Microsoft Graph](microsoft-graph-operations.md)
* [Pobieranie dzienników inspekcji za pomocą interfejsu API raportowania usługi Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
