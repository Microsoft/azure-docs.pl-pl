---
title: Słownik platformy Azure — słownik platformy Azure | Microsoft Docs
description: Skorzystaj z słownika platformy Azure, aby zrozumieć terminologię w chmurze na platformie Azure. Ten krótki słownik platformy Azure zawiera definicje typowych warunków w chmurze dla platformy Azure.
keywords: Słownik platformy Azure, terminologia w chmurze, słownik platformy Azure, definicje terminologii, warunki chmury
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 2e183ad8a6e25c12f073f51e9416b4fc0ed9365f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530407"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Słownik Microsoft Azure: Słownik terminologii w chmurze na platformie Azure

Słownik Microsoft Azure jest krótkim słownikiem terminologii w chmurze dla platformy Azure. Zobacz też:

* [Microsoft Azure i Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) — definicje usług platformy Azure i ich odpowiedników AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Warunki chmury obliczeniowej](https://azure.microsoft.com/overview/cloud-computing-dictionary/) — ogólne warunki chmury branżowej.

## <a name="account"></a>account
Konto używane do uzyskiwania dostępu do subskrypcji platformy Azure i zarządzania nią. Jest to często określane jako konto platformy Azure, ale konto może być dowolnym z nich: istniejącej pracy, szkoły lub konto Microsoft osobistej. Możesz również utworzyć konto, aby zarządzać subskrypcją platformy Azure po zarejestrowaniu się w celu [korzystania z bezpłatnej wersji próbnej](https://azure.microsoft.com).  
Zobacz [Rejestrowanie się w celu uzyskania subskrypcji platformy Azure przy użyciu konta Microsoft 365](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) i [kont, których możesz użyć do zalogowania](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)się.

## <a name="api-app"></a>Aplikacja interfejsu API
Inna nazwa [aplikacji App Service](#app-service-app).

## <a name="app-service-app"></a>Aplikacja usługi App Service
Zasoby obliczeniowe [Azure App Service](app-service/overview.md) umożliwiają hostowanie witryny sieci Web lub aplikacji internetowej, internetowego interfejsu API lub [zaplecza aplikacji mobilnej](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop). Aplikacje App Service są również określane jako *App Services*, *aplikacje sieci Web*, *aplikacje interfejsu API* i *aplikacje mobilne*.

## <a name="availability-set"></a>zestaw dostępności
Kolekcja maszyn wirtualnych, które są zarządzane razem w celu zapewnienia nadmiarowości i niezawodności aplikacji. Użycie zestawu dostępności gwarantuje, że podczas planowanego lub nieplanowanego zdarzenia konserwacji co najmniej jedna maszyna wirtualna jest dostępna.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows](./virtual-machines/manage-availability.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json) i [Zarządzanie dostępnością maszyn wirtualnych z systemem Linux](./virtual-machines/manage-availability.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klasyczny model wdrażania platformy Azure
Jeden z dwóch [modeli wdrażania](./azure-resource-manager/management/deployment-models.md) używanych do wdrażania zasobów na platformie Azure (nowy model to Azure Resource Manager). Niektóre usługi platformy Azure obsługują tylko Menedżer zasobów model wdrażania, ale niektóre obsługują tylko klasyczny model wdrażania, a niektóre obsługują obie te funkcje. Dokumentacja dla każdej usługi platformy Azure określa, które modele obsługują.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Interfejs wiersza polecenia platformy Azure
Interfejs wiersza polecenia, którego można użyć do zarządzania usługami platformy Azure z systemów Windows, macOS i Linux.  Niektóre usługi lub funkcje usługi mogą być zarządzane tylko za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia. Zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Interfejs wiersza polecenia do zarządzania usługami platformy Azure za pośrednictwem wiersza polecenia z komputerów z systemem Windows. Niektóre usługi lub funkcje usługi mogą być zarządzane tylko za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia.
Zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager model wdrażania
Jeden z dwóch [modeli wdrażania](./azure-resource-manager/management/deployment-models.md) używanych do wdrażania zasobów w Microsoft Azure (drugi to klasyczny model wdrażania). Niektóre usługi platformy Azure obsługują tylko Menedżer zasobów model wdrażania, ale niektóre obsługują tylko klasyczny model wdrażania, a niektóre obsługują obie te funkcje. Dokumentacja dla każdej usługi platformy Azure określa, które modele obsługują.

## <a name="fault-domain"></a>Domena błędów
Kolekcja maszyn wirtualnych w zestawie dostępności, która może być niemożliwa w tym samym czasie. Przykładem jest Grupa maszyn w stojaku, która udostępnia wspólne źródło napięcia i przełącznik sieciowy. Na platformie Azure maszyny wirtualne w zestawie dostępności są automatycznie oddzielane w wielu domenach błędów.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows](./virtual-machines/manage-availability.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json) lub [Zarządzanie dostępnością maszyn wirtualnych z systemem Linux](./virtual-machines/manage-availability.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json)  

## <a name="geo"></a>geograficznie
Zdefiniowana granica miejsca zamieszkania danych, która zwykle zawiera co najmniej dwa regiony. Granice mogą znajdować się poza granicami narodowymi i mieć wpływ na regulację podatkową. Każde geograficzne ma co najmniej jeden region. Przykłady georegionyów to Azja i Pacyfik i Japonia. Nazywana również *geografią*.  
Zobacz [regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>replikacja geograficzna
Proces automatycznego replikowania zawartości, takiej jak obiekty blob, tabele i kolejki w ramach pary regionalnej.  
Zobacz [aktywne Geo-Replication dla Azure SQL Database](./azure-sql/database/auto-failover-group-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image (obraz)
Plik, który zawiera system operacyjny i konfigurację aplikacji, za pomocą którego można utworzyć dowolną liczbę maszyn wirtualnych. Na platformie Azure istnieją dwa typy obrazów: obraz maszyny wirtualnej i obraz systemu operacyjnego. Obraz maszyny wirtualnej zawiera system operacyjny i wszystkie dyski dołączone do maszyny wirtualnej po utworzeniu obrazu. Obraz systemu operacyjnego zawiera tylko uogólniony system operacyjny bez konfiguracji dysków danych.  
Zobacz [nawigowanie i wybieranie obrazów maszyn wirtualnych z systemem Windows na platformie Azure przy użyciu programu PowerShell lub interfejsu wiersza polecenia](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>ograniczeń
Liczba zasobów, które można utworzyć, lub wydajności, które można osiągnąć. Limity są zwykle skojarzone z subskrypcjami, usługami i ofertami.  
Zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Moduł równoważenia obciążenia
Zasób, który dystrybuuje ruch przychodzący między komputerami w sieci. W systemie Azure moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych zdefiniowanych w zestawie modułu równoważenia obciążenia. [Moduł równoważenia obciążenia](load-balancer/load-balancer-overview.md) może być dostępny z Internetu lub może być wewnętrzny.  

## <a name="mobile-app"></a>Aplikacja mobilna
Inna nazwa [aplikacji App Service](#app-service-app).

## <a name="offer"></a>offer
Cennik, kredyty i powiązane warunki mające zastosowanie do subskrypcji platformy Azure.  
Zobacz [stronę szczegółów oferty platformy Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Bezpieczny portal sieci Web używany do wdrażania usług platformy Azure i zarządzania nimi.

## <a name="region"></a>region
Obszar w obszarze geograficznym, który nie przekracza granic krajowych i zawiera co najmniej jedno centrum danych. Cennik, usługi regionalne i typy ofert są ujawniane na poziomie regionu. Region jest zazwyczaj sparowany z innym regionem, który może znajdować się maksymalnie do stu kilometrów. Pary regionalne mogą służyć jako mechanizm odzyskiwania po awarii i scenariuszy wysokiej dostępności. Nazywana również *lokalizacją*.  
Zobacz [regiony platformy Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>zasób
Element, który jest częścią rozwiązania platformy Azure. Każda usługa platformy Azure umożliwia wdrażanie różnych typów zasobów, takich jak bazy danych lub maszyny wirtualne.   
Zobacz [przegląd Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>grupa zasobów
Kontener w Menedżer zasobów, który zawiera powiązane zasoby dla aplikacji. Grupa zasobów może obejmować wszystkie zasoby aplikacji lub tylko te zasoby, które są logicznie zgrupowane razem. Zasoby mogą być przydzielane do grup na podstawie priorytetów organizacji.  
Zobacz [przegląd Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Szablon usługi Resource Manager
Plik JSON, który deklaratywnie definiuje co najmniej jeden zasób platformy Azure i definiuje zależności między wdrożonymi zasobami. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów.  
Zobacz [Tworzenie szablonów Azure Resource Manager](./azure-resource-manager/templates/template-syntax.md)

## <a name="resource-provider"></a>dostawca zasobów
Usługa dostarczająca zasoby, które można wdrażać i zarządzać nimi za pomocą Menedżer zasobów. Każdy dostawca zasobów udostępnia operacje do pracy z wdrażanymi zasobami. Dostęp do dostawców zasobów można uzyskać za pomocą Azure Portal, Azure PowerShell i kilku zestawów SDK programistycznych.  
Zobacz [przegląd Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="role"></a>role (rola)
Metodę kontroli dostępu, który można przypisać do użytkowników, grup i usług. Role mogą wykonywać takie działania, jak tworzenie, zarządzanie i odczytywanie zasobów platformy Azure.  
Zobacz [RBAC: role wbudowane](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>Umowa dotycząca poziomu usług (SLA)
Umowa opisująca zobowiązania firmy Microsoft dotyczące czasu przestoju i łączności. Każda usługa platformy Azure ma określoną umowę SLA.  
Zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>Sygnatura dostępu współdzielonego (SAS)
Podpis, który umożliwia przyznanie ograniczonego dostępu do zasobu bez uwidaczniania klucza konta. Na przykład [usługa Azure Storage używa sygnatury dostępu współdzielonego](./storage/common/storage-sas-overview.md) , aby umożliwić klientom dostęp do obiektów, takich jak obiekty blob. [IoT Hub używa sygnatury dostępu współdzielonego](iot-hub/iot-hub-devguide-security.md#security-tokens) do przyznawania urządzeniom uprawnień do wysyłania telemetrii

## <a name="storage-account"></a>konto magazynu
Konto, które zapewnia dostęp do usług Azure Blob, kolejek, tabel i plików w usłudze Azure Storage. Nazwa konta magazynu definiuje unikatową przestrzeń nazw dla obiektów danych usługi Azure Storage.  
Zobacz [Informacje o kontach usługi Azure Storage](./storage/common/storage-account-create.md)

## <a name="subscription"></a>subskrypcja
Umowa klienta z firmą Microsoft, która umożliwia im uzyskanie usług platformy Azure. Cennik subskrypcji i powiązane warunki podlegają ofercie wybranej dla subskrypcji.
Zobacz [Umowa subskrypcyjna dotycząca usług online firmy Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/) i [sposób, w jaki subskrypcje platformy Azure są skojarzone z Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Termin indeksowania, który umożliwia kategoryzowanie zasobów zgodnie z wymaganiami dotyczącymi zarządzania lub rozliczeń. Jeśli masz złożoną kolekcję zasobów, możesz użyć tagów do wizualizacji tych zasobów w taki sposób, który jest najbardziej zrozumiały. Można na przykład oznaczyć tagami zasoby, które pełnią podobną rolę w organizacji lub należą do tego samego działu.  
Zobacz [Używanie tagów do organizowania zasobów platformy Azure](./azure-resource-manager/management/tag-resources.md)

## <a name="update-domain"></a>Aktualizuj domenę
Kolekcja maszyn wirtualnych w zestawie dostępności, które są aktualizowane w tym samym czasie. Maszyny wirtualne w tej samej domenie aktualizacji są uruchamiane ponownie wraz z zaplanowaną konserwacją. Platforma Azure nigdy nie uruchamia ponownie więcej niż jednej domeny aktualizacji jednocześnie. Nazywana również domeną uaktualnienia.  
Zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows](./virtual-machines/manage-availability.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json) i [Zarządzanie dostępnością maszyn wirtualnych z systemem Linux](./virtual-machines/manage-availability.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>Maszyna wirtualna
Implementacja oprogramowania komputera fizycznego z uruchomionym systemem operacyjnym. Na tym samym sprzęcie można uruchamiać wiele maszyn wirtualnych jednocześnie. Na platformie Azure maszyny wirtualne są dostępne w różnych rozmiarach.  
Zobacz [dokumentację Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>rozszerzenie maszyny wirtualnej
Zasób, który implementuje zachowania lub funkcje, które mogą pomóc innym programom w działaniu lub umożliwia współdziałanie z uruchomionym komputerem. Na przykład można użyć rozszerzenia dostępu do maszyny wirtualnej do resetowania lub modyfikowania wartości dostępu zdalnego na maszynie wirtualnej platformy Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Zobacz [Informacje o rozszerzeniach i funkcjach maszyn wirtualnych (Windows)](./virtual-machines/extensions/features-windows.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json) lub [o rozszerzeniach i funkcjach maszyn wirtualnych (Linux)](./virtual-machines/extensions/features-linux.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>Sieć wirtualna
Sieć, która zapewnia łączność między zasobami platformy Azure, które są odizolowane od wszystkich innych dzierżawców platformy Azure. [VPN Gateway platformy Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) umożliwia ustanawianie połączeń między sieciami wirtualnymi i między siecią wirtualną a siecią lokalną. Możesz w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabele tras w obrębie sieci wirtualnej.  
Zobacz [przegląd Virtual Network](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplikacja internetowa
Inna nazwa [aplikacji App Service](#app-service-app).

## <a name="see-also"></a>Zobacz także

* [Rozpoczynanie pracy z platformą Azure](https://azure.microsoft.com/get-started/)
* [Centrum zasobów w chmurze](https://azure.microsoft.com/resources/)  
* [Platforma Azure dla Twojej aplikacji biznesowej](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Platforma Azure w centrum danych](https://azure.microsoft.com/overview/business-apps-on-azure/)