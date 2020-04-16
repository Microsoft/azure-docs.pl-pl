---
title: Zabezpieczenia usługi Azure Key Vault | Dokumenty firmy Microsoft
description: Zarządzanie uprawnieniami dostępu do usługi Azure Key Vault, kluczami i wpisami tajnymi. Obejmuje model uwierzytelniania i autoryzacji dla usługi Key Vault oraz sposób zabezpieczania magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: dbe13b2d1291f212af7da9d1176bc3d90997978b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428968"
---
# <a name="azure-key-vault-security"></a>Zabezpieczenia usługi Azure Key Vault

Musisz chronić klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła w chmurze, aby używać usługi Azure Key Vault. Ponieważ przechowujesz poufne i biznesowe krytyczne dane, musisz podjąć kroki, aby zmaksymalizować bezpieczeństwo magazynów i przechowywanych w nich danych. W tym artykule omówisz niektóre pojęcia, które należy wziąć pod uwagę podczas projektowania zabezpieczeń usługi Azure Key Vault.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Podczas tworzenia magazynu kluczy w subskrypcji platformy Azure jest automatycznie skojarzony z dzierżawy usługi Azure AD subskrypcji. Każda osoba próbująca zarządzać zawartością lub pobierać ją z magazynu, musi zostać uwierzytelniona przez usługę Azure AD.

- Uwierzytelnianie ustanawia tożsamość wywołującego.
- Autoryzacja określa, które operacje można wykonać wywołującego. Autoryzacja w magazynie kluczy używa kombinacji [zasad kontroli dostępu opartego na rolach](../../role-based-access-control/overview.md) (RBAC) i zasad dostępu usługi Azure Key Vault.

### <a name="access-model-overview"></a>Omówienie modelu programu Access

Dostęp do przechowalni odbywa się za pośrednictwem dwóch interfejsów lub płaszczyzn. Płaszczyzny te są płaszczyzną zarządzania i płaszczyzną danych.

- Płaszczyzna *zarządzania* to miejsce, w którym samodzielnie zarządzasz magazynem kluczy i jest to interfejs używany do tworzenia i usuwania przechowalni. Można również odczytać właściwości magazynu kluczy i zarządzać zasadami dostępu.
- Płaszczyzna *danych* umożliwia pracę z danymi przechowywanymi w magazynie kluczy. Można dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Aby uzyskać dostęp do magazynu kluczy na obu płaszczyznach, wszystkie osoby wywołujące (użytkownicy lub aplikacje) muszą być uwierzytelnione i autoryzowane. Obie płaszczyzny używają usługi Azure Active Directory (Azure AD) do uwierzytelniania. Do autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), a płaszczyzna danych używa zasad dostępu usługi Key Vault.

Model pojedynczego mechanizmu uwierzytelniania w obu płaszczyznach ma kilka zalet:

- Organizacje mogą centralnie kontrolować dostęp do wszystkich magazynów kluczy w swojej organizacji.
- Jeśli użytkownik odejdzie, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji w usłudze Azure AD, takich jak włączenie uwierzytelniania wieloskładnikowego w celu zwiększenia zabezpieczeń

### <a name="managing-administrative-access-to-key-vault"></a>Zarządzanie dostępem administracyjnym do usługi Key Vault

Podczas tworzenia magazynu kluczy w grupie zasobów, można zarządzać dostęp przy użyciu usługi Azure AD. Użytkownikom lub grupom można zarządzać magazynami kluczy w grupie zasobów. Można udzielić dostępu na określonym poziomie zakresu, przypisując odpowiednie role RBAC. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną `key vault Contributor` rolę do użytkownika w określonym zakresie. Do roli RBAC można przypisać następujące poziomy zakresów:

- **Subskrypcja:** Rola RBAC przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów:** Rola RBAC przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób:** Rola RBAC przypisana do określonego zasobu ma zastosowanie do tego zasobu. W takim przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie odpowiada Twoim potrzebom, można zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Jeśli użytkownik `Contributor` ma uprawnienia do płaszczyzny zarządzania przechowalnią kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych, ustawiając zasady dostępu usługi Key Vault. Należy ściśle kontrolować, `Contributor` kto ma dostęp do roli do magazynów kluczy. Upewnij się, że tylko autoryzowane osoby mogą uzyskiwać dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Kontrolowanie dostępu do danych usługi Key Vault

