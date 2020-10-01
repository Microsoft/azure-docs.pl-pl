---
title: Zabezpieczenia usługi Azure Key Vault
description: Zarządzaj uprawnieniami dostępu do Azure Key Vault, kluczy i wpisów tajnych. Obejmuje model uwierzytelniania i autoryzacji dla Key Vault oraz sposób zabezpieczania magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
ms.openlocfilehash: b6163ca0cb02670024fe95459f31ac81c4da756c
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596369"
---
# <a name="azure-key-vault-security"></a>Zabezpieczenia usługi Azure Key Vault

Używasz Azure Key Vault do ochrony kluczy szyfrowania i wpisów tajnych, takich jak certyfikaty, ciągi połączeń i hasła w chmurze. W przypadku przechowywania danych poufnych i ważnych, należy podjąć kroki w celu zmaksymalizowania zabezpieczeń magazynów oraz przechowywanych w nich danych.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Podczas tworzenia magazynu kluczy w ramach subskrypcji platformy Azure jest on automatycznie kojarzony z dzierżawą usługi Azure AD subskrypcji. Każdy użytkownik próbujący zarządzać zawartością lub pobrać ją z magazynu musi być uwierzytelniany przez usługę Azure AD.

- Uwierzytelnianie ustanawia tożsamość obiektu wywołującego.
- Autoryzacja określa, które operacje mogą zostać wykonane przez obiekt wywołujący. Autoryzacja w Key Vault używa kombinacji [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) i zasad dostępu Azure Key Vault.

### <a name="access-model-overview"></a>Przegląd modelu dostępu

Dostęp do magazynów odbywa się za przez dwa interfejsy lub płaszczyzny. Te płaszczyzny są płaszczyzny zarządzania i płaszczyzny danych.

- *Płaszczyzny zarządzania* to miejsce, w którym zarządza się Key Vault i jest interfejsem używanym do tworzenia i usuwania magazynów. Można także odczytać właściwości magazynu kluczy i zarządzać zasadami dostępu.
- *Płaszczyzna danych* umożliwia korzystanie z danych przechowywanych w magazynie kluczy. Możesz dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Aby uzyskać dostęp do magazynu kluczy w jednej z płaszczyzn, wszystkie obiekty wywołujące (Użytkownicy i aplikacje) muszą zostać uwierzytelnione i autoryzowane. Obie płaszczyzny używają Azure Active Directory (Azure AD) do uwierzytelniania. W przypadku autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), a płaszczyzna danych używa zasad dostępu Key Vault.

Model jednego mechanizmu uwierzytelniania w obu płaszczyznach ma kilka zalet:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich magazynów kluczy w organizacji.
- Jeśli użytkownik opuści te osoby, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji w usłudze Azure AD, takich jak włączenie uwierzytelniania wieloskładnikowego w celu zwiększenia bezpieczeństwa.

### <a name="managing-administrative-access-to-key-vault"></a>Zarządzanie dostępem administracyjnym do Key Vault

Podczas tworzenia magazynu kluczy w grupie zasobów można zarządzać dostępem przy użyciu usługi Azure AD. Użytkownicy lub grupy mogą zarządzać magazynami kluczy w grupie zasobów. Można udzielić dostępu na określonym poziomie zakresu, przypisując odpowiednie role platformy Azure. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną `key vault Contributor` rolę do użytkownika w określonym zakresie. Do roli platformy Azure można przypisać następujące poziomy zakresów:

- **Subskrypcja**: rola platformy Azure przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów**: rola platformy Azure przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób**: dla danego zasobu jest stosowana rola platformy Azure przypisana do określonego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie spełnia Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [RBAC: role wbudowane](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Jeśli użytkownik ma `Contributor` uprawnienia do płaszczyzny zarządzania magazynu kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu Key Vault. Należy ściśle kontrolować, kto ma `Contributor` dostęp do roli do Twoich magazynów kluczy. Upewnij się, że tylko autoryzowani osoby mają dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz nimi zarządzać.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Kontrolowanie dostępu do danych Key Vault

Key Vault zasady dostępu udzielają uprawnień oddzielnie do kluczy, wpisów tajnych lub certyfikatów. Można udzielić użytkownikowi dostępu tylko do kluczy i nie do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów są zarządzane na poziomie magazynu.

> [!IMPORTANT]
> Zasady dostępu Key Vault nie obsługują szczegółowych uprawnień na poziomie obiektów, takich jak określony klucz, klucz tajny lub certyfikat. Gdy użytkownik uzyskuje uprawnienia do tworzenia i usuwania kluczy, mogą wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.

Można ustawić zasady dostępu dla magazynu kluczy przy użyciu [Azure Portal](assign-access-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)lub [interfejsów API REST zarządzania Key Vault](/rest/api/keyvault/).

Dostęp do płaszczyzny danych można ograniczyć za pomocą [punktów końcowych usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md)). [Zapory i reguły sieci wirtualnej](network-security.md) można skonfigurować w celu uzyskania dodatkowej warstwy zabezpieczeń.

