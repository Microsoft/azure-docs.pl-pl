---
title: Porównanie usług opartych na Active Directory na platformie Azure | Microsoft Docs
description: W tym omówieniu porównano różne oferty tożsamości dla Active Directory Domain Services, Azure Active Directory i Azure Active Directory Domain Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: joflore
ms.openlocfilehash: 916615808bc1e28b9794b57e08960520e3abd835
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602278"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Porównaj Active Directory Domain Services samozarządzane, Azure Active Directory i zarządzane Azure Active Directory Domain Services

Aby zapewnić aplikacjom, usługom lub urządzeniom dostęp do tożsamości centralnej, istnieją trzy typowe sposoby korzystania z usług opartych na Active Directory na platformie Azure. Ten wybór rozwiązań tożsamości zapewnia elastyczność korzystania z najbardziej odpowiedniego katalogu dla potrzeb organizacji. Na przykład w przypadku większości zarządzania użytkownikami tylko w chmurze, którzy uruchamiają urządzenia przenośne, może nie mieć sensu kompilowania i uruchamiania własnego rozwiązania do obsługi tożsamości w Active Directory Domain Services (AD DS). Zamiast tego można użyć Azure Active Directory.

Chociaż trzy rozwiązania do obsługi tożsamości oparte na Active Directory korzystają ze wspólnej nazwy i technologii, są one przeznaczone do świadczenia usług spełniających różne wymagania klientów. Na wysokim poziomie te rozwiązania i zestawy funkcji tożsamości są następujące:

* **Active Directory Domain Services (AD DS)** — serwer LDAP (Lightweight Directory Access Protocol), który udostępnia kluczowe funkcje, takie jak tożsamość i uwierzytelnianie, zarządzanie obiektami komputera, zasady grupy i relacje zaufania.
    * AD DS jest centralnym składnikiem w wielu organizacjach z lokalnym środowiskiem INFORMATYCZNym, a także udostępnia podstawowe funkcje uwierzytelniania i zarządzania kontami użytkowników.
    * Aby uzyskać więcej informacji, zobacz [Active Directory Domain Services Omówienie w dokumentacji systemu Windows Server][overview-adds].
* **Azure Active Directory (Azure AD)** — tożsamość oparta na chmurze i zarządzanie urządzeniami przenośnymi, które udostępniają usługi kont i uwierzytelniania użytkowników, takie jak Microsoft 365, Azure Portal lub aplikacje SaaS.
    * Usługę Azure AD można synchronizować z lokalnym środowiskiem AD DS w celu zapewnienia pojedynczej tożsamości użytkownikom, którzy działają natywnie w chmurze.
    * Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [co to jest Azure Active Directory?][whatis-azuread]
* **Azure Active Directory Domain Services (Azure AD DS)** — oferuje zarządzane usługi domenowe z podzbiorem w pełni zgodnych tradycyjnych funkcji AD DS, takich jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM.
    * Platforma Azure AD DS integruje się z usługą Azure AD, którą sama można synchronizować z lokalnym środowiskiem AD DS. Ta możliwość rozszerza centralne przypadki użycia tożsamości na tradycyjne aplikacje sieci Web, które działają na platformie Azure w ramach strategii podnoszenia i przesunięcia.
    * Aby dowiedzieć się więcej na temat synchronizacji z usługą Azure AD i lokalnie, zobacz [jak obiekty i poświadczenia są synchronizowane w domenie zarządzanej][synchronization].

W tym artykule omówiono porównanie i różnice między tym, jak te rozwiązania tożsamości mogą współdziałać, lub mogą być używane niezależnie, w zależności od potrzeb organizacji.

