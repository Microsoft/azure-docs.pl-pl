---
title: 'Azure AD Connect: uwagi dotyczące tożsamości hybrydowej dla Azure Government'
description: Specjalne zagadnienia dotyczące wdrażania Azure AD Connect w chmurze dla instytucji rządowych.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378927"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Zagadnienia dotyczące tożsamości hybrydowej dla usługi Azure Government 
W poniższym dokumencie opisano zagadnienia dotyczące implementowania środowiska hybrydowego w chmurze Azure Government.  Te informacje są udostępniane jako Dokumentacja dla administratorów i architektów, którzy pracują z chmurą Azure Government.  
> [!NOTE] 
> Aby zintegrować lokalne środowisko usługi AD z chmurą usługi Azure Governemnt, należy przeprowadzić uaktualnienie do najnowszej wersji [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Pełną listę punktów końcowych USA w Stanach Zjednoczonych można znaleźć w [dokumentacji](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) . 

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane 
Poniższe informacje dotyczą implementacji uwierzytelniania przekazywanego (PTA) i chmury Azure Government.

### <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL  
Przed wdrożeniem agenta uwierzytelniania przekazywanego Sprawdź, czy istnieje Zapora między serwerami i usługą Azure AD. Jeśli zapora lub serwer proxy zezwala na listy dozwolonych DNS, Dodaj następujące połączenia: 
> [!NOTE]
> Poniższe wskazówki dotyczą także instalowania [łącznika serwera proxy aplikacji](https://aka.ms/whyappproxy) dla środowisk Azure Governmentych.

|Adres URL |Zastosowanie|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Komunikacja między agentem a usługą w chmurze usługi Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Agent używa tych adresów URL do weryfikowania certyfikatów.| 
|Login.Windows.us Secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.NET </br> *. msauthimages.NET </br>*. msecnd.NET</br>*. msftauth.NET </br>*. msftauthimages.NET</br>*. PhoneFactor.NET </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Agent używa tych adresów URL podczas procesu rejestracji.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Zainstaluj agenta dla chmury Azure Government 
Aby zainstalować agenta dla chmury Azure Government, należy wykonać następujące czynności: w terminalu wiersza polecenia przejdź do folderu, w którym znajduje się plik wykonywalny służący do instalacji agenta. Uruchom następujące polecenie, które określa, że instalacja ma Azure Government. 

Na potrzeby uwierzytelniania Passthrough: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

W przypadku serwera proxy aplikacji:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Logowanie jednokrotne 
Skonfiguruj serwer Azure AD Connect: Jeśli używasz uwierzytelniania przekazywanego jako metody logowania, nie jest wymagane żadne dodatkowe Sprawdzanie wymagań wstępnych. Jeśli używasz synchronizacji skrótów haseł jako metody logowania, a jeśli między Azure AD Connect i usługą Azure AD istnieje Zapora, upewnij się, że:
- Używasz wersji 1.1.644.0 lub nowszej Azure AD Connect. 
- Jeśli zapora lub serwer proxy zezwala na listy dozwolonych DNS, Dodaj połączenia do adresów URL *. msapproxy.us przez port 443. W przeciwnym razie Zezwól na dostęp do zakresów adresów IP centrum danych platformy Azure, które są aktualizowane co tydzień. To wymaganie wstępne jest stosowane tylko po włączeniu funkcji. Nie jest to wymagane w przypadku rzeczywistych logowań użytkownika. 

### <a name="rolling-out-seamless-sso"></a>Bezproblemowe wdrażanie logowania jednokrotnego 
Możesz stopniowo wdrożyć bezproblemową rejestrację jednokrotną dla użytkowników, korzystając z instrukcji podanych poniżej. Aby rozpocząć, Dodaj następujący adres URL usługi Azure AD do ustawień strefy intranetowej wszystkich lub wybranych użytkowników za pomocą zasady grupy w Active Directory:https://autologon.microsoft.us 

Ponadto należy włączyć ustawienie zasad strefy intranet o nazwie Zezwalaj na aktualizacje na pasku stanu za pośrednictwem skryptu za pośrednictwem zasady grupy. Zagadnienia dotyczące przeglądarki Mozilla Firefox (wszystkie platformy) Mozilla Firefox nie korzysta automatycznie z uwierzytelniania Kerberos. Każdy użytkownik musi ręcznie dodać adres URL usługi Azure AD do ustawień przeglądarki Firefox, wykonując następujące czynności: 
1. Uruchom przeglądarkę Firefox i wpisz about: config na pasku adresu. Odrzuć wszystkie wyświetlone powiadomienia. 
2. Wyszukaj preferencje Network. Negocjuj-auth. Trusted-URI. To preferencje zawiera Zaufane witryny programu Firefox do uwierzytelniania Kerberos. 
3. Kliknij prawym przyciskiem myszy i wybierz polecenie Modyfikuj. 
4. Wprowadź https://autologon.microsoft.us wartość w polu.
5. Wybierz przycisk OK, a następnie ponownie otwórz przeglądarkę. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge oparta na chromie (wszystkie platformy) 
Jeśli `AuthNegotiateDelegateAllowlist`  `AuthServerAllowlist`ustawienia zasad w Twoim środowisku zostały zastąpione lub, upewnij się, że adres URL usługi Azure AD został równieżhttps://autologon.microsoft.us) dodany do nich. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy) 
Jeśli `AuthNegotiateDelegateWhitelist`  `AuthServerWhitelist`ustawienia zasad w Twoim środowisku zostały zastąpione lub, upewnij się, że adres URL usługi Azure AD został równieżhttps://autologon.microsoft.us) dodany do nich. 

## <a name="next-steps"></a>Następne kroki
[Pass-through Authentication](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Logowanie](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) jednokrotne uwierzytelniania przekazywanego 
