---
title: Delegowanie poddomeny — Azure DNS
description: Za pomocą tej ścieżki szkoleniowej Rozpocznij delegowanie poddomeny Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011544"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegowanie poddomeny Azure DNS

Azure Portal można użyć do delegowania poddomeny DNS. Na przykład jeśli jesteś członkiem domeny contoso.com, możesz delegować poddomenę o nazwie *Inżynieria* na inną, osobną strefę, którą można administrować niezależnie od strefy contoso.com.

Jeśli wolisz, możesz delegować poddomenę przy użyciu [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby delegować poddomenę Azure DNS, należy najpierw delegować domenę publiczną do Azure DNS. Zobacz [delegowanie domeny do Azure DNS,](./dns-delegate-domain-azure-dns.md) Aby uzyskać instrukcje dotyczące konfigurowania serwerów nazw na potrzeby delegowania. Po przeprowadzeniu delegowania domeny do strefy Azure DNS można delegować poddomenę.

> [!NOTE]
> Contoso.com jest używany jako przykład w tym artykule. Zastąp contoso.com swoją nazwą domeny.

## <a name="create-a-zone-for-your-subdomain"></a>Tworzenie strefy dla domeny podrzędnej

Najpierw Utwórz strefę dla poddomeny **inżynierów** .

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
2. W polu wyszukiwania wpisz **DNS**, a następnie wybierz pozycję **strefa DNS**.
3. Wybierz pozycję **Utwórz**.
4. W okienku **Tworzenie strefy DNS** wpisz **Engineering.contoso.com** w polu tekstowym **Nazwa** .
5. Wybierz grupę zasobów dla swojej strefy. Możesz chcieć używać tej samej grupy zasobów co strefa nadrzędna, aby zapewnić sobie podobne zasoby.
6. Kliknij pozycję **Utwórz**.
7. Po pomyślnym wdrożeniu przejdź do nowej strefy.

## <a name="note-the-name-servers"></a>Zanotuj serwery nazw

Następnie należy zwrócić uwagę na cztery serwery nazw dla poddomeny inżynierów.

W okienku strefa **inżynierii** Zanotuj cztery serwery nazw dla strefy. Te serwery nazw będą używane później.

## <a name="create-a-test-record"></a>Tworzenie rekordu testowego

Utwórz rekord **A,** który ma być używany do testowania. Na przykład Utwórz rekord **www** a i skonfiguruj go przy użyciu adresu IP **10.10.10.10** .

## <a name="create-an-ns-record"></a>Tworzenie rekordu NS

Następnie Utwórz rekord serwera nazw (NS) dla strefy **inżynierów** .

1. Przejdź do strefy dla domeny nadrzędnej.
2. Wybierz pozycję **+ Zestaw rekordów**.
3. W okienku **Dodawanie zestawu rekordów** wpisz **inżynieryjny** w polu tekstowym **Nazwa** .
4. W obszarze **Typ** wybierz pozycję **NS**.
5. W obszarze **Nazwa serwera** wprowadź cztery serwery nazw, które zostały zarejestrowane wcześniej ze strefy **Inżynieria** .
6. Kliknij przycisk **OK**.

## <a name="test-the-delegation"></a>Testowanie delegowania

Aby przetestować delegowanie, użyj polecenia nslookup.

1. Otwórz okno programu PowerShell.
2. W wierszu polecenia wpisz `nslookup www.engineering.contoso.com.`
3. Odpowiedź Nieautorytatywna powinna być wyświetlana z adresem **10.10.10.10**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować odwrotny serwer DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).