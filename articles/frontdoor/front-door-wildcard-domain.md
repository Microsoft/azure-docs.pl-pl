---
title: Platformy Azure — obsługa drzwi z symbolami wieloznacznymi
description: Ten artykuł pomaga zrozumieć, w jaki sposób platforma Azure front-drzwi obsługuje mapowanie i Zarządzanie domenami wieloznacznymi na liście domen niestandardowych.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425766"
---
# <a name="wildcard-domains"></a>Domeny wieloznaczne

Oprócz domen wierzchołków i poddomen można także zmapować domenę symboli wieloznacznych na hosty frontonu lub domeny niestandardowe dla profilu drzwi platformy Azure. Posiadanie wieloznacznych domen w konfiguracji drzwi platformy Azure upraszcza zachowanie routingu ruchu dla wielu poddomen dla interfejsu API, aplikacji lub witryny sieci Web z tej samej reguły routingu. Nie trzeba modyfikować konfiguracji, aby dodać lub określić każdą poddomenę osobno. Można na przykład zdefiniować Routing dla `customer1.contoso.com` , `customer2.contoso.com` i za `customerN.contoso.com` pomocą tej samej reguły routingu i dodać domenę wieloznaczną `*.contoso.com` .

Najważniejsze scenariusze, które zostały ulepszone z obsługą domen symboli wieloznacznych, to m.in.:

- Nie musisz dołączać każdej poddomeny w profilu usługi Azure front-drzwi, a następnie włączyć protokół HTTPS, aby powiązać certyfikat dla każdej poddomeny.
- Nie jest już wymagane zmiana konfiguracji produkcyjnej drzwi platformy Azure, jeśli aplikacja doda nową poddomenę. Wcześniej trzeba było dodać poddomenę, powiązać z nią certyfikat, dołączyć zasady zapory aplikacji sieci Web (WAF), a następnie dodać domenę do różnych reguł routingu.

> [!NOTE]
> Obecnie Dodawanie domen wieloznacznych za pośrednictwem Azure DNS jest obsługiwane tylko za pośrednictwem interfejsu API, programu PowerShell i interfejsu wiersza polecenia platformy Azure. Obsługa dodawania domen wieloznacznych i zarządzania nimi w Azure Portal jest niedostępna.

## <a name="adding-wildcard-domains"></a>Dodawanie domen wieloznacznych

W sekcji dla hostów lub domen frontonu można dodać domenę symboli wieloznacznych. Podobnie jak w przypadku poddomen, drzwiczki platformy Azure sprawdzają, czy istnieją mapowania rekordów CNAME dla domeny wieloznacznej. To mapowanie DNS może być bezpośrednim mapowaniem rekordu CNAME, takiego jak `*.contoso.com` mapowany do `contoso.azurefd.net` . Można też użyć tymczasowego mapowania afdverify. Na przykład `afdverify.contoso.com` mapowane do `afdverify.contoso.azurefd.net` walidacji mapy rekordów CNAME dla symbolu wieloznacznego.

> [!NOTE]
> Usługa DNS platformy Azure obsługuje rekordy z użyciem symboli wieloznacznych.

Na hostach frontonu można dodać dowolną liczbę poddomen domen z symbolem wielopoziomowym, do limitu hostów frontonu. Ta funkcjonalność może być wymagana w przypadku:

- Definiowanie innej trasy dla domeny podrzędnej niż reszta domen (z domeny z symbolem wieloznacznym).

- Posiadanie innych zasad WAF dla określonej domeny podrzędnej. Na przykład `*.contoso.com` zezwala na dodawanie `foo.contoso.com` bez konieczności ponownego posiadania własności domeny. Ale nie jest to dozwolone `foo.bar.contoso.com` , ponieważ nie jest poddomeną na poziomie jednego poziomu `*.contoso.com` . Aby dodać `foo.bar.contoso.com` bez sprawdzania poprawności własności domeny, należy `*.bar.contosonews.com` dodać.

Można dodawać symbole wieloznaczne i ich poddomeny z pewnymi ograniczeniami:

