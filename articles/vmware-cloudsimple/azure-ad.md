---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Użyj usługi Azure AD jako źródła tożsamości w chmurze prywatnej
description: Opisuje sposób dodawania usługi Azure AD jako dostawcy tożsamości w prywatnej chmurze CloudSimple do uwierzytelniania użytkowników uzyskujących dostęp do CloudSimple z platformy Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898610"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Użyj usługi Azure AD jako dostawcy tożsamości dla programu vCenter w chmurze prywatnej CloudSimple

Można skonfigurować chmurę prywatną CloudSimple Cloud w celu uwierzytelniania za pomocą usługi Azure Active Directory (Azure AD) dla administratorów oprogramowania VMware w celu uzyskania dostępu do programu vCenter. Po skonfigurowaniu źródła tożsamości logowania jednokrotnego użytkownik **cloudowner** może dodawać użytkowników ze źródła tożsamości do programu vCenter.  

Domenę Active Directory i kontrolery domeny można skonfigurować w dowolny z następujących sposobów:

* Active Directory domen i kontrolerów domeny działających lokalnie
* Active Directory kontrolery domeny i domeny działające na platformie Azure jako maszyny wirtualne w ramach subskrypcji platformy Azure
* Nowe Active Directory domeny i kontrolery domeny działające w chmurze prywatnej CloudSimple
* Usługa Azure Active Directory

W tym przewodniku wyjaśniono zadania wymagane do skonfigurowania usługi Azure AD jako źródła tożsamości.  Aby uzyskać informacje na temat używania lokalnego Active Directory lub Active Directory uruchomionego na platformie Azure, zapoznaj się z tematem [Konfigurowanie źródeł tożsamości programu vCenter do użycia Active Directory](set-vcenter-identity.md) w celu uzyskania szczegółowych instrukcji dotyczących konfigurowania źródła tożsamości.

## <a name="about-azure-ad"></a>Informacje o usłudze Azure AD

Azure AD to usługa firmy Microsoft do obsługi wielu dzierżawców, katalogów opartych na chmurze i zarządzania tożsamościami.  Usługa Azure AD zapewnia skalowalny, spójny i niezawodny mechanizm uwierzytelniania służący do uwierzytelniania użytkowników i uzyskiwania dostępu do różnych usług na platformie Azure.  Zapewnia także bezpieczne usługi LDAP dla wszystkich usług innych firm, aby używać usługi Azure AD jako źródła uwierzytelniania/tożsamości.  Usługa Azure AD łączy podstawowe usługi katalogowe, zaawansowane funkcje zarządzania tożsamościami i zarządzanie dostępem do aplikacji, które mogą być używane do udzielania dostępu do chmury prywatnej użytkownikom, którzy administrują chmurą prywatną.

Aby używać usługi Azure AD jako źródła tożsamości z programem vCenter, musisz skonfigurować usługi Azure AD i Azure AD Domain Services. Postępuj zgodnie z tymi instrukcjami:

