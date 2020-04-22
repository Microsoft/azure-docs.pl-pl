---
title: Obsługiwane wersje klastra w sieci szkieletowej usług Azure
description: Dowiedz się więcej o wersjach klastra w sieci szkieletowej usługi Azure, w tym łącze do najnowszych wersji z bloga zespołu sieci szkieletowej usług.
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.openlocfilehash: b68314a116b0d9da8baf5f61eeffeef2b30835c4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732873"
---
# <a name="supported-service-fabric-versions"></a>Obsługiwane wersje sieci szkieletowej usług

Upewnij się, że w klastrze jest zawsze uruchomiona obsługiwana wersja usługi Azure Service Fabric. Co najmniej 60 dni po ogłoszeniu wydania nowej wersji sieci szkieletowej usług, kończy się obsługa poprzedniej wersji. Ogłoszenia o nowych wersjach znajdziesz w [blogu zespołu Sieci szkieletowej usług](https://azure.microsoft.com/updates/?product=service-fabric).

Szczegółowe informacje na temat utrzymywania obsługiwanej wersji sieci szkieletowej usług w klastrze można znaleźć w następujących dokumentach:

- [Uaktualnianie klastra sieci szkieletowej usług Azure](service-fabric-cluster-upgrade.md)
- [Uaktualnianie wersji sieci szkieletowej usług, która działa w autonomicznym klastrze systemu Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Obsługiwane wersje

W poniższej tabeli wymieniono wersje sieci szkieletowej usług i daty zakończenia pomocy technicznej.

| Środowisko uruchomieniowe sieci szkieletowej usług w klastrze | Można uaktualnić bezpośrednio z wersji klastra |Kompatybilna wersja pakietu SDK lub NuGet | Koniec wsparcia |
| --- | --- |--- | --- |
| Wszystkie wersje klastra przed wersją 5.3.121 | 5.1.158.* |Wersja mniejsza lub równa wersji 2.3 |20 stycznia 2017 r. |
| 5.3.* | 5.1.158.* |Wersja mniejsza lub równa wersji 2.3 |24 lutego 2017 r. |
| 5.4.* | 5.1.158.* |Wersja mniejsza lub równa wersji 2.4 |10 maja 2017       |
| 5.5.* | 5.4.164.* |Wersja mniejsza lub równa wersji 2.5 |10 sierpnia 2017 r.    |
| 5.6.* | 5.4.164.* |Wersja mniejsza lub równa wersji 2.6 |13 października 2017 r.   |
| 5.7.* | 5.4.164.* |Wersja mniejsza lub równa wersji 2.7 |15 grudnia 2017 r.  |
| 6.0.* | 5.6.205.* |Wersja mniejsza lub równa wersji 2.8 |30 marca 2018 r.     |
| 6.1.* | 5.7.221.* |Mniej niż lub równa wersji 3.0 |15 lipca 2018 r.      |
| 6.2.* | 6.0.232.* |Wersja mniejsza lub równa wersji 3.1 |26 października 2018 r.   |
| 6.3.* | 6.1.480.* |Wersja mniejsza lub równa wersji 3.2 |31 marca 2019 r.  |
| 6.4.* | 6.2.301.* |Wersja mniejsza lub równa wersji 3.3 |15 września 2019 r. |
| 6.5.* | 6.4.617.* |Wersja mniejsza lub równa wersji 3.4 |1 sierpnia 2020 r. |
| 7.0.466.* | 6.4.664.* |Mniej niż lub równa wersji 4.0|1 sierpnia 2020 r.  |
| 7.0.466.* | 6.5.* |Mniej niż lub równa wersji 4.0|1 sierpnia 2020 r. |
| 7.0.470.* | 7.0.466.* |Mniej niż lub równa wersji 4.0 |1 sierpnia 2020 r.  |
| 7.1.409.* | 7.0.466.* |Mniej niż lub równa wersji 4.0 |Bieżąca wersja, więc nie ma daty zakończenia |

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono obsługiwane systemy operacyjne dla obsługiwanych wersji sieci szkieletowej usług.

| System operacyjny | Najwcześniejsza obsługiwana wersja sieci szkieletowej usług |
| --- | --- |
| Windows Server 2012 R2 | Wszystkie wersje |
| Windows Server 2016 | Wszystkie wersje |
| System Windows Server 1709 | 6.0 |
| System Windows Server 1803 | 6.4 |
| System Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Obsługiwane nazwy wersji

W poniższej tabeli wymieniono nazwy wersji sieci szkieletowej usług i odpowiadające im numery wersji.

| Nazwa wersji | Numer wersji systemu Windows | Numer wersji systemu Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | Nie dotyczy |
| 5.3 1. | 5.3.204.9494 | Nie dotyczy |
| 5.3 Cu2 | 5.3.301.9590 | Nie dotyczy |
| 5.3 Cu3 | 5.3.311.9590 | Nie dotyczy |
| 5.4 Cu2 | 5.4.164.9494 | Nie dotyczy |
| 5,5 1 YCH | 5.5.216.0    | Nie dotyczy |
| 5,5 cu2 | 5.5.219.0    | Nie dotyczy |
| 5,5 cu3 | 5.5.227.0    | Nie dotyczy |
| 5,5 cu4 | 5.5.232.0    | Nie dotyczy |
| 5.6 RTO | 5.6.204.9494 | Nie dotyczy |
| 5.6 Cu2 | 5.6.210.9494 | Nie dotyczy |
| 5.6 Cu3 | 5.6.220.9494 | Nie dotyczy |
| 5.7 RTO | 5.7.198.9494 | Nie dotyczy |
| 5.7 Cu4 | 5.7.221.9494 | Nie dotyczy |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 1 1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 cu2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 1 1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 Cu2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 Cu3 | 6.1.472.9494 | Nie dotyczy |
| 6.1 Cu4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 Cu1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 Cu2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 Cu3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 1. | 6.3.176.9494 | 6.3.124.1 |
| 6.3 1. | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 Cu2 | 6.4.622.9590 | Nie dotyczy |
| 6.4 Cu3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 Cu4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 Cu5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 Cu6 | 6.4.658.9590 | Nie dotyczy |
| 6.4 Cu7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 Cu8 | 6.4.670.9590 | Nie dotyczy |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 Cu1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 cu2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 Cu3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 cu5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 cu3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 cu4 | 7.0.470.9590 | 7.0.469.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
