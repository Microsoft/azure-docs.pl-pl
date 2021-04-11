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
ms.openlocfilehash: a5ae81cde28efbf57965beda2f82915854579e43
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106072832"
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
