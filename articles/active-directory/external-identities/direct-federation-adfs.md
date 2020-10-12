---
title: Konfigurowanie bezpośredniej Federacji z AD FS dla B2B — Azure AD
description: Dowiedz się, jak skonfigurować AD FS jako dostawcę tożsamości dla Federacji bezpośredniej, aby Goście mogli logować się do aplikacji usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909564"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Przykład: bezpośrednia Federacja z Active Directory Federation Services (AD FS) (wersja zapoznawcza)

> [!NOTE]
> Federacja bezpośrednia jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

W tym artykule opisano sposób konfigurowania [Federacji bezpośredniej](direct-federation.md) przy użyciu Active Directory Federation Services (AD FS) jako dostawcy tożsamości SAML 2,0 lub WS-Fed. Aby zapewnić obsługę federacji bezpośredniej, należy skonfigurować pewne atrybuty i oświadczenia u dostawcy tożsamości. Aby zilustrować sposób konfigurowania dostawcy tożsamości dla Federacji bezpośredniej, będziemy używać Active Directory Federation Services (AD FS) jako przykładu. Pokażemy, jak skonfigurować AD FS zarówno jako dostawcę tożsamości SAML, jak i jako dostawcę tożsamości WS-Fed.

> [!NOTE]
> W tym artykule opisano sposób konfigurowania AD FS dla języka SAML i WS-Fed na potrzeby ilustracji. W przypadku bezpośrednich integracji Federacji, w których jest AD FS dostawca tożsamości, zalecamy użycie WS-Fed jako protokołu. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurowanie AD FS dla Federacji bezpośredniego SAML 2,0
Usługę Azure AD B2B można skonfigurować tak, aby sfederować się z dostawcami tożsamości, którzy korzystają z protokołu SAML z określonymi wymaganiami wymienionymi poniżej. Aby zilustrować kroki konfiguracji języka SAML, w tej sekcji przedstawiono sposób konfigurowania AD FS dla protokołu SAML 2,0. 

