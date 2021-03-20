---
title: Skonfiguruj domenę niestandardową w usłudze Azure static Web Apps
description: Dowiedz się, jak mapować domenę niestandardową na statyczną Web Apps platformy Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92173689"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Konfigurowanie domeny niestandardowej w usłudze Azure Static Web Apps (wersja zapoznawcza)

Domyślnie usługa Azure static Web Apps zapewnia automatycznie wygenerowaną nazwę domeny. W tym artykule pokazano, jak zmapować niestandardową nazwę domeny do aplikacji statycznej Web Apps platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Nazwa zakupionej domeny
- Dostęp do właściwości konfiguracji DNS dla Twojej domeny

Podczas konfigurowania nazw domen "A" rekordy są używane do mapowania domen głównych (na przykład `example.com` ) na adres IP. Domeny główne muszą być mapowane bezpośrednio na adres IP, ponieważ Specyfikacja DNS nie zezwala na mapowanie jednej domeny na inną.

## <a name="dns-configuration-options"></a>Opcje konfiguracji DNS

Istnieje kilka różnych typów konfiguracji DNS dostępnych dla aplikacji.

| Jeśli chcesz | Następnie |
|--|--|
| Obsługa `www.example.com` lub `blog.example.net` | [Mapowanie rekordu CNAME](#map-a-cname-record) |
| Pomocy `example.com` | [Konfigurowanie domeny głównej](#configure-a-root-domain) |
| Wskaż Wszystkie poddomeny do `www.example.com` | [Mapuj symbol wieloznaczny](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>Mapowanie rekordu CNAME

Rekord CNAME mapuje jedną domenę na inną. Możesz użyć rekordu CNAME do mapowania `www.example.com` , `blog.example.com` lub innej poddomeny, na automatycznie wygenerowaną domenę, która jest dostarczana przez statyczne Web Apps platformy Azure.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

1. Wyszukaj i wybierz pozycję **statyczne Web Apps**

1. Na stronie _statycznej Web Apps_ wybierz nazwę aplikacji.

1. W menu kliknij pozycję **domeny niestandardowe** .

1. Kliknij przycisk **Dodaj**

1. W oknie _domeny niestandardowe_ Skopiuj adres URL w polu **wartość** .

### <a name="configure-dns-provider"></a>Konfigurowanie dostawcy DNS

1. Zaloguj się do witryny internetowej dostawcy domeny.

2. Znajdź stronę służącą do zarządzania rekordami DNS. Każdy dostawca domeny ma własny interfejs rekordów DNS, dlatego zapoznaj się z dokumentacją dostawcy. Poszukaj obszarów witryny z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerami nazw**.

3. Często stronę rekordów DNS można znaleźć, wyświetlając informacje o koncie, a następnie szukając linków takich jak **Moja domena**. Przejdź do tej strony, a następnie poszukaj linku o nazwie podobnej do **pliku strefy**, **rekordy DNS** lub **Konfiguracja zaawansowana**.

    Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Przykładowa konfiguracja dostawcy DNS":::

4. Utwórz nowy rekord **CNAME** o następujących wartościach...

    | Ustawienie             | Wartość                     |
    | ------------------- | ------------------------- |
    | Typ                | CNAME                     |
    | Host                | www                       |
    | Wartość               | Wklej ze schowka |
    | Czas wygaśnięcia (jeśli dotyczy) | Pozostaw jako wartość domyślną    |

5. Zapisz zmiany u dostawcy DNS.

### <a name="validate-cname"></a>Sprawdź poprawność rekordu CNAME

1. Wróć do okna _domeny niestandardowe_ w Azure Portal.

1. Wprowadź domenę, łącznie z `www` częścią w sekcji _Weryfikuj domenę niestandardową_ .

1. Kliknij przycisk **Weryfikuj** .

Po skonfigurowaniu domeny niestandardowej może upłynąć kilka godzin, zanim dostawca DNS propaguje zmiany na całym świecie. Stan propagacji możesz sprawdzić, przechodząc do [dnspropagation.NET](https://dnspropagation.net). Wprowadź domenę niestandardową, w tym `www` , wybierz pozycję CNAME z listy rozwijanej, a następnie wybierz pozycję **Uruchom**.

Jeśli wprowadzono zmiany w systemie DNS, witryna sieci Web zwróci automatycznie wygenerowany adres URL statycznej aplikacji sieci Web (na przykład _Random-Name-123456789c.azurestaticapps.NET_).

## <a name="configure-a-root-domain"></a>Konfigurowanie domeny głównej

Domeny główne to domena pomniejszona o każdą poddomenę, w tym `www` . Na przykład domena główna dla programu `www.example.com` ma wartość `example.com` . Jest to również nazywane domeną "APEX".

Gdy obsługa domeny głównej jest niedostępna w wersji zapoznawczej, można zobaczyć wpis na blogu [Konfigurowanie domen głównych w usłudze Azure Static Web Apps](https://burkeholland.github.io/posts/static-app-root-domain) , aby uzyskać szczegółowe informacje na temat konfigurowania obsługi domeny głównej za pomocą statycznej aplikacji sieci Web.

## <a name="map-a-wildcard-domain"></a>Mapowanie domeny z symbolami wieloznacznymi

Czasami chcesz, aby cały ruch wysyłany do domeny podrzędnej był kierowany do innej domeny. Typowym przykładem jest mapowanie całego ruchu poddomeny do `www.example.com` . W ten sposób, nawet jeśli ktoś wpisze `w.example.com` zamiast `www.example.com` , żądanie jest wysyłane do `www.example.com` .

### <a name="configure-dns-provider"></a>Konfigurowanie dostawcy DNS

1. Zaloguj się do witryny internetowej dostawcy domeny.

2. Znajdź stronę służącą do zarządzania rekordami DNS. Każdy dostawca domeny ma własny interfejs rekordów DNS, dlatego zapoznaj się z dokumentacją dostawcy. Poszukaj obszarów witryny z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerami nazw**.

3. Często stronę rekordów DNS można znaleźć, wyświetlając informacje o koncie, a następnie szukając linków takich jak **Moja domena**. Przejdź do tej strony, a następnie poszukaj linku o nazwie podobnej do **pliku strefy**, **rekordy DNS** lub **Konfiguracja zaawansowana**.

    Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Przykładowa konfiguracja dostawcy DNS":::

4. Utwórz nowy rekord **CNAME** o następujących wartościach, zastępując `www.example.com` je niestandardową nazwą domeny.

    | Ustawienie | Wartość                  |
    | ------- | ---------------------- |
    | Typ    | CNAME                  |
    | Host    | \*                     |
    | Wartość   | www.example.com        |
    | TTL     | Pozostaw jako wartość domyślną |

5. Zapisz zmiany u dostawcy DNS.

Po skonfigurowaniu domeny wieloznacznej może upłynąć kilka godzin, zanim zmiany zostaną rozpropagowane na całym świecie. Stan propagacji możesz sprawdzić, przechodząc do [dnspropagation.NET](https://dnspropagation.net). Wprowadź domenę niestandardową domeny z dowolną poddomeną (inną niż `www` ), wybierz pozycję CNAME z listy rozwijanej i wybierz pozycję **Uruchom**.

Jeśli wprowadzono zmiany w systemie DNS, witryna sieci Web zwróci domenę niestandardową skonfigurowaną dla statycznej aplikacji sieci Web (na przykład `www.example.com` ).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień aplikacji](application-settings.md)