> [!div class="nextstepaction"]
> [Aby rozpocząć, Utwórz domenę zarządzaną platformy Azure AD DS przy użyciu Azure Portal][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>AD DS platformy Azure i samozarządzane AD DS

Jeśli masz aplikacje i usługi, które wymagają dostępu do tradycyjnych mechanizmów uwierzytelniania, takich jak Kerberos lub NTLM, istnieją dwa sposoby zapewnienia Active Directory Domain Services w chmurze:

* *Domena zarządzana* utworzona przy użyciu Azure Active Directory Domain Services (AD DS platformy Azure). Firma Microsoft tworzy wymagane zasoby i zarządza nimi.
* *Samodzielna* domena, którą tworzysz i konfigurujesz przy użyciu tradycyjnych zasobów, takich jak maszyny wirtualne, system operacyjny gościa systemu Windows Server i Active Directory Domain Services (AD DS). Następnie można nadal administrować tymi zasobami.

Dzięki usłudze Azure AD DS podstawowe składniki usługi są wdrażane i obsługiwane przez firmę Microsoft jako środowisko domeny *zarządzanej* . Nie można wdrażać, instalować, poprawiać i zabezpieczać infrastruktury AD DS dla składników, takich jak maszyny wirtualne, system operacyjny Windows Server lub kontrolery domeny (DC).

Usługa Azure AD DS udostępnia mniejszy podzbiór funkcji dla tradycyjnego, samodzielnego środowiska AD DS, co zmniejsza stopień złożoności projektu i zarządzania. Na przykład nie istnieją żadne linki AD, domeny, witryny i replikacja do projektowania i konserwowania. Można nadal [tworzyć relacje zaufania lasów między usługą Azure AD DS i środowiskami lokalnymi][create-forest-trust].

W przypadku aplikacji i usług, które działają w chmurze i potrzebują dostępu do tradycyjnych mechanizmów uwierzytelniania, takich jak Kerberos lub NTLM, usługa Azure AD DS zapewnia środowisko domeny zarządzanej z minimalną ilością kosztów administracyjnych. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące zarządzania kontami użytkowników, hasłami i administracją w usłudze Azure AD DS][administration-concepts].

W przypadku wdrażania i uruchamiania samozarządzanego środowiska AD DS należy zachować wszystkie skojarzone składniki infrastruktury i katalogu. Istnieje dodatkowe obciążenie związane z konserwacją przy użyciu samodzielnego środowiska AD DS, ale następnie można wykonać dodatkowe zadania, takie jak rozszerzanie schematu lub tworzenie relacji zaufania lasów.

Typowe modele wdrażania dla samozarządzanego środowiska AD DS, które zapewniają tożsamość do aplikacji i usług w chmurze, obejmują następujące elementy:

* **Autonomiczna AD DS tylko w chmurze** — maszyny wirtualne platformy Azure są skonfigurowane jako kontrolery domeny i zostanie utworzone oddzielne środowisko AD DS tylko w chmurze. To środowisko AD DS nie jest integrowane z lokalnym środowiskiem AD DS. Inny zestaw poświadczeń służy do logowania i administrowania maszynami wirtualnymi w chmurze.
* **Wdrożenie lasu zasobów** — maszyny wirtualne platformy Azure są skonfigurowane jako kontrolery domeny i jest tworzony AD DS domeny, która jest częścią istniejącego lasu. Relacja zaufania jest następnie konfigurowana do lokalnego środowiska AD DS. Inne maszyny wirtualne platformy Azure mogą przyłączać do domeny do tego lasu zasobów w chmurze. Uwierzytelnianie użytkowników odbywa się za pośrednictwem połączenia VPN/ExpressRoute do lokalnego środowiska AD DS.
* **Rozszerzona domena lokalna na platformę Azure** — Sieć wirtualna platformy Azure łączy się z siecią lokalną za pomocą połączenia VPN/ExpressRoute. Maszyny wirtualne platformy Azure łączą się z tą siecią wirtualną platformy Azure, która umożliwia im przyłączanie do domeny w środowisku lokalnym AD DS.
    * Alternatywą jest tworzenie maszyn wirtualnych platformy Azure i ich podwyższenie poziomu jako repliki kontrolerów domeny z lokalnej domeny AD DS. Te kontrolery domeny są replikowane za pośrednictwem połączenia VPN/ExpressRoute do środowiska lokalnego AD DS. Lokalna domena AD DS jest efektywnie rozszerzona na platformę Azure.

