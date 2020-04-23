---
title: Integracja usługi Azure DNS z zasobami platformy Azure — usługa Azure DNS
description: W tym artykule dowiesz się, jak używać usługi Azure DNS razem, aby zapewnić dns dla zasobów platformy Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 56a7680de3127da06341ac03252a9ab0cff9da7c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024952"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Użyj usługi Azure DNS, aby zapewnić niestandardowe ustawienia domeny dla usługi platformy Azure

Usługa Azure DNS udostępnia usługę DNS dla domeny niestandardowej dla dowolnych zasobów platformy Azure, które obsługują domeny niestandardowe lub które mają w pełni kwalifikowaną nazwę domeny (FQDN). Przykładem jest to, że masz aplikację sieci Web platformy Azure i chcesz,\.aby użytkownicy uzyskiwać do niej dostęp za pomocą contoso.com lub www contoso.com jako FQDN. W tym artykule oszukujesz usługę platformy Azure przy użyciu usługi Azure DNS w celu korzystania z domen niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć usługi Azure DNS dla domeny niestandardowej, należy najpierw delegować domenę do usługi Azure DNS. Odwiedź [pozycję Deluj domenę do usługi Azure DNS, aby](./dns-delegate-domain-azure-dns.md) uzyskać instrukcje dotyczące konfigurowania serwerów nazw do delegowania. Po przekazaniu domeny do strefy DNS platformy Azure można skonfigurować wymagane rekordy DNS.

