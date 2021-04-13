---
title: Bezpieczny dostęp do magazynu kluczy
description: Model dostępu dla klientów Azure Key Vault, w tym uwierzytelnianie usługi Active Directory i punkty końcowe zasobów.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: b1ec89db7bc12ffbd21c6e302e065449eba3ac20
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366491"
---
# <a name="secure-access-to-a-key-vault"></a>Bezpieczny dostęp do magazynu kluczy

Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. Ponieważ te dane są poufne i krytyczne dla działalności firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko autoryzowanym aplikacjom i użytkownikom. Ten artykuł zawiera omówienie modelu Key Vault dostępu. Wyjaśniono w nim uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

Aby uzyskać więcej informacji na Key Vault, zobacz [Informacje o Azure Key Vault](overview.md); Aby uzyskać więcej informacji na temat tego, co można przechowywać w magazynie kluczy, zobacz [About keys, secrets, and certificates (Informacje o kluczach, wpisach tajnych i certyfikatach).](about-keys-secrets-certificates.md)

## <a name="access-model-overview"></a>Omówienie modelu dostępu

Dostęp do magazynu kluczy jest kontrolowany za pośrednictwem dwóch interfejsów: **płaszczyzny zarządzania** i **płaszczyzny danych**. Płaszczyzna zarządzania to miejsce, w którym zarządzasz Key Vault samodzielnie. Operacje na tej płaszczyźnie obejmują tworzenie i usuwanie magazynów kluczy, pobieranie Key Vault i aktualizowanie zasad dostępu. Płaszczyzna danych to miejsce, w którym pracujesz z danymi przechowywanymi w magazynie kluczy. Możesz dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Obie płaszczyzny używają [Azure Active Directory (Azure AD) do](../../active-directory/fundamentals/active-directory-whatis.md) uwierzytelniania. W celu autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach [(RBAC)](../../role-based-access-control/overview.md) platformy [Azure, a](./assign-access-policy-portal.md) płaszczyzna danych używa zasad dostępu usługi Key Vault i kontroli dostępu opartej na rolach platformy Azure Key Vault operacji płaszczyzny [danych.](./rbac-guide.md)

Aby uzyskać dostęp do magazynu kluczy na jednej z płaszczyzn, wszystkie wywołujące (użytkownicy lub aplikacje) muszą mieć odpowiednie uwierzytelnianie i autoryzację. Uwierzytelnianie określa tożsamość wywołującego. Autoryzacja określa, które operacje może wykonywać wywołujący. Uwierzytelnianie za Key Vault działa w połączeniu z [usługą Azure Active Directory (Azure AD),](../../active-directory/fundamentals/active-directory-whatis.md)która jest odpowiedzialna za uwierzytelnianie tożsamości dowolnego **podmiotu zabezpieczeń.**

Podmiot zabezpieczeń to obiekt, który reprezentuje użytkownika, grupę, usługę lub aplikację, która żąda dostępu do zasobów platformy Azure. Platforma Azure przypisuje unikatowy **identyfikator obiektu do** każdego podmiotu zabezpieczeń.

* Podmiot **zabezpieczeń** użytkownika identyfikuje osobę, która ma profil w Azure Active Directory.

* Podmiot **zabezpieczeń** grupy identyfikuje zestaw użytkowników utworzonych w Azure Active Directory. Wszystkie role lub uprawnienia przypisane do grupy są przyznawane wszystkim użytkownikom w grupie.

* Nazwa **główna usługi** to typ podmiotu zabezpieczeń, który identyfikuje aplikację lub usługę, czyli fragment kodu, a nie użytkownika lub grupę. Identyfikator obiektu jednostki usługi jest znany jako **identyfikator** klienta i działa jak nazwa użytkownika. Klucz tajny lub **certyfikat** klienta jednostki usługi **działa** jak jego hasło. Wiele usług platformy Azure obsługuje przypisywanie tożsamości [zarządzanej za pomocą](../../active-directory/managed-identities-azure-resources/overview.md) zautomatyzowanego zarządzania **identyfikatorem klienta** i **certyfikatem**. Tożsamość zarządzana jest najbezpiecznszą i zalecaną opcją uwierzytelniania na platformie Azure.

