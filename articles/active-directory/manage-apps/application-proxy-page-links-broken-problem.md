---
title: Linki na stronie nie działają w przypadku aplikacji serwera proxy aplikacji
description: Jak rozwiązywać problemy z przerwanymi łączami w aplikacjach serwera proxy aplikacji zintegrowanych z usługą Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1922ea9afd69366e534049f5a7a350cf39e52dee
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371583"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Linki na stronie nie działają w przypadku aplikacji serwera proxy aplikacji

Ten artykuł pomaga w rozwiązywaniu problemów z tym, dlaczego linki w aplikacji serwer proxy aplikacji usługi Azure Active Directory nie działają prawidłowo.

## <a name="overview"></a>Omówienie 
Po opublikowaniu aplikacji serwera proxy aplikacji jedynymi łączami, które działają domyślnie w aplikacji, są linki do miejsc docelowych zawartych w opublikowanym głównym adresie URL. Linki w aplikacjach nie działają, wewnętrzny adres URL aplikacji prawdopodobnie nie obejmuje wszystkich miejsc docelowych linków w aplikacji.

**Dlaczego tak się dzieje?** Po kliknięciu linku w aplikacji serwer proxy aplikacji próbuje rozpoznać adres URL jako wewnętrzny adres URL w tej samej aplikacji lub jako zewnętrzny dostęp do adresu URL. Jeśli link wskazuje na wewnętrzny adres URL, który nie znajduje się w tej samej aplikacji, nie należy do żadnego z tych zasobników i nie spowoduje błędu.

## <a name="ways-you-can-resolve-broken-links"></a>Sposoby rozpoznawania uszkodzonych linków

Istnieją trzy sposoby rozwiązania tego problemu. Poniższe opcje są wymienione w temacie zwiększającym złożoność.

1.  Upewnij się, że wewnętrzny adres URL jest katalogiem głównym zawierającym wszystkie odpowiednie linki dla aplikacji. Dzięki temu wszystkie linki mają być rozpoznawane jako zawartość opublikowana w ramach tej samej aplikacji.

    Jeśli zmienisz wewnętrzny adres URL, ale nie chcesz zmienić strony docelowej dla użytkowników, Zmień adres URL strony głównej na wcześniej opublikowany wewnętrzny adres URL. Można to zrobić, przechodząc do "Azure Active Directory" — &gt; Rejestracja aplikacji — &gt; Wybierz &gt; znakowanie aplikacji. W sekcji znakowania zobaczysz pole "adres URL strony głównej", które można dostosować do żądanej strony docelowej. Jeśli nadal korzystasz ze starszej wersji Rejestracje aplikacji, na karcie właściwości zostanie wyświetlony szczegółowy adres URL strony głównej. 
    
    > [!IMPORTANT]
    > W celu wprowadzenia powyższych zmian wymagane są uprawnienia do modyfikowania obiektów aplikacji w usłudze Azure AD. Użytkownik musi mieć przypisaną rolę [administratora aplikacji](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) , która przyznaje użytkownikowi uprawnienia modificaion do aplikacji w usłudze Azure AD.
    >

2.  Jeśli aplikacje używają w pełni kwalifikowanych nazw domen (FQDN), użyj [domen niestandardowych](application-proxy-configure-custom-domain.md) do publikowania aplikacji. Ta funkcja umożliwia użycie tego samego adresu URL zarówno wewnętrznie, jak i zewnętrznie.

    Ta opcja zapewnia, że linki w aplikacji są dostępne zewnętrznie za pomocą serwera proxy aplikacji, ponieważ linki w aplikacji do wewnętrznych adresów URL są również rozpoznawane zewnętrznie. Wszystkie linki nadal muszą należeć do opublikowanej aplikacji. Jednak w przypadku tej opcji linki nie muszą należeć do tej samej aplikacji i mogą należeć do wielu aplikacji.

3.  Jeśli żadna z tych opcji nie jest osiągalna, istnieje wiele opcji włączania tłumaczenia linków wbudowanych. Te opcje obejmują korzystanie z rozszerzenia Intune Managed Browser, moje aplikacje lub z użyciem ustawienia tłumaczenia linku w aplikacji. Aby dowiedzieć się więcej o każdej z tych opcji i sposobach ich włączania, zobacz [redirect stałe links for Apps opublikowanych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Następne kroki
[Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)

