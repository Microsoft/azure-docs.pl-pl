---
title: Rozwiązywanie problemów diagnozowania łączności w środowisku SSIS Integration Runtime
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów w celu zdiagnozowania łączności w środowisku SSIS Integration Runtime
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172716"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Rozwiązywanie problemów diagnozowania łączności w środowisku SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W przypadku wystąpienia problemów z łącznością podczas wykonywania pakietów usług SSIS w środowisku uruchomieniowym środowiska SSIS Korzystając z tej funkcji, można spróbować przeprowadzić samodiagnozowanie problemów przy użyciu tej usługi do diagnozowania łączności SSIS na stronie monitorowanie środowiska Integration Runtime w portalu Azure Data Factory. 

 ![Monitorowanie strony — ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ Strona Monitorowanie łączności — testowanie połączenia](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Ten artykuł zawiera najczęstsze błędy, które można napotkać podczas testowania połączenia w programie SSIS Integration Runtime. Opisuje potencjalne przyczyny i akcje rozwiązywania błędów. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Typowe błędy, potencjalne przyczyny i rozwiązania rekomendacji

### <a name="error-code-invalidinput"></a>Kod błędu: InvalidInput.
* **Komunikat o błędzie**: Sprawdź, czy dane wejściowe są poprawne.
* **Możliwe przyczyny:** Dane wejściowe są nieprawidłowe.
* **Zalecenie:** Sprawdź dane wejściowe.

### <a name="error-code-firewallornetworkissue"></a>Kod błędu: FirewallOrNetworkIssue.
* **Komunikat o błędzie**: Sprawdź, czy ten port jest otwarty na zaporze/serwerze/sieciowej grupy zabezpieczeń, a sieć jest stabilna.
* **Możliwe przyczyny:** 
  * Serwer nie otworzył tego portu.
  * W sieciowej grupie zabezpieczeń odmówiono ruchu wychodzącego na tym porcie
  * Zapora urządzenie WUS/Zapora platformy Azure Premium nie otwiera tego portu.
* **Zaleca** 
  * Otwórz ten port na serwerze.
  * Zaktualizuj sieciową grupę zabezpieczeń, aby zezwalała na ruch wychodzący na tym porcie.
  * Otwórz ten port w zaporze urządzenie WUS/Azure firewall/Premium.

### <a name="error-code-misconfigureddnssettings"></a>Kod błędu: MisconfiguredDnsSettings.
* **Komunikat o błędzie**: Jeśli używasz własnego serwera DNS w sieci wirtualnej przyłączonej przez Azure-SSIS IR, sprawdź, czy może on rozpoznać nazwę hosta.
* **Możliwe przyczyny:** 
  *  Problem z niestandardowym systemem DNS
  *  Nie używasz w pełni kwalifikowanej nazwy domeny (FQDN) dla nazwy hosta prywatnego
* **Zaleca** 
  *  Usuń niestandardowy problem z usługą DNS, aby upewnić się, że może on rozpoznać nazwę hosta.
  *  Użyj w pełni kwalifikowanej nazwy domeny (FQDN), np. Użyj <your_private_server>. contoso.com zamiast <your_private_server>, ponieważ Azure-SSIS IR nie będzie automatycznie dołączać własnego sufiksu DNS.

### <a name="error-code-servernotallowremoteconenction"></a>Kod błędu: ServerNotAllowRemoteConenction.
* **Komunikat o błędzie**: Upewnij się, że serwer zezwala na zdalne połączenia TCP za pośrednictwem tego portu.
* **Możliwe przyczyny:** 
  *  Zapora serwera nie zezwala na zdalne połączenia TCP.
  *  Serwer nie jest w trybie online.
* **Zaleca** 
  *  Zezwalaj na zdalne połączenia TCP na zaporze serwera.
  *  Uruchom serwer.
   
### <a name="error-code-misconfigurednsgsettings"></a>Kod błędu: MisconfiguredNsgSettings.
* **Komunikat o błędzie**: Upewnij się, że sieciowej grupy zabezpieczeń sieci wirtualnej zezwala na ruch wychodzący przez ten port. Jeśli używasz usługi Azure ExpressRoute i lub UDR, sprawdź, czy ten port jest otwarty na zaporze/serwerze.
* **Możliwe przyczyny:** 
  *  W sieciowej grupie zabezpieczeń odmówiono ruchu wychodzącego na tym porcie.
  *  Zapora urządzenie WUS/Zapora platformy Azure Premium nie otwiera tego portu.
* **Zaleca** 
  *  Zaktualizuj sieciową grupę zabezpieczeń, aby zezwalała na ruch wychodzący na tym porcie.
  *  Otwórz ten port w zaporze urządzenie WUS/Azure firewall/Premium.

### <a name="error-code-genericissues"></a>Kod błędu: GenericIssues.
* **Komunikat o błędzie**: Test connection nie powiódł się z powodu problemów ogólnych.
* **Możliwe przyczyny:** Połączenie testowe napotkało ogólny problem tymczasowy.
* **Zalecenie:** Ponów próbę połączenia testowego później. Jeśli ponawianie próby nie pomoże, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.


### <a name="error-code-pspingexecutiontimeout"></a>Kod błędu: PSPingExecutionTimeout.
* **Komunikat o błędzie**: Test connection limitu czasu, spróbuj ponownie później.
* **Możliwe przyczyny:** Przekroczono limit czasu połączenia testowego.
* **Zalecenie:** Ponów próbę połączenia testowego później. Jeśli ponawianie próby nie pomoże, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

### <a name="error-code-networkinstable"></a>Kod błędu: NetworkInstable.
* **Komunikat o błędzie**: Test connection nieprawidłowo zakończony z powodu niestabilności sieci.
* **Możliwe przyczyny:** Przejściowy problem z siecią.
* **Zalecenie:** Sprawdź, czy sieć serwer lub Zapora jest stabilna.

## <a name="next-steps"></a>Następne kroki

- Wdróż pakiety. Aby uzyskać więcej informacji, zobacz [wdrażanie projektu SSIS na platformie Azure za pomocą programu SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Uruchom pakiety. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS na platformie Azure za pomocą programu SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Zaplanuj pakiety. Aby uzyskać więcej informacji, zobacz [Planowanie pakietów usług SSIS na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

