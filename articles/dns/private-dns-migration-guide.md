---
title: Migrowanie starszej Azure DNS Private Zones do nowego modelu zasobów
titleSuffix: Azure DNS
description: Ten przewodnik zawiera instrukcje krok po kroku dotyczące migrowania starszych prywatnych stref DNS do najnowszego modelu zasobów
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: bfc7a851442d6dd70d55c9f00e6d2f18d37e56bf
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203406"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrowanie starszych stref prywatnych Azure DNS do nowego modelu zasobów

W publicznej wersji zapoznawczej prywatne strefy DNS zostały utworzone przy użyciu zasobu "dnszones" z właściwością "zonetype" ustawioną na "Private". Takie strefy nie będą obsługiwane po 31 grudnia 2019 i muszą zostać zmigrowane do modelu zasobów platformy "privateDnsZones", a nie "dnszones". Proces migracji jest prosty i podano skrypt programu PowerShell do automatyzowania tego procesu. Ten przewodnik zawiera instrukcje krok po kroku dotyczące migrowania stref prywatnych Azure DNS do nowego modelu zasobów.

Aby dowiedzieć się, jakie zasoby dnszones wymagają migracji; wykonaj poniższe polecenie w interfejsie wiersza polecenia platformy Azure.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zainstalowano najnowszą wersję Azure PowerShell. Aby uzyskać więcej informacji na temat Azure PowerShell (az) i sposobu instalowania go https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Upewnij się, że zainstalowano moduł PrivateDns dla Azure PowerShell. Aby zainstalować ten moduł, Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień (tryb administracyjny) i wprowadź następujące polecenie

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Proces migracji jest w pełni zautomatyzowany i nie powinien powodować jakiegokolwiek przestoju. Jeśli jednak używasz stref prywatnych Azure DNS (wersja zapoznawcza) w krytycznym środowisku produkcyjnym, musisz wykonać następujący proces migracji w trakcie planowanego okna czasu konserwacji. Podczas uruchamiania skryptu migracji nie należy modyfikować konfiguracji ani zestawów rekordów w prywatnych strefach DNS.

## <a name="installing-the-script"></a>Instalowanie skryptu

Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień (tryb administracyjny) i uruchom następujące polecenie

```powershell
install-script PrivateDnsMigrationScript
```

Wprowadź "A" po wyświetleniu monitu o zainstalowanie skryptu

![Instalowanie skryptu](./media/private-dns-migration-guide/install-migration-script.png)

Możesz również ręcznie uzyskać najnowszą wersję skryptu programu PowerShell na stronie https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Skrypt migracji nie może być uruchamiany w usłudze Azure Cloud Shell i musi być wykonywany w maszynie wirtualnej lub na komputerze lokalnym połączonym z Internetem.

## <a name="running-the-script"></a>Uruchamianie skryptu

Wykonaj następujące polecenie, aby uruchomić skrypt

```powershell
PrivateDnsMigrationScript.ps1
```

