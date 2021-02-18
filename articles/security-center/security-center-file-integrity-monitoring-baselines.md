---
title: Monitorowanie integralności plików w usłudze Azure Security Center
description: Dowiedz się, jak porównać linie bazowe z monitorowaniem integralności plików w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f160948a06d8a3175158e0100345ee2a330048c
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634716"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porównanie planów bazowych przy użyciu monitorowania integralności plików (FIM)

Program do monitorowania integralności plików (FIM) informuje o zmianach w obszarach poufnych zasobów, dzięki czemu można zbadać i rozwiązać nieautoryzowane działanie. Program FIM monitoruje pliki systemu Windows, rejestry systemu Windows i pliki Linux.

W tym temacie wyjaśniono, jak włączyć program FIM dla plików i rejestrów. Aby uzyskać więcej informacji na temat programu FIM, zobacz [monitorowanie integralności plików w Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Dlaczego warto korzystać z programu FIM?

System operacyjny, aplikacje i skojarzone konfiguracje sterują zachowaniem i stanem zabezpieczeń zasobów. W związku z tym, osoby atakujące są przeznaczone dla plików kontrolujących Twoje zasoby, aby overtake system operacyjny zasobu i/lub wykonywać działania bez wykrycia.

W rzeczywistości wiele standardów zgodności z przepisami, takich jak PCI-DSS & ISO 17799, wymaga zaimplementowania kontrolek FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Włącz wbudowane testy cykliczne rejestru

Domyślne ustawienia gałęzi rejestru programu FIM zapewniają wygodny sposób monitorowania zmian cyklicznych w ramach wspólnych obszarów zabezpieczeń.  Na przykład atakującej może skonfigurować skrypt do wykonania w kontekście LOCAL_SYSTEM przez skonfigurowanie wykonania przy uruchamianiu lub zamknięciu.  Aby monitorować zmiany tego typu, Włącz wbudowane sprawdzanie.  

![Rejestr](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Testy cykliczne mają zastosowanie tylko do zalecanych gałęzi zabezpieczeń, a nie do niestandardowych ścieżek rejestru.  

## <a name="add-a-custom-registry-check"></a>Dodawanie niestandardowego sprawdzania rejestru

Linie bazowe programu FIM zaczynają się od określenia cech znanego dobrego stanu dla systemu operacyjnego i aplikacji pomocniczej.  Na potrzeby tego przykładu będziemy skupić się na konfiguracjach zasad haseł dla systemu Windows Server 2008 i nowszych.


|Policy Name (Nazwa zasad)                 | Ustawienie rejestru|
|---------------------------------------|-------------|
|Kontroler domeny: odrzucaj zmiany hasła konta komputera| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Członek domeny: szyfruj lub podpisuj cyfrowo dane bezpiecznego kanału — zawsze|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Członek domeny: szyfruj cyfrowo dane bezpiecznego kanału — gdy to możliwe|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Członek domeny: podpisuj cyfrowo dane bezpiecznego kanału — gdy to możliwe|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Członek domeny: wyłącz zmiany hasła konta komputera|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Członek domeny: maksymalny wiek hasła konta komputera|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Członek domeny: wymagaj silnego klucza sesji (system Windows 2000 lub nowszy)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpieczenia sieciowe: ograniczanie ruchu NTLM: uwierzytelnianie NTLM w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpieczenia sieciowe: Ograniczanie ruchu NTLM: Dodaj wyjątki dla serwerów z tej domeny|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpieczenia sieciowe: ograniczanie ruchu NTLM: Przeprowadź inspekcję uwierzytelniania NTLM w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Aby dowiedzieć się więcej na temat ustawień rejestru obsługiwanych przez różne wersje systemu operacyjnego, zapoznaj się z [arkuszem kalkulacyjnym informacje o ustawieniach zasady grupy](https://www.microsoft.com/download/confirmation.aspx?id=25250).

Aby skonfigurować FIM do monitorowania planów bazowych rejestru:

1. W oknie dialogowym **Dodawanie rejestru systemu Windows dla Change Tracking** w polu tekstowym **klucz rejestru systemu Windows** wprowadź następujący klucz rejestru:

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="Włączanie programu FIM w rejestrze":::

## <a name="track-changes-to-windows-files"></a>Śledzenie zmian w plikach systemu Windows

1. W oknie **Dodawanie pliku systemu Windows dla Change Tracking** w polu tekstowym **wprowadź ścieżkę** wprowadź folder zawierający pliki, które chcesz śledzić. W przykładzie na poniższej ilustracji znajduje się **aplikacja sieci Web firmy Contoso** w D:\ dysk w strukturze folderów **ContosWebApp** .  
1. Utwórz niestandardowy wpis w pliku systemu Windows, podając nazwę klasy ustawień, włączając rekursję i określając folder Top z sufiksem symboli wieloznacznych (*).

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="Włącz program FIM na pliku":::

## <a name="retrieve-change-data"></a>Pobieranie danych zmiany

Dane monitorowania integralności plików znajdują się w zestawie tabel Log Analytics/Zmianakonfiguracji platformy Azure.  

 1. Ustaw zakres czasu, aby pobrać podsumowanie zmian według zasobu.
W poniższym przykładzie pobieramy wszystkie zmiany w ciągu ostatnich czternastu dni w kategorii rejestru i plików:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Aby wyświetlić szczegóły zmian w rejestrze:

    1. Usuń **pliki** z klauzuli **WHERE** , 
    1. Usuń wiersz podsumowania i zastąp go klauzulą porządkowania:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Raporty można eksportować do pliku CSV w celu archiwizacji i/lub kanału do raportu Power BIowego.  

![Dane programu FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)