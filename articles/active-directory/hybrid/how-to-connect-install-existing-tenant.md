---
title: 'Azure AD Connect: Jeśli masz już usługę Azure AD | Microsoft Docs'
description: W tym temacie opisano, jak używać usługi Connect, gdy masz istniejącą dzierżawę usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420533"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: Jeśli masz istniejącą dzierżawę
W większości tematów dotyczących używania Azure AD Connect założono, że zaczynasz od nowej dzierżawy usługi Azure AD i nie ma żadnych użytkowników ani innych obiektów. Ale jeśli rozpoczęłasz korzystanie z dzierżawy usługi Azure AD, wypełnisz ją użytkownikami i innymi obiektami, a teraz chcesz korzystać z programu Connect, ten temat jest dla Ciebie.

## <a name="the-basics"></a>Podstawy
Obiekt w usłudze Azure AD jest zarządzany w chmurze (Azure AD) lub lokalnie. Dla jednego pojedynczego obiektu nie można zarządzać niektórymi atrybutami lokalnymi i innymi atrybutami w usłudze Azure AD. Każdy obiekt ma flagę wskazującą, gdzie zarządza obiekt.

Niektórymi użytkownikami można zarządzać lokalnie i w chmurze. Typowym scenariuszem tej konfiguracji jest organizacja z mieszanymi pracownikami księgowymi i pracownikami ds. sprzedaży. Pracownicy ewidencjonowania aktywności mają lokalne konto usługi AD, ale pracownicy działu sprzedaży nie mają konta w usłudze Azure AD. Należy zarządzać użytkownikami lokalnymi i niektórymi w usłudze Azure AD.

Jeśli rozpoczęto zarządzanie użytkownikami w usłudze Azure AD, które znajdują się również w lokalnej usłudze AD, a później chcesz korzystać z programu Connect, należy wziąć pod uwagę pewne dodatkowe zagadnienia.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizuj z istniejącymi użytkownikami w usłudze Azure AD
Po zainstalowaniu Azure AD Connect i uruchomieniu synchronizacji usługa Azure AD Sync (w usłudze Azure AD) sprawdza każdy nowy obiekt i próbuje znaleźć istniejący obiekt do dopasowania. Dla tego procesu są używane trzy atrybuty: **userPrincipalName** , **proxyAddresses** i **sourceAnchor** / **immutableID**. Dopasowanie elementu **userPrincipalName** i **proxyAddresses** jest znane jako niezrównane **dopasowanie**. Dopasowanie **sourceAnchor** jest znane jako **twarde dopasowanie**. Dla atrybutu **proxyAddresses** należy użyć tylko wartości przy użyciu **protokołu SMTP:** , czyli podstawowego adresu e-mail.

Dopasowanie jest oceniane tylko dla nowych obiektów pochodzących z połączenia. Jeśli zmienisz istniejący obiekt tak, aby był dopasowany do któregokolwiek z tych atrybutów, zobaczysz błąd zamiast.

Jeśli usługa Azure AD odnajdzie obiekt, w którym wartości atrybutów są takie same dla obiektu pochodzącego z połączenia i że już istnieje w usłudze Azure AD, obiekt w usłudze Azure AD jest przełączany przez połączenie. Wcześniej obiekt zarządzany przez chmurę jest oflagowany jako zarządzany lokalnie. Wszystkie atrybuty w usłudze Azure AD z wartością w lokalnej usłudze AD są zastępowane wartością lokalną.

> [!WARNING]
> Ponieważ wszystkie atrybuty w usłudze Azure AD są zastępowane przez wartość lokalną, upewnij się, że masz dobre dane lokalnie. Jeśli na przykład masz tylko zarządzany adres e-mail w usłudze Microsoft 365 i nie będzie on aktualizowany w AD DS lokalnym, utracisz wszelkie wartości w usłudze Azure AD/Microsoft 365 nieobecny w AD DS.

