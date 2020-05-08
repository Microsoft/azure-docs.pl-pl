---
title: Konfigurowanie ustawień Windows Update do pracy z platformą Azure Update Management
description: W tym artykule opisano ustawienia Windows Update, które można skonfigurować do pracy z usługą Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: b9b5f2b19b29eae0132ec01a9f3fb7e8355361f5
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779454"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Skonfiguruj ustawienia Windows Update dla Update Management

Usługa Azure Update Management opiera się na [kliencie Windows Update](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) do pobierania i instalowania aktualizacji systemu Windows. Istnieją określone ustawienia, które są używane przez klienta Windows Update podczas nawiązywania połączenia z usługą Windows Server Update Services (WSUS) lub Windows Update. Wiele z tych ustawień może być zarządzanych przy użyciu:

- Edytor lokalnych zasad grupy
- Zasady grupy
- PowerShell
- Bezpośrednie edytowanie rejestru

Update Management przestrzega wielu ustawień określonych w celu kontrolowania Windows Update klienta. W przypadku korzystania z ustawień w celu włączenia aktualizacji innych niż Windows program Update Management również będzie zarządzać tymi aktualizacjami. Jeśli chcesz włączyć pobieranie aktualizacji przed wystąpieniem wdrożenia aktualizacji, wdrożenie aktualizacji może być szybsze, wydajniejsze i mniejsze niż okno obsługi.

Aby uzyskać dodatkowe zalecenia dotyczące konfigurowania usług WSUS w ramach subskrypcji platformy Azure i bezpiecznego utrzymania Aktualności maszyn wirtualnych z systemem Windows, zapoznaj się z tematem [Planowanie wdrożenia w celu aktualizacji maszyn wirtualnych z systemem Windows na platformie Azure przy użyciu usług WSUS](https://docs.microsoft.com/azure/architecture/example-scenario/wsus/).

## <a name="pre-download-updates"></a>Aktualizacje przed pobraniem

Aby skonfigurować automatyczne pobieranie aktualizacji, ale nie instalować ich automatycznie, można użyć zasady grupy, aby ustawić [ustawienie Skonfiguruj aktualizacje automatyczne](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. To ustawienie włącza pobieranie wymaganych aktualizacji w tle i powiadamia, że aktualizacje są gotowe do zainstalowania. W ten sposób Update Management pozostaje w kontroli nad harmonogramami, ale aktualizacje można pobrać poza oknem obsługi Update Management. To zachowanie uniemożliwia **przekroczenie błędów okna obsługi** w Update Management.

To ustawienie można włączyć za pomocą programu PowerShell, uruchamiając następujące polecenie:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurowanie ustawień ponownego uruchamiania

Klucze rejestru wymienione w temacie [Konfigurowanie aktualizacji automatycznych przez edycję rejestru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) i [kluczy rejestru używanych do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować ponowne uruchomienie maszyn, nawet jeśli w ustawieniach **wdrożenia aktualizacji** określono opcję **nigdy nie uruchamiaj ponownie** . Skonfiguruj te klucze rejestru, aby najlepiej odpowiadały Twojemu środowisku.

## <a name="enable-updates-for-other-microsoft-products"></a>Włącz aktualizacje dla innych produktów firmy Microsoft

Domyślnie program Windows Update Client jest skonfigurowany do udostępniania tylko aktualizacji dla systemu Windows. Jeśli włączysz ustawienie **Chcę otrzymywać aktualizacje dla innych produktów firmy Microsoft podczas aktualizacji systemu Windows** , uzyskasz także aktualizacje dla innych produktów, w tym poprawki zabezpieczeń dla Microsoft SQL Server i innego oprogramowania firmy Microsoft. Tę opcję można skonfigurować w przypadku pobrania i skopiowania najnowszych [plików szablonów administracyjnych](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) dostępnych dla systemu Windows 2016 i nowszych.

W przypadku korzystania z systemu Windows Server 2012 R2 nie można skonfigurować tego ustawienia za pomocą zasady grupy. Na tych maszynach Uruchom następujące polecenie programu PowerShell. Update Management jest zgodny z tym ustawieniem.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Ustawienia konfiguracji programu WSUS

Update Management obsługuje ustawienia usług WSUS. Ustawienia programu WSUS, które można skonfigurować do pracy z Update Management są wymienione poniżej.

### <a name="intranet-microsoft-update-service-location"></a>Lokalizacja intranetowej usługi aktualizacji firmy Microsoft

Możesz określić źródła do skanowania i pobierania aktualizacji w obszarze [Określ lokalizację intranetową Microsoft Update usługi](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Domyślnie program Windows Update Client jest skonfigurowany do pobierania aktualizacji z Windows Update. W przypadku określenia serwera programu WSUS jako źródła dla maszyn, jeśli aktualizacje nie są zatwierdzone w programie WSUS, wdrożenie aktualizacji nie powiedzie się. 

Aby ograniczyć liczbę komputerów tylko do usługi aktualizacji wewnętrznej, skonfiguruj [nie łącz się z dowolnymi Windows Update lokalizacjami internetowymi](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień Windows Update można zaplanować wdrożenie aktualizacji, postępując zgodnie z instrukcjami w temacie [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
