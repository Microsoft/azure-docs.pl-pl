---
title: Zagadnienia dotyczące poprawiania oprogramowania BareMetal for Oracle
description: Dowiedz się więcej o zagadnieniach dotyczących poprawek systemu operacyjnego/jądra dla oprogramowania BareMetal Infrastructure for Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559097"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Zagadnienia dotyczące poprawiania oprogramowania BareMetal for Oracle

W tym artykule przyjrzymy się ważnym zagadnieniam z uwzględnieniem poprawek systemu operacyjnego/jądra dla oprogramowania BareMetal Infrastructure for Oracle.

Aby uzyskać odpowiednią wydajność sieci i stabilność systemu, zainstaluj specyficzną dla systemu operacyjnego wersję sterowników eNIC i fNIC, jak pokazano w poniższej tabeli zgodności. 

Serwery są dostarczane do klientów ze zgodnymi wersjami. Podczas poprawiania systemu operacyjnego/jądra można jednak wycofać sterowniki do domyślnych wersji sterowników. Dlatego upewnij się, że odpowiednia wersja sterownika działa zgodnie z operacjami poprawek systemu operacyjnego/jądra.

| Dostawca systemu operacyjnego | Wersja pakietu systemu operacyjnego | Wersja oprogramowania układowego | Sterownik eNIC | Sterownik fNIC |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o konfiguracji sieci Ethernet programu BareMetal dla programu Oracle.

> [!div class="nextstepaction"]
> [Konfiguracja sieci Ethernet programu BareMetal dla programu Oracle](oracle-baremetal-ethernet.md)

