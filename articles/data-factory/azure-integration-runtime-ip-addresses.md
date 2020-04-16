---
title: Adresy IP usługi Azure Integration Runtime
description: Dowiedz się, z jakich adresów IP należy zezwolić na ruch przychodzący, aby prawidłowo skonfigurować zapory do zabezpieczania dostępu sieciowego do magazynów danych.
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
ms.openlocfilehash: b0ba47ff28208bce1a6fa6ec300a261d788167de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415593"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Adresy IP usługi Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Adresy IP używane przez środowisko uruchomieniowe integracji platformy Azure zależą od regionu, w którym znajduje się środowisko uruchomieniowe integracji platformy Azure. *Wszystkie* Środowiska wykonawcze integracji platformy Azure, które znajdują się w tym samym regionie, używają tych samych zakresów adresów IP.

> [!IMPORTANT]  
> Przepływy danych nie korzystają obecnie z tych adresów IP. 
>
> Tych zakresów adresów IP można używać do przenoszenia danych, potoku i wykonywania działań zewnętrznych. Te zakresy adresów IP mogą służyć do umieszczania na białej liście w magazynach danych/ sieciowej grupie zabezpieczeń (NSG)/ zapory dostępu przychodzącego ze środowiska wykonawczego integracji platformy Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Adresy IP środowiska wykonawczego integracji platformy Azure: określone regiony

Zezwalaj na ruch z adresów IP wymienionych dla środowiska wykonawczego integracji platformy Azure w określonym regionie platformy Azure, w którym znajdują się zasoby:

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
| Republika Południowej Afryki   | Republika Południowej Afryki Północ  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Wielka Brytania | Południowe Zjednoczone Królestwo            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Stany Zjednoczone  | Środkowe stany USA          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | Wschodnie stany USA             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | Wschodnie stany USA 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | Wschodnie Stany Zjednoczone 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | Północno-środkowe stany USA    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | Południowo-środkowe stany USA    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | Zachodnio-środkowe stany USA     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | Zachodnie stany USA             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | Zachodnie stany USA 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | US Gov Wirginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Znany problem z usługą Azure Storage

* Podczas łączenia się z kontem usługi Azure Storage reguły sieci IP nie mają wpływu na żądania pochodzące ze środowiska wykonawczego integracji platformy Azure w tym samym regionie co konto magazynu. Aby uzyskać więcej informacji, [zapoznaj się z tym artykułem](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Zamiast tego zalecamy korzystanie z [zaufanych usług podczas łączenia się z usługą Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Następne kroki

* [Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w usłudze Azure Data Factory](data-movement-security-considerations.md)
