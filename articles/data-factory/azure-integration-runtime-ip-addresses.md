---
title: Adresy IP usługi Azure Integration Runtime
description: Dowiedz się, które adresy IP należy zezwolić na ruch przychodzący z programu, w celu poprawnego skonfigurowania zapór do zabezpieczania dostępu sieciowego do magazynów danych.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: e3fae34d0bb1aaaa2471f1cc9fd55ef9b4b73dfd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594308"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Adresy IP usługi Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Adresy IP używane przez Azure Integration Runtime są zależne od regionu, w którym znajduje się środowisko Azure Integration Runtime. *Wszystkie* Środowiska Azure Integration Runtime, które znajdują się w tym samym regionie, używają tych samych zakresów adresów IP.

> [!IMPORTANT]  
> Przepływy danych nie obsługują używania stałych zakresów adresów IP.
>
> Możesz użyć tych zakresów adresów IP na potrzeby przenoszenia danych, wykonywania potoku i działań zewnętrznych. Te zakresy adresów IP mogą być używane dla listy dozwolonych w magazynach danych/sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)/zaporach na potrzeby dostępu przychodzącego z platformy Azure Integration Runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime adresy IP: określone regiony

Zezwalaj na ruch z adresów IP wymienionych dla środowiska Azure Integration Runtime w określonym regionie platformy Azure, w którym znajdują się zasoby:

|                | Region              | Adresy IP                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Azja           | Azja Wschodnia           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Azja Południowo-Wschodnia      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Australia      | Australia Wschodnia      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Australia Południowo-Wschodnia | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brazylia         | Brazylia Południowa        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Kanada         | Kanada Środkowa      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Chiny          | Chiny Wschodnie 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| Europa         | Europa Północna        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa Zachodnia         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Francja         | Francja Środkowa      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| Indie          | Indie Środkowe       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japonia          | Japonia Wschodnia          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Korea          | Korea Środkowa       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Republika Południowej Afryki   | Północna Republika Południowej Afryki  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Wielka Brytania | Południowe Zjednoczone Królestwo            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Stany Zjednoczone  | Środkowe stany USA          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | Wschodnie stany USA             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | Wschodnie stany USA 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | Wschodnie stany USA 2 — EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | Północno-środkowe stany USA    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | Południowo-środkowe stany USA    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | Zachodnio-środkowe stany USA     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | Zachodnie stany USA             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | Zachodnie stany USA 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | US Gov Wirginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Znany problem z usługą Azure Storage

* W przypadku nawiązywania połączenia z kontem usługi Azure Storage reguły sieci IP nie mają wpływu na żądania pochodzące z platformy Azure Integration Runtime w tym samym regionie, w którym znajduje się konto magazynu. Aby uzyskać więcej informacji, [zapoznaj się z tym artykułem](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Zamiast tego sugerujemy używanie [zaufanych usług podczas nawiązywania połączenia z usługą Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Następne kroki

* [Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w Azure Data Factory](data-movement-security-considerations.md)
