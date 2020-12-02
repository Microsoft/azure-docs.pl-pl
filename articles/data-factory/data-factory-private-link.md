---
title: Prywatny link do usługi Azure Azure Data Factory
description: Dowiedz się, jak działa łącze prywatne platformy Azure w Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 8d28a1f2040cfec7b81081754a6abd3bc3e14439
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511478"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Prywatny link do usługi Azure Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Za pomocą linku prywatnego platformy Azure można nawiązać połączenie z różnymi platformami jako usługą (PaaS) na platformie Azure za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to prywatny adres IP w ramach określonej sieci wirtualnej i podsieci. Aby zapoznać się z listą wdrożeń PaaS, które obsługują funkcję łączy prywatnych, zobacz [dokumentację linku prywatnego](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Bezpieczna komunikacja między sieciami klientów i Azure Data Factory 
Sieć wirtualną platformy Azure można skonfigurować jako logiczną reprezentację sieci w chmurze. Zapewnia to następujące korzyści:
* Możesz chronić zasoby platformy Azure przed atakami w sieciach publicznych.
* Umożliwia to sieci i Data Factory bezpieczne komunikowanie się ze sobą. 

Możesz również połączyć sieć lokalną z siecią wirtualną, konfigurując połączenie sieci VPN zabezpieczeń protokołu internetowego (IPsec) (lokacja-lokacja) lub połączenie usługi Azure ExpressRoute (prywatna Komunikacja równorzędna). 

Możesz również zainstalować własne środowisko Integration Runtime na maszynie lokalnej lub maszynie wirtualnej w sieci wirtualnej. Dzięki temu można:
* Uruchom działania kopiowania między magazynem danych w chmurze i magazynem danych w sieci prywatnej.
* Wysyłaj działania przekształcania na zasoby obliczeniowe w sieci lokalnej lub w sieci wirtualnej platformy Azure. 

Między Azure Data Factory i siecią wirtualną klienta są wymagane kilka kanałów komunikacyjnych, jak pokazano w poniższej tabeli:

| Domena | Port | Opis |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Płaszczyzna kontroli wymagana przez Data Factory tworzenia i monitorowania. |
| `*.{region}.datafactory.azure.net` | 443 | Wymagane przez własne środowisko Integration Runtime do nawiązywania połączenia z usługą Data Factory. |
| `*.servicebus.windows.net` | 443 | Wymagane przez własne środowisko Integration Runtime do interaktywnego tworzenia. |
| `download.microsoft.com` | 443 | Wymagane przez własne środowisko Integration Runtime do pobierania aktualizacji. |

Dzięki obsłudze prywatnego linku do Azure Data Factory można:
* Utwórz prywatny punkt końcowy w sieci wirtualnej.
* Włącz połączenie prywatne z określonym wystąpieniem usługi Data Factory. 

Komunikacja do Azure Data Factory usługi odbywa się za pośrednictwem prywatnego linku i pomaga zapewnić bezpieczną łączność prywatną. Nie musisz konfigurować poprzedniej domeny i portu w sieci wirtualnej ani firmowej zapory, aby zapewnić bezpieczniejszy sposób ochrony zasobów.  

