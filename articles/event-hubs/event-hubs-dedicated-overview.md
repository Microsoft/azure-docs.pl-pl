---
title: Omówienie dedykowanych centrów zdarzeń — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera omówienie dedykowanych Event Hubs platformy Azure, które oferują wdrożenia z jedną dzierżawą centrów zdarzeń.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: fe63b074bfdd01191d81dfb7024af5532a87a12f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310771"
---
# <a name="overview-of-event-hubs-dedicated"></a>Omówienie Event Hubs — warstwa Dedykowana

*Klastry Event Hubs* oferują wdrożenia z jedną dzierżawą dla klientów z najbardziej wymaganymi potrzebami przesyłania strumieniowego. Ta oferta z jedną dzierżawą ma gwarantowaną 99,99% umowy SLA i jest dostępna tylko w naszej dedykowanej warstwie cenowej. Klaster Event Hubs może odbierać miliony zdarzeń na sekundę z gwarantowaną pojemnością i drugim opóźnieniem. Przestrzenie nazw i centra zdarzeń utworzone w dedykowanym klastrze obejmują wszystkie funkcje standardowej oferty i wiele innych, ale bez ograniczeń związanych z transferem danych przychodzących. Zawiera również popularne funkcje [przechwytywania Event Hubs](event-hubs-capture-overview.md) bez dodatkowych kosztów. Ta funkcja umożliwia automatyczne wsadowe i rejestrowanie strumieni danych w usłudze Azure Storage lub Azure Data Lake. 

Klastry są udostępniane i są rozliczane według **jednostek pojemności**, wstępnie przydzielonej ilości zasobów procesora i pamięci. W każdym klastrze można zakupić 1, 2, 4, 8, 12, 16 lub 20 jednostek. Ilość danych do pozyskiwania i przesyłania strumieniowego na jednostkę CU zależy od różnych czynników, takich jak następujące: 

- Liczba producentów i konsumentów
- Kształt ładunku
- Szybkość ruchu wychodzącego

> [!NOTE]
> Wszystkie klastry Event Hubs są domyślnie włączone i obsługują punkty końcowe Kafka, które mogą być używane przez istniejące aplikacje oparte na Kafka. Funkcja Kafka włączona w klastrze nie wpływa na przypadki użycia poza Kafkaem; nie ma opcji ani nie trzeba wyłączać Kafka w klastrze.

## <a name="why-dedicated"></a>Dlaczego są dedykowane?

Dedykowane Event Hubs oferują trzy atrakcyjne korzyści dla klientów, którzy potrzebują pojemności na poziomie przedsiębiorstwa:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Gwarancja jednej dzierżawy w celu zapewnienia lepszej wydajności

Dedykowany klaster gwarantuje pojemność w pełnej skali. Może on przystąpić do gigabajtów danych przesyłanych strumieniowo za pomocą w pełni trwałego magazynu i sekund opóźnienia w celu uwzględnienia obciążeń w ruchu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Włączny i wyłączny dostęp do funkcji 
Oferta dedykowana obejmuje funkcje, takie jak przechwytywanie bez dodatkowych kosztów i wyłączny dostęp do nadchodzących funkcji, takich jak Bring Your Own Key (BYOK). Usługa zarządza również równoważeniem obciążenia, aktualizacjami systemu operacyjnego, poprawkami zabezpieczeń i partycjonowaniem. Dzięki temu można poświęcać mniej czasu na konserwację infrastruktury i więcej czasu na tworzeniu funkcji po stronie klienta.  

#### <a name="cost-savings"></a>Oszczędności kosztów
W przypadku dużych ilości danych wejściowych (>100 jednostek przepływności) klaster znacznie mniej na godzinę niż zakup porównywalnej liczby jednostek przepływności w ramach oferty standardowej.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Przydziały i limity Event Hubs — warstwa Dedykowana

W przypadku oferty Event Hubs — warstwa Dedykowana jest naliczana stała cena miesięczna, a co najmniej 4 godziny użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standard, ale z pojemnością i limitami skali przedsiębiorstwa dla klientów wymagających obciążeń. 

| Cecha | Standardowa (Standard) | Dedykowane |
| --- |:---|:---|
| Przepustowość | 20 TUs (do 40 TUs) | 20 jednostek |
| Przestrzenie nazw |  1 | 50 na CU |
| Event Hubs |  10 na przestrzeń nazw | 1000 na przestrzeń nazw |
| Zdarzenia związane z transferem danych przychodzących | Płatność za milion zdarzeń | Dołączono |
| Rozmiar komunikatu | 1 000 000 bajtów | 1 000 000 bajtów |
| Partycje | 32 za centrum zdarzeń | 1024 za centrum zdarzeń<br/>2000 na CU |
| Grupy odbiorców | 20 na centrum zdarzeń | Brak limitu na wartość CU, 1000 na centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 1 000, maksymalnie 5 000 | 100 K uwzględnione i maks. |
| [Przechowywanie zdarzeń](event-hubs-features.md#event-retention) | 7 dni, 84 GB uwzględnionych na jednostek PRZEPŁYWNOŚCI | 90 dni, 10 TB uwzględnionych na CU |
| Przechwytywanie | Płatność za godzinę | Dołączono |

Aby uzyskać więcej przydziałów i limitów, zobacz [Event Hubs przydziały i limity](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>Jak dołączyć

Samoobsługowe środowisko [tworzenia klastra Event Hubs](event-hubs-dedicated-cluster-create-portal.md) za pomocą [Azure Portal](https://aka.ms/eventhubsclusterquickstart) jest teraz w wersji zapoznawczej. Jeśli masz jakieś pytania lub potrzebujesz pomocy przy dołączaniu do Event Hubs — warstwa Dedykowana, skontaktuj się z [zespołem Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Często zadawane pytania

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z przedstawicielem handlowym firmy Microsoft lub pomoc techniczna firmy Microsoft, aby uzyskać dodatkowe informacje dotyczące Event Hubs — warstwa Dedykowana. Możesz również utworzyć klaster lub dowiedzieć się więcej na temat Event Hubs warstw cenowych, odwiedzając następujące linki:

- [Tworzenie klastra Event Hubs za pomocą Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Cennik Event Hubs — warstwa dedykowana](https://azure.microsoft.com/pricing/details/event-hubs/). Możesz również skontaktować się z przedstawicielem handlowym firmy Microsoft lub pomoc techniczna firmy Microsoft, aby uzyskać dodatkowe informacje dotyczące Event Hubs — warstwa Dedykowana pojemności.
- [Event Hubs często zadawane pytania](event-hubs-faq.yml) zawierają informacje o cenach i odpowiedzi na kilka często zadawanych pytań dotyczących Event Hubs.
