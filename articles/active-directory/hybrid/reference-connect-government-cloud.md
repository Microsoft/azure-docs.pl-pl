---
title: 'Azure AD Connect: uwagi dotyczące tożsamości hybrydowej dla chmury Azure Government'
description: Specjalne zagadnienia dotyczące wdrażania Azure AD Connect w chmurze Azure Government.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219893859d05eb419bc862484a9083abf8c26db7
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409301"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Zagadnienia dotyczące tożsamości hybrydowej w chmurze Azure Government

W tym artykule opisano zagadnienia dotyczące integracji środowiska hybrydowego z chmurą Microsoft Azure Government. Te informacje są udostępniane jako Dokumentacja dla administratorów i architektów, którzy pracują z chmurą Azure Government.

> [!NOTE]
> Aby zintegrować lokalne środowisko Microsoft Azure Active Directory (Azure AD) z chmurą Azure Government, należy przeprowadzić uaktualnienie do najnowszej wersji [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

Aby zapoznać się z pełną listą Stany Zjednoczone Departamentu Obrony rządowych, zapoznaj się z [dokumentacją](/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Uwierzytelnianie przekazywane przez usługę Azure AD

Poniższe informacje opisują implementację uwierzytelniania przekazywanego i chmurę Azure Government.

### <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL

Przed wdrożeniem agenta uwierzytelniania przekazywanego Sprawdź, czy istnieje Zapora między serwerami i usługą Azure AD. Jeśli zapora lub serwer proxy zezwala na dostęp do systemu nazw domen (DNS) blokowanych lub bezpiecznych dla programów, Dodaj następujące połączenia.

> [!NOTE]
> Poniższe wskazówki dotyczą także instalowania [łącznika usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/what-is-application-proxy.md) dla środowisk Azure Government.

|Adres URL |Zastosowanie|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|Agent używa tych adresów URL do komunikowania się z usługą w chmurze usługi Azure AD. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| Agent używa tych adresów URL do weryfikowania certyfikatów.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| Agent używa tych adresów URL podczas procesu rejestracji.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Zainstaluj agenta dla chmury Azure Government

Wykonaj następujące kroki, aby zainstalować agenta dla chmury Azure Government:

1. W terminalu wiersza polecenia przejdź do folderu, który zawiera plik wykonywalny, który instaluje agenta.
1. Uruchom następujące polecenia, aby określić, że instalacja ma Azure Government.

   W przypadku uwierzytelniania przekazywanego:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   W przypadku serwera proxy aplikacji:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Logowanie jednokrotne

### <a name="set-up-your-azure-ad-connect-server"></a>Skonfiguruj serwer Azure AD Connect

Jeśli używasz uwierzytelniania przekazywanego jako metody logowania, nie jest wymagane żadne dodatkowe Sprawdzanie wymagań wstępnych. Jeśli używasz synchronizacji skrótów haseł jako metody logowania i istnieje Zapora między Azure AD Connect a usługą Azure AD, upewnij się, że:

- Używasz Azure AD Connect w wersji 1.1.644.0 lub nowszej.
- Jeśli zapora lub serwer proxy zezwala na dostęp do zablokowanych lub bezpiecznych programów DNS, Dodaj połączenia do adresów URL &#42;. msappproxy.us na porcie 443.

  W przeciwnym razie Zezwól na dostęp do zakresów adresów IP centrum danych platformy Azure, które są aktualizowane co tydzień. To wymaganie wstępne ma zastosowanie tylko po włączeniu tej funkcji. Nie jest to wymagane w przypadku rzeczywistych logowania użytkowników.

### <a name="roll-out-seamless-single-sign-on"></a>Płynne rozwinięcie pojedynczej Sign-On

Możesz stopniowo wdrażać bezproblemowo pojedyncze Sign-On w usłudze Azure AD, wykonując poniższe instrukcje. Zacznij od dodania adresu URL usługi Azure AD `https://autologon.microsoft.us` do wszystkich ustawień strefy intranetowej lub wybranych użytkowników za pomocą zasady grupy w Active Directory.

Należy również włączyć ustawienie zasad strefy intranet **Zezwalaj na aktualizacje paska stanu za pośrednictwem skryptu za pośrednictwem zasady grupy**.

## <a name="browser-considerations"></a>Zagadnienia dotyczące przeglądarki

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (wszystkie platformy)

Mozilla Firefox nie korzysta automatycznie z uwierzytelniania Kerberos. Każdy użytkownik musi ręcznie dodać adres URL usługi Azure AD do swoich ustawień przeglądarki Firefox, wykonując następujące czynności:

1. Uruchom przeglądarkę Firefox i wpisz **about: config**   na pasku adresu. Odrzuć wszystkie powiadomienia, które mogą zostać wyświetlone.
1. Wyszukaj preferencje **Network. Negocjuj-auth. Trusted-URI**   . To preferencje zawiera listę witryn zaufanych przez przeglądarkę Firefox na potrzeby uwierzytelniania Kerberos.
1. Kliknij prawym przyciskiem myszy nazwę preferencji, a następnie wybierz polecenie **Modyfikuj**.
1. Wprowadź `https://autologon.microsoft.us` w polu.
1. Wybierz przycisk **OK**   , a następnie ponownie otwórz przeglądarkę.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge oparta na chromie (wszystkie platformy)

Jeśli ustawienia zasad zostały zastąpione  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   w środowisku programu, upewnij się, że do nich dodano adres URL usługi Azure AD `https://autologon.microsoft.us` .

### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy)

Jeśli ustawienia zasad zostały zastąpione  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   w środowisku programu, upewnij się, że do nich dodano adres URL usługi Azure AD `https://autologon.microsoft.us` .

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie przekazywane](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Logowanie jednokrotne](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)