W poniższej tabeli przedstawiono niektóre funkcje, które mogą być potrzebne w organizacji, oraz różnice między zarządzaną domeną platformy Azure AD DS lub samozarządzaną domeną AD DS:

| **Funkcja** | **Azure AD DS** | **AD DS samozarządzane** |
| ----------- |:---------------:|:----------------------:|
| **Usługa zarządzana**                               | **&#x2713;** | **&#x2715;** |
| **Bezpieczne wdrożenia**                            | **&#x2713;** | Administrator zabezpiecza wdrożenie |
| **Serwer DNS**                                    | **&#x2713;** (usługa zarządzana) |**&#x2713;** |
| **Uprawnienia administratora domeny lub przedsiębiorstwa** | **&#x2715;** | **&#x2713;** |
| **Przyłączanie do domeny**                                   | **&#x2713;** | **&#x2713;** |
| **Uwierzytelnianie domeny przy użyciu protokołów NTLM i Kerberos** | **&#x2713;** | **&#x2713;** |
| **Ograniczone delegowanie protokołu Kerberos**               | Oparte na zasobach | Oparte na zasobach & na koncie|
| **Niestandardowa struktura jednostki organizacyjnej**                           | **&#x2713;** | **&#x2713;** |
| **zasady grupy**                                  | **&#x2713;** | **&#x2713;** |
| **Rozszerzenia schematu**                             | **&#x2715;** | **&#x2713;** |
| **Relacje zaufania domen/lasów usługi AD**                     | **&#x2713;** (tylko jednokierunkowe zaufania lasów wychodzących) | **&#x2713;** |
| **Bezpieczny protokół LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **Odczyt LDAP**                                     | **&#x2713;** | **&#x2713;** |
| **Zapis LDAP**                                    | **&#x2713;** (w domenie zarządzanej) | **&#x2713;** |
| **Wdrożenia rozproszone geograficznie**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS i Azure AD

Usługa Azure AD umożliwia zarządzanie tożsamościami urządzeń używanych w organizacji oraz kontrolowanie dostępu do zasobów firmy z tych urządzeń. Użytkownicy mogą również rejestrować swoje urządzenia osobiste (BYOD) przy użyciu usługi Azure AD, która zapewnia urządzeniu tożsamość. Usługa Azure AD następnie uwierzytelnia urządzenie, gdy użytkownik zaloguje się do usługi Azure AD i użyje urządzenia, aby uzyskać dostęp do zabezpieczonych zasobów. Urządzeniem można zarządzać za pomocą oprogramowania do zarządzania urządzeniami przenośnymi (MDM), takiego jak Microsoft Intune. Ta możliwość zarządzania pozwala ograniczyć dostęp do poufnych zasobów do urządzeń zarządzanych i zgodnych z zasadami.

Tradycyjne komputery i laptopy mogą również przyłączać się do usługi Azure AD. Ten mechanizm oferuje te same korzyści związane z rejestrowaniem urządzenia osobistego w usłudze Azure AD, takie jak umożliwienie użytkownikom logowania się na urządzeniu przy użyciu poświadczeń firmowych.

Urządzenia przyłączone do usługi Azure AD dają następujące korzyści:

* Logowanie jednokrotne (SSO) do aplikacji zabezpieczonych przez usługę Azure AD.
* Roaming ustawień użytkownika zgodny z zasadami przedsiębiorstwa na różnych urządzeniach.
* Dostęp do sklepu Windows dla firm przy użyciu poświadczeń firmowych.
* Funkcja Windows Hello dla firm.
* Ograniczony dostęp do aplikacji i zasobów z urządzeń zgodnych z zasadami firmowymi.

