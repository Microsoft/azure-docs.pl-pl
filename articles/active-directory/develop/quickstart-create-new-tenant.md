---
title: 'Szybki Start: Tworzenie dzierżawy Azure Active Directory'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak utworzyć dzierżawę Azure Active Directory do użycia podczas tworzenia aplikacji korzystających z platformy tożsamości firmy Microsoft na potrzeby uwierzytelniania i autoryzacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535165"
---
# <a name="quickstart-set-up-a-tenant"></a>Szybki start: Konfigurowanie dzierżawy

Aby tworzyć aplikacje korzystające z platformy tożsamości firmy Microsoft do zarządzania tożsamościami i dostępem, musisz mieć dostęp do *dzierżawy* usługi Azure Active Directory (Azure AD). Znajduje się w dzierżawie usługi Azure AD, w której rejestrujesz aplikacje i zarządzasz nimi, Konfiguruj ich dostęp do danych w Microsoft 365 i innych interfejsów API sieci Web, a następnie Włącz funkcje takie jak dostęp warunkowy.

Dzierżawa reprezentuje organizację. Jest to dedykowane wystąpienie usługi Azure AD, które deweloper organizacji lub aplikacji otrzymuje na początku relacji z firmą Microsoft. Ta relacja może się zacząć od utworzenia na przykład usługi Azure, Microsoft Intune lub Microsoft 365.

Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD. Ma własną reprezentację tożsamości służbowych, tożsamości odbiorców (jeśli jest to Azure AD B2C dzierżawa) i rejestracji aplikacji. Rejestracja aplikacji w ramach dzierżawy może zezwalać na uwierzytelnianie tylko z kont w ramach dzierżawy lub wszystkich dzierżawców.

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Określanie typu środowiska

Można utworzyć dwa typy środowisk. Środowisko zależy wyłącznie od typów użytkowników, które będą uwierzytelniane przez aplikację. 

Ten przewodnik Szybki Start dotyczy dwóch scenariuszy dla typu aplikacji, którą chcesz skompilować:

* Konta służbowe (Azure AD) lub konta Microsoft (takie jak Outlook.com i Live.com)
* Konta społecznościowe i lokalne (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Konta służbowe lub osobiste konta Microsoft

Aby skompilować środowisko dla kont służbowych lub osobistych kont Microsoft, możesz użyć istniejącej dzierżawy usługi Azure AD lub utworzyć nową.
### <a name="use-an-existing-azure-ad-tenant"></a>Używanie istniejącej dzierżawy usługi Azure AD

Wielu deweloperów ma już dzierżawców za pomocą usług lub subskrypcji, które są powiązane z dzierżawami usługi Azure AD, takimi jak Microsoft 365 lub subskrypcje platformy Azure.

Aby sprawdzić dzierżawcę:

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>. Użyj konta, którego będziesz używać do zarządzania aplikacją.
1. Zaznacz prawy górny róg. Jeśli masz dzierżawcę, nastąpi automatyczne zalogowanie. Nazwa dzierżawy zostanie wyświetlona bezpośrednio pod nazwą konta.
   * Umieść wskaźnik myszy nad nazwą konta, aby wyświetlić nazwę, adres e-mail, katalog lub identyfikator dzierżawy (GUID) i domenę.
   * Jeśli Twoje konto jest skojarzone z wieloma dzierżawami, możesz wybrać nazwę swojego konta, aby otworzyć menu, w którym można przełączać się między dzierżawami. Każda dzierżawa ma własny identyfikator dzierżawy.

> [!TIP]
> Aby znaleźć identyfikator dzierżawy, możesz:
> * Umieść wskaźnik myszy nad nazwą konta, aby uzyskać katalog lub identyfikator dzierżawy.
> * W Azure Portal Wyszukaj i wybierz **Azure Active Directory**  >  **Właściwości**  >  **Identyfikator dzierżawy** .

Jeśli nie masz skojarzonej dzierżawy z Twoim kontem, zobaczysz identyfikator GUID w polu Nazwa konta. Nie będzie można wykonywać akcji, takich jak rejestrowanie aplikacji, dopóki nie utworzysz dzierżawy usługi Azure AD.

### <a name="create-a-new-azure-ad-tenant"></a>Tworzenie nowej dzierżawy usługi Azure AD

Jeśli nie masz jeszcze dzierżawy usługi Azure AD lub chcesz utworzyć nową na potrzeby projektowania, zobacz [Tworzenie nowej dzierżawy w usłudze Azure AD](../fundamentals/active-directory-access-create-new-tenant.md). Lub Użyj [środowiska tworzenia katalogów](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) w Azure Portal. 

Aby utworzyć nową dzierżawę, podaj następujące informacje:

- **Nazwa organizacji**
- **Domena początkowa** — ta domena jest częścią *. onmicrosoft.com. Domenę można dostosować później.
- **Kraj lub region**

> [!NOTE]
> Podczas określania nazwy dzierżawy używaj znaków alfanumerycznych. Znaki specjalne nie są dozwolone. Długość nazwy nie może przekraczać 256 znaków.

## <a name="social-and-local-accounts"></a>Konta społecznościowe i lokalne

Aby rozpocząć tworzenie aplikacji, które logują się na kontach społecznościowych i lokalnych, Utwórz dzierżawę Azure AD B2C. Aby rozpocząć, zobacz [Tworzenie dzierżawy Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarejestruj aplikację](quickstart-register-app.md) , aby zintegrować ją z platformą tożsamości firmy Microsoft.