![Uruchamianie skryptu](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Wprowadź identyfikator subskrypcji i zaloguj się na platformie Azure

Zostanie wyświetlony monit o podanie identyfikatora subskrypcji zawierającego prywatne strefy DNS, które mają zostać zmigrowane. Zostanie wyświetlony monit o zalogowanie się do konta platformy Azure. Ukończ logowanie, aby skrypt mógł uzyskać dostęp do prywatnych zasobów strefy DNS w subskrypcji.

![Logowanie na platformie Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Wybierz strefy DNS, które chcesz zmigrować

Skrypt z listą wszystkich prywatnych stref DNS w subskrypcji i monituje o potwierdzenie, które z nich mają zostać zmigrowane. Wprowadź wartość "A", aby zmigrować wszystkie prywatne strefy DNS. Po wykonaniu tego kroku skrypt utworzy nowe prywatne strefy DNS przy użyciu nowego modelu zasobów i skopiuje dane do nowej strefy DSN. Ten krok nie spowoduje zmiany istniejących prywatnych stref DNS mimo to.

![Wybierz strefy DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Przełączanie rozpoznawania nazw DNS do nowych stref DNS

Po skopiowaniu stref i rekordów do nowego modelu zasobów skrypt wyświetli monit o przełączenie rozpoznawania DNS do nowych stref DNS. Ten krok powoduje usunięcie skojarzenia między starszymi prywatnymi strefami DNS i sieciami wirtualnymi. Gdy Starsza strefa zostanie odłączona od sieci wirtualnych, nowe strefy DNS utworzone w powyższym kroku spowodują automatyczne przejęcie rozpoznawania nazw DNS dla tych sieci wirtualnych.

Wybierz pozycję "A", aby przełączyć rozpoznawanie nazw DNS dla wszystkich sieci wirtualnych.

![Przełączanie rozpoznawania nazw](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Weryfikowanie rozpoznawania nazw DNS

Przed kontynuowaniem upewnij się, że rozpoznawanie nazw DNS w strefach DNS działa zgodnie z oczekiwaniami. Możesz zalogować się do maszyn wirtualnych platformy Azure i wydać zapytanie nslookup dla zmigrowanych stref, aby sprawdzić, czy rozpoznawanie nazw DNS działa prawidłowo.

![Weryfikuj rozpoznawanie nazw](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Jeśli okaże się, że zapytania DNS nie są rozwiązywane, zaczekaj kilka minut i ponów próbę wykonania zapytań. Jeśli zapytania DNS działają zgodnie z oczekiwaniami, wprowadź wartość "Y", gdy skrypt monituje o usunięcie sieci wirtualnej z prywatnej strefy DNS.

![Potwierdź rozpoznawanie nazw](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Jeśli ze względu na to, że rozpoznawanie nazw DNS względem zmigrowanych stref nie działa zgodnie z oczekiwaniami, wprowadź "N" w powyższym kroku, a skrypt spowoduje przełączenie rozpoznawania DNS z powrotem do starszych stref. Utwórz bilet pomocy technicznej i będziemy mogli pomóc w migracji stref DNS.

## <a name="cleanup"></a>Czyszczenie

Ten krok spowoduje usunięcie starszych stref DNS i należy je wykonać dopiero po sprawdzeniu, że rozpoznawanie nazw DNS działa zgodnie z oczekiwaniami. Zostanie wyświetlony monit o usunięcie każdej prywatnej strefy DNS. Wprowadź "Y" w każdym monicie po sprawdzeniu, czy rozpoznawanie nazw DNS dla tych stref działa prawidłowo.

![Czyszczenie](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Aktualizowanie usługi Automation

Jeśli używasz automatyzacji, w tym szablonów, skryptów programu PowerShell lub niestandardowego kodu opracowanego przy użyciu zestawu SDK, musisz zaktualizować automatyzację, aby używała nowego modelu zasobów dla prywatnych stref DNS. Poniżej znajdują się linki do nowej prywatnej infrastruktury interfejsu wiersza polecenia DNS/PS/SDK.
* [Interfejs API REST stref prywatnych Azure DNS](/rest/api/dns/privatedns/privatezones)
* [Interfejs wiersza polecenia Azure DNS strefy prywatnej](/cli/azure/ext/privatedns/network/private-dns)
* [Azure DNS stref prywatnych programu PowerShell](/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Azure DNS zestawu SDK stref prywatnych](/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Potrzebna dodatkowa pomoc

Utwórz bilet pomocy technicznej, jeśli potrzebujesz dalszej pomocy dotyczącej procesu migracji lub z dowolnego powodu powyższe wymienione kroki nie będą działały. Dołącz plik transkrypcji wygenerowany przez skrypt programu PowerShell do biletu pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w Azure DNS przy użyciu [Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Zapoznaj się z typowymi [scenariuszami stref prywatnych](./private-dns-scenarios.md) , które mogą być realizowane przy użyciu stref prywatnych w Azure DNS.

* Często zadawane pytania i odpowiedzi dotyczące stref prywatnych w Azure DNS, w tym określonych zachowań, których można oczekiwać w przypadku niektórych rodzajów operacji, zapoznaj się z tematem [prywatna strefa DNS często zadawane pytania](./dns-faq-private.md).

* Informacje o strefach i rekordach DNS zawiera temat [Omówienie stref i rekordów DNS](dns-zones-records.md).

* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.