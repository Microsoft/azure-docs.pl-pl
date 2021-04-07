---
title: serwer proxy aplikacji usługi Azure Active Directory i Tableau | Microsoft Docs
description: Dowiedz się, jak za pomocą serwera proxy aplikacji usługi Azure Active Directory (Azure AD) zapewnić dostęp zdalny do wdrożenia usługi Tableau.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258629"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>serwer proxy aplikacji usługi Azure Active Directory i Tableau 

Serwer proxy aplikacji usługi Azure Active Directory i Tableau mają pewność, że można łatwo korzystać z serwera proxy aplikacji w celu zapewnienia dostępu zdalnego do wdrożenia usługi Tableau. W tym artykule wyjaśniono, jak skonfigurować ten scenariusz.  

## <a name="prerequisites"></a>Wymagania wstępne 

W scenariuszu w tym artykule założono, że masz:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) skonfigurowany. 

- [Łącznik serwera proxy aplikacji](application-proxy-add-on-premises-application.md) został zainstalowany. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Włączanie serwera proxy aplikacji dla Tableau 

Serwer proxy aplikacji obsługuje przepływ przyznania protokołu OAuth 2,0, który jest wymagany do poprawnego działania Tableau. Oznacza to, że nie ma już żadnych specjalnych kroków wymaganych do włączenia tej aplikacji, poza jej skonfigurowaniem, wykonując poniższe kroki publikacji.


## <a name="publish-your-applications-in-azure"></a>Publikowanie aplikacji na platformie Azure 

Aby opublikować Tableau, należy opublikować aplikację w witrynie Azure Portal.

Dla:

- Szczegółowe instrukcje dotyczące kroków 1-8 można znaleźć w temacie [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). 
- Informacje o sposobach znajdowania wartości Tableau dla pól serwera proxy aplikacji można znaleźć w dokumentacji Tableau.  

**Aby opublikować aplikację**: 


1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator aplikacji. 

2. Wybierz pozycję **Azure Active Directory > aplikacje dla przedsiębiorstw**. 

3. Wybierz pozycję **Dodaj** w górnej części bloku. 

4. Wybierz **aplikację lokalną**. 

5. Wypełnij pola wymagane informacjami o nowej aplikacji. Skorzystaj z następujących wskazówek dotyczących ustawień: 

    - **Wewnętrzny adres URL**: Ta aplikacja powinna mieć wewnętrzny adres URL, który jest samym adresem URL Tableau. Na przykład `https://adventure-works.tableau.com`. 

    - **Metoda wstępnego uwierzytelniania**: Azure Active Directory (zalecane, ale nie wymagane). 

6. Wybierz pozycję **Dodaj** w górnej części bloku. Aplikacja zostanie dodana i zostanie otwarte menu szybkiego startu. 

7. W menu szybki Start wybierz pozycję **Przypisz użytkownika do testowania** i Dodaj co najmniej jednego użytkownika do aplikacji. Upewnij się, że to konto testowe ma dostęp do aplikacji lokalnej. 

8. Wybierz pozycję **Przypisz** , aby zapisać testowe przypisanie użytkownika. 

9. Obowiązkowe Na stronie Zarządzanie aplikacjami wybierz pozycję **Logowanie jednokrotne**. Wybierz pozycję **zintegrowane uwierzytelnianie systemu Windows** z menu rozwijanego i wypełnij pola wymagane zgodnie z konfiguracją Tableau. Wybierz pozycję **Zapisz**. 

 

## <a name="testing"></a>Testowanie 

Aplikacja jest teraz gotowa do przetestowania. Uzyskaj dostęp do zewnętrznego adresu URL użytego do opublikowania usługi Tableau oraz Zaloguj się jako użytkownik przypisany do obu aplikacji.



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure serwer proxy aplikacji usługi Azure AD, zobacz [jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).

