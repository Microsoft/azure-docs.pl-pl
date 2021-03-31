---
title: Zabezpieczanie zabezpieczeń na hostach maszyn wirtualnych AKS
description: Dowiedz się więcej o zabezpieczaniu zabezpieczeń w systemie operacyjnym hosta maszyny wirtualnej AKS
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 84b826ce33b5395db5bd38e883b3a0fb3425725b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86244042"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Ograniczanie zabezpieczeń dla systemu operacyjnego hosta węzła agenta AKS

Usługa Azure Kubernetes Service (AKS) jest bezpieczną usługą zgodną ze standardami SOC, ISO, PCI DSS i HIPAA. W tym artykule opisano zaostrzone zabezpieczenia stosowane do AKS hostów maszyn wirtualnych. Aby uzyskać więcej informacji o zabezpieczeniach AKS, zobacz [pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](./concepts-security.md).

> [!Note]
> Ten dokument jest objęty zakresem agentów systemu Linux wyłącznie w programie AKS.

Klastry AKS są wdrażane na maszynach wirtualnych hosta, w których jest używany zoptymalizowany pod kątem zabezpieczeń system operacyjny używany do kontenerów działających w systemie AKS. Ten system operacyjny hosta jest oparty na obrazie **Ubuntu 16.04. LTS** z dodatkową funkcjonalnością zabezpieczeń i zastosowanymi optymalizacjami (zobacz szczegóły dotyczące zabezpieczania zabezpieczeń).

Celem systemu operacyjnego hosta z ograniczeniami zabezpieczeń jest zredukowanie obszaru ataków i optymalizację wdrożenia kontenerów w bezpieczny sposób.

> [!Important]
> System operacyjny z ograniczeniami zabezpieczeń nie jest w wersji testowej. Chociaż są nakładane na testy porównawcze CIS, cel nie jest zgodny z modelem CIS. Celem zapewnienia funkcjonalności systemu operacyjnego hosta jest zbieżność poziomu zabezpieczeń spójnego z wewnętrznymi standardami zabezpieczeń hostów firmy Microsoft.

## <a name="security-hardening-features"></a>Funkcje ograniczania zabezpieczeń

* AKS zapewnia domyślnie system operacyjny hosta zoptymalizowanego pod kątem zabezpieczeń. Nie ma możliwości wybrania alternatywnego systemu operacyjnego.

* Na platformie Azure są stosowane codzienne poprawki (w tym poprawki zabezpieczeń) do AKS hostów maszyn wirtualnych. Niektóre z tych poprawek będą wymagały ponownego uruchomienia, a inne nie. Użytkownik jest odpowiedzialny za planowanie ponownych uruchomień hosta maszyn wirtualnych AKS w razie potrzeby. Aby uzyskać informacje na temat sposobu automatyzowania poprawek AKS, zobacz [poprawek węzłów AKS](./node-updates-kured.md).

## <a name="what-is-configured"></a>Co jest skonfigurowane

| SIC  | Opis inspekcji|
|---|---|
| 1.1.1.1 |Upewnij się, że Instalowanie systemów plików cramfs jest wyłączone|
| 1.1.1.2 |Upewnij się, że Instalowanie systemów plików freevxfs jest wyłączone|
| 1.1.1.3 |Upewnij się, że Instalowanie systemów plików JFFS2 jest wyłączone|
| 1.1.1.4 |Upewnij się, że Instalowanie systemów plików HFS jest wyłączone|
| 1.1.1.5 |Upewnij się, że Instalowanie HFS i systemów plików jest wyłączone|
|1.4.3 |Upewnij się, że uwierzytelnianie jest wymagane dla trybu pojedynczego użytkownika |
|1.7.1.2 |Upewnij się, że transparent ostrzegawczy logowania lokalnego jest skonfigurowany prawidłowo |
|1.7.1.3 |Upewnij się, że transparent ostrzegawczy logowania zdalnego jest skonfigurowany prawidłowo |
|1.7.1.5 |Upewnij się, że zostały skonfigurowane uprawnienia do/etc/Issue |
|1.7.1.6 |Upewnij się, że zostały skonfigurowane uprawnienia do/etc/Issue.NET |
|ppkt |Upewnij się, że wartość--Streaming-Connection-Idle-Timeout nie jest ustawiona na 0 |
|3.1.2 |Upewnij się, że wysyłanie przekierowywanie pakietów jest wyłączone |
|3.2.1 |Upewnij się, że źródłowe pakiety routingu nie zostały zaakceptowane |
|3.2.2 |Upewnij się, że przekierowania protokołu ICMP nie są akceptowane |
|ppkt |Upewnij się, że bezpieczne przekierowania protokołu ICMP nie zostały zaakceptowane |
|3.2.4 |Upewnij się, że są zarejestrowane podejrzane pakiety |
|3.3.1 |Upewnij się, że anonse routera IPv6 nie zostały zaakceptowane |
|3.5.1 |Upewnij się, że DCCP jest wyłączone |
|3.5.2 |Upewnij się, że SCTP jest wyłączone |
|ppkt |Upewnij się, że RDS jest wyłączony |
|3.5.4 |Upewnij się, że TIPC jest wyłączone |
|4.2.1.2 |Upewnij się, że skonfigurowano rejestrowanie |
|5.1.2 |Upewnij się, że zostały skonfigurowane uprawnienia do/etc/crontab |
|ppkt |Upewnij się, że przekazywanie protokołu SSH X11 jest wyłączone |
|5.2.5 |Upewnij się, że MaxAuthTries SSH jest ustawiony na 4 lub mniej |
|5.2.8 |Upewnij się, że logowanie główne SSH jest wyłączone |
|5.2.10 |Upewnij się, że protokół SSH PermitUserEnvironment jest wyłączony |
|5.2.11 |Upewnij się, że są używane tylko zatwierdzone maksymalne algorytmy |
|5.2.12 |Upewnij się, że skonfigurowano interwał limitu czasu bezczynności SSH |
|5.2.13 |Upewnij się, że LoginGraceTime SSH jest ustawiona na jedną minutę lub mniejszą |
|5.2.15 |Upewnij się, że jest skonfigurowany transparent ostrzeżeń SSH |
|5.3.1 |Upewnij się, że skonfigurowano wymagania dotyczące tworzenia haseł |
|5.4.1.1 |Upewnij się, że wygaśnięcie hasła wynosi 90 dni lub mniej |
|5.4.1.4 |Upewnij się, że blokada hasła nieaktywnego to 30 dni lub mniej |
|5.4.4 |Upewnij się, że domyślny maska umask użytkownika jest 027 lub bardziej restrykcyjny |
|5,6 |Zapewnianie dostępu do polecenia su jest ograniczone|

## <a name="additional-notes"></a>Uwagi dodatkowe
 
* Aby jeszcze bardziej ograniczyć obszar narażony na ataki, niektóre zbędne sterowniki modułów jądra zostały wyłączone w systemie operacyjnym.

* System operacyjny z zaostrzonymi zabezpieczeniami jest zbudowany i konserwowany specjalnie dla AKS i nie jest obsługiwany poza platformą AKS.

## <a name="next-steps"></a>Następne kroki  

Więcej informacji na temat zabezpieczeń AKS można znaleźć w następujących artykułach: 

[Azure Kubernetes Service (AKS)](./intro-kubernetes.md)

[Zagadnienia dotyczące zabezpieczeń AKS ](./concepts-security.md)

[Najlepsze rozwiązania AKS ](./best-practices.md)
