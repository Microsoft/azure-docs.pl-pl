---
title: Dostępność regionu i dane miejsca zamieszkania dla usług Azure Communication Services
description: Informacje o miejscu zamieszkania i kwestiach związanych z ochroną prywatności w usłudze Azure Communications Services
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7c522abd04f4a3e480bb5c3e14e78cc03dbd5d86
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888644"
---
# <a name="region-availability-and-data-residency"></a>Dostępność w poszczególnych regionach i miejsce przechowywania danych

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Usługi komunikacyjne Azure są zaangażowane w pomaganie naszym klientom spełnić wymagania dotyczące prywatności i danych osobowych. Jako deweloper korzystający z usług komunikacyjnych z bezpośrednią relacją z ludźmi przy użyciu aplikacji, możesz być kontrolerem swoich danych. Ponieważ usługi Azure Communications Services przechowują te dane w Twoim imieniu, najprawdopodobniej jest to procesor tych danych. Ta strona zawiera podsumowanie, w jaki sposób usługa zachowuje dane oraz jak można identyfikować, eksportować i usuwać te dane.

## <a name="data-residency"></a>Rezydencja danych

Podczas tworzenia zasobu usług komunikacyjnych należy określić lokalizację geograficzną **(nie** centrum danych platformy Azure). Wszystkie dane przechowywane przez usługi komunikacyjne w czasie spoczynku będą przechowywane w tej lokalizacji geograficznej w centrum danych wybranym wewnętrznie przez usługi komunikacyjne. Jednakże dane mogą być tranzytowe lub przetwarzane w innych lokalizacje geograficzneach, te globalne punkty końcowe są niezbędne do zapewnienia wysokiej wydajności i małych opóźnień dla użytkowników końcowych bez względu na ich lokalizację.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Odnosi się do tożsamości usług Azure Communications Services

Aplikacja zarządza relacją między tożsamościami użytkowników ludzkich i usług komunikacyjnych. Aby usunąć dane dla użytkownika ludzkiego, należy usunąć dane obejmujące wszystkie tożsamości usługi komunikacji skorelowane dla danego użytkownika.

Istnieją dwie kategorie danych usługi komunikacyjnej:
- **Dane interfejsu API.** Te dane są tworzone i zarządzane przez interfejsy API usługi komunikacyjnej, typowy przykład komunikatów rozmowy zarządzanych za pośrednictwem interfejsów API rozmowy.
- **Dzienniki Azure monitor** Te dane są tworzone przez usługę i zarządzane za pomocą platformy danych Azure Monitor. Te dane obejmują telemetrię i metryki pomagające zrozumieć użycie usług komunikacyjnych. Nie jest to zarządzane przez interfejsy API usługi komunikacyjnej.

## <a name="api-data"></a>Dane interfejsu API

### <a name="identities"></a>Tożsamości

Usługi komunikacyjne platformy Azure przechowują katalog tożsamości za pomocą interfejsu API [DeleteIdentity](/rest/api/communication/communicationidentity/delete) , aby je usunąć. Usunięcie tożsamości spowoduje odwołanie wszystkich skojarzonych tokenów dostępu i usunięcie ich komunikatów rozmowy. Aby uzyskać więcej informacji na temat usuwania tożsamości, [Zobacz Tę stronę](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Za pomocą Azure Portal lub Azure Resource Manager interfejsów API z usługami komunikacyjnymi, można utworzyć dane osobowe. [Użyj tej strony, aby dowiedzieć się, jak zarządzać danymi osobowymi w systemach Azure Resource Manager.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Zarządzanie numerami telefonów

Usługi komunikacyjne Azure obsługują katalog numerów telefonów skojarzonych z zasobem usług komunikacyjnych. Użyj tych interfejsów API, aby pobrać numery telefonów i je usunąć:
- `Release Phone Number`

### <a name="chat"></a>Czat

Wątki rozmowy i komunikaty są zachowywane do momentu usunięcia ich jawnie. W pełni bezczynny wątek zostanie automatycznie usunięty po upływie 30 dni. [Interfejsy API rozmowy](/rest/api/communication/chat/deletechatmessage/deletechatmessage) umożliwiają pobieranie, wyświetlanie, aktualizowanie i usuwanie komunikatów.

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

Wysłane i odebrane wiadomości SMS są przetwarzane w sposób niedozwolony przez usługę i nie są zachowywane. 

### <a name="pstn-voice-calling"></a>Połączenie głosowe PSTN

Komunikacja audio i wideo jest nieulotnie przetwarzana przez usługę, a żadne dane nie są przechowywane w zasobie innym niż dzienniki Azure Monitor.

### <a name="internet-voice-and-video-calling"></a>Połączenie głosu internetowego i wideo

Komunikacja audio i wideo jest nieulotnie przetwarzana przez usługę, a żadne dane nie są przechowywane w zasobie innym niż dzienniki Azure Monitor.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor i Log Analytics

Usługi komunikacyjne platformy Azure będą pokazywać dane rejestrowania Azure Monitor w celu poznania kondycji operacyjnej i wykorzystania usługi. Niektóre z tych dzienników obejmują tożsamości usługi komunikacyjnej i numery telefonów jako dane pól. Aby usunąć wszystkie potencjalnie dane osobowe, [Użyj tych procedur dla Azure monitor](../../azure-monitor/platform/personal-data-mgmt.md). Możesz również skonfigurować [domyślny okres przechowywania dla Azure monitor](../../azure-monitor/platform/manage-cost-storage.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Żądania podmiotu danych platformy Azure dotyczące Rodo i CCPA](/microsoft-365/compliance/gdpr-dsr-azure?preserve-view=true&view=o365-worldwide)
- [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trust-center/privacy/data-location)
- [Mapa interaktywna Azure — gdzie są moje dane klienta?](https://azuredatacentermap.azurewebsites.net/)