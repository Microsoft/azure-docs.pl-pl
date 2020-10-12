---
title: Konfigurowanie niestandardowej nazwy domeny w Cloud Services | Microsoft Docs
description: Informacje o udostępnianiu aplikacji lub danych platformy Azure w Internecie w domenie niestandardowej przez skonfigurowanie ustawień DNS.  W poniższych przykładach użyto Azure Portal.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 37189df6b1c9bf3f9fca185226f2ee3eeb3ddd7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092732"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure
Podczas tworzenia usługi w chmurze platforma Azure przypisuje ją do poddomeny **cloudapp.NET**. Na przykład jeśli usługa w chmurze nosi nazwę "contoso", użytkownicy będą mogli uzyskiwać dostęp do aplikacji przy użyciu adresu URL, takiego jak `http://contoso.cloudapp.net` . Platforma Azure przypisuje również wirtualny adres IP.

Można jednak również uwidocznić swoją aplikację w swojej nazwie domeny, takiej jak **contoso.com**. W tym artykule wyjaśniono, jak zarezerwować lub skonfigurować niestandardową nazwę domeny dla ról sieci Web usługi w chmurze.

Czy już rozumiesz, jakie rekordy CNAME i A są? [Przejdź do ostatniego wyjaśnienia](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Procedury przedstawione w tym zadaniu dotyczą usługi Azure Cloud Services. Aby uzyskać App Services, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na platformę Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). W przypadku kont magazynu zobacz [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Azure Blob Storage](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Szybciej Rozpocznij korzystanie z nowej [wskazówki przewodnika](https://support.microsoft.com/kb/2990804)dotyczącego platformy Azure.  Tworzy skojarzenie niestandardowej nazwy domeny i zabezpieczania komunikacji (TLS) za pomocą usługi Azure Cloud Services lub usługi Azure Websites.
> 
> 

## <a name="understand-cname-and-a-records"></a>Informacje o rekordach CNAME i A
Rekordy CNAME (lub aliasów) i rekordów umożliwiają skojarzenie nazwy domeny z określonym serwerem (lub usługą w tym przypadku), jednak działają inaczej. Istnieją również pewne zagadnienia, które należy wziąć pod uwagę w przypadku używania rekordów z usługami w chmurze platformy Azure, które należy uwzględnić przed podjęciem decyzji o użyciu.

### <a name="cname-or-alias-record"></a>Rekord CNAME lub alias
Rekord CNAME mapuje *konkretną* domenę, taką jak **contoso.com** lub **www \. contoso.com**, na nazwę domeny kanonicznej. W takim przypadku nazwa domeny kanonicznej to **[MojaApl]. cloudapp. NET** nazwa domeny aplikacji hostowanej na platformie Azure. Po utworzeniu rekord CNAME tworzy alias dla **[MojaApl]. cloudapp. NET**. Wpis CNAME zostanie rozwiązany automatycznie z adresem IP usługi **[MojaApl]. cloudapp. NET** , więc jeśli adres IP usługi w chmurze ulegnie zmianie, nie trzeba podejmować żadnych działań.

> [!NOTE]
> Niektóre rejestratory domeny umożliwiają mapowanie poddomen tylko w przypadku korzystania z rekordu CNAME, takiego jak www \. contoso.com, a nie nazw głównych, takich jak contoso.com. Aby uzyskać więcej informacji na temat rekordów CNAME, zobacz dokumentację dostarczoną przez rejestratora, [wpis witryny Wikipedia w rekordzie CNAME](https://en.wikipedia.org/wiki/CNAME_record)lub dokument [IETF nazwy domen — implementacja i Specyfikacja](https://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Rekord A
Rekord *a* mapuje domenę, taką jak **contoso.com** lub **www \. contoso.com**, *lub domenę wieloznaczną* , taką jak ** \* . contoso.com**, na adres IP. W przypadku usługi w chmurze platformy Azure wirtualny adres IP usługi. W związku z tym główną zaletą rekordu w rekordzie CNAME jest możliwość posiadania jednego wpisu, który używa symbolu wieloznacznego, takiego jak \* **contoso.com**, który będzie obsługiwał żądania dla wielu domen podrzędnych, takich jak **mail.contoso.com**, **login.contoso.com**lub **www \. contso.com**.

> [!NOTE]
> Ponieważ rekord A jest mapowany na statyczny adres IP, nie może automatycznie rozwiązać zmian adresu IP usługi w chmurze. Adres IP używany przez usługę w chmurze jest przypisywany po raz pierwszy podczas wdrażania do pustego gniazda (produkcyjnego lub tymczasowego). Jeśli usuniesz wdrożenie dla gniazda, adres IP zostanie wydany przez platformę Azure, a wszystkie przyszłe wdrożenia w gnieździe mogą otrzymać nowy adres IP.
> 
> Wygodnie, adres IP danego miejsca wdrożenia (produkcyjne lub tymczasowe) jest utrwalany podczas wymiany wdrożeń przejściowych i produkcyjnych lub przeprowadzania uaktualnienia w miejscu istniejącego wdrożenia. Aby uzyskać więcej informacji na temat wykonywania tych akcji, zobacz [jak zarządzać usługami w chmurze](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Dodawanie rekordu CNAME dla domeny niestandardowej
Aby utworzyć rekord CNAME, należy dodać nowy wpis w tabeli DNS dla domeny niestandardowej przy użyciu narzędzi dostarczonych przez rejestratora. Każdy Rejestrator ma podobną, ale nieco inną metodę określania rekordu CNAME, ale koncepcje są takie same.

1. Użyj jednej z tych metod, aby znaleźć nazwę domeny **. cloudapp.NET** przypisaną do usługi w chmurze.

   * Zaloguj się do [Azure Portal], wybierz usługę w chmurze, zapoznaj się z sekcją **Przegląd** , a następnie Znajdź wpis **adresu URL witryny** .

       ![Sekcja szybkiego wglądu pokazująca adres URL witryny][csurl]

       **OR**
   * Zainstaluj i skonfiguruj program [Azure PowerShell](/powershell/azure/), a następnie użyj następującego polecenia:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Zapisz nazwę domeny używaną w adresie URL zwróconym przez każdą z metod, ponieważ będzie ona potrzebna podczas tworzenia rekordu CNAME.
2. Zaloguj się do witryny sieci Web rejestratora DNS i przejdź do strony, aby zarządzać systemem DNS. Wyszukaj linki lub obszary lokacji oznaczone jako **nazwa domeny**, **DNS**lub **Zarządzanie serwerem nazw**.
3. Teraz Znajdź, gdzie można wybrać lub wprowadzić rekordy CNAME. Może być konieczne wybranie typu rekordu z listy rozwijanej lub przechodzenie do strony ustawień zaawansowanych. Należy szukać wyrazów **CNAME**, **alias**lub **subdomen**.
4. Aby utworzyć alias dla **sieci web \. customdomain.com**, należy również podać alias domeny lub PODDOMENY dla rekordu CNAME, np. **www** . Jeśli chcesz utworzyć alias dla domeny katalogu głównego, może on być wymieniony jako **\@** symbol "" w narzędziach DNS rejestratora.
5. Następnie należy podać kanoniczną nazwę hosta, która jest w tym przypadku domeną **cloudapp.NET** aplikacji.

Na przykład następujący rekord CNAME przesyła cały ruch z ** \. contoso.com www** do **contoso.cloudapp.NET**, niestandardową nazwę domeny wdrożonej aplikacji:

| Alias/nazwa hosta/poddomena | Domena kanoniczna |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Odwiedzający ** \. contoso.com www** nigdy nie zobaczy prawdziwego hosta (contoso.cloudapp.NET), więc proces przekazywania jest niewidoczny dla użytkownika końcowego.
> 
> Powyższy przykład dotyczy tylko ruchu w poddomenie **www** . Ponieważ nie można używać symboli wieloznacznych z rekordami CNAME, należy utworzyć jeden rekord CNAME dla każdej domeny/poddomeny. Jeśli chcesz skierować ruch z poddomen, na przykład *. contoso.com, na adres cloudapp.net, możesz skonfigurować **przekierowanie adresu URL** lub wpis **do przodu adresu URL** w ustawieniach DNS lub utworzyć rekord a.

## <a name="add-an-a-record-for-your-custom-domain"></a>Dodawanie rekordu A dla domeny niestandardowej
Aby utworzyć rekord A, należy najpierw znaleźć wirtualny adres IP usługi w chmurze. Następnie Dodaj nowy wpis w tabeli DNS dla domeny niestandardowej przy użyciu narzędzi dostarczonych przez rejestratora. Każdy Rejestrator ma podobną, ale nieco inną metodę określania rekordu A, ale koncepcje są takie same.

1. Użyj jednej z poniższych metod, aby uzyskać adres IP usługi w chmurze.

   * Zaloguj się do [Azure Portal], wybierz usługę w chmurze, zapoznaj się z sekcją **Przegląd** , a następnie Znajdź wpis **publiczne adresy IP** .

       ![Sekcja szybkiego wglądu pokazująca adres VIP][vip]

       **OR**
   * Zainstaluj i skonfiguruj program [Azure PowerShell](/powershell/azure/), a następnie użyj następującego polecenia:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Zapisz adres IP, ponieważ będzie on potrzebny podczas tworzenia rekordu A.
2. Zaloguj się do witryny sieci Web rejestratora DNS i przejdź do strony, aby zarządzać systemem DNS. Wyszukaj linki lub obszary lokacji oznaczone jako **nazwa domeny**, **DNS**lub **Zarządzanie serwerem nazw**.
3. Teraz Znajdź, gdzie można wybrać lub wprowadzić rekord. Może być konieczne wybranie typu rekordu z listy rozwijanej lub przechodzenie do strony ustawień zaawansowanych.
4. Wybierz lub wprowadź domenę lub poddomenę, która będzie używać tego rekordu. Na przykład wybierz pozycję **www** , jeśli chcesz utworzyć alias dla **sieci Web \. customdomain.com**. Jeśli chcesz utworzyć wpis wieloznaczny dla wszystkich poddomen, wprowadź "* * * * *". Obejmuje to wszystkie domeny podrzędne, takie jak **mail.customdomain.com**, **login.customdomain.com**i **www \. customdomain.com**.

    Jeśli chcesz utworzyć rekord A dla domeny katalogu głównego, może on być wymieniony jako **\@** symbol "" w narzędziach DNS rejestratora.
5. W podanym polu Wprowadź adres IP usługi w chmurze. Kojarzy wpis domeny używany w rekordzie A z adresem IP wdrożenia usługi w chmurze.

Na przykład następujący rekord A przesyła dalej cały ruch z **contoso.com** do **137.135.70.239**, adres IP wdrożonej aplikacji:

| Nazwa hosta/poddomena | Adres IP |
| --- | --- |
| \@ |137.135.70.239 |

Ten przykład ilustruje tworzenie rekordu A dla domeny katalogu głównego. Jeśli chcesz utworzyć wpis z symbolem wieloznacznym, aby uwzględnić wszystkie poddomeny, należy wprowadzić "* * * * *" jako poddomenę.

> [!WARNING]
> Adresy IP na platformie Azure są domyślnie dynamiczne. Prawdopodobnie chcesz użyć [zastrzeżonego adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md) , aby upewnić się, że adres IP nie ulegnie zmianie.
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Jak zarządzać Cloud Services](cloud-services-how-to-manage-portal.md)
* [Jak zamapować zawartość usługi CDN na domenę niestandardową](../cdn/cdn-map-content-to-custom-domain.md)
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Skonfiguruj [Certyfikaty TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png



