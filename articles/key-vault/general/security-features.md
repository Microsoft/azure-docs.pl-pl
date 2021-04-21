---
title: Azure Key Vault omówienie zabezpieczeń
description: Omówienie funkcji zabezpieczeń i najlepszych rozwiązań dla Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: fe88933049ad39de57f879789e8c1b86ed7a54f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819453"
---
# <a name="azure-key-vault-security"></a>Zabezpieczenia usługi Azure Key Vault

Azure Key Vault chroni klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączenia i hasła) w chmurze. Jednak w przypadku przechowywania poufnych i krytycznych dla działania firmy danych należy podjąć kroki w celu zmaksymalizowania bezpieczeństwa magazynów i przechowywanych w nich danych.

Ten artykuł zawiera omówienie funkcji zabezpieczeń i najlepszych rozwiązań dla Azure Key Vault.

> [!NOTE]
> Aby uzyskać pełną listę zaleceń dotyczących Azure Key Vault zabezpieczeń, zobacz [Punkt odniesienia zabezpieczeń dla Azure Key Vault.](security-baseline.md)

## <a name="network-security"></a>Bezpieczeństwo sieci

Możesz zmniejszyć narażenie magazynów, określając, które adresy IP mają do nich dostęp. Punkty końcowe usługi dla sieci wirtualnej Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów IPv4 (protokół internetowy w wersji 4). Każdy użytkownik łączący się z magazynem kluczy spoza tych źródeł ma odmówiony dostępu.  Aby uzyskać szczegółowe informacje, zobacz [Virtual network service endpoints for Azure Key Vault (Punkty](overview-vnet-service-endpoints.md) końcowe usługi dla sieci wirtualnej dla Azure Key Vault

Gdy reguły zapory obowiązują, użytkownicy mogą odczytywać dane z usługi Key Vault tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu Key Vault z Azure Portal. Mimo że użytkownicy mogą przejść do magazynu kluczy z witryny Azure Portal, mogą nie być w stanie wyświetlić listy kluczy, wpisów tajnych lub certyfikatów, jeśli ich komputer kliencki nie znajduje się na liście dozwolonych. Aby uzyskać informacje o krokach [implementacji, zobacz Azure Key Vault zapory i sieci wirtualne](network-security.md)

Azure Private Link Service umożliwia dostęp do usług Azure Key Vault i usług klienta/partnera hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być przekierowyny przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Możesz połączyć się z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.  Aby uzyskać instrukcje implementacji, [zobacz Integrowanie Key Vault z Azure Private Link](private-link-service.md)

## <a name="tls-and-https"></a>Protokoły TLS i HTTPS

- Front Key Vault (płaszczyzna danych) to serwer wielodostępny. Oznacza to, że magazyny kluczy od różnych klientów mogą korzystać z tego samego publicznego adresu IP. W celu osiągnięcia izolacji każde żądanie HTTP jest uwierzytelniane i autoryzowane niezależnie od innych żądań.
- Możesz zidentyfikować starsze wersje protokołu TLS, aby zgłaszać luki w zabezpieczeniach, ale ponieważ publiczny adres IP jest udostępniony, nie jest możliwe, aby zespół usługi key vault wyłączył stare wersje protokołu TLS dla poszczególnych magazynów kluczy na poziomie transportu.
- Protokół HTTPS umożliwia klientowi uczestnictwo w negocjacji protokołu TLS. **Klienci mogą wymuszać najnowszą wersję** zabezpieczeń TLS i za każdym razem, gdy klient to robi, całe połączenie będzie korzystać z odpowiedniej ochrony na poziomie. Fakt, że Key Vault nadal obsługuje starsze wersje TLS, nie będzie negatywnie wpływać na bezpieczeństwo połączeń przy użyciu nowszej wersji TLS.
- Pomimo znanych luk w zabezpieczeniach protokołu TLS nie ma znanego ataku, który umożliwiałby złośliwemu agentowi wyodrębnianie dowolnych informacji z magazynu kluczy, gdy osoba atakująca zainicjuje połączenie z wersją protokołu TLS, która ma luki w zabezpieczeniach. Osoba atakująca nadal musi uwierzytelniać się i autoryzować, a jeśli legalni klienci zawsze łączą się z najnowszymi wersjami TLS, nie istnieje sposób, aby poświadczenia mogły zostać ujawnione z luk w zabezpieczeniach w starych wersjach TLS.

## <a name="identity-management"></a>Zarządzanie tożsamościami

Gdy tworzysz magazyn kluczy w subskrypcji platformy Azure, jest on automatycznie kojarzony z dzierżawą usługi Azure AD subskrypcji. Każda osoba próbująca zarządzać zawartością lub pobierać ją z magazynu musi być uwierzytelniona przez usługę Azure AD. W obu przypadkach aplikacje mogą uzyskać dostęp do Key Vault na trzy sposoby:

- **Tylko aplikacja:** aplikacja reprezentuje jednostkę usługi lub tożsamość zarządzaną. Ta tożsamość jest najbardziej powszechnym scenariuszem dla aplikacji, które okresowo muszą uzyskać dostęp do certyfikatów, kluczy lub wpisów tajnych z magazynu kluczy. Aby ten scenariusz działał, wartość aplikacji musi być określona w zasadach dostępu, a wartość nie może być `objectId` `applicationId` określona ani musi mieć wartość  `null` .
- **Tylko dla użytkownika:** użytkownik uzyskuje dostęp do magazynu kluczy z dowolnej aplikacji zarejestrowanej w dzierżawie. Przykłady tego typu dostępu obejmują Azure PowerShell i Azure Portal. Aby ten scenariusz działał, użytkownik musi być określony w zasadach dostępu, a identyfikator nie może być `objectId` określony lub musi mieć wartość `applicationId`  `null` .
- **Użytkownik aplikacji plus** (czasami określany jako tożsamość złożona): użytkownik musi uzyskać dostęp  do magazynu kluczy z określonej aplikacji, a aplikacja musi użyć przepływu uwierzytelniania w imieniu (OBO) w celu personifikacji użytkownika.  Aby ten scenariusz działał, należy określić zarówno i , `applicationId` jak i w zasadach `objectId` dostępu. Identyfikuje `applicationId` wymaganą aplikację, a identyfikator `objectId` identyfikuje użytkownika. Obecnie ta opcja nie jest dostępna dla płaszczyzny danych RBAC platformy Azure.

We wszystkich typach dostępu aplikacja uwierzytelnia się w usłudze Azure AD. Aplikacja używa dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-vs-authorization.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie w celu udzielenia dostępu. Zasób jest punktem końcowym na płaszczyźnie zarządzania lub danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do Key Vault. Aby dowiedzieć się więcej, przejrzyj [cały przepływ uwierzytelniania.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Aby uzyskać szczegółowe informacje, [zobacz Key Vault Authentication Fundamentals (Podstawy uwierzytelniania przy użyciu usługi Key Vault).](authentication-fundamentals.md)

## <a name="key-vault-authentication-options"></a>Key Vault opcje uwierzytelniania

Gdy tworzysz magazyn kluczy w subskrypcji platformy Azure, jest on automatycznie kojarzony z dzierżawą usługi Azure AD subskrypcji. Wszystkie wywołujące w obu płaszczyznach muszą zarejestrować się w tej dzierżawie i uwierzytelnić się, aby uzyskać dostęp do magazynu kluczy. W obu przypadkach aplikacje mogą uzyskać dostęp do Key Vault na trzy sposoby:

- **Tylko aplikacja:** aplikacja reprezentuje jednostkę usługi lub tożsamość zarządzaną. Ta tożsamość jest najbardziej powszechnym scenariuszem dla aplikacji, które okresowo muszą uzyskać dostęp do certyfikatów, kluczy lub wpisów tajnych z magazynu kluczy. Aby ten scenariusz działał, program aplikacji musi być określony w zasadach dostępu, a wartość nie może być `objectId` `applicationId` określona ani musi mieć wartość  `null` .
- **Tylko użytkownik:** użytkownik uzyskuje dostęp do magazynu kluczy z dowolnej aplikacji zarejestrowanej w dzierżawie. Przykładami tego typu dostępu są Azure PowerShell i Azure Portal. Aby ten scenariusz działał, użytkownik musi być określony w zasadach dostępu, a identyfikator nie może być `objectId` określony lub musi mieć wartość `applicationId`  `null` .
- **Użytkownik -plus-user** (czasami określany jako tożsamość złożona): użytkownik musi uzyskać  dostęp do magazynu kluczy z określonej aplikacji, a aplikacja musi użyć przepływu uwierzytelniania "w imieniu" (OBO) w celu personifikacji użytkownika.  Aby ten scenariusz działał, zarówno , `applicationId` jak i muszą być określone w zasadach `objectId` dostępu. Identyfikator `applicationId` identyfikuje wymaganą aplikację, a identyfikator `objectId` identyfikuje użytkownika. Obecnie ta opcja nie jest dostępna dla płaszczyzny danych RBAC platformy Azure (wersja zapoznawcza).

We wszystkich typach dostępu aplikacja uwierzytelnia się w usłudze Azure AD. Aplikacja używa dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-vs-authorization.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie w celu udzielenia dostępu. Zasób jest punktem końcowym na płaszczyźnie zarządzania lub danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do Key Vault. Aby dowiedzieć się więcej, przejrzyj [cały przepływ uwierzytelniania.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Model pojedynczego mechanizmu uwierzytelniania w obu płaszczyznach ma kilka zalet:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich magazynów kluczy w swojej organizacji.
- Jeśli użytkownik odejdzie, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji w usłudze Azure AD, takich jak włączanie uwierzytelniania wieloskładnikowego w celu dodania zabezpieczeń.

## <a name="access-model-overview"></a>Omówienie modelu dostępu

Dostęp do magazynu kluczy jest kontrolowany za pośrednictwem dwóch interfejsów: **płaszczyzny zarządzania** i **płaszczyzny danych**. Płaszczyzna zarządzania to miejsce, w którym zarządzasz Key Vault samodzielnie. Operacje na tej płaszczyźnie obejmują tworzenie i usuwanie magazynów kluczy, pobieranie Key Vault właściwości i aktualizowanie zasad dostępu. Płaszczyzna danych to miejsce, w którym pracujesz z danymi przechowywanymi w magazynie kluczy. Możesz dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Obie płaszczyzny używają [Azure Active Directory (Azure AD) do](../../active-directory/fundamentals/active-directory-whatis.md) uwierzytelniania. W celu autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach [(RBAC)](../../role-based-access-control/overview.md) platformy [Azure, a](./assign-access-policy-portal.md) płaszczyzna danych używa zasad dostępu usługi Key Vault i kontroli [dostępu opartej](./rbac-guide.md)na rolach platformy Azure na Key Vault danych.

Aby uzyskać dostęp do magazynu kluczy na jednej z płaszczyzn, wszystkie wywołujące (użytkownicy lub aplikacje) muszą mieć odpowiednie uwierzytelnianie i autoryzację. Uwierzytelnianie określa tożsamość wywołującego. Autoryzacja określa, które operacje może wykonywać wywołujący. Uwierzytelnianie za Key Vault działa w połączeniu z usługą [Azure Active Directory (Azure AD),](../../active-directory/fundamentals/active-directory-whatis.md)która jest odpowiedzialna za uwierzytelnianie tożsamości dowolnego **podmiotu zabezpieczeń.**

Podmiot zabezpieczeń to obiekt reprezentujący użytkownika, grupę, usługę lub aplikację, która żąda dostępu do zasobów platformy Azure. Platforma Azure przypisuje unikatowy **identyfikator obiektu do** każdego podmiotu zabezpieczeń.

- Podmiot **zabezpieczeń** użytkownika identyfikuje osobę, która ma profil w Azure Active Directory.
- Podmiot **zabezpieczeń** grupy identyfikuje zestaw użytkowników utworzonych w Azure Active Directory. Wszystkie role lub uprawnienia przypisane do grupy są przyznawane wszystkim użytkownikom w grupie.
- Nazwa **główna usługi** to typ podmiotu zabezpieczeń, który identyfikuje aplikację lub usługę, czyli fragment kodu, a nie użytkownika lub grupę. Identyfikator obiektu jednostki usługi jest  nazywany identyfikatorem klienta i działa jak nazwa użytkownika. Klucz tajny lub certyfikat klienta **jednostki** usługi **działa** jak jego hasło. Wiele usług platformy Azure obsługuje przypisywanie tożsamości [zarządzanej za pomocą](../../active-directory/managed-identities-azure-resources/overview.md) zautomatyzowanego zarządzania **identyfikatorem klienta** i **certyfikatem**. Tożsamość zarządzana to najbezpiecznsza i zalecana opcja uwierzytelniania na platformie Azure.

Aby uzyskać więcej informacji na temat uwierzytelniania w Key Vault, zobacz [Uwierzytelnianie w Azure Key Vault](authentication.md)

## <a name="privileged-access"></a>Dostęp uprzywilejowany

Autoryzacja określa, które operacje może wykonywać wywołujący. Autoryzacja w Key Vault korzysta z kombinacji kontroli dostępu na podstawie ról [(RBAC)](../../role-based-access-control/overview.md) platformy Azure i Azure Key Vault dostępu.

Dostęp do magazynów odbywa się za pośrednictwem dwóch interfejsów lub płaszczyzn. Te płaszczyzny to płaszczyzna zarządzania i płaszczyzna danych.

- Płaszczyzna *zarządzania* służy do zarządzania Key Vault i interfejsem używanym do tworzenia i usuwania magazynów. Możesz również odczytywać właściwości magazynu kluczy i zarządzać zasadami dostępu.
- Płaszczyzna *danych* umożliwia pracę z danymi przechowywanymi w magazynie kluczy. Możesz dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Aplikacje mają dostęp do płaszczyzn za pośrednictwem punktów końcowych. Mechanizmy kontroli dostępu dla dwóch płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do używania kluczy w magazynie kluczy, należy udzielić dostępu do płaszczyzny danych przy użyciu zasad Key Vault dostępu lub kontroli dostępu na platformie Azure. Aby udzielić użytkownikowi dostępu do odczytu do Key Vault i tagów, ale nie do danych (kluczy, wpisów tajnych lub certyfikatów), należy udzielić dostępu do płaszczyzny zarządzania przy użyciu kontroli dostępu na kontach platformy Azure.

W poniższej tabeli przedstawiono punkty końcowe płaszczyzn zarządzania i danych.

| Płaszczyzna &nbsp; dostępu | Punkty końcowe dostępu | Operacje | Mechanizm &nbsp; kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne:**<br> management.azure.com:443<br><br> **Azure (Chiny) — 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> management.microsoftazure.de:443 | Tworzenie, odczytywanie, aktualizowanie i usuwanie magazynów kluczy<br><br>Ustawianie Key Vault dostępu<br><br>Ustawianie Key Vault tagów | Kontrola dostępu na podstawie ról platformy Azure |
| Płaszczyzna danych | **Globalne:**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Azure (Chiny) — 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 | Klucze: szyfruj, odszyfruj, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Certyfikaty: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Wpisy tajne: get, list, set, delete,recover, backup, restore, purge | Key Vault dostępu lub RBAC platformy Azure |

### <a name="managing-administrative-access-to-key-vault"></a>Zarządzanie dostępem administracyjnym do Key Vault

Gdy tworzysz magazyn kluczy w grupie zasobów, zarządzasz dostępem przy użyciu usługi Azure AD. Użytkownikom lub grupom udziela się możliwości zarządzania magazynami kluczy w grupie zasobów. Możesz udzielić dostępu na określonym poziomie zakresu, przypisując odpowiednie role platformy Azure. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać mu wstępnie zdefiniowaną rolę `key vault Contributor` w określonym zakresie. Do roli platformy Azure można przypisać następujące poziomy zakresów:

- **Subskrypcja:** Rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów:** rola platformy Azure przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób:** rola platformy Azure przypisana dla określonego zasobu ma zastosowanie do tego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie pasuje do Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [Azure RBAC: Built-in roles (Azure RBAC: role wbudowane).](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Jeśli użytkownik ma uprawnienia do płaszczyzny zarządzania magazynu kluczy, może udzielić sobie dostępu do płaszczyzny danych, ustawiając Key Vault `Contributor` dostępu. Należy ściśle kontrolować, kto `Contributor` ma dostęp roli do magazynów kluczy. Upewnij się, że tylko autoryzowane osoby mogą uzyskać dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.

### <a name="controlling-access-to-key-vault-data"></a>Kontrolowanie dostępu do Key Vault danych

Key Vault zasad dostępu przyznają oddzielnie uprawnienia do kluczy, wpisów tajnych lub certyfikatu. Użytkownikowi można udzielić dostępu tylko do kluczy, a nie do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów są zarządzane na poziomie magazynu.

> [!IMPORTANT]
> Key Vault zasad dostępu nie obsługują szczegółowych uprawnień na poziomie obiektu, takich jak określony klucz, klucz tajny lub certyfikat. Gdy użytkownik ma uprawnienia do tworzenia i usuwania kluczy, może wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.

Zasady dostępu dla magazynu kluczy można ustawić przy użyciu interfejsu [Azure Portal,](assign-access-policy-portal.md)interfejsu wiersza polecenia platformy [Azure,](assign-access-policy-cli.md)interfejsu [Azure PowerShell](assign-access-policy-powershell.md)lub interfejsów API REST Key Vault [Management.](/rest/api/keyvault/)

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Key Vault rejestrowanie zapisuje informacje o działaniach wykonywanych w magazynie. Aby uzyskać szczegółowe informacje, [zobacz Key Vault rejestrowania.](logging.md)

Możesz zintegrować Key Vault z Event Grid w celu powiadomienia o zmianie stanu klucza, certyfikatu lub klucza tajnego przechowywanego w magazynie kluczy. Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie Key Vault za pomocą Azure Event Grid](event-grid-overview.md)

Ważne jest również, aby monitorować kondycję magazynu kluczy, aby upewnić się, że usługa działa zgodnie z przeznaczeniem. Aby dowiedzieć się, jak to zrobić, zobacz Monitorowanie i [alerty dla Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Tworzenie i przywracanie kopii zapasowych

Azure Key Vault usuwania nie soft i ochrony przed przeczyszczaniem umożliwia odzyskanie usuniętych magazynów i obiektów magazynu. Aby uzyskać szczegółowe informacje, zobacz [Azure Key Vault omówienie usuwania nie soft-delete.](soft-delete-overview.md)

Należy również regularnie tworzyć kopię zapasową magazynu podczas aktualizowania/usuwania/tworzenia obiektów w magazynie.  

## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault punkt odniesienia zabezpieczeń](security-baseline.md)
- [Azure Key Vault najlepsze rozwiązania](security-baseline.md)
- [Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: role wbudowane](../../role-based-access-control/built-in-roles.md)