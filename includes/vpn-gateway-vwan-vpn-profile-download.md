---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fc2393cfe87e2639ce40e66e6053d4d430518719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87515319"
---
## <a name="1-download-the-file"></a>1. Pobierz plik

Uruchom następujące polecenia. Skopiuj adres URL wyniku do przeglądarki, aby pobrać plik zip profilu.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Wyodrębnij plik zip

Wyodrębnij plik ZIP. Plik zawiera następujące foldery:

* AzureVPN
* Ogólny
* OpenVPN (Jeśli włączono ustawienia OpenVPN przy użyciu **certyfikatu platformy Azure** lub ustawień **uwierzytelniania usługi RADIUS** na bramie). Aby uzyskać VPN Gateway, zobacz [Tworzenie dzierżawy](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). W przypadku wirtualnej sieci WAN zobacz [Tworzenie dzierżawy — VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Pobieranie informacji

W folderze **AzureVPN** przejdź do pliku ***azurevpnconfig.xml*** i otwórz go w Notatniku. Zanotuj tekst między następującymi tagami.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Szczegóły profilu

Po dodaniu połączenia użyj informacji zebranych w poprzednim kroku dla strony Szczegóły profilu. Pola odnoszą się do następujących informacji:

   * **Odbiorcy:** Identyfikuje zasób odbiorcy, dla którego jest przeznaczony token
   * **Wystawca:** Identyfikuje usługę tokenu zabezpieczającego (STS), która emituje token, a także dzierżawę usługi Azure AD
   * **Dzierżawca:** Zawiera niezmienny, unikatowy identyfikator dzierżawy katalogu, który wystawił token
   * **Nazwa FQDN:** W pełni kwalifikowana nazwa domeny (FQDN) w bramie sieci VPN platformy Azure
   * **ServerSecret:** Klucz wstępny bramy sieci VPN

## <a name="folder-contents"></a>Zawartość folderu

* **Folder ogólny** zawiera publiczny certyfikat serwera i plik VpnSettings.xml. Plik VpnSettings.xml zawiera informacje, które są konieczne do skonfigurowania klienta ogólnego.

* Pobrany plik zip może również zawierać foldery **WindowsAmd64** i **WindowsX86** . Foldery te zawierają Instalatora protokołu SSTP i IKEv2 dla klientów z systemem Windows. Aby je zainstalować, musisz mieć uprawnienia administratora na kliencie.
