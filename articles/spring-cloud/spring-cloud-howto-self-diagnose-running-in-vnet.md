---
title: Jak przeprowadzić samodiagnostyknie sieci wirtualnej chmury z obsługą platformy Azure
description: Dowiedz się, jak automatycznie diagnozować i rozwiązywać problemy w chmurze Azure wiosny działającej w sieci wirtualnej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125101"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Samoobsługowe diagnozowanie działającej chmury platformy Azure w sieci wirtualnej
Diagnostyka chmurowa platformy Azure obsługuje interaktywne Rozwiązywanie problemów z aplikacjami uruchomionymi w sieciach wirtualnych bez konfiguracji. Diagnostyka chmurowa platformy Azure służy do identyfikowania problemów i prowadzi do informacji ułatwiających rozwiązywanie problemów i ich rozwiązywanie.

## <a name="navigate-to-the-diagnostics-page"></a>Przejdź do strony Diagnostyka
Poniższa procedura uruchamia diagnostykę dla aplikacji sieciowych.
1. Zaloguj się w witrynie Azure Portal.
1. Przejdź do strony omówienia chmury Azure wiosennej.
1. Wybierz opcję **Diagnozuj i rozwiąż problemy** w menu w okienku nawigacji po lewej stronie.
1. Wybierz trzecią kategorię, **Sieć**.

   ![Tytuł samodiagnostyki](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Wyświetl raport diagnostyczny
Po kliknięciu kategorii **Sieć** można wyświetlić dwa problemy związane z siecią, które są specyficzne dla sieci wirtualnej, z możliwością korzystania z chmury wiosennej platformy Azure: **rozpoznawanie nazw DNS** i **wymagany ruch wychodzący**.

   ![Opcje samodiagnostyki](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Wybierz docelowy problem, aby wyświetlić raport diagnostyczny. Zostanie wyświetlona podsumowanie informacji diagnostycznych, takich jak: 

* *Zasób został usunięty.*
* *Zasób nie został wdrożony w Twojej sieci wirtualnej*.

Niektóre wyniki zawierają powiązaną dokumentację. Różne podsieci będą wyświetlać wyniki oddzielnie.

## <a name="dns-resolution"></a>Rozpoznawanie nazw DNS 
W przypadku wybrania **rozpoznawania nazw DNS** wyniki będą wskazywać, czy występują problemy z usługą DNS dotyczące aplikacji.  Aplikacje w dobrej kondycji są wymienione w następujący sposób:

* *Rozwiązano problemy z usługą DNS bez problemów w podsieci "subnet01"*.
* *Rozwiązano problemy z usługą DNS bez problemów w podsieci "subnet02"*.

Poniższy przykład raportu diagnostycznego wskazuje, że kondycja aplikacji jest nieznana. W przedziale czasowym raportowania nie jest uwzględniony czas, w którym zgłoszono stan kondycji.  Załóżmy, że czas zakończenia kontekstu to *2021-03-03T04:20:00Z*. Najnowsza SYGNATURa CZASowa w przypadku **renderowania tabeli rozpoznawania nazw DNS** to *2021-03-03T03:39:00Z*, poprzedni dzień. Dziennik sprawdzania kondycji mógł nie zostać wysłany z powodu zablokowanej sieci. 

Wyniki nieznanego stanu kondycji zawierają powiązaną dokumentację.  Możesz kliknąć lewy nawias kątowy, aby wyświetlić listę rozwijaną.

   ![Nieznane DNS](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

W przypadku nieprawidłowych konfiguracji zestawu rekordów strefy Prywatna strefa DNS otrzymasz krytyczny wynik, taki jak: `Failed to resolve the Private DNS in subnet xxx` . 

Z listy rozwijanej w **tabeli rozpoznawania nazw DNS** znajdują się szczegółowe informacje o komunikatach, z których można sprawdzić konfigurację.

## <a name="required-outbound-traffic"></a>Wymagany ruch wychodzący 

W przypadku wybrania **wymaganego ruchu wychodzącego** wyniki będą wskazywać, czy występują problemy z ruchem wychodzącym w aplikacjach.  Aplikacje w dobrej kondycji są wymienione w następujący sposób:

* * Wymagany ruch wychodzący został rozwiązany bez problemów w podsieci "subnet01".
* * Wymagany ruch wychodzący został rozwiązany bez problemów w podsieci "subnet02".

Jeśli dowolna podsieć jest blokowana przez sieciowej grupy zabezpieczeń lub reguły zapory, a jeśli dziennik nie został zablokowany, zostaną znalezione następujące błędy. Możesz sprawdzić, czy zostały przeszukane jakieś [obowiązki klientów](spring-cloud-vnet-customer-responsibilities.md).
    
   ![Nie można wykonać punktu końcowego](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Jeśli w `Required Outbound Traffic Table Renderings` ciągu 30 minut nie ma danych, wynik będzie mieć wartość `health status unknown` . Być może sieć jest zablokowana lub usługa log nie działa.

   ![Nieznany punkt końcowy diagnostyki](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Zobacz też
* [Jak przeprowadzić samodiagnostyknie chmury Wiosnowej platformy Azure](spring-cloud-howto-self-diagnose-solve.md)