1. [Jak skonfigurować usługi Azure AD i Azure AD Domain Services](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Jak skonfigurować źródło tożsamości w chmurze prywatnej vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Konfigurowanie usług Azure AD i Azure AD Domain Services

Przed rozpoczęciem będziesz potrzebować dostępu do subskrypcji platformy Azure z uprawnieniami administratora globalnego.  Poniższe kroki zapewniają ogólne wytyczne. Szczegóły znajdują się w dokumentacji platformy Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Jeśli masz już usługę Azure AD, możesz pominąć tę sekcję.

1. Skonfiguruj usługę Azure AD w ramach subskrypcji zgodnie z opisem w  [dokumentacji usługi Azure AD](../active-directory/fundamentals/active-directory-whatis.md).
2. Włącz Azure Active Directory — wersja Premium w ramach subskrypcji zgodnie z opisem w temacie [Rejestrowanie się](../active-directory/fundamentals/active-directory-get-started-premium.md)w usłudze Azure Active Directory — wersja Premium.
3. Skonfiguruj niestandardową nazwę domeny i sprawdź niestandardową nazwę domeny zgodnie z opisem w polu [Dodaj niestandardową nazwę domeny do Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Skonfiguruj rekord DNS w rejestratorze domeny z informacjami podanymi na platformie Azure.
    2. Ustaw niestandardową nazwę domeny jako domenę podstawową.

Opcjonalnie można skonfigurować inne funkcje usługi Azure AD.  Nie są one wymagane do włączenia uwierzytelniania vCenter w usłudze Azure AD.

### <a name="azure-ad-domain-services"></a>Usługi domenowe Azure AD

> [!NOTE]
> Jest to ważny krok do włączenia usługi Azure AD jako źródła tożsamości dla serwera vCenter.  Aby uniknąć problemów, należy się upewnić, że wszystkie kroki są wykonywane poprawnie.

1. Włącz usługi domenowe Azure AD zgodnie z opisem w temacie [Włączanie usług domenowych Azure Active Directory przy użyciu Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
2. Skonfiguruj sieć, która będzie używana przez usługi domenowe Azure AD, zgodnie z opisem w temacie [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
3. Skonfiguruj grupę administratorów do zarządzania Azure AD Domain Services zgodnie z opisem w temacie [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
4. Zaktualizuj ustawienia DNS dla Azure AD Domain Services zgodnie z opisem w artykule [włączanie Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).  Jeśli chcesz nawiązać połączenie z usługą AD za pośrednictwem Internetu, skonfiguruj rekord DNS dla publicznego adresu IP usług domenowych Azure AD na nazwę domeny.
5. Włącz synchronizację skrótów haseł dla użytkowników.  Ten krok umożliwia synchronizację skrótów haseł wymaganych przez program NT LAN Manager (NTLM) i uwierzytelnianie Kerberos do Azure AD Domain Services. Po skonfigurowaniu synchronizacji skrótów haseł użytkownik może zalogować się do domeny zarządzanej przy użyciu poświadczeń firmowych. Zobacz [Włączanie synchronizacji skrótów haseł, aby Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).
    1. Jeśli istnieją użytkownicy tylko w chmurze, muszą zmienić hasło przy użyciu <a href="https://myapps.microsoft.com/" target="_blank">panelu dostępu usługi Azure AD</a> , aby zapewnić, że skróty haseł są przechowywane w formacie wymaganym przez protokół NTLM lub Kerberos.  Postępuj zgodnie z instrukcjami w temacie [Włączanie synchronizacji skrótów haseł do domeny zarządzanej dla kont użytkowników tylko w chmurze](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Ten krok należy wykonać dla poszczególnych użytkowników i każdego nowego użytkownika, który został utworzony w katalogu usługi Azure AD za pomocą poleceń cmdlet programu PowerShell Azure Portal lub Azure AD. Użytkownicy, którzy potrzebują dostępu do usług domenowych Azure AD, muszą użyć <a href="https://myapps.microsoft.com/" target="_blank">panelu dostępu usługi Azure AD</a> i uzyskać dostęp do swojego profilu, aby zmienić hasło.

        > [!NOTE]
        > Jeśli organizacja ma konta użytkowników tylko w chmurze, wszyscy użytkownicy, którzy muszą korzystać z usług Azure Active Directory Domain Services, muszą zmienić swoje hasła. Konto użytkownika tylko w chmurze to konto, które zostało utworzone w katalogu usługi Azure AD przy użyciu witryny Azure Portal lub poleceń cmdlet programu Azure AD PowerShell. Takie konta użytkownika nie są synchronizowane z poziomu katalogu lokalnego.

    2. Jeśli synchronizujesz hasła z lokalnej usługi Active Directory, wykonaj kroki opisane w [dokumentacji Active Directory](../active-directory-domain-services/tutorial-configure-password-hash-sync.md).

6.  Skonfiguruj bezpieczny protokół LDAP na Azure Active Directory Domain Services zgodnie z opisem w temacie [Konfigurowanie bezpiecznego protokołu LDAP (LDAPS) dla domeny zarządzanej Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Przekaż certyfikat do użycia przez bezpieczny protokół LDAP zgodnie z opisem w temacie [dotyczącym platformy Azure uzyskaj certyfikat dla bezpiecznego protokołu LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple zaleca użycie podpisanego certyfikatu wystawionego przez urząd certyfikacji, aby upewnić się, że program vCenter może ufać certyfikatowi.
    2. Włącz bezpieczny protokół LDAP zgodnie z opisem [Włącz bezpieczny protokół LDAP (LDAPS) dla domeny zarządzanej Azure AD Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Zapisz publiczną część certyfikatu (bez klucza prywatnego) w formacie CER do użycia z programem vCenter podczas konfigurowania źródła tożsamości.
    4. Jeśli jest wymagany dostęp do usług domenowych w usłudze Azure AD, Włącz opcję "Zezwalaj na bezpieczny dostęp do protokołu LDAP over internet".
    5. Dodaj regułę zabezpieczeń dla ruchu przychodzącego dla usług domenowych Azure AD sieciowej grupy zabezpieczeń dla portu TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Konfigurowanie źródła tożsamości w chmurze prywatnej vCenter

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md) do chmury prywatnej dla programu vCenter.
2. Zbierz parametry konfiguracji wymagane do skonfigurowania źródła tożsamości.

    | **Opcja** | **Opis** |
    |------------|-----------------|
    | **Nazwa** | Nazwa źródła tożsamości. |
    | **Podstawowa nazwa wyróżniająca dla użytkowników** | Podstawowa nazwa wyróżniająca dla użytkowników.  W przypadku usługi Azure AD Użyj: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  przykład: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` .|
    | **Nazwa domeny** | Nazwa FQDN domeny, na przykład example.com. W tym polu tekstowym nie należy podawać adresu IP. |
    | **Alias domeny** | *(opcjonalnie)* Nazwa NetBIOS domeny. Dodaj nazwę NetBIOS domeny Active Directory jako alias źródła tożsamości, jeśli używasz uwierzytelniania SSPI. |
    | **Podstawowa nazwa wyróżniająca dla grup** | Podstawowa nazwa wyróżniająca dla grup. W przypadku usługi Azure AD Użyj: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  przykład: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Podstawowy adres URL serwera** | Serwer LDAP podstawowego kontrolera domeny dla domeny.<br><br>Użyj formatu `ldaps://hostname:port`. Port jest zazwyczaj 636 dla połączeń LDAPs. <br><br>Certyfikat, który ustanawia zaufanie dla punktu końcowego LDAPs serwera Active Directory, jest wymagany w przypadku użycia `ldaps://` w podstawowym lub pomocniczym adresie URL LDAP. |
    | **Adres URL serwera pomocniczego** | Adres serwera LDAP pomocniczego kontrolera domeny, który jest używany do pracy w trybie failover. |
    | **Wybieranie certyfikatu** | Jeśli chcesz używać LDAPs z serwerem LDAP Active Directory lub źródłem tożsamości serwera OpenLDAP, po wpisaniu `ldaps://` w polu tekstowym adresu URL zostanie wyświetlony przycisk Wybierz certyfikat. Pomocniczy adres URL nie jest wymagany. |
    | **Nazwa użytkownika** | Identyfikator użytkownika w domenie, który ma minimalny dostęp tylko do odczytu do podstawowej nazwy wyróżniającej dla użytkowników i grup. |
    | **Password** (Hasło) | Hasło użytkownika, który jest określony przez nazwę użytkownika. |

3. Zaloguj się do prywatnej chmury programu vCenter po eskalacji uprawnień.
4. Postępuj zgodnie z instrukcjami w temacie [Dodawanie źródła tożsamości w programie vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) przy użyciu wartości z poprzedniego kroku, aby skonfigurować Azure Active Directory jako źródło tożsamości.
5. Dodaj użytkowników/grupy z usługi Azure AD do grup vCenter, zgodnie z opisem w temacie VMware [Dodaj członków do grupy vCenter Single Sign-On](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group   Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorów* .

## <a name="next-steps"></a>Następne kroki

* [Informacje o modelu uprawnień chmury prywatnej](learn-private-cloud-permissions.md)