![Diagram prywatnego linku dla architektury Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

Włączenie usługi link prywatny dla każdego z powyższych kanałów komunikacji oferuje następujące funkcje:
- **Obsługiwane**:
   - Możesz tworzyć i monitorować fabrykę danych w sieci wirtualnej, nawet jeśli zablokujesz całą komunikację wychodzącą.
   - Komunikacja między własnym środowiskiem Integration Runtime i usługą Azure Data Factory można przeprowadzić bezpiecznie w środowisku sieci prywatnej. Ruch między własnym hostowanym środowiskiem Integration Runtime i usługą Azure Data Factory odbywa się za pomocą linku prywatnego. 
- **Nie jest obecnie obsługiwane**:
   - Interaktywne tworzenie, które korzysta z własnego środowiska Integration Runtime, takiego jak połączenie testowe, przeglądanie listy folderów i listy tabel, Pobieranie schematu i Podgląd danych, odbywa się za pośrednictwem prywatnego linku.
   - Nową wersję środowiska Integration Runtime (własne środowisko) można pobrać automatycznie z centrum pobierania firmy Microsoft w przypadku włączenia automatycznej aktualizacji.

   > [!NOTE]
   > W przypadku funkcji, które nie są obecnie obsługiwane, nadal trzeba skonfigurować wcześniej wymienioną domenę i port w sieci wirtualnej lub zaporze firmowej. 

> [!WARNING]
> Podczas tworzenia połączonej usługi upewnij się, że Twoje poświadczenia są przechowywane w magazynie kluczy platformy Azure. W przeciwnym razie poświadczenia nie będą działały po włączeniu prywatnego linku w Azure Data Factory.

## <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych
Podczas tworzenia prywatnego punktu końcowego rekord zasobu CNAME DNS dla Data Factory zostanie zaktualizowany do aliasu w poddomenie z prefiksem "privatelink". Domyślnie tworzymy również [prywatną strefę DNS](https://docs.microsoft.com/azure/dns/private-dns-overview), odpowiadającą poddomeną "privatelink", z rekordem zasobów DNS dla prywatnych punktów końcowych.

Po rozwiązaniu adresu URL punktu końcowego usługi Data Factory spoza sieci wirtualnej z prywatnym punktem końcowym jest on rozpoznawany jako publiczny punkt końcowy usługi Data Factory. Po rozwiązaniu problemu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, adres URL punktu końcowego magazynu jest rozpoznawany jako adres IP prywatnego punktu końcowego.

W przykładzie przedstawionym powyżej rekordy zasobów DNS dla Data Factory "DataFactory", po rozwiązaniu spoza sieci wirtualnej obsługującej prywatny punkt końcowy, będą:

| Nazwa | Typ | Wartość |
| ---------- | -------- | --------------- |
| Fabryka a. {Region}. DataFactory. Azure. NET | CNAME   | Fabryka a. {Region}. privatelink. DataFactory. Azure. NET |
| Fabryka a. {Region}. privatelink. DataFactory. Azure. NET | CNAME   | < publiczny punkt końcowy usługi Data Factory > |
| < publiczny punkt końcowy usługi Data Factory >  | A | < publiczny adres IP usługi Fabryka danych > |

Rekordy zasobów DNS dla fabryki danych, po rozwiązaniu w sieci wirtualnej hostującym prywatny punkt końcowy, będą:

| Nazwa | Typ | Wartość |
| ---------- | -------- | --------------- |
| Fabryka a. {Region}. DataFactory. Azure. NET | CNAME   | Fabryka a. {Region}. privatelink. DataFactory. Azure. NET |
| Fabryka a. {Region}. privatelink. DataFactory. Azure. NET   | A | < adres IP prywatnego punktu końcowego > |

Jeśli używasz niestandardowego serwera DNS w sieci, klienci muszą mieć możliwość rozpoznania nazwy FQDN dla punktu końcowego Data Factory do prywatnego adresu IP punktu końcowego. Należy skonfigurować serwer DNS w celu delegowania poddomeny prywatnego linku do prywatnej strefy DNS dla sieci wirtualnej lub skonfigurować rekordy A dla "DataFactory". {Region}. privatelink. DataFactory. Azure. NET "z prywatnym adresem IP punktu końcowego.

Aby uzyskać więcej informacji na temat konfigurowania własnego serwera DNS do obsługi prywatnych punktów końcowych, zapoznaj się z następującymi artykułami:
- [Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Konfiguracja DNS dla prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Skonfiguruj prywatny link do Azure Data Factory
Prywatne punkty końcowe można utworzyć przy użyciu [Azure Portal](../private-link/create-private-endpoint-portal.md).

Możesz również przejść do usługi Azure Data Factory w Azure Portal i utworzyć prywatny punkt końcowy, jak pokazano poniżej:

![Zrzut ekranu przedstawiający okienko "połączenia prywatne punktu końcowego" służące do tworzenia prywatnego punktu końcowego.](./media/data-factory-private-link/create-private-endpoint.png)


Jeśli chcesz zablokować publiczny dostęp do usługi Azure Data Factory i zezwolić na dostęp tylko za pomocą linku prywatnego, wyłącz dostęp sieciowy do Azure Data Factory w Azure Portal, jak pokazano poniżej:

![Zrzut ekranu przedstawiający okienko "dostęp sieciowy" służące do tworzenia prywatnego punktu końcowego.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Wyłączenie dostępu do sieci publicznej dotyczy tylko samodzielnego środowiska Integration Runtime, a nie do Azure Integration Runtime i SQL Server Integration Services (SSIS) Integration Runtime.

> [!NOTE]
> Po wyłączeniu dostępu do sieci publicznej nadal można uzyskać dostęp do portalu Azure Data Factory za pomocą sieci publicznej.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie fabryki danych przy użyciu interfejsu użytkownika Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
- [Tworzenie wizualne w Azure Data Factory](author-visually.md)