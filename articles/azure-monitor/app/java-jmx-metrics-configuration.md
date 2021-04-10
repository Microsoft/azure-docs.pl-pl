---
title: Jak skonfigurować metryki JMX — Azure Monitor Application Insights dla języka Java
description: Konfigurowanie dodatkowej kolekcji metryk JMX dla agenta Java usługi Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609612"
---
# <a name="configuring-jmx-metrics"></a>Konfigurowanie metryk JMX

Agent Java 3,0 w usłudze Application Insights domyślnie zbiera pewne metryki JMX, ale w wielu przypadkach jest to niewystarczające. W tym dokumencie opisano opcję konfiguracji JMX szczegółowo.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Jak mogę zebrać dodatkowe metryki JMX?

Kolekcję metryk JMX można skonfigurować przez dodanie ```"jmxMetrics"``` sekcji do applicationinsights.jspliku. Możesz określić nazwę metryki, która ma być wyświetlana w Azure Portal w zasobie usługi Application Insights. Należy zdefiniować nazwę obiektu i atrybut dla każdej z metryk, które mają być zbierane.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Jak mogę wiedzieć, jakie metryki są dostępne do skonfigurowania?

Ująłeś go — musisz znać nazwy obiektów i atrybuty, te właściwości różnią się w zależności od bibliotek, platform i serwerów aplikacji i często nie są dobrze udokumentowane. Aby uzyskać nazwy obiektów i atrybuty, należy wyświetlić drzewo MBean. Komponent MBean to zarządzany obiekt Java, który może reprezentować urządzenie, aplikację lub zasób, i ma zestaw atrybutów. 

Aby wyświetlić dostępne metryki i przejrzeć dostępne metryki, zalecamy użycie [kontrolki sterowanie obsługą języka Java](https://www.oracle.com/java/technologies/jdk-mission-control.html).

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Jak nawigować po kontrolce misja języka Java, aby uzyskać dostęp do właściwych metryk?

Po uruchomieniu narzędzia Sterowanie misjami w języku Java zostanie wybrana opcja JVMs dostępna po lewej stronie, a następnie kliknij odpowiedni proces na karcie "JVM Browser". Zaczekaj, aż JMC załaduje pulpit nawigacyjny dla tego procesu, wybierz kartę "MBean przeglądarki" u dołu (patrz poniżej). JMC musi znajdować się w tym samym folderze co JVM, a proces/aplikacja musi być uruchomiona.

![Zrzut ekranu przedstawiający przeglądarkę JMC MBean](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Jak uzyskać dostęp do żądanych metryk i wymaganych atrybutów?

Przeglądarka MBean otwiera drzewo MBean z listą kategorii, które mogą być rozwinięte. Wybranie kategorii po lewej stronie powoduje otwarcie listy atrybutów po prawej stronie. Poniżej znajduje się przykład metryki, jej nazwy obiektu i atrybutów. Atrybuty mogą być zagnieżdżane, jak w poniższym przykładzie.

![Zrzut ekranu drzewa JMC MBean](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Przykład konfiguracji

Z zaznaczenia, jak pokazano na powyższym obrazie, umożliwia skonfigurowanie kilku metryk. Pierwszy z nich to przykład zagnieżdżonej metryki — `LastGcInfo` która ma kilka właściwości i chcemy przechwycić `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Typy zebranych metryk i dostępnych opcji konfiguracji?

Obsługujemy metryki liczbowych i logicznych JMX, podczas gdy inne typy nie są obsługiwane i zostaną zignorowane. 

Obecnie symbole wieloznaczne i agregowane nie są obsługiwane, dlatego, że każdy atrybut "Object Name"/"Attribute" musi być skonfigurowany osobno. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Gdzie mogę znaleźć metryki JMX w usłudze Application Insights?

Gdy aplikacja jest uruchomiona i zbierane są metryki JMX, możesz je wyświetlić, przechodząc do Azure Portal i przejdź do zasobu usługi Application Insights. Na karcie metryki wybierz listę rozwijaną, jak pokazano poniżej, aby wyświetlić metryki.

![Zrzut ekranu metryk w portalu](media/java-ipa/jmx/jmx-portal.png)
