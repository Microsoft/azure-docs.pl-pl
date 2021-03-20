---
title: Projektowanie tożsamości hybrydowej — strategia wdrażania platformy Azure | Microsoft Docs
description: Przy użyciu kontroli dostępu warunkowego usługa Azure AD sprawdza określone warunki, które można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836193"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiowanie hybrydowej strategii wdrażania tożsamości
W tym zadaniu zdefiniujemy hybrydową strategię wdrażania tożsamości dla hybrydowego rozwiązania do obsługi tożsamości, aby spełnić wymagania biznesowe omówione w temacie:

* [Określanie potrzeb firmy](plan-hybrid-identity-design-considerations-business-needs.md)
* [Określanie wymagań dotyczących synchronizacji katalogów](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiowanie strategii potrzeb firmy
Pierwsze zadanie określa wymagania biznesowe organizacji.  Może to być bardzo szerokie i zakres może wystąpić, jeśli nie jest to ostrożne.  Na początku należy zachować prostotę, ale zawsze należy pamiętać o zaplanowaniu projektu, który będzie uwzględniał zmiany w przyszłości.  Bez względu na to, czy jest to prosty projekt, czy bardzo skomplikowany, Azure Active Directory jest platformą tożsamości firmy Microsoft, która obsługuje Microsoft 365, usług online firmy Microsoft i aplikacji obsługujących chmurę.

## <a name="define-an-integration-strategy"></a>Definiowanie strategii integracji
Firma Microsoft ma trzy główne scenariusze integracji, które są tożsamościami w chmurze, synchronizowanymi tożsamościami i tożsamościami federacyjnymi.  Należy zaplanować przyjęcie jednej z tych strategii integracji.  Wybrana strategia może się różnić, a decyzje w wyborze jednego mogą obejmować, jaki typ środowiska użytkownika ma być udostępniany, czy masz istniejącą infrastrukturę i najbardziej opłacalną.  

![scenariusze integracji](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scenariusze zdefiniowane na powyższym rysunku są następujące:

* **Tożsamości w chmurze**: są to tożsamości, które istnieją wyłącznie w chmurze.  W przypadku usługi Azure AD mogą się one znajdować w katalogu usługi Azure AD.
* **Zsynchronizowane**: są to tożsamości, które znajdują się lokalnie i w chmurze.  Przy użyciu Azure AD Connect są one tworzone lub dołączane do istniejących kont usługi Azure AD.  Skrót hasła użytkownika jest synchronizowany z poziomu środowiska lokalnego do chmury w systemie, w którym jest wywoływana wartość skrótu hasła.  Po zsynchronizowaniu z jednym zastrzeżeniem jest to, że jeśli użytkownik jest wyłączony w środowisku lokalnym, może upłynąć do 3 godzin, aby wyświetlić stan tego konta w usłudze Azure AD.  Jest to spowodowane przedziałem czasu synchronizacji.
* **Federacyjne**: te tożsamości istnieją zarówno lokalnie, jak i w chmurze.  Przy użyciu Azure AD Connect są one tworzone lub dołączane do istniejących kont usługi Azure AD.  

> [!NOTE]
> Aby uzyskać więcej informacji na temat opcji synchronizacji, przeczytaj artykuł [integrowanie tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

W poniższej tabeli przedstawiono zalety i wady każdej z następujących strategii:

| Strategia | Zalety | Wady |
| --- | --- | --- |
| **Tożsamości w chmurze** |Łatwiejsze zarządzanie małymi organizacjami. <br> Nie ma nic do zainstalowania w środowisku lokalnym. Nie jest wymagany żaden dodatkowy sprzęt<br>Łatwo wyłączyć, jeśli użytkownik opuści firmę |Użytkownicy będą musieli zalogować się podczas uzyskiwania dostępu do obciążeń w chmurze <br> Hasła mogą lub nie być takie same w przypadku tożsamości w chmurze i lokalnych |
| **Zsynchronizować** |Hasło lokalne uwierzytelnia zarówno katalogi lokalne, jak i w chmurze <br>Łatwiejsze zarządzanie małymi, średnimi lub dużymi organizacjami <br>Użytkownicy mogą korzystać z logowania jednokrotnego (SSO) dla niektórych zasobów <br> Preferowana metoda firmy Microsoft do synchronizacji <br> Łatwiejsze zarządzanie |Niektórzy klienci mogą być zniechęcić do synchronizowania swoich katalogów z zasadami obowiązującymi w chmurze dla określonych firm |
| **Federacyjni** |Użytkownicy mogą mieć Logowanie jednokrotne (SSO) <br>Jeśli użytkownik zostanie przerwany lub opuszcza, konto może zostać natychmiast wyłączone i dostęp zostaje odwołany.<br> Obsługuje zaawansowane scenariusze, które nie mogą być wykonywane z synchronizacją |Więcej kroków konfigurowania i konfigurowania <br> Wyższa konserwacja <br> Może wymagać dodatkowego sprzętu dla infrastruktury STS <br> Może wymagać dodatkowego sprzętu, aby zainstalować serwer federacyjny. Jeśli jest używane AD FS, wymagane jest dodatkowe oprogramowanie <br> Wymagaj rozbudowanej konfiguracji logowania jednokrotnego <br> Krytyczny punkt awarii, jeśli serwer federacyjny nie działa, użytkownicy nie będą mogli się uwierzytelnić |

### <a name="client-experience"></a>Środowisko pracy klienta
Stosowana strategia spowoduje wydyktowanie środowiska logowania użytkownika.  W poniższych tabelach przedstawiono informacje na temat tego, co użytkownicy powinni oczekiwać na korzystanie z funkcji logowania.  Nie wszyscy dostawcy tożsamości federacyjnych obsługują Logowanie jednokrotne we wszystkich scenariuszach.

**Domenowe — połączone i prywatne aplikacje sieciowe**:

| Aplikacja | Tożsamość zsynchronizowana | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki sieci Web |Uwierzytelnianie oparte na formularzach |Logowanie jednokrotne, czasami wymagane do podania identyfikatora organizacji |
| Outlook |Monituj o poświadczenia |Monituj o poświadczenia |
| Skype dla firm (Lync) |Monituj o poświadczenia |Logowanie jednokrotne w programie Lync, monitowanie poświadczeń dla programu Exchange |
| OneDrive dla Firm |Monituj o poświadczenia |Logowanie jednokrotne |
| Subskrypcja pakietu Office Pro Plus |Monituj o poświadczenia |Logowanie jednokrotne |

**Źródła zewnętrzne lub niezaufane**:

| Aplikacja | Tożsamość zsynchronizowana | Tożsamość federacyjna |
| --- | --- | --- |
| Przeglądarki sieci Web |Uwierzytelnianie oparte na formularzach |Uwierzytelnianie oparte na formularzach |
| Outlook, Skype dla firm (Lync), OneDrive dla firm i subskrypcja pakietu Office |Monituj o poświadczenia |Monituj o poświadczenia |
| Exchange ActiveSync |Monituj o poświadczenia |Logowanie jednokrotne w programie Lync, monitowanie poświadczeń dla programu Exchange |
| Aplikacji mobilnych |Monituj o poświadczenia |Monituj o poświadczenia |

W przypadku określenia z zadania 1 dostawcy tożsamości innej firmy lub korzystania z niej w celu zapewnienia Federacji z usługą Azure AD należy znać następujące obsługiwane możliwości:

* Każdy dostawca SAML 2,0, który jest zgodny z profilem SP-Lite może obsługiwać uwierzytelnianie w usłudze Azure AD i skojarzonych aplikacjach
* Obsługuje uwierzytelnianie pasywne, co ułatwia uwierzytelnianie w programie OWA, SPO itp.
* Klienci usługi Exchange Online mogą być obsługiwani za pośrednictwem rozszerzonego profilu klienta SAML 2,0 (ECP)

Należy również wiedzieć, jakie funkcje nie będą dostępne:

* Bez obsługi protokołu WS-Trust/Federacji wszystkie pozostałe aktywne klienci są przerywane.
  * Oznacza to, że nie ma klienta Lync, klienta usługi OneDrive, subskrypcji pakietu Office i pakietu Office Mobile przed pakietem Office 2016
* Przejście pakietu Office do pasywnego uwierzytelniania pozwala im na obsługę czystych dostawców tożsamości SAML 2,0, ale pomoc techniczna będzie nadal działać na klientach.

> [!NOTE]
> Aby uzyskać listę najbardziej zaktualizowanych informacji, przeczytaj artykuł [Lista zgodności Federacji usługi Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Zdefiniuj strategię synchronizacji
W tym zadaniu zdefiniujesz narzędzia, które będą używane do synchronizowania danych lokalnych organizacji z chmurą i topologii, której chcesz używać.  Ze względu na to, że większość organizacji używa Active Directory, informacje na temat korzystania z usługi Azure AD Connect w celu rozwiązania powyższych pytań są podane szczegółowo.  W przypadku środowisk, które nie mają Active Directory, istnieją informacje dotyczące korzystania z programu FIM 2010 R2 lub MIM 2016 w celu zaplanowania tej strategii.  Jednak w przyszłych wersjach Azure AD Connect będą obsługiwane katalogi LDAP, dlatego w zależności od osi czasu te informacje mogą być pomocne.

### <a name="synchronization-tools"></a>Narzędzia synchronizacji
W ciągu lat wiele narzędzi synchronizacji istniało i używanych w różnych scenariuszach.  Obecnie Azure AD Connect to wybrane narzędzie do wyboru dla wszystkich obsługiwanych scenariuszy.  AAD Sync i DirSync również są nadal używane i mogą nawet być obecne w Twoim środowisku. 

> [!NOTE]
> Najnowsze informacje dotyczące obsługiwanych funkcji poszczególnych narzędzi znajdziesz w artykule [porównanie narzędzi do integracji katalogów](plan-hybrid-identity-design-considerations-tools-comparison.md) .  
> 
> 

### <a name="supported-topologies"></a>Obsługiwane topologie
Podczas definiowania strategii synchronizacji należy określić topologię, która jest używana. W zależności od informacji, które zostały określone w kroku 2 można określić topologię, która jest odpowiednia do użycia. Pojedynczy las, pojedyncza topologia usługi Azure AD jest najpopularniejszy i składa się z jednego lasu Active Directory i jednego wystąpienia usługi Azure AD.  Ta wartość jest używana w większości scenariuszy i jest oczekiwaną topologią w przypadku korzystania z instalacji Azure AD Connect Express, jak pokazano na poniższej ilustracji.

![Scenariusz obsługiwane topologie ](./media/plan-hybrid-identity-design-considerations/single-forest.png) Single Forest jest często stosowany w dużych i nawet małych organizacjach, które mają wiele lasów, jak pokazano na rysunku 5.

> [!NOTE]
> Aby uzyskać więcej informacji na temat różnych lokalnych i topologii usługi Azure AD z synchronizacją Azure AD Connect, zapoznaj się z [topologiami artykułu Azure AD Connect](plan-connect-topologies.md).
> 
> 

![Topologia z obsługą kilku lasów](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenariusz obejmujący wiele lasów

W takim przypadku należy wziąć pod uwagę pojedynczą topologię usługi Azure AD, jeśli spełnione są następujące elementy:

* Użytkownicy mają tylko 1 tożsamość we wszystkich lasach — bardziej szczegółowo opisano w poniższej sekcji Użytkownicy z jednoznacznie identyfikującymi się informacjami.
* Użytkownik jest uwierzytelniany w lesie, w którym znajduje się jego tożsamość
* Nazwa UPN i kotwica źródłowa (z niezmiennym identyfikatorem) będą pochodzić z tego lasu
* Wszystkie lasy są dostępne przez Azure AD Connect — oznacza to, że nie trzeba przyłączyć się do domeny i może być umieszczane w strefie DMZ, jeśli to ułatwia.
* Użytkownicy mają tylko jedną skrzynkę pocztową
* Las, który hostuje skrzynkę pocztową użytkownika, ma najlepszą jakość danych dla atrybutów widocznych na globalnej liście adresowej programu Exchange.
* Jeśli użytkownik nie ma skrzynki pocztowej, do współtworzenia tych wartości można użyć dowolnego lasu.
* Jeśli masz połączoną skrzynkę pocztową, istnieje również inne konto w innym lesie używanym do logowania.

> [!NOTE]
> Obiekty istniejące zarówno w środowisku lokalnym, jak i w chmurze są "połączone" przy użyciu unikatowego identyfikatora. W kontekście synchronizacji katalogów Ten unikatowy identyfikator jest określany jako SourceAnchor. W kontekście logowania jednokrotnego nazywa się to ImmutableId. [Zaprojektuj koncepcje dotyczące Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) , aby uzyskać więcej informacji dotyczących używania SourceAnchor.
> 
> 

Jeśli powyższe wartości nie są spełnione i masz więcej niż jedno aktywne konto lub więcej niż jedna Skrzynka pocztowa, Azure AD Connect wybierze jedną i zignoruje drugą.  Jeśli masz połączone skrzynki pocztowe, ale nie inne konto, te konta nie zostaną wyeksportowane do usługi Azure AD, a użytkownik nie będzie członkiem żadnej grupy.  Różni się to od tego, jak był w przeszłości za pomocą narzędzia DirSync i jest zamierzony, aby lepiej obsługiwać te scenariusze obejmujące wiele lasów. Scenariusz obejmujący wiele lasów przedstawiono na poniższej ilustracji.

![wiele dzierżawców usługi Azure AD](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Scenariusz wielu lasów usługi Azure AD**

Zalecane jest używanie tylko jednego katalogu w usłudze Azure AD dla organizacji, ale jest on obsługiwany, a 1:1 relacja jest utrzymywana między serwerem synchronizacji Azure AD Connect a katalogiem usługi Azure AD.  Dla każdego wystąpienia usługi Azure AD potrzebna jest instalacja Azure AD Connect.  Ponadto usługa Azure AD jest izolowana, a użytkownicy w jednym wystąpieniu usługi Azure AD nie będą mogli zobaczyć użytkowników w innym wystąpieniu.

Jest możliwe i obsługiwane łączenie jednego lokalnego wystąpienia Active Directory z wieloma katalogami usługi Azure AD, jak pokazano na poniższej ilustracji:

![Filtrowanie pojedynczego lasu](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Scenariusz filtrowania z jednym lasem**

W tym celu należy wykonać następujące czynności:

* Serwery synchronizacji Azure AD Connect muszą być skonfigurowane do filtrowania, dzięki czemu każdy z nich ma wzajemnie wykluczające się zestaw obiektów.  Można to zrobić na przykład przez określanie zakresu każdego serwera do określonej domeny lub jednostki organizacyjnej.
* Domenę DNS można zarejestrować tylko w jednym katalogu usługi Azure AD, aby nazwy UPN użytkowników w lokalnej usłudze AD musiały używać oddzielnych przestrzeni nazw
* Użytkownicy w jednym wystąpieniu usługi Azure AD będą mogli zobaczyć użytkowników z ich wystąpienia.  Nie będą oni mogli zobaczyć użytkowników w innych wystąpieniach
* Tylko jeden z katalogów usługi Azure AD może włączyć funkcję hybrydową programu Exchange z lokalną usługą AD
* Wzajemne Wykluczanie ma zastosowanie również do zapisu zwrotnego.  Powoduje to, że niektóre funkcje zapisywania zwrotnego nie są obsługiwane w przypadku tej topologii, ponieważ zakładają one jedną konfigurację lokalną.  Obejmuje to następujące działania:
  * Grupuj kopie zapasowe z konfiguracją domyślną
  * Zapisywanie zwrotne urządzeń

Następujące elementy nie są obsługiwane i nie powinny być wybierane jako implementacja:

* Nie jest obsługiwane, aby wiele serwerów synchronizacji Azure AD Connect łączyć się z tym samym katalogiem usługi Azure AD, nawet jeśli są skonfigurowane do synchronizacji wzajemnie wykluczających się zestawów obiektów
* Synchronizacja tego samego użytkownika z wieloma katalogami usługi Azure AD jest nieobsługiwana. 
* Nie jest również możliwe wprowadzenie zmian w konfiguracji, aby umożliwić użytkownikom w jednej usłudze Azure AD wyświetlanie jako kontakty w innym katalogu usługi Azure AD. 
* Modyfikowanie Azure AD Connect synchronizacji w celu nawiązania połączenia z wieloma katalogami usługi Azure AD nie jest również obsługiwane.
* Katalogi usługi Azure AD są według konstrukcji izolowanych. Nie można zmienić konfiguracji Azure AD Connect synchronizacji w celu odczytania danych z innego katalogu usługi Azure AD w celu utworzenia wspólnej i ujednoliconej tabeli zmian między katalogami. Nie jest również możliwe eksportowanie użytkowników jako kontaktów do innej lokalnej usługi AD przy użyciu synchronizacji Azure AD Connect.

> [!NOTE]
> Jeśli organizacja ogranicza komputery w sieci do łączenia się z Internetem, w tym artykule wymieniono punkty końcowe (nazwy FQDN, IPv4 i IPv6), które powinny zostać uwzględnione na liście dozwolonych wychodzących i zaufanych witryn programu Internet Explorer na komputerach klienckich, aby upewnić się, że komputery mogą pomyślnie korzystać z Microsoft 365. Aby uzyskać więcej informacji [, przeczytaj adresy URL i zakresy adresów IP pakietu Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiowanie strategii uwierzytelniania wieloskładnikowego
W tym zadaniu zdefiniujesz strategię uwierzytelniania wieloskładnikowego do użycia.  Usługa Azure AD Multi-Factor Authentication ma dwie różne wersje.  Jest to oparta na chmurze, a druga — oparta na serwerze usługi Azure MFA.  Na podstawie oceny, którą podano powyżej, można określić, które rozwiązanie jest odpowiednie dla danej strategii.  Skorzystaj z poniższej tabeli, aby określić, która opcja projektowania najlepiej spełnia wymagania w zakresie zabezpieczeń firmy:

Opcje projektu wieloskładnikowego:

| Zasób do zabezpieczenia | Usługa MFA w chmurze | Lokalna usługa MFA |
| --- | --- | --- |
| Aplikacje firmy Microsoft |tak |tak |
| Aplikacje SaaS w galerii aplikacji |tak |tak |
| Aplikacje usług IIS opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD |tak |tak |
| Aplikacje usług IIS, które nie są publikowane za pomocą serwera proxy aplikacja usługi Azure AD |nie |tak |
| Dostęp zdalny jako sieci VPN, RDG |nie |tak |

Mimo że można rozliczyć rozwiązanie dla danej strategii, nadal musisz użyć oceny z powyższych lokalizacji, w której znajdują się użytkownicy.  Może to spowodować zmianę rozwiązania.  Skorzystaj z poniższej tabeli, aby pomóc Ci w ustaleniu:

| Lokalizacja użytkownika | Preferowana opcja projektowania |
| --- | --- |
| Azure Active Directory |Wiele FactorAuthentication w chmurze |
| Usługa Azure AD i lokalna usługa AD przy użyciu federacji z usługami AD FS |Oba |
| Azure AD i lokalna usługa AD przy użyciu Azure AD Connect bez synchronizacji haseł |Oba |
| Usługa Azure AD i lokalna przy użyciu Azure AD Connect z synchronizacją haseł |Oba |
| Lokalna usługi AD |Serwer Multi-Factor Authentication |

> [!NOTE]
> Należy również upewnić się, że wybrana opcja projektu uwierzytelnianie wieloskładnikowe obsługuje funkcje wymagane dla Twojego projektu.  Aby uzyskać więcej informacji [, zapoznaj się z tematem Wybieranie rozwiązania do obsługi zabezpieczeń wieloskładnikowych](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Dostawca usługi MFA
Uwierzytelnianie wieloskładnikowe jest domyślnie dostępne dla administratorów globalnych, którzy mają dzierżawę Azure Active Directory. Jeśli jednak chcesz przełożyć uwierzytelnianie wieloskładnikowe na wszystkich użytkowników i/lub chcesz, aby administratorzy globalni mogli korzystać z funkcji, takich jak Portal zarządzania, niestandardowe pozdrowienia i raporty, musisz zakupić i skonfigurować dostawcę Multi-Factor Authentication.

> [!NOTE]
> Należy również upewnić się, że wybrana opcja projektu uwierzytelnianie wieloskładnikowe obsługuje funkcje wymagane dla Twojego projektu. 
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

