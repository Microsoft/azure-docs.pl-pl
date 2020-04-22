---
title: Drzwi frontowe platformy Azure — obsługa domen wieloznacznych
description: Ten artykuł ułatwia zrozumienie, jak usługi Azure Front Door obsługuje mapowanie domen wieloznacznych i zarządzanie nimi na liście domen niestandardowych.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768307"
---
# <a name="wildcard-domains"></a>Domeny wieloznaczne

Poza domenami i poddomenami wierzchołków można mapować nazwę domeny z symbolami wieloznacznych na listę hostów frontu lub domen niestandardowych w profilu usługi Azure Front Door. Posiadanie domen wieloznacznych w konfiguracji usługi Azure Front Door upraszcza zachowanie routingu ruchu dla wielu poddomen dla interfejsu API, aplikacji lub witryny sieci Web z tej samej reguły routingu. Nie trzeba modyfikować konfiguracji, aby dodać lub określić każdą poddomenę oddzielnie. Na przykład można zdefiniować routing `customer1.contoso.com` `customer2.contoso.com`dla `customerN.contoso.com` programu , a także użyć tej samej reguły routingu i dodać domenę symboli wieloznacznych `*.contoso.com`.

Kluczowe scenariusze, które zostały ulepszone dzięki obsłudze domen wieloznacznych, obejmują:

- Nie musisz dołączać każdej poddomeny w profilu usługi Azure Front Door, a następnie włączyć protokół HTTPS do powiązania certyfikatu dla każdej poddomeny.
- Nie musisz już zmieniać konfiguracji usługi Azure Front Door w przypadku, gdy aplikacja doda nową poddomenę. Wcześniej trzeba było dodać poddomenę, powiązać do niej certyfikat, dołączyć zasady zapory aplikacji sieci web (WAF), a następnie dodać domenę do różnych reguł routingu.

> [!NOTE]
> Obecnie domeny wieloznaczne są obsługiwane tylko za pośrednictwem interfejsu API, programu PowerShell i interfejsu wiersza polecenia platformy Azure. Obsługa dodawania domen wieloznacznych i zarządzania nimi w witrynie Azure portal nie jest dostępna.

## <a name="adding-wildcard-domains"></a>Dodawanie domen wieloznacznych

W sekcji dla hostów front-end lub domen można dodać domenę wieloznaczną. Podobnie jak poddomeny usługi Azure Front Door sprawdza, czy istnieje mapowanie rekordów CNAME dla domeny wieloznacznej. To mapowanie DNS może być bezpośrednim `*.contoso.com` mapowaniem `contoso.azurefd.net`rekordów CNAME, takim jak mapowane na . Można też użyć mapowania tymczasowego afdverify. Na przykład `afdverify.contoso.com` mapowane `afdverify.contoso.azurefd.net` do sprawdzania poprawności mapy rekordów CNAME dla symbolu wieloznacznego.

> [!NOTE]
> Usługa DNS platformy Azure obsługuje rekordy z użyciem symboli wieloznacznych.

Można dodać dowolną liczbę poddomen jednopoziomowych domeny wieloznacznej w hostach front-end, do limitu hostów frontu. Ta funkcja może być wymagana dla:

- Definiowanie innej trasy dla poddomeny niż pozostałe domeny (z domeny wieloznacznej).

- Posiadanie innej zasady WAF dla określonej poddomeny. Na przykład `*.contoso.com` umożliwia `foo.contoso.com` dodawanie bez konieczności ponownego udowodnienia własności domeny. Ale to nie `foo.bar.contoso.com` pozwala, ponieważ nie jest to subdomena `*.contoso.com`jednego poziomu . Aby `foo.bar.contoso.com` dodać bez dodatkowej `*.bar.contosonews.com` weryfikacji własności domeny, należy dodać.

Domeny wieloznaczne i ich poddomeny można dodawać z pewnymi ograniczeniami:

