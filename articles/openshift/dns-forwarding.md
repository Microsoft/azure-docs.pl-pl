---
title: Konfigurowanie przekazywania DNS na platformie Azure Red Hat OpenShift 4
description: Konfigurowanie przekazywania DNS na platformie Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: aa18959d0dc78dedf8b57dc120ab6744afa9051f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070597"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Konfigurowanie przekazywania DNS w klastrze Red Hat OpenShift 4 na platformie Azure

Aby skonfigurować przekazywanie DNS w klastrze Red Hat OpenShift systemu Azure, należy zmodyfikować operator DNS. Ta modyfikacja umożliwi aplikacji sieci firmowej działanie w klastrze w celu rozpoznania nazw hostowanych na prywatnym serwerze DNS poza klastrem. Te kroki są udokumentowane dla OpenShift 4,6 w [tym miejscu](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).

Na przykład jeśli chcesz przesłać dalej wszystkie żądania DNS dla *. example.com, które mają zostać rozpoznane przez serwer DNS 192.168.100.10, można edytować konfigurację operatora, uruchamiając polecenie:
 
```bash
oc edit dns.operator/default
```
 
Spowoduje to uruchomienie Edytora i umożliwi zastępowanie `spec: {}` :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Zapisz plik i Zamknij Edytor.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dodatkowymi informacjami na temat przekazywania DNS na OpenShift 4,6 [tutaj](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).