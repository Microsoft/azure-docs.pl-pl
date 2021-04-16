---
title: Windows Virtual Desktop listy wymaganych adresów URL — Azure
description: Listę adresów URL, które należy odblokować, aby upewnić się, że wdrożenie Windows Virtual Desktop działa zgodnie z zamierzeniami.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 00ae761af44b9e6537149c96607c0ba00e6439c8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514989"
---
# <a name="required-url-list"></a>Lista wymaganych adresów URL

Aby wdrożyć i używać Windows Virtual Desktop, należy odblokować określone adresy URL, aby maszyny wirtualne w dowolnym momencie mogą uzyskać do nich dostęp. W tym artykule wymieniono wymagane adresy URL, które należy odblokować, aby Windows Virtual Desktop działać prawidłowo. 

>[!IMPORTANT]
>Windows Virtual Desktop nie obsługuje wdrożeń, które blokują adresy URL wymienione w tym artykule.

## <a name="required-url-check-tool"></a>Narzędzie do sprawdzania wymaganego adresu URL

Narzędzie Sprawdzanie wymaganego adresu URL zweryfikuje adresy URL i wyświetli, czy adresy URL wymagane do działania maszyny wirtualnej są dostępne. Jeśli nie, narzędzie będzie wyświetlić listę niedostępnych adresów URL, aby w razie potrzeby je odblokować.

Ważne jest, aby pamiętać o następujących kwestiach:

- W przypadku wdrożeń w chmurach komercyjnych można używać tylko narzędzia Do sprawdzania wymaganego adresu URL.
- Narzędzie Wymagane sprawdzanie adresu URL nie może sprawdzać adresów URL z symbolami wieloznacznymi, dlatego najpierw upewnij się, że te adresy URL są odblokowane.

### <a name="requirements"></a>Wymagania

Do korzystania z narzędzia do sprawdzania wymaganego adresu URL potrzebne są następujące elementy:

- Maszyna wirtualna musi mieć platformę .NET 4.6.2
- RdAgent w wersji 1.0.2944.400 lub wyższej
- Plik WVDAgentUrlTool.exe musi znajdować się w tym samym folderze co WVDAgentUrlTool.config plików

### <a name="how-to-use-the-required-url-check-tool"></a>Jak używać narzędzia do sprawdzania wymaganego adresu URL

Aby użyć narzędzia do sprawdzania wymaganego adresu URL:

1. Otwórz wiersz polecenia jako administrator na maszynie wirtualnej.
2. Uruchom następujące polecenie, aby zmienić katalog na ten sam folder co agent kompilacji:

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Uruchom następujące polecenie:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. Po uruchomieniu pliku zostanie wyświetlona lista dostępnych i niedostępnych adresów URL.

    Na przykład poniższy zrzut ekranu przedstawia scenariusz, w którym należy odblokować dwa wymagane adresy URL bez symboli wieloznacznych:

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający dane wyjściowe z niedostępnymi adresami URL.](media/noaccess.png)
    
    Oto jak powinny wyglądać dane wyjściowe po odblokowaniu wszystkich wymaganych adresów URL bez symboli wieloznacznych:

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający dostępne dane wyjściowe adresów URL.](media/access.png)

## <a name="virtual-machines"></a>Maszyny wirtualne

