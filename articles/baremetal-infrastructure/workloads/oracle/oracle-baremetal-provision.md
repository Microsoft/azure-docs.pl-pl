---
title: Aprowizuj oprogramowanie BareMetal dla programu Oracle
description: Dowiedz się więcej na temat aprowizowania infrastruktury BareMetal dla programu Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559096"
---
# <a name="provision-baremetal-for-oracle"></a>Aprowizuj oprogramowanie BareMetal dla programu Oracle

W tym artykule przyjrzymy się, jak aprowizować wystąpienia infrastruktury BareMetal dla obciążeń Oracle. 

Pierwszym krokiem do aprowizować wystąpienia programu BareMetal jest praca z kontem CSA firmy Microsoft. Będą one pomocne w zależności od konkretnych potrzeb w zakresie obciążenia i wdrażanej architektury, niezależnie od tego, czy jest to pojedyncze wystąpienie, jedno węzło RAC, czy RAC. Aby uzyskać więcej informacji na temat tych topologii, zobacz Architecture of BareMetal Infrastructure for Oracle (Architektura [baremetalowej infrastruktury dla programu Oracle).](oracle-baremetal-architecture.md)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Aktywna subskrypcja platformy Azure
> * Umowa pomocy technicznej Premier firmy Microsoft
> * Licencje dla Red Hat Enterprise Linux 7.6
> * Umowa pomocy technicznej firmy Oracle 
> * Licencje i składniki instalacyjne oprogramowania dla programu Oracle
> * Usługa ExpressRoute połączona **lokalnie** z platformą Azure **(opcjonalnie** skonfiguruj usługę ExpressRoute Global Reach na Oracle Database)   
> * Sieć wirtualna
> * Tworzenie bramy
> * Maszyny wirtualne w sieci wirtualnej (serwery przeskoku)

## <a name="information-to-provide-microsoft-operations"></a>Informacje o operacjach firmy Microsoft

Musisz podać następujące informacje do csa:

1. Przestrzeń adresowa sieci wirtualnej. Ten zakres musi być /24 podsieci; na przykład 10.11.0.0/24.
2. Zakres P2P. Ten zakres musi być podsiecią /29; na przykład 10.12.0.0/29.
3. Pula adresów IP serwera. Zalecany zakres to /24; na przykład 10.13.0.0/24.
4. Adres IP serwera. Wybierz adres IP z puli adresów IP serwera.

    > [!Note] 
    > Pierwsze 30 adresów IP jest zarezerwowanych dla konfiguracji infrastruktury firmy Microsoft. W tym przykładzie pierwszym dostępnym adresem IP dla bloku będzie 10.13.0.30.

5. Wymagany region świadczenia usługi Azure. na przykład Zachodnie stany USA 2.
6. Wymagana jestkuku SKU infrastruktury BareMetal; na przykład S192 (dwie maszyny).

## <a name="storage-requirements"></a>Wymagania dotyczące magazynu

We współpracy z przedstawicielem CSA w celu obsługi potrzeb w zakresie magazynu w trakcie żądania aprowiwizowania, w tym z oczekiwanymi potrzebami magazynu w zależności od przyszłego wzrostu. Dodano magazyn o przyrostach 1 TB.

W przypadku woluminów przestrzegamy [standardu Optimal Flexible Architecture (OFA)](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)firmy Oracle z konfiguracją w warstwie Podstawowa i konfiguracją przedsiębiorstwa. Jeśli masz niestandardowe wymagania dotyczące magazynu inne niż standardowy rozmiar "T-shirt", możesz wykonać żądanie niestandardowe za pośrednictwem csa.

| Konfiguracja podstawowa (poc/testowanie) | Opis | Mały | Śred. | Duży |
| --- | --- | --- | --- | --- |
| /u01 | Pliki binarne Oracle | 500 GB | 500 GB | 500 GB |
| /u02 | Odczyt z intensywnym odczytem/administrator | 500 GB | 1 TB | 5 TB |
| /u03 | Intensywne pisanie/dzienniki | 500 GB | 1 TB | 5 TB |
| /u09 | Backup | 5 TB | 10 TB | 15 TB |

| Konfiguracja przedsiębiorstwa | Opis | Mały | Śred. | Duży | Bardzo duże |
| --- | --- | --- | --- | --- | --- |
| /u01 | Pliki binarne Oracle | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | Administracja | 100 GB | 100 GB | 100 GB | 100 GB |
| /u10 do /u59 | Intensywne odczyty | 500 GB | 5 TB | 10 TB | 20 TB |
| /u60 do /u89 | Intensywne pisanie | 500 GB | 5 TB | 10 TB | 20 TB |
| /u90 do /u91 | Ponowne rejestrowanie | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Archiwum | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Następny krok

Dowiedz się więcej o infrastrukturze BareMetal dla oracle.

> [!div class="nextstepaction"]
> [Co to jest infrastruktura BareMetal na platformie Azure?](../../concepts-baremetal-infrastructure-overview.md)
