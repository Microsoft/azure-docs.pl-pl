---
title: Adresy IP zarządzania usługą Azure HDInsight
description: Dowiedz się, które adresy IP należy zezwolić na ruch przychodzący z programu, w celu poprawnego skonfigurowania sieciowych grup zabezpieczeń i tras zdefiniowanych przez użytkownika dla sieci wirtualnych za pomocą usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 682db0ef52e624a23d95aa18a007f05a78f39677
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518791"
---
# <a name="hdinsight-management-ip-addresses"></a>Adresy IP zarządzania usługą HDInsight

W tym artykule wymieniono adresy IP używane przez usługi Azure HDInsight Health i Management Services. Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) lub tras zdefiniowanych przez użytkownika (UDR), może być konieczne dodanie niektórych z tych adresów IP do listy dozwolonych dla przychodzącego ruchu sieciowego.

## <a name="introduction"></a>Wprowadzenie
 
> [!Important]
> W większości przypadków można teraz używać [tagów usługi](hdinsight-service-tags.md) dla sieciowych grup zabezpieczeń, zamiast ręcznie dodawać adresy IP. Adresy IP nie będą publikowane dla nowych regionów świadczenia usługi Azure i będą miały tylko opublikowane Tagi usługi. Statyczne adresy IP dla adresów IP zarządzania będą ostatecznie przestarzałe.

W przypadku używania sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) lub tras zdefiniowanych przez użytkownika (UDR) do kontrolowania ruchu przychodzącego do klastra usługi HDInsight należy upewnić się, że klaster może komunikować się z krytycznymi usługami Azure Health i Management.  Niektóre adresy IP dla tych usług są specyficzne dla regionu, a niektóre z nich dotyczą wszystkich regionów świadczenia usługi Azure. Może być również konieczne zezwolenie na ruch z usługi Azure DNS, jeśli nie jest używany niestandardowy serwer DNS.

Jeśli potrzebujesz adresów IP dla regionu, którego nie ma na liście, możesz użyć [interfejsu API odnajdywania tagów usługi](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) , aby znaleźć adresy IP dla danego regionu. Jeśli nie możesz użyć interfejsu API, Pobierz [plik JSON znacznika usługi](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) i wyszukaj żądany region.

W poniższych sekcjach omówiono konkretne adresy IP, które muszą być dozwolone.

## <a name="azure-dns-service"></a>Usługa Azure DNS

Jeśli używasz usługi DNS udostępnionej przez platformę Azure, Zezwól na dostęp do __168.63.129.16__ na porcie 53 dla protokołów TCP i UDP. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) . Jeśli używasz niestandardowej usługi DNS, Pomiń ten krok.

## <a name="health-and-management-services-all-regions"></a>Usługi kondycji i zarządzania: wszystkie regiony

Zezwalaj na ruch z następujących adresów IP dla usług Azure HDInsight Health i Management Services, które mają zastosowanie do wszystkich regionów platformy Azure:

| Źródłowy adres IP | Miejsce docelowe  | Kierunek |
| ---- | ----- | ----- |
| 168.61.49.99 | \*: 443 | Przychodzący |
| 23.99.5.239 | \*: 443 | Przychodzący |
| 168.61.48.131 | \*: 443 | Przychodzący |
| 138.91.141.162 | \*: 443 | Przychodzący |

## <a name="health-and-management-services-specific-regions"></a>Usługi kondycji i zarządzania: określone regiony

Zezwalaj na ruch z adresów IP wymienionych dla usług Azure HDInsight Health i Management w określonym regionie platformy Azure, w którym znajdują się zasoby:

> [!IMPORTANT]  
> Jeśli w regionie platformy Azure, którego używasz, nie ma na liście, użyj funkcji [tag usługi](hdinsight-service-tags.md) dla sieciowych grup zabezpieczeń.

