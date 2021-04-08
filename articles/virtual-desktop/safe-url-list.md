---
title: Lista wymaganych adresów URL pulpitu wirtualnego systemu Windows — Azure
description: Lista adresów URL, które należy odblokować, aby upewnić się, że wdrożenie pulpitu wirtualnego systemu Windows działa zgodnie z oczekiwaniami.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 872a67d8d9f41c0c809df54304352b2a5f58e011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251764"
---
# <a name="required-url-list"></a>Lista wymaganych adresów URL

Aby można było wdrożyć i korzystać z pulpitu wirtualnego systemu Windows, należy odblokować niektóre adresy URL, aby maszyny wirtualne mogły uzyskiwać do nich dostęp w dowolnym momencie. Ten artykuł zawiera listę wymaganych adresów URL, które należy odblokować w celu poprawnego funkcjonowania pulpitu wirtualnego systemu Windows. 

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows nie obsługuje wdrożeń, które blokują adresy URL wymienione w tym artykule.

## <a name="virtual-machines"></a>Maszyny wirtualne

Maszyny wirtualne platformy Azure tworzone dla pulpitu wirtualnego systemu Windows muszą mieć dostęp do następujących adresów URL w chmurze komercyjnej platformy Azure:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*. wvd.microsoft.com|443|Ruch usługi|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Ruch agenta|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Ruch agenta|AzureCloud|
|* xt.blob.core.windows.net|443|Ruch agenta|AzureCloud|
|* eh.servicebus.windows.net|443|Ruch agenta|AzureCloud|
|* xt.table.core.windows.net|443|Ruch agenta|AzureCloud|
|* xt.queue.core.windows.net|443|Ruch agenta|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktywacja systemu Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aktualizacje stosu agenta i SXS|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Obsługa Azure Portal|AzureCloud|
| 169.254.169.254 | 80 | [Punkt końcowy usługi metadanych wystąpienia platformy Azure](../virtual-machines/windows/instance-metadata-service.md) | Nie dotyczy |
| 168.63.129.16 | 80 | [Monitorowanie kondycji hosta sesji](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Nie dotyczy |

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows obsługuje teraz tag FQDN. Aby uzyskać więcej informacji, zobacz [Korzystanie z zapory platformy Azure do ochrony wdrożeń pulpitów wirtualnych systemu Windows](../firewall/protect-windows-virtual-desktop.md).
>
>Zalecamy używanie tagów FQDN lub tagów usług zamiast adresów URL, aby zapobiec problemom z usługą. Wymienione adresy URL i Tagi odnoszą się tylko do witryn i zasobów pulpitu wirtualnego systemu Windows. Nie obejmują one adresów URL dla innych usług, takich jak Azure Active Directory.

Maszyny wirtualne platformy Azure tworzone dla pulpitu wirtualnego systemu Windows muszą mieć dostęp do następujących adresów URL w chmurze Azure Government:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*. wvd.microsoft.us|443|Ruch usługi|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|*. servicebus.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Aktywacja systemu Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Aktualizacje stosu agenta i SXS|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Obsługa Azure Portal|AzureCloud|
| 169.254.169.254 | 80 | [Punkt końcowy usługi metadanych wystąpienia platformy Azure](../virtual-machines/windows/instance-metadata-service.md) | Nie dotyczy |
| 168.63.129.16 | 80 | [Monitorowanie kondycji hosta sesji](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Nie dotyczy |

W poniższej tabeli przedstawiono opcjonalne adresy URL, do których maszyny wirtualne platformy Azure mogą mieć dostęp:

|Adres|Wychodzący port TCP|Przeznaczenie|Azure gov|
|---|---|---|---|
|*.microsoftonline.com|443|Uwierzytelnianie w usługach online firmy Microsoft|login.microsoftonline.us|
|*. events.data.microsoft.com|443|Usługa telemetrii|Brak|
|www.msftconnecttest.com|443|Wykrywa, czy system operacyjny jest połączony z Internetem|Brak|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Brak|
|login.windows.net|443|Zaloguj się do usług Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*. sfx.ms|443|Aktualizacje oprogramowania klienckiego usługi OneDrive|oneclient.sfx.ms|
|*. digicert.com|443|Sprawdzenie odwołania certyfikatu|Brak|
|*. azure-dns.com|443|Azure DNS rozwiązanie|Brak|
|*. azure-dns.net|443|Azure DNS rozwiązanie|Brak|

>[!NOTE]
>Pulpit wirtualny systemu Windows nie ma obecnie listy zakresów adresów IP, które można odblokować, aby zezwolić na ruch sieciowy. Obecnie obsługujemy odblokowywanie określonych adresów URL.
>
>Jeśli używasz zapory nowej generacji (zapory następnej generacji), musisz użyć dynamicznej listy przeznaczonej dla adresów IP platformy Azure, aby upewnić się, że można nawiązać połączenie.
>
>Aby uzyskać listę bezpiecznych adresów URL związanych z pakietem Office, w tym wymaganych adresów URL związanych z Azure Active Directory, zobacz [adresy URL i zakresy adresów IP usługi office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Należy użyć symbolu wieloznacznego (*) dla adresów URL związanych z ruchem usługi. Jeśli wolisz używać * w przypadku ruchu związanego z agentem, Oto jak znaleźć adresy URL bez symboli wieloznacznych:
>
>1. Zarejestruj maszyny wirtualne w puli hostów systemu Windows Virtual Desktop.
>2. Otwórz **Podgląd zdarzeń**, a następnie przejdź do pozycji **Dzienniki systemu Windows**  >  **Application**  >  **WVD-Agent** i Wyszukaj zdarzenie o identyfikatorze 3701.
>3. Odblokuj adresy URL, które znajdują się w obszarze zdarzenia o IDENTYFIKATORze 3701. Adresy URL pod IDENTYFIKATORem zdarzenia 3701 są specyficzne dla regionu. Należy powtórzyć proces odblokowywania przy użyciu odpowiednich adresów URL dla każdego regionu, w którym mają zostać wdrożone maszyny wirtualne.

## <a name="remote-desktop-clients"></a>Klienci usług pulpitu zdalnego

Wszyscy klienci korzystający z Pulpit zdalny muszą mieć dostęp do następujących adresów URL:

|Adres|Wychodzący port TCP|Przeznaczenie|Klienci|Azure gov|
|---|---|---|---|---|
|*. wvd.microsoft.com|443|Ruch usługi|Wszystko|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|Rozwiązywanie problemów z danymi|Wszystko|*. servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Linki fwlink firmy Microsoft|Wszystko|Brak|
|aka.ms|443|Shortener URL firmy Microsoft|Wszystko|Brak|
|docs.microsoft.com|443|Dokumentacja|Wszystko|Brak|
|privacy.microsoft.com|443|Oświadczenie o ochronie prywatności|Wszystko|Brak|
|query.prod.cms.rt.microsoft.com|443|Aktualizacje klienta|Pulpit systemu Windows|Brak|

>[!IMPORTANT]
>Otwieranie tych adresów URL jest niezbędne dla niezawodnego środowiska klienta. Blokowanie dostępu do tych adresów URL nie jest obsługiwane i wpłynie na funkcjonalność usługi.
>
>Te adresy URL odnoszą się tylko do lokacji i zasobów klienta. Ta lista nie zawiera adresów URL dla innych usług, takich jak Azure Active Directory. Adresy URL Azure Active Directory można znaleźć pod IDENTYFIKATORem 56 dla [adresów URL i zakresów adresów IP pakietu Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).