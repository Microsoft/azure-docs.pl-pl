---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979077"
---
## <a name="extract-the-zip-file"></a>Wyodrębnij plik zip

Wyodrębnij plik ZIP. Plik zawiera następujące foldery:

* AzureVPN
* Ogólny
* OpenVPN (Jeśli włączono ustawienia OpenVPN przy użyciu **certyfikatu platformy Azure** lub ustawień **uwierzytelniania usługi RADIUS** na bramie). Wybierz odpowiedni artykuł, który odnosi się do konfiguracji w celu utworzenia dzierżawy.

  * [VPN Gateway — Utwórz dzierżawę](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Wirtualna sieć WAN — tworzenie dzierżawy](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Pobierz informacje

W folderze **AzureVPN** przejdź do pliku **_azurevpnconfig.xml_** i otwórz go w Notatniku. Zanotuj tekst między następującymi tagami.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Szczegóły profilu

Po dodaniu połączenia użyj informacji zebranych w poprzednim kroku dla strony Szczegóły profilu. Pola odnoszą się do następujących informacji:

* **Odbiorcy:** Identyfikuje zasób odbiorcy, dla którego jest przeznaczony token.
* **Wystawca:** Identyfikuje usługę tokenu zabezpieczającego (STS), która emituje token, a także dzierżawę usługi Azure AD.
* **Dzierżawca:** Zawiera niezmienny, unikatowy identyfikator dzierżawy katalogu, który wystawił token.
* **Nazwa FQDN:** W pełni kwalifikowana nazwa domeny (FQDN) w bramie sieci VPN platformy Azure.
* **ServerSecret:** Klucz wstępny bramy sieci VPN.

## <a name="folder-contents"></a>Zawartość folderu

* **Folder ogólny** zawiera publiczny certyfikat serwera i plik VpnSettings.xml. Plik VpnSettings.xml zawiera informacje, które są konieczne do skonfigurowania klienta ogólnego.

* Pobrany plik zip może również zawierać foldery **WindowsAmd64** i **WindowsX86** . Foldery te zawierają Instalatora protokołu SSTP i IKEv2 dla klientów z systemem Windows. Aby je zainstalować, musisz mieć uprawnienia administratora na kliencie.
