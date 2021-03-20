---
title: Co to jest Agent Azure AD Connect administrator Azure AD Connect | Microsoft Docs
description: Opisuje narzędzia używane do synchronizowania i monitorowania środowiska lokalnego w usłudze Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91312961"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co to jest agent administratora programu Azure AD Connect? 
Agent administracji Azure AD Connect jest nowym składnikiem Azure Active Directory Connect, który można zainstalować na serwerze Azure Active Directory Connect. Służy do zbierania określonych danych ze środowiska Active Directory, które ułatwiają inżynierowi pomocy technicznej firmy Microsoft Rozwiązywanie problemów podczas otwierania zgłoszenia do pomocy technicznej. 

>[!NOTE]
>Agent administracyjny nie jest instalowany i domyślnie włączony.  Należy zainstalować agenta, aby zbierać dane w celu ułatwienia pomocy technicznej.

Po zainstalowaniu Agent administracji Azure AD Connect czeka na określone żądania danych z Azure Active Directory, pobiera żądane dane ze środowiska synchronizacji i wysyła je do Azure Active Directory, gdzie jest prezentowany inżynierowi pomocy technicznej firmy Microsoft. 

Informacje, które Agent administracyjny Azure AD Connect pobiera ze środowiska, nie są przechowywane w żaden sposób — jest on wyświetlany tylko dla inżyniera pomocy technicznej firmy Microsoft, aby pomóc im w badaniu i rozwiązywaniu problemów związanych z obsługą Azure Active Directory Connect przypadku, gdy otwarty Agent administracyjny Azure AD Connect nie jest zainstalowany na serwerze Azure AD Connect domyślnie. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalowanie Azure AD Connect agenta administracyjnego na serwerze Azure AD Connect 

Wymagania wstępne:
1.    Azure AD Connect jest zainstalowany na serwerze
2.    Azure AD Connect Health jest zainstalowany na serwerze

![Agent administracyjny](media/whatis-aadc-admin-agent/adminagent0.png)

Pliki binarne agenta administracyjnego Azure AD Connect są umieszczane na serwerze programu AAD Connect. Aby zainstalować agenta programu, wykonaj następujące czynności:

1.    Otwórz program PowerShell w trybie administratora
2.    Przejdź do katalogu, w którym znajduje się dysk CD "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    Uruchom ConfigureAdminAgent.ps1

Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego usługi Azure AD. Powinno to być te same poświadczenia, które zostały wprowadzone podczas instalacji Azure AD Connect.

Po zainstalowaniu agenta zobaczysz następujące dwa nowe programy z listy "Dodaj/Usuń programy" w panelu sterowania serwera: 

![Zrzut ekranu pokazujący listę Dodaj/Usuń programy, która zawiera nowe dodane programy.](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jakie dane w ramach usługi synchronizacji są widoczne dla inżyniera usługi firmy Microsoft? 
Po otwarciu pomocy technicznej pomoc techniczna firmy Microsoft inżynier może zobaczyć, że dla danego użytkownika, odpowiednie dane w Active Directory, miejsce łącznika Active Directory na serwerze Azure Active Directory Connect, Azure Active Directory przestrzeń łącznika na serwerze Azure Active Directory Connect i w obiekcie Metaverse na serwerze Azure Active Directory Connect. 

Inżynier pomoc techniczna firmy Microsoft nie może zmienić żadnych danych w systemie i nie może zobaczyć żadnych haseł. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co zrobić, jeśli nie chcę, aby specjalista pomocy technicznej firmy Microsoft mógł uzyskać dostęp do moich danych? 
Jeśli agent zostanie zainstalowany, jeśli nie chcesz, aby inżynier usług firmy Microsoft mógł uzyskać dostęp do danych w celu uzyskania pomocy technicznej, możesz wyłączyć tę funkcję, modyfikując plik konfiguracji usługi zgodnie z poniższym opisem: 

1. Otwórz **folder C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** w Notatniku.
2. Wyłącz ustawienie **UserDataEnabled** , jak pokazano poniżej. Jeśli ustawienie **UserDataEnabled** istnieje i ma wartość true, ustaw dla niego wartość false. Jeśli to ustawienie nie istnieje, należy dodać ustawienie, jak pokazano poniżej.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Zapisz plik konfiguracji.
4. Uruchom ponownie usługę agenta administracyjnego Azure AD Connect, jak pokazano poniżej

![Zrzut ekranu pokazujący, gdzie należy ponownie uruchomić usługę agenta administratora usługi Azure AD.](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
