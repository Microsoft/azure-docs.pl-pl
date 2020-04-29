---
title: Niestandardowe poddomeny
titleSuffix: Azure Cognitive Services
description: Niestandardowe nazwy domen poddomen dla każdego zasobu usługi poznawczej są tworzone za pomocą Azure Portal, Azure Cloud Shell lub interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73647692"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Niestandardowe nazwy domen poddomen dla Cognitive Services

Platforma Azure Cognitive Services używać niestandardowych nazw poddomen dla każdego zasobu utworzonego za pomocą [Azure Portal](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). W przeciwieństwie do regionalnych punktów końcowych, które były wspólne dla wszystkich klientów w określonym regionie świadczenia usługi Azure, niestandardowe nazwy domen poddomen są unikatowe dla zasobu. Niestandardowe nazwy domen poddomen są wymagane do włączenia funkcji, takich jak Azure Active Directory (Azure AD) do uwierzytelniania.

## <a name="how-does-this-impact-existing-resources"></a>Jak ma to wpływ na istniejące zasoby?

Cognitive Services zasobów utworzonych przed 1 lipca 2019 będzie używać regionalnych punktów końcowych dla skojarzonej usługi. Te punkty końcowe będą współpracować z istniejącymi i nowymi zasobami.

Jeśli chcesz przeprowadzić migrację istniejącego zasobu, aby użyć niestandardowych nazw domen, aby można było włączyć funkcje takie jak Azure AD, wykonaj następujące instrukcje:

1. Zaloguj się do Azure Portal i Znajdź zasób Cognitive Services, do którego chcesz dodać niestandardową nazwę domeny podrzędnej.
2. W bloku **Przegląd** zlokalizuj i wybierz pozycję **Generuj niestandardową nazwę domeny**.
3. Zostanie otwarty panel z instrukcjami, aby utworzyć unikatową niestandardową poddomenę dla zasobu.
   > [!WARNING]
   > Po utworzeniu niestandardowej nazwy domeny podrzędnej **nie można** jej zmienić.

## <a name="do-i-need-to-update-my-existing-resources"></a>Czy muszę zaktualizować moje istniejące zasoby?

Nie. Regionalny punkt końcowy będzie nadal działał w przypadku nowych i istniejących Cognitive Services, a niestandardowa nazwa domeny podrzędnej jest opcjonalna. Nawet jeśli dodaliśmy niestandardową nazwę domeny podrzędnej, regionalny punkt końcowy będzie kontynuował pracę z zasobem.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Co zrobić, jeśli zestaw SDK poprosi o podanie regionu dla zasobu?

> [!WARNING]
> **Usługi mowy nie obsługują** w tym momencie niestandardowych poddomen. Użyj regionalnych punktów końcowych, gdy korzystasz z usług mowy i skojarzonych zestawów SDK.

Regionalne punkty końcowe i niestandardowe nazwy domen są obsługiwane i mogą być używane zamiennie. Wymagany jest jednak pełny punkt końcowy.

Informacje o regionie są dostępne w bloku **Przegląd** dla zasobu w [Azure Portal](https://portal.azure.com). Aby uzyskać pełną listę regionalnych punktów końcowych, zobacz [czy istnieje lista regionalnych punktów końcowych?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Czy niestandardowe nazwy domen są regionalne?

Tak. Używanie niestandardowej nazwy domeny podrzędnej nie zmienia żadnych aspektów regionalnych zasobów Cognitive Services.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Jakie są wymagania dotyczące niestandardowej nazwy domeny podrzędnej?

Niestandardowa nazwa domeny podrzędnej jest unikatowa dla zasobu. Nazwa może zawierać tylko znaki alfanumeryczne i `-` znak; musi mieć długość od 2 do 64 znaków i nie może kończyć się znakiem `-`.

## <a name="can-i-change-a-custom-domain-name"></a>Czy mogę zmienić niestandardową nazwę domeny?

Nie. Po utworzeniu niestandardowej nazwy domeny podrzędnej i skojarzeniu jej z zasobem nie można jej zmienić.

## <a name="can-i-reuse-a-custom-domain-name"></a>Czy mogę ponownie użyć niestandardowej nazwy domeny?

Każda niestandardowa nazwa domeny podrzędnej jest unikatowa, dlatego w celu ponownego użycia niestandardowej nazwy domeny podrzędnej przypisanej do zasobu Cognitive Services należy usunąć istniejący zasób. Po usunięciu zasobu można ponownie użyć niestandardowej nazwy domeny podrzędnej.

## <a name="is-there-a-list-of-regional-endpoints"></a>Czy istnieje lista regionalnych punktów końcowych?

Tak. Jest to lista regionalnych punktów końcowych, które mogą być używane z zasobami Cognitive Services platformy Azure.

> [!NOTE]
> Interfejs API tłumaczenia tekstu w usłudze Translator i interfejsy API wyszukiwania Bing używają globalnych punktów końcowych.

| Typ punktu końcowego | Region | Endpoint |
|---------------|--------|----------|
| Public | Globalne (tłumaczenie tekstu w usłudze Translator & Bing) | `https://api.cognitive.microsoft.com` |
| | Australia Wschodnia | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brazylia Południowa | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Kanada Środkowa | `https://canadacentral.api.cognitive.microsoft.com` |
| | Środkowe stany USA | `https://centralus.api.cognitive.microsoft.com` |
| | Azja Wschodnia | `https://eastasia.api.cognitive.microsoft.com` |
| | Wschodnie stany USA | `https://eastus.api.cognitive.microsoft.com` |
| | Wschodnie stany USA 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Francja Środkowa | `https://francecentral.api.cognitive.microsoft.com` |
| | Indie Środkowe | `https://centralindia.api.cognitive.microsoft.com` |
| | Japonia Wschodnia | `https://japaneast.api.cognitive.microsoft.com` |
| | Korea Środkowa | `https://koreacentral.api.cognitive.microsoft.com` |
| | Północno-środkowe stany USA | `https://northcentralus.api.cognitive.microsoft.com` |
| | Europa Północna | `https://northeurope.api.cognitive.microsoft.com` |
| | Północna Republika Południowej Afryki | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Południowo-środkowe stany USA | `https://southcentralus.api.cognitive.microsoft.com` |
| | Azja Południowo-Wschodnia | `https://southeastasia.api.cognitive.microsoft.com` |
| | Południowe Zjednoczone Królestwo | `https://uksouth.api.cognitive.microsoft.com` |
| | Zachodnio-środkowe stany USA | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa Zachodnia | `https://westeurope.api.cognitive.microsoft.com` |
| | Zachodnie stany USA | `https://westus.api.cognitive.microsoft.com` |
| | Zachodnie stany USA 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov Wirginia | `https://virginia.api.cognitive.microsoft.us` |
| Chiny | Chiny Wschodnie 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Chiny Północne | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Zobacz także

* [Co to jest Cognitive Services?](Welcome.md)
* [Authentication](authentication.md)
