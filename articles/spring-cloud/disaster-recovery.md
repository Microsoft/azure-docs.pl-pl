---
title: Azure sprężynowe w chmurze — odzyskiwanie po awarii | Microsoft Docs
description: Dowiedz się, jak chronić aplikację w chmurze ze sprężyną przed awarią regionalną
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92092906"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Odzyskiwanie po awarii w chmurze z platformy Azure

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

W tym artykule opisano niektóre strategie, których można użyć do ochrony Twoich aplikacji w chmurze platformy Azure przed wykorzystaniem przestojów.  W każdym regionie lub centrum danych może wystąpić przestój spowodowany przez regionalne awarie, ale staranne planowanie może ograniczyć wpływ na klientów.

## <a name="plan-your-application-deployment"></a>Planowanie wdrożenia aplikacji

Aplikacje chmurowe platformy Azure są uruchamiane w określonym regionie.  Platforma Azure działa w wielu lokalizacjach geograficznych na całym świecie. Lokalizacja geograficzna platformy Azure to zdefiniowany obszar świata, który zawiera co najmniej jeden region świadczenia usługi Azure. Region świadczenia usługi Azure jest obszarem geograficznym zawierającym co najmniej jedno centrum danych.  Każdy region platformy Azure jest sparowany z innym regionem w tej samej lokalizacji geograficznej, tworząc parę regionalną. Platforma Azure serializacji aktualizacje platformy (planowana konserwacja) między parami regionalnymi, co zapewnia, że tylko jeden region w każdej parze jest aktualizowany jednocześnie. W przypadku awarii mającej wpływ na wiele regionów, co najmniej jeden region w każdej parze będzie miał priorytet na potrzeby odzyskiwania.

Zapewnienie wysokiej dostępności i ochrony przed awariami wymaga wdrożenia aplikacji w chmurze ze sprężyną w wielu regionach.  Platforma Azure udostępnia listę [par regionów](../best-practices-availability-paired-regions.md) , aby można było zaplanować wdrożenia w chmurze ze sprężyną regionalną.  Zaleca się, aby wziąć pod uwagę trzy kluczowe czynniki podczas projektowania architektury mikrousług: dostępność regionów, sparowane regiony platformy Azure i dostępność usług.

*  Dostępność regionu: Wybierz obszar geograficzny blisko użytkowników, aby zminimalizować czas opóźnienia i transmisji w sieci.
*  Sparowane regiony platformy Azure: Wybierz sparowane regiony w wybranym obszarze geograficznym, aby zapewnić skoordynowane aktualizacje platformy i priorytetowe działania odzyskiwania w razie potrzeby.
*  Dostępność usługi: należy zdecydować, czy sparowane regiony powinny uruchamiać gorącą, gorącą, gorącą/zimną.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Kierowanie ruchu przy użyciu usługi Azure Traffic Manager

Usługa [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) zapewnia Równoważenie obciążenia ruchem opartym na systemie DNS i umożliwia dystrybucję ruchu sieciowego między wieloma regionami.  Użyj usługi Azure Traffic Manager, aby skierować klientów do najbliższego wystąpienia usługi w chmurze ze sprężyną na platformie Azure.  Aby uzyskać najlepszą wydajność i nadmiarowość, należy skierować cały ruch aplikacji za pośrednictwem platformy Azure Traffic Manager przed wysłaniem go do usługi w chmurze Azure wiosennej.

Jeśli masz aplikacje chmurowe platformy Azure w wielu regionach, Użyj usługi Azure Traffic Manager, aby kontrolować przepływ ruchu do aplikacji w poszczególnych regionach.  Zdefiniuj punkt końcowy Traffic Manager platformy Azure dla każdej usługi korzystającej z adresu IP usługi. Klienci powinni łączyć się z nazwą DNS usługi Azure Traffic Manager, wskazując na usługę Azure wiosennej w chmurze.  Usługa Azure Traffic Manager równoważy obciążenie ruchu między zdefiniowanymi punktami końcowymi.  Jeśli awaria zostanie wyświetlona w centrum danych, usługa Azure Traffic Manager przekieruje ruch z tego regionu do pary, zapewniając ciągłość usługi.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Tworzenie usługi Azure Traffic Manager dla chmury wiosennej platformy Azure

1. Utwórz chmurę z platformą Azure z dwoma różnymi regionami.
W dwóch różnych regionach (Wschodnie stany USA i Europa Zachodnia) będą potrzebne dwa wystąpienia usługi Azure wiosennej. Uruchom istniejącą aplikację w chmurze platformy Azure przy użyciu Azure Portal, aby utworzyć dwa wystąpienia usługi. Każdy z nich będzie obsługiwał jako podstawowy i bezawaryjny punkt końcowy dla ruchu. 

**Dwie informacje o wystąpieniach usługi:**

| Nazwa usługi | Lokalizacja | Aplikacja |
|--|--|--|
| Service — przykład-a | East US | Brama/uwierzytelnianie-usługa/konto — usługa |
| Service-Sample-b | West Europe | Brama/uwierzytelnianie-usługa/konto — usługa |

2. Skonfiguruj domenę niestandardową dla usługi, postępując zgodnie z [dokumentem domena niestandardowa](spring-cloud-tutorial-custom-domain.md) , aby skonfigurować domenę niestandardową dla tych dwóch istniejących wystąpień usługi. Po pomyślnym skonfigurowaniu oba wystąpienia usługi zostaną powiązane z domeną niestandardową: bcdr-test.contoso.com

3. Utwórz Menedżera ruchu i dwa punkty końcowe: [Utwórz profil Traffic Manager przy użyciu Azure Portal](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Oto profil Menedżera ruchu:
* Traffic Manager nazwę DNS: `http://asc-bcdr.trafficmanager.net`
* Profile punktów końcowych: 

| Profil | Typ | Cel | Priorytet | Niestandardowe ustawienia nagłówka |
|--|--|--|--|--|
| Punkt końcowy profilu | Zewnętrzny punkt końcowy | service-sample-a.asc-test.net | 1 | Host: bcdr-test.contoso.com |
| Profil punktu końcowego B | Zewnętrzny punkt końcowy | service-sample-b.asc-test.net | 2 | Host: bcdr-test.contoso.com |

4. Utwórz rekord CNAME w strefie DNS: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. Środowisko jest teraz całkowicie skonfigurowane. Klienci powinni mieć dostęp do aplikacji za pośrednictwem: bcdr-test.contoso.com

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny](spring-cloud-quickstart.md)
