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
ms.openlocfilehash: 2cd9f01404a4e33303356dd3f452cd7dbc47a747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328568"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Prywatny link do usługi Azure Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Za pomocą linku prywatnego platformy Azure można nawiązać połączenie z różnymi wdrożeniami platformy jako usługi (PaaS) na platformie Azure za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to prywatny adres IP w ramach określonej sieci wirtualnej i podsieci. Aby zapoznać się z listą wdrożeń PaaS, które obsługują funkcję łączy prywatnych, zobacz [dokumentację linku prywatnego](https://docs.microsoft.com/azure/private-link/). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Bezpieczna komunikacja między sieciami klientów i Azure Data Factory 
Sieć wirtualną platformy Azure można skonfigurować jako logiczną reprezentację sieci w chmurze. Zapewnia to następujące korzyści:
* Możesz chronić zasoby platformy Azure przed atakami w sieciach publicznych.
* Umożliwia to sieci i Data Factory bezpieczne komunikowanie się ze sobą. 

Możesz również połączyć sieć lokalną z siecią wirtualną, konfigurując połączenie sieci VPN zabezpieczeń protokołu internetowego (IPsec) (lokacja-lokacja) lub połączenie usługi Azure ExpressRoute (prywatna Komunikacja równorzędna). 

Możesz również zainstalować własne środowisko Integration Runtime na maszynie lokalnej lub maszynie wirtualnej w sieci wirtualnej. Dzięki temu można:
* Uruchom działania kopiowania między magazynem danych w chmurze i magazynem danych w sieci prywatnej.
* Wysyłaj działania przekształcania na zasoby obliczeniowe w sieci lokalnej lub w sieci wirtualnej platformy Azure. 

Między Azure Data Factory i siecią wirtualną klienta są wymagane kilka kanałów komunikacyjnych, jak pokazano w poniższej tabeli:

| Obszar | Port | Opis |
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
   - Nową wersję środowiska Integration Runtime (własne) można automatycznie pobrać z centrum pobierania firmy Microsoft w przypadku włączenia funkcji Aktualizacje automatyczne.

   > [!NOTE]
   > W przypadku funkcji, które nie są obecnie obsługiwane, nadal trzeba skonfigurować wcześniej wymienioną domenę i port w sieci wirtualnej lub zaporze firmowej. 

> [!WARNING]
> Podczas tworzenia połączonej usługi upewnij się, że Twoje poświadczenia są przechowywane w magazynie kluczy platformy Azure. W przeciwnym razie poświadczenia nie będą działały po włączeniu prywatnego linku w Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Skonfiguruj prywatny link do Azure Data Factory
Prywatne punkty końcowe można utworzyć przy użyciu [Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal), programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

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