| Country (Kraj) | Region | Dozwolone źródłowe adresy IP | Dozwolone miejsce docelowe | Kierunek |
| ---- | ---- | ---- | ---- | ----- |
| Azja | Azja Wschodnia | 23.102.235.122</br>52.175.38.134 | \*: 443 | Przychodzący |
| &nbsp; | Southeast Asia | 13.76.245.160</br>13.76.136.249 | \*: 443 | Przychodzący |
| Australia | Australia Wschodnia | 104.210.84.115</br>13.75.152.195 | \*: 443 | Przychodzący |
| &nbsp; | Australia Południowo-Wschodnia | 13.77.2.56</br>13.77.2.94 | \*: 443 | Przychodzący |
| Brazylia | Brazil South | 191.235.84.104</br>191.235.87.113 | \*: 443 | Przychodzący |
| Kanada | Kanada Wschodnia | 52.229.127.96</br>52.229.123.172 | \*: 443 | Przychodzący |
| &nbsp; | Kanada Środkowa | 52.228.37.66</br>52.228.45.222 |\*: 443 | Przychodzący |
| Chiny | Chiny Północne | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*: 443 | Przychodzący |
| &nbsp; | Chiny Wschodnie | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*: 443 | Przychodzący |
| &nbsp; | Chiny Północne 2 | 40.73.37.141</br>40.73.38.172 | \*: 443 | Przychodzący |
| &nbsp; | Chiny Wschodnie 2 | 139.217.227.106</br>139.217.228.187 | \*: 443 | Przychodzący |
| Europa | Europa Północna | 52.164.210.96</br>13.74.153.132 | \*: 443 | Przychodzący |
| &nbsp; | West Europe| 52.166.243.90</br>52.174.36.244 | \*: 443 | Przychodzący |
| Francja | Francja Środkowa| 20.188.39.64</br>40.89.157.135 | \*: 443 | Przychodzący |
| Niemcy | Niemcy Środkowe | 51.4.146.68</br>51.4.146.80 | \*: 443 | Przychodzący |
| &nbsp; | Niemcy Północno-Wschodnie | 51.5.150.132</br>51.5.144.101 | \*: 443 | Przychodzący |
| Indie | Indie Środkowe | 52.172.153.209</br>52.172.152.49 | \*: 443 | Przychodzący |
| &nbsp; | Indie Południowe | 104.211.223.67<br/>104.211.216.210 | \*: 443 | Przychodzący |
| Japonia | Japonia Wschodnia | 13.78.125.90</br>13.78.89.60 | \*: 443 | Przychodzący |
| &nbsp; | Japonia Zachodnia | 40.74.125.69</br>138.91.29.150 | \*: 443 | Przychodzący |
| Korea | Korea Środkowa | 52.231.39.142</br>52.231.36.209 | \*: 443 | Przychodzący |
| &nbsp; | Korea Południowa | 52.231.203.16</br>52.231.205.214 | \*: 443 | Przychodzący
| Zjednoczone Królestwo | Zachodnie Zjednoczone Królestwo | 51.141.13.110</br>51.141.7.20 | \*: 443 | Przychodzący |
| &nbsp; | Południowe Zjednoczone Królestwo | 51.140.47.39</br>51.140.52.16 | \*: 443 | Przychodzący |
| Stany Zjednoczone | Central US | 13.89.171.122</br>13.89.171.124 | \*: 443 | Przychodzący |
| &nbsp; | East US | 13.82.225.233</br>40.71.175.99 | \*: 443 | Przychodzący |
| &nbsp; | Północno-środkowe stany USA | 157.56.8.38</br>157.55.213.99 | \*: 443 | Przychodzący |
| &nbsp; | Zachodnio-środkowe stany USA | 52.161.23.15</br>52.161.10.167 | \*: 443 | Przychodzący |
| &nbsp; | Zachodnie stany USA | 13.64.254.98</br>23.101.196.19 | \*: 443 | Przychodzący |
| &nbsp; | Zachodnie stany USA 2 | 52.175.211.210</br>52.175.222.222 | \*: 443 | Przychodzący |
| &nbsp; | Północne Zjednoczone Emiraty Arabskie | 65.52.252.96</br>65.52.252.97 | \*: 443 | Przychodzący |
| &nbsp; | Środkowy Zjednoczone Emiraty Arabskie | 20.37.76.96</br>20.37.76.99 | \*: 443 | Przychodzący |

Aby uzyskać informacje dotyczące adresów IP, które mają być używane na potrzeby Azure Government, zobacz dokument [Azure Government Intelligence + Analytics](../azure-government/compare-azure-government-global-azure.md) .

Aby uzyskać więcej informacji, zobacz [sterowanie ruchem sieciowym](./control-network-traffic.md).

Jeśli używasz tras zdefiniowanych przez użytkownika (UDR), należy określić trasę i zezwolić na ruch wychodzący z sieci wirtualnej do powyższych adresów IP przy użyciu następnego przeskoku ustawionego na wartość "Internet".

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md)
* [Tagi usług sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight](hdinsight-service-tags.md)
