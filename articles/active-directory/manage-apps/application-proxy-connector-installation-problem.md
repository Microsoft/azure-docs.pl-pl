---
title: Problem z instalacją łącznika agenta proxy aplikacji | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy, które mogą wystąpić podczas instalowania łącznika agenta agenta proxy aplikacji
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406699"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem z instalacją łącznika agenta serwera proxy aplikacji

Łącznik serwera proxy aplikacji AAD firmy Microsoft to wewnętrzny składnik domeny, który używa połączeń wychodzących do ustanawiania łączności z dostępnego punktu końcowego w chmurze z domeną wewnętrzną.

## <a name="general-problem-areas-with-connector-installation"></a>Ogólne obszary problemowe z instalacją łącznika

Gdy instalacja łącznika nie powiedzie się, główną przyczyną jest zwykle jeden z następujących obszarów:

1.  **Łączność** — aby zakończyć pomyślną instalację, nowy łącznik musi zarejestrować i ustanowić przyszłe właściwości zaufania. Odbywa się to przez połączenie z usługą serwera proxy aplikacji AAD w chmurze.

2.  **Trust Establishment** — nowy łącznik tworzy certyfikat z podpisem własnym i rejestruje się w usłudze w chmurze.

3.  **Uwierzytelnianie administratora** — podczas instalacji użytkownik musi podać poświadczenia administratora, aby ukończyć instalację łącznika.

> [!NOTE]
> Dzienniki instalacji łącznika można znaleźć w folderze %TEMP% i mogą pomóc w dostarczeniu dodatkowych informacji na temat przyczyn awarii instalacji.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Weryfikowanie łączności z usługą serwera proxy aplikacji w chmurze i stroną Microsoft Login

**Cel:** Sprawdź, czy komputer łącznika może łączyć się z punktem końcowym rejestracji serwera proxy aplikacji AAD, a także ze stroną logowania firmy Microsoft.

1.  Na serwerze łączników uruchom test portu za pomocą [usługi telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) lub innego narzędzia do testowania portów w celu sprawdzenia, czy porty 443 i 80 są otwarte.

2.  Jeśli którykolwiek z tych portów nie powiedzie się, sprawdź, czy serwer proxy zapory lub wewnętrznej bazy danych ma dostęp do wymaganych domen i portów zobacz, [Przygotuj środowisko lokalne](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Otwórz przeglądarkę (oddzielną kartę) i przejdź `https://login.microsoftonline.com`do następującej strony internetowej: , upewnij się, że możesz zalogować się do tej strony.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Weryfikowanie obsługi składników komputera i zaplecza dla certyfikatu zaufania serwera proxy aplikacji

**Cel:** Sprawdź, czy komputer łącznika, serwer proxy wewnętrznej bazy danych i zapora mogą obsługiwać certyfikat utworzony przez łącznik dla przyszłego zaufania i czy certyfikat jest prawidłowy.

>[!NOTE]
>Łącznik próbuje utworzyć certyfikat SHA512, który jest obsługiwany przez TLS1.2. Jeśli komputer lub zapora wewnętrznej bazy danych i serwer proxy nie obsługuje protokołu TLS1.2, instalacja nie powiedzie się.
>
>

**Zapoznaj się z wymaganymi wymaganiami wstępnymi:**

1.  Sprawdź, czy urządzenie obsługuje protokół TLS1.2 — wszystkie wersje systemu Windows po 2012 R2 powinny obsługiwać protokół TLS 1.2. Jeśli urządzenie złącza pochodzi z wersji 2012 R2 lub wcześniejszej, upewnij się, że na urządzeniu są zainstalowane następujące kb:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Skontaktuj się z administratorem sieci i poproś o sprawdzenie, czy serwer proxy i zapora wewnętrznej bazy danych nie blokują sha512 dla ruchu wychodzącego.

**Aby zweryfikować certyfikat klienta:**

Sprawdź odcisk palca bieżącego certyfikatu klienta. Magazyn certyfikatów można znaleźć w pliku %ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Oto możliwe wartości i znaczenia **IsInUserStore:**

- **false** — certyfikat klienta został utworzony podczas instalacji lub rejestracji zainicjowanej przez polecenie Register-AppProxyConnector. Jest on przechowywany w osobistym pojemniku w magazynie certyfikatów komputera lokalnego. 

Wykonaj czynności, aby zweryfikować certyfikat:

1. Uruchom **plik certlm.msc**
2. W konsoli zarządzania rozwiń kontener osobisty i kliknij certyfikaty
3. Znajdź certyfikat wystawiony przez **connectorregistrationca.msappproxy.net**

- **true** — automatycznie odnowiony certyfikat jest przechowywany w kontenerze osobistym w magazynie certyfikatów użytkownika usługi sieciowej. 

Wykonaj czynności, aby zweryfikować certyfikat:

1. Pobierz [PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. Wyodrębnij [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) z pakietu i uruchom **psexec -i -u "nt authority\network service" cmd.exe** z wiersza polecenia z podwyższonym poziomem uprawnień.
3. Uruchom **plik certmgr.msc** w nowo wyświetlonym wierszu polecenia
2. W konsoli zarządzania rozwiń kontener osobisty i kliknij certyfikaty
3. Znajdź certyfikat wystawiony przez **connectorregistrationca.msappproxy.ne

**Aby odnowić certyfikat klienta:**

Jeśli łącznik nie jest podłączony do usługi przez kilka miesięcy, jego certyfikaty mogą być nieaktualne. Niepowodzenie odnowienia certyfikatu prowadzi do wygasłego certyfikatu. Dzięki temu usługa łącznika przestaje działać. Zdarzenie 1000 jest rejestrowane w dzienniku administratora łącznika:

"Ponowna rejestracja łącznika nie powiodła się: certyfikat zaufania łącznika wygasł. Uruchom polecenie cmdlet Register-AppProxyConnector programu PowerShell na komputerze, na którym jest uruchomiony łącznik, aby ponownie zarejestrować łącznik."

W takim przypadku odinstaluj i zainstaluj ponownie łącznik, aby wyzwolić rejestrację lub można uruchomić następujące polecenia programu PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Aby dowiedzieć się więcej o poleceniu Register-AppProxyConnector, zobacz [Tworzenie skryptu instalacji nienadzorowanego dla łącznika serwera proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Sprawdź, czy administrator jest używany do zainstalowania łącznika

**Cel:** Sprawdź, czy użytkownik, który próbuje zainstalować łącznik, jest administratorem z poprawnymi poświadczeniami. Obecnie użytkownik musi być co najmniej administratorem aplikacji, aby instalacja powiodła się.

**Aby sprawdzić, czy poświadczenia są poprawne:**

Połącz `https://login.microsoftonline.com` się z tymi samymi poświadczeniami i użyj ich. Upewnij się, że logowanie zakończyło się pomyślnie. Możesz sprawdzić rolę użytkownika, przechodząc do **usługi Azure Active Directory Użytkownicy**  - &gt; **i grupy**  - &gt; **Wszyscy użytkownicy**. 

Wybierz konto użytkownika, a następnie "Rola katalogu" w menu wynikowym. Sprawdź, czy wybrana rola to "Administrator aplikacji". Jeśli nie możesz uzyskać dostępu do żadnej ze stron w tych krokach, nie masz wymaganej roli.

## <a name="next-steps"></a>Następne kroki
[Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
