---
title: Korzystanie z funkcji Diagnozuj połączenie w środowisku SSIS Integration Runtime
description: Rozwiązywanie problemów z połączeniami w programie SSIS Integration Runtime przy użyciu funkcji Diagnozuj łączność.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 698a9c062596a3439d95ac0d586854fc6616fdd6
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556549"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Korzystanie z funkcji Diagnozuj połączenie w środowisku SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Problemy z łącznością można znaleźć podczas wykonywania pakietów SQL Server Integration Services (SSIS) w programie SSIS Integration Runtime. Te problemy występują szczególnie w przypadku, gdy w środowisku SSIS Integration Runtime jest przyłączany do sieci wirtualnej platformy Azure.

Rozwiązywanie problemów z łącznością przy użyciu funkcji *Diagnozuj łączność* do testowania połączeń. Ta funkcja znajduje się na stronie monitorowanie programu SSIS Integration Runtime w portalu Azure Data Factory.

 ![Monitorowanie strony — diagnozowanie łączności](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Monitorowanie strony — testowanie połączenia](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

W poniższych sekcjach znajdują się informacje o najbardziej typowych błędach występujących podczas testowania połączeń. W każdej sekcji opisano:

- Kod błędu
- Komunikat o błędzie
- Potencjalne przyczyny błędu:
- Zalecane rozwiązania

## <a name="error-code-invalidinput"></a>Kod błędu: InvalidInput

- **Komunikat o błędzie**: "Upewnij się, że dane wejściowe są poprawne".
- **Potencjalna przyczyna**: dane wejściowe są nieprawidłowe.
- **Zalecenie**: Sprawdź dane wejściowe.

## <a name="error-code-firewallornetworkissue"></a>Kod błędu: FirewallOrNetworkIssue

- **Komunikat o błędzie**: "Upewnij się, że ten port jest otwarty na zaporze/serwerze/sieciowej grupy zabezpieczeń, a sieć jest stabilna".
- **Możliwe przyczyny:**
  - Na serwerze nie jest otwarty port.
  - W sieciowej grupie zabezpieczeń nie jest odrzucany ruch wychodzący na porcie.
  - URZĄDZENIE WUS/Zapora platformy Azure nie ma otwartego portu.
- **Mając**
  - Otwórz port na serwerze.
  - Zaktualizuj grupę zabezpieczeń sieci, aby zezwalała na ruch wychodzący na porcie.
  - Otwórz port w zaporze urządzenie WUS/Azure firewall/Zapora lokalna.

## <a name="error-code-misconfigureddnssettings"></a>Kod błędu: MisconfiguredDnsSettings

- **Komunikat o błędzie**: "Jeśli używasz własnego serwera DNS w sieci wirtualnej przyłączonej przez Azure-SSIS IR, sprawdź, czy może on rozpoznać nazwę hosta".
- **Możliwe przyczyny:**
  -  Wystąpił problem z niestandardowym systemem DNS.
  -  Nie używasz w pełni kwalifikowanej nazwy domeny (FQDN) dla nazwy hosta prywatnego.
- **Mając**
  -  Usuń niestandardowy problem z usługą DNS, aby upewnić się, że może on rozpoznać nazwę hosta.
  -  Użyj nazwy FQDN. Azure-SSIS IR nie będzie automatycznie dołączać własnego sufiksu DNS. Na przykład użyj **<your_private_server>. contoso.com** zamiast **<your_private_server**>.

## <a name="error-code-servernotallowremoteconnection"></a>Kod błędu: ServerNotAllowRemoteConnection

- **Komunikat o błędzie**: "Upewnij się, że serwer zezwala na zdalne połączenia TCP za pośrednictwem tego portu".
- **Możliwe przyczyny:**
  -  Zapora serwera nie zezwala na zdalne połączenia TCP.
  -  Serwer nie jest w trybie online.
- **Mając**
  -  Zezwalaj na zdalne połączenia TCP na zaporze serwera.
  -  Uruchom serwer.
   
## <a name="error-code-misconfigurednsgsettings"></a>Kod błędu: MisconfiguredNsgSettings

- **Komunikat o błędzie**: "Upewnij się, że sieciowej grupy zabezpieczeń sieci wirtualnej zezwala na ruch wychodzący przez ten port. Jeśli używasz usługi Azure ExpressRoute i lub UDR, sprawdź, czy ten port jest otwarty na zaporze/serwerze ".
- **Możliwe przyczyny:**
  -  W sieciowej grupie zabezpieczeń nie jest odrzucany ruch wychodzący na porcie.
  -  URZĄDZENIE WUS/Zapora platformy Azure nie ma otwartego portu.
- **Zaleca**
  -  Zaktualizuj grupę zabezpieczeń sieci, aby zezwalała na ruch wychodzący na porcie.
  -  Otwórz port w zaporze urządzenie WUS/Azure firewall/Zapora lokalna.

## <a name="error-code-genericissues"></a>Kod błędu: GenericIssues

- **Komunikat o błędzie**: "test Connection nie powiodło się z powodu problemów ogólnych".
- **Potencjalna przyczyna**: połączenie testowe napotkało ogólny problem tymczasowy.
- **Zalecenie**: ponów próbę połączenia testowego później. Jeśli ponawianie próby nie pomoże, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

## <a name="error-code-pspingexecutiontimeout"></a>Kod błędu: PSPingExecutionTimeout

- **Komunikat o błędzie**: "Test connection timeout, spróbuj ponownie później".
- **Potencjalna przyczyna**: upłynął limit czasu połączenia testowego.
- **Zalecenie**: ponów próbę połączenia testowego później. Jeśli ponawianie próby nie pomoże, skontaktuj się z zespołem pomocy technicznej Azure Data Factory.

## <a name="error-code-networkinstable"></a>Kod błędu: NetworkInstable

- **Komunikat o błędzie**: "Test connection nieprawidłowo zakończony z powodu niestabilności sieci".
- **Potencjalna przyczyna**: przejściowy problem z siecią.
- **Zalecenie**: Sprawdź, czy sieć serwer lub Zapora jest stabilna.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie projektu SSIS na platformie Azure przy użyciu programu SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [Uruchamianie pakietów SSIS na platformie Azure za pomocą programu SSMS](/sql/integration-services/ssis-quickstart-run-ssms)
- [Planowanie pakietów usług SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)