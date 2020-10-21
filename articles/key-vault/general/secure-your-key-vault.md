---
title: Bezpieczny dostęp do magazynu kluczy
description: Model dostępu dla Azure Key Vault, w tym Active Directory uwierzytelnianie i punkty końcowe zasobów.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: b04bd6975a2ba8824124c769e66da1e4ebe7534a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309936"
---
# <a name="secure-access-to-a-key-vault"></a>Bezpieczny dostęp do magazynu kluczy

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. Ponieważ te dane są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko na autoryzowane aplikacje i użytkowników. Ten artykuł zawiera omówienie modelu dostępu Key Vault. W tym artykule wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

Aby uzyskać więcej informacji na temat Key Vault, zobacz [Informacje o Azure Key Vault](overview.md); Aby uzyskać więcej informacji na temat tego, co może być przechowywane w magazynie kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md).

## <a name="access-model-overview"></a>Przegląd modelu dostępu

Dostęp do magazynu kluczy jest kontrolowany przez dwa interfejsy: **płaszczyzny zarządzania** i **płaszczyzny danych**. Płaszczyzny zarządzania to miejsce, w którym zarządza się Key Vault. Operacje na tej płaszczyźnie obejmują tworzenie i usuwanie magazynów kluczy, pobieranie Key Vault właściwości i aktualizowanie zasad dostępu. Płaszczyzna danych to miejsce, w którym można korzystać z danych przechowywanych w magazynie kluczy. Możesz dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Obie płaszczyzny używają [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) do uwierzytelniania. W przypadku autoryzacji płaszczyzna zarządzania używa [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) i płaszczyzny danych używa [zasad dostępu Key Vault](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) i [usługi Azure RBAC na potrzeby operacji Key Vault płaszczyzny danych (wersja zapoznawcza)](https://docs.microsoft.com/azure/key-vault/general/rbac-guide).

Aby uzyskać dostęp do magazynu kluczy w jednej z płaszczyzn, wszyscy wywołujący (Użytkownicy lub aplikacje) muszą mieć odpowiednie uwierzytelnianie i autoryzację. Uwierzytelnianie ustanawia tożsamość obiektu wywołującego. Autoryzacja określa, które operacje mogą zostać wykonane przez obiekt wywołujący. Uwierzytelnianie za pomocą Key Vault działa w połączeniu z usługą [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis), która jest odpowiedzialna za uwierzytelnianie tożsamości dowolnego danego **podmiotu zabezpieczeń**.

Podmiot zabezpieczeń to obiekt, który reprezentuje użytkownika, grupę, usługę lub aplikację, która żąda dostępu do zasobów platformy Azure. Platforma Azure przypisuje unikatowy **Identyfikator obiektu** do każdego podmiotu zabezpieczeń.

* Podmiot zabezpieczeń **użytkownika** identyfikuje osoby, które mają profil w Azure Active Directory.

* Podmiot zabezpieczeń **grupy** identyfikuje zestaw użytkowników utworzonych w Azure Active Directory. Wszystkie role lub uprawnienia przypisane do grupy są udzielane wszystkim użytkownikom należącym do grupy.

* Nazwa **główna usługi** jest typem podmiotu zabezpieczeń, który tożsamościuje aplikację lub usługę, czyli fragment kodu, a nie użytkownika lub grupy. Identyfikator obiektu jednostki usługi jest znany jako jego **Identyfikator klienta** i działa jak jego nazwa użytkownika. **Klucz tajny klienta** lub **certyfikat** jednostki usługi jest traktowany jak jego hasło. Wiele usług platformy Azure obsługuje przypisywanie [tożsamości zarządzanej](/azure/active-directory/managed-identities-azure-resources/overview) za pomocą zautomatyzowanego zarządzania **identyfikatorem klienta** i **certyfikatem**. Tożsamość zarządzana to najbezpieczniejsza i zalecana opcja uwierzytelniania na platformie Azure.

Aby uzyskać więcej informacji na temat uwierzytelniania do Key Vault, zobacz [uwierzytelnianie do Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Opcje uwierzytelniania Key Vault

Podczas tworzenia magazynu kluczy w ramach subskrypcji platformy Azure jest on automatycznie kojarzony z dzierżawą usługi Azure AD subskrypcji. Wszystkie obiekty wywołujące w obu płaszczyznach muszą rejestrować się w tej dzierżawie i uwierzytelniać się w celu uzyskania dostępu do magazynu kluczy. W obu przypadkach aplikacje mogą uzyskiwać dostęp do Key Vault na trzy sposoby:

- **Tylko aplikacja**: aplikacja reprezentuje jednostkę usługi lub zarządzaną tożsamość. Ta tożsamość jest najbardziej typowym scenariuszem dla aplikacji, które okresowo potrzebują do uzyskiwania dostępu do certyfikatów, kluczy lub wpisów tajnych z magazynu kluczy. Aby ten scenariusz działał, `objectId` aplikacja musi być określona w zasadach dostępu i `applicationId` _nie_ może być określona lub musi być `null` .
- **Tylko użytkownik**: użytkownik uzyskuje dostęp do magazynu kluczy z dowolnej aplikacji zarejestrowanej w dzierżawie. Przykłady tego typu dostępu obejmują Azure PowerShell i Azure Portal. Aby ten scenariusz działał, `objectId` użytkownik musi być określony w zasadach dostępu i `applicationId` _nie_ może być określony lub musi być `null` .
- **Aplikacja-Plus — użytkownik** (czasami określany jako _tożsamość złożona_): użytkownik jest zobowiązany do uzyskania dostępu do magazynu kluczy z określonej aplikacji _, a_ aplikacja musi używać przepływu uwierzytelniania w imieniu (OBO) do personifikacji użytkownika. Aby ten scenariusz działał, oba `applicationId` i `objectId` muszą być określone w zasadach dostępu. `applicationId`Identyfikuje wymaganą aplikację i `objectId` identyfikuje użytkownika. Obecnie ta opcja jest niedostępna w przypadku płaszczyzny danych Azure RBAC (wersja zapoznawcza).

W przypadku wszystkich typów dostępu aplikacja uwierzytelnia się za pomocą usługi Azure AD. Aplikacja używa dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-scenarios.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie, aby udzielić dostępu. Zasób jest punktem końcowym w obszarze zarządzania lub płaszczyzny danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do Key Vault. Aby dowiedzieć się więcej, zapoznaj się z [całym przepływem uwierzytelniania](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Model jednego mechanizmu uwierzytelniania w obu płaszczyznach ma kilka zalet:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich magazynów kluczy w organizacji.
- Jeśli użytkownik opuści te osoby, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji w usłudze Azure AD, takich jak włączenie uwierzytelniania wieloskładnikowego w celu zwiększenia bezpieczeństwa.

## <a name="resource-endpoints"></a>Punkty końcowe zasobów

Aplikacje uzyskują dostęp do płaszczyzn za pomocą punktów końcowych. Kontrole dostępu dla dwóch płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do korzystania z kluczy w magazynie kluczy, Udziel dostępu do płaszczyzny danych przy użyciu zasad dostępu Key Vault lub kontroli RBAC platformy Azure (wersja zapoznawcza). Aby udzielić użytkownikowi dostępu do odczytu do Key Vault właściwości i tagów, ale nie dostępu do danych (kluczy, wpisów tajnych lub certyfikatów), Udziel dostępu do płaszczyzny zarządzania za pomocą RBAC.

W poniższej tabeli przedstawiono punkty końcowe dla punktów zarządzania i płaszczyzny danych.

| &nbsp;Płaszczyzna dostępu | Punkty końcowe dostępu | Operacje | &nbsp;Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne**<br> management.azure.com:443<br><br> **Azure Chiny 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Administracja USA platformy Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> management.microsoftazure.de:443 | Tworzenie, odczytywanie, aktualizowanie i usuwanie magazynów kluczy<br><br>Ustawianie zasad dostępu Key Vault<br><br>Ustawianie tagów Key Vault | Kontrola dostępu na podstawie ról platformy Azure |
| Płaszczyzna danych | **Globalne**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Azure Chiny 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Administracja USA platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 | Klucze: szyfrowanie, odszyfrowywanie, wrapKey, unwrapKey, podpisywanie, weryfikowanie, pobieranie, wyświetlanie, tworzenie, aktualizowanie, importowanie, usuwanie, odzyskiwanie, tworzenie kopii zapasowej, przywracanie, przeczyszczanie<br><br> Certyfikaty: managecontacts, getemitencis, listissuers, setemitencis, deleteissuers, manageissuers, get, list, Create, import, Update, DELETE, Recover, Backup, Restore, przeczyszczanie<br><br>  Wpisy tajne: pobieranie, wyświetlanie, Ustawianie, usuwanie, odzyskiwanie, tworzenie kopii zapasowej, przywracanie, przeczyszczanie | Zasady dostępu Key Vault lub RBAC platformy Azure (wersja zapoznawcza)|

## <a name="management-plane-and-azure-rbac"></a>Płaszczyzna zarządzania i kontrola RBAC platformy Azure

W płaszczyźnie zarządzania korzystasz z [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) , aby autoryzować operacje, które mogą zostać wykonane przez obiekt wywołujący. W modelu RBAC platformy Azure Każda subskrypcja platformy Azure ma wystąpienie usługi Azure AD. Przyznasz użytkownikom, grupom i aplikacjom dostęp do tego katalogu. Dostęp jest udzielany do zarządzania zasobami w ramach subskrypcji platformy Azure, która używa modelu wdrażania Azure Resource Manager.

Utwórz magazyn kluczy w grupie zasobów i Zarządzaj dostępem za pomocą usługi Azure AD. Użytkownicy lub grupy mogą zarządzać magazynami kluczy w grupie zasobów. Przyznanie dostępu na określonym poziomie zakresu przez przypisanie odpowiednich ról platformy Azure. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać do użytkownika wstępnie zdefiniowaną rolę [współautor Key Vault](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor) w określonym zakresie. Do roli platformy Azure można przypisać następujące poziomy zakresów:

- **Subskrypcja**: rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów**: rola platformy Azure przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób**: dla danego zasobu jest stosowana rola platformy Azure przypisana do określonego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie spełnia Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md). 

Musisz mieć `Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` uprawnienia i uprawnień, takie jak [administrator dostępu użytkowników](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) lub [właściciel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

> [!IMPORTANT]
> Jeśli użytkownik ma `Contributor` uprawnienia do płaszczyzny zarządzania magazynu kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu Key Vault. Należy ściśle kontrolować, kto ma `Contributor` dostęp do roli do Twoich magazynów kluczy. Upewnij się, że tylko autoryzowani osoby mają dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz nimi zarządzać.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Zasady dostępu i płaszczyzny danych

Dostęp do płaszczyzny danych można udzielić przez ustawienie Key Vault zasad dostępu dla magazynu kluczy. Aby ustawić te zasady dostępu, użytkownik, Grupa lub aplikacja musi mieć `Key Vault Contributor` uprawnienia do płaszczyzny zarządzania dla tego magazynu kluczy.

Przyznanie użytkownikowi, grupie lub aplikacji dostępu do wykonywania określonych operacji dla kluczy lub wpisów tajnych w magazynie kluczy. Key Vault obsługuje wpisy zasad dostępu do 1 024 dla magazynu kluczy. Aby udzielić dostępu do płaszczyzny danych kilku użytkownikom, należy utworzyć grupę zabezpieczeń usługi Azure AD i dodać użytkowników do tej grupy.

Pełną listę operacji magazynu i wpisów tajnych można znaleźć tutaj: [informacje dotyczące operacji Key Vault](https://docs.microsoft.com/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault zasady dostępu udzielają uprawnień oddzielnie do kluczy, wpisów tajnych i certyfikatów.  Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów znajdują się na poziomie magazynu. 

Aby uzyskać więcej informacji o korzystaniu z zasad dostępu magazynu kluczy, zobacz [przypisywanie zasad dostępu Key Vault](assign-access-policy-portal.md)

> [!IMPORTANT]
> Zasady dostępu Key Vault są stosowane na poziomie magazynu. Gdy użytkownik uzyskuje uprawnienia do tworzenia i usuwania kluczy, mogą wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.
Zasady dostępu Key Vault nie obsługują szczegółowych uprawnień na poziomie obiektów, takich jak określony klucz, klucz tajny lub certyfikat. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Płaszczyzna danych i usługa Azure RBAC (wersja zapoznawcza)

Kontrola dostępu oparta na rolach na platformie Azure jest alternatywnym modelem uprawnień do kontrolowania dostępu do Azure Key Vault płaszczyzny danych, która może być włączona w poszczególnych magazynach kluczy. Model uprawnień RBAC platformy Azure ma charakter wyłączny, a po ustawieniu zasady dostępu do magazynu stały się nieaktywne. Azure Key Vault definiuje zestaw wbudowanych ról platformy Azure, które obejmują wspólne zestawy uprawnień używane do uzyskiwania dostępu do kluczy, wpisów tajnych lub certyfikatów.

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być objęty zakresem subskrypcji, grupy zasobów, magazynu kluczy lub pojedynczego klucza, klucza tajnego lub certyfikatu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Najważniejsze zalety korzystania z uprawnień usługi Azure RBAC w ramach zasad dostępu do magazynu to scentralizowane zarządzanie kontrolą dostępu i integrację z usługą [Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure). Privileged Identity Management zapewnia aktywację roli opartej na czasie i zatwierdzania, aby ograniczyć ryzyko nadmiernego, niepotrzebnego lub nieużywanego dostępu do zasobów, które Cię interesują.

Aby uzyskać więcej informacji na temat Key Vault płaszczyzny danych z RBAC, zobacz [Key Vault kluczy, certyfikatów i wpisów tajnych za pomocą kontroli dostępu opartej na rolach na platformie Azure (wersja zapoznawcza)](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Zapory i sieci wirtualne

Aby uzyskać dodatkową warstwę zabezpieczeń, można skonfigurować zapory i reguły sieci wirtualnej. Można skonfigurować zapory Key Vault i sieci wirtualne, aby odmówić dostępu do ruchu ze wszystkich sieci (łącznie z ruchem internetowym). Można udzielić dostępu do ruchu z określonych [sieci wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i publicznych zakresów adresów IP, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.

Poniżej przedstawiono kilka przykładów użycia punktów końcowych usługi:

* Używasz Key Vault do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikatów i chcesz zablokować dostęp do magazynu kluczy z publicznej sieci Internet.
* Chcesz zablokować dostęp do magazynu kluczy, tak aby tylko aplikacja lub krótka lista wystawianych hostów mogła połączyć się z magazynem kluczy.
* Aplikacja działa w sieci wirtualnej platformy Azure, a ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi nawiązać połączenie z usługą Key Vault, aby pobrać klucze tajne lub certyfikaty lub użyć kluczy kryptograficznych.

Aby uzyskać więcej informacji na temat zapory Key Vault i sieci wirtualnych, zobacz [konfigurowanie Azure Key Vault zapór i sieci wirtualnych](network-security.md)

> [!NOTE]
> Key Vault zapory i reguły sieci wirtualnej stosują się tylko do płaszczyzny danych Key Vault. Nie mają one wpływ na działania dotyczące płaszczyzny sterowania Key Vault (takie jak tworzenie, usuwanie i modyfikowanie operacji, Ustawianie zasad dostępu, Ustawianie zapór i reguł sieci wirtualnej).

## <a name="private-endpoint-connection"></a>Połączenie prywatnego punktu końcowego

W przypadku potrzeby całkowitego blokowania Key Vault narażenie na publiczną można użyć [prywatnego punktu końcowego platformy Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) . Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Typowe scenariusze korzystania z prywatnego linku dla usług platformy Azure:

- **Usługi dostępu prywatnego na platformie Azure**: łączenie sieci wirtualnej z usługami na platformie Azure bez publicznego adresu IP w źródle lub miejscu docelowym. Dostawcy usług mogą renderować swoje usługi w swoich własnych sieciach wirtualnych, a konsumenci mogą uzyskiwać dostęp do tych usług w lokalnej sieci wirtualnej. Platforma linków prywatnych będzie obsługiwała łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. 
 
- **Sieci lokalne i równorzędne**: usługi dostępu uruchomione na platformie Azure pochodzą z zasobów lokalnych za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, tuneli VPN i równorzędnych sieci wirtualnych za pomocą prywatnych punktów końcowych. Nie ma potrzeby konfigurowania publicznej komunikacji równorzędnej ani przechodzenia przez Internet w celu uzyskania dostępu do usługi. Link prywatny umożliwia bezpieczną metodę migrowania obciążeń do platformy Azure.
 
- **Ochrona przed wyciekiem danych**: prywatny punkt końcowy jest mapowany do wystąpienia zasobu PaaS zamiast całej usługi. Konsumenci mogą łączyć się tylko z określonym zasobem. Dostęp do dowolnego innego zasobu w usłudze jest zablokowany. Mechanizm ten zapewnia ochronę przed ryzykiem wycieku danych. 
 
- **Globalne zasięg**: Połącz się prywatnie z usługami działającymi w innych regionach. Sieć wirtualna konsumenta może znajdować się w regionie A i może łączyć się z usługami za linkiem prywatnym w regionie B.  
 
- Zapoznaj **się z własnymi usługami**: Włącz te same czynności i funkcje, aby umożliwić prywatną pracę usługi dla klientów na platformie Azure. Przez umieszczenie usługi za standardową Azure Load Balancer można ją włączyć dla linku prywatnego. Konsument może następnie połączyć się bezpośrednio z usługą przy użyciu prywatnego punktu końcowego we własnej sieci wirtualnej. Żądania połączenia można zarządzać przy użyciu przepływu wywołań zatwierdzenia. Połączenie prywatne platformy Azure działa dla klientów i usług należących do różnych dzierżawców Azure Active Directory. 

Aby uzyskać więcej informacji o prywatnych punktach końcowych, zobacz [Key Vault z linkiem prywatnym platformy Azure](https://docs.microsoft.com/azure/key-vault/general/private-link-service)

## <a name="example"></a>Przykład

W tym przykładzie tworzymy aplikację, która używa certyfikatu dla protokołów TLS/SSL, usługi Azure Storage do przechowywania danych oraz klucza RSA 2 048-bitowego do szyfrowania danych w usłudze Azure Storage. Nasza aplikacja działa na maszynie wirtualnej platformy Azure (lub w zestawie skalowania maszyn wirtualnych). Możemy użyć magazynu kluczy do przechowywania wpisów tajnych aplikacji. Firma Microsoft może przechowywać certyfikat Bootstrap używany przez aplikację do uwierzytelniania w usłudze Azure AD.

Potrzebujemy dostępu do następujących przechowywanych kluczy i wpisów tajnych:
- **Certyfikat TLS/SSL**: używany w przypadku protokołu TLS/SSL.
- **Klucz magazynu**: służy do uzyskiwania dostępu do konta magazynu.
- **Klucz RSA 2 048-bitowy**: służy do zawijania/odwinięcia klucza szyfrowania danych przez usługę Azure Storage.
- **Tożsamość zarządzana przez aplikację**: służy do uwierzytelniania w usłudze Azure AD. Po udzieleniu dostępu do Key Vault aplikacja może pobrać klucz magazynu i certyfikat.

Musimy zdefiniować następujące role, aby określić, kto może zarządzać aplikacją, wdrażać ją i przeprowadzać inspekcję:
- **Zespół ds. zabezpieczeń**: personel działu IT z biura firmy CSO (Dyrektor ds. zabezpieczeń) lub podobnych współautorów. Zespół ds. zabezpieczeń jest odpowiedzialny za odpowiednie przechowywanie wpisów tajnych. Wpisy tajne mogą obejmować certyfikaty protokołu TLS/SSL, klucze RSA do szyfrowania, parametry połączenia i klucze kont magazynu.
- **Deweloperzy i operatorzy**: pracownicy, którzy opracowują aplikację i wdrażają ją na platformie Azure. Członkowie tego zespołu nie są częścią personelu zabezpieczeń. Nie powinny mieć dostępu do poufnych danych, takich jak certyfikaty TLS/SSL i klucze RSA. Tylko wdrażana aplikacja powinna mieć dostęp do poufnych danych.
- **Audytorzy**: Ta rola jest dla współautorów, którzy nie są członkami rozwoju ani ogólnego personelu IT. Zapoznają się z użyciem i konserwacją certyfikatów, kluczy i wpisów tajnych w celu zapewnienia zgodności ze standardami zabezpieczeń.

Istnieje inna rola, która jest poza zakresem naszej aplikacji: Administrator subskrypcji (lub grupy zasobów). Administrator subskrypcji konfiguruje uprawnienia dostępu początkowego do zespołu ds. zabezpieczeń. Przyznają one dostęp do zespołu ds. zabezpieczeń przy użyciu grupy zasobów, która zawiera zasoby wymagane przez aplikację.

Musimy autoryzować następujące operacje dla naszych ról:

**Zespół ds. zabezpieczeń**
- Utwórz magazyny kluczy.
- Włącz rejestrowanie Key Vault.
- Dodaj klucze i wpisy tajne.
- Tworzenie kopii zapasowych kluczy na potrzeby odzyskiwania po awarii.
- Ustawianie zasad dostępu Key Vault i przypisywanie ról w celu udzielenia uprawnień użytkownikom i aplikacjom dla określonych operacji.
- Okresowe przywracanie kluczy i wpisów tajnych.

**Deweloperzy i operatorzy**
- Uzyskaj odwołania od zespołu ds. zabezpieczeń dla certyfikatów Bootstrap i TLS/SSL (odciski palców), klucza magazynu (Secret URI) i klucza RSA (identyfikator URI klucza) do zawijania/depakowania.
- Utwórz i Wdróż aplikację w celu programistycznego uzyskiwania dostępu do certyfikatów i wpisów tajnych.

**Audytorzy**
- Zapoznaj się z dziennikami Key Vault, aby potwierdzić odpowiednie użycie kluczy i wpisów tajnych oraz zapewnić zgodność ze standardami zabezpieczeń danych.

Poniższa tabela zawiera podsumowanie uprawnień dostępu dla naszych ról i aplikacji.

| Rola | Uprawnienia do płaszczyzny zarządzania | Uprawnienia płaszczyzny danych — zasady dostępu do magazynu | Uprawnienia płaszczyzny danych — Azure RBAC (wersja zapoznawcza)  |
| --- | --- | --- | --- |
| Zespół ds. zabezpieczeń | [Współautor Key Vault](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor) | Certyfikaty: wszystkie operacje <br> Klucze: wszystkie operacje <br> Wpisy tajne: wszystkie operacje | [Administrator Key Vault (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-administrator-preview) |
| Deweloperzy i &nbsp; operatorzy | Uprawnienie do wdrażania Key Vault<br><br> **Uwaga**: to uprawnienie umożliwia wdrożonym maszynom wirtualnym pobieranie wpisów tajnych z magazynu kluczy. | Brak | Brak |
| Audytorzy | Brak | Certyfikaty: Lista <br> Klucze: wyświetlanie<br>Wpisy tajne: wyświetlanie<br><br> **Uwaga**: to uprawnienie umożliwia audytorom inspekcję atrybutów (tagów, dat aktywacji, dat wygaśnięcia) dla kluczy i wpisów tajnych, które nie są emitowane w dziennikach. | [Key Vault Reader (wersja zapoznawcza)]https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-reader-preview |
| Konto usługi Azure Storage | Brak | Klucze: Get, list, wrapKey, unwrapKey <br> | [Key Vault szyfrowanie usługi kryptograficznej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-crypto-service-encryption-preview) |
| Aplikacja | Brak | Wpisy tajne: Pobierz, Wyświetl <br> Certyfikaty: Get, list | [Key Vault Reader (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-reader-preview), [Key Vault użytkownik tajny (wersja zapoznawcza)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-secrets-user-preview) |

Trzy role zespołu potrzebują dostępu do innych zasobów wraz z uprawnieniami Key Vault. Aby wdrożyć maszyny wirtualne (lub Web Apps funkcję Azure App Service), deweloperzy i operatorzy potrzebują dostępu do wdrożenia. Audytorzy muszą mieć dostęp do odczytu do konta magazynu, w którym są przechowywane dzienniki Key Vault.

Nasz przykład opisuje prosty scenariusz. Scenariusze życiowe mogą być bardziej skomplikowane. Możesz dostosować uprawnienia do magazynu kluczy w zależności od potrzeb. Zakładamy, że zespół ds. zabezpieczeń zawiera odwołania do kluczy i wpisów tajnych (identyfikatorów URI i odcisków palców), które są używane przez personel DevOps w swoich aplikacjach. Deweloperzy i operatorzy nie potrzebują dostępu do płaszczyzny danych. Firma Microsoft koncentruje się na sposobie zabezpieczania magazynu kluczy.

> [!NOTE]
> Ten przykład pokazuje, jak dostęp Key Vault jest blokowany w środowisku produkcyjnym. Deweloperzy powinni mieć własną subskrypcję lub grupę zasobów z pełnymi uprawnieniami do zarządzania swoimi magazynami, maszynami wirtualnymi i kontem magazynu, na którym opracowują aplikację.

## <a name="resources"></a>Zasoby

- [Informacje o usłudze Azure Key Vault](overview.md)
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Kontrola dostępu na podstawie ról platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Link prywatny](https://docs.microsoft.com/azure/private-link/private-link-overview)

## <a name="next-steps"></a>Następne kroki

[Uwierzytelnianie w usłudze Azure Key Vault](authentication.md)

[Przypisywanie zasad dostępu Key Vault](assign-access-policy-portal.md)

[Przypisywanie roli platformy Azure do uzyskiwania dostępu do kluczy, wpisów tajnych i certyfikatów](rbac-guide.md)

[Konfigurowanie zapór i sieci wirtualnych usługi Key Vault](network-security.md)

[Ustanów połączenie prywatne z Key Vault](private-link-service.md)
