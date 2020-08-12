---
title: 'Szybki Start: rejestrowanie aplikacji przy użyciu platformy tożsamości firmy Microsoft | Azure'
description: W tym przewodniku szybki start dowiesz się, jak dodać i zarejestrować aplikację za pomocą platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115360"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft

W tym przewodniku szybki start zarejestrowano aplikację przy użyciu środowiska **rejestracje aplikacji** w Azure Portal. 

Aplikacja jest zintegrowana z platformą tożsamości firmy Microsoft przez zarejestrowanie jej w dzierżawie Azure Active Directory. Deweloperzy w przedsiębiorstwach i dostawcy oprogramowania jako usługi (SaaS) mogą opracowywać komercyjne usługi w chmurze lub aplikacje biznesowe, które można zintegrować z platformą tożsamości firmy Microsoft. Integracja zapewnia bezpieczne logowanie i autoryzację dla takich usług.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Dzierżawa usługi Azure AD](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Rejestrowanie nowej aplikacji w witrynie Azure Portal

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu. Skonfiguruj sesję portalu do dzierżawy usługi Azure AD, której chcesz użyć.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**. W obszarze **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. W obszarze **zarejestruj aplikację**wprowadź zrozumiałą nazwę aplikacji wyświetlaną dla użytkowników.
1. Określ, kto może korzystać z aplikacji w następujący sposób:

    | Obsługiwane typy kont | Opis |
    |-------------------------|-------------|
    | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli kompilujesz aplikację biznesową. Ta opcja jest niedostępna, jeśli aplikacja nie jest rejestrowana w katalogu.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację jednodostępną.<br><br>Ta opcja jest wartością domyślną, chyba że rejestrujesz aplikację poza katalogiem. W przypadkach, gdy aplikacja jest rejestrowana poza katalogiem, wartość domyślna to aplikacja wielodostępna usługi Azure AD i konta osobiste Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli aplikacja jest przeznaczona dla wszystkich klientów biznesowych i edukacyjnych.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację wielodostępną.<br><br>Jeśli aplikacja została zarejestrowana jako jedyna dzierżawa usługi Azure AD, możesz ją zaktualizować do usługi Azure AD z wieloma dzierżawcami i z powrotem do jednej dzierżawy za pomocą strony **uwierzytelniania** . |
    | **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft** | Wybierz tę opcję, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.<br><br>Ta opcja mapuje do usługi Azure AD aplikację wielodostępną i konta osobiste Microsoft.<br><br>Jeśli aplikacja została zarejestrowana jako usługa Azure AD z wieloma dzierżawcami i osobistymi kontami Microsoft, nie można zmienić tego ustawienia w interfejsie użytkownika. Zamiast tego musisz użyć edytora manifestu aplikacji, aby zmienić obsługiwane typy konta. |

1. W obszarze **URI przekierowania (opcjonalnie)** wybierz typ aplikacji, którą tworzysz: klient **internetowy** lub **publiczny (Mobile & Desktop)**. Następnie wprowadź identyfikator URI przekierowania lub adres URL odpowiedzi dla aplikacji.

    * W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg `https://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego.
    * W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Podaj wartość specyficzną dla Twojej aplikacji, np. `myapp://auth`.

    Przykłady aplikacji sieci Web lub aplikacji natywnych można znaleźć w sekcji Przewodniki Szybki Start na [platformie Microsoft Identity platform](./index.yml).

1. Po zakończeniu wybierz pozycję **Rejestruj**.

    ![Wyświetla ekran, aby zarejestrować nową aplikację w Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Usługa Azure AD przypisuje do aplikacji unikatową aplikację lub identyfikator klienta. W portalu zostanie otwarta strona **Przegląd** aplikacji. Aby dodać możliwości do aplikacji, możesz wybrać inne opcje konfiguracji, w tym znakowanie, certyfikaty i wpisy tajne, uprawnienia interfejsu API i inne.

![Przykład strony przeglądu nowo zarejestrowanej aplikacji](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać dostęp do interfejsów API sieci Web, zobacz [Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md)

* Aby dowiedzieć się więcej o uprawnieniach, zobacz [uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](v2-permissions-and-consent.md).

* Aby udostępnić interfejsy API sieci Web, zobacz [Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](quickstart-configure-app-expose-web-apis.md).

* Aby zarządzać obsługiwanymi kontami, zobacz [Szybki Start: modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md).

* Aby skompilować aplikację i dodać jej funkcje, zobacz Przewodniki Szybki Start na [platformie Microsoft Identity platform](./index.yml).

* Aby dowiedzieć się więcej na temat dwóch obiektów usługi Azure AD, które reprezentują zarejestrowaną aplikację i związek między nimi, zobacz [Application objects and service principal objects](app-objects-and-service-principals.md) (Obiekty aplikacji i obiekty jednostek usługi).

* Aby dowiedzieć się więcej na temat wytycznych dotyczących znakowania, których należy użyć podczas tworzenia aplikacji, zobacz [wskazówki dotyczące znakowania dla aplikacji](howto-add-branding-in-azure-ad-apps.md).