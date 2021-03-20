---
title: Problem z instalacją łącznika agenta serwera proxy aplikacji
description: Jak rozwiązywać problemy, które można napotkać podczas instalowania łącznika agenta serwera proxy aplikacji dla Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0f76f03883746b6f4b87bb817f8adde850ed28b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253668"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem z instalacją łącznika agenta serwera proxy aplikacji

Łącznik serwera proxy aplikacji Microsoft Azure Active Directory jest wewnętrznym składnikiem domeny, który używa połączeń wychodzących do nawiązywania połączenia z punktem końcowym dostępnym w chmurze do domeny wewnętrznej.

## <a name="general-problem-areas-with-connector-installation"></a>Ogólne obszary problemów z instalacją łącznika

Gdy instalacja łącznika nie powiedzie się, główną przyczyną jest zazwyczaj jeden z następujących obszarów. **W celu przeprowadzenia rozwiązywania problemów należy ponownie uruchomić łącznik.**

1.  **Łączność** — aby ukończyć pomyślną instalację, nowy łącznik musi zarejestrować i ustanowić przyszłe właściwości zaufania. W tym celu należy nawiązać połączenie z usługą serwer proxy aplikacji usługi Azure Active Directory w chmurze.

2.  **Ustanowienie zaufania** — nowy łącznik tworzy certyfikat z podpisem własnym i rejestruje usługę w chmurze.

3.  **Uwierzytelnianie administratora** — w trakcie instalacji użytkownik musi podać poświadczenia administratora, aby zakończyć instalację łącznika.

> [!NOTE]
> Dzienniki instalacji łącznika znajdują się w folderze% TEMP% i mogą pomóc w określeniu dodatkowych informacji na temat tego, co powoduje błąd instalacji.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Weryfikowanie łączności z usługą serwera proxy aplikacji w chmurze i stroną logowania firmy Microsoft

**Cel:** Sprawdź, czy komputer łącznika może nawiązać połączenie z punktem końcowym rejestracji serwera proxy aplikacji, a także stroną logowania firmy Microsoft.

1.  Na serwerze łącznika Uruchom test portu przy użyciu programu [Telnet](/windows-server/administration/windows-commands/telnet) lub innego narzędzia do testowania portów, aby sprawdzić, czy porty 443 i 80 są otwarte.

