---
title: Śledzenie przepływu w aplikacji Cloud Services (klasycznej) za pomocą Diagnostyka Azure
description: Dodawanie komunikatów śledzenia do aplikacji platformy Azure w celu ułatwienia debugowania, mierzenia wydajności, monitorowania, analizy ruchu i nie tylko.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b00bb28128cfe9a2e701647ad174ea2c9dd458e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742129"
---
# <a name="trace-the-flow-of-a-cloud-services-classic-application-with-azure-diagnostics"></a>Śledzenie przepływu aplikacji Cloud Services (klasycznej) z Diagnostyka Azure

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Śledzenie służy do monitorowania wykonywania aplikacji w trakcie jej działania. Za pomocą klas [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace), [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug)i [System. Diagnostics. TraceSource](/dotnet/api/system.diagnostics.tracesource) można rejestrować informacje o błędach i wykonywaniu aplikacji w dziennikach, plikach tekstowych lub innych urządzeniach w celu późniejszej analizy. Aby uzyskać więcej informacji na temat śledzenia, zobacz [śledzenie i Instrumentacja aplikacji](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Używanie instrukcji Trace i przełączników śledzenia
Zaimplementuj śledzenie w aplikacji Cloud Services, dodając [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) do konfiguracji aplikacji i wykonując wywołania do System. Diagnostics. Trace lub system. Diagnostics. Debug w kodzie aplikacji. Użyj *app.config* pliku konfiguracji dla ról procesów roboczych i *web.config* dla ról sieci Web. Podczas tworzenia nowej usługi hostowanej przy użyciu szablonu programu Visual Studio Diagnostyka Azure jest automatycznie dodawana do projektu i DiagnosticMonitorTraceListener jest dodawany do odpowiedniego pliku konfiguracji dla ról, które zostały dodane.

Aby uzyskać informacje dotyczące umieszczania instrukcji śledzenia, zobacz [How to: Dodawanie instrukcji Trace do kodu aplikacji](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Umieszczając [przełączniki śledzenia](/dotnet/framework/debug-trace-profile/trace-switches) w kodzie, można kontrolować, czy śledzenie ma się odbywać i jak obszerne. Umożliwia to monitorowanie stanu aplikacji w środowisku produkcyjnym. Jest to szczególnie ważne w przypadku aplikacji biznesowej, która używa wielu składników uruchomionych na wielu komputerach. Aby uzyskać więcej informacji, zobacz [How to: Configure Trace switchs](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurowanie odbiornika śledzenia w aplikacji platformy Azure
Trace, Debug i TraceSource, wymagają skonfigurowania "odbiorników" do zbierania i rejestrowania wysyłanych komunikatów. Odbiorniki zbierają, przechowują i rozsyłają komunikaty śledzenia. Kierują one dane wyjściowe śledzenia do odpowiedniego obiektu docelowego, takiego jak dziennik, okno lub plik tekstowy. Diagnostyka Azure używa klasy [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) .

Przed wykonaniem poniższej procedury należy zainicjować monitor diagnostyki platformy Azure. Aby to zrobić, zobacz [Włączanie diagnostyki w Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Należy pamiętać, że jeśli używasz szablonów dostarczonych przez program Visual Studio, konfiguracja odbiornika jest automatycznie dodawana.

### <a name="add-a-trace-listener"></a>Dodawanie odbiornika śledzenia
1. Otwórz plik web.config lub app.config dla roli.
2. Dodaj następujący kod do pliku. Zmień atrybut wersji, aby użyć numeru wersji zestawu, którego dotyczy odwołanie. Wersja zestawu nie zmienia się koniecznie w przypadku każdej wersji zestawu Azure SDK, chyba że są w niej dostępne aktualizacje.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Upewnij się, że masz odwołanie do projektu zestawu Microsoft. WindowsAzure. Diagnostics. Zaktualizuj numer wersji w kodzie XML powyżej, aby odpowiadał wersji zestawu Microsoft. WindowsAzure. Diagnostics.
   > 
   > 
3. Zapisz plik konfiguracji.

Aby uzyskać więcej informacji na temat odbiorników, zobacz [detektory śledzenia](/dotnet/framework/debug-trace-profile/trace-listeners).

Po wykonaniu kroków w celu dodania odbiornika można dodać do niego instrukcje śledzenia.

### <a name="to-add-trace-statement-to-your-code"></a>Aby dodać instrukcję trace do kodu
1. Otwórz plik źródłowy aplikacji. Na przykład \<RoleName> plik CS dla roli procesu roboczego lub roli sieci Web.
2. Dodaj następującą dyrektywę using, jeśli nie została jeszcze dodana:
    ```
        using System.Diagnostics;
    ```
3. Dodaj instrukcje śledzenia, w których chcesz przechwytywać informacje o stanie aplikacji. Aby sformatować dane wyjściowe instrukcji Trace, można użyć różnych metod. Aby uzyskać więcej informacji, zobacz [jak: dodać instrukcje śledzenia do kodu aplikacji](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Zapisz plik źródłowy.




