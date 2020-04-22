---
title: Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się więcej o pojęciach i krokach, które należy podjąć, aby uwierzytelnić dostęp do interfejsu API usługi Azure Media Services za pomocą usługi Azure Active Directory (Azure AD).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e1aeaf105ce371e965b433ac78e2b257f4bc18b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682046"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure AD.  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Interfejs API usługi Azure Media Services jest interfejsem API RESTful. Można go używać do wykonywania operacji na zasobach multimedialnych przy użyciu interfejsu API REST lub przy użyciu dostępnych sdk klienta. Usługa Azure Media Services oferuje zestaw SDK klienta usług Media Services dla platformy Microsoft .NET. Aby uzyskać autoryzację dostępu do zasobów usługi Media Services i interfejsu API usługi Media Services, należy najpierw zostać uwierzytelniony. 

Usługa Media Services obsługuje [uwierzytelnianie oparte na usłudze Azure Active Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Usługa Azure Media REST wymaga, aby użytkownik lub aplikacja, która sprawia, że żądania interfejsu API REST mają rolę **współautora** lub **właściciela,** aby uzyskać dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do kontroli dostępu opartej na rolach w witrynie Azure portal](../../role-based-access-control/overview.md).  

W tym dokumencie przedstawiono omówienie sposobu uzyskiwania dostępu do interfejsu API usługi Media Services przy użyciu interfejsów API REST lub .NET.

> [!NOTE]
> Autoryzacja kontroli dostępu została przestarzała 1 czerwca 2018 r.

## <a name="access-control"></a>Kontrola dostępu

Aby żądanie REST multimediów platformy Azure powiodło się, użytkownik wywołujący musi mieć rolę współautora lub właściciela dla konta usługi Media Services, do które próbuje uzyskać dostęp.  
Tylko użytkownik z rolą Właściciel może przyznać zasobowi multimedialnemu (kontu) dostęp do nowych użytkowników lub aplikacji. Rola Współautor może uzyskać dostęp tylko do zasobu multimedialnego.
Nieautoryzowane żądania nie powiodą się, z kodem stanu 401. Jeśli widzisz ten kod błędu, sprawdź, czy użytkownik ma przypisaną rolę Współautor lub Właściciel dla konta usługi Media Services użytkownika. Możesz to sprawdzić w witrynie Azure portal. Wyszukaj konto multimedialne, a następnie kliknij kartę **Kontrola dostępu.** 

