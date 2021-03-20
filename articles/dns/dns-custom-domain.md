---
title: Integruj Azure DNS z zasobami platformy Azure — Azure DNS
description: W tym artykule dowiesz się, jak używać usługi Azure DNS, aby zapewnić usłudze DNS dla zasobów platformy Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92487402"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Użyj Azure DNS, aby podać niestandardowe ustawienia domeny dla usługi platformy Azure

Azure DNS udostępnia serwer DNS dla domeny niestandardowej dla dowolnych zasobów platformy Azure, które obsługują domeny niestandardowe lub mają w pełni kwalifikowaną nazwę domeny (FQDN). Przykładem jest aplikacja internetowa platformy Azure i chcesz, aby użytkownicy mieli do nich dostęp za pomocą contoso.com lub www \. contoso.com jako nazwy FQDN. W tym artykule przedstawiono sposób konfigurowania usługi platformy Azure przy użyciu Azure DNS korzystania z domen niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać Azure DNS dla domeny niestandardowej, należy najpierw delegować domenę do Azure DNS. Odwiedź stronę [delegowanie domeny do Azure DNS,](./dns-delegate-domain-azure-dns.md) Aby uzyskać instrukcje dotyczące konfigurowania serwerów nazw na potrzeby delegowania. Po przeprowadzeniu delegowania domeny do strefy Azure DNS można skonfigurować potrzebne rekordy DNS.