> [!IMPORTANT]
> Jeśli używasz synchronizacji haseł, która jest zawsze używana przez ustawienia ekspresowe, hasło w usłudze Azure AD jest zastępowane hasłem w lokalnej usłudze AD. Jeśli użytkownicy są używani do zarządzania różnymi hasłami, należy poinformować ich o konieczności użycia hasła lokalnego po zainstalowaniu programu Connect.

Przed zaplanowaniem należy wziąć pod uwagę poprzednią sekcję i ostrzeżenie. Jeśli wprowadzono wiele zmian w usłudze Azure AD, które nie są odzwierciedlone w AD DS lokalnym, należy zaplanować zaplanowanie sposobu wypełniania AD DS wartościami zaktualizowanymi przed synchronizacją obiektów z Azure AD Connect.

Jeśli obiekty są dopasowane do nietrwałego dopasowania, to **sourceAnchor** jest dodawany do obiektu w usłudze Azure AD, co pozwala na późniejsze użycie.

>[!IMPORTANT]
> Firma Microsoft zdecydowanie zaleca się synchronizowanie kont lokalnych z istniejącymi kontami administracyjnymi w programie Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Twarde dopasowanie a miękkie — dopasowanie
W przypadku nowej instalacji programu Connect nie ma praktycznej różnicy między miękką i twardą różnicą. Różnica dotyczy sytuacji odzyskiwania po awarii. Jeśli serwer został utracony za pomocą Azure AD Connect, można ponownie zainstalować nowe wystąpienie bez utraty danych. Obiekt z sourceAnchor jest wysyłany do połączenia podczas początkowej instalacji. Dopasowanie może następnie zostać ocenione przez klienta (Azure AD Connect), który jest dużo szybszy niż ten sam w usłudze Azure AD. Twarde dopasowanie jest oceniane zarówno przez połączenie, jak i przez usługę Azure AD. Niezrównane dopasowanie jest oceniane tylko przez usługę Azure AD.

### <a name="other-objects-than-users"></a>Inne obiekty niż użytkownicy
W przypadku grup i kontaktów z włączoną obsługą poczty można uzyskać niezrównane dopasowanie na podstawie proxyAddresses. Twarde dopasowanie nie ma zastosowania, ponieważ tylko sourceAnchor/immutableID (przy użyciu programu PowerShell) tylko dla użytkowników. W przypadku grup, które nie obsługują poczty, obecnie nie jest obsługiwane dopasowanie miękkie ani twarde.

### <a name="admin-role-considerations"></a>Zagadnienia dotyczące roli administratora
Aby zapobiec dopasowywaniu niezaufanych użytkowników lokalnych do użytkowników w chmurze, którzy mają dowolną rolę administracyjną, Azure AD Connect nie będą pasować do lokalnych obiektów użytkownika z obiektami mającymi rolę administratora. Jest to domyślnie. Aby obejść ten problem, można wykonać następujące czynności:

1.  Usuń role katalogu z obiektu użytkownika tylko w chmurze.
2.  Jeśli wystąpiła próba nieudanej synchronizacji przez użytkownika, Usuń obiekt z kwarantanny w chmurze.
3.  Wyzwalanie synchronizacji.
4.  Opcjonalnie Dodaj role katalogu z powrotem do obiektu użytkownika w chmurze po wystąpieniu dopasowania.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Tworzenie nowego Active Directory lokalnego na podstawie danych w usłudze Azure AD
Niektórzy klienci rozpoczynają się od rozwiązania z usługą Azure AD tylko w chmurze i nie mają lokalnej usługi AD. Później chcą korzystać z zasobów lokalnych i utworzyć lokalną usługę AD na podstawie danych usługi Azure AD. Azure AD Connect nie może pomóc w tym scenariuszu. Nie powoduje ona tworzenia lokalnych użytkowników i nie ma możliwości ustawienia lokalnego hasła na takie same jak w usłudze Azure AD.

Jeśli tylko powód, dla którego planujesz dodać lokalne usługi AD, będzie obsługiwał LOB (aplikacje biznesowe), a następnie warto rozważyć użycie [usług domenowych Azure AD](../../active-directory-domain-services/index.yml) .

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