![Karta Kontrola dostępu](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typy uwierzytelniania 
 
Podczas korzystania z uwierzytelniania usługi Azure AD w usłudze Azure Media Services dostępne są dwie opcje uwierzytelniania:

- **Uwierzytelnianie użytkownika**. Uwierzytelnij osobę, która korzysta z aplikacji do interakcji z zasobami usługi Media Services. Interaktywna aplikacja powinna najpierw monitować użytkownika o poświadczenia użytkownika. Przykładem jest aplikacja konsoli zarządzania używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 
- **Uwierzytelnianie jednostkowe usługi**. Uwierzytelnij usługę. Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które uruchamiają usługi demona, usługi warstwy środkowej lub zaplanowane zadania. Przykładami są aplikacje sieci web, aplikacje funkcyjne, aplikacje logiki, interfejs API i mikrousług.

### <a name="user-authentication"></a>Uwierzytelnianie użytkowników 

Aplikacje, które powinny korzystać z metody uwierzytelniania użytkownika, to aplikacje natywne do zarządzania lub monitorowania: aplikacje mobilne, aplikacje systemu Windows i aplikacje konsoli. Ten typ rozwiązania jest przydatny, gdy chcesz interakcji człowieka z usługą w jednym z następujących scenariuszy:

- Pulpit nawigacyjny monitorowania zadań kodowania.
- Pulpit nawigacyjny monitorowania transmisji na żywo.
- Aplikacja do zarządzania dla użytkowników komputerów stacjonarnych lub mobilnych do administrowania zasobami na koncie usługi Media Services.

> [!NOTE]
> Ta metoda uwierzytelniania nie powinna być używana dla aplikacji przeznaczonych dla konsumentów. 

Aplikacja natywna musi najpierw uzyskać token dostępu z usługi Azure AD, a następnie użyć go podczas wysyłania żądań HTTP do interfejsu API REST usługi Media Services. Dodaj token dostępu do nagłówka żądania. 

Na poniższym diagramie przedstawiono typowy przepływ uwierzytelniania aplikacji interaktywnych: 

![Diagram aplikacji natywnych](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Na poprzednim diagramie liczby reprezentują przepływ żądań w porządku chronologicznym.

> [!NOTE]
> Korzystając z metody uwierzytelniania użytkownika, wszystkie aplikacje współużytkują ten sam (domyślny) identyfikator klienta aplikacji natywnej i natywny identyfikator przekierowania aplikacji URI. 

1. Monituj użytkownika o poświadczenia.
2. Zażądaj tokenu dostępu usługi Azure AD z następującymi parametrami:  

   * Punkt końcowy dzierżawy usługi Azure AD.

       Informacje o dzierżawie można pobrać z witryny Azure portal. Umieść kursor nad nazwą zalogowanego użytkownika w prawym górnym rogu.
   * Identyfikator URI zasobu usługi Media Services. 

       Ten identyfikator URI jest taki sam dla kont usługi Media Services,\/które znajdują się w tym samym środowisku platformy Azure (na przykład https: /rest.media.azure.net).

   * Identyfikator klienta aplikacji usługi Media Services (natywnej).
   * Przekierowanie identyfikatora URI aplikacji usługi Media Services (natywnej).
   * Identyfikator URI zasobu dla usługi REST Media Services.
        
       Identyfikator URI reprezentuje punkt końcowy interfejsu https://test03.restv2.westus.media.azure.net/api/)API REST (na przykład .

     Aby uzyskać wartości dla tych parametrów, zobacz [Korzystanie z witryny Azure Portal, aby uzyskać dostęp do ustawień uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md) przy użyciu opcji uwierzytelniania użytkownika.

3. Token dostępu usługi Azure AD jest wysyłany do klienta.
4. Klient wysyła żądanie do interfejsu API rest multimediów azure za pomocą tokenu dostępu usługi Azure AD.
5. Klient pobiera dane z usługi Media Services.

Aby uzyskać informacje dotyczące używania uwierzytelniania usługi Azure AD do komunikowania się z żądaniami REST przy użyciu sdk klienta usługi Media Services .NET, zobacz [Używanie uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API usługi Media Services za pomocą platformy .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz zestawu SDK klienta usługi Media Services .NET, należy ręcznie utworzyć żądanie tokenu dostępu usługi Azure AD przy użyciu parametrów opisanych w kroku 2. Aby uzyskać więcej informacji, zobacz Jak uzyskać token usługi Azure AD token za [pomocą biblioteki uwierzytelniania usługi Azure AD.](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aplikacje, które często używają tej metody uwierzytelniania są aplikacje, które uruchamiają usługi warstwy środkowej i zaplanowane zadania: aplikacje sieci web, aplikacje funkcji, aplikacje logiki, interfejsy API i mikrousług. Ta metoda uwierzytelniania jest również odpowiednia dla aplikacji interaktywnych, w których można użyć konta usługi do zarządzania zasobami.

Podczas korzystania z metody uwierzytelniania jednostki usługi do tworzenia scenariuszy konsumenckich, uwierzytelnianie jest zazwyczaj obsługiwane w warstwie środkowej (za pośrednictwem niektórych interfejsów API), a nie bezpośrednio w aplikacji mobilnej lub klasycznej. 

Aby użyć tej metody, należy utworzyć aplikację i jednostkę usługi Azure AD w własnej dzierżawie. Po utworzeniu aplikacji należy przyznać aplikacji współautor lub właściciel dostępu do konta usługi Media Services. Można to zrobić w witrynie Azure portal, przy użyciu interfejsu wiersza polecenia platformy Azure lub skryptu programu PowerShell. Można również użyć istniejącej aplikacji usługi Azure AD. Możesz zarejestrować aplikację i jednostkę usługi Azure AD i zarządzać nią [w witrynie Azure portal.](media-services-portal-get-started-with-aad.md) Można to również zrobić przy użyciu [interfejsu wiersza polecenia platformy Azure](media-services-use-aad-auth-to-access-ams-api.md) lub programu [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplikacje warstwy środkowej](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Po utworzeniu aplikacji usługi Azure AD otrzymasz wartości dla następujących ustawień. Do uwierzytelniania potrzebne są następujące wartości:

- Identyfikator klienta 
- Klucz tajny klienta 

Na powyższej ilustracji liczby reprezentują przepływ żądań w porządku chronologicznym:
    
1. Aplikacja warstwy środkowej (internetowy interfejs API lub aplikacja sieci web) żąda tokenu dostępu usługi Azure AD, który ma następujące parametry:  

   * Punkt końcowy dzierżawy usługi Azure AD.

       Informacje o dzierżawie można pobrać z witryny Azure portal. Umieść kursor nad nazwą zalogowanego użytkownika w prawym górnym rogu.
   * Identyfikator URI zasobu usługi Media Services. 

       Ten identyfikator URI jest taki sam dla kont usługi Media Services, które\/znajdują się w tym samym środowisku platformy Azure (na przykład https: /rest.media.azure.net).

   * Identyfikator URI zasobu dla usługi REST Media Services.

       Identyfikator URI reprezentuje punkt końcowy interfejsu https://test03.restv2.westus.media.azure.net/api/)API REST (na przykład .

   * Wartości aplikacji usługi Azure AD: identyfikator klienta i klucz tajny klienta.
    
     Aby uzyskać wartości dla tych parametrów, zobacz [Korzystanie z witryny Azure Portal, aby uzyskać dostęp do ustawień uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md) przy użyciu opcji uwierzytelniania jednostkowego usługi.

2. Token dostępu usługi Azure AD jest wysyłany do warstwy środkowej.
4. Warstwa środkowa wysyła żądanie do interfejsu API rest multimediów azure za pomocą tokenu usługi Azure AD.
5. Warstwa środkowa pobiera dane z usługi Media Services.

Aby uzyskać więcej informacji na temat używania uwierzytelniania usługi Azure AD do komunikowania się z żądaniami REST przy użyciu sdk klienta usługi Media Services .NET, zobacz [Korzystanie z uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API usługi Azure Media Services za pomocą platformy .NET](media-services-dotnet-get-started-with-aad.md). 

Jeśli nie używasz zestawu SDK klienta usługi Media Services .NET, należy ręcznie utworzyć żądanie tokenu usługi Azure AD przy użyciu parametrów opisanych w kroku 1. Aby uzyskać więcej informacji, zobacz Jak uzyskać token usługi Azure AD token za [pomocą biblioteki uwierzytelniania usługi Azure AD.](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Wyjątek: "Serwer zdalny zwrócił błąd: (401) Nieautoryzowane."

Rozwiązanie: Aby żądanie REST usługi Media Services zakończyło się pomyślnie, użytkownik wywołujący musi być rolą współautora lub właściciela na koncie usługi Media Services, do które próbuje uzyskać dostęp. Aby uzyskać więcej informacji, zobacz sekcję [Kontrola dostępu.](media-services-use-aad-auth-to-access-ams-api.md#access-control)

## <a name="resources"></a>Resources

Następujące artykuły są omówienia koncepcji uwierzytelniania usługi Azure AD: 

- [Scenariusze uwierzytelniania zaadresowane przez usługę Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Dodawanie, aktualizowanie lub usuwanie aplikacji w usłudze Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Konfigurowanie kontroli dostępu opartej na rolach i zarządzanie nią przy użyciu programu PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Następne kroki

* Użyj witryny Azure Portal, aby [uzyskać dostęp do uwierzytelniania usługi Azure AD, aby korzystać z interfejsu API usługi Azure Media Services.](media-services-portal-get-started-with-aad.md)
* Użyj uwierzytelniania usługi Azure AD, aby uzyskać dostęp do [interfejsu API usługi Azure Media Services za pomocą platformy .NET](media-services-dotnet-get-started-with-aad.md).