Można skonfigurować próżność lub domenę niestandardową dla [aplikacji funkcji Azure,](#azure-function-app) [publicznych adresów IP,](#public-ip-address) [usługi App Service (aplikacje sieci Web),](#app-service-web-apps) [magazynu obiektów Blob](#blob-storage)i [usługi Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Aplikacja funkcji platformy Azure

Aby skonfigurować domenę niestandardową dla aplikacji funkcji platformy Azure, tworzony jest rekord CNAME, a także konfiguracja w samej aplikacji funkcji.
 
Przejdź do **aplikacji Funkcji** i wybierz aplikację funkcji. Kliknij pozycję **Funkcje platformy** i w obszarze **Sieć** kliknij pozycję **Domeny niestandardowe**.

![blok aplikacji funkcji](./media/dns-custom-domain/functionapp.png)

Zanotuj bieżący adres URL w bloku **Domeny niestandardowe,** ten adres jest używany jako alias utworzonego rekordu DNS.

![blok domeny niestandardowej](./media/dns-custom-domain/functionshostname.png)

Przejdź do strefy DNS i kliknij + **Ustaw rekord**. Wypełnij następujące informacje w bloku **Dodaj zestaw rekordów** i kliknij przycisk **OK,** aby go utworzyć.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | myfunctionapp (myfunctionapp)        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN dla niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | adatumfunction.azurewebsites.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to adatumfunction.azurewebsites.net nazwa DNS podana domyślnie do aplikacji funkcyjnej.        |

Przejdź z powrotem do aplikacji funkcji, kliknij pozycję **Funkcje platformy**, a następnie w obszarze **Sieć** kliknij pozycję **Domeny niestandardowe**, a następnie w obszarze **Niestandardowe nazwy hostów** kliknij **+ Dodaj nazwę hosta**.

W bloku **Dodaj nazwę hosta** wprowadź rekord CNAME w polu tekstowym **nazwa hosta** i kliknij przycisk **Sprawdź poprawność**. Jeśli rekord zostanie znaleziony, zostanie wyświetlony przycisk **Dodaj nazwy hosta.** Kliknij **pozycję Dodaj nazwę hosta,** aby dodać alias.

![aplikacje funkcyjne dodają blok nazwy hosta](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Publiczny adres IP

Aby skonfigurować domenę niestandardową dla usług korzystających z publicznego zasobu adresu IP, takiego jak Brama aplikacji, Moduł równoważenia obciążenia, usługa w chmurze, maszyny wirtualne Menedżera zasobów i klasyczne maszyny wirtualne, używany jest rekord A.

Przejdź do**publicznego adresu IP** **sieci,** > wybierz publiczny zasób IP i kliknij przycisk **Konfiguracja**. Zasądzić wyświetlony adres IP.

![publiczne ostrze ip](./media/dns-custom-domain/publicip.png)

Przejdź do strefy DNS i kliknij + **Ustaw rekord**. Wypełnij następujące informacje w bloku **Dodaj zestaw rekordów** i kliknij przycisk **OK,** aby go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | serwer mywebserver        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN dla niestandardowej nazwy domeny.        |
|Typ     | A        | Użyj rekordu A, ponieważ zasób jest adresem IP.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiar czasu         |
|Adres IP     | `<your ip address>`       | Publiczny adres IP.|

![tworzenie rekordu A](./media/dns-custom-domain/arecord.png)

Po utworzeniu rekordu A `nslookup` uruchom, aby sprawdzić poprawność rekordu jest rozpoznawany.

![publiczne wyszukiwanie dns ip](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Usługa aplikacji (aplikacje sieci Web)

Poniższe kroki można wykonać podczas konfigurowania domeny niestandardowej dla aplikacji sieci web usługi aplikacji.

Przejdź do **usługi App Service** i wybierz zasób, który konfigurujesz niestandardową nazwę domeny, a następnie kliknij pozycję Domeny **niestandardowe**.

Zanotuj bieżący adres URL w bloku **Domeny niestandardowe,** ten adres jest używany jako alias utworzonego rekordu DNS.

![Blok domen niestandardowych](./media/dns-custom-domain/url.png)

Przejdź do strefy DNS i kliknij + **Ustaw rekord**. Wypełnij następujące informacje w bloku **Dodaj zestaw rekordów** i kliknij przycisk **OK,** aby go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | serwer mywebserver        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN dla niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu. Jeśli zasób używany adres IP, rekord A będzie używany.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | webserver.azurewebsites.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to webserver.azurewebsites.net nazwa DNS podana domyślnie do aplikacji sieci web.        |


![tworzenie rekordu CNAME](./media/dns-custom-domain/createcnamerecord.png)

Przejdź z powrotem do usługi aplikacji skonfigurowaną dla niestandardowej nazwy domeny. Kliknij **pozycję Domeny niestandardowe**, a następnie kliknij pozycję **Nazwy hostów**. Aby dodać utworzony rekord CNAME, kliknij przycisk **+ Dodaj nazwę hosta**.

![rysunek 1](./media/dns-custom-domain/figure1.png)

Po zakończeniu procesu uruchom **nslookup,** aby sprawdzić poprawność rozpoznawania nazw działa.

![rysunek 1](./media/dns-custom-domain/finalnslookup.png)

Aby dowiedzieć się więcej na temat mapowania domeny niestandardowej do usługi App Service, odwiedź stronę [Mapowanie istniejącej niestandardowej nazwy DNS na usługi Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Aby dowiedzieć się, jak przeprowadzić migrację aktywnej nazwy DNS, zobacz [Migrowanie aktywnej nazwy DNS do usługi Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Jeśli chcesz kupić domenę niestandardową, odwiedź stronę [Kup niestandardową nazwę domeny dla usługi Azure Web Apps,](../app-service/manage-custom-dns-buy-domain.md) aby dowiedzieć się więcej o domenach usługi App Service.

## <a name="blob-storage"></a>Blob Storage

Poniższe kroki można wykonać podczas konfigurowania rekordu CNAME dla konta magazynu obiektów blob przy użyciu metody asverify. Ta metoda zapewnia, że nie ma przestojów.

Przejdź **do** > **pozycji Konta magazynu,** wybierz konto magazynu i kliknij pozycję **Domena niestandardowa**. Zanotuj FQDN w kroku 2, ta wartość jest używana do tworzenia pierwszego rekordu CNAME

![Domena niestandardowa magazynu obiektów blob](./media/dns-custom-domain/blobcustomdomain.png)

Przejdź do strefy DNS i kliknij + **Ustaw rekord**. Wypełnij następujące informacje w bloku **Dodaj zestaw rekordów** i kliknij przycisk **OK,** aby go utworzyć.


|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | asverify.mystoragekont        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN dla niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to asverify.adatumfunctiona9ed.blob.core.windows.net nazwa DNS podana domyślnie do konta magazynu.        |

Przejdź z powrotem do **Storage** > konta magazynu, klikając pozycję**Konta magazynu,** wybierz konto magazynu i kliknij pozycję **Domena niestandardowa**. Wpisz utworzony alias bez prefiksu aswerify w polu tekstowym, zaznacz pole wyboru **Użyj pośredniej weryfikacji CNAME**i kliknij przycisk **Zapisz**. Po zakończeniu tego kroku wróć do strefy DNS i utwórz rekord CNAME bez prefiksu asverify.  Po tym punkcie można bezpiecznie usunąć rekord CNAME z prefiksem cdnverify.

![Domena niestandardowa magazynu obiektów blob](./media/dns-custom-domain/indirectvalidate.png)

Sprawdzanie poprawności rozpoznawania dns przez uruchomienie`nslookup`

Aby dowiedzieć się więcej na temat mapowania domeny niestandardowej do punktu końcowego magazynu obiektów blob odwiedź [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego magazynu obiektów Blob](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Usługa Azure CDN

Poniższe kroki można wykonać poprzez skonfigurowanie rekordu CNAME dla punktu końcowego sieci CDN przy użyciu metody cdnverify. Ta metoda zapewnia, że nie ma przestojów.

Przejdź do **sekcji Profile sieciowej** > **sieci CDN**, wybierz swój profil sieci CDN.

Wybierz punkt końcowy, z którego pracujesz, i kliknij pozycję **+ Domena niestandardowa**. Należy **zauważyć, że nazwa hosta punktu końcowego,** ponieważ ta wartość jest rekordem, który wskazuje rekord CNAME.

![Domena niestandardowa usługi CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Przejdź do strefy DNS i kliknij + **Ustaw rekord**. Wypełnij następujące informacje w bloku **Dodaj zestaw rekordów** i kliknij przycisk **OK,** aby go utworzyć.

|Właściwość  |Wartość  |Opis  |
|---------|---------|---------|
|Nazwa     | cdnverify.mycdnendpoint        | Ta wartość wraz z etykietą nazwy domeny jest nazwą FQDN dla niestandardowej nazwy domeny.        |
|Typ     | CNAME        | Użyj rekordu CNAME przy użyciu aliasu.        |
|TTL     | 1        | 1 jest używany przez 1 godzinę        |
|Jednostka czasu wygaśnięcia     | Godziny        | Godziny są używane jako pomiar czasu         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Nazwa DNS, dla której tworzysz alias, w tym przykładzie jest to nazwa cdnverify.adatumcdnendpoint.azureedge.net DNS podana domyślnie do konta magazynu.        |

Przejdź z powrotem do punktu końcowego sieci CDN, klikając **pozycję Profile** > **sieci cdn**i wybierz profil sieci CDN. Kliknij **+ Domena niestandardowa** i wprowadź alias rekordu CNAME bez prefiksu cdnverify i kliknij przycisk **Dodaj**.

Po zakończeniu tego kroku wróć do strefy DNS i utwórz rekord CNAME bez prefiksu cdnverify.  Po tym punkcie można bezpiecznie usunąć rekord CNAME z prefiksem cdnverify. Aby uzyskać więcej informacji na temat sieci CDN i sposobu konfigurowania domeny niestandardowej bez kroku rejestracji pośredniej, odwiedź [mapowanie zawartości usługi Azure CDN do domeny niestandardowej](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować odwrotny system DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).