Zasady dostępu usługi Key Vault przyznają uprawnienia oddzielnie do kluczy, wpisów tajnych lub certyfikatów. Można udzielić użytkownikowi dostępu tylko do kluczy, a nie do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów są zarządzane na poziomie przechowalni.

> [!IMPORTANT]
> Zasady dostępu usługi Key Vault nie obsługują szczegółowych uprawnień na poziomie obiektu, takich jak określony klucz, klucz tajny lub certyfikat. Gdy użytkownikowi udzielono uprawnień do tworzenia i usuwania kluczy, mogą wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.

Aby ustawić zasady dostępu dla magazynu kluczy, użyj [witryny Azure Portal](https://portal.azure.com/), [interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli?view=azure-cli-latest) [programu Azure PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsów API REST usługi Key Vault Management](/rest/api/keyvault/).

Dostęp do płaszczyzny danych można ograniczyć przy użyciu [punktów końcowych usługi sieci wirtualnej dla usługi Azure Key Vault](overview-vnet-service-endpoints.md)). Można skonfigurować [zapory i reguły sieci wirtualnej](network-security.md) dla dodatkowej warstwy zabezpieczeń.

## <a name="network-access"></a>Dostęp do sieci

Można zmniejszyć ekspozycję magazynów, określając, które adresy IP mają do nich dostęp. Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów Protokołu IPv4 (protokół internetowy w wersji 4). Każdemu użytkownikowi łączącemu się z magazynem kluczy spoza tych źródeł odmówiono dostępu.

Po wprowadzeniu reguł zapory użytkownicy mogą odczytywać dane z usługi Key Vault tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do usługi Key Vault z witryny Azure portal. Mimo że użytkownicy mogą przeglądać do magazynu kluczy z witryny Azure portal, mogą nie być w stanie wyświetlić listy kluczy, wpisów tajnych lub certyfikatów, jeśli ich komputer kliencki nie znajduje się na liście dozwolonych. Dotyczy to również selektora magazynu kluczy przez inne usługi platformy Azure. Użytkownicy mogą być w stanie zobaczyć listę magazynów kluczy, ale nie klucze listy, jeśli reguły zapory uniemożliwiają ich komputera klienckiego.

Aby uzyskać więcej informacji na temat przeglądu adresu sieciowego usługi Azure Key [Vault, punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="monitoring"></a>Monitorowanie

Rejestrowanie usługi Key Vault zapisuje informacje o działaniach wykonywanych w przechowalni. Dzienniki magazynu kluczy:

- Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądania nieudane
  - Operacje na samym magazynie kluczy. Operacje te obejmują tworzenie, usuwanie, ustawianie zasad dostępu i aktualizowanie atrybutów magazynu kluczy, takich jak tagi.
  - Operacje dotyczące kluczy i wpisów tajnych w magazynie kluczy, w tym:
    - Tworzenie, modyfikowanie lub usuwanie tych kluczy lub wpisów tajnych.
    - Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijanie i rozpakowywanie kluczy, uzyskiwanie wpisów tajnych oraz wyświetlanie listy kluczy i wpisów tajnych (i ich wersji).
- Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykładami są żądania, które nie mają tokenu nośnika, które są zniekształcone lub wygasły lub które mają nieprawidłowy token.

Dostęp do informacji rejestrowania można uzyskać w ciągu 10 minut po operacji magazynu kluczy. To do Ciebie, aby zarządzać dziennikami na koncie pamięci masowej.

- Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
- Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Aby uzyskać zalecenie dotyczące bezpiecznego zarządzania kontami magazynu, zapoznaj się z [przewodnikiem po zabezpieczeniach usługi Azure Storage](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Następne kroki

- [Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](overview-vnet-service-endpoints.md))
- [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md)
- [punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](overview-vnet-service-endpoints.md))
