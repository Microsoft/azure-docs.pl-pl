---
title: Tworzenie profilu usługi Intune dla klientów sieci VPN platformy Azure
titleSuffix: Azure VPN Gateway
description: Dowiedz się, jak utworzyć niestandardowy profil usługi Intune, aby wdrożyć profile klienta sieci VPN platformy Azure
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/11/2020
ms.author: cherylmc
ms.openlocfilehash: 5a754a14cada1547a83e29d474e9b77aed7a2728
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024353"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>Tworzenie profilu usługi Intune w celu wdrożenia profilów klienta sieci VPN

Profile dla klientów sieci VPN platformy Azure (Windows 10) można wdrożyć przy użyciu Microsoft Intune. W tym artykule opisano tworzenie profilu usługi Intune przy użyciu ustawień niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenia są już zarejestrowane w usłudze MDM w usłudze Intune.
* Klient sieci VPN platformy Azure dla systemu Windows 10 został już wdrożony na komputerze klienckim.
* Obsługiwany jest tylko system Windows w wersji 19H2 lub nowszej.

## <a name="modify-xml"></a><a name="xml"></a>Modyfikuj plik XML

W poniższych krokach użyto przykładowego kodu XML dla niestandardowego profilu OMA-URI dla usługi Intune z następującymi ustawieniami:

* Połącz ponownie
* Włączono funkcję wykrywania zaufanych sieci.

Aby poznać inne obsługiwane opcje, zapoznaj się z artykułem [Obsługa sieci VPNV2 CSP](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) .

1. Pobierz profil sieci VPN z Azure Portal i wyodrębnij plik *azurevpnconfig.xml* z pakietu.
1. Skopiuj i wklej poniższy tekst do nowego pliku edytora tekstu.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Zmodyfikuj wpis między ```<ServerUrlList>``` i ```</ServerUrlList>``` wraz z pozycją pobranego profilu (azurevpnconfig.xml). Zmień nazwę FQDN "TrustedNetworkDetection" tak, aby była zgodna z Twoim środowiskiem.
1. Otwórz profil pobrany przez platformę Azure (azurevpnconfig.xml) i skopiuj zawartość do schowka, wyróżnij tekst i naciśnij klawisze <ctrl> + C. Skopiuj wszystko między następującymi wierszami AzVpnProfile, ale nie Kopiuj samych wierszy AzVpnProfile:

   ```
   <AzVpnProfile xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.datacontract.org/2004/07/">
     <any xmlns:d2p1="http://schemas.datacontract.org/2004/07/System.Xml"
       i:nil="true" />

   For example - copy the text in your xml that is located here.

   </AzVpnProfile>
   ```
1. Wklej skopiowany tekst z poprzedniego kroku do pliku utworzonego w kroku 2 między ```<CustomConfiguration>  </CustomConfiguration>``` tagami. Zapisz plik z rozszerzeniem XML.
1. Zapisz wartość w ```<name>  </name>``` znacznikach. To jest nazwa profilu. Ta nazwa będzie potrzebna podczas tworzenia profilu w usłudze Intune. Zamknij plik i Zapamiętaj lokalizację, w której został zapisany.

## <a name="create-intune-profile"></a>Utwórz profil usługi Intune

W tej sekcji utworzysz profil Microsoft Intune z ustawieniami niestandardowymi.

1. Zaloguj się do usługi Intune i przejdź do **urządzeń — > profile konfiguracji**. Wybierz pozycję **+ Utwórz profil**.

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="Profile konfiguracji":::
1. W polu **Platforma** wybierz pozycję **Windows 10 i nowsze**. W obszarze **profil**wybierz pozycję **niestandardowy**. Następnie wybierz przycisk **Utwórz**.
1. Podaj nazwę i opis profilu, a następnie wybierz przycisk **dalej**.
1. Na karcie **Ustawienia konfiguracji** wybierz pozycję **Dodaj**.

    * **Nazwa:** Wprowadź nazwę konfiguracji.
    * **Opis:** Opcjonalny opis.
    * **Identyfikator OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (te informacje można znaleźć w pliku azurevpnconfig.xml w <name> </name> tagu).
    * **Typ danych:** Ciąg (plik XML).

   Wybierz ikonę folderu i wybierz plik zapisany w kroku 6 w krokach [XML](#xml) . Wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="Ustawienia konfiguracji" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. Wybierz pozycję **Next** (Dalej).
1. W obszarze **przypisania**wybierz grupę, do której chcesz wypchnąć konfigurację. Następnie wybierz pozycję **Dalej**.
1. Reguły zastosowania są opcjonalne. W razie konieczności Zdefiniuj reguły, a następnie wybierz przycisk **dalej**.
1. Na stronie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="Utwórz profil":::
1. Profil niestandardowy został utworzony. Aby uzyskać Microsoft Intune kroków związanych z wdrażaniem tego profilu, zobacz [przypisywanie profilów użytkowników i urządzeń](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign).
 
## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Point-to-site, zobacz [Informacje o punkcie-to-site](point-to-site-about.md).
