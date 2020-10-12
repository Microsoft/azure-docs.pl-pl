---
title: Ustawienia usługi DNS zasad zapory platformy Azure (wersja zapoznawcza)
description: Zasady zapory platformy Azure można skonfigurować przy użyciu serwera DNS i ustawień serwera proxy DNS.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: bf189e4c9853d9f2ff6e8495423f4f36f14f41d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85611802"
---
# <a name="azure-firewall-policy-dns-settings-preview"></a>Ustawienia usługi DNS zasad zapory platformy Azure (wersja zapoznawcza)

> [!IMPORTANT]
> Ustawienia usługi DNS dla zapory platformy Azure są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Można skonfigurować niestandardowy serwer DNS i włączyć serwer proxy DNS dla zasad zapory platformy Azure. Te ustawienia można skonfigurować podczas wdrażania zapory lub później ze strony **ustawień DNS** .

## <a name="dns-servers"></a>Serwery DNS

Serwer DNS obsługuje i rozpoznaje nazwy domen do adresów IP. Domyślnie Zapora platformy Azure używa do rozpoznawania nazw Azure DNS. Ustawienie **serwer DNS** umożliwia skonfigurowanie własnych serwerów DNS do rozpoznawania nazw zapory platformy Azure. Można skonfigurować jeden lub wiele serwerów.

### <a name="configure-custom-dns-servers"></a>Konfigurowanie niestandardowych serwerów DNS

1. Wybierz zasady zapory.
2. W obszarze **Ustawienia**wybierz pozycję **Ustawienia DNS**.
3. W obszarze **serwery DNS**można wpisać lub dodać istniejące serwery DNS, które zostały wcześniej określone w Virtual Network.
4. Wybierz pozycję **Zapisz**.
5. Zapora kieruje teraz ruch DNS do określonych serwerów DNS w celu rozpoznawania nazw.

## <a name="dns-proxy-preview"></a>Serwer proxy DNS (wersja zapoznawcza)

Zaporę platformy Azure można skonfigurować tak, aby działała jako serwer proxy DNS. Serwer proxy DNS działa jako pośrednik dla żądań DNS z maszyn wirtualnych klienta do serwera DNS. W przypadku konfigurowania niestandardowego serwera DNS należy włączyć serwer proxy DNS, aby uniknąć niezgodności rozpoznawania nazw DNS, i włączyć filtrowanie nazw FQDN w regułach sieci.

Jeśli nie włączysz serwera proxy DNS, żądania DNS od klienta mogą być przesyłane do serwera DNS w innym czasie lub zwracać inną odpowiedź w porównaniu z tą zaporą. Serwer proxy DNS umieszcza w ścieżce żądania klienta zaporę platformy Azure, aby uniknąć niespójności.

Konfiguracja serwera proxy DNS wymaga wykonania trzech kroków:
1. Włącz serwer proxy DNS w ustawieniach DNS zapory platformy Azure.
2. Opcjonalnie można skonfigurować niestandardowy serwer DNS lub użyć podanego ustawienia domyślnego.
3. Na koniec należy skonfigurować prywatny adres IP zapory platformy Azure jako niestandardowy adres DNS w ustawieniach serwera DNS sieci wirtualnej. Gwarantuje to, że ruch DNS jest kierowany do zapory platformy Azure.

### <a name="configure-dns-proxy-preview"></a>Konfigurowanie serwera proxy DNS (wersja zapoznawcza)

Aby skonfigurować serwer proxy DNS, należy skonfigurować ustawienie serwerów DNS sieci wirtualnej w taki sposób, aby korzystały z prywatnego adresu IP zapory. Następnie Włącz serwer proxy DNS w **ustawieniach DNS**zasad zapory platformy Azure.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurowanie serwerów DNS sieci wirtualnej

1. Wybierz sieć wirtualną, w której ruch DNS będzie kierowany przez zaporę platformy Azure.
2. W obszarze **Ustawienia**wybierz pozycję **serwery DNS**.
3. W obszarze **serwery DNS**wybierz opcję **niestandardowe** .
4. Wprowadź prywatny adres IP zapory.
5. Wybierz pozycję **Zapisz**.

#### <a name="enable-dns-proxy-preview"></a>Włącz serwer proxy DNS (wersja zapoznawcza)

1. Wybierz zasady zapory platformy Azure.
2. W obszarze **Ustawienia**wybierz pozycję **Ustawienia DNS**.
3. Domyślnie **serwer proxy DNS** jest wyłączony. Po włączeniu Zapora nasłuchuje na porcie 53 i przekazuje żądania DNS do skonfigurowanych serwerów DNS.
4. Przejrzyj konfigurację **serwerów DNS** , aby upewnić się, że ustawienia są odpowiednie dla danego środowiska.
5. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Filtrowanie nazw FQDN w regułach sieci](fqdn-filtering-network-rules.md)