Maszyny wirtualne platformy Azure, które tworzysz dla Windows Virtual Desktop muszą mieć dostęp do następujących adresów URL w chmurze komercyjnej platformy Azure:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*.wvd.microsoft.com|443|Ruch w usłudze|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Ruch agenta|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Ruch agenta|AzureCloud|
|*Xt.blob.core.windows.net|443|Ruch agenta|AzureCloud|
|*Eh.servicebus.windows.net|443|Ruch agenta|AzureCloud|
|*Xt.table.core.windows.net|443|Ruch agenta|AzureCloud|
|*xt.queue.core.windows.net|443|Ruch agenta|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktywacja systemu Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aktualizacje stosu agentów i SXS|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal pomocy technicznej|AzureCloud|
| 169.254.169.254 | 80 | [Punkt końcowy usługi Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) | Nie dotyczy |
| 168.63.129.16 | 80 | [Monitorowanie kondycji hosta sesji](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Nie dotyczy |

>[!IMPORTANT]
>Windows Virtual Desktop obsługuje teraz tag FQDN. Aby uzyskać więcej informacji, zobacz Use Azure Firewall to protect Window Virtual Desktop deployments (Używanie usługi Windows Virtual Desktop do ochrony [wdrożeń usługi Windows Virtual Desktop).](../firewall/protect-windows-virtual-desktop.md)
>
>Zalecamy używanie tagów FQDN lub tagów usługi zamiast adresów URL, aby zapobiec problemom z usługą. Wymienione adresy URL i tagi odpowiadają tylko witrynom Windows Virtual Desktop i zasobom. Nie zawierają one adresów URL dla innych usług, takich jak Azure Active Directory.

Maszyny wirtualne platformy Azure, które tworzysz dla Windows Virtual Desktop muszą mieć dostęp do następujących adresów URL w Azure Government chmury:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*.wvd.microsoft.us|443|Ruch w usłudze|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|*Xt.blob.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|*Xt.table.core.usgovcloudapi.net|443|Ruch agenta|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Aktywacja systemu Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Aktualizacje stosu agenta i SXS|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure Portal pomocy technicznej|AzureCloud|
| 169.254.169.254 | 80 | [Punkt końcowy usługi Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) | Nie dotyczy |
| 168.63.129.16 | 80 | [Monitorowanie kondycji hosta sesji](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Nie dotyczy |

W poniższej tabeli wymieniono opcjonalne adresy URL, do których maszyny wirtualne platformy Azure mogą mieć dostęp:

|Adres|Wychodzący port TCP|Przeznaczenie|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Uwierzytelnianie w usługach online firmy Microsoft|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Usługa telemetrii|Brak|
|www.msftconnecttest.com|443|Wykrywa, czy system operacyjny jest połączony z Internetem|Brak|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Brak|
|login.windows.net|443|Zaloguj się do usług Online Services firmy Microsoft, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Aktualizacje oprogramowania klienckiego usługi OneDrive|oneclient.sfx.ms|
|*.digicert.com|443|Sprawdzenie odwołania certyfikatu|Brak|
|*.azure-dns.com|443|Azure DNS rozwiązania|Brak|
|*.azure-dns.net|443|Azure DNS rozwiązania|Brak|

>[!NOTE]
>Windows Virtual Desktop obecnie nie ma listy zakresów adresów IP, które można odblokować, aby zezwolić na ruch sieciowy. Obecnie obsługujemy tylko odblokowywanie określonych adresów URL.
>
>Jeśli używasz zapory nowej generacji (NGFW), musisz użyć dynamicznej listy adresów IP platformy Azure, aby upewnić się, że możesz nawiązać połączenie.
>
>Aby uzyskać listę bezpiecznych adresów URL związanych z pakietem Office, w tym wymaganych adresów URL Azure Active Directory, zobacz Zakresy adresów URL i IP usługi [Office 365.](/office365/enterprise/urls-and-ip-address-ranges)
>
>W przypadku adresów URL obejmujących ruch usługi należy użyć symbolu wieloznacznego (*). Jeśli nie chcesz używać * dla ruchu związanego z agentem, oto jak znaleźć adresy URL bez symboli wieloznacznych:
>
>1. Zarejestruj maszyny wirtualne w puli Windows Virtual Desktop hostów.
>2. Otwórz **Podgląd zdarzeń,** a następnie przejdź do dzienników systemu **Windows**  >  **Application**  >  **WVD-Agent** i poszukaj zdarzenia o identyfikatorze 3701.
>3. Odblokuj adresy URL, które znajdują się w obszarze Identyfikator zdarzenia 3701. Adresy URL w obszarze Identyfikator zdarzenia 3701 są specyficzne dla regionu. Należy powtórzyć proces odblokowywania z odpowiednimi adresami URL dla każdego regionu, w którym chcesz wdrożyć maszyny wirtualne.

## <a name="remote-desktop-clients"></a>Klienci usług pulpitu zdalnego

Wszystkie Pulpit zdalny klientów, których używasz, muszą mieć dostęp do następujących adresów URL:

|Adres|Wychodzący port TCP|Przeznaczenie|Klienci|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Ruch w usłudze|Wszystko|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Rozwiązywanie problemów z danymi|Wszystko|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|Wszystko|Brak|
|aka.ms|443|Skracanie adresów URL firmy Microsoft|Wszystko|Brak|
|docs.microsoft.com|443|Dokumentacja|Wszystko|Brak|
|privacy.microsoft.com|443|Oświadczenie o ochronie prywatności|Wszystko|Brak|
|query.prod.cms.rt.microsoft.com|443|Aktualizacje klienta|Pulpit systemu Windows|Brak|

>[!IMPORTANT]
>Otwieranie tych adresów URL jest niezbędne dla niezawodnego działania klienta. Blokowanie dostępu do tych adresów URL nie jest obsługiwane i będzie miało wpływ na funkcjonalność usługi.
>
>Te adresy URL odpowiadają tylko lokacjom klienckim i zasobom. Ta lista nie zawiera adresów URL dla innych usług, takich jak Azure Active Directory. Azure Active Directory URL można znaleźć w obszarze Identyfikator 56 w zakresach adresów URL i IP usługi [Office 365.](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)
