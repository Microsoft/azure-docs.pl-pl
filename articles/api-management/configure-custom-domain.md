---
title: Skonfiguruj niestandardową nazwę domeny dla wystąpienia usługi Azure API Management
titleSuffix: Azure API Management
description: W tym temacie opisano, jak skonfigurować niestandardową nazwę domeny dla wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: a7032c64efa486c65830e013373239647a368540
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92311135"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Konfigurowanie niestandardowej nazwy domeny dla wystąpienia usługi Azure API Management

Gdy tworzysz wystąpienie usługi Azure API Management, platforma Azure przypisze ją poddomenie `azure-api.net` (na przykład `apim-service-name.azure-api.net` ). Można jednak uwidocznić API Management punkty końcowe przy użyciu własnej niestandardowej nazwy domeny, takiej jak **contoso.com**. W tym samouczku przedstawiono sposób mapowania istniejącej niestandardowej nazwy DNS na punkty końcowe udostępniane przez wystąpienie API Management.

> [!IMPORTANT]
> API Management akceptuje tylko żądania zawierające wartości [nagłówka hosta](https://tools.ietf.org/html/rfc2616#section-14.23) zgodne z domyślną nazwą domeny lub dowolnymi skonfigurowanymi niestandardowymi nazwami domen.

> [!WARNING]
> Klienci, którzy chcą korzystać z przypinania certyfikatów w celu poprawy bezpieczeństwa aplikacji, muszą używać niestandardowej nazwy domeny i certyfikatu, którym zarządzają, a nie z domyślnym certyfikatem. Klienci, którzy przystosują domyślnie certyfikat domyślny, będą korzystać z twardej zależności od właściwości niekontrolowanego certyfikatu, co nie jest zalecanym postępowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować:

-   Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Wystąpienie API Management. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
-   Niestandardowa nazwa domeny, do której należy użytkownik lub Twoja organizacja. Ten temat nie zawiera instrukcji dotyczących sposobu pozyskiwania niestandardowej nazwy domeny.
-   Rekord CNAME hostowany na serwerze DNS, który mapuje niestandardową nazwę domeny na domyślną nazwę domeny wystąpienia API Management. Ten temat nie zawiera instrukcji dotyczących hostowania rekordu CNAME.
-   Musisz mieć prawidłowy certyfikat z kluczem publicznym i prywatnym (. PFX). Podmiot lub alternatywna nazwa podmiotu (SAN) musi być zgodna z nazwą domeny (umożliwia API Management wystąpieniu bezpiecznego ujawniania adresów URL za pośrednictwem protokołu TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Użyj Azure Portal, aby ustawić niestandardową nazwę domeny

1. Przejdź do wystąpienia API Management w [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **domeny niestandardowe**.

    Istnieje kilka punktów końcowych, do których można przypisać niestandardową nazwę domeny. Obecnie dostępne są następujące punkty końcowe:

    - **Brama** (domyślnie: `<apim-service-name>.azure-api.net` ),
    - **Portal dla deweloperów (starsza wersja** : `<apim-service-name>.portal.azure-api.net` ),
    - **Portal dla deweloperów** (domyślnie: `<apim-service-name>.developer.azure-api.net` ).
    - **Zarządzanie** (domyślnie: `<apim-service-name>.management.azure-api.net` ),
    - **SCM** (domyślnie: `<apim-service-name>.scm.azure-api.net` ),

    > [!NOTE]
    > Tylko punkt końcowy **bramy** jest dostępny do konfiguracji w warstwie zużycia.
    > Można zaktualizować wszystkie punkty końcowe lub niektóre z nich. Często klienci aktualizują **bramę** (ten adres URL jest używany do wywoływania interfejsu API uwidocznionego za pomocą API Management) i **portalu** (adres URL portalu dla deweloperów).
    > Punkty końcowe **zarządzania** i **SCM** są używane wewnętrznie przez właścicieli wystąpień API Management i w ten sposób rzadziej przypisywana jest niestandardowa nazwa domeny.
    > Warstwa **Premium** obsługuje ustawianie wielu nazw hostów dla punktu końcowego **bramy** .

1. Wybierz punkt końcowy, który chcesz zaktualizować.
1. W oknie po prawej stronie kliknij pozycję **niestandardowy**.

    - W polu **nazwa domeny niestandardowej** Określ nazwę, której chcesz użyć. Na przykład `api.contoso.com`.
    - W obszarze **certyfikat** wybierz certyfikat z Key Vault. Można również przekazać prawidłowy. Plik PFX i podaj jego **hasło**, jeśli certyfikat jest chroniony hasłem.

    > [!NOTE]
    > Nazwy domen wieloznacznych, np., `*.contoso.com` są obsługiwane we wszystkich warstwach poza warstwą zużycia.

    > [!TIP]
    > Zalecamy używanie [Azure Key Vault do zarządzania certyfikatami](../key-vault/certificates/about-certificates.md) i ustawiania ich do autoodnawiania.
    > Jeśli używasz Azure Key Vault do zarządzania certyfikatami protokołu TLS/SSL domeny niestandardowej, upewnij się, że certyfikat został wstawiony do Key Vault [jako _certyfikat_](/rest/api/keyvault/createcertificate/createcertificate), a nie _wpis tajny_.
    >
    > Aby można było pobrać certyfikat TLS/SSL, API Management musi mieć uprawnienia listy i pobrania kluczy tajnych na Azure Key Vault zawierający certyfikat. W przypadku korzystania z Azure Portal wszystkie niezbędne kroki konfiguracji zostaną wykonane automatycznie. W przypadku korzystania z narzędzi wiersza polecenia lub interfejsu API zarządzania należy przyznać te uprawnienia ręcznie. Odbywa się to w dwóch krokach. Najpierw użyj strony tożsamości zarządzane w wystąpieniu API Management, aby upewnić się, że zarządzana tożsamość jest włączona, i zanotuj identyfikator podmiotu zabezpieczeń pokazywany na tej stronie. Następnie nadaj liście uprawnień i uzyskaj uprawnienia do tego identyfikatora podmiotu zabezpieczeń na Azure Key Vault zawierającym certyfikat.
    >
    > Jeśli certyfikat jest ustawiony na automatyczne odnawianie, API Management automatycznie pobierze najnowszą wersję bez żadnego przestoju usługi (Jeśli warstwa API Management ma umowę SLA-i. e. we wszystkich warstwach poza warstwą dewelopera).

1. Kliknij pozycję Zastosuj.

    > [!NOTE]
    > Proces przypisywania certyfikatu może potrwać 15 minut lub dłużej w zależności od rozmiaru wdrożenia. Jednostka SKU dla deweloperów ma przestoje, a w przypadku wersji podstawowa i wyższa nie ma przestojów.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Konfiguracja usługi DNS

Podczas konfigurowania usługi DNS dla niestandardowej nazwy domeny dostępne są dwie opcje:

-   Skonfiguruj rekord CNAME, który wskazuje punkt końcowy skonfigurowanej niestandardowej nazwy domeny.
-   Skonfiguruj rekord A, który wskazuje adres IP bramy API Management.

> [!NOTE]
> Mimo że adres IP wystąpienia zarządzania interfejsem API jest statyczny, może on ulec zmianie w kilku scenariuszach. Z tego powodu zaleca się użycie rekordu CNAME podczas konfigurowania domeny niestandardowej. Weź pod uwagę podczas wybierania metody konfiguracji DNS. Przeczytaj więcej w [artykule dotyczącym protokołu IP](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) i [API Management często zadawane pytania](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Następne kroki

[Uaktualnianie i skalowanie usługi](upgrade-and-scale.md)