- Jeśli domena z symbolami wieloznacznymi zostanie dodana do profilu usługi Azure front-drzwi:
  - Nie można dodać domeny z symbolami wieloznacznymi do żadnego profilu usługi Azure front-drzwi.
  - Nie można dodać poddomen pierwszego poziomu domeny z symbolami wieloznacznymi do innego profilu platformy Azure z systemem lub profilu usługi Azure Content Delivery Network.
- Jeśli poddomena domeny z symbolem wieloznacznym została już dodana do profilu usługi Azure front-drzwi lub profilu Content Delivery Network platformy Azure, nie można używać domeny z symbolami wieloznacznymi dla innych profilów platformy Azure.
- Jeśli dwa profile (z przodu platformy Azure lub na platformie Azure Content Delivery Network) mają różne poddomeny domeny głównej, nie można dodać domen symboli wieloznacznych do jednego z tych profilów.

## <a name="certificate-binding"></a>Powiązanie certyfikatu

W przypadku akceptowania ruchu HTTPS w domenie wieloznacznej należy włączyć protokół HTTPS w domenie symboli wieloznacznych. Powiązanie certyfikatu dla domeny wieloznacznej wymaga certyfikatu z symbolem wieloznacznym. Oznacza to, że nazwa podmiotu certyfikatu powinna również mieć domenę wieloznaczną.

> [!NOTE]
> Obecnie do włączenia protokołu HTTPS dla domen wieloznacznych jest dostępna tylko opcja korzystania z własnej niestandardowej opcji certyfikatu protokołu SSL. W przypadku domen wieloznacznych nie można używać certyfikatów z możliwością zarządzania drzwiami platformy Azure.

Można wybrać użycie tego samego certyfikatu wieloznacznego z Azure Key Vault lub z certyfikatów zarządzanych przez usługę Azure Front-submains dla poddomen.

Jeśli poddomena zostanie dodana dla domeny wieloznacznej, z którą jest już skojarzony certyfikat, nie można wyłączyć protokołu HTTPS dla domeny podrzędnej. Poddomena używa powiązania certyfikatu dla domeny z symbolami wieloznacznymi, chyba że inny Key Vault lub certyfikat zarządzany przez program Azure front-drzwi nie zastępuje go.

## <a name="waf-policies"></a>Zasady WAF

Zasady WAFymi można dołączać do domen z symbolami wieloznacznymi, podobnie jak w przypadku innych domen. Inne zasady WAF można zastosować do poddomeny w domenie wieloznacznej. W przypadku poddomen należy określić zasady WAF, które mają być używane, nawet jeśli są to te same zasady, co w przypadku domeny wieloznacznej. Poddomeny *nie dziedziczą automatycznie zasad* WAF z domeny wieloznacznej.

Jeśli nie chcesz uruchamiać zasad WAFymi dla poddomeny, możesz utworzyć puste zasady WAFymi bez reguł zarządzanych lub niestandardowych.

## <a name="routing-rules"></a>Reguły routingu

Podczas konfigurowania reguły routingu można wybrać domenę wieloznaczną jako hosta frontonu. Istnieje również różne zachowanie trasy dla domen symboli wieloznacznych i poddomen. Zgodnie z opisem w temacie [jak usługa Azure Front-drzwiczki kieruje Dopasowywanie](front-door-route-matching.md)do siebie, w czasie wykonywania wybiera się najbardziej szczegółowe dopasowanie dla domeny w różnych regułach routingu.

> [!IMPORTANT]
> Musisz mieć zgodne wzorce ścieżki w regułach routingu lub klienci będą widzieć błędy. Na przykład istnieją dwie reguły routingu, takie jak Route 1 ( `*.foo.com/*` zamapowane do puli zaplecza a) i trasy 2 ( `/bar.foo.com/somePath/*` mapowane na pulę zaplecza B). Następnie zostanie odebrane żądanie `bar.foo.com/anotherPath/*` . Drzwi frontonu platformy Azure wybierają pozycję Route 2 w oparciu o bardziej szczegółowe dopasowanie domeny, aby znaleźć niezgodne wzorce ścieżki w ramach tras.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć profil dla drzwi platformy Azure](quickstart-create-front-door.md).
- Dowiedz się, jak [dodać domenę niestandardową na platformie Azure front-drzwi](front-door-custom-domain.md).
- Dowiedz się, jak [włączyć protokół https w domenie niestandardowej](front-door-custom-domain-https.md).
