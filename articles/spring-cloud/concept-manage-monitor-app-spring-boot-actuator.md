---
title: Zarządzanie aplikacją i monitorowanie jej przy użyciu programu Azure Spring Boot Actuator
description: Dowiedz się, jak zarządzać aplikacją i monitorować ją za pomocą urządzenia uruchamiającego rozruch sprężynowy.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878308"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Zarządzanie aplikacją i monitorowanie jej przy użyciu programu Azure Spring Boot Actuator

**Ten artykuł ma zastosowanie do:** ✔️ Java

Po wdrożeniu nowego pliku binarnego w aplikacji możesz chcieć sprawdzić funkcjonalność i wyświetlić informacje o działającej aplikacji. W tym artykule wyjaśniono, jak uzyskać dostęp do interfejsu API z punktu końcowego testu dostarczonego przez chmurę usługi Azure wiosną i uwidocznić funkcje gotowe do użycia w środowisku produkcyjnym.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz aplikację z systemem wiosny 2. x, która może zostać pomyślnie wdrożona i uruchomiona w usłudze Azure wiosennej w chmurze.  Zobacz [Szybki Start: uruchamianie istniejącej aplikacji w chmurze platformy Azure przy użyciu Azure Portal](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Weryfikowanie aplikacji przy użyciu punktu końcowego testu
1. Przejdź do **pulpitu nawigacyjnego aplikacji** i kliknij swoją aplikację, aby wprowadzić stronę przeglądu aplikacji.

1. W okienku **Przegląd** powinien zostać wyświetlony **punkt końcowy testu**.  Uzyskaj dostęp do tego punktu końcowego z poziomu wiersza polecenia lub przeglądarki i obserwuj odpowiedź interfejsu API.

1. Zwróć uwagę na identyfikator URI **punktu końcowego testu** , który będzie używany w dalszej części.

>[!TIP]
> * Jeśli aplikacja zwróci stronę frontonu i odwołuje się do innych plików za pomocą ścieżki względnej, upewnij się, że punkt końcowy testu kończy się ukośnikiem (/). Dzięki temu plik CSS zostanie załadowany poprawnie.
> * Jeśli przeglądasz interfejs API z przeglądarce, a Twoja przeglądarka wymaga wprowadzenia poświadczeń logowania w celu wyświetlenia strony, użyj [dekodowania adresu URL](https://www.urldecoder.org/) , aby zdekodować punkt końcowy testu. Dekodowanie adresu URL zwraca adres URL w postaci "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/ \<app-name> / \<deployment-name> ".  Ten formularz służy do uzyskiwania dostępu do punktu końcowego.

## <a name="add-actuator-dependency"></a>Dodaj zależność siłownika

Aby dodać obiekt uruchamiającym do projektu opartego na Maven, Dodaj zależność "Starter":

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Skompiluj nowy plik binarny i Wdróż go w aplikacji.

## <a name="enable-production-ready-features"></a>Włącz funkcje gotowe do produkcji
Punkty końcowe urządzenia uruchamiającego umożliwiają monitorowanie aplikacji i korzystanie z niej. Domyślnie aplikacja do rozruchu sprężynowego uwidacznia `health` i `info` punkty końcowe pokazujące informacje o aplikacji i kondycji.

Aby obserwować konfigurację i konfigurowalne środowisko, należy również włączyć funkcję `env` i `configgrops` punkty końcowe.

1. Przejdź do okienka **Przegląd** aplikacji, w menu Ustawienia kliknij pozycję **Konfiguracja** , a następnie przejdź do strony Konfiguracja **zmiennych środowiskowych** .
1. Dodaj następujące właściwości zgodnie z formularzem "klucz: wartość". To środowisko spowoduje otwarcie punktu końcowego uruchamiającego sprężynę "ENV", "Health", "info" (informacje).

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Kliknij przycisk **Zapisz** , aplikacja zostanie automatycznie ponownie uruchomiona i załaduje nowe zmienne środowiskowe.

Możesz teraz wrócić do okienka przegląd aplikacji i poczekać, aż stan aprowizacji zostanie zmieniony na "powodzenie".  Będzie więcej niż jedno uruchomione wystąpienie.

> [!Note] 
> Po udostępnieniu aplikacji publicznej te punkty końcowe urządzenia uruchamiającego są również udostępniane publicznie. Można ukryć wszystkie punkty końcowe, usuwając zmienne środowiskowe `management.endpoints.web.exposure.include` i ustawiając `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Wyświetl punkt końcowy siłowni, aby wyświetlić informacje o aplikacji
1. Teraz możesz uzyskać dostęp do adresu URL, `"<test-endpoint>/actuator/"` Aby zobaczyć wszystkie punkty końcowe udostępniane przez siłownik rozruchu sprężynowego.
1. Adres URL dostępu `"<test-endpoint>/actuator/env"` , można zobaczyć aktywne profile używane przez aplikację i wszystkie załadowane zmienne środowiskowe.
1. Jeśli chcesz wyszukać określone środowisko, możesz uzyskać dostęp do adresu URL,  `"<test-endpoint>/actuator/env/{toMatch}"` Aby go wyświetlić.

Aby wyświetlić wszystkie wbudowane punkty końcowe, zobacz [Uwidacznianie punktów końcowych](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>Następne kroki

* [Informacje o metrykach dla chmury wiosennej platformy Azure](spring-cloud-concept-metrics.md)
* [Informacje o stanie aplikacji w usłudze Azure Spring Cloud](spring-cloud-concept-app-status.md)