- Jeśli domena wieloznaczna zostanie dodana do profilu usługi Azure Front Door:
  - Nie można dodać domeny wieloznacznych do żadnego innego profilu usługi Azure Front Door.
  - Poddomen pierwszego poziomu domeny z symbolami wieloznaczymi nie można dodać do innego profilu usługi Azure Front Door ani do profilu usługi Azure Content Delivery Network.
- Jeśli poddomena domeny wieloznacznej zostanie dodana do profilu usługi Azure Front Door lub profilu usługi Azure Content Delivery Network, nie można dodać domeny wieloznacznej do innych profili usługi Azure Front Door.
- Jeśli dwa profile (Usługi Azure Front Door lub Azure Content Delivery Network) mają różne poddomeny domeny głównej, nie można dodać domen wieloznacznych do żadnego z profili.

## <a name="certificate-binding"></a>Powiązanie certyfikatu

Aby akceptować ruch HTTPS w domenie wieloznacznych, należy włączyć protokół HTTPS w domenie wieloznacznych. Powiązanie certyfikatu dla domeny wieloznacznych wymaga certyfikatu symboli wieloznacznych. Oznacza to, że nazwa podmiotu certyfikatu powinna mieć również domenę symboli wieloznacznych.

> [!NOTE]
> Obecnie tylko przy użyciu własnej opcji certyfikatu SSL jest dostępna do włączania protokołu HTTPS dla domen wieloznacznych. Certyfikatów zarządzanych usługi Azure Front Door nie można używać dla domen wieloznacznych.

Możesz użyć tego samego certyfikatu symboli wieloznacznych z usługi Azure Key Vault lub certyfikatów zarządzanych usługi Azure Front Door dla poddomen.

Jeśli subdomena jest dodawana dla domeny wieloznacznych, która ma już skojarzony certyfikat, nie można wyłączyć protokołu HTTPS dla poddomeny. Poddomena używa powiązania certyfikatu dla domeny wieloznacznych, chyba że inny certyfikat zarządzany usługi Key Vault lub Usługi Azure Front Door zastępuje go.

## <a name="waf-policies"></a>Zasady WAF

Zasady WAF mogą być dołączone do domen wieloznacznych, podobnie jak inne domeny. Inne zasady WAF można zastosować do poddomeny domeny wieloznacznej. W przypadku poddomen należy określić zasady WAF, które mają być używane, nawet jeśli są to te same zasady co domena wieloznaczna. Poddomeny *nie* dziedziczą automatycznie zasad WAF z domeny symboli wieloznacznych.

Jeśli nie chcesz, aby zasady WAF były uruchamiane dla poddomeny, możesz utworzyć pustą zasadę WAF bez zarządzanych lub niestandardowych planów reguł.

## <a name="routing-rules"></a>Reguły routingu

Podczas konfigurowania reguły routingu można wybrać domenę wieloznaczną jako hosta frontowego. Można również mieć różne zachowanie trasy dla domen wieloznacznych i poddomen. Jak opisano w [jak usługa Azure Front Door nie dopasowywania trasy,](front-door-route-matching.md)najbardziej szczegółowe dopasowanie dla domeny w różnych reguł routingu jest wybierany w czasie wykonywania.

> [!IMPORTANT]
> Musisz mieć pasujące wzorce ścieżek w regułach routingu lub klienci zobaczą błędy. Na przykład masz dwie reguły routingu, takie jak Trasa 1`*.foo.com/*` (mapowana`bar.foo.com/somePath/*` na pulę zaplecza A) i Trasa 2 (mapowana na pulę zaplecza B). Następnie pojawia się `bar.foo.com/anotherPath/*`prośba o . Usługa Azure Front Door wybiera trasę 2 na podstawie bardziej szczegółowego dopasowania domeny, aby znaleźć nie pasujące wzorce ścieżek w trasach.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć profil drzwi y frontowej platformy Azure.](quickstart-create-front-door.md)
- Dowiedz się, jak [dodać domenę niestandardową w drzwiach frontowych platformy Azure](front-door-custom-domain.md).
- Dowiedz się, jak [włączyć protokół HTTPS w domenie niestandardowej](front-door-custom-domain-https.md).
