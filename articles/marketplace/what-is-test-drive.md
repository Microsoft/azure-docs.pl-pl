---
title: Co to jest wersja testowa? Platforma handlowa firmy Microsoft
description: Wyjaśnienie funkcji dysku testowego witryny Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: e44d5d94a8dc172962a26f3e0dae9ccbb7f8a865
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818894"
---
# <a name="what-is-a-test-drive"></a>Co to jest wersja testowa?

Dysk testowy to doskonały sposób, aby zaprezentować swoją ofertę potencjalnym klientom, dając im możliwość wypróbowania przed zakupem, generowania wysoce kwalifikowanych potencjalnych klientów i uzyskania zwiększonej konwersji. Dysk testowy wprowadza produkt do życia w rzeczywistym scenariuszu implementacji. Klienci, którzy wypróbują Twój produkt, jasno demonstrują zamiar zakupu podobnego rozwiązania. Skorzystaj z tego, korzystając z bardziej zaawansowanych potencjalnych klientów.

Klienci korzystają również z dysku testowego. Umożliwienie im wypróbowania najpierw produktu zmniejsza liczbę procesów zakupu. Ponadto wstępnie aprowizowana jest dysk testowy, tzn. klienci nie muszą pobierać, konfigurować ani konfigurować produktu.

## <a name="how-does-it-work"></a>Jak to działa?

Dyski testowe to wystąpienia zarządzane, które uruchamiają Twoje rozwiązanie lub aplikację na żądanie dla klientów, którzy go zażądają. Po przypisaniu wystąpienia wersji testowej jest ono dostępne do użycia przez tego klienta przez określony czas. Po zakończeniu okresu jest on usuwany w celu utworzenia miejsca dla innego klienta.

Jako wydawca konfigurujesz ustawienia dysków testowych i zarządzasz nimi w Partner Center. Szczegóły konfiguracji technicznej różnią się w zależności od typu oferty. Aby uzyskać szczegółowe wskazówki, zobacz [Test drive technical configuration (Konfiguracja techniczna dysku testowego).](./test-drive-technical-configuration.md)

Potencjalni klienci odnajdą Twój testowy dysk jako wezwanie do działania w twojej ofercie w [usłudze AppSource.](https://appsource.microsoft.com/en-US/) Podają informacje kontaktowe i zgadzają się na warunki oferty oraz zasady ochrony prywatności, a następnie uzyskują dostęp do wstępnie skonfigurowanego środowiska w celu wypróbowania go przez ustalony czas. Klienci otrzymują praktyczne, samodzielne wersje próbne najważniejszych funkcji i korzyści twojego produktu, a Ty otrzymujesz cennego potencjalnego klienta.

## <a name="types-of-test-drives"></a>Typy dysków testowych

Dostępne są różne dyski testowe na komercyjnej platformie handlowej dla wybranych ofert w zależności od typu produktu, scenariusza i platformy handlowej, na którym się znajdujesz:

- Azure Resource Manager
    - Aplikacje platformy Azure
    - SaaS
    - Virtual Machines
- Hostowany dysk testowy
    - Dynamics 365 for Business Central (obecnie nie jest obsługiwana)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplikacja logiki (tylko w trybie pomocy technicznej)
- Power BI

Aby uzyskać szczegółowe informacje na temat konfigurowania jednego z tych dysków testowych, zobacz [Konfiguracja techniczna dysku testowego](./test-drive-technical-configuration.md). 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testowa

Ten szablon wdrożenia zawiera wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, używają tylko zasobów platformy Azure. Dysk Azure Resource Manager testowy jest dostępny dla następujących typów ofert: 

- Aplikacje platformy Azure
- SaaS
- Maszyny wirtualne

>[!NOTE]
>Jest to jedyna opcja wersji testowej dla maszyn wirtualnych i ofert aplikacji platformy Azure.

### <a name="hosted-test-drive-recommended"></a>Hostowany dysk testowy (zalecane)

Hostowany dysk testowy usuwa złożoność konfiguracji, umożliwiając hostowi firmy Microsoft i konserwowanie usługi, która wykonuje aprowizowanie i cokaprowizowanie użytkowników na dysku testowym. Jeśli masz ofertę na platformie Microsoft AppSource, skompilować dysk testowy, aby nawiązać połączenie z wystąpieniem dynamics AX/CRM. Możesz użyć następujących typów ofert w usłudze AppSource:

- Użyj [usługi Dynamics 365 for Customer Engagement](dynamics-365-customer-engage-offer-setup.md) i Power Apps dla systemu Customer Engagement, takiego jak sprzedaż, usługa, usługa projektu i usługa w terenie.
- Użyj [usługi Dynamics 365 for Operations dla](partner-center-portal/create-new-operations-offer.md) systemu planowania zasobów przedsiębiorstwa Finance and Operations, takiego jak finanse, operacje i produkcja, łańcuch dostaw.

### <a name="logic-app-test-drive"></a>Test aplikacji logiki

Ten typ dysku testowego nie jest hostowany przez firmę Microsoft i używa szablonów Azure Resource Manager (ARM) dla typów ofert Dynamics AX/CRM. Aby utworzyć wymagane zasoby w subskrypcji platformy Azure, należy uruchomić szablon usługi ARM. Usługa Logic App Test Drive jest obecnie dostępna tylko w trybie pomocy technicznej i nie jest zalecana przez firmę Microsoft. Aby uzyskać szczegółowe informacje na temat konfigurowania dysku testowego aplikacji logiki, zobacz Test drive technical configuration (Konfiguracja techniczna dysku [testowego).](./test-drive-technical-configuration.md)

### <a name="power-bi-test-drive"></a>Power BI testowa

Jest to po prostu osadzony link do niestandardowego pulpitu nawigacyjnego. Każdy produkt, który demonstruje tylko Power BI wizualizacji, powinien używać tego typu dysku testowego.

## <a name="transforming-examples"></a>Przekształcanie przykładów

Proces przekształcania architektury zasobów w dysk testowy może być przytłaczający. Zapoznaj się z tymi przykładami, aby dowiedzieć się, jak najlepiej przekształcić bieżące architektury.

[Przekształcanie szablonu witryny internetowej w dysk testowy](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Przekształcanie szablonu maszyny wirtualnej w dysk testowy](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Przekształcanie istniejącego Resource Manager szablonu na dysk testowy](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generowanie potencjalnych klientów z dysku testowego

Testowa platforma handlowa to doskonałe narzędzie dla marketerów. Zalecamy uwzględnienie go w działaniach w zakresie wprowadzenia na rynek po uruchomieniu w celu wygenerowania większej liczby potencjalnych klientów dla swojej firmy. Aby uzyskać szczegółowe wskazówki, zobacz Customer leads from your commercial marketplace offer (Potencjalni klienci [z oferty platformy handlowej).](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)

Jeśli zamkniesz umowę z potencjalnym użytkownikiem testowym, zarejestruj go w witrynie [Microsoft Partner Sales Connect.](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect) Ponadto chcielibyśmy się dosyć o tym, że klienci wygrają miejsce, w którym w roli odgrywała dysk testowy.

## <a name="other-resources"></a>Inne zasoby

Dodatkowe zasoby na dysku testowym:

- [Najlepsze rozwiązania dotyczące dysków testowych](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Przegląd](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

## <a name="next-step"></a>Następny krok

- [Wersja testowa konfiguracji technicznej](test-drive-technical-configuration.md)