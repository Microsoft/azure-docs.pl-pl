---
title: Skonfiguruj konto i zaloguj się przy użyciu konta Microsoft
titleSuffix: Azure AD B2C
description: Podaj konto i zaloguj się do klientów przy użyciu kont Microsoft w swoich aplikacjach za pomocą Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388004"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą konto Microsoft przy użyciu Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konto Microsoft

Aby użyć konto Microsoft jako [dostawcy tożsamości](openid-connect.md) w Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie usługi Azure AD. Dzierżawa usługi Azure AD nie jest taka sama jak dzierżawa Azure AD B2C. Jeśli nie masz jeszcze konto Microsoft, możesz uzyskać jeden z nich [https://www.live.com/](https://www.live.com/) .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *MSAapp1*.
1. W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**.

   Aby uzyskać więcej informacji na temat różnych typów kont, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md).
1. W obszarze **URI przekierowania (opcjonalnie)** wybierz pozycję **Sieć Web** i wprowadź `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` wartość w polu tekstowym. Zamień `<tenant-name>` na nazwę dzierżawy Azure AD B2C.
1. Wybierz pozycję **zarejestruj**
1. Zapisz **Identyfikator aplikacji (klienta)** widoczny na stronie przeglądu aplikacji. Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.
1. Wybierz **certyfikaty & wpisy tajne**
1. Kliknij pozycję **Nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego, na przykład *hasło aplikacji 1*, a następnie kliknij przycisk **Dodaj**.
1. Zapisz hasło aplikacji wyświetlane w kolumnie **wartość** . Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurowanie konto Microsoft jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **konto Microsoft**.
1. Wprowadź **nazwę**. Na przykład *MSA*.
1. W polu **Identyfikator klienta**wprowadź identyfikator aplikacji (klienta) utworzonej wcześniej aplikacji usługi Azure AD.
1. W polu **klucz tajny klienta**Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.
