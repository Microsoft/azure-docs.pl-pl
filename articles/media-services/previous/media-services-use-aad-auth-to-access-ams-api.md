---
title: Dostęp do interfejsu API Azure Media Services przy użyciu uwierzytelniania Azure Active Directory | Microsoft Docs
description: Informacje o pojęciach i krokach, które należy wykonać w celu uwierzytelniania dostępu do interfejsu API Azure Media Services za pomocą usługi Azure Active Directory (Azure AD).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3688f0f9266844ce584f3f2d2e1a7c39ed3e4f70
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009020"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure AD. 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Interfejs API Azure Media Services jest interfejsem API RESTful. Można jej używać do wykonywania operacji na zasobach multimedialnych przy użyciu interfejsu API REST lub z dostępnych zestawów SDK klienta. Azure Media Services oferuje zestaw SDK klienta Media Services dla Microsoft .NET. Aby uzyskać prawa dostępu do zasobów usługi Media Services i do interfejsu API usługi Media Services, należy się najpierw uwierzytelnić. 

Media Services obsługuje [uwierzytelnianie oparte na Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Usługa Azure Media REST wymaga, aby użytkownik lub aplikacja, która wykonuje żądania interfejsu API REST, miała rolę **współautor** lub **właściciela** , aby uzyskać dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md).  

Ten dokument zawiera omówienie sposobu uzyskiwania dostępu do interfejsu API Media Services przy użyciu interfejsów API REST lub .NET.

> [!NOTE]
> Autoryzacja Access Control była przestarzała 1 czerwca 2018.

## <a name="access-control"></a>Kontrola dostępu

Aby żądanie REST usługi Azure Media zakończyło się pomyślnie, użytkownik wywołujący musi mieć rolę współautora lub właściciela dla konta Media Services, do którego próbuje uzyskać dostęp.  
Tylko użytkownik mający rolę właściciela może udzielić dostępu do nowych użytkowników lub aplikacji na potrzeby zasobów multimedialnych. Rola współautor może uzyskać dostęp tylko do zasobu multimedialnego.
Nieautoryzowane żądania kończą się niepowodzeniem z kodem stanu 401. Jeśli widzisz ten kod błędu, sprawdź, czy użytkownik ma przypisaną rolę współautora lub właściciela dla konta Media Services użytkownika. Możesz to sprawdzić w Azure Portal. Wyszukaj swoje konto multimediów, a następnie kliknij kartę **Kontrola dostępu** . 