Można skonfigurować znaczącym lub domenę niestandardową dla [aplikacji funkcji platformy Azure](#azure-function-app), [publicznych adresów IP](#public-ip-address), [App Service (Web Apps)](#app-service-web-apps), usługi [BLOB Storage](#blob-storage)i [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>aplikacja funkcji platformy Azure

Aby skonfigurować domenę niestandardową dla aplikacji funkcji platformy Azure, tworzony jest rekord CNAME oraz konfiguracja w samej aplikacji funkcji.
 
Przejdź do **aplikacja funkcji** i wybierz aplikację funkcji. Kliknij pozycję **funkcje platformy** i w obszarze **Sieć** kliknij pozycję **domeny niestandardowe**.

![blok aplikacji funkcji](./media/dns-custom-domain/functionapp.png)

Zanotuj bieżący adres URL w bloku **domeny niestandardowe** ten adres jest używany jako alias dla utworzonego rekordu DNS.

![blok domeny niestandardowej](./media/dns-custom-domain/functionshostname.png)

Przejdź do strefy DNS, a następnie kliknij pozycję **+ zestaw rekordów**. Wypełnij poniższe informacje w bloku **Dodawanie zestawu rekordów** , a następnie kliknij przycisk **OK** , aby go utworzyć.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | myfunctionapp        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka TTL     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | adatumfunction.azurewebsites.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to nazwa adatumfunction.azurewebsites.net DNS udostępniona domyślnie dla aplikacji funkcji.        |

Wróć do aplikacji funkcji, kliknij pozycję **funkcje platformy** i w obszarze **Sieć** kliknij pozycję **domeny niestandardowe**, a następnie w obszarze **niestandardowe nazwy hostów** kliknij pozycję **+ Dodaj nazwę hosta**.

W bloku **Dodaj nazwę hosta** Wprowadź rekord CNAME w polu tekstowym **Nazwa hosta** , a następnie kliknij przycisk **Weryfikuj**. Jeśli rekord zostanie znaleziony, zostanie wyświetlony przycisk **Dodaj nazwę hosta** . Kliknij przycisk **Dodaj nazwę hosta** , aby dodać alias.

![Aplikacja funkcji Dodaj blok nazwy hosta](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Publiczny adres IP

Aby skonfigurować domenę niestandardową dla usług, które korzystają z zasobu publicznego adresu IP, takiego jak Application Gateway, Load Balancer, usługa w chmurze, Menedżer zasobów VM, i klasycznych maszyn wirtualnych, jest używany rekord A.

Przejdź do opcji **Sieć**  >  **publiczny adres IP**, wybierz zasób publiczny adres IP, a następnie kliknij pozycję **Konfiguracja**. Notated adres IP.

![blok publicznego adresu IP](./media/dns-custom-domain/publicip.png)

Przejdź do strefy DNS, a następnie kliknij pozycję **+ zestaw rekordów**. Wypełnij poniższe informacje w bloku **Dodawanie zestawu rekordów** , a następnie kliknij przycisk **OK** , aby go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | mójserwersieciweb        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN niestandardowej nazwy domeny.        |
|Typ     | A        | Użyj rekordu A jako zasobu jest adresem IP.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka TTL     | Godziny        | Godziny są używane jako pomiar czasu         |
|Adres IP     | `<your ip address>`       | Publiczny adres IP.|

![Tworzenie rekordu A](./media/dns-custom-domain/arecord.png)

Po utworzeniu rekordu, uruchom polecenie, `nslookup` Aby sprawdzić, czy rekord jest rozpoznawany.

![Wyszukiwanie publicznych adresów IP w systemie DNS](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Poniższe kroki pozwalają skonfigurować domenę niestandardową dla aplikacji sieci Web usługi App Service.

Przejdź do **App Service** i wybierz zasób, który konfigurujesz niestandardową nazwę domeny, a następnie kliknij pozycję **domeny niestandardowe**.

Zanotuj bieżący adres URL w bloku **domeny niestandardowe** ten adres jest używany jako alias dla utworzonego rekordu DNS.

![blok domen niestandardowych](./media/dns-custom-domain/url.png)

Przejdź do strefy DNS, a następnie kliknij pozycję **+ zestaw rekordów**. Wypełnij poniższe informacje w bloku **Dodawanie zestawu rekordów** , a następnie kliknij przycisk **OK** , aby go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | mójserwersieciweb        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu. Jeśli zasób użył adresu IP, będzie używany rekord A.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka TTL     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | webserver.azurewebsites.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to nazwa webserver.azurewebsites.net DNS udostępniona domyślnie dla aplikacji sieci Web.        |


![Tworzenie rekordu CNAME](./media/dns-custom-domain/createcnamerecord.png)

Przejdź z powrotem do usługi App Service, która jest skonfigurowana dla niestandardowej nazwy domeny. Kliknij pozycję **domeny niestandardowe**, a następnie kliknij pozycję **nazwy hostów**. Aby dodać utworzony rekord CNAME, kliknij pozycję **+ Dodaj nazwę hosta**.

![Zrzut ekranu, który wyróżnia przycisk + Dodaj nazwę hosta.](./media/dns-custom-domain/figure1.png)

Po zakończeniu procesu uruchom polecenie **nslookup** , aby sprawdzić, czy rozpoznawanie nazw działa.

![rysunek 1](./media/dns-custom-domain/finalnslookup.png)

Aby dowiedzieć się więcej o mapowaniu domeny niestandardowej na App Service, odwiedź stronę [Mapowanie istniejącej niestandardowej nazwy DNS na platformę Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Aby dowiedzieć się, jak przeprowadzić migrację aktywnej nazwy DNS, zobacz [Migrowanie aktywnej nazwy DNS do Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Jeśli musisz zakupić domenę niestandardową, odwiedź stronę [kupowanie niestandardowej nazwy domeny dla platformy Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) , aby dowiedzieć się więcej na temat App Service domen.

## <a name="blob-storage"></a>Blob Storage

Poniższe kroki przeprowadzimy przez proces konfigurowania rekordu CNAME dla konta usługi BLOB Storage przy użyciu metody asverify. Ta metoda zapewnia brak przestojów.

Przejdź do   >  **konta** magazynu magazynu, wybierz konto magazynu, a następnie kliknij pozycję **domena niestandardowa**. Notate nazwę FQDN w kroku 2, ta wartość jest używana do tworzenia pierwszego rekordu CNAME

![Domena niestandardowa magazynu obiektów BLOB](./media/dns-custom-domain/blobcustomdomain.png)

Przejdź do strefy DNS, a następnie kliknij pozycję **+ zestaw rekordów**. Wypełnij poniższe informacje w bloku **Dodawanie zestawu rekordów** , a następnie kliknij przycisk **OK** , aby go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | asverify. mojekontomagazynu        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka TTL     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to nazwa asverify.adatumfunctiona9ed.blob.core.windows.net DNS podana domyślnie dla konta magazynu.        |

Wróć do **konta magazynu, klikając pozycję**  >  **konta magazynu**, wybierz konto magazynu, a następnie kliknij pozycję **domena niestandardowa**. Wpisz w polu tekstowym Alias utworzony bez prefiksu asverify, zaznacz pole wyboru **Użyj pośredniej walidacji rekordu CNAME**, a następnie kliknij przycisk **Zapisz**. Po zakończeniu tego kroku Wróć do strefy DNS i Utwórz rekord CNAME bez prefiksu asverify.  Po tym momencie można bezpiecznie usunąć rekord CNAME z prefiksem cdnverify.

![Zrzut ekranu przedstawiający stronę domena niestandardowa.](./media/dns-custom-domain/indirectvalidate.png)

Sprawdź poprawność rozpoznawania nazw DNS, uruchamiając `nslookup`

Aby dowiedzieć się więcej o mapowaniu domeny niestandardowej do punktu końcowego usługi BLOB Storage, odwiedź stronę [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi BLOB Storage](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json) .

## <a name="azure-cdn"></a>Azure CDN

Poniższe kroki przeprowadzimy przez proces konfigurowania rekordu CNAME dla punktu końcowego usługi CDN przy użyciu metody cdnverify. Ta metoda zapewnia brak przestojów.

Przejdź do opcji **sieciowe**  >  **Profile sieci CDN**, a następnie wybierz profil usługi CDN.

Wybierz punkt końcowy, z którym pracujesz, a następnie kliknij pozycję **+ domena niestandardowa**. Sprawdź **nazwę hosta punktu końcowego** , ponieważ ta wartość jest rekordem, na który wskazuje rekord CNAME.

![Domena niestandardowa CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Przejdź do strefy DNS, a następnie kliknij pozycję **+ zestaw rekordów**. Wypełnij poniższe informacje w bloku **Dodawanie zestawu rekordów** , a następnie kliknij przycisk **OK** , aby go utworzyć.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | cdnverify. mycdnendpoint        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka TTL     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to nazwa cdnverify.adatumcdnendpoint.azureedge.net DNS podana domyślnie dla konta magazynu.        |

Wróć do punktu końcowego usługi CDN, klikając kolejno pozycje **Sieć**  >  **CDN profile** i profil usługi CDN. Kliknij pozycję **+ domena niestandardowa** i wprowadź alias rekordu CNAME bez prefiksu cdnverify, a następnie kliknij przycisk **Dodaj**.

Po zakończeniu tego kroku Wróć do strefy DNS i Utwórz rekord CNAME bez prefiksu cdnverify.  Po tym momencie można bezpiecznie usunąć rekord CNAME z prefiksem cdnverify. Aby uzyskać więcej informacji na temat sieci CDN i jak skonfigurować domenę niestandardową bez kroku rejestracji pośredniej, odwiedź stronę [mapowanie Azure CDN zawartości do domeny niestandardowej](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować odwrotny serwer DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).
