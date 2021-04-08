---
title: Co to jest wersja testowa? Komercyjne oprogramowanie Microsoft Marketplace
description: Wyjaśnienie funkcji dysku testowego Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96490044"
---
# <a name="what-is-a-test-drive"></a>Co to jest wersja testowa?

Wersja testowa to doskonały sposób na pokazanie oferty dla potencjalnych klientów, dając im możliwość wypróbowania przed zakupieniem, generując wysoce kwalifikowanych potencjalnych klientów i powodującą zwiększenie konwersji. Wersja testowa umożliwia korzystanie z produktu w rzeczywistym scenariuszu implementacji. Klienci, którzy wypróbuje Twój produkt, pokazują jasny cel zakupu podobnego rozwiązania. Skorzystaj z tej funkcji, postępując zgodnie z bardziej zaawansowanymi potencjalnymi klientami.

Klienci korzystają również z dysku testowego. Przez umożliwienie im wcześniejszego wypróbowania produktu, możesz zmniejszyć liczbę operacji zakupu. Ponadto dysk testowy jest wstępnie zainicjowany, tj. klienci nie muszą pobierać, konfigurować ani konfigurować produktu.

## <a name="how-does-it-work"></a>Jak to działa?

Dyski testowe to wystąpienia zarządzane, które uruchamiają rozwiązanie lub aplikację na żądanie dla klientów, którzy go żądają. Po przypisaniu wystąpienia dysku testowego jest ono dostępne do użycia przez tego klienta przez określony okres. Po zakończeniu okresu zostanie on usunięty w celu utworzenia pokoju dla innego klienta.

Jako wydawca możesz zarządzać ustawieniami dysków testowych w centrum partnerskim i konfigurować je. Szczegóły konfiguracji technicznej różnią się w zależności od typu oferty. Aby uzyskać szczegółowe wskazówki, zobacz [konfigurację techniczną na dysku testowym](./test-drive-technical-configuration.md).

Potencjalni klienci odkrywają swój dysk testowy jako CTA w ramach oferty w witrynie [AppSource](https://appsource.microsoft.com/en-US/). Udostępniają one swoje informacje kontaktowe i akceptują Warunki oferty oraz zasady zachowania poufności informacji, a następnie uzyskują dostęp do wstępnie skonfigurowanego środowiska w celu wypróbowania go przez ustalony okres. Klienci otrzymują praktyczne, samodzielne wersje próbne kluczowych funkcji i korzyści produktu, a ty otrzymujesz cenny potencjalny klient.

## <a name="types-of-test-drives"></a>Typy dysków testowych

Istnieją różne dyski testowe dostępne na rynku komercyjnym dla wybranych ofert w zależności od typu produktu, scenariusza i portalu Marketplace:

- Azure Resource Manager
    - Aplikacje platformy Azure
    - SaaS
    - Virtual Machines
- Hostowany dysk testowy
    - Dynamics 365 for Business Central (obecnie nieobsługiwane)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplikacja logiki (tylko w trybie obsługi)
- Power BI

Aby uzyskać szczegółowe informacje na temat konfigurowania jednego z tych dysków testowych, zobacz temat [konfiguracja techniczna systemu testowego](./test-drive-technical-configuration.md). 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager dysk testowy

Ten szablon wdrożenia zawiera wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, korzystają tylko z zasobów platformy Azure. Dla następujących typów ofert jest dostępny Azure Resource Manager dysk testowy: 

- Aplikacje platformy Azure
- SaaS
- Maszyny wirtualne

>[!NOTE]
>Jest to jedyna opcja dysku testowego dla ofert dla maszyn wirtualnych i aplikacji platformy Azure.

### <a name="hosted-test-drive-recommended"></a>Hostowany dysk testowy (zalecane)

Hostowany dysk testowy eliminuje złożoność Instalatora przez umożliwienie hostowi firmy Microsoft i konserwację usługi, która wykonuje wstępne Inicjowanie obsługi administracyjnej użytkowników i zwalnianie. Jeśli masz ofertę na Microsoft AppSource, skompiluj dysk testowy, aby połączyć się z wystąpieniem programu Dynamics AX/CRM. Można użyć następujących typów ofert AppSource:

- Użyj usługi [Dynamics 365 do zaangażowania klientów](partner-center-portal/create-new-customer-engagement-offer.md) w system zaangażowania klientów, taki jak sprzedaż, usługa, usługa projektu i usługa pola.
- Użyj usługi [Dynamics 365 dla operacji](partner-center-portal/create-new-operations-offer.md) dla systemu planowania zasobów przedsiębiorstwa (Finanse i operacyjne), takich jak finanse, operacje i produkcja, łańcuch dostaw.

### <a name="logic-app-test-drive"></a>Dysk testowy aplikacji logiki

Ten typ dysku testowego nie jest hostowany przez firmę Microsoft i używa szablonów Azure Resource Manager (ARM) dla typów ofert Dynamics AX/CRM. Musisz uruchomić szablon ARM, aby utworzyć wymagane zasoby w ramach subskrypcji platformy Azure. Wersja testowa aplikacji logiki jest obecnie dostępna tylko w trybie obsługi i nie jest zalecana przez firmę Microsoft w celu uzyskania szczegółowych informacji na temat konfigurowania dysku testowego aplikacji logiki, zobacz temat [konfiguracja techniczna testowa](./test-drive-technical-configuration.md).

### <a name="power-bi-test-drive"></a>Power BI dysk testowy

Jest to po prostu osadzony link do pulpitu nawigacyjnego skompilowanego niestandardowo. Każdy produkt, który demonstruje tylko interaktywną wizualizację Power BI, powinien używać tego typu dysku testowego.

## <a name="transforming-examples"></a>Przykłady transformacji

Proces przekształcania architektury zasobów na dysk testowy może być zniechęcające. Zapoznaj się z tymi przykładami, jak najlepiej przetwarzać bieżące architektury.

[Przekształcanie szablonu witryny sieci Web w dysk testowy](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Przekształcanie szablonu maszyny wirtualnej w dysk testowy](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Przekształć istniejący szablon Menedżer zasobów na dysk testowy](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generuj potencjalni klienci z dysku testowego

Komercyjny dysk testowy portalu Marketplace jest doskonałym narzędziem dla rynków. Firma Microsoft zaleca, aby podczas uruchamiania w celu wygenerowania większej liczby potencjalnych klientów dla Twojej firmy uwzględnić je w działaniach na rynku. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Jeśli zamkniesz pracę z potencjalnym klientem testowym, pamiętaj o zarejestrowaniu go na platformie [Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Ponadto chcielibyśmy poznać klienta usługi WINS, gdzie dysk testowy odgrywa rolę.

## <a name="other-resources"></a>Inne zasoby

Dodatkowe zasoby dotyczące stacji testowych:

- [Najlepsze rozwiązania dotyczące wersji testowej](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Przegląd](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

## <a name="next-step"></a>Następny krok

- [Wersja testowa konfiguracji technicznej](test-drive-technical-configuration.md)