![Karta kontroli dostępu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy uwierzytelniania 
 
W przypadku korzystania z uwierzytelniania usługi Azure AD z usługą Azure Media Services dostępne są dwie opcje uwierzytelniania:

- **Uwierzytelnianie użytkownika**. Uwierzytelnij osobę, która korzysta z aplikacji, aby korzystać z zasobów Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o podanie poświadczeń użytkownika. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 
- **Uwierzytelnianie nazwy głównej usługi**. Uwierzytelnianie usługi. Aplikacje, które często korzystają z tej metody uwierzytelniania, to aplikacje uruchamiające usługi demonów, usługi warstwy środkowej lub zaplanowane zadania. Przykłady to aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejs API i mikrousługi.

### <a name="user-authentication"></a>Uwierzytelnianie użytkowników 

Aplikacje, które powinny używać metody uwierzytelniania użytkownika, to zarządzanie lub monitorowanie natywnych aplikacji: aplikacje mobilne, aplikacje systemu Windows i aplikacje konsolowe. Ten typ rozwiązania jest przydatny, jeśli chcesz, aby człowiek korzystał z usługi w jednym z następujących scenariuszy:

- Pulpit nawigacyjny monitorowania zadań kodowania.
- Pulpit nawigacyjny monitorowania strumieni na żywo.
- Aplikacja do zarządzania dla komputerów stacjonarnych i mobilnych do administrowania zasobami na koncie Media Services.

> [!NOTE]
> Tej metody uwierzytelniania nie należy używać w przypadku aplikacji przeznaczonych dla klientów. 

Aplikacja natywna musi najpierw uzyskać token dostępu z usługi Azure AD, a następnie użyć go podczas żądania HTTP do interfejsu API REST Media Services. Dodaj token dostępu do nagłówka żądania. 

Na poniższym diagramie przedstawiono typowy przepływ uwierzytelniania aplikacji interaktywnej: 

![Diagram natywnych aplikacji](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Na powyższym diagramie liczba reprezentuje przepływ żądań w kolejności chronologicznej.

> [!NOTE]
> W przypadku korzystania z metody uwierzytelniania użytkownika wszystkie aplikacje używają tego samego (domyślnego) macierzystego identyfikatora klienta aplikacji i identyfikatora URI przekierowania aplikacji natywnych. 

1. Monituj użytkownika o poświadczenia.
2. Zażądaj tokenu dostępu usługi Azure AD z następującymi parametrami:  

   * Punkt końcowy dzierżawy usługi Azure AD.

       Informacje o dzierżawie można pobrać z Azure Portal. Umieść kursor nad nazwą zalogowanego użytkownika w prawym górnym rogu.
   * Media Services identyfikator URI zasobu. 

       Ten identyfikator URI jest taki sam dla kont Media Services, które znajdują się w tym samym środowisku platformy Azure (na przykład https: \/ /rest.Media.Azure.NET).

   * Identyfikator klienta aplikacji Media Services (natywny).
   * Media Services (natywny) identyfikator URI przekierowania aplikacji.
   * Identyfikator URI zasobu dla Media Services REST.
        
       Identyfikator URI reprezentuje punkt końcowy interfejsu API REST (na przykład https://test03.restv2.westus.media.azure.net/api/) .

     Aby uzyskać wartości dla tych parametrów, zobacz [używanie Azure Portal do uzyskiwania dostępu do ustawień uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md) przy użyciu opcji uwierzytelnianie użytkownika.

3. Token dostępu usługi Azure AD jest wysyłany do klienta.
4. Klient wysyła żądanie do interfejsu API REST usługi Azure Media przy użyciu tokenu dostępu usługi Azure AD.
5. Klient pobiera dane z Media Services.

Aby uzyskać informacje dotyczące sposobu korzystania z uwierzytelniania usługi Azure AD w celu komunikowania się z żądaniami REST przy użyciu zestawu SDK klienta Media Services .NET, zobacz [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API Media Services przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz zestawu SDK klienta Media Services .NET, musisz ręcznie utworzyć żądanie tokenu dostępu usługi Azure AD przy użyciu parametrów opisanych w kroku 2. Aby uzyskać więcej informacji, zobacz [jak używać biblioteki uwierzytelniania usługi Azure AD w celu uzyskania tokenu usługi Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aplikacje, które często używają tej metody uwierzytelniania, to aplikacje, które uruchamiają usługi warstwy środkowej i zaplanowane zadania: aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejsy API i mikrousługi. Ta metoda uwierzytelniania jest również odpowiednia dla aplikacji interaktywnych, w których można używać konta usługi do zarządzania zasobami.

W przypadku korzystania z metody uwierzytelniania jednostki usługi w celu tworzenia scenariuszy klientów uwierzytelnianie zwykle jest obsługiwane w warstwie środkowej (za pomocą pewnego interfejsu API), a nie bezpośrednio w aplikacji mobilnej lub klasycznej. 

Aby użyć tej metody, Utwórz aplikację usługi Azure AD i nazwę główną usługi w swojej dzierżawie. Po utworzeniu aplikacji nadaj współautorowi aplikacji lub dostęp do roli właściciela do konta Media Services. Można to zrobić w Azure Portal przy użyciu interfejsu wiersza polecenia platformy Azure lub skryptu programu PowerShell. Możesz również użyć istniejącej aplikacji usługi Azure AD. [W Azure Portal](media-services-portal-get-started-with-aad.md)możesz zarejestrować swoją aplikację usługi Azure AD i jej nazwę główną. Można to zrobić również przy użyciu [interfejsu wiersza polecenia platformy Azure](media-services-use-aad-auth-to-access-ams-api.md) lub [programu PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplikacje warstwy środkowej](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po utworzeniu aplikacji usługi Azure AD uzyskasz wartości dla następujących ustawień. Te wartości są wymagane do uwierzytelniania:

- Identyfikator klienta 
- Klucz tajny klienta 

Na powyższym rysunku liczba reprezentuje przepływ żądań w kolejności chronologicznej:
    
1. Aplikacja warstwy środkowej (interfejs API sieci Web lub aplikacja sieci Web) żąda tokenu dostępu usługi Azure AD, który ma następujące parametry:  

   * Punkt końcowy dzierżawy usługi Azure AD.

       Informacje o dzierżawie można pobrać z Azure Portal. Umieść kursor nad nazwą zalogowanego użytkownika w prawym górnym rogu.
   * Media Services identyfikator URI zasobu. 

       Ten identyfikator URI jest taki sam dla kont Media Services, które znajdują się w tym samym środowisku platformy Azure (na przykład https: \/ /rest.Media.Azure.NET).

   * Identyfikator URI zasobu dla Media Services REST.

       Identyfikator URI reprezentuje punkt końcowy interfejsu API REST (na przykład https://test03.restv2.westus.media.azure.net/api/) .

   * Wartości aplikacji usługi Azure AD: identyfikator klienta i klucz tajny klienta.
    
     Aby uzyskać wartości dla tych parametrów, zobacz [używanie Azure Portal do uzyskiwania dostępu do ustawień uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md) przy użyciu opcji uwierzytelniania głównego usługi.

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwa środkowa wysyła żądanie do interfejsu API REST usługi Azure Media przy użyciu tokenu usługi Azure AD.
5. Warstwa środkowa pobiera dane z Media Services.

Aby uzyskać więcej informacji na temat sposobu korzystania z uwierzytelniania usługi Azure AD w celu komunikowania się z żądaniami REST przy użyciu zestawu SDK klienta Media Services .NET, zobacz [używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API Azure Media Services przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz zestawu SDK klienta Media Services .NET, musisz ręcznie utworzyć żądanie tokenu usługi Azure AD przy użyciu parametrów opisanych w kroku 1. Aby uzyskać więcej informacji, zobacz [jak używać biblioteki uwierzytelniania usługi Azure AD w celu uzyskania tokenu usługi Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Wyjątek: "serwer zdalny zwrócił błąd: (401) Brak autoryzacji".

Rozwiązanie: aby żądanie REST Media Services powiodło się, użytkownik wywołujący musi być rolą współautor lub właściciela na koncie Media Services, do którego próbuje uzyskać dostęp. Aby uzyskać więcej informacji, zobacz sekcję [Kontrola dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control) .

## <a name="resources"></a>Zasoby

Poniższe artykuły zawierają omówienia pojęć związanych z uwierzytelnianiem w usłudze Azure AD: 

- [Scenariusze uwierzytelniania rozwiązywane przez usługę Azure AD](../../active-directory/develop/authentication-vs-authorization.md)
- [Dodawanie, aktualizowanie lub usuwanie aplikacji w usłudze Azure AD](../../active-directory/develop/quickstart-register-app.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Następne kroki

* Użyj Azure Portal, aby [uzyskać dostęp do uwierzytelniania usługi Azure AD w celu korzystania z interfejsu API Azure Media Services](media-services-portal-get-started-with-aad.md).
* Użyj uwierzytelniania usługi Azure AD, aby [uzyskać dostęp do interfejsu API Azure Media Services przy użyciu platformy .NET](media-services-dotnet-get-started-with-aad.md).