Aby skonfigurować bezpośrednią Federacji, należy odebrać następujące atrybuty w odpowiedzi SAML 2,0 od dostawcy tożsamości. Te atrybuty można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego w trybie online lub wprowadzając je ręcznie. Krok 12 w temacie [Create a test AD FS instance](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) opisuje, jak znaleźć punkty końcowe AD FS lub jak wygenerować adres URL metadanych, na przykład `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Atrybut  |Wartość  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator URI wystawcy partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |

Następujące oświadczenia muszą zostać skonfigurowane w tokenie SAML 2,0 wystawionym przez dostawcę tożsamości:


|Atrybut  |Wartość  |
|---------|---------|
|Format NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


W następnej sekcji pokazano, jak skonfigurować wymagane atrybuty i oświadczenia przy użyciu AD FS jako przykład dostawcy tożsamości SAML 2,0.

### <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem tej procedury należy wcześniej skonfigurować i działać serwer AD FS. Aby uzyskać pomoc dotyczącą konfigurowania serwera AD FS, zobacz [Tworzenie wystąpienia testowego AD FS 3,0 na maszynie wirtualnej platformy Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Dodaj opis zgłoszenia

1. Na serwerze AD FS wybierz pozycję **Narzędzia**  >  **AD FS zarządzanie**.
2. W okienku nawigacji wybierz pozycję opisy **usług**  >  **Claim Descriptions**.
3. W obszarze **Akcje**wybierz pozycję **Dodaj opis żądania**.
4. W oknie **Dodawanie opisu roszczeń** określ następujące wartości:

   - **Nazwa wyświetlana**: Identyfikator trwały
   - **Identyfikator zgłoszenia**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Zaznacz pole wyboru **Publikuj ten opis tego żądania w metadanych Federacji jako typ typu, który może akceptować ta usługa federacyjna**.
   - Zaznacz pole wyboru **Opublikuj ten opis tego żądania w metadanych Federacji jako typ, który może wysłać ta usługa federacyjna**.

5. Kliknij przycisk **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Dodawanie zaufania jednostki uzależnionej i reguł dotyczących roszczeń

1. Na serwerze AD FS przejdź do pozycji **Narzędzia**  >  **AD FS zarządzanie**.
2. W okienku nawigacji wybierz pozycję **zaufania relacje**  >  **zaufania jednostek uzależnionych**.
3. W obszarze **Akcje**wybierz pozycję **Dodaj zaufanie jednostki uzależnionej**. 
4. W Kreatorze dodawania zaufania jednostki uzależnionej dla **wyboru źródła danych**Użyj opcji **Importuj dane dotyczące jednostki uzależnionej opublikowane online lub w sieci lokalnej**. Określ adres URL metadanych Federacji — https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml . Pozostaw inne ustawienia domyślne. Wybierz pozycję **Close** (Zamknij).
5. Zostanie otwarty Kreator **edycji reguł dotyczących roszczeń** .
6. W kreatorze **Edytowanie reguł dotyczących roszczeń** wybierz pozycję **Dodaj regułę**. W obszarze **Wybierz typ reguły**wybierz opcję **Wyślij atrybuty LDAP jako oświadczenia**. Wybierz opcję **Dalej**.
7. W obszarze **Konfigurowanie reguły roszczeń**określ następujące wartości: 

   - **Nazwa reguły**dotyczącej roszczeń: reguła dotycząca roszczeń e-mail 
   - **Magazyn atrybutów**: Active Directory 
   - **Atrybut LDAP**: adresy e-mail 
   - **Typ zgłoszenia wychodzącego**: adres E-mail

8. Wybierz pozycję **Zakończ**.
9. W oknie **Edycja reguł roszczeń** zostanie wyświetlona Nowa reguła. Kliknij pozycję **Zastosuj**. 
10. Kliknij przycisk **OK**.  

### <a name="create-an-email-transform-rule"></a>Tworzenie reguły przekształcania wiadomości e-mail
1. Przejdź do pozycji **Edytuj reguły dotyczące roszczeń** i kliknij pozycję **Dodaj regułę**. W obszarze **Wybierz typ reguły**wybierz pozycję **Przekształć przychodzące zdarzenie** i kliknij przycisk **dalej**. 
2. W obszarze **Konfigurowanie reguły roszczeń**określ następujące wartości: 

   - **Nazwa reguły**dotyczącej roszczeń: reguła transformacji poczty e-mail 
   - **Typ zgłoszenia przychodzącego**: adres E-mail 
   - **Typ zgłoszenia wychodzącego**: identyfikator nazwy 
   - **Format identyfikatora nazwy wychodzącej**: Identyfikator trwały 
   - Wybierz pozycję **Przekazuj wszystkie wartości oświadczeń**.

3. Kliknij przycisk **Finish** (Zakończ). 
4. W oknie **Edycja reguł roszczeń** zostaną wyświetlone nowe reguły. Kliknij pozycję **Zastosuj**. 
5. Kliknij przycisk **OK**. Serwer AD FS jest teraz skonfigurowany do bezpośredniej Federacji przy użyciu protokołu SAML 2,0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurowanie AD FS dla Federacji bezpośredniej WS-Fed 
Usługę Azure AD B2B można skonfigurować tak, aby sfederować się z dostawcami tożsamości, którzy używają protokołu WS-Fed z określonymi wymaganiami wymienionymi poniżej. Obecnie dwaj dostawcy WS-Fed zostali przetestowani pod kątem zgodności z usługą Azure AD, w tym AD FS i Shibboleth. W tym miejscu będziemy używać Active Directory Federation Services (AD FS) jako przykładowego dostawcy tożsamości WS-Fed. Aby uzyskać więcej informacji na temat ustanawiania zaufania jednostki uzależnionej między dostawcą zgodnym WS-Fed a usługą Azure AD, pobierz dokumenty zgodności dostawcy tożsamości usługi Azure AD.

Aby skonfigurować bezpośrednią Federacji, należy odebrać następujące atrybuty w komunikacie WS-Fed od dostawcy tożsamości. Te atrybuty można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego w trybie online lub wprowadzając je ręcznie. Krok 12 w temacie [Create a test AD FS instance](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) opisuje, jak znaleźć punkty końcowe AD FS lub jak wygenerować adres URL metadanych, na przykład `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Atrybut  |Wartość  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator URI wystawcy partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |

