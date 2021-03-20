---
title: Informacje o stanie aplikacji w usłudze Azure Spring Cloud
description: Poznaj kategorie stanu aplikacji w chmurze Azure wiosennej
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ec87c5523d6f608363ca615037f20365705609df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90892865"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Informacje o stanie aplikacji w usłudze Azure Spring Cloud

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Interfejs użytkownika chmury wiosennej platformy Azure dostarcza informacje o stanie uruchomionych aplikacji.  Istnieje opcja **aplikacje** dla każdej grupy zasobów w subskrypcji, która wyświetla ogólny stan typów aplikacji.  Dla każdego typu aplikacji jest wyświetlane **wystąpienie aplikacji**.

## <a name="apps-status"></a>Stan aplikacji
Aby wyświetlić stan ogólny typu aplikacji, wybierz pozycję **aplikacje** w lewym okienku nawigacji grupy zasobów. Spowoduje to wyświetlenie stanu wdrożonej aplikacji:

* **Stan aprowizacji** przedstawia stan aprowizacji wdrożenia
* **Uruchomione wystąpienie** pokazuje, ile wystąpień aplikacji jest uruchomionych/ile wystąpień aplikacji jest potrzebnych. Jeśli aplikacja powinna zostać zatrzymana, ta kolumna będzie wyświetlana jako *zatrzymana*.
* **Zarejestrowane wystąpienie** pokazuje, ile wystąpień aplikacji jest zarejestrowanych w Eureka/ile jest potrzebnych wystąpień aplikacji. Jeśli aplikacja powinna zostać zatrzymana, ta kolumna będzie wyświetlana jako *zatrzymana*.


 ![Stan aplikacji](media/spring-cloud-concept-app-status/apps-ui-status.png)

**Stan wdrożenia jest raportowany jako jedna z następujących wartości:**

| Wyliczenie | Definicja |
|:--:|:----------------:|
| Uruchomienie | Wdrożenie powinno być uruchomione. |
| Zatrzymano | Wdrożenie powinno zostać zatrzymane. |

**Stan aprowizacji jest dostępny tylko w interfejsie wiersza polecenia.  Jest raportowany jako jedna z następujących wartości:**

| Wyliczenie | Definicja |
|:--:|:----------------:|
| Tworzenie | Tworzony jest zasób. |
| Aktualizowanie | Zasób jest aktualizowany. |
| Powodzenie | Pomyślnie dostarczono zasoby i wdrożono plik binarny. |
| Niepowodzenie | Nie można osiągnąć celu *zakończonego powodzeniem* . |
| Usuwanie | Trwa usuwanie zasobu. Zapobiega to operacji, a zasób nie jest dostępny w tym stanie. |

## <a name="app-instances-status"></a>Stan wystąpień aplikacji

Aby wyświetlić stan określonego wystąpienia wdrożonej aplikacji, kliknij **nazwę** aplikacji w interfejsie użytkownika **aplikacji** . Zostaną wyświetlone wyniki:
* **Stan**: czy wystąpienie jest uruchomione, czy jego stan
* **DiscoveryStatus**: zarejestrowany stan wystąpienia aplikacji na serwerze Eureka

 ![Stan wystąpień aplikacji](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**Stan wystąpienia jest raportowany jako jedna z następujących wartości:**

| Wyliczenie | Definicja |
|:--:|:----------------:|
| Uruchamianie | Plik binarny został pomyślnie wdrożony w danym wystąpieniu. Rozruch wystąpienia pliku JAR może się nie powieść, ponieważ nie można prawidłowo uruchomić systemu jar. |
| Uruchomienie | Wystąpienie działa. |
| Niepowodzenie | Uruchomienie przez wystąpienie aplikacji danych binarnych użytkownika po kilku próbach nie powiodło się. |
| Zakończona | Trwa zamykanie wystąpienia aplikacji. |

**Stan odnajdywania wystąpienia jest raportowany jako jedna z następujących wartości:**

| Wyliczenie | Definicja |
|:--:|:----------------:|
| Konfigurowanie | Wystąpienie aplikacji jest zarejestrowane w usłudze Eureka i gotowe do odbierania ruchu |
| OUT_OF_SERVICE | Wystąpienie aplikacji jest zarejestrowane w usłudze Eureka i może odbierać ruch. ale zamyka się w celu zamierzonego ruchu. |
| NOTUJ | Wystąpienie aplikacji nie jest zarejestrowane w Eureka lub jest zarejestrowane, ale nie może odbierać ruchu. |


## <a name="see-also"></a>Zobacz też
* [Przygotowywanie aplikacji sprężynowej lub steeltoe na potrzeby wdrażania w chmurze Azure wiosennej](spring-cloud-tutorial-prepare-app-deployment.md)