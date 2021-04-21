---
title: Konfigurowanie usługi Dynamics 365 for Customer Engagement, & usługa PowerApps oferuje konfigurację techniczną Microsoft AppSource (Azure Marketplace)
description: Skonfiguruj usługę Dynamics 365 for Customer Engagement, & usługa PowerApps oferuje konfigurację techniczną Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820474"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Konfigurowanie usługi Dynamics 365 for Customer Engagement & Power Apps konfiguracji technicznej

Na tej stronie zdefiniowano szczegóły techniczne używane do nawiązywania połączenia z ofertą. To połączenie pozwala nam aprowizować Twoją ofertę dla klienta końcowego, jeśli zdecyduje się ją uzyskać.

## <a name="offer-information"></a>Informacje o ofercie

**Podstawowy model licencji** określa, w jaki sposób klienci są przypisywani do aplikacji w Centrum administracyjnym CRM. Wybierz **pozycję Zasób** w przypadku licencjonowania opartego na wystąpieniach lub **Pozycję** Użytkownik, jeśli licencje są przypisane po jednej dla dzierżawy.

Pole **wyboru Wymaga dostępu wychodzącego S2S** i bezpiecznego magazynu CRM umożliwia konfigurację bezpiecznego magazynu CRM lub dostępu wychodzącego serwer-serwer (S2S). Ta funkcja wymaga wyspecjalizowanego uwzględnienia przez zespół usługi Dynamics 365 w fazie certyfikacji. Firma Microsoft skontaktuje się z Tobą w celu wykonania dodatkowych czynności w celu obsługi tej funkcji.

Pozostaw **pole Adres URL konfiguracji aplikacji** puste. Jest on do użytku w przyszłości.

## <a name="crm-package"></a>Pakiet CRM

W **polu Adres URL lokalizacji pakietu** wprowadź adres URL konta Azure Blob Storage, które zawiera przekazany plik ZIP pakietu CRM. Uwzględnij w adresie URL klucz sygnatury dostępu współdzielonego tylko do odczytu, aby firma Microsoft może odebrać Pakiet do weryfikacji.

> [!IMPORTANT]
> Aby uniknąć bloku publikowania, upewnij się, że data wygaśnięcia w adresie URL magazynu obiektów blob nie wygasła. Datę można poprawić, korzystając z zasad. Zalecamy, aby **w przyszłości czas** wygaśnięcia był co najmniej jednym miesiącem.

Zaznacz pole **Istnieje więcej niż jeden pakiet CRM w pliku pakietu,** jeśli ma to zastosowanie. Jeśli tak, pamiętaj, aby dołączyć wszystkie pakiety do pliku zip.

Aby uzyskać szczegółowe informacje na temat sposobu kompilowania pakietu i aktualizowania jego struktury, zobacz Krok 3. Tworzenie pakietu [usługi AppSource dla aplikacji.](/powerapps/developer/common-data-service/create-package-app-appsource)

## <a name="crm-package-availability"></a>Dostępność pakietu CRM

Wybierz **pozycję + Dodaj region,** aby określić regiony geograficzne, w których pakiet CRM będzie dostępny dla klientów. Wdrażanie w następujących suwerennych regionach wymaga specjalnych uprawnień i weryfikacji podczas procesu certyfikacji: [Niemcy,](../germany/index.yml)chmura dla [instytucji rządowych](../azure-government/documentation-government-welcome.md)USA i TIP.

Domyślnie wprowadzony powyżej **adres URL konfiguracji** aplikacji będzie używany dla każdego regionu. Jeśli wolisz, możesz wprowadzić oddzielny adres URL konfiguracji aplikacji dla co najmniej jednego określonego regionu.

Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem do następnej karty w menu nav po lewej stronie, **Co-sell with Microsoft (Współużytkowanie z firmą Microsoft).** Aby uzyskać informacje na temat konfigurowania współpracy sprzedaży z firmą Microsoft (opcjonalnie), zobacz [Co-sell partner engagement (Współs sprzedaży partnerów).](marketplace-co-sell.md) Jeśli nie masz konfiguracji sprzedaży współużytkowej lub zakończono jej pracę, przejdź do **następnych kroków** poniżej.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie zawartości uzupełniającej](dynamics-365-customer-engage-supplemental-content.md)