Aby uzyskać więcej informacji na temat uwierzytelniania w Key Vault, zobacz [Authenticate to Azure Key Vault](authentication.md) (Uwierzytelnianie w Azure Key Vault

## <a name="key-vault-authentication-options"></a>Key Vault opcje uwierzytelniania

Gdy tworzysz magazyn kluczy w subskrypcji platformy Azure, jest on automatycznie kojarzony z dzierżawą usługi Azure AD subskrypcji. Wszystkie wywołujące na obu płaszczyznach muszą zarejestrować się w tej dzierżawie i uwierzytelnić się, aby uzyskać dostęp do magazynu kluczy. W obu przypadkach aplikacje mogą uzyskać dostęp Key Vault na trzy sposoby:

- **Tylko aplikacja:** aplikacja reprezentuje jednostkę usługi lub tożsamość zarządzaną. Ta tożsamość jest najbardziej powszechnym scenariuszem dla aplikacji, które okresowo muszą uzyskać dostęp do certyfikatów, kluczy lub wpisów tajnych z magazynu kluczy. Aby ten scenariusz działał, wartość aplikacji musi być określona w zasadach dostępu, a wartość nie może być `objectId` `applicationId` określona ani musi mieć wartość  `null` .
- **Tylko dla użytkownika:** użytkownik uzyskuje dostęp do magazynu kluczy z dowolnej aplikacji zarejestrowanej w dzierżawie. Przykłady tego typu dostępu obejmują Azure PowerShell i Azure Portal. Aby ten scenariusz działał, użytkownik musi być określony w zasadach dostępu, a identyfikator nie może być `objectId` określony lub musi mieć wartość `applicationId`  `null` .
- **Użytkownik -plus-user** (czasami określany jako tożsamość złożona): użytkownik musi uzyskać  dostęp do magazynu kluczy z określonej aplikacji, a aplikacja musi użyć przepływu uwierzytelniania "w imieniu" (OBO) w celu personifikacji użytkownika.  Aby ten scenariusz działał, zarówno , `applicationId` jak i muszą zostać określone w zasadach `objectId` dostępu. Identyfikator `applicationId` identyfikuje wymaganą aplikację, a identyfikator `objectId` identyfikuje użytkownika. Obecnie ta opcja nie jest dostępna dla platformy Azure RBAC płaszczyzny danych.

We wszystkich typach dostępu aplikacja uwierzytelnia się w usłudze Azure AD. Aplikacja używa dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-vs-authorization.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie w celu udzielenia dostępu. Zasób jest punktem końcowym na płaszczyźnie zarządzania lub danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do Key Vault. Aby dowiedzieć się więcej, przejrzyj [cały przepływ uwierzytelniania.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Model pojedynczego mechanizmu uwierzytelniania w obu płaszczyznach ma kilka zalet:

- Organizacje mogą centralnie kontrolować dostęp do wszystkich magazynów kluczy w swojej organizacji.
- Jeśli użytkownik odchodzi, natychmiast traci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji w usłudze Azure AD, takich jak włączanie uwierzytelniania wieloskładnikowego w celu dodania zabezpieczeń.

## <a name="resource-endpoints"></a>Punkty końcowe zasobów

Aplikacje mają dostęp do płaszczyzn za pośrednictwem punktów końcowych. Mechanizmy kontroli dostępu dla dwóch płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do używania kluczy w magazynie kluczy, należy udzielić dostępu do płaszczyzny danych przy użyciu zasad Key Vault dostępu lub kontroli dostępu na platformie Azure. Aby udzielić użytkownikowi dostępu do odczytu do Key Vault i tagów, ale nie do danych (kluczy, wpisów tajnych lub certyfikatów), należy udzielić dostępu do płaszczyzny zarządzania przy użyciu kontroli dostępu na RBAC platformy Azure.

W poniższej tabeli przedstawiono punkty końcowe płaszczyzn zarządzania i danych.

| Płaszczyzna &nbsp; dostępu | Punkty końcowe dostępu | Operacje | Mechanizm &nbsp; kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne:**<br> management.azure.com:443<br><br> **Azure (Chiny) — 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> management.microsoftazure.de:443 | Tworzenie, odczytywanie, aktualizowanie i usuwanie magazynów kluczy<br><br>Ustawianie Key Vault dostępu<br><br>Ustawianie Key Vault tagów | Kontrola dostępu na podstawie ról platformy Azure |
| Płaszczyzna danych | **Globalne:**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Azure (Chiny) — 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 | Klucze: szyfruj, odszyfruj, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Certyfikaty: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Wpisy tajne: get, list, set, delete,recover, backup, restore, purge | Key Vault dostępu lub RBAC platformy Azure |

## <a name="management-plane-and-azure-rbac"></a>Płaszczyzna zarządzania i RBAC platformy Azure

Na płaszczyźnie zarządzania korzystasz z kontroli dostępu opartej na rolach [(RBAC)](../../role-based-access-control/overview.md) platformy Azure, aby autoryzować operacje, które może wykonywać wywołujący. W modelu RBAC platformy Azure każda subskrypcja platformy Azure ma wystąpienie usługi Azure AD. Dostęp z tego katalogu można przyznać użytkownikom, grupom i aplikacjom. Dostęp jest udzielany w celu zarządzania zasobami w subskrypcji platformy Azure, które korzystają Azure Resource Manager modelu wdrażania.

Magazyn kluczy można utworzyć w grupie zasobów i zarządzać dostępem przy użyciu usługi Azure AD. Użytkownikom lub grupom można przyznać możliwość zarządzania magazynami kluczy w grupie zasobów. Dostęp można przyznać na określonym poziomie zakresu, przypisując odpowiednie role platformy Azure. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać użytkownikowi wstępnie Key Vault [rolę współautora](../../role-based-access-control/built-in-roles.md#key-vault-contributor) w określonym zakresie. Do roli platformy Azure można przypisać następujące poziomy zakresów:

- **Subskrypcja:** Rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów:** rola platformy Azure przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób:** rola platformy Azure przypisana dla określonego zasobu ma zastosowanie do tego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie pasuje do Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md). 

Musisz mieć uprawnienia `Microsoft.Authorization/roleAssignments/write` i , takie jak Administrator dostępu `Microsoft.Authorization/roleAssignments/delete` [użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) lub [Właściciel](../../role-based-access-control/built-in-roles.md#owner)

> [!IMPORTANT]
> Jeśli użytkownik ma uprawnienia do płaszczyzny zarządzania magazynu kluczy, może udzielić sobie dostępu do płaszczyzny danych, ustawiając Key Vault `Contributor` dostępu. Należy ściśle kontrolować, kto `Contributor` ma dostęp roli do magazynów kluczy. Upewnij się, że tylko autoryzowane osoby mogą uzyskać dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Płaszczyzna danych i zasady dostępu

Dostęp do płaszczyzny danych można udzielić, ustawiając Key Vault dostępu dla magazynu kluczy. Aby ustawić te zasady dostępu, użytkownik, grupa lub aplikacja musi mieć uprawnienia do płaszczyzny zarządzania `Key Vault Contributor` dla tego magazynu kluczy.

Udzielasz użytkownikowi, grupie lub aplikacji dostępu w celu wykonywania określonych operacji na kluczach lub wpisach tajnych w magazynie kluczy. Key Vault obsługuje maksymalnie 1024 wpisy zasad dostępu dla magazynu kluczy. Aby udzielić dostępu do płaszczyzny danych kilku użytkownikom, utwórz grupę zabezpieczeń usługi Azure AD i dodaj użytkowników do tej grupy.

Pełną listę operacji magazynu i kluczy tajnych można znaleźć tutaj: [Key Vault Operation Reference](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault dostępu przyznają uprawnienia oddzielnie do kluczy, wpisów tajnych i certyfikatów.  Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów są na poziomie magazynu. 

Aby uzyskać więcej informacji na temat używania zasad dostępu magazynu kluczy, zobacz [Przypisywanie Key Vault dostępu](assign-access-policy-portal.md)

> [!IMPORTANT]
> Key Vault dostępu są stosowane na poziomie magazynu. Gdy użytkownik ma uprawnienia do tworzenia i usuwania kluczy, może wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.
Key Vault dostępu nie obsługują szczegółowych uprawnień na poziomie obiektu, takich jak określony klucz, klucz tajny lub certyfikat. 
>

## <a name="data-plane-and-azure-rbac"></a>Płaszczyzna danych i RBAC platformy Azure

Kontrola dostępu oparta na rolach na platformie Azure to alternatywny model uprawnień do kontrolowania dostępu do Azure Key Vault danych, który można włączyć w poszczególnych magazynach kluczy. Model uprawnień RBAC platformy Azure jest wyłączny i po jego skonfigurowaniu zasady dostępu do magazynu stały się nieaktywne. Azure Key Vault definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do kluczy, wpisów tajnych lub certyfikatów.

Gdy rola platformy Azure jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Zakres dostępu może być ograniczony do poziomu subskrypcji, grupy zasobów, magazynu kluczy lub pojedynczego klucza, klucza tajnego lub certyfikatu. Podmiotem zabezpieczeń usługi Azure AD może być użytkownik, grupa, nazwa główna usługi aplikacji lub tożsamość [zarządzana dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Kluczowymi zaletami korzystania z uprawnień RBAC platformy Azure nad zasadami dostępu do magazynu są scentralizowane zarządzanie kontrolą dostępu i ich integracja [z usługą Privileged Identity Management (PIM).](../../active-directory/privileged-identity-management/pim-configure.md) Privileged Identity Management zapewnia aktywację roli opartą na czasie i zatwierdzaniu, aby ograniczyć ryzyko związane z nadmiernymi, niepotrzebnymi lub niewłaściwie nieprawidłowymi uprawnieniami dostępu do ludzkich zasobów.

Aby uzyskać więcej informacji na Key Vault danych przy użyciu kontroli dostępu opartej na rolach platformy Azure, zobacz [Key Vault kluczy,](rbac-guide.md) certyfikatów i wpisów tajnych przy użyciu kontroli dostępu opartej na rolach platformy Azure

## <a name="firewalls-and-virtual-networks"></a>Zapory i sieci wirtualne

Aby uzyskać dodatkową warstwę zabezpieczeń, można skonfigurować zapory i reguły sieci wirtualnej. Możesz skonfigurować zapory Key Vault wirtualne, aby domyślnie odmawiać dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego). Możesz udzielić dostępu do ruchu z określonych sieci wirtualnych platformy [Azure](../../virtual-network/virtual-networks-overview.md) i zakresów publicznych internetowych adresów IP, co pozwoli utworzyć bezpieczną granicę sieci dla aplikacji.

Oto kilka przykładów użycia punktów końcowych usługi:

* Używasz usługi Key Vault do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikatów, a chcesz zablokować dostęp do magazynu kluczy z publicznego Internetu.
* Chcesz zablokować dostęp do magazynu kluczy, aby tylko aplikacja lub krótka lista wyznaczonych hostów umożliwiała łączenie się z magazynem kluczy.
* Twoja aplikacja działa w sieci wirtualnej platformy Azure i ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi nawiązać połączenie z Key Vault, aby pobrać wpisy tajne lub certyfikaty, lub użyć kluczy kryptograficznych.

Aby uzyskać więcej informacji na temat Key Vault sieci wirtualnych i zapory sieci wirtualnych, zobacz [Azure Key Vault zapory i sieci wirtualne](network-security.md)

> [!NOTE]
> Key Vault i reguły sieci wirtualnej mają zastosowanie tylko do płaszczyzny danych Key Vault. Key Vault operacji płaszczyzny sterowania (takich jak tworzenie, usuwanie i modyfikowanie operacji, ustawianie zasad dostępu, ustawianie zapór i reguł sieci wirtualnej) nie mają wpływu na zapory i reguły sieci wirtualnej.

## <a name="private-endpoint-connection"></a>Połączenie z prywatnym punktem końcowym

W przypadku konieczności całkowitego zablokowania Key Vault publicznego można użyć prywatnego punktu końcowego platformy [Azure.](../../private-link/private-endpoint-overview.md) Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być przekierowyny przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Możesz połączyć się z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Typowe scenariusze korzystania z Private Link dla usług platformy Azure:

- **Prywatny dostęp do usług na** platformie Azure: połącz sieć wirtualną z usługami na platformie Azure bez publicznego adresu IP w miejscu źródłowym lub docelowym. Dostawcy usług mogą renderować swoje usługi we własnej sieci wirtualnej, a konsumenci mogą uzyskać dostęp do tych usług w ich lokalnej sieci wirtualnej. Platforma Private Link będzie obsługiwać łączność między konsumentem a usługami za pośrednictwem sieci szkieletowej platformy Azure. 
 
- **Sieci lokalne i** równorzędne: dostęp do usług działających na platformie Azure ze środowiska lokalnego za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, tuneli VPN i równorzędnych sieci wirtualnych przy użyciu prywatnych punktów końcowych. Nie ma potrzeby konfigurowania publicznej komunikacji równorzędnej ani przechodzenia przez Internet w celu połączenia z usługą. Private Link zapewnia bezpieczny sposób migrowania obciążeń na platformę Azure.
 
- **Ochrona przed wyciekiem danych:** prywatny punkt końcowy jest mapowany na wystąpienie zasobu PaaS zamiast całej usługi. Użytkownicy mogą łączyć się tylko z określonym zasobem. Dostęp do dowolnego innego zasobu w usłudze jest zablokowany. Ten mechanizm zapewnia ochronę przed zagrożeniami wycieku danych. 
 
- **Zasięg globalny:** prywatne połączenie z usługami uruchomionymi w innych regionach. Sieć wirtualna konsumenta może być w regionie A i może łączyć się z usługami Private Link regionie B.  
 
- **Rozszerzanie na własne usługi:** włącz to samo środowisko i funkcje, aby prywatnie renderować usługę dla użytkowników na platformie Azure. Umieszczając usługę za standardowym Azure Load Balancer, możesz ją włączyć dla Private Link. Następnie konsument może połączyć się bezpośrednio z usługą przy użyciu prywatnego punktu końcowego we własnej sieci wirtualnej. Żądaniami połączeń można zarządzać przy użyciu przepływu wywołań zatwierdzenia. Azure Private Link działa w przypadku użytkowników i usług należących do różnych Azure Active Directory dzierżaw. 

Aby uzyskać więcej informacji na temat prywatnych punktów końcowych, [zobacz Key Vault with Azure Private Link](./private-link-service.md)

## <a name="example"></a>Przykład

W tym przykładzie opracowujemy aplikację, która używa certyfikatu dla protokołu TLS/SSL, usługi Azure Storage do przechowywania danych oraz 2048-bitowego klucza RSA do szyfrowania danych w usłudze Azure Storage. Nasza aplikacja działa na maszynie wirtualnej platformy Azure (lub w zestawie skalowania maszyn wirtualnych). Do przechowywania wpisów tajnych aplikacji możemy użyć magazynu kluczy. Możemy przechowywać certyfikat ładowania początkowego, który jest używany przez aplikację do uwierzytelniania w usłudze Azure AD.

Potrzebujemy dostępu do następujących przechowywanych kluczy i wpisów tajnych:
- **Certyfikat TLS/SSL:** używany w przypadku protokołu TLS/SSL.
- **Klucz magazynu:** służy do uzyskiwania dostępu do konta magazynu.
- **2048-bitowy klucz RSA:** używany do opakowywania/odpakowywania klucza szyfrowania danych przez usługę Azure Storage.
- **Tożsamość zarządzana aplikacji:** służy do uwierzytelniania w usłudze Azure AD. Po przyznaniu Key Vault dostępu do magazynu aplikacja może pobrać klucz magazynu i certyfikat.

Należy zdefiniować następujące role, aby określić, kto może zarządzać naszą aplikacją, wdrażać ją i inspekcjić:
- **Zespół ds.** zabezpieczeń: pracownicy IT z biura CSO (dyrektor ds. zabezpieczeń) lub podobni współautorzy. Zespół ds. zabezpieczeń jest odpowiedzialny za właściwe zabezpieczenie wpisów tajnych. Wpisy tajne mogą obejmować certyfikaty TLS/SSL, klucze RSA do szyfrowania, parametry połączenia i klucze konta magazynu.
- **Deweloperzy i operatorzy:** pracownicy, którzy opracowują aplikację i wdrażają ją na platformie Azure. Członkowie tego zespołu nie są częścią personelu ds. zabezpieczeń. Nie powinny mieć dostępu do poufnych danych, takich jak certyfikaty TLS/SSL i klucze RSA. Tylko wdrażana przez nich aplikacja powinna mieć dostęp do poufnych danych.
- **Audytorzy:** Ta rola jest dla współautorów, którzy nie są członkami zespołu programowego ani ogólnego personelu IT. Przeglądają użycie i konserwację certyfikatów, kluczy i wpisów tajnych w celu zapewnienia zgodności ze standardami zabezpieczeń.

Istnieje inna rola, która znajduje się poza zakresem naszej aplikacji: administrator subskrypcji (lub grupy zasobów). Administrator subskrypcji konfiguruje początkowe uprawnienia dostępu dla zespołu ds. zabezpieczeń. Przyznają dostęp zespołowi ds. zabezpieczeń przy użyciu grupy zasobów, która ma zasoby wymagane przez aplikację.

Musimy autoryzować następujące operacje dla naszych ról:

**Zespół ds. zabezpieczeń**
- Tworzenie magazynów kluczy.
- Włącz rejestrowanie Key Vault rejestrowania.
- Dodawanie kluczy i wpisów tajnych.
- Tworzenie kopii zapasowych kluczy na wypadek odzyskiwania po awarii.
- Ustaw Key Vault dostępu i przypisz role, aby przyznać uprawnienia użytkownikom i aplikacjom dla określonych operacji.
- Okresowo wycofuj klucze i wpisy tajne.

**Deweloperzy i operatorzy**
- Pobierz od zespołu ds. zabezpieczeń odwołania do certyfikatów bootstrap i TLS/SSL (odciski palca), klucza magazynu (tajnego URI) i klucza RSA (URI klucza) do opakowywania/odpakowywania.
- Opracowywanie i wdrażanie aplikacji w celu programowego uzyskiwania dostępu do certyfikatów i wpisów tajnych.

**Audytorzy**
- Przejrzyj dzienniki Key Vault, aby potwierdzić prawidłowe użycie kluczy i wpisów tajnych oraz zgodność ze standardami zabezpieczeń danych.

W poniższej tabeli podsumowano uprawnienia dostępu do ról i aplikacji.

| Rola | Uprawnienia do płaszczyzny zarządzania | Uprawnienia płaszczyzny danych — zasady dostępu do magazynu | Uprawnienia płaszczyzny danych — RBAC platformy Azure  |
| --- | --- | --- | --- |
| Zespół ds. zabezpieczeń | [Key Vault współautora](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certyfikaty: wszystkie operacje <br> Klucze: wszystkie operacje <br> Wpisy tajne: wszystkie operacje | [Key Vault administratora](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Deweloperzy i &nbsp; operatorzy | Key Vault wdrażania<br><br> **Uwaga:** to uprawnienie umożliwia wdrożonym maszynom wirtualnych pobieranie wpisów tajnych z magazynu kluczy. | Brak | Brak |
| Audytorzy | Brak | Certyfikaty: lista <br> Klucze: wyświetlanie<br>Wpisy tajne: wyświetlanie<br><br> **Uwaga:** to uprawnienie umożliwia audytorom sprawdzanie atrybutów (tagów, dat aktywacji, dat wygaśnięcia) pod uwagę kluczy i wpisów tajnych, które nie są emitowane w dziennikach. | [Key Vault czytelnika](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Konto usługi Azure Storage | Brak | Klucze: get, list, wrapKey, unwrapKey <br> | [Key Vault szyfrowania usługi kryptograficznych](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Aplikacja | Brak | Wpisy tajne: get, list <br> Certyfikaty: pobierz, lista | [Key Vault Reader](../../role-based-access-control/built-in-roles.md#key-vault-reader), [Key Vault Secret User](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) |

Te trzy role zespołu muszą mieć dostęp do innych zasobów wraz z Key Vault uprawnieniami. Aby wdrożyć maszyny wirtualne (lub Web Apps funkcji Azure App Service), deweloperzy i operatorzy muszą wdrożyć dostęp. Audytorzy muszą mieć dostęp do odczytu do konta magazynu, na którym są Key Vault przechowywane dzienniki.

W naszym przykładzie opisano prosty scenariusz. Rzeczywiste scenariusze mogą być bardziej złożone. Możesz dostosować uprawnienia do magazynu kluczy w zależności od potrzeb. Przyjęto założenie, że zespół ds. zabezpieczeń udostępnia odwołania do kluczy i kluczy tajnych ( URI i odciski palca), które są używane przez personel DevOps w swoich aplikacjach. Deweloperzy i operatorzy nie wymagają dostępu do płaszczyzny danych. Skoncentrowaliśmy się na zabezpieczonym magazynie kluczy.

> [!NOTE]
> W tym przykładzie pokazano, Key Vault dostęp jest zablokowany w środowisku produkcyjnym. Deweloperzy powinni mieć własną subskrypcję lub grupę zasobów z pełnymi uprawnieniami do zarządzania magazynami, maszynami wirtualnych i kontem magazynu, na którym opracowują aplikację.

## <a name="resources"></a>Zasoby

- [Informacje o usłudze Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Kontrola dostępu na podstawie ról platformy Azure](../../role-based-access-control/overview.md)
- [Link prywatny](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Następne kroki

[Uwierzytelnianie w usłudze Azure Key Vault](authentication.md)

[Przypisywanie zasad Key Vault dostępu](assign-access-policy-portal.md)

[Przypisywanie roli platformy Azure w celu uzyskania dostępu do kluczy, wpisów tajnych i certyfikatów](rbac-guide.md)

[Konfigurowanie zapór i sieci wirtualnych usługi Key Vault](network-security.md)

[Nawiązywanie połączenia z połączeniem prywatnym z Key Vault](private-link-service.md)
