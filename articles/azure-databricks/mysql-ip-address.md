---
title: Aktualizowanie tabel tras i zapór Azure Databricks przy użyciu nowych adresów IP MySQL
description: Dowiedz się, jak aktualizować tabele tras i zapory Azure Databricks przy użyciu nowych adresów IP programu MySQL.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 5bd4822c21e6d9dbe8f3534dd0171a6f785bd5ac
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325915"
---
# <a name="update-your-azure-databricks-route-tables-and-firewalls-with-new-mysql-ips"></a>Aktualizowanie tabel tras i zapór Azure Databricks przy użyciu nowych adresów IP MySQL

Wszystkie adresy IP Azure Database for MySQL, używane jako Azure Databricks magazynu metadanych, są zmieniane. Zaktualizuj tabele tras Azure Databricks lub zapory za pomocą nowych adresów IP programu MySQL przed 30 czerwca 2020, aby uniknąć przerw w działaniu.

Obszar roboczy Azure Databricks wdrożony we własnej sieci wirtualnej ma dołączoną [tabelę tras](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/udr#--metastore-artifact-blob-storage-log-blob-storage-and-event-hub-endpoint-ip-addresses) . Tabela tras może bezpośrednio zawierać adres IP Azure Databricks magazynu metadanych lub trasę do urządzenia z zaporą lub serwerem proxy, które mogą być listy dozwolonych przez adres.

## <a name="recommended-actions"></a>Zalecane akcje

Aby uniknąć przerw w działaniu usługi, przejrzyj i Zastosuj te akcje przed 30 czerwca 2020.

* Sprawdź, czy w tabeli tras ma wpływ zmiana w aktualizacji adresu IP usługi Azure MySQL.

* Skorzystaj z tabeli w poniższej sekcji, aby wyszukać nowe adresy IP. Dla każdego regionu może istnieć wiele adresów IP używanych jako podstawowy i pomocniczy adres IP bram Azure Database for MySQL. Podstawowy adres IP to bieżący adres IP bramy, a drugi — adresy IP trybu failover w przypadku awarii podstawowej. Aby zapewnić kondycję usługi, należy zezwolić na ruch wychodzący do wszystkich adresów IP. Jeśli używasz [zewnętrznego magazynu metadanych](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore), upewnij się, że prawidłowy adres IP odpowiadający za powiadomienia usługi Azure MySQL jest kierowany lub listy dozwolonych.

* Zaktualizuj tabelę tras, zaporę lub urządzenie serwera proxy przy użyciu nowych adresów IP.

## <a name="updated-mysql-ip-addresses"></a>Zaktualizowane adresy IP MySQL

Poniższa tabela zawiera wszystkie adresy IP, które muszą być listy dozwolonych. Adresy IP **pogrubione** to nowe adresy IP. 

| Nazwa regionu          | Adresy IP bramy                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| Australia Środkowa    | 20.36.105.0                                                                                                |
| Australia Środkowa 2  | 20.36.113.0                                                                                                |
| Australia Wschodnia       | 13.75.149.87<br><br>40.79.161.1                                                                            |
| Australia Południowo-Wschodnia | 191.239.192.109<br><br>13.73.109.251                                                                       |
| Brazylia Południowa         | 104.41.11.5 <br><br> 191.233.201.8 <br><br> **191.233.200.16**                                             |
| Kanada Środkowa       | 40.85.224.249                                                                                              |
| Kanada Wschodnia          | 40.86.226.166                                                                                              |
| Środkowe stany USA           | 23.99.160.139<br><br>13.67.215.62<br><br>**52.182.136.37**<br><br>**52.182.136.38**                        |
| Chiny Wschodnie           | 139.219.130.35                                                                                             |
| Chiny Wschodnie 2         | 40.73.82.1                                                                                                 |
| Chiny Północne          | 139.219.15.17                                                                                              |
| Chiny Północne 2        | 40.73.50.0                                                                                                 |
| Azja Wschodnia            | 191.234.2.139<br><br>52.175.33.150<br><br>**13.75.33.20**<br><br>**13.75.33.21**                           |
| Wschodnie stany USA              | 40.121.158.30<br>191.238.6.43                                                                              |
| Wschodnie stany USA 2            | 40.79.84.180<br><br>191.239.224.107<br><br>52.177.185.181<br><br>**40.70.144.38**<br><br>**52.167.105.38** |
| Francja Środkowa       | 40.79.137.0<br><br>40.79.129.1                                                                             |
| Niemcy Środkowe      | 51.4.144.100                                                                                               |
| Niemcy Północne wschód   | 51.5.144.179                                                                                               |
| Indie Środkowe        | 104.211.96.159                                                                                             |
| Indie Południowe          | 104.211.224.146                                                                                            |
| Indie Zachodnie           | 104.211.160.80                                                                                             |
| Japonia Wschodnia           | 13.78.61.196<br><br>191.237.240.43                                                                         |
| Japonia Zachodnia           | 104.214.148.156<br><br>191.238.68.11<br><br>**40.74.96.6**<br><br>**40.74.96.7**                           |
| Korea Środkowa        | 52.231.32.42                                                                                               |
| Korea Południowa          | 52.231.200.86                                                                                              |
| Północno-środkowe stany USA     | 23.96.178.199<br><br>23.98.55.75<br><br>**52.162.104.35**<br><br>**52.162.104.36**                         |
| Europa Północna         | 40.113.93.91<br><br>191.235.193.75<br><br>**52.138.224.6**<br><br>**52.138.224.7**                         |
| Północna Republika Południowej Afryki   | 102.133.152.0                                                                                              |
| Zachodnia Republika Południowej Afryki    | 102.133.24.0                                                                                               |
| Południowo-środkowe stany USA     | 13.66.62.124<br><br>23.98.162.75<br><br>**104.214.16.39**<br><br>**20.45.120.0**                           |
| Azja Południowo-Wschodnia      | 104.43.15.0<br><br>23.100.117.95<br><br>**40.78.233.2**<br><br>**23.98.80.12**                             |
| Środkowy Zjednoczone Emiraty Arabskie          | 20.37.72.64                                                                                                |
| Północne Zjednoczone Emiraty Arabskie            | 65.52.248.0                                                                                                |
| Południowe Zjednoczone Królestwo             | 51.140.184.11                                                                                              |
| Zachodnie Zjednoczone Królestwo              | 51.141.8.11                                                                                                |
| Zachodnio-środkowe stany USA      | 13.78.145.25                                                                                               |
| Europa Zachodnia          | 40.68.37.158<br><br>191.237.232.75<br><br>**13.69.105.208**                                                |
| Zachodnie stany USA              | 104.42.238.205<br><br>23.99.34.75                                                                          |
| Zachodnie stany USA 2            | 13.66.226.202                                                                                              |

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie Azure Databricks w sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)
* [Zewnętrzny magazyn metadanych Apache Hive](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)