Wymagane oświadczenia dla WS-Fed tokenu wystawionego przez dostawcy tożsamości:

|Atrybut  |Wartość  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

W następnej sekcji pokazano, jak skonfigurować wymagane atrybuty i oświadczenia przy użyciu AD FS jako przykładowego dostawcy tożsamości WS-Fed.

### <a name="before-you-begin"></a>Zanim rozpoczniesz
Przed rozpoczęciem tej procedury należy wcześniej skonfigurować i działać serwer AD FS. Aby uzyskać pomoc dotyczącą konfigurowania serwera AD FS, zobacz [Tworzenie wystąpienia testowego AD FS 3,0 na maszynie wirtualnej platformy Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Dodawanie zaufania jednostki uzależnionej i reguł dotyczących roszczeń 
1. Na serwerze AD FS przejdź do pozycji **Narzędzia**  >  **AD FS zarządzanie**. 
1. W okienku nawigacji wybierz pozycję **zaufania relacje**  >  **zaufania jednostek uzależnionych**. 
1. W obszarze **Akcje**wybierz pozycję **Dodaj zaufanie jednostki uzależnionej**.  
1. W Kreatorze dodawania zaufania jednostki uzależnionej dla **Wybierz źródło danych**Użyj opcji **Importuj dane dotyczące jednostki uzależnionej opublikowane online lub w sieci lokalnej**. Określ adres URL metadanych Federacji: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Pozostaw inne ustawienia domyślne. Wybierz pozycję **Close** (Zamknij).
1. Zostanie otwarty Kreator **edycji reguł dotyczących roszczeń** . 
1. W kreatorze **Edytowanie reguł dotyczących roszczeń** wybierz pozycję **Dodaj regułę**. W obszarze **Wybierz typ reguły**wybierz pozycję **Wyślij oświadczenia przy użyciu reguły niestandardowej**. Wybierz opcję *Dalej*. 
1. W obszarze **Konfigurowanie reguły roszczeń**określ następujące wartości:

   - **Nazwa reguły**dotyczącej roszczeń: problem z niezmiennym identyfikatorem  
   - **Reguła niestandardowa**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Wybierz pozycję **Zakończ**. 
1. W oknie **Edycja reguł roszczeń** zostanie wyświetlona Nowa reguła. Kliknij pozycję **Zastosuj**.  
1. W tym samym kreatorze **edycji reguł dotyczących roszczeń** wybierz pozycję **Dodaj regułę**. W obszarze **Typ reguły**dla dowolnego węża wybierz opcję **Wyślij atrybuty LDAP jako oświadczenia**. Wybierz opcję **Dalej**.
1. W obszarze **Konfigurowanie reguły roszczeń**określ następujące wartości: 

   - **Nazwa reguły**dotyczącej roszczeń: reguła dotycząca roszczeń e-mail  
   - **Magazyn atrybutów**: Active Directory  
   - **Atrybut LDAP**: adresy e-mail  
   - **Typ zgłoszenia wychodzącego**: adres E-mail 

1.  Wybierz pozycję **Zakończ**. 
1.  W oknie **Edycja reguł roszczeń** zostanie wyświetlona Nowa reguła. Kliknij pozycję **Zastosuj**.  
1.  Kliknij przycisk **OK**. Serwer AD FS jest teraz skonfigurowany do bezpośredniej Federacji przy użyciu protokołu WS-karmionego.

## <a name="next-steps"></a>Następne kroki
Następnie [skonfigurujesz bezpośrednią Federacji w usłudze Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) w portalu usługi Azure AD lub przy użyciu programu PowerShell. 