Urządzenia mogą być przyłączone do usługi Azure AD z wdrożeniem hybrydowym lub bez niego, które obejmuje lokalne środowisko AD DS. W poniższej tabeli przedstawiono typowe modele własności urządzeń oraz sposób ich przyłączenia do domeny:

| **Typ urządzenia**                                        | **Platformy urządzeń**             | **Ustanawia**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Urządzenia osobiste                                          | Windows 10, iOS, Android, macOS | Zarejestrowane w usłudze Azure AD    |
| Urządzenie należące do organizacji nie jest przyłączone do AD DS lokalnego | Windows 10                       | Dołączone do usługi Azure AD        |
| Urządzenie należące do organizacji przyłączone do AD DS lokalnego  | Windows 10                       | hybrydowym dołączonym do usługi Azure AD. |

Na urządzeniu przyłączonym do usługi Azure AD lub zarejestrowaniu uwierzytelnianie użytkownika odbywa się przy użyciu nowoczesnych protokołów opartych na programie OAuth/OpenID Connect Connect. Te protokoły są przeznaczone do pracy przez Internet, dlatego doskonale nadaje się do scenariuszy mobilnych, w których użytkownicy uzyskują dostęp do zasobów firmy z dowolnego miejsca.

W przypadku urządzeń przyłączonych do usługi Azure AD DS aplikacje mogą używać protokołów Kerberos i NTLM do uwierzytelniania, więc mogą obsługiwać starsze aplikacje migrowane do uruchamiania na maszynach wirtualnych platformy Azure w ramach strategii podnoszenia i przesunięcia. W poniższej tabeli przedstawiono różnice w sposobie reprezentowania urządzeń i można uwierzytelnić się w katalogu:

| **Aspekt**                      | **Usługa Azure AD — przyłączona**                                 | **AD DS platformy Azure — przyłączone**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Urządzenie kontrolowane przez            | Azure AD                                            | Domena zarządzana AD DS platformy Azure                                                |
| Reprezentacja w katalogu | Obiekty urządzeń w katalogu usługi Azure AD            | Obiekty komputerów w domenie zarządzanej AD DS platformy Azure                        |
| Authentication                  | Protokoły oparte na połączeniach OAuth/OpenID Connect              | Protokoły Kerberos i NTLM                                               |
| Zarządzanie                      | Oprogramowanie do zarządzania urządzeniami przenośnymi (MDM), takie jak usługa Intune | Zasady grupy                                                              |
| Sieć                      | Działa za pośrednictwem Internetu                             | Połączenie z siecią wirtualną, w której wdrożono domenę zarządzaną, musi być połączone z usługą lub za pomocą komunikacji równorzędnej |
| Doskonały dla...                    | Urządzenia mobilne lub stacjonarne użytkowników końcowych                  | Maszyny wirtualne serwera wdrożone na platformie Azure                                              |


Jeśli na Premium AD DS i usługi Azure AD są skonfigurowane do uwierzytelniania federacyjnego przy użyciu usług AD FS, w usłudze Azure DS nie jest dostępny żaden (bieżący/prawidłowy) skrót hasła. Konta użytkowników usługi Azure AD utworzone przed zaimplementowanym uwierzytelnianiem pokarmowym mogą mieć stary skrót hasła, ale prawdopodobnie nie pasuje do skrótu hasła Premium. Dlatego AD DS platformy Azure nie będzie w stanie zweryfikować poświadczeń użytkowników

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usługi Azure AD DS, [Utwórz domenę zarządzaną platformy azure AD DS przy użyciu Azure Portal][tutorial-create].

Możesz również dowiedzieć się więcej [na temat pojęć związanych z zarządzaniem kontami użytkowników, hasłami i administracją w usłudze Azure AD DS][administration-concepts] oraz [sposobu synchronizacji obiektów i poświadczeń w domenie zarządzanej][synchronization].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