## <a name="network-access"></a>Dostęp do sieci

Możesz zmniejszyć narażenie swoich magazynów, określając adresy IP, które mają do nich dostęp. Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów IPv4 (protokołu internetowego w wersji 4). Każdy użytkownik nawiązujący połączenie z magazynem kluczy spoza tych źródeł nie odmówi dostępu.

Gdy reguły zapory są stosowane, użytkownicy mogą odczytywać dane z Key Vault tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do Key Vault z Azure Portal. Mimo że użytkownicy mogą przechodzić do magazynu kluczy z Azure Portal, mogą nie być w stanie wyświetlać kluczy, wpisów tajnych ani certyfikatów, jeśli ich maszyny klienckie nie znajdują się na liście dozwolonych. Ma to również wpływ na wybór Key Vault przez inne usługi platformy Azure. Użytkownicy mogą widzieć listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają ich komputerom klienckim.

Aby uzyskać więcej informacji na temat Azure Key Vault adresów sieciowych Sprawdź [punkty końcowe usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md))

### <a name="tls-and-https"></a>Protokoły TLS i HTTPS

*   Fronton Key Vault (płaszczyzna danych) to serwer z wieloma dzierżawami. Oznacza to, że magazyny kluczy pochodzące od różnych klientów mogą współużytkować ten sam publiczny adres IP. W celu uzyskania izolacji każde żądanie HTTP jest uwierzytelniane i autoryzowane niezależnie od innych żądań.
*   Można zidentyfikować starsze wersje protokołu TLS w celu zgłaszania luk w zabezpieczeniach, ale ponieważ publiczny adres IP jest współużytkowany, nie jest możliwe, aby zespół usługi magazynu kluczy mógł wyłączyć stare wersje protokołu TLS dla poszczególnych magazynów kluczy na poziomie transportu.
*   Protokół HTTPS pozwala klientowi uczestniczyć w negocjacji protokołu TLS. **Klienci mogą wymusić najnowszą wersję protokołu TLS**i zawsze, gdy klient wykonuje takie działania, całe połączenie będzie używać odpowiedniej ochrony na poziomie. Fakt, że Key Vault nadal obsługuje starsze wersje protokołu TLS, nie będzie zakłócać bezpieczeństwa połączeń przy użyciu nowszych wersji protokołu TLS.
*   Pomimo znanych luk w zabezpieczeniach protokołu TLS nie ma znanego ataku, który zezwoli złośliwemu agentowi na Wyodrębnienie informacji z magazynu kluczy, gdy atakujący inicjuje połączenie z wersją protokołu TLS, która ma luki w zabezpieczeniach. Osoba atakująca nadal musi uwierzytelnić się i autoryzować siebie i tak długo, jak legalni klienci zawsze łączą się z najnowszymi wersjami protokołu TLS, nie ma żadnego sposobu, aby poświadczenia mogły zostać ujawnione w przypadku luk w zabezpieczeniach w starych wersjach protokołu TLS.


## <a name="monitoring"></a>Monitorowanie

Rejestrowanie Key Vault zapisuje informacje o działaniach wykonywanych w magazynie. Key Vault dzienników:

- Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądania zakończone niepowodzeniem
  - Operacje w magazynie kluczy. Te operacje obejmują tworzenie, usuwanie, Ustawianie zasad dostępu i aktualizowanie atrybutów magazynu kluczy, takich jak Tagi.
  - Operacje dotyczące kluczy i wpisów tajnych w magazynie kluczy, w tym:
    - Tworzenie, modyfikowanie lub usuwanie tych kluczy lub wpisów tajnych.
    - Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijanie i depakowanie kluczy, pobieranie wpisów tajnych i wyświetlanie listy kluczy i wpisów tajnych (oraz ich wersji).
- Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykłady to żądania, które nie mają tokenu okaziciela, które są źle sformułowane lub wygasłe lub które mają nieprawidłowy token.

Dostęp do informacji o rejestrowaniu można uzyskać w ciągu 10 minut od operacji magazynu kluczy. Można zarządzać dziennikami na koncie magazynu.

- Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
- Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Aby uzyskać zalecenie dotyczące bezpiecznego zarządzania kontami magazynu, zapoznaj się z [przewodnikiem po zabezpieczeniach usługi Azure Storage](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Następne kroki

- [Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md)