2.  Jeśli którykolwiek z tych portów nie powiedzie się, sprawdź, czy zapora lub serwer proxy zaplecza ma dostęp do wymaganych domen i portów, zobacz [Przygotowywanie środowiska lokalnego](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Otwórz przeglądarkę (osobna karta) i przejdź do następującej strony sieci Web: Upewnij się `https://login.microsoftonline.com` , że możesz zalogować się na tej stronie.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Sprawdź, czy maszyny i składniki zaplecza obsługują certyfikat zaufania serwera proxy aplikacji

**Cel:** Sprawdź, czy maszyna łącznika, serwer proxy zaplecza i Zapora mogą obsługiwać certyfikat utworzony przez łącznik na potrzeby przyszłego zaufania i czy certyfikat jest prawidłowy.

>[!NOTE]
>Łącznik próbuje utworzyć certyfikat SHA512, który jest obsługiwany przez protokół TLS 1.2. Jeśli maszyna lub Zapora zaplecza i serwer proxy nie obsługują protokołu TLS 1.2, instalacja nie powiedzie się.
>
>

**Przejrzyj wymagane wymagania wstępne:**

1.  Sprawdź, czy maszyna obsługuje protokół TLS 1.2 — wszystkie wersje systemu Windows po 2012 R2 powinny obsługiwać protokół TLS 1,2. Jeśli komputer łącznika pochodzi z wersji 2012 R2 lub starszej, upewnij się, że na maszynie jest zainstalowany następujący artykułów bazy wiedzy: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Skontaktuj się z administratorem sieci i poproś o zweryfikowanie, czy serwer proxy zaplecza i Zapora nie blokują SHA512 dla ruchu wychodzącego.

**Aby zweryfikować certyfikat klienta:**

Sprawdź odcisk palca bieżącego certyfikatu klienta. Magazyn certyfikatów można znaleźć w temacie `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Możliwe wartości **IsInUserStore** to **true** i **false**. Wartość **true** oznacza, że automatycznie odnowiony certyfikat jest przechowywany w kontenerze osobistym w magazynie certyfikatów użytkownika usługi sieciowej. Wartość **false** oznacza, że certyfikat klienta został utworzony podczas instalacji lub rejestracji zainicjowanej przez polecenie Register-AppProxyConnector i jest przechowywany w kontenerze osobistym w magazynie certyfikatów komputera lokalnego.

Jeśli wartość jest **równa true**, wykonaj następujące kroki, aby zweryfikować certyfikat:
1. Pobierz [PsTools.zip](/sysinternals/downloads/pstools)
2. Wyodrębnij [PsExec](/sysinternals/downloads/psexec) z pakietu i uruchom **PsExec-i-u "NT authority\network Service" cmd.exe** z wiersza polecenia z podwyższonym poziomem uprawnień.
3. Uruchom **certmgr. msc** w nowo wyświetlonym wierszu polecenia
4. W konsoli zarządzania rozwiń kontener osobisty i kliknij pozycję Certyfikaty.
5. Lokalizowanie certyfikatu wystawionego przez **connectorregistrationca.msappproxy.NET**

Jeśli wartość jest równa **false**, wykonaj następujące kroki, aby zweryfikować certyfikat:
1. Uruchom **certlm. msc**
2. W konsoli zarządzania rozwiń kontener osobisty i kliknij pozycję Certyfikaty.
3. Lokalizowanie certyfikatu wystawionego przez **connectorregistrationca.msappproxy.NET**

**Aby odnowić certyfikat klienta:**

Jeśli łącznik nie jest połączony z usługą przez kilka miesięcy, jego certyfikaty mogą być nieaktualne. Niepowodzenie odnowienia certyfikatu prowadzi do wygasłego certyfikatu. Dzięki temu usługa łącznika przestanie działać. Zdarzenie 1000 jest rejestrowane w dzienniku administratora łącznika:

"Ponowna rejestracja łącznika nie powiodła się: certyfikat zaufania łącznika wygasł. Uruchom polecenie cmdlet programu PowerShell Register-AppProxyConnector na komputerze, na którym jest uruchomiony łącznik, aby ponownie zarejestrować łącznik ".

W takim przypadku Odinstaluj i ponownie zainstaluj łącznik, aby wyzwolić rejestrację, lub uruchom następujące polecenia programu PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Aby dowiedzieć się więcej na temat polecenia Register-AppProxyConnector, zobacz [Tworzenie skryptu instalacji nienadzorowanej dla łącznika usługi Azure serwer proxy aplikacji usługi Azure AD](./application-proxy-register-connector-powershell.md) .

## <a name="verify-admin-is-used-to-install-the-connector"></a>Sprawdź, czy administrator służy do instalowania łącznika

**Cel:** Sprawdź, czy użytkownik, który próbuje zainstalować łącznik, jest administratorem z prawidłowymi poświadczeniami. Obecnie aby instalacja się powiodła, użytkownik musi być co najmniej administratorem aplikacji.

**Aby sprawdzić, czy poświadczenia są poprawne:**

Połącz się z `https://login.microsoftonline.com` tymi samymi poświadczeniami i używaj tych samych poświadczeń. Upewnij się, że logowanie zakończyło się pomyślnie. Rolę użytkownika można sprawdzić, przechodząc do **Azure Active Directory**  - &gt; **użytkowników i grup**  - &gt; **Wszyscy użytkownicy**. 

Wybierz konto użytkownika, a następnie "rola katalogu" w menu wyników. Sprawdź, czy wybrana rola to "Administrator aplikacji". Jeśli nie możesz uzyskać dostępu do żadnych stron w ramach tych kroków, nie masz wymaganej roli.

## <a name="next-steps"></a>Następne kroki
[Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
