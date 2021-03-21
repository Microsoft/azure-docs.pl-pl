---
title: Omówienie zabezpieczeń Azure Key Vault
description: Omówienie funkcji zabezpieczeń i najlepszych rozwiązań dla Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c7635fdc2012ab404709733d8f5849465c2ee82f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071573"
---
# <a name="azure-key-vault-security"></a>Zabezpieczenia usługi Azure Key Vault

Używasz Azure Key Vault do ochrony kluczy szyfrowania i wpisów tajnych, takich jak certyfikaty, ciągi połączeń i hasła w chmurze. W przypadku przechowywania danych poufnych i ważnych, należy podjąć kroki w celu zmaksymalizowania zabezpieczeń magazynów oraz przechowywanych w nich danych.

Ten artykuł zawiera omówienie funkcji zabezpieczeń i najlepszych rozwiązań dotyczących Azure Key Vault. 

> [!NOTE]
> Aby uzyskać pełną listę zaleceń dotyczących zabezpieczeń Azure Key Vault, zobacz [linię bazową zabezpieczeń dla Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

Możesz zmniejszyć narażenie swoich magazynów, określając adresy IP, które mają do nich dostęp. Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów IPv4 (protokołu internetowego w wersji 4). Każdy użytkownik nawiązujący połączenie z magazynem kluczy spoza tych źródeł nie odmówi dostępu.  Aby uzyskać szczegółowe informacje, zobacz [punkty końcowe usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md)

Gdy reguły zapory są stosowane, użytkownicy mogą odczytywać dane z Key Vault tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do Key Vault z Azure Portal. Mimo że użytkownicy mogą przechodzić do magazynu kluczy z Azure Portal, mogą nie być w stanie wyświetlać kluczy, wpisów tajnych ani certyfikatów, jeśli ich maszyny klienckie nie znajdują się na liście dozwolonych. Ma to również wpływ na wybór Key Vault przez inne usługi platformy Azure. Użytkownicy mogą widzieć listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają ich komputerom klienckim.  Aby uzyskać instrukcje implementacji, zobacz [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](network-security.md)

Usługa link prywatny platformy Azure umożliwia dostęp do Azure Key Vault i hostowanych usług partnerskich klientów platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.  Aby uzyskać instrukcje dotyczące implementacji, zobacz [integracja Key Vault z prywatnym łączem platformy Azure](private-link-service.md)

## <a name="tls-and-https"></a>Protokoły TLS i HTTPS

- Fronton Key Vault (płaszczyzna danych) to serwer z wieloma dzierżawami. Oznacza to, że magazyny kluczy pochodzące od różnych klientów mogą współużytkować ten sam publiczny adres IP. W celu uzyskania izolacji każde żądanie HTTP jest uwierzytelniane i autoryzowane niezależnie od innych żądań.
- Można zidentyfikować starsze wersje protokołu TLS w celu zgłaszania luk w zabezpieczeniach, ale ponieważ publiczny adres IP jest współużytkowany, nie jest możliwe, aby zespół usługi magazynu kluczy mógł wyłączyć stare wersje protokołu TLS dla poszczególnych magazynów kluczy na poziomie transportu.
- Protokół HTTPS pozwala klientowi uczestniczyć w negocjacji protokołu TLS. **Klienci mogą wymusić najnowszą wersję protokołu TLS** i zawsze, gdy klient wykonuje takie działania, całe połączenie będzie używać odpowiedniej ochrony na poziomie. Fakt, że Key Vault nadal obsługuje starsze wersje protokołu TLS, nie będzie zakłócać bezpieczeństwa połączeń przy użyciu nowszych wersji protokołu TLS.
- Pomimo znanych luk w zabezpieczeniach protokołu TLS nie ma znanego ataku, który zezwoli złośliwemu agentowi na Wyodrębnienie informacji z magazynu kluczy, gdy atakujący inicjuje połączenie z wersją protokołu TLS, która ma luki w zabezpieczeniach. Osoba atakująca nadal musi uwierzytelnić się i autoryzować siebie i tak długo, jak legalni klienci zawsze łączą się z najnowszymi wersjami protokołu TLS, nie ma żadnego sposobu, aby poświadczenia mogły zostać ujawnione w przypadku luk w zabezpieczeniach w starych wersjach protokołu TLS.

## <a name="identity-management"></a>Zarządzanie tożsamościami

Podczas tworzenia magazynu kluczy w ramach subskrypcji platformy Azure jest on automatycznie kojarzony z dzierżawą usługi Azure AD subskrypcji. Każdy użytkownik próbujący zarządzać zawartością lub pobrać ją z magazynu musi być uwierzytelniany przez usługę Azure AD. W obu przypadkach aplikacje mogą uzyskiwać dostęp do Key Vault na trzy sposoby:

- **Tylko aplikacja**: aplikacja reprezentuje jednostkę usługi lub zarządzaną tożsamość. Ta tożsamość jest najbardziej typowym scenariuszem dla aplikacji, które okresowo potrzebują do uzyskiwania dostępu do certyfikatów, kluczy lub wpisów tajnych z magazynu kluczy. Aby ten scenariusz działał, `objectId` aplikacja musi być określona w zasadach dostępu i `applicationId` _nie_ może być określona lub musi być `null` .
- **Tylko użytkownik**: użytkownik uzyskuje dostęp do magazynu kluczy z dowolnej aplikacji zarejestrowanej w dzierżawie. Przykłady tego typu dostępu obejmują Azure PowerShell i Azure Portal. Aby ten scenariusz działał, `objectId` użytkownik musi być określony w zasadach dostępu i `applicationId` _nie_ może być określony lub musi być `null` .
- **Aplikacja-Plus — użytkownik** (czasami określany jako _tożsamość złożona_): użytkownik jest zobowiązany do uzyskania dostępu do magazynu kluczy z określonej aplikacji _, a_ aplikacja musi używać przepływu uwierzytelniania w imieniu (OBO) do personifikacji użytkownika. Aby ten scenariusz działał, oba `applicationId` i `objectId` muszą być określone w zasadach dostępu. `applicationId`Identyfikuje wymaganą aplikację i `objectId` identyfikuje użytkownika. Obecnie ta opcja jest niedostępna w przypadku płaszczyzny danych Azure RBAC (wersja zapoznawcza).

W przypadku wszystkich typów dostępu aplikacja uwierzytelnia się za pomocą usługi Azure AD. Aplikacja używa dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-vs-authorization.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie, aby udzielić dostępu. Zasób jest punktem końcowym w obszarze zarządzania lub płaszczyzny danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do Key Vault. Aby dowiedzieć się więcej, zapoznaj się z [całym przepływem uwierzytelniania](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Aby uzyskać szczegółowe informacje, zobacz [Key Vault podstawowe uwierzytelnianie](authentication-fundamentals.md)

## <a name="privileged-access"></a>Dostęp uprzywilejowany

Autoryzacja określa, które operacje mogą zostać wykonane przez obiekt wywołujący. Autoryzacja w Key Vault korzysta z kombinacji [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md) i zasad dostępu Azure Key Vault.

Dostęp do magazynów odbywa się za przez dwa interfejsy lub płaszczyzny. Te płaszczyzny są płaszczyzny zarządzania i płaszczyzny danych.

- *Płaszczyzny zarządzania* to miejsce, w którym zarządza się Key Vault i jest interfejsem używanym do tworzenia i usuwania magazynów. Można także odczytać właściwości magazynu kluczy i zarządzać zasadami dostępu.
- *Płaszczyzna danych* umożliwia korzystanie z danych przechowywanych w magazynie kluczy. Możesz dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Aplikacje uzyskują dostęp do płaszczyzn za pomocą punktów końcowych. Kontrole dostępu dla dwóch płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do korzystania z kluczy w magazynie kluczy, Udziel dostępu do płaszczyzny danych przy użyciu zasad dostępu Key Vault lub kontroli RBAC platformy Azure (wersja zapoznawcza). Aby udzielić użytkownikowi dostępu do odczytu do Key Vault właściwości i tagów, ale nie do uzyskiwania dostępu do danych (kluczy, wpisów tajnych lub certyfikatów), przyznano dostęp do płaszczyzny zarządzania przy użyciu funkcji RBAC platformy Azure.

W poniższej tabeli przedstawiono punkty końcowe dla punktów zarządzania i płaszczyzny danych.

| &nbsp;Płaszczyzna dostępu | Punkty końcowe dostępu | Operacje | &nbsp;Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne**<br> management.azure.com:443<br><br> **Azure Chiny 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Administracja USA platformy Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> management.microsoftazure.de:443 | Tworzenie, odczytywanie, aktualizowanie i usuwanie magazynów kluczy<br><br>Ustawianie zasad dostępu Key Vault<br><br>Ustawianie tagów Key Vault | Kontrola dostępu na podstawie ról platformy Azure |
| Płaszczyzna danych | **Globalne**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Azure Chiny 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Administracja USA platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 | Klucze: szyfrowanie, odszyfrowywanie, wrapKey, unwrapKey, podpisywanie, weryfikowanie, pobieranie, wyświetlanie, tworzenie, aktualizowanie, importowanie, usuwanie, odzyskiwanie, tworzenie kopii zapasowej, przywracanie, przeczyszczanie<br><br> Certyfikaty: managecontacts, getemitencis, listissuers, setemitencis, deleteissuers, manageissuers, get, list, Create, import, Update, DELETE, Recover, Backup, Restore, przeczyszczanie<br><br>  Wpisy tajne: pobieranie, wyświetlanie, Ustawianie, usuwanie, odzyskiwanie, tworzenie kopii zapasowej, przywracanie, przeczyszczanie | Zasady dostępu Key Vault lub RBAC platformy Azure (wersja zapoznawcza)|

### <a name="managing-administrative-access-to-key-vault"></a>Zarządzanie dostępem administracyjnym do Key Vault

Podczas tworzenia magazynu kluczy w grupie zasobów można zarządzać dostępem przy użyciu usługi Azure AD. Użytkownicy lub grupy mogą zarządzać magazynami kluczy w grupie zasobów. Można udzielić dostępu na określonym poziomie zakresu, przypisując odpowiednie role platformy Azure. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną `key vault Contributor` rolę do użytkownika w określonym zakresie. Do roli platformy Azure można przypisać następujące poziomy zakresów:

- **Subskrypcja**: rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów**: rola platformy Azure przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób**: dla danego zasobu jest stosowana rola platformy Azure przypisana do określonego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie spełnia Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [Azure RBAC: role wbudowane](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Jeśli użytkownik ma `Contributor` uprawnienia do płaszczyzny zarządzania magazynu kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu Key Vault. Należy ściśle kontrolować, kto ma `Contributor` dostęp do roli do Twoich magazynów kluczy. Upewnij się, że tylko autoryzowani osoby mają dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz nimi zarządzać.

### <a name="controlling-access-to-key-vault-data"></a>Kontrolowanie dostępu do danych Key Vault

Key Vault zasady dostępu udzielają uprawnień oddzielnie do kluczy, wpisów tajnych lub certyfikatów. Można udzielić użytkownikowi dostępu tylko do kluczy i nie do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów są zarządzane na poziomie magazynu.

> [!IMPORTANT]
> Zasady dostępu Key Vault nie obsługują szczegółowych uprawnień na poziomie obiektów, takich jak określony klucz, klucz tajny lub certyfikat. Gdy użytkownik uzyskuje uprawnienia do tworzenia i usuwania kluczy, mogą wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.

Można ustawić zasady dostępu dla magazynu kluczy przy użyciu [Azure Portal](assign-access-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)lub [interfejsów API REST zarządzania Key Vault](/rest/api/keyvault/).

Dostęp do płaszczyzny danych można ograniczyć za pomocą [punktów końcowych usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md)). [Zapory i reguły sieci wirtualnej](network-security.md) można skonfigurować w celu uzyskania dodatkowej warstwy zabezpieczeń.

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Rejestrowanie Key Vault zapisuje informacje o działaniach wykonywanych w magazynie. Aby uzyskać szczegółowe informacje, zobacz [rejestrowanie Key Vault](logging.md).

Możesz zintegrować Key Vault z Event Grid, aby otrzymywać powiadomienia o zmianie stanu klucza, certyfikatu lub wpisu tajnego przechowywanego w magazynie kluczy. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie Key Vault z Azure Event Grid](event-grid-overview.md)

Ważne jest również, aby monitorować kondycję magazynu kluczy, aby upewnić się, że usługa działa zgodnie z oczekiwaniami. Aby dowiedzieć się, jak to zrobić, zobacz [monitorowanie i zgłaszanie alertów dla Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Tworzenie i przywracanie kopii zapasowych

Azure Key Vault trwałe usuwanie i przeczyszczanie ochrony umożliwia Odzyskiwanie usuniętych magazynów i obiektów magazynu. Aby uzyskać szczegółowe informacje, zobacz [Azure Key Vault Omówienie usuwania nietrwałego](soft-delete-overview.md).

Należy również regularnie z powrotem korzystać z magazynu w celu aktualizowania/usuwania/tworzenia obiektów w magazynie.  

## <a name="next-steps"></a>Następne kroki

- [Linia bazowa zabezpieczeń Azure Key Vault](security-baseline.md)
- [Azure Key Vault najlepszych praktyk](security-baseline.md)
- [Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md)
