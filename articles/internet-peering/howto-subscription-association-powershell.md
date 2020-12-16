---
title: Kojarzenie równorzędnych elementów ASN z subskrypcją platformy Azure przy użyciu programu PowerShell
titleSuffix: Azure
description: Kojarzenie równorzędnych elementów ASN z subskrypcją platformy Azure przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590192"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Kojarzenie równorzędnych elementów ASN z subskrypcją platformy Azure przy użyciu programu PowerShell

Przed przesłaniem żądania komunikacji równorzędnej należy najpierw skojarzyć numer ASN z subskrypcją platformy Azure, wykonując poniższe kroki.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [portalu](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Utwórz PeerASN, aby skojarzyć swój numer ASN z subskrypcją platformy Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Zarejestruj dla dostawcy zasobów komunikacji równorzędnej
Zarejestruj się w przypadku dostawcy zasobów komunikacji równorzędnej w subskrypcji przy użyciu poniższego polecenia. Jeśli to nie zrobisz, zasoby platformy Azure wymagane do skonfigurowania komunikacji równorzędnej są niedostępne.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Stan rejestracji można sprawdzić za pomocą poniższych poleceń:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Poczekaj, aż *RegistrationState* "zarejestrowano" przed kontynuowaniem. Po wykonaniu polecenia może upłynąć od 5 do 30 minut.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Aktualizowanie informacji równorzędnych skojarzonych z tą subskrypcją

Poniżej znajduje się przykład aktualizacji informacji o komunikacji równorzędnej.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Nazwa odpowiada nazwie zasobu i może być dowolna. Jednak — peerName odpowiada nazwie swojej firmy i musi być możliwie jak najbliżej profilu PeeringDB. Należy pamiętać, że wartość parametru-peerName obsługuje tylko znaki a-z, A-Z i spację.

Subskrypcja może mieć wiele numerów ASN. Aktualizowanie informacji o komunikacji równorzędnej dla każdego numeru ASN. Upewnij się, że wartość "name" jest unikatowa dla każdego numeru ASN.

Elementy równorzędne powinny mieć pełny i aktualny profil w usłudze [PeeringDB](https://www.peeringdb.com). Te informacje są używane podczas rejestracji w celu weryfikacji szczegółowych informacji dotyczących elementów równorzędnych, takich jak informacje NOC, informacje kontaktowe techniczne i ich obecność w urządzeniach komunikacji równorzędnej itp.

Należy pamiętać, że zamiast elementu **{Subscription}** w danych wyjściowych zostanie wyświetlony rzeczywisty identyfikator subskrypcji.

## <a name="view-status-of-a-peerasn"></a>Wyświetl stan elementu PeerASN

Sprawdź stan sprawdzania poprawności numeru ASN przy użyciu poniższego polecenia:

```powershell
Get-AzPeerAsn
```

Poniżej znajduje się Przykładowa odpowiedź:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Poczekaj, aż ValidationState przełączy "zatwierdzone" przed przesłaniem żądania komunikacji równorzędnej. Zatwierdzenie może potrwać do 12 godzin.

## <a name="modify-peerasn"></a>Modyfikuj PeerAsn
W każdej chwili możesz zmodyfikować informacje o kontakcie NOC.

Poniżej znajduje się przykład:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Usuń PeerAsn
Usuwanie elementu PeerASN nie jest obecnie obsługiwane. Jeśli musisz usunąć PeerASN, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej](howto-direct-powershell.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure](howto-legacy-direct-powershell.md)
* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md)
