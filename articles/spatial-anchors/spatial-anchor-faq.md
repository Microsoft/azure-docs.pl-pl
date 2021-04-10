---
title: Często zadawane pytania
description: Często zadawane pytania dotyczące usługi zakotwiczeń przestrzennych platformy Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: b67dcf038dbe13b8c040f317bb117f3b7a7afdde
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384242"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Często zadawane pytania dotyczące usługi Azure Spatial Anchors

Azure Spatial Anchors to zarządzana usługa w chmurze i platforma programistyczna dla wielu użytkowników i urządzeń umożliwiająca korzystanie ze środowisk rzeczywistości mieszanej z orientacją przestrzenną na urządzeniach HoloLens oraz na urządzeniach z systemem iOS i Android.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Spatial Anchors](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Listy często zadawanych pytań dotyczące produktu Azure Spatial Anchors

**P: które urządzenia obsługują kotwice przestrzenne platformy Azure?**

Odp **.:** Kotwice przestrzenne platformy Azure umożliwiają deweloperom tworzenie aplikacji dla urządzeń z systemem iOS przy użyciu obsługi ARKit oraz na urządzeniach z systemem Android z obsługą ARCore; w przypadku systemów iOS i Android obejmuje to telefony i tablety.

**P: Czy muszę mieć połączenie z chmurą, aby korzystać z kotwic przestrzennych platformy Azure?**

Odp **.:** Kotwice przestrzenne platformy Azure wymagają obecnie połączenia sieciowego z Internetem. Zachęcamy do zgłaszania komentarzy na naszej [witrynie opinii](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**P: Jakie są wymagania dotyczące łączności dla kotwic przestrzennych platformy Azure?**

Odp **.:** Kotwice przestrzenne platformy Azure współdziałają z połączeniami Wi-Fi i urządzeniami przenośnymi szerokopasmowymi.

**P: jak dokładnie mogą znajdować się kotwice przestrzenne platformy Azure?**

Odp **.:** Wiele czynników wpływa na dokładność lokalizowania kotwic — warunków oświetlenia, obiektów w środowisku, a nawet powierzchni, w której zakotwiczenie jest umieszczane. Aby określić, czy dokładność będzie odpowiadać Twoim potrzebom, spróbuj użyć kotwic w środowiskach reprezentatywnych dla planowanego miejsca użycia. W przypadku środowisk, dla którym dokładność nie spełnia Twoich potrzeb, zobacz [Logging and diagnostics in Azure Spatial Anchors (Rejestrowanie i diagnostyka w usłudze Azure Spatial Anchors)](./concepts/logging-diagnostics.md).

**P: jak długo trwa tworzenie i lokalizowanie kotwic?**

Odp **.:** Czas wymagany do tworzenia i lokalizowania kotwic zależy od wielu czynników — połączenia sieciowego, przetwarzania i ładowania urządzenia oraz określonego środowiska. Nasi klienci tworzą aplikacje w wielu branżach, takich jak produkcja przemysłowa, handel detaliczny i przemysł gier, co wskazuje, że usługa zapewnia świetne środowisko użytkownika dla ich scenariuszy.

## <a name="privacy-faq"></a>Często zadawane pytania dotyczące ochrony prywatności

**P: Kiedy moja aplikacja umieszcza kotwicę przestrzenną w miejscu, w którym wszystkie aplikacje mają do niego dostęp?**

Odp **.:** Kotwice są izolowane według konta platformy Azure. Tylko aplikacje, dla których przyznano dostęp do Twojego konta, będą mogły uzyskać dostęp do kotwic w ramach tego konta.

**P: jak kotwice przestrzenne platformy Azure przechowują dane?**

Odp **.:** Wszystkie dane są przechowywane w postaci zaszyfrowanej przy użyciu klucza szyfrowania danych zarządzanych przez firmę Microsoft, a wszystkie dane są przechowywane regionalnie dla każdego z zasobów.

**P: jakie informacje o środowisku są przesyłane i przechowywane w usłudze przy użyciu kotwic przestrzennych platformy Azure? Czy obrazy środowiska są przesyłane i przechowywane?**

Odp **.: podczas** tworzenia lub lokalizowania kotwic obrazy środowiska są przetwarzane na urządzeniu w formacie pochodnym. Ten format pochodny jest przesyłany i przechowywany w ramach usługi.

Aby zapewnić przejrzystość, poniżej znajduje się obraz środowiska i pochodna chmura punktów rozrzedzonych. Chmura punktów przedstawia geometryczną reprezentację środowiska, która jest przesyłana i przechowywane w usłudze. Dla każdego punktu w chmurze punktów rozrzedzonych przesyłamy i przechowujemy jego skrót właściwości wizualnych. Skrót jest tworzony na podstawie danych pikseli, ale nie zawiera żadnych z nich.

Usługa Azure Spatial Anchors jest zgodna z [postanowieniami umowy serwisowej platformy Azure](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) i [oświadczeniem firmy Microsoft o ochronie prywatności](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Środowisko i jej pochodny chmurowy punkt na ](./media/sparse-point-cloud.png)
 *rysunku 1: środowisko i jego pochodna Chmura punktów rozrzedzonych*

**P: Czy można wysyłać informacje diagnostyczne do firmy Microsoft?**

Odp **.: tak**. W usłudze Azure Spatial Anchors istnieje tryb diagnostyczny, z którego mogą korzystać deweloperzy za pomocą interfejsu API usługi Azure Spatial Anchors. Jest to przydatne, gdy na przykład napotkasz środowisko, w którym nie ma możliwości tworzenia ani przewidywalnego znajdowania kotwic. Możemy Cię poprosić o przesłanie raportu diagnostyki zawierającego informacje pomocne w debugowaniu. Aby uzyskać więcej informacji, zobacz [Logging and diagnostics in Azure Spatial Anchors (Rejestrowanie i diagnostyka w usłudze Azure Spatial Anchors)](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Często zadawane pytania dotyczące dostępności i cennika

**P: czy jest udostępniana umowa SLA?**

Odp **.:** Zgodnie z normą dla usług platformy Azure celem jest dostępność większa niż 99,9%. 

**P: Czy mogę opublikować moje aplikacje przy użyciu kotwic Azure przestrzenny w sklepach z aplikacjami? Czy mogę używać kotwic przestrzennych platformy Azure do kluczowych scenariuszy produkcyjnych?**

Odp **.:** Tak, kotwice przestrzenne platformy Azure są ogólnie dostępne i mają standardową umowę SLA usług platformy Azure. Zapraszamy do tworzenia aplikacji dla wdrożeń produkcyjnych i [udostępniania nam opinii](https://feedback.azure.com/forums/919252-azure-spatial-anchors) o produkcie.

**P: czy masz jakieś limity ograniczania przepustowości?**

Odp **.: tak**, mamy limity ograniczania.  Nie oczekujemy, że zostaną osiągnięte podczas zwykłego tworzenia aplikacji i ich testowania. W przypadku wdrożeń produkcyjnych jesteśmy gotowi do obsługi wymagań naszych klientów związanych z wysoką skalowalnością. [Skontaktuj się z nami](mailto:azuremrscontact@microsoft.com) w celu omówienia tych zagadnień.

**P: w jakich regionach są dostępne kotwice przestrzenne platformy Azure?**

Odp **.:** Kotwice przestrzenne platformy Azure są obecnie dostępne w regionie zachodnie stany USA 2, Wschodnie stany USA, Wschodnie stany USA 2, Południowo-środkowe stany USA, Europa Zachodnia, Europa Północna, Południowe Zjednoczone Królestwo i Australia Wschodnia. Dodatkowe regiony będą dostępne w przyszłości.

Oznacza to, że w tych regionach usługa obliczeniowa i magazyn są przeznaczone dla tej usługi. Nie ma jednak żadnych ograniczeń związanych z tym, gdzie znajdują się klienci. 

**P: czy są naliczane opłaty za kotwice przestrzenne platformy Azure?**

Odp **.:** Szczegółowe informacje o cenach można znaleźć na naszej [stronie cennika](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Często zadawane pytania techniczne

**P: jak działają kotwice przestrzenne platformy Azure?**

Odp **.:** Kotwice przestrzenne platformy Azure są zależne od śledzenia rzeczywistości mieszanej/rozszerzonej. Te moduły śledzące służą do obserwacji środowiska za pomocą kamer i śledzenia urządzenia w sześciu stopniach swobody, gdy przemieszcza się ono w przestrzeni.

Posiadając moduł śledzący działający w sześciu stopniach swobody jako blok konstrukcyjny, usługa Azure Spatial Anchors umożliwia określenie pewnych punktów orientacyjnych w rzeczywistym środowisku jako punktów zakotwiczenia. Możesz na przykład użyć punktu zakotwiczenia do renderowania zawartości w określonym miejscu w rzeczywistych warunkach.

Podczas tworzenia takiego punktu zestaw SDK klienta przechwytuje informacje o środowisku wokół tego punktu i przekazuje je do usługi. Jeśli inne urządzenie szuka kotwicy w tym samym miejscu, do usługi przesyłane są podobne dane. Te dane są dopasowywane do wcześniej zapisanych danych dotyczących środowiska. Pozycja kotwicy względem urządzenia zostanie następnie przesłana z powrotem w celu użycia w aplikacji.

**P: jak kotwice przestrzenne platformy Azure integrują się z ARKit i ARCore w systemach iOS i Android?**

Odp **.:** Kotwice przestrzenne platformy Azure wykorzystują natywne możliwości śledzenia ARKit i ARCore. Ponadto nasze zestawy SDK dla systemów iOS i Android oferują funkcje takie jak utrwalanie kotwic w ramach zarządzanej usługi w chmurze oraz umożliwianie aplikacjom ponownego znajdowania tych kotwic za pomocą standardowego połączenia z usługą.

**P: jak kotwice przestrzenne platformy Azure integrują się z usługą HoloLens?**

Odp **.:** Kotwice przestrzenne platformy Azure wykorzystują natywne możliwości śledzenia urządzeń HoloLens. Zapewniamy zestaw SDK usługi Azure Spatial Anchors do tworzenia aplikacji na urządzeniu HoloLens. Zestaw SDK integruje się z natywnymi funkcjami urządzenia HoloLens i udostępnia dodatkowe funkcje. Te funkcje umożliwiają deweloperom aplikacji utrwalanie kotwic w ramach zarządzanej usługi w chmurze oraz ponowne znajdowanie tych kotwic przez aplikacje dzięki nawiązaniu połączenia z usługą.

**P: które platformy i języki obsługują kotwice przestrzenne platformy Azure?**

Odp **.:** Deweloperzy mogą tworzyć aplikacje z zakotwiczeniami przestrzennymi platformy Azure przy użyciu znanych narzędzi i struktur dla ich urządzeń:

- Platforma Unity na urządzeniu HoloLens oraz w systemie iOS i Android
- Platforma Xamarin w systemach iOS i Android
- Platforma SWIFT i Objective-C w systemie iOS
- Platforma Java lub zestaw Android NDK w systemie Android
- Platforma C++/ WinRT na urządzeniu HoloLens

Rozpocznij programowanie od zapoznania się z [dokumentacją](index.yml).

**P: czy działa z Unreal?**

Odp **.:** Pomoc techniczna dla Unreal zostanie uwzględniona w przyszłości.

**P: jakich portów i protokołów używa kotwice przestrzenne platformy Azure?**

Odp **.:** Kotwice przestrzenne platformy Azure komunikują się za pośrednictwem portu TCP 443 przy użyciu protokołu szyfrowanego. W przypadku uwierzytelniania używa [Azure Active Directory](../active-directory/index.yml), które komunikuje się za pośrednictwem protokołu HTTPS przez port 443.