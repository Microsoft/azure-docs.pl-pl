---
title: Konfiguracja i zalecenia dotyczące zabezpieczeń zapory platformy Azure Percept
description: Dowiedz się więcej na temat konfiguracji i zaleceń dotyczących zabezpieczeń zapory platformy Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604417"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Konfiguracja i zalecenia dotyczące zabezpieczeń zapory platformy Azure Percept

Zapoznaj się z poniższymi wskazówkami, aby uzyskać informacje na temat konfigurowania zapór i ogólnych najlepszych rozwiązań dotyczących zabezpieczeń w usłudze Azure Percept.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Konfigurowanie zapór dla platformy Azure Percept DK

Jeśli konfiguracja sieci wymaga jawnie zezwolenia na połączenia realizowane z urządzeń z systemem Azure Percept DK, przejrzyj poniższą listę składników.

Ta lista kontrolna jest punktem początkowym dla reguł zapory:

|Adres URL (* = symbol wieloznaczny)|Wychodzące porty TCP|Użycie|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Azure DK — uwierzytelnianie i autoryzacja modelu SOM|
|*. auth.projectsantacruz.azure.net|443|Azure DK — uwierzytelnianie i autoryzacja modelu SOM|

Ponadto Przejrzyj listę [połączeń używanych przez Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Dodatkowe zalecenia dotyczące wdrażania w środowisku produkcyjnym

Usługa Azure Percept DK oferuje szeroką gamę możliwości zabezpieczeń. Oprócz tych zaawansowanych funkcji zabezpieczeń zawartych w bieżącej wersji Firma Microsoft sugeruje również następujące wytyczne podczas rozważania wdrożeń produkcyjnych:

- Silna ochrona fizyczna urządzenia
- Upewnij się, że włączone jest szyfrowanie danych w spoczynku
- Ciągłe monitorowanie stan urządzeń i szybkie reagowanie na alerty
- Ogranicz liczbę administratorów, którzy mają dostęp do urządzenia

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zabezpieczeniach platformy Azure Percept](./overview-percept-security.md)