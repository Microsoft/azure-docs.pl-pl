---
title: Co to jest wersja testowa? Komercyjne oprogramowanie Microsoft Marketplace
description: Wyjaśnienie funkcji dysku testowego Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: bd3b77f80a414dd3db1d5106929fa0e215e34c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121871"
---
# <a name="what-is-a-test-drive"></a>Co to jest wersja testowa?

Wersja testowa to świetny sposób na pokazanie oferty potencjalnym klientom, dając im możliwość *wypróbowania przed zakupieniem*, co spowodowało zwiększoną konwersję i generowanie wysoce wykwalifikowanych klientów. Wersja testowa umożliwia korzystanie z produktu w rzeczywistym scenariuszu implementacji, a także generowanie wysoce kwalifikowanych potencjalnych klientów.

Dyski testowe to wystąpienia zarządzane, które wdrażają swoje rozwiązanie lub aplikacje na żądanie dla klientów, którzy go żądają. Po przypisaniu wystąpienia dysku testowego jest on dostępny do użycia przez określony czas, a następnie usuwany w celu utworzenia pokoju dla innego klienta.

Jako wydawca możesz zarządzać ustawieniami dysków testowych w centrum partnerskim i konfigurować je. Szczegóły konfiguracji technicznej różnią się w zależności od typu oferty, z którą pracujesz. Aby uzyskać szczegółowe wskazówki, zobacz link w [następnym kroku](#next-step) na końcu tego tematu.

Potencjalni klienci odnajdują twój dysk testowy na komercyjnym rynku. Udostępniają one swoje informacje kontaktowe i akceptują Warunki oferty oraz zasady zachowania poufności informacji, a następnie uzyskują dostęp do wstępnie skonfigurowanego środowiska w celu wypróbowania go przez ustalony czas. Klienci otrzymują praktyczne, samodzielne wersje próbne kluczowych funkcji i korzyści produktu, a ty otrzymujesz cenny potencjalny klient.

## <a name="how-does-it-work"></a>Jak to działa?

Jako wydawca możesz zarządzać ustawieniami dysków testowych w centrum partnerskim i konfigurować je. Po zakończeniu instalacji będzie to wystąpienie zarządzane, które zostanie wdrożone na żądanie dla klienta wysyłającego żądanie. Po przypisaniu wystąpienia dysku testowego jest on dostępny do użycia przez określony czas, a następnie usuwany w celu utworzenia pokoju dla innego klienta.

## <a name="types-of-test-drives"></a>Typy dysków testowych

Istnieją różne dyski testowe dostępne na rynku komercyjnym dla wybranych ofert w zależności od typu produktu, scenariusza i portalu Marketplace:

- Azure Resource Manager
- Hostowany dysk testowy
    - Dynamics 365 for Business Central
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplikacja logiki
- Power BI

Aby uzyskać szczegółowe informacje na temat konfigurowania jednego z tych dysków testowych, zapoznaj się z linkiem w [następnym kroku](#next-step) na końcu tego tematu.

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager dysk testowy

Ten szablon wdrożenia zawiera wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, korzystają tylko z zasobów platformy Azure. Dla następujących typów ofert jest dostępny Azure Resource Manager dysk testowy: 

- Aplikacje platformy Azure
- Dynamics 365 for Business Central
- Dynamics 365 dla zaangażowania klienta & PowerApps
- Dynamics 365 for Operations
- SaaS
- Maszyny wirtualne

>[!NOTE]
>Jest to jedyna opcja dysku testowego dla ofert dla maszyn wirtualnych i aplikacji platformy Azure.

### <a name="hosted-test-drive"></a>Hostowany dysk testowy

Hostowany dysk testowy eliminuje złożoność instalacji przez umożliwienie hostowi firmy Microsoft i konserwację usługi, która przeprowadza testowanie, wdrażanie i Inicjowanie obsługi administracyjnej użytkowników. Jeśli masz ofertę na Microsoft AppSource, skompiluj dysk testowy, aby nawiązać połączenie z wystąpieniem programu Dynamics AX/CRM lub innym zasobem poza platformą Azure. Użyj tego typu dla ofert AppSource, aby połączyć się z następującymi ofertami usługi Dynamics 365:

- Użyj programu [Dynamics 365 for Business Central](partner-center-portal/create-new-operations-offer.md) dla firmowego systemu planowania zasobów przedsiębiorstwa, na przykład finansów, operacji, łańcucha dostaw i programu CRM.
- Użyj usługi [Dynamics 365 do zaangażowania klientów](partner-center-portal/create-new-customer-engagement-offer.md) w system zaangażowania klientów, taki jak sprzedaż, usługa, usługa projektu i usługa pola.
- Użyj usługi [Dynamics 365 dla operacji](partner-center-portal/create-new-operations-offer.md) dla systemu planowania zasobów przedsiębiorstwa (Finanse i operacyjne), takich jak finanse, operacje i produkcja, łańcuch dostaw.

### <a name="logic-app-test-drive"></a>Dysk testowy aplikacji logiki

Ten typ dysku testowego nie jest hostowany przez firmę Microsoft. Służy do nawiązywania połączenia z ofertą Dynamics 365 lub innym zasobem niestandardowym.

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

- [Najlepsze rozwiązania techniczne](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Przegląd](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

## <a name="next-step"></a>Następny krok

- [Wersja testowa konfiguracji technicznej](test-drive-technical-configuration.md)
