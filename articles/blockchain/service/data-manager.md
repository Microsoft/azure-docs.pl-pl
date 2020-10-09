---
title: Co to jest łańcucha bloków Data Manager dla usługi Azure łańcucha bloków Service
description: Łańcucha bloków Data Manager do przechwytywania, przekształcania i dostarczania danych łańcucha bloków do tematów Event Grid.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77209447"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Co to jest menedżer danych łańcucha bloków dla usługi Azure Blockchain Service?

Łańcucha bloków Data Manager przechwytuje, przekształca i dostarcza dane transakcji usługi Azure łańcucha bloków Service do Azure Event Grid tematów zapewniających niezawodną i skalowalną integrację z księgą łańcucha bloków z usługami platformy Azure.

W większości scenariuszy łańcucha bloków w przedsiębiorstwie, Księga łańcucha bloków jest jedną częścią rozwiązania. Na przykład aby przenieść zasób z jednej jednostki do innej, wymagany jest mechanizm przesyłania transakcji. Następnie potrzebny jest mechanizm odczytywania danych księgi w celu upewnienia się, że transakcja została zaakceptowana, a następnie wprowadzone zmiany stanu są zintegrowane z kompleksowym rozwiązaniem. W tym przykładzie, jeśli napiszesz kontrakt inteligentny do transferu zasobów, możesz użyć łańcucha bloków Data Manager, aby zintegrować aplikacje w łańcuchu i magazyny danych. W przypadku transferu zasobów Przykładowo, gdy element zawartości jest transferowany w łańcucha bloków, zmiany stanu zdarzeń i właściwości są dostarczane przez łańcucha bloków Data Manager za pośrednictwem Event Grid. Następnie można użyć wielu możliwych programów obsługi zdarzeń, aby Event Grid do przechowywania danych poza łańcuchami lub reagowania na zmiany stanu w czasie rzeczywistym.

Łańcucha bloków Data Manager wykonuje trzy główne funkcje: Przechwytywanie, przekształcanie i dostarczanie.

![Funkcje Data Manager łańcucha bloków](./media/data-manager/functions.png)

## <a name="capture"></a>Przechwytywanie

Każde wystąpienie Data Manager łańcucha bloków nawiązuje połączenie z jednym węzłem transakcji elementu członkowskiego usługi Azure łańcucha bloków. Tylko użytkownicy z dostępem do węzła transakcji mogą utworzyć połączenie zapewniające odpowiednią kontrolę dostępu do danych klienta. Wystąpienie Data Manager łańcucha bloków niezawodnie przechwytuje wszystkie nieprzetworzone dane nieprzetworzonych danych i nieprzetworzonych transakcji z węzła transakcji oraz może skalować do obsługi obciążeń przedsiębiorstwa.

## <a name="transform"></a>Przekształcanie

Za pomocą łańcucha bloków Data Manager można zdekodować zdarzenie i stan właściwości przez skonfigurowanie aplikacji inteligentnych kontraktów w łańcucha bloków Data Manager. Aby dodać kontrakt inteligentny, należy podać ABI kontraktu i kod bajtowy. Łańcucha bloków Data Manager używa artefaktów kontraktu inteligentnego do dekodowania i odnajdywania adresów kontraktu. Po dodaniu aplikacji łańcucha bloków do wystąpienia łańcucha bloków Data Manager dynamicznie odnajduje adres kontraktu inteligentnego w przypadku wdrożenia kontraktu inteligentnego w ramach konsorcjum i wysyła zdekodowane zdarzenie i stan właściwości do skonfigurowanych miejsc docelowych.

## <a name="deliver"></a>Dostarczanie

Łańcucha bloków Data Manager obsługuje wiele połączeń wychodzących tematu Event Grid dla danego wystąpienia Data Manager łańcucha bloków. Dane łańcucha bloków można wysyłać do jednego miejsca docelowego lub wysyłać dane łańcucha bloków do wielu miejsc docelowych. Korzystając z łańcucha bloków Data Manager, można utworzyć skalowalne rozwiązanie do publikowania danych oparte na zdarzeniach dla dowolnego wdrożenia łańcucha bloków.

## <a name="configuration-options"></a>Opcje konfiguracji

Data Manager można skonfigurować w taki sposób, aby spełniały potrzeby rozwiązania. Można na przykład udostępnić:

* Pojedyncze wystąpienie łańcucha bloków Data Manager dla elementu członkowskiego usługi Azure łańcucha bloków.
* Wystąpienie Data Manager łańcucha bloków na węzeł transakcji usługi Azure łańcucha bloków. Na przykład prywatne węzły transakcji mogą mieć własne wystąpienie łańcucha bloków Data Manager, aby zachować poufność.
* Wystąpienie Data Manager łańcucha bloków może obsługiwać wiele połączeń wyjściowych. Jedno wystąpienie Data Manager łańcucha bloków może służyć do zarządzania wszystkimi punktami integracji publikowania danych dla elementu członkowskiego usługi Azure łańcucha bloków.

## <a name="next-steps"></a>Następne kroki

Spróbuj [utworzyć wystąpienie Data Manager łańcucha bloków](data-manager-portal.md) dla elementu członkowskiego usługi Azure łańcucha bloków.
